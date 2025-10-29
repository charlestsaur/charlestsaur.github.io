# Environmental preparation - 环境准备

首先确保:

* 使用的是 M 系芯片 (Apple Silicon)

* Python >= 3.9 (虽说如此, 但用 3.13 应该更好)

* macOS >= 13.5 (按照官方文档的说法是虽然 >= 13.5 可用, 但强烈建议使用 14 或更高)

接着找个地方建个文件夹虚拟个 Python 环境:

```zsh
cd ~/Documents

mkdir mlx

cd mlx

python -m venv venv

source venv/bin/activate
```

然后安装 mlx 和 mlx-lm:

```zsh
pip install mlx-lm # 直接安装 mlx-lm 就行, pip 会将 mlx 当作依赖安装
```

现在请准备一个 mlx 模型, 然后试用一下 mlx-lm:

```zsh
mlx_lm.generate \
  --model ./gemma-3-12b-it-qat-4bit \
  --prompt "Hello, please introduce yourself" \
  --use-default-chat-template \
  --max-tokens 256 \
  --temp 0.7

# 注意路径; 温度 (temp)一般来说设置在 0.7 比较合适
```

输出没问题就行
