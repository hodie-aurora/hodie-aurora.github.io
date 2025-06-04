---
layout:     post   				# 使用的布局（不需要改）
title:      06 Kubernetes CI CD 集成 		# 标题 
subtitle:   Kubernetes 生态系统集成系列		#副标题
date:       2025-01-20				# 时间
author:     zhaohaiwen 				# 作者
header-img: img/post-bg-2025-01-07.jpg		#这篇文章标题背景图片
catalog: true 					# 是否归档
tags:						#标签
    - Kubernetes 生态系统集成系列
---
### 微调流程

1. **准备数据和模型**:

   - 上传微调数据集（如 RAG 任务的问答对，格式为 JSON 或文本）和 DeepSeek 预训练模型到 MinIO 存储。
   - 可选：预处理数据（如生成嵌入向量存入 Milvus，用于数据分析或验证）。
2. **执行微调任务**:

   - 启动 K8s Job，运行包含 Hugging Face Transformers 的容器。
   - 容器从 MinIO 加载数据集和 DeepSeek 模型，使用 LoRA（参数高效微调）优化模型，针对特定任务（如金融风控问答）调整性能。
   - 微调过程利用 GPU 资源（通过 NVIDIA GPU Operator 分配），记录超参数和性能指标（如损失函数）到 MLflow。
3. **保存微调结果**:

   - 微调完成后，生成模型 checkpoint（包含 LoRA 适配器参数），保存到 MinIO。
   - MLflow 存储实验记录，包括超参数、指标和 checkpoint 位置，便于版本管理和选择最佳模型。
4. **部署微调模型**:

   - Ollama 从 MinIO 加载微调后的 checkpoint（可能需格式转换），部署为 K8s 服务，提供 REST API 用于推理。
   - Redis 缓存推理中间结果（如热门查询），Milvus 支持 RAG 任务的向量检索。
5. **验证和监控**:

   - 使用 Milvus 测试微调后模型的 RAG 性能（如检索准确率）。
   - Prometheus 和 Grafana 监控微调任务的资源使用（如 GPU 利用率）和推理性能（如延迟、吞吐量）。
   - 通过 MLflow 查看实验结果，确认模型优化效果。

---

### 流程特点

- **轻量高效**：使用 LoRA 减少 GPU 和存储需求，适合单机微调。
- **集成 K8s**：通过 K8s Job 和 MinIO 实现自动化和持久化。
- **可观测性**：MLflow 跟踪实验，Prometheus + Grafana 提供实时监控。
- **场景适用**：优化 DeepSeek 模型，满足金融风控、客服自动化等需求。
