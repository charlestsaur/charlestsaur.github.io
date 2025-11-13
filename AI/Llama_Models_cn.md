# Llama Models

本文档没有提及 Llama Guard 3（内容安全分类模型）、Prompt Guard（提示注入检测模型）若需要这两个安全辅助模型的信息，请自行查找

## Llama 3 INFO

Llama 3 全系为稠密模型（Dense）

## Llama 3

2024 年 4 月初始发布，上下文长度 8K tokens，训练数据为 15T tokens（公开数据+合成数据）

* Llama-3-8B

* Llama-3-70B

## Llama 3.1

2024 年 7 月发布，上下文长度 128K tokens（用 RoPE 扩展），训练数据超过 30T tokens（含更多多语言数据）

* Llama-3.1-8B

* Llama-3.1-70B

* Llama-3.1-405B

## Llama 3.2

2024年9月25日 & 10月发布，上下文长度 128K tokens，多模态支持单张图像最大分辨率：1120×1120 像素

纯文本模型（1B / 3B）的数据集为最高 9 万亿 tokens（公开来源）

视觉模型（11B / 90B）60 亿个图像-文本对（6B image-text pairs），先在大规模噪声数据上预训练，再在中规模高质量数据上精炼

1B / 3B 模型在预训练阶段使用了知识蒸馏（从 Llama-3.1-70B 借 logit）

11B / 90B 使用了后训练（post-training）：使用 Llama 3.1 生成的 合成数据 + 超过 300 万个视觉指令示例

* Llama-3.2-1B

* Llama-3.2-3B

* Llama-3.2-11B-Vision

* Llama-3.2-90B-Vision

## Llama 3.3

2024年12月6日发布，上下文长度 128K tokens（使用 RoPE 缩放等技术），训练数据为 15T tokens（公开网络数据新混合）

他不是全新从零训练的模型，而是基于 Llama-3.1-70B 的高级后训练（post-training）优化版，通过更先进的对齐技术（如拒绝微调、指令跟随强化），实现了接近 Llama 3.1 405B 的性能，但推理成本仅约为其 1/6

* Llama-3.3-70B

## Llama4 INFO

Llama 4 全部转向 MoE（Mixture-of-Experts，稀疏模型）+ 原生多模态（图像、视频早融合），不再是稠密模型（Dense）

Scout 和 Maverick 由 Behemoth 蒸馏而来

## Llama 4 Scout

官方名称：meta-llama/Llama-4-Scout-17B-16E

参数量：总参数 ≈ 109B，活跃参数 17B，16 个专家

上下文长度：10M tokens（使用 Ring Attention + Infini-Attention 实现）

多模态：支持图像输入（和 Llama 3.2 Vision 不同，是训练时就融合的）

## Llama 4 Maverick

官方名称：meta-llama/Llama-4-Maverick-17B-128E

参数量：总参数 ≈ 400B，活跃参数 17B，128 个专家

上下文长度：1M tokens

多模态：图像+短视频输入（最长 1 分钟 720p）

## Llama 4 Behemoth

尚未发布，仍在训练中（2025,11）

参数量：激活 288B，16 个专家，总参数 ≈ 2T

作为 Scout/Maverick 的“教师”模型
