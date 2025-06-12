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
一：采集(在节点上由 Promtail 完成)：
1：假如有一个java程序，将json格式的日志写入了标准输出，放到容器内的约定位置，此时会被该节点上作为daemonset的promtail发现并采集，并且为自动采集的日志流打上一组标签，比如“`{namespace="prod", app="appA", pod="PodA"}`”，
2：原始日志进入promtail的处理流水线，进行
   * 多行聚合（multiline，假如是堆栈跟踪，会将多行日志合并成一条完整记录）、
   * 内容解析（json，解析日志json结构，提取出level、trace_id等字段）、
   * 标签提升（labels，比如level的字段值为error，因为基数低（只有 info、debug、error等几种），会被提升为一个新的标签，而trace_id由于基数高保存在原始日志中）。
   * 脱敏：通过正则表达式对敏感信息进行脱敏，比如对身份证号进行脱敏
   * 速率限制：如果日志产生速率正常，则被放行，如果速率异常，比如过快等等就被归类为日志风暴，超出的部分将被丢弃并且触发告警
最终标签集可能是 `{namespace="prod", app="appA", pod="PodA", level="error"}`
二：传输(从 Promtail 到 Loki)
1：发送日志给loki：promtail将处理好的日志加密的发送给loki集群（TLS加密，HTTP/2格式），为了防止网络抖动导致日志丢失，promtail会将发送失败的日志暂存到本地磁盘，等网络恢复后重试（通过promtail配置WAL(Write-Ahead Log)）
2：loki识别与分发：
   * 请求先通过kubemate的"Go API网关"，识别其租户ID（角色ID）为RoleA，于是在请求头加入，`X-Scope-OrgID: RoleA`
   * 请求被转发给 loki 的 **Distributor** 组件，**Distributor** 组件根据请求头知道这个日志属于哪个租户。
   * **Distributor** 组件根据日志的标签和租户ID进行哈希值计算，将这条日志同时发给3个不同的 **Ingester** 实例（因为我们的 `replication_factor` 设置为3）。
三：入库 (内存处理Ingester与持久化minio)
1：内存处理：3个 **Ingester** 实例都收到了这条日志并保存在内存中，并且将这条日志追加到对应流（stream）的日志块（chunk）中。（速度极快，因为几乎没有磁盘IO），至少两个 **Ingester** 确认接受成功才会向 Distributor 返回成功响应
2：持久化到对象存储：**Ingester** 内存积累一定数量日志（或达到一定时间后），会将压缩好的日志块（chunks）和对应的索引（index）**刷写（flush）**到后端的 **MinIO 对象存储**中。数据在 MinIO 中是按租户ID进行物理隔离存储的。
3：后台优化：在规定时间（如凌晨3~4点）Loki 的 **Compactor** 组件会扫描 MinIO 将众多小文件合并成更大的、为查询优化的文件，这极大地提升了后续历史数据的查询性能。 
四：查询（用户查询日志） 
1：用户查询：用户可以在日志查询页面选择命名空间、对应的服务、日志级别以及关键字，进行查询，平台会自动生成 LogQL 发送给loki Query Fronted
2：查询路由与加速（Query Fronted）： **Query Fronted** 收到LogQL，首先查询缓存，如果未命中转发给  **Querier** 。如果这是一个长查询，比如一个月的查询， **Query Fronted** 会将其拆分为30个并行的天级子查询
3：执行查询（Querier）： **Querier** 收到查询命令后，执行：
   * 查询索引：首先从 minio中获取与此次查询命名空间和级别相关的索引文件（ `{namespace="prod", level="error"}` ）。通过索引迅速定位到可能包含这些日志的日志块文件名。
   * 获取数据： **Querier** 从minio中下载这些被索引定位到的日志块
   * 数据过滤： **Querier** 将这些日志块解压，再逐行执行关键词的内容过滤操作，最终找到所需日志
4：返回结果：查询结果沿着原路返回，最终展示在loki的日志查询页面，并且我们使用虚拟滚动技术，确保返回上万条日志页面依然流畅，并且可以根据日志里的 `trace_id` 直接跳转到链路追踪系统查看完整的分布式调用链 
5：告警：
可以使用loki的 **Rule** 组件运行一些告警规则，比如5分钟内appA的error警告级别日志超过10条，就触发告警。让日志到达10条时，Ruler会向Altermanager发送告警并通知运维人员。

