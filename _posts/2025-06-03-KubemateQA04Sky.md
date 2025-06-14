---
layout:     post   				# 使用的布局（不需要改）
title:      Kubemate QA04Skywalking            		# 标题 
subtitle:   Kubemate QA04Skywalking				#副标题
date:       2025-06-03				# 时间
author:     zhaohaiwen 				# 作者
header-img: img/post-bg-2025-01-07.jpg		#这篇文章标题背景图片
catalog: true 					# 是否归档
tags:						#标签
    - Kubemate
---
### **版本一：基于 Jaeger 的链路追踪全流程 (简化版)**

#### **一、Trace 生成与传递** 

1. **入口创建 Trace**：一个请求到达集群网关。如果没有 `trace_id` 则为其生成一个，并创建第一个 Span。然后，将包含 `trace_id` 的请求转发给下游服务。
2. **服务间链路延续**：当请求在服务之间（如 Service A -> Service B）传递时，OpenTelemetry (OTEL) Agent 会自动将 `trace_id` 往下传，并为每个服务创建一个新的子 Span，将它们关联起来，形成一条完整的调用链。
3. **数据上报**：每个服务中的 OTEL Agent 将各自产生的 Span 数据，统一发送到 OTEL Collector 进行集中处理。

#### **二、数据处理与采样**

1. **数据聚合**：OTEL Collector 负责从所有应用中收集 Span 数据。
2. **处理与采样**：Collector 会对数据进行批处理以提升效率，并执行核心的 **“尾部采样”**。它会等待一个 Trace 的所有 Span 都到达后，再根据预设规则（例如：只保留出错的、高延迟的或包含特定业务标签的 Trace）来决定是存储还是丢弃整个 Trace。
3. **指标生成**：Collector 还能根据 Span 数据实时生成可供 Prometheus 使用的性能指标（如请求数、错误数、延迟）。

#### **三、数据存储**

1. **数据发送**：经过采样后，OTEL Collector 将需要保留的 Trace 数据发送给 Jaeger 的后端。
2. **持久化存储**：Jaeger Backend 将接收到的 Trace 数据写入 OpenSearch 或 Elasticsearch 等数据库中，以便后续查询。

#### **四、查询与可视化**

1. **用户查询**：开发者在 Jaeger UI 中通过 `trace_id` 或其他条件（如服务名、是否有错误）进行查询。
2. **Trace 重组与展示**：Jaeger 后端从数据库中找出所有相关的 Span，将它们在内存中重新组合成一个完整的 Trace，最后以火焰图等形式直观地展示给用户。

#### **五、告警**

1. **指标采集**：Prometheus 负责从 OTEL Collector 收集第二步中生成的性能指标。
2. **告警触发**：当 Prometheus 发现某个指标（如错误率）触发了预设的告警规则时，它会通知 Alertmanager。
3. **告警发送**：Alertmanager 负责将告警信息去重、分组，并通过邮件、短信等渠道发送给相关人员。

---

### **版本二：基于 Apache SkyWalking 的链路追踪全流程 (简化版)**

#### **一、Trace 生成与上报**

1. **入口创建 Trace**：当一个请求到达第一个微服务时，其 SkyWalking Agent 会检查请求头。如果没有 `trace_id`，它会生成一个新的，并创建第一个 Span。
2. **自动传递**：当服务之间相互调用时，Agent 会自动在请求中传递 `trace_id`，并将各个服务的处理过程作为子 Span 串联起来。
3. **直接上报**：Agent 将采集到的 Trace 数据、JVM 指标等信息，直接上报给 SkyWalking OAP 后端服务器。

#### **二、数据分析与处理**

1. **一体化处理**：SkyWalking 的核心大脑 **OAP Server** 接收所有 Agent 上报的数据。它是一个一体化的平台，负责所有后续处理。
2. **实时分析**：OAP 在内存中流式处理数据，完成 **Trace 聚合、性能指标计算、服务拓扑关系分析** 等所有工作。
3. **服务端采样**：OAP Server 也可以根据配置的采样率（如 1%）来决定是否持久化 Trace，以节省存储。

#### **三、数据存储**

1. **统一写入**：OAP Server 将分析处理后得到的三种核心数据——**追踪数据 (Traces)、指标数据 (Metrics) 和拓扑关系 (Topology)**——统一写入后端的 OpenSearch 或 Elasticsearch 等数据库中。

#### **四、查询与可视化**

1. **用户查询**：开发者在 SkyWalking UI 上进行操作，它提供了开箱即用的仪表盘、拓扑图和追踪查询功能。
2. **数据显示**：SkyWalking UI 向 OAP Server 请求数据，OAP 从数据库中查询后返回。UI 负责将这些数据显示为丰富的图表，如性能仪表盘、服务依赖拓扑图和 Trace 火焰图。

#### **五、告警**

1. **内置告警引擎**：SkyWalking **OAP Server 内置了完整的告警功能**，无需依赖外部的 Prometheus。
2. **规则定义与触发**：用户可以直接在 SkyWalking UI 上配置告警规则（例如“服务A的平均响应时间在过去5分钟内大于500毫秒”）。
3. **告警发送**：OAP Server 会自动评估这些规则。一旦规则被触发，它会通过 Webhook 将告警信息直接发送到邮箱、短信、企业微信 等通知渠道。
