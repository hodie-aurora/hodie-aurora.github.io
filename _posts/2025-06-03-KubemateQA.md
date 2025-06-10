---
layout:     post   				# 使用的布局（不需要改）
title:      Kubemate QA            		# 标题 
subtitle:   Kubemate QA				#副标题
date:       2025-06-03				# 时间
author:     zhaohaiwen 				# 作者
header-img: img/post-bg-2025-01-07.jpg		#这篇文章标题背景图片
catalog: true 					# 是否归档
tags:						#标签
    - Kubemate
---
## 项目名称：Kubemate

**一、 50个常见问题 (覆盖项目整体)**

1. 当初为什么选择创建 Kubemate 这个项目？它主要解决了哪些痛点？
2. Kubemate 的核心价值主张是什么？它与其他 Kubernetes 管理平台或 AI 基础设施平台有何不同？
3. 为什么选择 Go 语言作为后端开发语言？Gin 框架带来了哪些优势？ [[1]](https://time.geekbang.org/column/article/837934)
4. 前端技术选型为什么是 Vue3 + PrimeVue？它们在构建管理控制台方面表现如何？
5. 在项目初期，Kubemate 的核心关注点是什么？后来是如何逐步扩展到现有功能的？
6. 请描述一下 Kubemate 的整体架构，各个主要组件是如何协同工作的？
7. 在封装 Kubernetes API 时，遇到了哪些挑战？是如何简化复杂性的？ [[2]](https://www.iceyao.com.cn/post/2018-04-08-kubernetes_api%E5%B0%81%E8%A3%85%E7%9A%84%E5%BF%83%E5%BE%97/)
8. 多租户支持是如何实现的？RBAC 和 Namespace 在其中扮演了怎样的角色？ [[3]](https://blog.nashtechglobal.com/how-to-secure-multi-tenant-kubernetes-clusters-with-capsule-and-rbac/)[[4]](https://www.loft.sh/blog/kubernetes-multi-tenancy-and-rbac-implementation-and-security-considerations)
9. 在7家金融机构的生产环境上云过程中，遇到的最大挑战是什么？如何克服的？ [[5]](https://www.alauda.cn/case-studies/zxxyk.html)[[6]](https://www.sofastack.tech/blog/sofa-financial-cloud-native-exploration/)
10. 金融机构对平台的安全性、稳定性和合规性有哪些特殊要求？Kubemate 是如何满足的？ [[7]](https://www.redhat.com/en/blog/implementing-storage-approaches-stateful-financial-services-applications-kubernetes-configurations)[[8]](https://www.motadata.com/blog/devops-with-kubernetes-overcoming-challenges/)
11. ERP 环境的部署与金融机构的部署有何不同？需要注意哪些特有问题？
12. 项目中提到替换了 SkyWalking 为 OpenTelemetry + Jaeger，做出这个决策的原因是什么？带来了哪些改进？
13. Thanos 在 Prometheus 高可用方案中扮演了什么角色？为什么选择 Thanos 而不是其他方案（如 Cortex）？ [[9]](https://www.cnblogs.com/love-DanDan/p/18404489)[[10]](https://www.qikqiak.com/k8strain2/monitor/thanos/)
14. MinIO 作为 S3 兼容存储，在项目中主要用于哪些场景？它的优势是什么？
15. Milvus 向量数据库在 AI 基础设施中是如何支持 RAG 场景的？
16. Redis 在项目中主要用于哪些缓存场景？对性能提升有多大帮助？
17. Linkerd 作为轻量级服务网格，它的“轻量级”体现在哪些方面？为什么选择它而不是 Istio？ [[11]](https://www.solo.io/topics/service-mesh)[[12]](https://www.loft.sh/blog/implementing-a-service-mesh-in-kubernetes)
18. Tekton 和 Jenkins 在 CI/CD 流水线中是如何分工与集成的？ [[13]](https://www.cloudoptimo.com/blog/kubernetes-for-ci-cd-a-complete-guide-for-2025/)[[14]](https://platform9.com/blog/argo-cd-vs-tekton-vs-jenkins-x-finding-the-right-gitops-tooling/)
19. Ollama 在大模型部署方面有哪些优势和局限性？
20. Kubeflow 在高级版 AI 基础设施中提供了哪些核心能力？与基础版相比有何提升？
21. NVIDIA GPU Operator 是如何简化 GPU 资源在 Kubernetes 中的管理的？
22. 云环境巡检功能中，Ansible 和 Go 各自负责哪些巡检任务？为什么采用这种组合？
23. 项目开发过程中，团队是如何协作的？您在其中扮演了怎样的领导角色（尤其是在带领实习生方面）？
24. 您认为 Kubemate 项目最成功的地方是什么？
25. 在项目开发和部署过程中，您遇到的最棘手的技术难题是什么？是如何解决的？
26. 如果让您重新设计 Kubemate 的某个模块，您会选择哪个模块？为什么？会做哪些改变？
27. Kubemate 的可扩展性如何？未来有计划支持更大规模的集群或更多的 AI 模型吗？
28. 平台的安全性是如何保障的？除了 RBAC，还采取了哪些安全措施？ [[3]](https://blog.nashtechglobal.com/how-to-secure-multi-tenant-kubernetes-clusters-with-capsule-and-rbac/)[[15]](https://cloud.google.com/kubernetes-engine/docs/best-practices/enterprise-multitenancy)
29. 对于平台的升级和维护，你们是如何进行的？如何保证业务的连续性？
30. 用户反馈是如何收集和处理的？有没有根据用户反馈进行过重大的功能调整？
31. 在性能优化方面，你们做了哪些工作？特别是在高并发场景下。
32. 成本控制是企业级平台的重要考量，Kubemate 在资源利用率和运维成本方面有何优势？
33. 您如何看待 Kubernetes 生态的快速发展？Kubemate 如何保持与社区的同步和技术的先进性？
34. AI 技术日新月异，Kubemate 在 AI 基础设施方面未来的发展方向是什么？
35. 项目中使用了多种开源组件，如何管理这些组件的版本和依赖关系？
36. 在项目文档和知识沉淀方面，你们是如何做的？
37. 对于新加入的团队成员，如何帮助他们快速上手 Kubemate 项目？
38. Kubemate 的监控仪表盘主要关注哪些核心指标？是如何帮助用户快速定位问题的？
39. 告警的准确性和及时性是如何保证的？如何避免告警风暴？ [[9]](https://www.cnblogs.com/love-DanDan/p/18404489)
40. 日志分析功能支持哪些高级查询和分析能力？
41. 链路追踪如何帮助开发者理解微服务间的调用关系和性能瓶颈？
42. 服务网格的引入对应用的性能和资源消耗有何影响？
43. CI/CD 流水线的平均构建和部署时长是多少？有哪些优化手段？ [[16]](https://www.vervecopilot.com/blog/best-platform-engineering-interview-questions-how-to-prepare-like-a-pro)[[17]](https://thenewstack.io/how-to-build-scalable-and-reliable-ci-cd-pipelines-with-kubernetes/)
44. GPU 资源的调度策略是怎样的？如何确保公平性和高效性？
45. 在项目推广和市场应用方面，有哪些经验可以分享？
46. 您个人在 Kubemate 项目中最大的收获是什么？
47. 如果要将 Kubemate 开源，您认为需要做哪些准备工作？
48. Kubemate 如何处理有状态应用（Stateful Applications）的部署和管理？ [[7]](https://www.redhat.com/en/blog/implementing-storage-approaches-stateful-financial-services-applications-kubernetes-configurations)
49. 平台的灾备和恢复机制是如何设计的？ [[18]](https://www.remoterocketship.com/advice/guide/site-reliability-engineer/kubernetes-management-interview-questions-and-answers)
50. 未来 Kubemate 有没有考虑引入 Serverless 或者函数计算的能力来进一步优化 AI 工作负载？

---

**二、 各模块问题 (每个模块20个普通问题，5个刁钻问题)**

**模块1：自动化 Kubernetes 管理**

* **普通问题 (20)**
  1. Kubemate 是如何通过 Go 和 Gin 框架封装 Kubernetes API 的？能否举例说明一个简化操作的场景？ [[1]](https://time.geekbang.org/column/article/837934)
  2. 平台支持哪些常见的 Kubernetes 资源操作？（如 Pod, Deployment, Service, Ingress 等）
  3. RBAC 是如何配置和应用的？支持哪些粒度的权限控制？ [[3]](https://blog.nashtechglobal.com/how-to-secure-multi-tenant-kubernetes-clusters-with-capsule-and-rbac/)
  4. Namespace 如何用于实现多租户隔离？租户间的网络策略是如何配置的？ [[4]](https://www.loft.sh/blog/kubernetes-multi-tenancy-and-rbac-implementation-and-security-considerations)[[19]](https://www.finalroundai.com/blog/kubernetes-devops-engineer-interview-questions)
  5. Vue3 和 PrimeVue 构建的控制台提供了哪些核心的集群管理功能？
  6. 用户如何通过控制台创建和管理一个应用？
  7. 平台是否支持 Kubernetes 版本的升级管理？
  8. 对于集群节点的管理，平台提供了哪些功能（如添加、删除、状态监控）？
  9. Kubemate 如何处理 Kubernetes 的事件（Events）并呈现给用户？
  10. 是否支持 Helm Chart 的部署和管理？ [[19]](https://www.finalroundai.com/blog/kubernetes-devops-engineer-interview-questions)
  11. 平台如何管理 ConfigMap 和 Secret？ [[20]](https://razorops.com/blog/top-50-kubernetes-interview-question-and-answers/)
  12. 对于 PersistentVolume (PV) 和 PersistentVolumeClaim (PVC) 的管理，平台提供了哪些支持？ [[20]](https://razorops.com/blog/top-50-kubernetes-interview-question-and-answers/)
  13. 用户能否自定义 Kubernetes 资源的 YAML 文件进行部署？
  14. 平台如何展示集群的整体资源使用情况（CPU, 内存, 存储）？
  15. 是否支持集群联邦（Cluster Federation）或多集群管理功能？
  16. 在简化 Kubernetes API 方面，有没有考虑过使用 Operator SDK 或 Kubebuilder 来创建 CRD？
  17. 平台的 API 是否有版本控制和向后兼容策略？
  18. 如何确保用户通过 Kubemate 进行的操作符合最佳实践，避免错误配置？
  19. 对于 Kubernetes 中废弃 (deprecated) 的 API 版本，Kubemate 是如何处理和迁移的？
  20. 控制台的响应速度和用户体验如何？在高负载下表现如何？
* **刁钻问题 (5)**
  1. 当底层 Kubernetes API 版本发生重大变更（例如某些 API 被移除或替换）时，Kubemate 如何保证平滑过渡，并最大限度减少对用户的影响？
  2. 在多租户场景下，如果一个租户的错误操作（如创建大量无用资源）可能影响到 Kubernetes API Server 的性能，Kubemate 有哪些机制来限制或缓解这种情况？ [[21]](https://www.tigera.io/learn/guides/kubernetes-security/kubernetes-multi-tenancy/)
  3. Kubemate 封装的 API 与直接使用 kubectl 或 Kubernetes 客户端库相比，在灵活性和功能完整性上是否存在一些妥协？如何平衡易用性和功能的全面性？
  4. 对于 CRD (Custom Resource Definitions) 的管理，Kubemate 提供了多大程度的支持？用户能否通过 Kubemate 的界面或 API 来管理自定义资源？
  5. 如果 Kubemate 本身的管理组件（运行在 Kubernetes 之上）发生故障，用户将如何访问和管理其 Kubernetes 集群？是否有应急预案？

---

**模块2：监控与告警**

* **普通问题 (20)**

第一部分：Prometheus 和 Thanos

1. Prometheus 是如何部署的？采用了哪些高可用配置？
2. Thanos 的哪些组件被部署了（如 Sidecar, Querier, Store Gateway, Compactor）？它们各自的作用是什么？具体是如何部署的（不要提供代码）？
3. MinIO 是如何作为 Thanos 的长期存储的？存储桶的配置和生命周期管理是怎样的？
4. Prometheus 的抓取配置 (scrape configs) 是如何管理的？是否支持动态更新？
5. 监控数据的查询性能如何？特别是在查询大范围时间序列数据时，Thanos 的降采样（down-sampling）是如何帮助提升性能的？
6. 你们是如何管理 Prometheus 指标的基数（Cardinality）问题的？有没有遇到过高基数带来的性能问题，又是如何解决的？
7. Thanos Querier 是如何对来自两个 Prometheus 副本的数据进行去重的？这个机制的原理是什么？
8. 你们为 Prometheus 和 Thanos 的各个组件设置了怎样的资源请求（requests）和限制（limits）？在资源规划上有什么考量？

第二部分：Prometheus 和 Alertmanager

1. Alertmanager 支持哪些告警渠道？邮件和短信告警是如何配置的？告警的原理是什么？
2. 平台预定义了哪些关键的告警规则？用户是否可以自定义告警规则？如果可以，是如何实现的？
3. 告警的抑制 (Silencing) 和去重 (Deduplication) 是如何通过 Alertmanager 实现的？请举例说明。
4. 告警通知中包含哪些关键信息，以帮助用户快速定位问题？模板是如何定制的？
5. 请描述一个告警从 Prometheus 触发到通过 Alertmanager 发送到用户手中的完整生命周期。
6. 你们如何处理告警风暴（Alert Storm）和告警抖动（Flapping）的问题？
7. Alertmanager 的路由（routing）规则是如何配置的？能否举一个例子，比如将不同严重程度或不同业务线的告警发送给不同的人？

第三部分：其他

1. 应用健康状态是如何定义的？通过哪些指标来衡量？
2. 用户如何通过仪表盘下钻 (drill down) 查看特定应用或组件的详细监控数据？
3. 是否监控 Kubernetes 控制平面组件（API Server, Scheduler, Controller Manager, etcd）的健康状况？主要关注哪些指标？
4. 应用性能指标（如 QPS, 延迟, 错误率）是如何采集和展示的？这部分是基于 OpenTelemetry 吗？
5. 监控系统本身的健康状况是如何监控的（"meta-monitoring"）？比如 Prometheus 自身是否健康，Thanos 组件是否正常工作。

* **刁钻问题 (5)**

第一部分：Prometheus 和 Thanos

1. 当监控数据量巨大时，Thanos Compactor 和 Store Gateway 的性能瓶瓶颈可能出现在哪里？有哪些优化策略？
2. 如何处理 Prometheus 实例间的抓取目标冲突或重复抓取问题，尤其是在多集群联邦查询的场景下？
3. 在金融等对数据准确性要求极高的场景，Prometheus Pull 模式可能存在的微小数据延迟或因目标实例宕机导致的抓取失败（数据丢失），是否会构成问题？你们是如何缓解或应对这种风险的？

第二部分：Prometheus 和 Alertmanager

4. Alertmanager 的高可用集群是如何保证在网络分区（Network Partition）或节点故障情况下，告警状态（如 silences, notifications）的一致性，以及如何确保不丢失、不重复发送告警的？

第三部分：其他

5. 对于需要非常低延迟的告警（例如，几秒内必须响应的交易系统关键故障），当前的 Prometheus -> Alertmanager 架构能否满足？如果不能，有哪些改进方向或替代方案可以考虑？

---

**模块3：日志收集与分析**

* **普通问题 (20)**
  1. Promtail 是如何在 Kubernetes 节点上部署和配置的？（例如 DaemonSet）
  2. Promtail 主要收集哪些来源的日志？（如容器标准输出/错误、特定文件路径）
  3. Loki 的架构是怎样的？（例如，是否区分 Ingester, Distributor, Querier）
  4. Loki 的日志存储是如何配置的？是否使用了对象存储（如 MinIO）？
  5. Vue3 控制台集成的日志可视化界面提供了哪些查询功能？（如按时间、关键词、服务过滤）
  6. 用户如何通过界面进行日志的实时追踪 (tailing)？
  7. Loki 的 LogQL 查询语言，用户需要掌握到什么程度才能有效使用？平台是否提供了简化查询的方式？
  8. 日志的索引策略是怎样的？如何平衡查询性能和存储成本？
  9. 多行日志（如堆栈跟踪）是如何处理和聚合的？
  10. 对于结构化日志 (JSON 等格式)，Loki 是否支持字段提取和基于字段的查询？
  11. 日志的保留策略是怎样的？如何管理历史日志的归档和清理？
  12. Promtail 和 Loki 之间的通信是否加密？
  13. 如何处理日志量突增的情况？Loki 的可扩展性如何？
  14. 用户能否将特定应用的日志导出或下载？
  15. 除了容器日志，平台是否支持收集节点系统日志或其他自定义日志源？
  16. 日志告警是如何实现的？是否与 Prometheus Alertmanager 集成？
  17. 在日志查询界面，用户能否保存常用的查询或创建仪表盘？
  18. Loki 的多租户支持是如何实现的？日志数据是否按租户隔离？
  19. Promtail 的资源消耗（CPU, 内存）如何？对节点性能有无明显影响？
  20. 如何确保日志在采集、传输、存储过程中的完整性和不丢失？
* **刁钻问题 (5)**
  1. 当某个应用产生大量无效或重复的日志（日志风暴）时，Promtail 或 Loki 有哪些机制可以防止系统过载或存储爆炸？
  2. Loki 的索引机制主要依赖标签 (labels)，如果标签设计不当（例如基数过高），会对查询性能和存储造成什么影响？Kubemate 如何指导用户合理设计标签？
  3. 在进行大规模、跨度较长的时间范围的日志查询时，Loki Querier 的性能表现如何？有哪些优化手段或限制？
  4. 如果需要对日志内容进行复杂的关联分析（例如，跨多个服务的用户行为分析），Loki 是否是最佳选择？有没有考虑过集成更专业的日志分析引擎（如 ELK Stack 的 Elasticsearch）？
  5. 对于敏感信息（如个人身份信息、密钥等）出现在日志中，Kubemate 或 Loki/Promtail 体系是否有脱敏或屏蔽机制？

---

**模块4：链路追踪与应用拓扑**

* **普通问题 (20)**
  1. 为什么选择 OpenTelemetry 和 Jaeger 替换 SkyWalking？主要的考虑因素是什么？
  2. OpenTelemetry Collector 是如何部署和配置的？
  3. 应用服务是如何集成 OpenTelemetry SDK 进行埋点的？是否提供了自动化注入 (auto-instrumentation) 的方案？
  4. Jaeger 的架构是怎样的？（如 Agent, Collector, Query, Storage）
  5. Jaeger 的后端存储是如何选择和配置的？（例如 Elasticsearch, Cassandra, 或内存）
  6. Vue3 仪表盘如何展示链路追踪数据？用户能看到哪些信息？
  7. 应用拓扑图是如何生成的？它能展示哪些依赖关系？
  8. 用户如何通过链路追踪数据分析服务间的调用延迟和瓶颈？
  9. 如何通过 Jaeger UI 或 Kubemate 控制台查询特定的 Trace？支持哪些查询条件？
  10. 采样策略 (Sampling Strategy) 是如何配置的？（例如固定采样率、基于概率的采样、自适应采样）
  11. 对于异步调用、消息队列等场景，链路信息是如何传递和关联的？
  12. 链路追踪数据与日志、监控指标是如何关联起来，以提供更全面的可观测性的？
  13. OpenTelemetry 是否支持收集除链路之外的其他遥测数据（如 Metrics, Logs）？Kubemate 是否利用了这些？
  14. Jaeger 的数据保留策略是怎样的？
  15. 链路追踪系统本身的性能开销如何？对应用服务的影响有多大？
  16. 如何确保 Trace ID 和 Span ID 在整个调用链中的正确传递？
  17. 对于 AI 推理请求的链路追踪，有哪些特殊的考虑或挑战？
  18. 用户能否在拓扑图上直观地看到服务的健康状况或异常点？
  19. 是否支持对特定业务流程或关键路径进行重点追踪？
  20. OpenTelemetry 的生态和社区支持如何？遇到问题时是否容易找到解决方案？
* **刁钻问题 (5)**
  1. 在高并发和大规模微服务环境下，全量采集链路数据可能会带来巨大的存储和处理压力。如果采用采样策略，如何确保关键错误或异常的 Trace 不被漏掉？
  2. 当服务间的调用关系非常复杂，生成的拓扑图可能难以阅读和理解。Kubemate 在可视化方面做了哪些优化来应对这种复杂性？
  3. OpenTelemetry 的规范仍在不断演进，不同语言的 SDK 和组件的成熟度可能存在差异。在实践中是否遇到过兼容性或稳定性问题？如何解决？
  4. 对于使用了多种通信协议（HTTP, gRPC, Kafka, RPC 等）的异构系统，如何保证 OpenTelemetry 能够统一有效地进行追踪？
  5. 从 SkyWalking 迁移到 OpenTelemetry + Jaeger 的过程中，最大的挑战是什么？数据迁移是如何处理的（如果需要的话）？原有基于 SkyWalking 的分析和告警逻辑是如何适配的？

---

**模块5：服务网格与流量管理**

* **普通问题 (20)**
  1. Traefik 作为网关，主要承担了哪些职责？（如动态路由、负载均衡、SSL 终止）
  2. Traefik 的配置是如何管理的？是否支持基于 Kubernetes Ingress 对象的动态配置？
  3. Linkerd 是如何部署到 Kubernetes 集群中的？控制平面和数据平面组件有哪些？ [[11]](https://www.solo.io/topics/service-mesh)
  4. Linkerd 的 Sidecar 代理 (linkerd-proxy) 是如何注入到应用 Pod 中的？ [[11]](https://www.solo.io/topics/service-mesh)
  5. Linkerd 提供了哪些核心的服务网格功能？（如 mTLS, 流量切分, 重试, 超时） [[11]](https://www.solo.io/topics/service-mesh)[[12]](https://www.loft.sh/blog/implementing-a-service-mesh-in-kubernetes)
  6. 服务间的通信加密 (mTLS) 是如何通过 Linkerd 自动实现的？ [[11]](https://www.solo.io/topics/service-mesh)
  7. Linkerd 如何提供服务间的可观测性？（如黄金指标：成功率、请求速率、延迟）
  8. 为什么选择 Linkerd 而不是更功能丰富的 Istio？主要考虑因素是什么？ [[12]](https://www.loft.sh/blog/implementing-a-service-mesh-in-kubernetes)[[25]](https://stackshare.io/stackups/linkerd-vs-traefik)
  9. Traefik 和 Linkerd 在功能上是否有重叠？它们是如何协同工作的？
  10. 如何通过 Linkerd 实现蓝绿部署或金丝雀发布？
  11. Linkerd 的流量切分 (Traffic Splitting) 功能是如何配置和使用的？
  12. 对于中小型集群，Linkerd 的资源消耗和性能影响如何？
  13. Kubemate 控制台是否提供了对 Traefik 路由规则或 Linkerd 策略的管理界面？
  14. Linkerd 是否支持 Kubernetes NetworkPolicy？它们之间的关系是怎样的？
  15. Traefik 是否支持自定义中间件 (Middleware) 来扩展其功能？
  16. Linkerd 的故障排查和调试是如何进行的？
  17. 服务网格的引入对应用开发者来说，是否透明？需要应用层面做哪些配合？
  18. Kubemate 如何管理和监控 Traefik 和 Linkerd 本身的健康状况？
  19. Linkerd 是否支持 gRPC 和 HTTP/2 协议的流量管理？
  20. Traefik 的高可用是如何保证的？
* **刁钻问题 (5)**
  1. Linkerd 以简单著称，但在某些复杂场景下（如需要细粒度流量策略、与外部系统集成等），其简化的模型是否会成为限制？ [[26]](https://www.toptal.com/kubernetes/service-mesh-comparison)
  2. 在服务网格中，数据平面的 Sidecar 代理会增加每个请求的延迟。Linkerd 在性能优化方面做了哪些工作来最小化这种开销？实际效果如何？ [[12]](https://www.loft.sh/blog/implementing-a-service-mesh-in-kubernetes)
  3. 当集群中同时存在已注入 Linkerd Sidecar 的服务和未注入的服务时，它们之间的通信行为是怎样的？Linkerd 如何处理这种混合模式？
  4. Traefik 作为边缘网关，如果其本身成为性能瓶颈或单点故障，有哪些扩展和容灾策略？
  5. 如果未来业务需求超出了 Linkerd 的能力范围，需要迁移到功能更全面的服务网格（如 Istio），评估过这种迁移的难度和成本吗？Kubemate 平台是否为此预留了接口或做了考虑？

---

**模块6：CI/CD 流水线**

* **普通问题 (20)**
  1. 为什么选择 Tekton 作为云原生 CI/CD 流水线工具？它与 Jenkins 相比有哪些优势？ [[14]](https://platform9.com/blog/argo-cd-vs-tekton-vs-jenkins-x-finding-the-right-gitops-tooling/)[[27]](https://www.groundcover.com/blog/ci-cd-kubernetes)
  2. Tekton 的核心概念（如 Task, Pipeline, TaskRun, PipelineRun, PipelineResource）在 Kubemate 中是如何应用的？ [[28]](https://tekton.dev/)
  3. CI/CD 流水线的基本流程是怎样的？（从代码提交到部署上线）
  4. Git 仓库的代码提交是如何自动触发 Tekton 流水线的？（例如 Webhooks）
  5. 镜像是如何构建的？（例如使用 Kaniko, Buildah, 或 Docker-in-Docker）
  6. 构建好的镜像是推送到哪个私有镜像仓库？（例如 Harbor, MinIO 作为 Docker Registry）
  7. 应用是如何部署到 Kubernetes 集群的？（例如使用 kubectl apply, Helm, Kustomize）
  8. 如何管理不同环境（dev/staging/prod）的配置差异？
  9. Tekton 流水线中的任务（Task）是如何定义和复用的？
  10. Jenkins 是如何与 Tekton 集成的？主要用于支持哪些传统流水线场景？ [[27]](https://www.groundcover.com/blog/ci-cd-kubernetes)
  11. 流水线的执行状态和日志是如何在 Kubemate 控制台中展示的？
  12. 是否支持流水线的手动触发和审批节点？
  13. 构建和部署失败时，如何进行回滚操作？
  14. CI/CD 过程中的安全性是如何保障的？（如代码扫描、镜像扫描、权限控制）
  15. Tekton PipelineResource (或其替代方案如 Artifact Registry) 是如何管理流水线输入输出的？
  16. 如何优化 CI/CD 流水线的执行效率？（如缓存、并行执行） [[17]](https://thenewstack.io/how-to-build-scalable-and-reliable-ci-cd-pipelines-with-kubernetes/)
  17. Kubemate 是否提供了可视化的流水线编排界面？
  18. 对于微服务架构，每个服务都有独立的流水线吗？还是共享流水线模板？
  19. 流水线中是否集成了自动化测试（单元测试、集成测试、端到端测试）？
  20. Tekton Dashboard 或类似工具是否用于可视化和管理 Tekton 资源？
* **刁钻问题 (5)**
  1. Tekton 是 Kubernetes 原生的，但其基于 CRD 的声明式模型在处理非常复杂或动态的流水线逻辑时，是否会显得不够灵活或难以调试？ [[14]](https://platform9.com/blog/argo-cd-vs-tekton-vs-jenkins-x-finding-the-right-gitops-tooling/)
  2. 在多租户的 CI/CD 场景下，如何确保不同租户的流水线执行环境、资源（如构建节点、缓存）和敏感配置（如仓库凭证）是严格隔离和安全的？
  3. 当 Tekton 流水线中的某个长时间运行的任务（如大规模编译或复杂测试）失败时，如何有效地进行断点续传或仅重试失败部分，以节省时间和资源？
  4. Jenkins 作为传统的 CI/CD 工具，与云原生的 Tekton 在设计理念和运维模式上有较大差异。将两者集成时，如何解决配置管理、状态同步和用户体验不一致的问题？ [[27]](https://www.groundcover.com/blog/ci-cd-kubernetes)
  5. 对于需要 GPU 资源进行构建或测试（例如 AI 模型的某些预处理步骤）的流水线，Tekton 是如何与 NVIDIA GPU Operator 配合，实现 GPU 资源的按需分配和调度的？

---

**模块7：云环境巡检**

* **普通问题 (20)**
  1. Ansible 自动化巡检主要检查 Kubernetes 集群的哪些方面？（如节点状态、Pod 健康性、配置合规性）
  2. Ansible Playbook 是如何编写和管理的？巡检任务是定时执行还是按需执行？
  3. 基于 Go 的 goroutine 分布式采集系统是如何设计的？它采集哪些高级指标？
  4. Go 采集的指标是如何与 Prometheus 和 Thanos 集成的？
  5. 巡检报告包含哪些内容？以什么形式呈现给用户？
  6. 异常检测的规则是如何定义的？基于静态阈值还是动态基线？
  7. 巡检功能是否支持对多个 Kubernetes 集群进行统一管理和报告？
  8. 配置合规性检查是依据哪些标准或最佳实践进行的？
  9. 巡检任务对被巡检集群的性能影响有多大？
  10. 用户是否可以自定义巡检项或巡检脚本？
  11. 巡检发现的问题是如何通知运维人员的？是否与告警系统联动？
  12. Go 分布式采集系统与 Ansible 巡检在功能上是如何互补的？
  13. 巡检历史数据是否保存？用于趋势分析或审计？
  14. 对于金融机构等对安全性要求高的环境，巡检脚本和工具的安全性是如何保证的？
  15. 巡检功能是否覆盖了网络、存储等基础设施层面？
  16. 如何确保巡检结果的准确性和可靠性？
  17. 巡检报告是否提供修复建议或自动化修复能力？
  18. Go 采集系统在实现分布式采集时，如何处理节点间通信和任务调度？
  19. Ansible 巡检是否需要目标节点安装 Agent？
  20. 巡检的频率是如何设定的？不同巡检项的频率是否可以不同？
* **刁钻问题 (5)**
  1. 对于大规模集群（例如数百上千节点），Ansible 巡检的效率如何保证？Go 分布式采集系统在数据聚合和处理上会面临哪些挑战？
  2. “配置合规性”的定义可能因企业策略或行业法规而异。Kubemate 的巡检功能如何适应这种定制化的合规性需求？
  3. 当巡检任务本身发生故障或产生错误结果时（例如，Go 采集程序崩溃或 Ansible 脚本误判），如何快速发现并纠正，以避免对运维决策产生误导？
  4. 在复杂的云环境中，某些“异常”可能是预期行为（例如计划内的维护、压力测试）。巡检系统如何区分真正的故障和这些预期内的“异常”？
  5. Go 分布式采集系统与 Prometheus 本身的指标采集在功能上是否有重叠？为什么不直接扩展 Prometheus Exporter 来满足高级巡检的指标采集需求，而是另建一个 Go 系统？

---

**模块8：AI 基础设施（基础版）**

* **普通问题 (20)**
  1. Ollama 是如何集成到 Kubemate 中用于部署大模型的？
  2. 用户上传文档后，自动拆分、生成嵌入向量的流程是怎样的？使用了哪些库或工具？
  3. 嵌入向量存储到 Milvus 的过程是怎样的？Milvus 的集合 (Collection) 是如何设计的？
  4. MinIO 在此模块中具体存储哪些数据？（如原始文档、模型权重、数据集）
  5. Redis 主要缓存哪些推理结果或中间数据？缓存策略是怎样的？
  6. RAG (Retrieval Augmented Generation) 场景是如何实现的？用户提问后，系统如何从 Milvus 检索相关文档片段并结合大模型生成答案？
  7. Prometheus 如何监控 Ollama 部署的模型的推理性能？（如QPS、延迟、资源使用）
  8. Vue3 仪表盘上展示了哪些 AI 任务相关的状态和信息？
  9. 基础版 AI 基础设施支持哪些类型的大模型？（例如 LLM、Embedding 模型）
  10. 用户如何管理上传的文档和对应生成的向量数据？
  11. Ollama 服务的资源分配（CPU, 内存, GPU）是如何管理的？
  12. Milvus 的部署和运维是怎样的？是否考虑了其高可用性？
  13. 文档拆分的策略是什么？（例如按段落、按句子、固定长度）
  14. 生成嵌入向量时，使用的是哪个 Embedding 模型？用户是否可以替换？
  15. 对于用户上传的文档，是否有格式或大小限制？
  16. AI 推理的 API 是如何暴露给用户的？
  17. 如何保证向量搜索的准确性和效率？Milvus 的索引是如何配置的？
  18. MinIO 和 Milvus 的数据备份和恢复机制是怎样的？
  19. 基础版 AI 设施的并发处理能力如何？
  20. 对于不同用户的文档数据，是如何进行隔离和权限控制的？
* **刁钻问题 (5)**
  1. Ollama 主要面向本地和轻量级部署，当面临企业级大规模并发推理请求或需要更复杂模型管理（如版本控制、A/B 测试）时，Ollama 是否能满足需求？其在生产环境的稳定性如何？
  2. RAG 场景中，文档块 (chunk) 的大小和重叠 (overlap) 策略对检索效果和最终生成质量有显著影响。Kubemate 是如何确定最优拆分策略的？是否支持用户调整？
  3. Milvus 中向量索引的选择（如 IVF_FLAT, HNSW）对搜索性能和构建时间有很大影响。Kubemate 是如何根据数据规模和查询需求自动或推荐选择合适的索引类型的？[ ]
  4. 当用户上传的文档集非常庞大，导致 Milvus 存储和索引压力过大时，基础版 AI 设施有哪些扩展方案或性能瓶颈？
  5. 对于私有化部署的客户，他们可能希望使用自定义的 Embedding 模型或私有大模型。基础版 AI 设施在模型的替换和接入方面有多大的灵活性？

---

**模块9：AI 基础设施（高级版）**

* **普通问题 (20)**
  1. Kubeflow 是如何扩展 AI 工作流的？主要使用了 Kubeflow 的哪些组件？（如 Pipelines, Training Operators, KFServing）
  2. 模型微调 (fine-tuning) 的流程是怎样的？用户如何上传数据集和选择基础模型？
  3. Hugging Face Transformers 和 PEFT (LoRA) 是如何在微调中应用的？
  4. Kubeflow PyTorchJob (或 TensorFlowJob) 是如何支持分布式训练的？
  5. KFServing (或 KServe) 是如何提供高并发推理服务的？它与基础版 Ollama 推理有何不同？
  6. Katib 是如何用于自动化超参数调优的？支持哪些搜索算法？
  7. 高级版 AI 基础设施覆盖了 AI 开发的哪些全流程环节？
  8. 金融风控、客服自动化等场景是如何利用高级版功能的？能否举例说明？
  9. 用户如何通过 Kubemate 界面定义和管理 Kubeflow Pipeline？
  10. 训练产生 Mô Hình 和相关产物（如日志、指标）是如何管理的？
  11. 分布式训练时，数据是如何分发到各个训练节点的？
  12. 模型微调完成后，如何评估模型性能并将其部署到 KFServing？
  13. Kubeflow 的多租户和权限管理是如何与 Kubemate 的体系集成的？
  14. 与基础版相比，高级版在模型版本控制、实验追踪方面有哪些增强？
  15. Kubeflow 各组件的资源消耗如何？对 Kubernetes 集群有何要求？
  16. 用户是否可以自定义 Kubeflow Pipeline 中的组件？
  17. 高级版 AI 设施是否支持其他机器学习框架（如 Scikit-learn, XGBoost）？
  18. 模型的可解释性 (explainability) 在高级版中是否有考虑？
  19. Kubeflow 的部署和维护复杂性如何？Kubemate 是如何简化这一过程的？
  20. 对于训练好的模型，是否有模型仓库 (Model Registry) 进行统一管理？
* **刁钻问题 (5)**
  1. Kubeflow 是一个功能强大但相对复杂的平台，其组件众多且依赖关系复杂。在生产环境中维护 Kubeflow 的稳定性和版本升级，遇到了哪些挑战？Kubemate 是如何应对的？
  2. 参数高效微调（如 LoRA）虽然能降低训练成本，但在某些复杂任务或需要大幅改变模型行为的场景下，其效果可能不如全量微调。Kubemate 如何帮助用户判断选择哪种微调策略？
  3. 分布式训练的配置和调试往往比较困难，尤其是在涉及多机多卡、网络通信优化等问题时。Kubemate 在简化分布式训练的门槛方面做了哪些工作？
  4. KFServing (KServe) 支持 Serverless 推理和模型自动伸缩，但在冷启动或流量突增时可能存在延迟。对于有严格延迟要求的在线推理服务，Kubemate 和 KFServing 是如何优化的？
  5. Katib 进行超参数搜索可能非常耗时和消耗计算资源。Kubemate 是否提供了优化搜索空间、提前终止无效试验或结合更智能搜索策略（如贝叶斯优化）的功能来提高效率？
