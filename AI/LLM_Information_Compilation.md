# AI Information Compilation

[https://huggingface.co](https://huggingface.co) has a wealth of LLM resources (models, datasets, etc.)

## Training Frameworks

### PyTorch

Currently the most widely used AI training framework

Links: [https://pytorch.org](https://pytorch.org), [https://docs.pytorch.org/docs/stable/index.html](https://docs.pytorch.org/docs/stable/index.html), [https://github.com/pytorch/pytorch](https://github.com/pytorch/pytorch)

### JAX

Google's high-performance AI training framework, specifically designed for TPUs.

Links: [https://docs.jax.dev/en/latest/installation.html](https://docs.jax.dev/en/latest/installation.html), [https://docs.jax.dev/en/latest](https://docs.jax.dev/en/latest), [https://github.com/jax-ml/jax](https://github.com/jax-ml/jax)

### MLX

Apple's AI based on JAX, specifically designed for Apple silicon. Training Framework.

Links: [https://opensource.apple.com/projects/mlx](https://opensource.apple.com/projects/mlx), [https://ml-explore.github.io/mlx/build/html/index.html](https://ml-explore.github.io/mlx/build/html/index.html), [https://github.com/ml-explore/mlx](https://github.com/ml-explore/mlx)

## Projects Focused on Inference

* llama.cpp [https://github.com/ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)

## Model Vendors Providing Web Access

* GPT [https://chatgpt.com](https://chatgpt.com)

* Gork [https://grok.com](https://grok.com)

  * Or on X (Twitter) [https://x.com/i/grok](https://x.com/i/grok)

* Gemini [https://gemini.google.com/app](https://gemini.google.com/app)

  * Or on Google AI Studio [https://aistudio.google.com](https://aistudio.google.com)

* Mistral [https://chat.mistral.ai/chat](https://chat.mistral.ai/chat)

* Claude [https://claude.ai](https://claude.ai/)

## Open Source Models

Personally, I think some of the better open source LLM models are (I'm not sure about the last two):

### Llama (Meta)

There are two types of models: pure text model or multimodal (only supports: text, images →) (Text)

You can see the [brief information](Llama_Models.md) I specifically collected for Llama 3 ~ 4.

Links🔗: [https://www.llama.com](https://www.llama.com), [https://huggingface.co/meta-llama](https://huggingface.co/meta-llama)

### Gemma (Google)

Primarily multimodal (supports only: text, image → text). In the Gemma3 series, except for version 1B which is plain text, 4B, E2B, E4B, 12B, and 27B are all multimodal (supports only: text, image → text).

Links🔗: [https://deepmind.google/models/gemma](https://deepmind.google/models/gemma), [https://ai.google.dev/gemma/docs](https://ai.google.dev/gemma/docs), [https://huggingface.co/google](https://huggingface.co/google)

### Mistral (Mistral)

It's quite unique, with a broad range of open-source models, including: plain text models, multimodal models (including speech), and models specifically optimized for encoding...

It also has many names, including: Mistral, Magistral, Devstral, Mixtral, Ministral, Pixtral, Voxtral, Mamba, Codestral

Links🔗: [https://docs.mistral.ai/getting-started/models](https://docs.mistral.ai/getting-started/models), [https://huggingface.co/mistralai](https://huggingface.co/mistralai)

### GPT-OSS (Open AI)

It only has two specifications: 20B and 120B, both of which are MoE Pure Text Model

Links🔗: [https://openai.com/index/introducing-gpt-oss](https://openai.com/index/introducing-gpt-oss), [https://github.com/openai/gpt-oss](https://github.com/openai/gpt-oss), [https://gpt-oss.com](https://gpt-oss.com/), [https://huggingface.co/openai](https://huggingface.co/openai)

### Phi (Microsoft)

The models are also good, all small models (maximum 14B).

Links🔗: [https://azure.microsoft.com/en-us/products/phi](https://azure.microsoft.com/en-us/products/phi), [https://github.com/microsoft/PhiCookBook](https://github.com/microsoft/PhiCookBook), [https://huggingface.co/microsoft](https://huggingface.co/microsoft)

### Granite (IBM)

The model is also good, mainly focusing on miniaturization.

Links🔗: [https://www.ibm.com/granite](https://www.ibm.com/granite), [https://github.com/ibm-granite](https://github.com/ibm-granite), [https://huggingface.co/ibm-granite](https://huggingface.co/ibm-granite)

### Stable LM (Stability AI)

Um, I don't know the specifics (I don't even know the different models). The latest model is from May 20, 2024, which is quite old.

### Nemotron (NVIDIA)

I don't know the specifics either (I don't even know the model). The latest should be V2 (its Hugging Face model card says June 2025 ~ August 2025).

## Simple Local Model Inference Software

Out-of-the-box software:

* ollama (open-source CLI software, but extremely simple to use, can only infer GGUF) [https://github.com/ollama/ollama](https://github.com/ollama/ollama)

* Anything LLM (open-source graphical software, not only for communicating with local models, but also for building RAGs, knowledge bases, MCPs... It can only infer itself) GGUF (but can connect to external models) [https://github.com/Mintplex-Labs/anything-llm](https://github.com/Mintplex-Labs/anything-llm)

* LM Studio (closed-source, free graphical software for interacting with local models; it has a good UI and local model management system, but deleting a model leaves an empty folder; supports GGUF and MLX models) [https://lmstudio.ai](https://lmstudio.ai)

## API

Most people's local computing power is probably not yet sufficient to run models with hundreds of billions of parameters smoothly, so the usefulness of APIs becomes apparent when experiencing many projects that require LLM model inference services.

### Gemini

Gemini's API is the most generous, offering one million free tokens per day and supporting all Google models (over 60 types), including:

* Audio, image, video, text, PDF → Text

* Text → Audio

* Text → Image

* Audio, Video, Text → Audio, Text

* Text, Image, Video → Video with Audio

* Image, Text → Image, Text

* Music Generation (Cannot directly interact with this model)

* Text Embedding Model (Cannot directly interact with this model)

The Gemini 2.5 series models use an internal "thinking process" that significantly improves reasoning and multi-step planning capabilities, making them ideal for complex tasks such as coding, advanced mathematics, and data analysis. (← Original quote from Google Docs)

Both Gemini 2.0 and 2.5 support structured output, but "Gemini 2.0 requires explicitly specifying the propertyOrdering list in the JSON input to define the preferred structure. (← Original quote from Google Docs)"

Many Gemini models come with a huge context window with 1 million or more lexical units. (← Original quote from Google Docs)

Another model is the Live model, which enables low-latency voice and video interaction.

Official documentation: [https://ai.google.dev/gemini-api/docs](https://ai.google.dev/gemini-api/docs)

### Mistral

There is a free API, but the free quota is limited. Mistral models have good inference capabilities, giving them an advantage in mathematics and coding.

### Cloudflare

Cloudflare's Workers AI API is also good, with a decent free quota, second only to Google among the major companies' APIs I know of. However, since Cloudflare doesn't have its own models, it provides open-source models from other vendors. It does offer Llama4.

## Related Projects

* Open WebUI [https://github.com/open-webui/open-webui](https://github.com/open-webui/open-webui) Provides a fully functional UI for local models or APIs.

* Open-LLM-VTuber [https://github.com/Open-LLM-VTuber/Open-LLM-VTuber](https://github.com/Open-LLM-VTuber/Open-LLM-VTuber) This is an application-level software that I personally think is very good.

* n8n [https://github.com/n8n-io/n8n](https://github.com/n8n-io/n8n) A workflow automation platform.

## Unrestricted Models

Unrestricted models (uncensored models) will answer any question you ask, including those related to horror, pornography, gambling, etc. Or issues that are clearly unethical in nature.

Large companies train open-source models with limitations (this behavior is not problematic; on the contrary, they do it very well, it's just that some companies' limitations are too strict). Many questions the model cannot answer, but some experts, through fine-tuning, retraining, and opposing homogenization, have transformed the originally limited model into an unrestricted model.

Those with resources or companies will not train unrestricted models themselves and profit from it, because this could lead to legal trouble (and ultimately loss of freedom 😭).

You can find such models at [https://huggingface.co](https://huggingface.co), or fine-tune them yourself using datasets that break limitations.

You can use and research unrestricted models yourself, but please do not participate in sharing unrestricted models, datasets that break limitations, unrestricted model outputs, etc., and absolutely do not provide unrestricted model inference services to the public internet.

## Quick FLOPS

### Performance Measurement

* Effective FLOPS

* Sustained FLOPS

* Achieved FLOPS FLOPS

Using FLOPS to measure a computer's performance when running AI locally is not comprehensive, as it only reflects the performance of your GPU itself. In practice, bandwidth, caching (memory, VRAM), and execution strategies must also be considered.

Therefore, to determine the specific parameters your computer can run in an LLM, refer to actual benchmarks and then conduct your own testing.

My personal recommendation is to ensure the quantization is no less than Int 4. Int 4 is suitable, balancing quality and speed. If performance allows, FP8 is a better choice.

### Model Types

In most LLMs, models fall into two main categories:

* Pretrained (also called Base model): Performs self-supervised learning (predicting the next token) only on large-scale unlabeled text data.

  * Goal: Learning general language understanding, knowledge, and world models.

  * Cannot directly chat; output is often a continuation of text, lacking alignment.

* Instruction (also called Chat/Instruction-tuned model): Based on the pretrained model, uses... Supervised fine-tuning (SFT) + rejection sampling (RS) + preference optimization (DPO/RLHF) on instruction datasets (such as human dialogue and question-answering).

  * Goal: To make the model responsive, user-friendly, safe, and helpful.

  * You can chat directly.

## Specific Model Recommendations

Personal Recommended Models:

The names in the following list are linked to their official repositories, accessible via `https://huggingface.co/name`. The names I've listed are not the original models, but rather versions that have been aligned and fine-tuned (I think 😀).

There are simply too many variant versions to collect, and I can't guarantee their security, so I won't list them here (actually, I'm just lazy 😝).

For MLX versions, please visit the repository [https://huggingface.co/mlx-community](https://huggingface.co/mlx-community). This is a repository focused on converting various open-source models to MLX format and providing quantization (as of November 12, 2025, this repository has 3185 models 😱).

You can also see the [brief information](Llama_Models.md) I specifically collected for Llama 3 ~ 4.

### 1 ~ 10 B

There are a lot of models in this range. It seems manufacturers really want to make models smaller, and these small models are even multimodal, including audio multimodal models.

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

  * microsoft/Phi-4-mini-reasoning (4B)

  * microsoft/Phi-4-mini-instruct (4B)

  * microsoft/Phi-4-multimodal-instruct (6B)

  * microsoft/Phi-3-small-128k-instruct (7B)

* mistral

  * mistralai/Voxtral-Mini-3B-2507 (but Hugging Face shows 5B)

  * mistralai/Mistral-7B-Instruct-v0.3

  * mistralai/Mamba-Codestral-7B-v0.1

  * mistralai/Mathstral-7B-v0.1

  * mistralai/Ministral-8B-Instruct-2410

* granite4

  * ibm-granite/granite-4.0-h-1b (1.5B)

  * ibm-granite/granite-4.0-1b (1.6B)

  * ibm-granite/granite-4.0-micro (3B)

  * ibm-granite/granite-4.0-tiny-preview (7B)

### 10 ~ 20 B

(Excluding 20B)

Most are below 10B, but 10B ~ 20B is a bit awkward, not many. The ones I recommend are considered top-tier in this range (probably 😁)

* llama3

  * meta-llama/Llama-3.2-11B-Vision-Instruct

* gemma3

  * gemma-3-12b-it-qat

* mistral

  * mistralai/Pixtral-12B-2409

  * mistralai/Mistral-Nemo-Instruct-2407 (12B)

* phi4

  * microsoft/phi-4 (15B)

  * microsoft/Phi-4-reasoning-plus (15B)

### 20 ~ 30 B

(Inclusive of 20B)

I personally think the models in this range are already quite capable, but there don't seem to be many models in this range.

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

The capabilities of models in this range are already quite good.

* llama3

  * meta-llama/Llama-3.3-70B-Instruct

  * meta-llama/Llama-3.2-90B-Vision-Instruct

* mistral

  * mistralai/Mixtral-8x7B-Instruct-v0.1

* granite

  * ibm-granite/granite-4.0-h-small (32B)

### 100B ~ 200B

The capabilities of models in this range are, of course, even better.

* gpt

  * openai/gpt-oss-120b

* llama

  * meta-llama/Llama-4-Scout-17B-16E-Instruct

* mistral

  * mistralai/Mixtral-8x22B-Instruct-v0.1

  * mistralai/Mistral-Large-Instruct-2411

  * mistralai/Pixtral-Large-Instruct-2411

### 200B +

There is only one open-source model in this range: llama (there is also Nemotron, but I am not familiar with this model)

* llama

  * meta-llama/Llama-4-Maverick-17B-128E-Instruct

  * meta-llama/Llama-3.1-405B-Instruct

### Additional Notes

* In pure image understanding, Llama-3.2-90B-Vision-Instruct should outperform Llama-4-Scout-17B-16E-Instruct and Llama-4-Maverick-17B-128E-Instruct

And Llama-3.2-90B-Vision-Instruct is more stable.

* In complex reasoning, mathematical, and coding problems, Llama-3.1-405B-Instruct should outperform Llama-4-Maverick-17B-128E-Instruct.

Furthermore, Llama-3.1-405B-Instruct is more stable.

This stems from the fundamental difference between MoE and Dense. Although Llama-4-Scout has 109 bytes and Llama-4-Maverick has 402 bytes, they only have 17 bytes of active parameters. This is significantly different from Llama-3.2-90B and Llama-3.1-405B, which use full parameters every time. Moreover, MoE is inherently less powerful than Dense in image and complex logic problems.

The routing mechanism in MoE makes it more complex and prone to errors, but MoE consumes fewer resources.

Meta refers to Llama4. The use of "early fusion technology" to achieve multimodal processing might improve the situation.

In short, with the same total parameters but without considering computational resources, Dense should outperform MoE.

However, these things are too subjective. Even with various evaluations, it's better to try it yourself than to just listen to what others say.
