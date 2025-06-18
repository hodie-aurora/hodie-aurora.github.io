---
layout:     post   				# 使用的布局（不需要改）
title:      Kubemate A Good           		# 标题 
subtitle:   Kubemate A Good 				#副标题
date:       2025-06-03				# 时间
author:     zhaohaiwen 				# 作者
header-img: img/post-bg-2025-01-07.jpg		#这篇文章标题背景图片
catalog: true 					# 是否归档
tags:						#标签
    - Kubemate
---
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

6.为什么选择thanos+minio，这个方案好在哪里？

7.各个模块都是这么组成的？为什么这么选择，当前的方案好在哪里？

8.当你遇到问题之后，有没有深入探究导致问题的具体原因？比如为什么skywalking更耗能？为什么jaeger比skywalking启动慢了1.5倍？你又是这么排查这些问题的？

9.你的技术选型都是怎样决定的？是通过开源社区还是什么？都有哪些方面的考量？

[技术选型](https://hodie-aurora.github.io/2025/06/03/Kubemate-%E6%8A%80%E6%9C%AF%E9%80%89%E5%9E%8B/)

10.CNI等等概念有接触吗？你们用的都是什么？为什么这样选择？有详细了解过吗？

11.什么是pause容器？pause容器起到什么作用？

pause容器是pod内一个最先启动的非常不起眼的容器，用来达到pod内容器之间共享ip、网络、PID、IPC等基础设施的效果，pod重启也不会影响pause容器，和pod生命周期相同。如果整个 Pod 被删除或重新创建（例如修改了 Pod 的 spec 导致重建），pause 容器也会被销毁并重新创建。不过，Kubernetes 的 CNI 插件会确保新创建的 pause 容器继承相同的 IP 地址