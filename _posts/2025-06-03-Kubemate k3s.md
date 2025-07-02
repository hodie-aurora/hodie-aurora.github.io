---
layout:     post   				# 使用的布局（不需要改）
title:      k3s文档阅读顺序                                 # 标题 
subtitle:   k3s文档阅读顺序 				#副标题
date:       2025-07-02				# 时间
author:     zhaohaiwen 				# 作者
header-img: img/post-bg-2025-01-07.jpg		#这篇文章标题背景图片
catalog: true 					# 是否归档
tags:						#标签
    - k3s
---
### 完整文档阅读顺序列表

#### 阶段一：架构决策记录 (ADRs) - 理解 K3s 的设计哲学

* **起始点：元信息与核心架构**

  1. `docs/adrs/record-architecture-decisions.md` - **【首读】** 了解什么是 ADR 以及为什么它们很重要。
  2. `docs/adrs/standalone-containerd.md` - 了解 K3s 与核心组件 containerd 的关系。
  3. `docs/adrs/cri-dockerd.md` - 了解为支持 Docker 而引入的 cri-dockerd。
  4. `docs/adrs/etcd-snapshot-cr.md` - 了解 K3s 的核心数据存储及其快照机制。
  5. `docs/adrs/etcd-s3-secret.md` - 了解如何将快照安全地备份到 S3。
  6. `docs/adrs/status-for-etcd-node.md` - 了解如何获取和展示 etcd 节点的状态。
* **集群构建与网络**

  7. `docs/adrs/agent-join-token.md` - 理解 Agent 节点加入集群所使用的令牌机制。
  8. `docs/adrs/server-token-rotation.md` - 理解 Server 令牌的轮换机制，增强安全性。
  9. `docs/adrs/servicelb-ccm.md` - 了解 K3s 内置的 Service LoadBalancer (ServiceLB) 如何演变为云控制器管理器（CCM）。
  10. `docs/adrs/remove-svclb-daemonset.md` - 了解 ServiceLB 从 DaemonSet 模式中移除的历史。
  11. `docs/adrs/flannel-options.md` - 了解 K3s 默认 CNI (Flannel) 的可配置选项。
  12. `docs/adrs/add-dual-stack-support-to-netpol-agent.md` - 了解网络策略代理如何支持 IPv4/IPv6 双栈。
  13. `docs/adrs/multus.md` - 了解如何集成 Multus 以支持在 Pod 上使用多个网络接口。
  14. `docs/adrs/integrate-vpns.md` - 了解 K3s 集成 VPN 的设计思路。
* **安全与证书管理**

  15. `docs/adrs/ca-cert-rotation.md` - **【核心安全】** 理解 K3s 的 CA 证书轮换机制。
  16. `docs/adrs/secrets-encryption-v3.md` - 理解 Kubernetes Secret 的静态加密机制是如何实现的。
  17. `docs/adrs/cert-expiry-checks.md` - 了解 K3s 如何检查证书的过期时间。
  18. `docs/adrs/core-controller-user.md` - 了解为核心控制器创建专门用户的安全实践。
* **附加功能与项目维护**

  19. `docs/adrs/embedded-registry.md` - 了解 K3s 内置镜像仓库的功能。
  20. `docs/adrs/k3s-charts.md` - 了解 K3s 如何管理打包的 Helm charts。
  21. `docs/adrs/add-auto-import-containerd.md` - 了解容器镜像自动导入的机制。
  22. `docs/adrs/deprecating-and-removing-flags.md` - 了解项目如何管理和废弃旧的命令行参数。
  23. `docs/adrs/gh-branch-strategy.md` - 了解项目的 Github 分支管理策略。
  24. `docs/adrs/testing-2024.md` - 了解项目在2024年的测试策略和理念。
  25. `docs/adrs/security-updates-automation.md` - 了解安全更新的自动化流程。

#### 阶段二：贡献者指南 - 学习如何参与项目

26. `docs/contrib/development.md` - **【贡献起点】** 如何设置本地开发环境。
27. `docs/contrib/git_workflow.md` - 学习标准的 Git 工作流（分支、提交、PR）。
28. `docs/contrib/code_conventions.md` - 学习项目的编码规范。
29. `docs/contrib/continuous_integration.md` - 了解 CI 系统如何自动检查你的代码。

#### 阶段三：实用工具与脚本 - 探索 K3s 的运维实践

30. `contrib/util/DIAGNOSTICS.md` - **【运维必读】** 学习如何使用诊断工具。
31. `contrib/util/diagnostics.sh` - 阅读诊断脚本源码，了解它具体收集了哪些信息。
32. `contrib/util/fetch-diags.sh` - 阅读用于获取诊断包的辅助脚本。
33. `contrib/util/rotate-default-ca-certs.sh` - 阅读脚本，了解轮换 CA 证书的具体命令。
34. `contrib/util/generate-custom-ca-certs.sh` - 阅读脚本，了解如何生成自定义 CA。
35. `contrib/util/check-config.sh` - 阅读脚本，了解如何检查配置。
36. `contrib/ansible/README.md` - 了解如何使用 Ansible 进行自动化部署。
37. `contrib/gotests_templates/header.tmpl` - 查看 Go 测试文件的头部模板。
38. `contrib/gotests_templates/function.tmpl` - 查看测试函数的模板。
39. `contrib/gotests_templates/call.tmpl` - 查看函数调用的模板。
40. `contrib/gotests_templates/inputs.tmpl` - 查看测试输入的模板。
41. `contrib/gotests_templates/results.tmpl` - 查看测试结果的模板。
42. `contrib/gotests_templates/message.tmpl` - 查看测试消息的模板。
43. `contrib/gotests_templates/inline.tmpl` - 查看内联测试的模板。

#### 阶段四：发布流程 - 深入了解项目维护细节

44. `docs/release/release.md` - **【发布概览】** 了解发布新版本的总体流程。
45. `docs/release/kubernetes-upgrade.md` - 了解跟随上游 Kubernetes 版本升级的专门流程。
46. `docs/release/expanded/setup_env.md` - 了解发布所需的环境变量设置。
47. `docs/release/expanded/setup_k8s_repos.md` - 了解如何设置上游 Kubernetes 的代码仓库。
48. `docs/release/expanded/setup_k3s_repos.md` - 了解如何设置 K3s 自己的代码仓库。
49. `docs/release/expanded/channel_server.md` - 了解 K3s 版本通道（Channel）服务器的工作原理。
50. `docs/release/expanded/milestones.md` - 了解如何使用 GitHub Milestones 进行版本规划。
51. `docs/release/expanded/pr.md` - 了解在发布周期中如何处理 Pull Requests。
52. `docs/release/expanded/rebase.md` - 了解发布分支的 rebase 操作。
53. `docs/release/expanded/update_kdm.md` - 了解如何更新 KDM（K3s Data Manager）数据。
54. `docs/release/expanded/cut_release.md` - **【核心发布步骤】** 了解创建新版本的具体步骤。
55. `docs/release/expanded/tagging.md` - 了解版本发布的打标（Tagging）规范。
56. `docs/release/expanded/build_container.md` - 了解如何构建发布的容器镜像。
57. `docs/release/expanded/release_images.md` - 了解如何发布这些容器镜像。
58. `docs/release/expanded/release_notes.md` - 了解如何撰写和生成版本发布说明。
59. `docs/release/expanded/setup_rc.md` - 了解如何设置和发布一个 RC（Release Candidate）版本。
