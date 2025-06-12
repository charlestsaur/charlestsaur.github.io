# 直接部署 Stable Diffusion

(本文档改自 Grok 的写的文档)

我们用原始的 Stable Diffusion 仓库（stability-ai/stablediffusion 或 diffusers 库），只装核心依赖

## 1. 准备环境

需要 Python 和基本工具，但不需要 WebUI 的复杂依赖。

### 1.1. 确保有 Python 3.10：


```zsh
brew install python@3.10
python3.10 --version
```

### 1.2. 创建新文件夹：

```zsh
mkdir ~/stable-diffusion
cd ~/stable-diffusion
```

### 1.3. 创建新的虚拟环境，保持环境干净，只装必要的东西。

输入：

```zsh
python3.10 -m venv venv
source venv/bin/activate
```

看到 (venv) 表示成功激活。

## 2. 安装核心依赖

直接用 diffusers 库（Hugging Face 提供的官方接口），简单高效，支持 M2 的 MPS 加速。

### 2.1 安装 PyTorch（跳过 SSL 验证）：

```zsh
pip install torch torchvision --index-url https://download.pytorch.org/whl/nightly/cpu --trusted-host download.pytorch.org
```

### 2.2 安装 diffusers 和其他必要库：

```zsh
pip install diffusers transformers accelerate --trusted-host pypi.org --trusted-host files.pythonhosted.org
```

### 2.3 检查安装：

```zsh
python3.10 -c "import torch; print(torch.__version__); print(torch.backends.mps.is_available())"
```

看到版本号和 True，说明 PyTorch 和 MPS 就绪。

## 3. 下载模型

### 3.1 直接用 diffusers 加载模型，不需要手动下载 .ckpt 文件。
---

登录 Hugging Face（如果你没 token，先去 https://huggingface.co/settings/tokens 生成一个）：

```zsh
pip install huggingface_hub
python3.10 -c "from huggingface_hub import login; login()"
```

输入你的 token，按提示完成。

模型会自动在脚本运行时下载（后面会用到）。

### 3.2 你有 .ckpt 文件(比如之前下载的 v1-5-pruned-emaonly.ckpt)
---

v1-5-pruned-emaonly.ckpt 可以去 Hugging Face 找到 stable-diffusion-v1-5，下载 v1-5-pruned-emaonly.ckpt（约 4GB）。(需要有Hugging Face的账号(免费))

#### 3.2.1 确认 .ckpt 文件位置
---

###### 3.2.1.1 如果你之前备份了模型（比如 ~/stable-diffusion-models/v1-5-pruned-emaonly.ckpt），很好。

##### 3.2.1.2 如果不确定位置，在终端输入：

```zsh
find ~ -name "*.ckpt"
```

找到路径（比如 /Users/你的用户名/stable-diffusion-models/v1-5-pruned-emaonly.ckpt），记下来。

##### 3.2.1.3 为方便，把它移到当前工作目录：

```zsh
mv /path/to/your/ckpt ~/stable-diffusion/
```

（把 /path/to/your/ckpt 换成实际路径，比如 ~/stable-diffusion-models/v1-5-pruned-emaonly.ckpt）。


#### 3.2.2 修改依赖（支持 .ckpt）
---

diffusers 默认用 Hugging Face 格式的模型，加载 .ckpt 需要额外支持。

确保在虚拟环境里：

```zsh
cd ~/stable-diffusion
source venv/bin/activate
```

安装完整 diffusers（带 .ckpt 支持）：

```zsh
pip install diffusers[torch] --trusted-host pypi.org --trusted-host files.pythonhosted.org
```

## 4. 运行 Stable Diffusion

### 4.1 写一个简单脚本
---

用 Python 脚本调用 Stable Diffusion 生成图像，灵活且轻量。

在 ~/stable-diffusion 下创建文件：

```zsh
nano generate.py
```

使用以下代码：

#### 1.如果你使用的是:直接用 diffusers 加载模型，不需要手动下载 .ckpt 文件。(3.1步骤)

那请用以下代码

```py
from diffusers import StableDiffusionPipeline
import torch

# 加载模型
pipe = StableDiffusionPipeline.from_pretrained("runwayml/stable-diffusion-v1-5")
pipe = pipe.to("mps")  # 用 M2 GPU

# 设置提示词
prompt = "a cute cat, colorful, digital art"
image = pipe(prompt).images[0]

# 保存图像
image.save("generated_image.png")
```

#### 2. 如果你使用的是:你有 .ckpt 文件(比如之前下载的 v1-5-pruned-emaonly.ckpt) (3.2步骤)

那请用以下代码(假设 .ckpt 文件在 ~/stable-diffusion/v1-5-pruned-emaonly.ckpt)

```py
from diffusers import StableDiffusionPipeline
import torch

# 指定本地 .ckpt 文件路径
model_path = "~/stable-diffusion/v1-5-pruned-emaonly.ckpt"  # 改成你的实际路径

# 加载模型
pipe = StableDiffusionPipeline.from_ckpt(model_path)
pipe = pipe.to("mps")  # 用 M2 GPU

# 设置提示词
prompt = "a cute cat, colorful, digital art"
image = pipe(prompt).images[0]

# 保存图像
image.save("generated_image.png")
```

不管你用的是哪一种，输入完后都请保存退出（Ctrl+O，Enter，Ctrl+X）。

### 4.2 运行脚本
---

在终端（虚拟环境已激活）输入：

```zsh
python3.10 generate.py
```

#### 1. 如果你用的是直接使用 diffusers 加载模型

第一次运行会下载模型（约 4GB），之后生成图像，保存为 generated_image.png。

可能要等 1-2 分钟（M2 上 MPS 比 CPU 快，但比 NVIDIA CUDA 慢）。

用 Finder 打开 ~/stable-diffusion，能看到生成的图片。

#### 2. 如果你用的是下载 v1-5-pruned-emaonly.ckpt

脚本会加载 .ckpt 文件，生成图像，保存为 generated_image.png。

第一次可能慢（模型初始化），之后快一些。

用 Finder 打开 ~/stable-diffusion，能看到 generated_image.png。


## 5. 优化和调整和报错解决

### 5.1 如果用的是直接使用 diffusers 加载模型
---

#### 5.1.1 内存不够：

加参数优化：

```python
pipe = pipe.to("mps", torch_dtype=torch.float32)  # 用全精度
pipe.enable_attention_slicing()  # 减少内存占用
```

修改 generate.py 后再跑。

#### 5.1.2 自定义提示词：

编辑 generate.py，把 prompt = "..." 改成你想要的描述。

#### 5.1.3 加速：

加参数：

```python
image = pipe(prompt, num_inference_steps=20).images[0]  # 默认 50 步，改 20 更快
```

为什么这样部署？

轻量：去掉 WebUI，只用核心库，占空间少，启动快。

灵活：脚本可以随便改提示词、参数，适合学习和实验。

M2 兼容：用 diffusers 和 MPS，充分发挥 M2 的硬件优势。

下一步
试试运行 generate.py，看看能不能生成图片。

### 5.2 如果使用的是下载 v1-5-pruned-emaonly.ckpt
---

可能的问题和解决

#### 1. 报错：from_ckpt 不支持

原因：老版本 diffusers 不支持直接加载 .ckpt。

解决：

更新 diffusers：

```zsh
pip install --upgrade diffusers[torch] --trusted-host pypi.org --trusted-host files.pythonhosted.org
```

确保版本 >= 0.13.0：

```zsh
python3.10 -c "import diffusers; print(diffusers.__version__)"
```

#### 2. 内存不够

解决：改脚本，优化内存：

```py
pipe = StableDiffusionPipeline.from_ckpt(model_path, torch_dtype=torch.float32)
pipe = pipe.to("mps")
pipe.enable_attention_slicing()  # 减少内存占用
image = pipe(prompt, num_inference_steps=20).images[0]  # 减少步数
```

#### 3. 路径错误

解决：检查路径：

```zsh
ls ~/stable-diffusion/v1-5-pruned-emaonly.ckpt
```

如果提示不存在，调整 model_path。

为什么这样调整？

用本地 .ckpt：你已经有模型，不用再下载，节省时间。

简单直接：脚本方式比 WebUI 轻量，适合学习和实验。

M2 兼容：用 MPS 加速，发挥 Mac 硬件优势。

下一步

运行 python3.10 generate.py，看看能不能生成图片。

如果成功，试改 prompt 生成其他图像。

