---
layout:     post   				# 使用的布局（不需要改）
title:      kubernetes杂项笔记 			# 标题 
subtitle:   记录k8s与云原生相关的杂项概念		#副标题
date:       2025-01-07 				# 时间
author:     zhaohaiwen 				# 作者
header-img: img/post-bg-2025-01-07.jpg		#这篇文章标题背景图片
catalog: true 					# 是否归档
tags:						#标签
    - kubernetes杂项笔记
---
### 1、etcd及其特点

etcd是CoreOS团队发起的开源项目，是一个管理配置信息和服务发现（service discovery）的项目，它的目标是构建一个高可用的分布式键值（key-value）数据库，基于Go语言实现。

特点：

- 简单：支持REST风格的HTTP+JSON API
- 安全：支持HTTPS方式的访问
- 快速：支持并发1k/s的写操作
- 可靠：支持分布式结构，基于Raft的一致性算法，Raft是一套通过选举主节点来实现分布式系统一致性的算法。

### 2、etcd适应的场景

etcd基于其优秀的特点，可广泛的应用于以下场景：

- 服务发现（Service Discovery）：服务发现主要解决在同一个分布式集群中的进程或服务，要如何才能找到对方并建立连接。本质上来说，服务发现就是想要了解集群中是否有进程在监听UDP或TCP端口，并且通过名字就可以查找和连接。
- 消息发布与订阅：在分布式系统中，最适用的一种组件间通信方式就是消息发布与订阅。即构建一个配置共享中心，数据提供者在这个配置中心发布消息，而消息使用者则订阅他们关心的主题，一旦主题有消息发布，就会实时通知订阅者。通过这种方式可以做到分布式系统配置的集中式管理与动态更新。应用中用到的一些配置信息放到etcd上进行集中管理。
- 负载均衡：在分布式系统中，为了保证服务的高可用以及数据的一致性，通常都会把数据和服务部署多份，以此达到对等服务，即使其中的某一个服务失效了，也不影响使用。etcd本身分布式架构存储的信息访问支持负载均衡。etcd集群化以后，每个etcd的核心节点都可以处理用户的请求。所以，把数据量小但是访问频繁的消息数据直接存储到etcd中也可以实现负载均衡的效果。
- 分布式通知与协调：与消息发布和订阅类似，都用到了etcd中的Watcher机制，通过注册与异步通知机制，实现分布式环境下不同系统之间的通知与协调，从而对数据变更做到实时处理。
- 分布式锁：因为etcd使用Raft算法保持了数据的强一致性，某次操作存储到集群中的值必然是全局一致的，所以很容易实现分布式锁。锁服务有两种使用方式，一是保持独占，二是控制时序。
- 集群监控与Leader竞选：通过etcd来进行监控实现起来非常简单并且实时性强。

### 3、什么是Kubernetes

Kubernetes是开源容器编排平台，它提供了自动化部署、自动扩展、管理容器化应用能力。其核心功能包括：服务自动发现、自动负载均衡、自我修复、滚动更新、秘密与配置管理，根据流量自动伸缩、支持多云与混合环境。

### 4、Kubernetes和Docker的关系

kubernetes 1.23版本之前使用docker作为容器运行时，在1.23版本开始默认使用containerd作为容器运行时

### 5、Minikube、Kubectl、Kubelet分别是什么

Minikube是一种可以在本地轻松运行一个单节点Kubernetes群集的工具。

Kubectl是一个命令行工具，可以使用该工具控制Kubernetes集群管理器，如检查群集资源，创建、删除和更新组件，查看应用程序。

Kubelet管理 Pod 和容器生命周期

### 6、Kubernetes常见的部署方式

常见的Kubernetes部署方式有：

- kubeadm，也是推荐的一种部署方式；
- 二进制；

### 7、Kubernetes如何实现集群管理

在集群管理方面，Kubernetes将集群中的机器划分为一个Master节点和一群工作节点Node。其中，在Master节点运行着集群管理相关的一组进程kube-apiserver、kube-controller-manager和kube-scheduler，这些进程实现了整个集群的资源管理、Pod调度、弹性伸缩、安全控制、系统监控和纠错等管理能力，并且都是全自动完成的。

### 8、Kubernetes的优势、适应场景及其特点

Kubernetes作为一个完备的分布式系统支撑平台，其主要优势：

- 容器编排
- 开源
- 弹性伸缩
- 负载均衡

Kubernetes常见场景：

- 快速部署应用
- 快速扩展应用
- 无缝对接新的应用功能
- 节省资源，优化硬件资源的使用

Kubernetes相关特点：

- 可移植：支持公有云、私有云、混合云、多重云（multi-cloud）。
- 可扩展: 模块化,、插件化、可挂载、可组合。
- 自动化: 自动部署、自动重启、自动复制、自动伸缩/扩展。

### 9、Kubernetes的缺点或当前的不足之处

* 学习曲线陡峭
* 配置复杂繁琐
* 资源消耗较高
* 调试故障困难
* 升级风险大
* 存储管理弱
* 安全性需额外配置
* 小规模场景过重

### 11、Kubernetes集群相关组件

Kubernetes Master控制组件，调度管理整个系统（集群），包含如下组件：

- Kubernetes API Server：作为Kubernetes系统的入口，其封装了核心对象的增删改查操作，以RESTful API接口方式提供给外部客户和内部组件调用，集群内各个功能模块之间数据交互和通信的中心枢纽。
- Kubernetes Scheduler：为新建立的Pod进行节点（Node）选择（即分配机器），负责集群的资源调度。
- Kubernetes Controller：负责执行各种控制器，目前已经提供了很多控制器来保证Kubernetes的正常运行。
- Replication Controller：管理维护Replication Controller，关联Replication Controller和Pod，保证Replication Controller定义的副本数量与实际运行Pod数量一致。
- Node Controller：管理维护Node，定期检查Node的健康状态，标识出（失效|未失效）的Node节点。
- Namespace Controller：管理维护Namespace，定期清理无效的Namespace，包括Namesapce下的API对象，比如Pod、Service等。
- Service Controller：管理维护Service，提供负载以及服务代理。
- EndPoints Controller：管理维护Endpoints，关联Service和Pod，创建Endpoints为Service的后端，当Pod发生变化时，实时更新Endpoints。
- Service Account Controller：管理维护Service Account，为每个Namespace创建默认的Service Account，同时为Service Account创建Service Account Secret。
- Persistent Volume Controller：管理维护Persistent Volume和Persistent Volume Claim，为新的Persistent Volume Claim分配Persistent Volume进行绑定，为释放的Persistent Volume执行清理回收。
- Daemon Set Controller：管理维护Daemon Set，负责创建Daemon Pod，保证指定的Node上正常的运行Daemon Pod。
- Deployment Controller：管理维护Deployment，关联Deployment和Replication Controller，保证运行指定数量的Pod。当Deployment更新时，控制实现Replication Controller和Pod的更新。
- Job Controller：管理维护Job，为Jod创建一次性任务Pod，保证完成Job指定完成的任务数目
- Pod Autoscaler Controller：实现Pod的自动伸缩，定时获取监控数据，进行策略匹配，当满足条件时执行Pod的伸缩动作。

### 12、Kubernetes RC的机制

Replication Controller用来管理Pod的副本，保证集群中存在指定数量的Pod副本。当定义了RC并提交至Kubernetes集群中之后，Master节点上的Controller Manager组件获悉，并同时巡检系统中当前存活的目标Pod，并确保目标Pod实例的数量刚好等于此RC的期望值，若存在过多的Pod副本在运行，系统会停止一些Pod，反之则自动创建一些Pod。

### 13、Kubernetes Replica Set和Replication Controller之间有什么区别

Replica Set和Replication Controller类似，都是确保在任何给定时间运行指定数量的Pod副本。不同之处在于RS使用基于集合的选择器，而Replication Controller使用基于权限的选择器。

### 14、kube-proxy的作用

kube-proxy运行在所有节点上，是Service的透明代理和负载均衡器，其核心功能是将到某个Service的访问请求转发到后端的多个Pod实例上。

(举个例子，pod的ip是会变化的，而service的ip则不会变化，当你访问某个service ip时，请求由本地节点的 kube-proxy 拦截请求并负载均衡转发到对应的各个节点的pod)

### 15、kube-proxy iptables的原理

Kubernetes从1.2版本开始，将iptables作为kube-proxy的默认模式。iptables模式下的kube-proxy不再起到Proxy的作用，其核心功能：通过API Server的Watch接口实时跟踪Service与Endpoint的变更信息，并更新对应的iptables规则，Client的请求流量则通过iptables的NAT机制“直接路由”到目标Pod。

(也就是说，kube-proxy在K8S1.2版本使用的是iptables模式，只写iptables规则，让iptables来处理流量转发的事情，也就是说本地访问server ip的流量被kube-proxy的规则改写为分配到对应pod上的流量)

### 16、kube-proxy ipvs的原理

IPVS在Kubernetes1.11中升级为GA稳定版。IPVS则专门用于高性能负载均衡，并使用更高效的数据结构（Hash表），允许几乎无限的规模扩张，因此被kube-proxy采纳为最新模式。

在IPVS模式下，使用iptables的扩展ipset，而不是直接调用iptables来生成规则链。iptables规则链是一个线性的数据结构，ipset则引入了带索引的数据结构，因此当规则很多时，也可以很高效地查找和匹配。

可以将ipset简单理解为一个IP（段）的集合，这个集合的内容可以是IP地址、IP网段、端口等，iptables可以直接添加规则对这个“可变的集合”进行操作，这样做的好处在于可以大大减少iptables规则的数量，从而减少性能损耗。
(也就是说，在k8s1.11之后，kube-proxy支持采用IPVS模式，维护ipvs hash表，访问server的流量会根据ipvs hash表的配置访问其他节点上对应的ip实现负载均衡访问pod)

### 17、kube-proxy ipvs和iptables的异同

iptables与IPVS都是基于Netfilter实现的，但因为定位不同，二者有着本质的差别：iptables是为防火墙而设计的；IPVS则专门用于高性能负载均衡，并使用更高效的数据结构（Hash表），允许几乎无限的规模扩张。

与iptables相比，IPVS拥有以下明显优势：

- 为大型集群提供了更好的可扩展性和性能；
- 支持比iptables更复杂的复制均衡算法（最小负载、最少连接、加权等）；
- 支持服务器健康检查和连接重试等功能；
- 可以动态修改ipset的集合，即使iptables的规则正在使用这个集合。

### 18、Kubernetes中什么是静态Pod

静态Pod是由kubelet进行管理的仅存在于特定Node的Pod上，他们不能通过API Server进行管理，无法与ReplicationController、Deployment或者DaemonSet进行关联，并且kubelet无法对他们进行健康检查。静态Pod总是由kubelet进行创建，并且总是在kubelet所在的Node上运行。

### 19、Kubernetes中Pod可能位于的状态

- Pending：API Server已经创建该Pod，且Pod内还有一个或多个容器的镜像没有创建，包括正在下载镜像的过程。
- Running：Pod内所有容器均已创建，且至少有一个容器处于运行状态、正在启动状态或正在重启状态。
- Succeeded：Pod内所有容器均成功执行退出，且不会重启。
- Failed：Pod内所有容器均已退出，但至少有一个容器退出为失败状态。
- Unknown：由于某种原因无法获取该Pod状态，可能由于网络通信不畅导致。

### 20、Kubernetes创建一个Pod的主要流程？

* **用户提交** ：kubectl apply 提交 Pod YAML 到  **API Server** 。
* **API 验证** ：API Server 校验权限、格式，存入  **etcd** 。
* **Scheduler 调度** ：Scheduler 监控未调度 Pod，按策略（资源、亲和性）选择  **Node** ，打上 nodeName。
* **Kubelet 监听到** ：目标 Node 的 Kubelet 通过 API Server 发现新 Pod。
* **创建容器** ：Kubelet 调用  **容器运行时** （如 containerd/Docker）拉镜像、启动容器。
* **状态上报** ：Kubelet 持续上报 Pod 状态 → API Server → etcd，用户见 Running。

### 21、Kubernetes中Pod的重启策略

Pod重启策略（RestartPolicy）应用于Pod内的所有容器，并且仅在Pod所处的Node上由kubelet进行判断和重启操作。当某个容器异常退出或者健康检查失败时，kubelet将根据RestartPolicy的设置来进行相应操作。

Pod的重启策略包括Always、OnFailure和Never，默认值为Always。

- Always：当容器失效时，由kubelet自动重启该容器；
- OnFailure：当容器终止运行且退出码不为0时，由kubelet自动重启该容器；
- Never：不论容器运行状态如何，kubelet都不会重启该容器。

同时Pod的重启策略与控制方式关联，当前可用于管理Pod的控制器包括ReplicationController、Job、DaemonSet及直接管理kubelet管理（静态Pod）。

不同控制器的重启策略限制如下：

- RC和DaemonSet：必须设置为Always，需要保证该容器持续运行；
- Job：OnFailure或Never，确保容器执行完成后不再重启；
- kubelet：在Pod失效时重启，不论将RestartPolicy设置为何值，也不会对Pod进行健康检查。

### 22、Kubernetes中Pod的健康检查方式

对Pod的健康检查可以通过两类探针来检查：LivenessProbe和ReadinessProbe。

- LivenessProbe探针：用于判断容器是否存活（running状态），如果LivenessProbe探针探测到容器不健康，则kubelet将杀掉该容器，并根据容器的重启策略做相应处理。若一个容器不包含LivenessProbe探针，kubelet认为该容器的LivenessProbe探针返回值用于是“Success”。
- ReadineeProbe探针：用于判断容器是否启动完成（ready状态）。如果ReadinessProbe探针探测到失败，则Pod的状态将被修改。Endpoint Controller将从Service的Endpoint中删除包含该容器所在Pod的Eenpoint。
- startupProbe探针：启动检查机制，应用一些启动缓慢的业务，避免业务长时间启动而被上面两类探针kill掉。

### 23、Kubernetes Pod的LivenessProbe探针的常见方式

kubelet定期执行LivenessProbe探针来诊断容器的健康状态，通常有以下三种方式：

- ExecAction：在容器内执行一个命令，若返回码为0，则表明容器健康。
- TCPSocketAction：通过容器的IP地址和端口号执行TCP检查，若能建立TCP连接，则表明容器健康。
- HTTPGetAction：通过容器的IP地址、端口号及路径调用HTTP Get方法，若响应的状态码大于等于200且小于400，则表明容器健康。

### 24、Kubernetes Pod的常见调度方式

Kubernetes中，Pod通常是容器的载体，主要有如下常见调度方式：

- Deployment或RC：该调度策略主要功能就是自动部署一个容器应用的多份副本，以及持续监控副本的数量，在集群内始终维持用户指定的副本数量。
- NodeSelector：定向调度，当需要手动指定将Pod调度到特定Node上，可以通过Node的标签（Label）和Pod的nodeSelector属性相匹配。
- NodeAffinity亲和性调度：亲和性调度机制极大的扩展了Pod的调度能力，目前有两种节点亲和力表达：
  - requiredDuringSchedulingIgnoredDuringExecution：硬规则，必须满足指定的规则，调度器才可以调度Pod至Node上（类似nodeSelector，语法不同）。
  - preferredDuringSchedulingIgnoredDuringExecution：软规则，优先调度至满足的Node的节点，但不强求，多个优先级规则还可以设置权重值。
- Taints和Tolerations（污点和容忍）：
  - Taint：使Node拒绝特定Pod运行；
  - Toleration：为Pod的属性，表示Pod能容忍（运行）标注了Taint的Node。

### 25、Kubernetes初始化容器（init container）

init container的运行方式与应用容器不同，它们必须先于应用容器执行完成，当设置了多个init container时，将按顺序逐个运行，并且只有前一个init container运行成功后才能运行后一个init container。当所有init container都成功运行后，Kubernetes才会初始化Pod的各种信息，并开始创建和运行应用容器。

### 26、Kubernetes deployment升级过程

- 初始创建Deployment时，系统创建了一个ReplicaSet，并按用户的需求创建了对应数量的Pod副本。
- 当更新Deployment时，系统创建了一个新的ReplicaSet，并将其副本数量扩展到1，然后将旧ReplicaSet缩减为2。
- 之后，系统继续按照相同的更新策略对新旧两个ReplicaSet进行逐个调整。
- 最后，新的ReplicaSet运行了对应个新版本Pod副本，旧的ReplicaSet副本数量则缩减为0。

(也就是说，每个deploy对应一个rs，升级时创建新的rs，新的rs副本扩张到正常，旧rs削减副本至0)

### 27、Kubernetes deployment升级策略

在Deployment的定义中，可以通过spec.strategy指定Pod更新的策略，目前支持两种策略：Recreate（重建）和RollingUpdate（滚动更新），默认值为RollingUpdate。

- Recreate：设置spec.strategy.type=Recreate，表示Deployment在更新Pod时，会先杀掉所有正在运行的Pod，然后创建新的Pod。
- RollingUpdate：设置spec.strategy.type=RollingUpdate，表示Deployment会以滚动更新的方式来逐个更新Pod。同时，可以通过设置spec.strategy.rollingUpdate下的两个参数（maxUnavailable和maxSurge）来控制滚动更新的过程。

### 28、Kubernetes DaemonSet类型的资源特性

DaemonSet资源对象会在每个Kubernetes集群中的节点上运行，并且每个节点只能运行一个Pod，这是它和Deployment资源对象的最大也是唯一的区别。因此，在定义yaml文件中，不支持定义replicas。

它的一般使用场景如下：

- 在去做每个节点的日志收集工作。
- 监控每个节点的的运行状态。

### 29、Kubernetes自动扩容机制

Kubernetes使用Horizontal Pod Autoscaler（HPA）的控制器实现基于CPU使用率进行自动Pod扩缩容的功能。HPA控制器周期性地监测目标Pod的资源性能指标，并与HPA资源对象中的扩缩容条件进行对比，在满足条件时对Pod副本数量进行调整。

Kubernetes中的某个Metrics Server（Heapster或自定义Metrics Server）持续采集所有Pod副本的指标数据。HPA控制器通过Metrics Server的API（Heapster的API或聚合API）获取这些数据，基于用户定义的扩缩容规则进行计算，得到目标Pod副本数量。

当目标Pod副本数量与当前副本数量不同时，HPA控制器就向Pod的副本控制器（Deployment、RC或ReplicaSet）发起scale操作，调整Pod的副本数量，完成扩缩容操作。

### 30、Kubernetes Service类型

通过创建Service，可以为一组具有相同功能的容器应用提供一个统一的入口地址，并且将请求负载分发到后端的各个容器应用上。其主要类型有：

- ClusterIP：虚拟的服务IP地址，该地址用于Kubernetes集群内部的Pod访问，在Node上kube-proxy通过设置的iptables规则进行转发；
- NodePort：使用宿主机的端口，使能够访问各Node的外部客户端通过Node的IP地址和端口号就能访问服务；
- LoadBalancer：使用外接负载均衡器完成到服务的负载分发，需要在spec.status.loadBalancer字段指定外部负载均衡器的IP地址，通常用于公有云。

### 31、Kubernetes Service分发后端的策略

Service负载分发的策略有：RoundRobin和SessionAffinity

- RoundRobin：默认为轮询模式，即轮询将请求转发到后端的各个Pod上。
- SessionAffinity：基于客户端IP地址进行会话保持的模式，即第1次将某个客户端发起的请求转发到后端的某个Pod上，之后从相同的客户端发起的请求都将被转发到后端相同的Pod上。

### 32、Kubernetes Headless Service

在某些应用场景中，若需要人为指定负载均衡器，不使用Service提供的默认负载均衡的功能，或者应用程序希望知道属于同组服务的其他实例。Kubernetes提供了Headless Service来实现这种功能，即不为Service设置ClusterIP（入口IP地址），仅通过Label Selector将后端的Pod列表返回给调用的客户端。

### 33、Kubernetes外部如何访问集群内的服务

对于Kubernetes，集群外的客户端默认情况，无法通过Pod的IP地址或者Service的虚拟IP地址：虚拟端口号进行访问。通常可以通过以下方式进行访问Kubernetes集群内的服务：

- 映射Pod到物理机：将Pod端口号映射到宿主机，即在Pod中采用hostPort方式，以使客户端应用能够通过物理机访问容器应用。
- 映射Service到物理机：将Service端口号映射到宿主机，即在Service中采用nodePort方式，以使客户端应用能够通过物理机访问容器应用。
- 映射Sercie到LoadBalancer：通过设置LoadBalancer映射到云服务商提供的LoadBalancer地址。这种用法仅用于在公有云服务提供商的云平台上设置Service的场景。

### 34、Kubernetes ingress

Kubernetes的Ingress资源对象，用于将不同URL的访问请求转发到后端不同的Service，以实现HTTP层的业务路由机制。

Kubernetes使用了Ingress策略和Ingress Controller，两者结合并实现了一个完整的Ingress负载均衡器。使用Ingress进行负载分发时，Ingress Controller基于Ingress规则将客户端请求直接转发到Service对应的后端Endpoint（Pod）上，从而跳过kube-proxy的转发功能，kube-proxy不再起作用，全过程为：ingress controller + ingress 规则 ----> services。

同时当Ingress Controller提供的是对外服务，则实际上实现的是边缘路由器的功能。

### 35、Kubernetes镜像的下载策略

Kubernetes的镜像下载策略有三种：Always、Never、IFNotPresent。

- Always：镜像标签为latest时，总是从指定的仓库中获取镜像。
- Never：禁止从仓库中下载镜像，也就是说只能使用本地镜像。
- IfNotPresent：仅当本地没有对应镜像时，才从目标仓库中下载。默认的镜像下载策略是：当镜像标签是latest时，默认策略是Always；当镜像标签是自定义时（也就是标签不是latest），那么默认策略是IfNotPresent。

### 37、Kubernetes各模块如何与API Server通信

Kubernetes API Server作为集群的核心，负责集群各功能模块之间的通信。集群内的各个功能模块通过API Server将信息存入etcd，当需要获取和操作这些数据时，则通过API Server提供的REST接口（用GET、LIST或WATCH方法）来实现，从而实现各模块之间的信息交互。

如kubelet进程与API Server的交互：每个Node上的kubelet每隔一个时间周期，就会调用一次API Server的REST接口报告自身状态，API Server在接收到这些信息后，会将节点状态信息更新到etcd中。

如kube-controller-manager进程与API Server的交互：kube-controller-manager中的Node Controller模块通过API Server提供的Watch接口实时监控Node的信息，并做相应处理。

如kube-scheduler进程与API Server的交互：Scheduler通过API Server的Watch接口监听到新建Pod副本的信息后，会检索所有符合该Pod要求的Node列表，开始执行Pod调度逻辑，在调度成功后将Pod绑定到目标节点上。

（也就是说所有组件都向api server发送信息，而api server将收到的信息存入etcd，接收方 通过 长连接 访问api sever来接收消息）

### 38、Kubernetes Scheduler作用及实现原理

Kubernetes Scheduler是负责Pod调度的重要功能模块，Kubernetes Scheduler在整个系统中承担了“承上启下”的重要功能，“承上”是指它负责接收Controller Manager创建的新Pod，为其调度至目标Node；“启下”是指调度完成后，目标Node上的kubelet服务进程接管后继工作，负责Pod接下来生命周期。

Kubernetes Scheduler的作用是将待调度的Pod（API新创建的Pod、Controller Manager为补足副本而创建的Pod等）按照特定的调度算法和调度策略绑定（Binding）到集群中某个合适的Node上，并将绑定信息写入etcd中。

在整个调度过程中涉及三个对象，分别是待调度Pod列表、可用Node列表，以及调度算法和策略。

Kubernetes Scheduler通过调度算法调度为待调度Pod列表中的每个Pod从Node列表中选择一个最适合的Node来实现Pod的调度。随后，目标节点上的kubelet通过API Server监听到Kubernetes Scheduler产生的Pod绑定事件，然后获取对应的Pod清单，下载Image镜像并启动容器。

### 39、Kubernetes Scheduler使用哪两种算法将Pod绑定到worker节点

Kubernetes Scheduler根据如下两种调度算法将 Pod 绑定到最合适的工作节点：

- 预选（Predicates）：输入是所有节点，输出是满足预选条件的节点。kube-scheduler根据预选策略过滤掉不满足策略的Nodes。如果某节点的资源不足或者不满足预选策略的条件则无法通过预选。如“Node的label必须与Pod的Selector一致”。
- 优选（Priorities）：输入是预选阶段筛选出的节点，优选会根据优先策略为通过预选的Nodes进行打分排名，选择得分最高的Node。例如，资源越富裕、负载越小的Node可能具有越高的排名。

### 40、Kubernetes kubelet的作用

在Kubernetes集群中，在每个Node（又称Worker）上都会启动一个kubelet服务进程。该进程用于处理Master下发到本节点的任务，管理Pod及Pod中的容器。每个kubelet进程都会在API Server上注册节点自身的信息，定期向Master汇报节点资源的使用情况，并通过cAdvisor监控容器和节点资源。

### 41、Kubernetes kubelet监控Worker节点资源是使用什么组件来实现的

kubelet使用cAdvisor对worker节点资源进行监控。在Kubernetes系统中，cAdvisor已被默认集成到kubelet组件内，当kubelet服务启动时，它会自动启动cAdvisor服务，然后cAdvisor会实时采集所在节点的性能指标及在节点上运行的容器的性能指标。

### 42、Kubernetes如何保证集群的安全性

Kubernetes通过一系列机制来实现集群的安全控制，主要有如下不同的维度：

- 基础设施方面：保证容器与其所在宿主机的隔离；
- 权限方面：
  - 最小权限原则：合理限制所有组件的权限，确保组件只执行它被授权的行为，通过限制单个组件的能力来限制它的权限范围。
  - 用户权限：划分普通用户和管理员的角色。
- 集群方面：
  - API Server的认证授权：Kubernetes集群中所有资源的访问和变更都是通过Kubernetes API Server来实现的，因此需要建议采用更安全的HTTPS或Token来识别和认证客户端身份（Authentication），以及随后访问权限的授权（Authorization）环节。
  - API Server的授权管理：通过授权策略来决定一个API调用是否合法。对合法用户进行授权并且随后在用户访问时进行鉴权，建议采用更安全的RBAC方式来提升集群安全授权。
- 敏感数据引入Secret机制：对于集群敏感数据建议使用Secret方式进行保护。
- AdmissionControl（准入机制）：对kubernetes api的请求过程中，顺序为：先经过认证 & 授权，然后执行准入操作，最后对目标对象进行操作。

### 43、Kubernetes准入机制

在对集群进行请求时，每个准入控制代码都按照一定顺序执行。如果有一个准入控制拒绝了此次请求，那么整个请求的结果将会立即返回，并提示用户相应的error信息。

准入控制（AdmissionControl）准入控制本质上为一段准入代码，在对kubernetes api的请求过程中，顺序为：先经过认证 & 授权，然后执行准入操作，最后对目标对象进行操作。常用组件（控制代码）如下：

- AlwaysAdmit：允许所有请求
- AlwaysDeny：禁止所有请求，多用于测试环境。
- ServiceAccount：它将serviceAccounts实现了自动化，它会辅助serviceAccount做一些事情，比如如果pod没有serviceAccount属性，它会自动添加一个default，并确保pod的serviceAccount始终存在。
- LimitRanger：观察所有的请求，确保没有违反已经定义好的约束条件，这些条件定义在namespace中LimitRange对象中。
- NamespaceExists：观察所有的请求，如果请求尝试创建一个不存在的namespace，则这个请求被拒绝。

### 44、Kubernetes RBAC及其特点（优势）

RBAC是基于角色的访问控制，是一种基于个人用户的角色来管理对计算机或网络资源的访问的方法。

相对于其他授权模式，RBAC具有如下优势：

- 对集群中的资源和非资源权限均有完整的覆盖。
- 整个RBAC完全由几个API对象完成， 同其他API对象一样， 可以用kubectl或API进行操作。
- 可以在运行时进行调整，无须重新启动API Server。

### 45、Kubernetes Secret作用

Secret对象，主要作用是保管私密数据，比如密码、OAuth Tokens、SSH Keys等信息。将这些私密信息放在Secret对象中比直接放在Pod或Docker Image中更安全，也更便于使用和分发。

### 46、Kubernetes Secret有哪些使用方式

创建完secret之后，可通过如下三种方式使用：

- 在创建Pod时，通过为Pod指定Service Account来自动使用该Secret。
- 通过挂载该Secret到Pod来使用它。
- 在Docker镜像下载时使用，通过指定Pod的spc.ImagePullSecrets来引用它。

### 47、Kubernetes PodSecurityPolicy机制

Kubernetes PodSecurityPolicy是为了更精细地控制Pod对资源的使用方式以及提升安全策略。在开启PodSecurityPolicy准入控制器后，Kubernetes默认不允许创建任何Pod，需要创建PodSecurityPolicy策略和相应的RBAC授权策略（Authorizing Policies），Pod才能创建成功。

### 48、Kubernetes PodSecurityPolicy机制能实现哪些安全策略

在PodSecurityPolicy对象中可以设置不同字段来控制Pod运行时的各种安全策略，常见的有：

- 特权模式：privileged是否允许Pod以特权模式运行。
- 宿主机资源：控制Pod对宿主机资源的控制，如hostPID：是否允许Pod共享宿主机的进程空间。
- 用户和组：设置运行容器的用户ID（范围）或组（范围）。
- 提升权限：AllowPrivilegeEscalation：设置容器内的子进程是否可以提升权限，通常在设置非root用户（MustRunAsNonRoot）时进行设置。
- SELinux：进行SELinux的相关配置。

### 49、Kubernetes网络模型

Kubernetes网络模型中每个Pod都拥有一个独立的IP地址，并假定所有Pod都在一个可以直接连通的、扁平的网络空间中。所以不管它们是否运行在同一个Node（宿主机）中，都要求它们可以直接通过对方的IP进行访问。设计这个原则的原因是，用户不需要额外考虑如何建立Pod之间的连接，也不需要考虑如何将容器端口映射到主机端口等问题。

同时为每个Pod都设置一个IP地址的模型使得同一个Pod内的不同容器会共享同一个网络命名空间，也就是同一个Linux网络协议栈。这就意味着同一个Pod内的容器可以通过localhost来连接对方的端口。

在Kubernetes的集群里，IP是以Pod为单位进行分配的。一个Pod内部的所有容器共享一个网络堆栈（相当于一个网络命名空间，它们的IP地址、网络设备、配置等都是共享的）。

### 50、Kubernetes CNI模型

CNI提供了一种应用容器的插件化网络解决方案，定义对容器网络进行操作和配置的规范，通过插件的形式对CNI接口进行实现。CNI仅关注在创建容器时分配网络资源，和在销毁容器时删除网络资源。在CNI模型中只涉及两个概念：容器和网络。

- 容器（Container）：是拥有独立Linux网络命名空间的环境，例如使用Docker或rkt创建的容器。容器需要拥有自己的Linux网络命名空间，这是加入网络的必要条件。
- 网络（Network）：表示可以互连的一组实体，这些实体拥有各自独立、唯一的IP地址，可以是容器、物理机或者其他网络设备（比如路由器）等。

对容器网络的设置和操作都通过插件（Plugin）进行具体实现，CNI插件包括两种类型：CNI Plugin和IPAM（IP Address Management）Plugin。CNI Plugin负责为容器配置网络资源，IPAM Plugin负责对容器的IP地址进行分配和管理。IPAM Plugin作为CNI Plugin的一部分，与CNI Plugin协同工作。

### 51、Kubernetes网络策略

为实现细粒度的容器间网络访问隔离策略，Kubernetes引入Network Policy。

Network Policy的主要功能是对Pod间的网络通信进行限制和准入控制，设置允许访问或禁止访问的客户端Pod列表。Network Policy定义网络策略，配合策略控制器（Policy Controller）进行策略的实现。

### 52、Kubernetes网络策略原理

Network Policy的工作原理主要为：policy controller需要实现一个API Listener，监听用户设置的Network Policy定义，并将网络访问规则通过各Node的Agent进行实际设置（Agent则需要通过CNI网络插件实现）。

### 53、Kubernetes中flannel的作用

Flannel可以用于Kubernetes底层网络的实现，主要作用有：

- 它能协助Kubernetes，给每一个Node上的Docker容器都分配互相不冲突的IP地址。
- 它能在这些IP地址之间建立一个覆盖网络（Overlay Network），通过这个覆盖网络，将数据包原封不动地传递到目标容器内。

### 54、Kubernetes Calico网络组件实现原理

Calico是一个基于BGP的纯三层的网络方案，与OpenStack、Kubernetes、AWS、GCE等云平台都能够良好地集成。

Calico在每个计算节点都利用Linux Kernel实现了一个高效的vRouter来负责数据转发。每个vRouter都通过BGP协议把在本节点上运行的容器的路由信息向整个Calico网络广播，并自动设置到达其他节点的路由转发规则。

Calico保证所有容器之间的数据流量都是通过IP路由的方式完成互联互通的。Calico节点组网时可以直接利用数据中心的网络结构（L2或者L3），不需要额外的NAT、隧道或者Overlay Network，没有额外的封包解包，能够节约CPU运算，提高网络效率。

### 55、Kubernetes共享存储的作用

Kubernetes对于有状态的容器应用或者对数据需要持久化的应用，因此需要更加可靠的存储来保存应用产生的重要数据，以便容器应用在重建之后仍然可以使用之前的数据。因此需要使用共享存储。

### 56、Kubernetes数据持久化的方式有哪些

Kubernetes通过数据持久化来持久化保存重要数据，常见的方式有：

- EmptyDir（空目录）：没有指定要挂载宿主机上的某个目录，直接由Pod内保部映射到宿主机上。类似于docker中的manager volume。
- 场景：
  - 只需要临时将数据保存在磁盘上，比如在合并/排序算法中；
  - 作为两个容器的共享存储。
- 特性：
  - 同个pod里面的不同容器，共享同一个持久化目录，当pod节点删除时，volume的数据也会被删除。
  - emptyDir的数据持久化的生命周期和使用的pod一致，一般是作为临时存储使用。
- Hostpath：将宿主机上已存在的目录或文件挂载到容器内部。类似于docker中的bind mount挂载方式。
- 特性：增加了Pod与节点之间的耦合。

PersistentVolume（简称PV）：如基于NFS服务的PV，也可以基于GFS的PV。它的作用是统一数据持久化目录，方便管理。

### 57、Kubernetes PV和PVC

PV是对底层网络共享存储的抽象，将共享存储定义为一种“资源”。

PVC则是用户对存储资源的一个“申请”。

### 58、Kubernetes PV生命周期内的阶段

某个PV在生命周期中可能处于以下4个阶段（Phaes）之一。

- Available：可用状态，还未与某个PVC绑定。
- Bound：已与某个PVC绑定。
- Released：绑定的PVC已经删除，资源已释放，但没有被集群回收。
- Failed：自动资源回收失败。

### 59、Kubernetes所支持的存储供应模式

Kubernetes支持两种资源的存储供应模式：静态模式（Static）和动态模式（Dynamic）。

- 静态模式：集群管理员手工创建许多PV，在定义PV时需要将后端存储的特性进行设置。
- 动态模式：集群管理员无须手工创建PV，而是通过StorageClass的设置对后端存储进行描述，标记为某种类型。此时要求PVC对存储的类型进行声明，系统将自动完成PV的创建及与PVC的绑定。

### 60、Kubernetes CSI模型

Kubernetes CSI是Kubernetes推出与容器对接的存储接口标准，存储提供方只需要基于标准接口进行存储插件的实现，就能使用Kubernetes的原生存储机制为容器提供存储服务。CSI使得存储提供方的代码能和Kubernetes代码彻底解耦，部署也与Kubernetes核心组件分离，显然，存储插件的开发由提供方自行维护，就能为Kubernetes用户提供更多的存储功能，也更加安全可靠。

CSI包括CSI Controller和CSI Node：

- CSI Controller的主要功能是提供存储服务视角对存储资源和存储卷进行管理和操作。
- CSI Node的主要功能是对主机（Node）上的Volume进行管理和操作。

### 61、Kubernetes Worker节点加入集群的过程

通常需要对Worker节点进行扩容，从而将应用系统进行水平扩展。主要过程如下：

- 在该Node上安装Docker、kubelet和kube-proxy服务；
- 然后配置kubelet和kubeproxy的启动参数，将Master URL指定为当前Kubernetes集群Master的地址，最后启动这些服务；
- 通过kubelet默认的自动注册机制，新的Worker将会自动加入现有的Kubernetes集群中；
- Kubernetes Master在接受了新Worker的注册之后，会自动将其纳入当前集群的调度范围。

### 62、Kubernetes Pod如何实现对节点的资源控制

Kubernetes集群里的节点提供的资源主要是计算资源，计算资源是可计量的能被申请、分配和使用的基础资源。当前Kubernetes集群中的计算资源主要包括CPU、GPU及Memory。CPU与Memory是被Pod使用的，因此在配置Pod时可以通过参数CPU Request及Memory Request为其中的每个容器指定所需使用的CPU与Memory量，Kubernetes会根据Request的值去查找有足够资源的Node来调度此Pod。

通常，一个程序所使用的CPU与Memory是一个动态的量，确切地说，是一个范围，跟它的负载密切相关：负载增加时，CPU和Memory的使用量也会增加。

### 63、Kubernetes Requests和Limits如何影响Pod的调度

当一个Pod创建成功时，Kubernetes调度器（Scheduler）会为该Pod选择一个节点来执行。对于每种计算资源（CPU和Memory）而言，每个节点都有一个能用于运行Pod的最大容量值。调度器在调度时，首先要确保调度后该节点上所有Pod的CPU和内存的Requests总和，不超过该节点能提供给Pod使用的CPU和Memory的最大容量值。

### 64、Kubernetes Metric Service

在Kubernetes从1.10版本后采用Metrics Server作为默认的性能数据采集和监控，主要用于提供核心指标（Core Metrics），包括Node、Pod的CPU和内存使用指标。

对其他自定义指标（Custom Metrics）的监控则由Prometheus等组件来完成。

### 65、Kubernetes中，如何使用EFK实现日志的统一管理

在Kubernetes集群环境中，通常一个完整的应用或服务涉及组件过多，建议对日志系统进行集中化管理，通常采用EFK实现。

EFK是 Elasticsearch、Fluentd 和 Kibana 的组合，其各组件功能如下：

- Elasticsearch：是一个搜索引擎，负责存储日志并提供查询接口；
- Fluentd：负责从 Kubernetes 搜集日志，每个Node节点上面的Fluentd监控并收集该节点上面的系统日志，并将处理过后的日志信息发送给Elasticsearch；
- Kibana：提供了一个 Web GUI，用户可以浏览和搜索存储在 Elasticsearch 中的日志。

通过在每台Node上部署一个以DaemonSet方式运行的Fluentd来收集每台Node上的日志。Fluentd将Docker日志目录/var/lib/docker/containers和/var/log目录挂载到Pod中，然后Pod会在Node节点的/var/log/pods目录中创建新的目录，可以区别不同的容器日志输出，该目录下有一个日志文件链接到/var/lib/docker/contianers目录下的容器日志输出。

### 66、Kubernetes如何进行优雅的节点关机维护

由于Kubernetes节点运行大量Pod，因此在进行关机维护之前，建议先使用kubectl drain将该节点的Pod进行驱逐，然后进行关机维护。

### 67、Kubernetes集群联邦

Kubernetes集群联邦可以将多个Kubernetes集群作为一个集群进行管理。因此，可以在一个数据中心/云中创建多个Kubernetes集群，并使用集群联邦在一个地方控制/管理所有集群。

### 68、Helm及其优势

Helm是Kubernetes的软件包管理工具。类似Ubuntu中使用的APT、CentOS中使用的yum 或者Python中的 pip 一样。

Helm能够将一组Kubernetes资源打包统一管理, 是查找、共享和使用为Kubernetes构建的软件的最佳方式。

Helm中通常每个包称为一个Chart，一个Chart是一个目录（一般情况下会将目录进行打包压缩，形成name-version.tgz格式的单一文件，方便传输和存储）。

在Kubernetes中部署一个可以使用的应用，需要涉及到很多的 Kubernetes 资源的共同协作。使用Helm则具有如下优势：

- 统一管理、配置和更新这些分散的Kubernetes的应用资源文件；
- 分发和复用一套应用模板；
- 将应用的一系列资源当做一个软件包管理。
- 对于应用发布者而言，可以通过 Helm 打包应用、管理应用依赖关系、管理应用版本并发布应用到软件仓库。

对于使用者而言，使用Helm后不用需要编写复杂的应用部署文件，可以以简单的方式在Kubernetes上查找、安装、升级、回滚、卸载应用程序。

### 69、k8s

答：Kubenetes是一个针对容器应用，进行自动部署，弹性伸缩和管理的开源系统。主要功能是生产环境中的容器编排。

K8S是Google公司推出的，它来源于由Google公司内部使用了15年的Borg系统，集结了Borg的精华。

### 70、K8s架构的组成

答：和大多数分布式系统一样，K8S集群至少需要一个主节点（Master）和多个计算节点（Node）。

- 主节点主要用于暴露API，调度部署和节点的管理；
- 计算节点运行一个容器运行环境，一般是docker环境（类似docker环境的还有rkt），同时运行一个K8s的代理（kubelet）用于和master通信。计算节点也会运行一些额外的组件，像记录日志，节点监控，服务发现等等。计算节点是k8s集群中真正工作的节点。

#### K8S架构细分：

1、Master节点（默认不参加实际工作）：

- Kubectl：客户端命令行工具，作为整个K8s集群的操作入口；
- Api Server：在K8s架构中承担的是“桥梁”的角色，作为资源操作的唯一入口，它提供了认证、授权、访问控制、API注册和发现等机制。客户端与k8s群集及K8s内部组件的通信，都要通过Api Server这个组件；
- Controller-manager：负责维护群集的状态，比如故障检测、自动扩展、滚动更新等；
- Scheduler：负责资源的调度，按照预定的调度策略将pod调度到相应的node节点上；
- Etcd：担任数据中心的角色，保存了整个群集的状态；

2、Node节点：

- Kubelet：负责维护容器的生命周期，同时也负责Volume和网络的管理，一般运行在所有的节点，是Node节点的代理，当Scheduler确定某个node上运行pod之后，会将pod的具体信息（image，volume）等发送给该节点的kubelet，kubelet根据这些信息创建和运行容器，并向master返回运行状态。（自动修复功能：如果某个节点中的容器宕机，它会尝试重启该容器，若重启无效，则会将该pod杀死，然后重新创建一个容器）；
- Kube-proxy：Service在逻辑上代表了后端的多个pod。负责为Service提供cluster内部的服务发现和负载均衡（外界通过Service访问pod提供的服务时，Service接收到的请求后就是通过kube-proxy来转发到pod上的）；
- container-runtime：是负责管理运行容器的软件，比如docker
- Pod：是k8s集群里面最小的单位。每个pod里边可以运行一个或多个container（容器），如果一个pod中有两个container，那么container的USR（用户）、MNT（挂载点）、PID（进程号）是相互隔离的，UTS（主机名和域名）、IPC（消息队列）、NET（网络栈）是相互共享的。我比较喜欢把pod来当做豌豆夹，而豌豆就是pod中的container；

### 71、容器和主机部署应用的区别

答：容器的中心思想就是秒级启动；一次封装、到处运行；这是主机部署应用无法达到的效果，但同时也更应该注重容器的数据持久化问题。

另外，容器部署可以将各个服务进行隔离，互不影响，这也是容器的另一个核心概念。

### 89、k8s是怎么进行服务注册的？

答：Pod启动后会加载当前环境所有Service信息，以便不同Pod根据Service名进行通信。

### 95、节点心跳的处理

当节点控制器检测到某个节点的心跳丢失时，会执行以下操作：

节点心跳是 Kubernetes 中确保节点健康和集群稳定的重要机制。通过 `kubelet` 定期报告节点状态，`kube-apiserver` 和节点控制器可以及时检测节点的健康状况，并在节点发生故障时采取相应措施（如驱逐 Pod）。正确配置这些参数可以提高集群的稳定性和可靠性。如果你有更多问题或需要进一步的解释，欢迎继续提问。

1. **标记节点状态** ：将节点状态标记为 "NotReady"。
2. **驱逐 Pod** ：如果节点在默认 5 分钟内（可配置）未恢复，节点控制器会开始驱逐该节点上的 Pod。驱逐操作确保 Pod 可以在其他健康节点上重新调度和运行

Kubernetes（k8s）中，CIDR（Classless Inter-Domain Routing，无类域间路由）分配是一种用于管理和分配 IP 地址空间的方法，尤其是在大规模集群中非常重要。

### 96、PodDisruptionBudget和**Deployment 的最小副本数**的区别

 **Deployment 的最小副本数** ：确保在正常运行条件下，应用至少有指定数量的副本。

 **PodDisruptionBudget (PDB)** ：在自愿干扰（如维护或更新）期间，确保应用至少有一定数量的可用副本以维持服务可用性。

### 97、k8s中Qos的服务质量级别

Guaranteed：Pod 请求的资源被保证。不会因资源不足被驱逐或杀死。
Burstables：Pod 请求的资源在必要时可以扩展。资源紧张时可能被驱逐。
BestEffort： Pod 没有请求或限制资源。资源使用根据系统资源情况调整，资源紧张时最可能被驱逐。

### 101、什么是树外存储？什么是树内存储？

### 104、容器启动过程

1. **拉取镜像**：

   - 容器运行时（如 Docker、containerd）从镜像仓库拉取镜像的所有层到本地存储。
2. **联合文件系统**：

   - 使用联合文件系统（如 OverlayFS）将镜像的所有层叠加在一起，形成一个统一的文件系统视图。这些层以只读的方式叠加在一起。
3. **创建可写层**：

   - 为容器创建一个新的可写层，用于保存容器运行时的文件系统变更。这个可写层叠加在所有只读层的最上面。
4. **挂载文件系统**：

   - 将合并后的文件系统挂载为容器的根文件系统。
5. **初始化容器环境**：

   - 设置容器的命名空间、cgroups、网络配置和安全策略等。
6. **启动容器进程**：

   - 启动容器内的 init 进程或用户指定的应用程序进程。

### 105、联合文件系统和根文件系统的区别

#### 联合文件系统（Union Filesystem）

**定义**：将多个独立的文件系统层叠加在一起，形成一个统一的文件系统视图。

**特点**：

- **分层结构**：由多个只读层和一个可写层组成。
- **写时复制（Copy-on-Write）**：修改时将文件从只读层复制到可写层。
- **高效存储**：共享基础层，节省存储空间。
- **动态合并**：提供实时的文件系统视图。

**应用**：

- **容器技术**：广泛用于容器镜像管理（如 Docker）。

#### 根文件系统（Root Filesystem）

**定义**：操作系统的最顶层目录结构，挂载在 `/` 目录下，包含所有必要的系统目录和文件。

**特点**：

- **单层结构**：是一个完整的文件系统树。
- **操作系统核心**：包含系统启动和运行所需的所有基本目录和文件。
- **多种文件系统类型**：如 ext4、xfs、btrfs、zfs。

**应用**：

- **操作系统**：支持操作系统的启动和运行。

也就是说，其实最终的根文件系统是联合文件系统根据每一层镜像层叠加起来的，一层单独的镜像层可能只是一个目录，叠加起来之后就成为了根文件系统，如果我要对某个镜像层进行修改，这时联合文件系统会复制一份我想要修改的文件到可写层，我修改可写层并不影响镜像，而且如果我想把我修改后的容器作为镜像，那么就会把我的可写层保存为一层一层新的镜像层，产生一个新的镜像，这也解释了为什么我基于其他人的成品镜像修改之后打出来的镜像为什么体积会那么大，因为多了很多大体重复修改微小的镜像层

### 106、负载均衡、正向代理与反向代理

1. **负载均衡**：

   - **功能**：将流量分配到多台服务器，提升性能和可靠性。
   - **工具**：Nginx、HAProxy、LVS 等。
2. **反向代理**：

   - **功能**：代理服务器接受外部请求并转发到内部服务器，隐藏后端服务器的具体信息。
   - **例子**：Nginx 作为反向代理，将80端口的请求根据路径转发到不同的后端端口。
3. **正向代理**：

   - **功能**：代理客户端的请求，代表客户端向外部服务器发送请求，提供缓存、内容过滤和隐私保护等功能。
   - **用途**：企业网络中常用于控制和监控员工上网行为。
4. **堡垒机**：

   - **功能**：作为内网安全管理和访问控制的关键点，提供身份验证、日志记录和安全审查。
   - **例子**：设置一台专用服务器用于内网穿透，并集中管理外部访问。

### 107、堡垒机的实现

1. 外部流量的拦截和管理

**反向代理**：

- **目的**：保护内网资源，防止未经授权的外部访问。
- **实现方式**：在堡垒机上部署反向代理（如Nginx），拦截所有进入内网的外部请求，并进行身份验证和访问控制。这样外部用户需要通过反向代理进行访问，反向代理会根据配置将请求转发到内网的特定服务。
- **优点**：实现较为简便，集中管理外部访问流量，提供细粒度的访问控制。

2. 内网对外网访问的限制

**正向代理**：

- **目的**：控制和监控内网用户对外网的访问，防止数据泄露和访问不良网站。
- **实现方式**：在堡垒机上部署正向代理（如Squid），配置默认拒绝策略，只允许经过管理员批准的外网访问。这样内网设备的所有外网请求都需要通过正向代理进行审查和过滤。
- **优点**：集中管理内网用户的外网访问行为，提供内容过滤和日志记录等功能。

3. 整体配置的优点

- **集中管理**：通过在堡垒机上部署反向代理和正向代理，可以集中管理内外网的访问请求，简化运维和管理。
- **安全性**：提高了内网资源的安全性，防止未经授权的访问和数据泄露。
- **灵活性**：可以根据具体需求配置细粒度的访问控制策略和审查机制，确保网络安全和合规。

### 109、Kubernetes中读取配置的优先级

**配置读取的优先级顺序为：**

1. 环境变量
2. 命令行参数
3. 挂载的ConfigMap文件
4. 默认配置文件
