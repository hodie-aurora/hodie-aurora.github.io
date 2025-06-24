---
layout:     post   				# 使用的布局（不需要改）
title:      Kubemate Milvus           		# 标题 
subtitle:   Kubemate Milvus 				#副标题
date:       2025-06-18				# 时间
author:     zhaohaiwen 				# 作者
header-img: img/post-bg-2025-01-07.jpg		#这篇文章标题背景图片
catalog: true 					# 是否归档
tags:						#标签
    - Kubemate
---
1.milvus是如何保持数据一致性的？

- **元数据一致性**：etcd 存储元数据，Raft 协议确保强一致性。
- **数据分片与复制**：多分片多副本，主分片写操作通过日志流同步副本。
- **日志流持久化**：写操作记录在 Pulsar/Kafka，异步持久化到对象存储。
- **一致性级别**：
  - 强一致：读最新数据，等待副本同步。
  - 会话一致：同一会话读写一致。
  - 最终一致：允许短暂数据滞后。
- **故障恢复**：DataCoord/QueryCoord 管理分片，日志重放恢复数据。
- **Operator 部署**：Kubernetes 调度 Pod，CRD 配置副本与一致性，保障高可用性。
