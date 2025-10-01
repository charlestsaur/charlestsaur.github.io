# Open-LLM-VTuber deploy

Open-LLM-VTuber ([https://github.com/Open-LLM-VTuber/Open-LLM-VTuber](https://github.com/Open-LLM-VTuber/Open-LLM-VTuber)) 项目的官方介绍为 :

```plaintext
Open-LLM-VTuber 是一款独特的语音交互 AI 伴侣，它不仅支持实时语音对话和视觉感知，还配备了生动的 Live2D 形象。所有功能都可以在你的电脑上完全离线运行！
```

我个人认为他不只是可以用来做 AI 伴侣

这是其官方的简易部署文档 [https://docs.llmvtuber.com/docs/quick-start/](https://docs.llmvtuber.com/docs/quick-start/)

如果你愿意你也可以看本篇文档来完成简易部署

以下是其官方的提醒 (格式有改动, 连接并未改动) :

* 注意

  本项目只推荐使用 Chrome 浏览器。已知 Edge、Safari 等浏览器都存在不同的问题。

* 关于代理

  如果你位于中国大陆，建议你开启代理后再部署和使用本项目，确保能顺利下载所有资源。

  如果你遇到开启代理后本地服务 (ollama、deeplx、gptsovits) 无法访问，但关闭代理后就能访问的问题。请你确保你的代理绕过本地地址 (localhost)，或者在所有资源下载完毕后关闭代理再运行本项目。更多信息参考 [设置代理绕过](https://www.clashverge.dev/guide/bypass.html) 。

  Groq Whisper API、OpenAI API 等国外大模型/推理平台 API 一般无法使用香港地区的代理。

* 关于桌面应用

  如果你更喜欢 Electron 应用 (窗口模式 + 桌宠模式)，可以从 [Open-LLM-VTuber-Web Releases](https://github.com/Open-LLM-VTuber/Open-LLM-VTuber-Web/releases) 下载对应平台 Electron 客户端，可以在后端服务运行的前提下直接使用。但你有可能会遇到因为没有签名验证而导致的**安全警告**，具体情况和解决方案请查看 [模式介绍](https://docs.llmvtuber.com/docs/user-guide/frontend/mode/)

  有关前端的更多信息，请参考 [前端指南](https://docs.llmvtuber.com/docs/user-guide/frontend/)

现在确保你有 git, ffmpeg, uv

## 1. 获取项目代码

从项目的 Release 页面, 下载长得像 Open-LLM-VTuber-v1.x.x.zip 的 zip 文件, 并将解压后将文件放置在一个**路径不包含中文的合适位置**

## 2. 安装项目依赖

创建环境并安装依赖 :

```zsh
# 确保你在项目根目录下运行这个命令
uv sync
# 这个命令将创建一个 `.venv` 虚拟环境
```

接着, 我们运行一下主程序来生成预设的配置文件

```zsh
uv run run_server.py
```

然后按下 Ctrl + C 退出程序

官方提醒 :

v1.1.0 版本开始，`conf.yaml` 文件可能不会自动出现在项目目录下。请复制 `config_templates` 目录下的 `conf.default.yaml` 或 `conf.ZH.default.yaml` 文件到项目根目录并重命名为 `conf.yaml`。

或者，你也可以通过运行主程序 `uv run run_server.py` 并使用 `Ctrl + C` 退出程序来生成配置文件（不推荐使用这个方法）。请注意，退出操作需要及时执行，否则程序会开始下载模型文件（此刻退出可能会导致下次无法启动，解决方案为删除 `models/` 下的全部文件）。

## 3. 配置 LLM

我们以 Ollama 为例进行配置, 其他选项请参考[LLM 配置指南](https://docs.llmvtuber.com/docs/user-guide/backend/llm)

官方提醒 :

如果你不想使用 Ollama / 在 Ollama 的配置上遇到了难以解决的问题，本项目也支持：

OpenAI 兼容 API

OpenAI 官方 API

Claude

Gemini

Mistral

智谱

DeepSeek

LM Studio（类似 Ollama，使用更简单）

vLLM（性能更好，配置较复杂）

llama.cpp（直接运行 .gguf 格式模型）

以及更多 (大部分的 LLM 推理后端和API都支持 OpenAI 格式，可以直接接入本项目)

更多信息请参考[LLM 配置指南](https://docs.llmvtuber.com/docs/user-guide/backend/llm)。

确保你有 ollama

拉取一个模型 (以 `gemma3:12B` 为例) :

```zsh
ollama pull gemma3:12B
```

查看已安装的模型 :

```zsh
ollama ls
```

### 修改配置文件

官方提示 :

如果你的项目目录下没有 `conf.yaml` 文件，请运行一次项目主程序 `uv run run_server.py`，生成配置文件，然后退出。

编辑 `conf.yaml` :

1. 将 `basic_memory_agent` 下的 `llm_provider` 设置为 `ollama_llm`

2. 调整 `llm_configs` 选项下的 `ollama_llm` 下的设置:

* `base_url` 本地运行保持默认即可，无需修改。

* 设置 `model` 为你使用的模型，比如本文档使用的 `gemma3:12B`

  ```yaml
  ollama_llm:
    base_url: http://localhost:11434  # 本地运行保持默认
    model: qwen2.5:latest            # ollama list 得到的模型名称
    temperature: 0.7                 # 控制回答随机性，越高越随机 (0~1)
  ```

关于配置文件的详细说明，可以参考 [用户指南/配置文件](https://docs.llmvtuber.com/docs/user-guide/backend/config/)。

# 4. 配置其他模块

本项目 `conf.yaml` 默认配置中使用了 sherpa-onnx-asr (SenseVoiceSmall) 和 edgeTTS，并默认关闭翻译功能，你可以不用进行修改。

或者你可以参考 [ASR 配置指南](https://docs.llmvtuber.com/docs/user-guide/backend/asr/)、[TTS 配置指南](https://docs.llmvtuber.com/docs/user-guide/backend/tts/) 和 [Translator 配置指南](https://docs.llmvtuber.com/docs/user-guide/backend/translate/) 进行修改。

## 5. 启动项目

运行后端服务 :

```zsh
uv run run_server.py
# 第一次运行可能会下载一些模型，导致等待时间较久。
```
运行成功后，浏览器访问 `http://localhost:12393` 即可打开 Web 界面。

官方提示 :

关于桌面应用

如果你更喜欢 Electron 应用 (窗口模式 + 桌宠模式)，可以从 [Open-LLM-VTuber-Web Releases](https://github.com/Open-LLM-VTuber/Open-LLM-VTuber-Web/releases) 下载对应平台 Electron 客户端，可以在后端服务运行的前提下直接使用。但你有可能会遇到因为没有签名验证而导致的**安全警告**，具体情况和解决方案请查看 [模式介绍](https://docs.llmvtuber.com/docs/user-guide/frontend/mode/)

有关前端的更多信息，请参考 [前端使用指南](https://docs.llmvtuber.com/docs/user-guide/frontend/)