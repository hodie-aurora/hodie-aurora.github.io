---
layout:     post   				# 使用的布局（不需要改）
title:      Kubemate A Good           		# 标题 
subtitle:   Kubemate A Good 				#副标题
date:       2025-06-18				# 时间
author:     zhaohaiwen 				# 作者
header-img: img/post-bg-2025-01-07.jpg		#这篇文章标题背景图片
catalog: true 					# 是否归档
tags:						#标签
    - Kubemate
---
0.模块速记

[模块速记](https://hodie-aurora.github.io/2025/06/18/Kubemate-%E6%A8%A1%E5%9D%97%E9%80%9F%E8%AE%B0/)

1.遇到过哪些复杂问题？

loki相关

[高基数标签问题](https://hodie-aurora.github.io/2025/06/03/Kubemate-Loki-%E9%AB%98%E5%9F%BA%E6%95%B0%E6%A0%87%E7%AD%BE%E9%97%AE%E9%A2%98/)

skywalking相关

[偶发性交易结算数据不一致问题](https://hodie-aurora.github.io/2025/06/03/Kubemate-%E9%93%BE%E8%B7%AF%E8%BF%BD%E8%B8%AA-%E5%81%B6%E5%8F%91%E6%80%A7%E4%BA%A4%E6%98%93%E7%BB%93%E7%AE%97%E6%95%B0%E6%8D%AE%E4%B8%8D%E4%B8%80%E8%87%B4%E9%97%AE%E9%A2%98/)

[因安全策略导致的关键业务链路中断](https://hodie-aurora.github.io/2025/06/03/Kubemate-%E9%93%BE%E8%B7%AF%E8%BF%BD%E8%B8%AA-%E5%9B%A0%E5%AE%89%E5%85%A8%E7%AD%96%E7%95%A5%E5%AF%BC%E8%87%B4%E7%9A%84%E5%85%B3%E9%94%AE%E4%B8%9A%E5%8A%A1%E9%93%BE%E8%B7%AF%E4%B8%AD%E6%96%AD/)

2.哪个模块需要改进？

[CICD集成gitops应用交付中心](https://hodie-aurora.github.io/2025/06/03/Kubemate-CICD-%E9%9B%86%E6%88%90-GitOps-%E5%BA%94%E7%94%A8%E4%BA%A4%E4%BB%98%E4%B8%AD%E5%BF%83/)

3.最复杂的模块是哪个？

[监控与告警模块](https://hodie-aurora.github.io/2025/06/03/Kubemate-%E7%9B%91%E6%8E%A7%E4%B8%8E%E5%91%8A%E8%AD%A6%E6%A8%A1%E5%9D%97/)

4.ERP出现过什么问题？

[低效LogQL查询问题](https://hodie-aurora.github.io/2025/06/03/Kubemate-Loki-%E4%BD%8E%E6%95%88LogQL%E6%9F%A5%E8%AF%A2%E9%97%AE%E9%A2%98/)

5.为开源社区做出过哪些具体的贡献，能详细讲一讲吗？

也就是说，其实根本原因是querynode这个组件被HPA和operator同时控制的时候升级时HPA和operator会互相干扰，升级流程是，本来有两个deploy，正常情况下deployA为1，deployB为0，正常的升级是把deployB使用最新版本个数调整为1个，等到B正常工作之后再把deployA个数设置为0，但是HPA不允许deployA被设置为0，如果设置为0会自动扩容，我们的解决方案是，在启动HPA时自动设置为OneDeployMode单部署模式，非HPA场景保留TwoDeployMode

6.各个模块都是怎么组成的？为什么各个模块采用目前的方案，这些方案好在哪里？和同等技术横向对比一下？

[技术选型](https://hodie-aurora.github.io/2025/06/03/Kubemate-%E6%8A%80%E6%9C%AF%E9%80%89%E5%9E%8B/)

7.当你遇到问题之后，有没有深入探究导致问题的具体原因？比如为什么skywalking更耗能？为什么jaeger比skywalking启动慢了1.5倍？你又是这么排查这些问题的？

SkyWalking 为了提供**高保真、细粒度、开箱即用**的监控体验，其探针在**运行时插桩的深度、生成数据的丰富度以及探针本身的复杂性**上通常比一些更轻量级或更专注的工具要高，这不可避免地带来了更高的运行时资源消耗

OTel 在启动时最大的开销来自  **Java Agent 对大量类进行扫描和字节码增强的耗时过程** ，其次是  **OTel SDK 众多组件的复杂初始化流程** （包括配置解析、资源发现、导出器连接、依赖处理等）。这些操作都发生在应用业务逻辑启动之前，因此会显著增加整体的容器启动时间，尤其在大型应用中。

8.你的技术选型都是怎样决定的？是通过开源社区还是什么？都有哪些方面的考量？

技术选项的原则的低成本的满足当下目标和未来可能的需求，并保留扩展能力，选型的渠道有：CNCF的毕业项目或孵化项目，业界类似平台技术栈，技术社区与行业会议（比如github、k8s的slack、技术博客、Reddit、kubecon等行业会议），对于我们新研发的kubemate版本，会在我司的ERP环境下进行验证和优化，确认技术可行并且没有重大bug才会发行稳定版给我们的金融客户使用。

9.CNI等等概念有接触吗？你们用的都是什么？为什么这样选择？有详细了解过吗？

| 术语          | 功能与作用                                                                                    | 关键补充                                                                                    |
| ------------- | --------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| **OCI** | **容器标准化**：定义容器镜像格式（OCI Image Spec）和运行时规范（OCI Runtime Spec）。    | 底层基石，K8s 通过 containerd 调用 OCI 运行时（如 `runc`）。                              |
| **CRI** | **解耦容器运行时**：为 Kubelet 提供操作容器的统一接口（创建/销毁/执行命令）。           | CRI 是接口，containerd/CRI-O 是实现该接口的**容器运行时**。                           |
| **CNI** | **Pod 网络互联**：为 Pod 配置网络栈（IP、路由、网卡），实现跨节点通信。                 | 插件化设计，Flannel/Calico 是具体实现。                                                     |
| **CSI** | **Pod 存储扩展**：对接外部存储系统（云盘/NFS/CEPH），为 Pod 提供持久化卷。              | 支持动态卷供应、快照、扩容等高级能力。                                                      |
| **SMI** | **服务流量治理标准**：定义流量拆分、访问控制等 API 规范，**由服务网格具体实现**。 | 自身不处理流量，而是**控制服务网格的行为**（如 Istio 监听 SMI API 生成 Envoy 配置）。 |
| **CPI** | **云平台集成**：对接公有云 API，管理 LB、磁盘、节点等基础设施。                         | 替代旧版 in-tree 云驱动，提升可扩展性。                                                     |

10.什么是pause容器？pause容器起到什么作用？

pause 容器是 Pod 内一个最先启动的非常不起眼的容器，用来达到 Pod 内容器之间共享 IP、网络、PID、IPC 等基础设施的效果。Pod 内用户容器重启时不会影响 pause 容器（Pod IP 保持不变），且 pause 容器生命周期与 Pod 相同。如果整个 Pod 被删除或重新创建（例如修改了 Pod 的 spec 导致重建），pause 容器也会被销毁并重新创建。默认情况下，新创建的 pause 容器会获得新 IP 地址；但在特定配置下（如使用 StatefulSet 并搭配支持 IP 保留的 CNI 插件，例如 Calico/Cilium/AWS VPC CNI），Kubernetes 可能为新 Pod 分配与旧 Pod 相同的 IP 地址。

11.golang怎么排查堆栈异常？

12.golang并发模型

[golang并发模型](https://www.yuque.com/aceld/golang/srxd6d)

Go 并发的核心是其运行时（runtime）调度器实现的  **GMP 模型** 。

* **G (Goroutine)** ：是 Go 语言中并发执行的基本单位，它是一个轻量级的执行体，仅包含指令指针、栈等少量信息，创建和销毁的成本极低。
* **M (Machine)** ：代表一个内核线程，是操作系统线程的抽象，是真正执行代码的实体。
* **P (Processor)** ：是逻辑处理器，作为 G 和 M 之间的调度上下文。每个 P 拥有一个本地的可运行 G 队列，并管理着执行 Goroutine 所需的资源。

其原理是：调度器将 P 与 M 进行绑定，M 从 P 的本地队列中不断取出 G 并运行。如果 P 的队列为空，它会从其他 P 或全局队列中“窃取”G 来执行（ **任务窃取** ），以实现负载均衡。当一个 G 因系统调用或网络 IO 等原因阻塞时，调度器会将这个 M 从 P 上解绑，并为 P 寻找另一个空闲的 M 来继续执行队列中的其他 G

13.golang垃圾回收算法

[golang垃圾回收算法](https://www.yuque.com/aceld/golang/zhzanb)

从根节点开始扫描，初始对象被标记为 **灰色** 。每执行一轮，灰色对象变为 **黑色** ，其引用的白色对象被标记为 **灰色** 。这个过程持续到不再有灰色对象为止，最终内存中只剩下黑色（存活）和白色（垃圾）对象，随后清除所有白色对象。

Go 的垃圾回收在三色标记清除算法上采用了 **混合写屏障（Hybrid Write Barrier）** 。当 GC 与用户程序并发执行时，为防止因对象引用关系被修改而导致活对象被误删，混合写屏障对**堆（Heap）**和**栈（Stack）**内存采取了不同的策略：在**堆**上，写屏障会拦截黑色对象指向白色对象的操作，将被引用的白色对象强制标记为灰色；而在**栈**上，则不使用写屏障，作为替代，GC 会在初始阶段一次性扫描所有栈，确保栈上可达的对象都得到标记。

14.什么是容器逃逸？怎么阻止容器逃逸？

**容器逃逸手段速记**

1. **运行时漏洞**：利用 Docker/runc 漏洞逃逸。
2. **特权模式**： `--privileged` 或挂载 `/docker.sock`。
3. **内核漏洞**：共享内核，提权到宿主机。
4. **不安全镜像**：恶意镜像执行逃逸代码。
5. **系统调用**：弱 seccomp，滥用危险调用。
6. **Kubernetes 配置**：高权限 SA 或 `hostPath`。
7. **网络攻击**：弱网络策略，访问管理接口。

**防御速记**

1. **最小权限**：禁特权，限权能，非 root。
2. **强隔离**：用 seccomp、AppArmor、用户命名空间。
3. **及时更新**：修补运行时、内核、镜像。
4. **安全镜像**：扫描漏洞，用可信来源。
5. **监控审计**：Falco 检测，日志分析。

15.kubernetes有哪些组件？起到什么作用？

控制平面 (Control Plane) - 集群大脑

kube-apiserver: 所有组件交互的统一入口，提供 API 服务。
etcd: 存储整个集群的状态和配置数据的键值数据库。
kube-scheduler: 负责将新创建的 Pod 分配到最合适的节点上。
kube-controller-manager: 运行各种控制器，维持集群的期望状态（如副本数、节点状态）。

节点 (Node) - 工作机器

kubelet: 节点上的代理，负责管理 Pod 和容器的生命周期。
kube-proxy: 维护节点上的网络规则，实现 Service 的服务发现和负载均衡。
容器运行时 (Container Runtime): 真正运行容器的软件，如 docker 或 containerd。

16.如何打包出非常小的镜像？

多阶段构建：最核心的方法。多阶段构建通过将构建过程拆分为多个阶段，优化了 Docker 镜像的体积、效率和安全性。
使用最小基础镜像

17.容器如何二次瘦身？

DockerSlim通过静态和动态分析，监控容器应用在运行时真正使用的文件和依赖，基于监控结果，生成一个全新的、只包含必要文件的镜像

18.milvus与es的区别？

19.iptables如何配置？iptables如何组成的？

**组成：表 (Table) → 链 (Chain) → 规则 (Rule)**

* **表 (Tables)** : 按功能划分。
* `filter`: 过滤 (ACCEPT, DROP, REJECT)，默认表。
* `nat`: 网络地址转换 (SNAT, DNAT)。
* **链 (Chains)** : 数据包流经的关卡。
* `INPUT`: 进入本机的数据包。
* `OUTPUT`: 从本机发出的数据包。
* `FORWARD`: 经过本机转发的数据包。
* `PREROUTING` / `POSTROUTING`: 用于地址转换。
* **规则 (Rules)** : 具体的匹配条件和动作。

 **核心配置命令** :

* `iptables -L -n -v`: 查看规则 (L=List, n=numeric, v=verbose)。
* `iptables -A INPUT ... -j ACCEPT`: 追加 (Append) 规则。
* `iptables -D INPUT <num>`: 删除 (Delete) 规则。
* `iptables -P INPUT DROP`: 设置链的默认策略 (Policy)。
* `iptables -F`: 清空 (Flush) 所有规则。
* `service iptables save` 或 `iptables-save > file`: 保存规则。

20.集群或服务器断网了如何排查？

**排查路线图：由底层到上层**

1. **物理/链路层** :

* `ip addr` / `ifconfig`: 检查网卡状态是否 `UP`，IP地址是否正确。

1. **网络层 (IP/路由)** :

* `ping <网关IP>`: 检查到网关的连通性。
* `ip route` / `netstat -rn`: 检查默认路由是否存在。
* `ping 8.8.8.8`: 检查公网连通性。
* `mtr 8.8.8.8`: 诊断到公网各节点的延迟和丢包，定位故障点。

1. **传输层/应用层 (DNS/防火墙)** :

* `dig <域名>` / `nslookup <域名>`: 检查DNS解析是否正常。
* `iptables -L -n` / `firewall-cmd --list-all`: 检查本机防火墙规则。
* **云平台** : 检查安全组和网络ACL规则。

21.SNI是什么？

* **全称** : Server Name Indication (服务器名称指示)。
* **目的** : 解决**一个IP地址+端口只能绑定一个SSL证书**的问题。
* **原理** : 在TLS握手阶段，客户端通过 `ClientHello` 消息中的 `server_name` 扩展字段，明确告诉服务器自己想访问哪个域名。服务器根据这个域名返回对应的SSL证书。
* **作用** : 使得在 **单个IP上可以托管多个不同的HTTPS网站** ，极大节约了IPv4地址。

22.向量余弦相似度是什么？

一种衡量两个向量在**方向上**的相似程度的指标，忽略其大小或长度。
