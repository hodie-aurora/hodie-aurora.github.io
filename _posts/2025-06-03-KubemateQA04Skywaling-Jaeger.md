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
### **版本一：基于 Jaeger 的链路追踪全流程**

#### **一、Trace 生成与上下文注入 (在应用 Pod 中由 OTEL Agent/SDK 完成)**

1. **请求入口与 Trace 初始化**：一个外部请求到达集群入口网关（如 Traefik）。

   * **生成 Trace ID**：网关的追踪中间件会检查 HTTP Header。由于这是链路的起点，它会**生成一个全局唯一的 `trace_id`**，并创建第一个 Span (Root Span)。
   * **注入上下文**：网关将包含 `trace_id` 的追踪上下文（遵循 W3C Trace Context 标准）注入到请求头中，然后将请求转发给下游的 `order-service`。
2. **自动 instrumentation 与 Span 创建**：请求到达 `order-service`。其 Pod 中注入的 **OpenTelemetry Java Agent** 会自动拦截该请求。

   * **提取上下文**：Agent 从请求头中**提取 (Extract)** 追踪上下文，并得知此请求属于哪个 `trace_id`。
   * **创建子 Span**：它会创建一个新的 Span 来代表 `order-service` 的处理过程，该 Span 的 `parent_id` 会指向网关创建的 Root Span，从而建立父子关系。
3. **手动埋点增强 (可选)**：在 `order-service` 的业务代码中，开发者可以通过 **OpenTelemetry SDK** 手动创建更细粒度的子 Span（例如 `process-order`），并为其添加业务相关的属性（Attributes），如 `order.id="12345"`，以丰富追踪数据。
4. **跨服务上下文传播**：当 `order-service` 通过 gRPC 调用 `inventory-service` 时，OTEL Agent 会自动将当前的追踪上下文（`trace_id` 和当前 Span 的 `span_id`）**注入 (Inject)** 到 gRPC 的元数据（Metadata）中，确保链路的延续。
5. **链路延续**：`inventory-service` 收到请求后，其 OTEL Agent 会重复第 2 步的过程：提取上下文，并创建自己的子 Span，精确地将两个服务在同一条 Trace 下串联起来。
6. **数据导出**：应用中产生的所有 Span 数据，都会通过 **OTLP (OpenTelemetry Protocol) 协议**，被实时地发送到该节点上部署的 OTEL Collector Agent。

#### **二、数据采集与处理 (在节点和中心 OTEL Collector 中完成)**

1.  **节点采集 (DaemonSet Collector)**：部署在每个 K8s 节点上的 **OTEL Collector (Agent 模式)** 负责接收来自本节点所有 Pod 的 Span 数据，并可附加 `k8s.node.name` 等元数据。
2.  **中心化聚合 (Deployment Collector)**：节点 Collector 随即将数据转发到一组中心化的、高可用的 **OTEL Collector (Gateway 模式)**，进行集中处理。
3.  **数据处理流水线 (Processors)**：在 Gateway Collector 中，Span 数据会流经一条强大的处理流水线：
    *   **`memory_limiter`**：防止 Collector 因数据洪峰而内存溢出。
    *   **`batch`**：将零散的 Span 打包成批次，提升网络和写入效率。
    *   **`attributes`**：统一添加、修改或删除 Span 的属性（如添加环境标签 `env="prod"` 或脱敏）。
    *   **`spanmetrics`**：根据 Span 数据实时生成 Prometheus 指标（RED 指标：请求数、错误数、延迟），用于监控告警。
    *   **`tail_sampling`**：执行核心的 **尾部采样**。等待一个 Trace 的所有 Span 到达后，再根据规则（如：有错误的、高延迟的、或包含特定业务标签的 Trace）决定是保留还是丢弃整个 Trace。

#### **三、数据传输与入库 (从 OTEL Collector 到 Jaeger)**

1. **数据导出 (Exporter)** ：经过处理和采样后，被判定为需要保留的 Trace 数据，会通过  **Jaeger Exporter** ，以 gRPC 协议发送给 Jaeger 的后端组件。
2. **租户识别与路由** ：在导出前，OTEL Collector 可根据 Pod 的 Namespace 等信息，在请求头中动态加入 `X-Scope-OrgID: <tenant_id>`，以支持多租户隔离。
3. **Jaeger Collector 接收** ：**Jaeger Collector** 组件接收到来自 OTEL Collector 的数据，验证租户信息（如果配置了）。
4. **持久化存储** ：Jaeger Collector 将 Span 数据批量写入后端的持久化存储中，最常见的选择是 **OpenSearch** 或 Elasticsearch。每个 Span 作为一个独立的 JSON 文档被存储和索引，关键字段如 `traceID`, `serviceName`, `operationName`, `tags` 等都会被索引，以支持高性能的复杂查询。

#### **四、查询与可视化 (用户在 Jaeger UI / Kubemate UI 中查询)**

1. **用户查询** ：开发或运维人员在 **Jaeger UI** 或集成了其查询能力的前端（如 Kubemate UI）中，输入 `trace_id` 或构造复杂的查询条件（例如 `service=order-service and error=true and http.status_code=500`）。
2. **API 请求** ：前端将查询请求发送给 **Jaeger Query** 服务。
3. **执行查询** ：Jaeger Query 服务将前端的查询请求翻译成对应存储后端的 **DSL (Domain Specific Language)** 查询语句（例如 OpenSearch DSL）。
4. **检索数据** ：OpenSearch 利用其强大的倒排索引，迅速定位到所有匹配的 Span 文档，并将这些原始数据返回给 Jaeger Query。
5. **Trace 重组** ：Jaeger Query 服务在内存中，将属于同一个 `traceID` 的所有 Span 聚合起来，并根据它们的父子关系（`parent_id`）和时间戳， **重建成一个完整且有序的 Trace 树状结构** 。
6. **结果展示** ：重组后的 Trace 数据以 JSON 格式返回给前端，前端将其渲染成用户友好的 **火焰图、甘特图、服务依赖拓扑图** 等多种视图，帮助用户快速定位问题。

#### **五、告警与分析 (基于 Trace 生成的指标)**

1. **指标生成** ：在第二步的 OTEL Collector 中，`spanmetrics` 处理器已经从 Trace 数据中派生出了关键的业务和性能指标。
2. **指标采集** ：**Prometheus** 服务会定期从 OTEL Collector 的 `/metrics` 端点抓取这些指标，或通过 Remote Write 接收。
3. **告警评估** ：Prometheus 中预设的告警规则（Alerting Rules），例如 `sum(rate(traces_spanmetrics_calls_total{status_code="ERROR"}[5m])) by (service_name) > 5`，被持续评估。
4. **告警通知** ：一旦规则被触发，Prometheus 会将告警事件发送给  **Alertmanager** 。Alertmanager 负责对告警进行去重、分组、抑制，并最终通过配置好的渠道（如邮件、Slack、钉钉）通知相关人员，形成从问题发生到被感知的完整闭环。

---

### **版本二：基于 Apache SkyWalking 的链路追踪全流程**

#### **一、Trace 生成与上下文注入 (在应用 Pod 中由 SkyWalking Agent 完成)**

1.  **请求入口与 Trace 初始化**：一个外部请求到达集群。与 OTEL 类似，`trace_id` 应该在链路的最前端生成。这可以由支持 SkyWalking 协议的网关插件完成，或者由第一个接触到请求的微服务 Agent 完成。
    *   **生成 Trace ID**：当请求到达第一个 Java 微服务（`order-service`）时，其 **SkyWalking Java Agent** 会检查请求头。如果不存在 SkyWalking 的追踪上下文，它会**生成一个新的 `trace_id`** 并创建 **根 Span (Root Span)**。
    *   **上下文传播**：当 `order-service` 通过 gRPC 调用 `inventory-service` 时，SkyWalking Agent 会自动将当前追踪上下文**注入 (Inject)** 到 gRPC 的元数据中。这个过程对开发者完全透明。

2.  **跨服务关联**：`inventory-service` 的 SkyWalking Agent 会自动从 gRPC 元数据中 **提取 (Extract)** 上下文，并创建新的子 Span（SkyWalking 中称为 **Entry Span**），并与上游 Span 关联，从而构建出完整的服务调用链。

3.  **手动埋点增强 (可选)**：开发者可以通过 **SkyWalking's Application Toolkit (OAP-APIs)** 手动创建子 Span（**Local Span**），并使用 `@Tag` 注解添加业务标签。

4.  **数据上报**：SkyWalking Agent 会将采集到的追踪片段（Segments）、JVM 指标、服务拓扑关系等数据，通过 **gRPC 协议**，直接上报给 SkyWalking 的后端 OAP Server。

#### **二、数据接收与分析 (在 SkyWalking OAP Server 中完成)**

1. **数据接收与预处理** ：所有 Agent 上报的数据，都会被发送到  **SkyWalking OAP (Observability Analysis Platform) Server** 。OAP Server 是 SkyWalking 的核心大脑，它以集群模式部署，保证高可用和可扩展性。
2. **流式分析处理** ：数据进入 OAP 后，会流经其内部的  **流式处理引擎** 。OAP 在此阶段完成所有核心分析工作，无需类似 OTEL Collector 的外部处理组件：

* **Trace 片段聚合** ：将来自不同服务的 Trace Segment 聚合成完整的 Trace。
* **指标聚合** ：从 Trace 和 Span 数据中实时聚合出各种维度的指标，包括：
  * **服务、服务实例、端点（Endpoint）** 的吞吐量、延迟、成功率。
  * 数据库、消息队列等依赖的性能指标。
* **拓扑分析** ：根据 Span 间的调用关系，自动发现和构建服务依赖拓扑图、虚拟数据库拓扑图等。
* **采样** ：OAP 支持在服务端进行采样，可以根据配置的采样率（如 `1/1000`）来决定是否持久化整个 Trace，以节省存储空间。

#### **三、数据持久化 (由 SkyWalking OAP Server 完成)**

1. **统一写入** ：经过 OAP Server 的实时分析和聚合后，生成的三类核心数据—— **追踪数据 (Traces)** 、**指标数据 (Metrics)** 和  **拓扑关系 (Topology)** ，会被分别写入后端的持久化存储。
2. **存储后端** ：SkyWalking OAP 支持多种存储后端，常见的选择包括  **OpenSearch** 、Elasticsearch、TiDB 等。OAP Server 会根据 SkyWalking 预定义的索引模板和数据模型，高效地将不同类型的数据写入对应的索引中。例如，追踪数据写入 `sw_segment-*` 索引，服务指标写入 `sw_service_resp_time-*` 索引。
3. **多租户隔离** ：SkyWalking 可以通过逻辑隔离（如为索引添加租户前缀）或结合认证插件实现多租户数据隔离。

#### **四、查询与可视化 (用户在 SkyWalking UI 中查询)**

1. **用户查询** ：开发或运维人员在 **SkyWalking UI** 的仪表盘、拓扑图或追踪查询页面进行交互。SkyWalking UI 提供了非常丰富的、开箱即用的可视化功能。
2. **API 请求** ：SkyWalking UI 使用 **GraphQL** 协议与 OAP Server 进行通信，发送查询请求。例如，查询某个服务的历史性能指标，或根据服务名和延迟过滤 Trace。
3. **执行查询** ：**OAP Server** 接收到 GraphQL 请求后，会将其解析，并向后端的 **OpenSearch** 等存储引擎发起查询。OAP 负责查询聚合后的指标数据或原始的追踪数据。
4. **数据处理与返回** ：OAP 从存储中获取数据后，可能会进行二次处理（如重组 Trace），然后将结果通过 GraphQL 响应返回给 UI。
5. **结果展示** ：**SkyWalking UI** 将返回的数据渲染成直观的图表，包括：

* **全局和服务的仪表盘** （包含 RED 指标、Apdex 分数等）。
* **自动生成的服务拓扑图** 。
* **带有时间轴的 Trace 火焰图** 。
* **依赖分析、慢SQL分析** 等深度诊断功能。

#### **五、告警与分析 (由 SkyWalking OAP 内置引擎完成)**

1. **内置告警引擎** ：SkyWalking OAP  **内置了完整的告警引擎** ，无需依赖外部的 Prometheus 和 Alertmanager。
2. **定义告警规则** ：用户可以在 SkyWalking UI 上或通过配置文件定义告警规则（Webhook）。规则可以基于 OAP 自身生成的任何指标，例如：`avg(service_resp_time) > 500 in last 5 mins` 或 `service_success_rate < 99 in last 3 mins`。
3. **规则评估** ：OAP Server 的告警引擎会周期性地评估这些规则。
4. **触发与通知** ：一旦某个指标触发了告警阈值，OAP Server 会立即生成告警事件，并通过配置好的 **Webhook** 将告警信息发送到指定的通知渠道，如钉钉、企业微信、Slack 或自定义的 HTTP 端点，形成一个高度集成和自洽的监控告警闭环。
