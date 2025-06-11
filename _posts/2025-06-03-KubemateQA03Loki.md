---
layout:     post   				# 使用的布局（不需要改）
title:      Kubemate QA03Loki            		# 标题 
subtitle:   Kubemate QA03Loki				#副标题
date:       2025-06-03				# 时间
author:     zhaohaiwen 				# 作者
header-img: img/post-bg-2025-01-07.jpg		#这篇文章标题背景图片
catalog: true 					# 是否归档
tags:						#标签
    - Kubemate
---
### **Kubemate 日志采集全流程：一条日志的生命周期之旅**

pod中应用日志打印与采集过程如下：

#### **阶段一：采集 - 发现与预处理 (在节点上由 Promtail 完成)**

1. **日志产生：** 应用将JSON 格式日志写入了标准输出。这条日志可能是一个多行的 Java 堆栈跟踪，其中还可能包含用户的身份证号等敏感信息。
2. **自动发现：** 在该 Pod 所在的 Kubernetes 节点上，一个作为 **DaemonSet** 部署的 **Promtail** 实例立即通过 Kubernetes API 发现了这个新 Pod。它读取 Pod 的元数据，自动为即将采集的日志流附加了一组基础标签，如 `{namespace="prod", app="trading-service", pod="trading-service-xyz"}`。
3. **进入处理流水线 (`pipeline_stages`)：** 原始日志进入了 Promtail 的处理流水线，在这里它将经历一系列“加工”：
   * **多行聚合 (`multiline`):** 如果是堆栈跟踪，`multiline` 阶段会智能地将多行日志合并成一条完整的记录。
   * **内容解析 (`json`):** `json` 阶段解析日志的 JSON 结构，提取出 `level` 和 `trace_id` 等字段。
   * **标签提升 (`labels`):** `level` 字段的值为 "error"，因为其基数低（只有 info, debug, error 等几种），它被提升为一个新的标签：`level="error"`。而 `trace_id` 因为基数太高，则保留在原始日志内容中。
   * **敏感信息脱敏 (`replace`):** `replace` 阶段的正则表达式匹配到了日志中的身份证号，并将其替换为 `**************`，完成了数据脱敏。
   * **速率限制 (`limit`):** `limit` 阶段检查该 Pod 的日志产生速率。由于速率正常，日志被放行。如果这是一个“日志风暴”，超出的部分将被丢弃，并触发一个告警。

经过处理后，这条日志已经变得干净、结构化且安全。它的最终标签集可能是 `{namespace="prod", app="trading-service", pod="trading-service-xyz", level="error"}`。

#### **阶段二：传输 - 加密传输与高可用接收 (从 Promtail 到 Loki)**

1. **可靠发送：** Promtail 将处理好的日志，通过 **TLS 加密的 HTTP/2 连接**发送给 Loki 集群。为了防止网络抖动导致数据丢失，Promtail 启用了  **WAL (Write-Ahead Log)** ，任何发送失败的日志都会被暂存到本地磁盘，待网络恢复后重试。
2. **租户识别与分发 (`Distributor`)：**
   * 请求首先到达 Kubemate 的  **Go API 网关** 。网关验证了用户的 JWT，识别出其租户 ID 为 `tenant-financial`，于是在请求头中注入了  **`X-Scope-OrgID: tenant-financial`** 。
   * 请求随后被转发给 Loki 的 **Distributor** 组件。Distributor 读取这个 Header，知道了这条日志属于哪个租户。
   * 为了实现高可用，Distributor 根据日志的标签和租户ID进行哈希计算，并将这条日志**同时发送**给3个不同的 **Ingester** 实例（因为我们的 `replication_factor` 设置为3）。

#### **阶段三：入库 - 内存处理与持久化 (`Ingester` 与 `MinIO`)**

1. **内存处理 (`Ingester`)：** 3个 Ingester 实例都收到了这条日志。它们在各自的内存中，将这条日志追加到对应流（stream）的日志块（chunk）中。这个过程极快，因为几乎没有磁盘 I/O。只有当至少2个 Ingester 确认接收成功后，才向 Distributor 返回成功响应。
2. **持久化到对象存储：** Ingester 在内存中积累了一定数量的日志（或达到一定时间）后，会将压缩好的日志块（chunks）和对应的索引（index）**刷写（flush）**到后端的 **MinIO 对象存储**中。数据在 MinIO 中是按租户ID进行物理隔离存储的。
3. **后台优化 (`Compactor`)：** 在夜深人静时，Loki 的 **Compactor** 组件会启动，扫描 MinIO，将白天产生的众多小文件合并成更大的、为查询优化的文件，这极大地提升了后续历史数据的查询性能。

#### **阶段四：响应 - 智能查询与可视化 (从用户点击到界面呈现)**

1. **用户查询：** 您在 Kubemate 的 **Vue3 控制台**上，通过点选“prod”命名空间和“error”级别，并输入关键词“transaction failed”，平台自动为您生成了 LogQL 查询：`{namespace="prod", level="error"} |= "transaction failed"`。
2. **查询路由与加速 (`Query Frontend`)：**
   * 这个查询请求同样经过了注入 `X-Scope-OrgID` 的 Go API 网关，然后到达 Loki 的  **Query Frontend** 。
   * Query Frontend 发现这是一个常见的查询模式，它首先检查缓存，如果未命中，它会将查询转发给  **Querier** 。如果这是一个长达一个月的查询，它会将其拆分成30个并行的天级别子查询。
3. **执行查询 (`Querier`)：** Querier 收到查询后，执行以下操作：
   * **查询索引：** 它首先从 MinIO 中获取与 `{namespace="prod", level="error"}` 相关的索引文件。通过索引，它迅速定位到可能包含这些日志的、极少数的日志块（chunks）的文件名。
   * **获取数据：** 它只从 MinIO 中下载这些被索引定位到的日志块。
   * **内容过滤：** 将这些日志块在内存中解压后，它再逐行执行 `|= "transaction failed"` 的内容过滤操作，最终找到了我们最初那条日志。
4. **结果呈现：** 查询结果沿着原路返回，最终呈现在您的 Vue 控制台上。得益于**虚拟滚动**技术，即使返回了上万条日志，页面依然流畅。您还可以点击日志中的 `trace_id`，直接跳转到 Jaeger 系统查看完整的分布式调用链。

#### **最终的闭环：自动化告警 (`Ruler` 与 `Alertmanager`)**

与此同时，Loki 的 **Ruler** 组件正在后台默默地执行一条告警规则：“如果5分钟内 `trading-service` 的 error 级别日志超过10条，就触发告警”。当这类错误日志累积到一定数量时，Ruler 会立即向 **Alertmanager** 发送告警，后者再通过钉钉或短信通知到您，实现了从被动查询到主动发现问题的飞跃。
