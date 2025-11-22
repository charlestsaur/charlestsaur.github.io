# AI Information Compilation

[https://huggingface.co](https://huggingface.co) 上有着十分多的 LLM 资源（模型、数据集...）

## 训练框架

### PyTorch

目前使用最广的 AI 训练框架

链接🔗：[https://pytorch.org](https://pytorch.org)、[https://docs.pytorch.org/docs/stable/index.html](https://docs.pytorch.org/docs/stable/index.html)、[https://github.com/pytorch/pytorch](https://github.com/pytorch/pytorch)

### JAX

Google 开发的高性能 AI 训练框架，专用于 TPU

链接🔗：[https://docs.jax.dev/en/latest/installation.html](https://docs.jax.dev/en/latest/installation.html)、[https://docs.jax.dev/en/latest](https://docs.jax.dev/en/latest)、[https://github.com/jax-ml/jax](https://github.com/jax-ml/jax)

### MLX

Apple 基于 JAX 开发的专用于 Apple silicon 的 AI 训练框架

链接🔗：[https://opensource.apple.com/projects/mlx](https://opensource.apple.com/projects/mlx)、[https://ml-explore.github.io/mlx/build/html/index.html](https://ml-explore.github.io/mlx/build/html/index.html)、[https://github.com/ml-explore/mlx](https://github.com/ml-explore/mlx)

## 专注推理的项目

* llama.cpp [https://github.com/ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)

## 提供网页使用的模型厂商

* GPT [https://chatgpt.com](https://chatgpt.com)

* Gork [https://grok.com](https://grok.com)

  * 或在 X（Twitter）上 [https://x.com/i/grok](https://x.com/i/grok)

* Gemini [https://gemini.google.com/app](https://gemini.google.com/app)

  * 或在 Google AI Studio 上 [https://aistudio.google.com](https://aistudio.google.com)

* Mistral [https://chat.mistral.ai/chat](https://chat.mistral.ai/chat)

* Claude [https://claude.ai](https://claude.ai/)

## 开源模型

个人认为比较不错的开源 LLM 模型（最后两个不清楚）：

### Llama（Meta）

有两种模型：纯文本模型或多模态（只支持：文字、图片 → 文字）

可以看我专门收集的 Llama3 ~ 4 的[简要信息](Llama_Models_cn.md)

链接🔗：[https://www.llama.com](https://www.llama.com)、[https://huggingface.co/meta-llama](https://huggingface.co/meta-llama)

### Gemma（Google）

主要是多模态（只支持：文字、图片 → 文字）。Gemma3 系列中除 1B 版本是纯文本之外，4B、E2B、E4B、12B、27B 都是多模态（只支持：文字、图片 → 文字）

链接🔗：[https://deepmind.google/models/gemma](https://deepmind.google/models/gemma)、[https://ai.google.dev/gemma/docs](https://ai.google.dev/gemma/docs)、[https://huggingface.co/google](https://huggingface.co/google)

### Mistral（Mistral）

比较特殊，它的开源模型涵盖之广，包括：纯文本模型、多模态模型（包含语音）、针对编码的特别优化模型...

名字也多，包括：Mistral、Magistral、Devstral、Mixtral、Ministral、Pixtral、Voxtral、Mamba、Codestral

链接🔗：[https://docs.mistral.ai/getting-started/models](https://docs.mistral.ai/getting-started/models)、[https://huggingface.co/mistralai](https://huggingface.co/mistralai)

### GPT-oss（Open AI）

只有两个规格：20B、120B 并都是 MoE 纯文本模型

链接🔗：[https://openai.com/index/introducing-gpt-oss](https://openai.com/index/introducing-gpt-oss)、[https://github.com/openai/gpt-oss](https://github.com/openai/gpt-oss)、[https://gpt-oss.com](https://gpt-oss.com/)、[https://huggingface.co/openai](https://huggingface.co/openai)

### Phi（Microsoft）

模型也不错，都是小模型（最大 14B）

链接🔗：[https://azure.microsoft.com/en-us/products/phi](https://azure.microsoft.com/en-us/products/phi)、[https://github.com/microsoft/PhiCookBook](https://github.com/microsoft/PhiCookBook)、[https://huggingface.co/microsoft](https://huggingface.co/microsoft)

### Granite（IBM）

模型也不错，主要偏小型化

链接🔗：[https://www.ibm.com/granite](https://www.ibm.com/granite)、[https://github.com/ibm-granite](https://github.com/ibm-granite)、[https://huggingface.co/ibm-granite](https://huggingface.co/ibm-granite)

### Stable LM（Stability AI）

额，我不知道具体怎么样（我连各种型号都不知道），最新的模型是 2024,5,20 的了，也挺老了

### Nemotron（NVIDIA）

我也不清楚具体内容（也是连型号都不清楚），现在应该最新是 V2 (在其 Hugging Face 的模型卡上写着 2025,6 ~ 2025,8)

## 简易本地模型推理软件

开箱即用的软件：

* ollama（开源 CLI 软件，不过操作极其简易，只能推理 GGUF）[https://github.com/ollama/ollama](https://github.com/ollama/ollama)

* Anything LLM（开源图形化软件，不止用于与本地模型交流，也可以搭建 RAG、知识库、MCP... 本身只能推理 GGUF，不过可以连接外部模型）[https://github.com/Mintplex-Labs/anything-llm](https://github.com/Mintplex-Labs/anything-llm)

* LM Studio（闭源免费图形化软件，用于与本地模型交互，拥有不错的 UI 和本地模型管理系统，但删除模型时会留下空文件夹，支持 GGUF 和 MLX 模型）[https://lmstudio.ai](https://lmstudio.ai)

## API

相信大家的本地算力应该还没有达到能使上千亿参数模型流畅运行的水平，所以在体验很多需要 LLM 模型推理服务的项目时 API 的用处就体现出来了

### Gemini

Gemini 的 API 最良心，一天免费一百万 token，并能使用 Google 全模型（超过 60 种）,类型包含：

* 音频、图片、视频、文本、PDF → 文本

* 文本 → 音频

* 文本 → 图片

* 音频、视频、文字 → 音频、文字

* 文字、图片、视频 → 带音频的视频

* 图片、文字 → 图片、文字

* 音乐生成（无法与此模型直接对话）

* 文本嵌入模型（无法与此模型直接对话）

Gemini 2.5 系列模型使用内部“思考过程”，可显著提升推理和多步规划能力，因此非常适合处理编码、高等数学和数据分析等复杂任务。（← Google 文档原话）

Gemini 2.0 和 2.5 都支持结构化输出，但 “Gemini 2.0 需要在 JSON 输入中明确指定 propertyOrdering 列表，以定义首选结构。（← Google 文档原话）”

许多 Gemini 模型都配备了 100 万个或更多词元的超大上下文窗口。（← Google 文档原话）

还有一种模型是 Live 模型，他可以进行低延迟的语音和视频互动

官方文档：[https://ai.google.dev/gemini-api/docs](https://ai.google.dev/gemini-api/docs)

### Mistral

有免费 API，只是免费额度不多。Mistral 系列的模型的推理能力不错，所以在数学、编码上较有优势

### cloudflare

cloudflare 的 Workers AI API 也行，免费额度也还不错，在我知道的大厂 API 中仅次于 Google，但由于 cloudflare 没有自己的模型，所以它提供的都是其他厂商的开源模型，不过它提供 Llama4

## 相关项目

* Open WebUI [https://github.com/open-webui/open-webui](https://github.com/open-webui/open-webui) 为本地模型或 API 提供一个功能较齐全 UI 界面

* Open-LLM-VTuber [https://github.com/Open-LLM-VTuber/Open-LLM-VTuber](https://github.com/Open-LLM-VTuber/Open-LLM-VTuber) 这是一个个人认为很不错的应用层级软件

* n8n [https://github.com/n8n-io/n8n](https://github.com/n8n-io/n8n) 是一个工作流自动化平台

## 无限制模型

未经审查模型（无限制模型），即会回答你的任何问题，包括恐怖、色情、赌博... 或主观上明显违反道德的问题

大厂训练的开源模型都有限制（这种行为并无任何问题，相反他们做的很好，只是有些厂商的限制过于严格了），很多问题模型不会回答，但有一些大佬经过对模型微调、后训练、反对齐... 后使原本带有限制的模型变成了无限制模型

那些有资源的人或公司也不会自己训练无限制模型并以此获利，因为这样有可能会使其吃官司（最后失去自由😭）

在 [https://huggingface.co](https://huggingface.co) 上你可以找到这类模型，或自己使用突破限制数据集微调

无限制模型你可以自己使用、研究，但请不要参与分享无限制模型、突破限制数据集、无限制模型输出等，以及一定不要向公网提供无限制模型推理服务

## 小知识

### 性能衡量

* Effective FLOPS（有效浮点运算能力）

* Sustained FLOPS（持续浮点运算能力）

* Achieved FLOPS（实际达到的 FLOPS）

本地跑 AI 中使用 FLOPS 来衡量一台电脑的性能不太全面，因为这只是你的 GPU 本身的性能，而实际中还要考虑带宽、缓存（内存、显存）、运行策略...

所以你的电脑具体能跑多大参数的的 LLM，可以看实际的测评，然后再自己测试

我个人的建议是量化不要小于 Int 4，Int 4 比较合适，兼顾质量和速度，如果性能允许的话 FP8 是更好的选择

### 模型类型

在大多数 LLM 中，模型分为两大类：

* Pretrained（预训练模型，也叫 Base 模型）：只在大规模无标签文本数据上进行 自监督学习（预测下一个 token）

  * 目标：学习通用语言理解、知识、世界模型

  * 不能直接聊天，输出往往是续写文本，缺乏对齐（alignment）

* Instruct（指令微调模型，也叫 Chat/Instruction-tuned）：在预训练模型基础上，用 指令数据集（如人类对话、问答）进行监督微调（SFT）+ 拒绝采样（RS）+ 偏好优化（DPO/RLHF）

  * 目标：让模型听指令、友好、安全、helpful。

  * 可以直接聊天

## 具体模型推荐

个人推荐模型：

以下列表中的名字为其官方仓库的链接，使用 `https://huggingface.co/name` 即可访问，我列出的名字不是原始模型，都是已对其、已指令微调的版本（应该是😀）

各种变体版本实在是收集不过来，而且我也不能保证他们的安全性，所以这里我就不列了（其实也是我懒😝）

MLX 版本请进入 [https://huggingface.co/mlx-community](https://huggingface.co/mlx-community) 仓库查找，这是一个专注于将各种开源模型转换 MLX 格式并提供量化的仓库（截止 2025,11,12 该仓库已有 3185 个模型了😱）

另外可以看我专门收集的 Llama3 ~ 4 的[简要信息](Llama_Models_cn.md)

### 1 ~ 10 B

这个区间的模型真的很多，看来厂家们真的很希望模型变小，而且这么小的模型竟然还有多模态的，而且还有音频多模态的

* llama3

  * meta-llama/Llama-3.2-1B-Instruct

  * meta-llama/Llama-3.2-3B-Instruct

  * meta-llama/Llama-3.1-8B-Instruct

* gemma3

  * google/gemma-3-1b-it-qat-q4_0-gguf

  * google/gemma-3-4b-it-qat-q4_0-gguf

  * google/gemma-3n-E2B-it

  * google/gemma-3n-E4B-it

* phi

  * microsoft/Phi-3.5-mini-instruct (4B)

  * microsoft/Phi-4-mini-reasoning（4B）

  * microsoft/Phi-4-mini-instruct（4B）

  * microsoft/Phi-4-multimodal-instruct（6B）

  * microsoft/Phi-3-small-128k-instruct（7B）

* mistral

  * mistralai/Voxtral-Mini-3B-2507（但 Hugging Face 显示是 5B）

  * mistralai/Mistral-7B-Instruct-v0.3

  * mistralai/Mamba-Codestral-7B-v0.1

  * mistralai/Mathstral-7B-v0.1

  * mistralai/Ministral-8B-Instruct-2410

* granite4

  * ibm-granite/granite-4.0-h-1b（1.5B）

  * ibm-granite/granite-4.0-1b（1.6B）

  * ibm-granite/granite-4.0-micro（3B）

  * ibm-granite/granite-4.0-tiny-preview（7B）

### 10 ~ 20 B

（不包含 20B）

10B 以下的多，10B ~ 20B 倒是尴尬了，没多少，我推荐的这几个也算是这个区间顶尖的了（应该是😁）

* llama3

  * meta-llama/Llama-3.2-11B-Vision-Instruct

* gemma3

  * gemma-3-12b-it-qat

* mistral

  * mistralai/Pixtral-12B-2409

  * mistralai/Mistral-Nemo-Instruct-2407（12B）

* phi4

  * microsoft/phi-4（15B）

  * microsoft/Phi-4-reasoning-plus（15B）

### 20 ~ 30 B

（包含 20B）

这个区间的模型我个人认为能力就已经不错了，但是感觉这个区间的模型也不多

* gpt

  * openai/gpt-oss-20b

* gemma3

  * google/gemma-3-27b-it-qat-q4_0-gguf

* mistral

  * mistralai/Magistral-Small-2509-GGUF

  * mistralai/Devstral-Small-2507_gguf

  * mistralai/Mistral-Small-3.2-24B-Instruct-2506

  * mistralai/Voxtral-Small-24B-2507

  * mistralai/Codestral-22B-v0.1

### 30B ~ 100B

这个区间的模型的能力已经很好了

* llama3

  * meta-llama/Llama-3.3-70B-Instruct

  * meta-llama/Llama-3.2-90B-Vision-Instruct

* mistral

  * mistralai/Mixtral-8x7B-Instruct-v0.1

* granite

  * ibm-granite/granite-4.0-h-small（32B）

### 100B ~ 200B

这个区间的模型的能力当然更好了

* gpt

  * openai/gpt-oss-120b

* llama

  * meta-llama/Llama-4-Scout-17B-16E-Instruct

* mistral

  * mistralai/Mixtral-8x22B-Instruct-v0.1

  * mistralai/Mistral-Large-Instruct-2411

  * mistralai/Pixtral-Large-Instruct-2411

### 200B +

这个区间的开源模型仅 llama 一个（其实还有 Nemotron，但我不了解这个模型）

* llama

  * meta-llama/Llama-4-Maverick-17B-128E-Instruct

  * meta-llama/Llama-3.1-405B-Instruct

### 补充说明

* 在存粹的图像理解中 Llama-3.2-90B-Vision-Instruct 的能力应该超过 Llama-4-Scout-17B-16E-Instruct 和 Llama-4-Maverick-17B-128E-Instruct

  并且 Llama-3.2-90B-Vision-Instruct 更稳定

* 在复杂推理、数学、代码等问题上，Llama-3.1-405B-Instruct 的能力应该超过 Llama-4-Maverick-17B-128E-Instruct

  并且 Llama-3.1-405B-Instruct 更稳定

这源于 MoE 和 Dense 的本质区别，虽然 Llama-4-Scout 有 109B，Llama-4-Maverick 有 402B，但他们都只有 17B 的活跃参数，这与每次都使用完整参数的 Llama-3.2-90B 和 Llama-3.1-405B 还是有很大差距，而且在图像和复杂逻辑问题上 MoE 天生就没有 Dense 强

MoE 中的路由机制使 MoE 更复杂，更容易出错，但 MoE 消耗的资源低

Meta 称 Llama4 使用了“早起融合技术”来实现多模态，所以可能情况有所好转

总之在相同总参数，但不考虑计算资源的情况下，Dense 的能力应该优于 MoE

不过这些东西都太过主观，即使有各种测评，但听别人这说那说，不如自己一试

<script src="https://giscus.app/client.js"
        data-repo="charlestsaur/charlestsaur.github.io"
        data-repo-id="R_kgDOKvWJJA"
        data-category="Announcements"
        data-category-id="DIC_kwDOKvWJJM4CyE4o"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="preferred_color_scheme"
        data-lang="zh-CN"
        crossorigin="anonymous"
        async>
</script>