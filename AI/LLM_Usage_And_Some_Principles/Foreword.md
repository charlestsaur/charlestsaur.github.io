# LLM Usage And Some Principles

本文档偏向在使用的途中了解 LLM 的部分原理

使用的 LLM 推理框架为: MLX

MLX ([https://github.com/ml-explore/mlx](https://github.com/ml-explore/mlx)) 是一个用于在 Apple Silicon 的 Mac 上高效率推理 AI 模型的开源项目, 由 Apple 官方团队维护

llama.cpp ([https://github.com/ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)) 也是一个开源 AI 推理框架, 最开始它是做 CPU 推理的, 后来 GPU 的支持也不错, 而且支持的硬件也较广泛

但在 M 系列芯片的 Mac 上, MLX 的推理效率高于 llama.cpp, 所以本文档主要使用 MLX

MLX 在 Hugging Face 上的仓库: [https://huggingface.co/mlx-community](https://huggingface.co/mlx-community) (主要是 MLX 官方做的一些开源 LLM 的 MLX 版本, 含量化, 以及一些供微调的文本)

(本文档使用的工具版本: mlx 0.29.2, mlx-lm 0.28.2, macOS 26.0.1)

## MLX 生态简介

[https://github.com/ml-explore](https://github.com/ml-explore)

1. MLX

   核心库 (MLX Framework 本体) Apple 推出的深度学习框架, 类似于 PyTorch 或 JAX, 但专为 Apple Silicon 优化, 是整个生态的底层基础

   提供底层的张量计算, 自动求导, GPU 加速, 设备管理等功能

   核心内容:

   * mlx.core: 张量, 设备, 计算图

   * mlx.nn: 神经网络模块

   * mlx.optimizers: 优化器

   * mlx.training: 训练循环等

2. mlx-lm

   大语言模型 (LLM) 推理与微调框架

   基于 mlx 实现的高层封装, 专门用于部署, 量化, 微调各种大语言模型

3. mlx-examples

   Apple 官方示例仓库, 展示如何用 mlx 做各种任务

   包含内容:

   图像分类, Transformer, Diffusion 模型

   自定义模型训练, 梯度下降演示

   一些用 mlx-lm 的语言模型示例

```md
┌──────────────────────┐
│     mlx-examples     │  ← 教学/示例代码
└──────────┬───────────┘
           ↓ 依赖使用
┌──────────┴───────────┐
│       mlx-lm         │  ← 高层 LLM 推理封装
└──────────┬───────────┘
           ↓ 依赖底层
┌──────────┴───────────┐
│         mlx          │  ← 核心推理框架
└──────────────────────┘
```
