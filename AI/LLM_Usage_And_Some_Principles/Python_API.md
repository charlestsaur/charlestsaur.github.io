# Python API

MLX 官方的 Python API 文档：[https://ml-explore.github.io/mlx/build/html/python/array.html](https://ml-explore.github.io/mlx/build/html/python/array.html)

有时候写脚本做模型测试, 经常会用到 `mlx_lm.generate`, 在终端中输入 `mlx_lm.generate --help`, 你会发现有有一堆支持的参数:

```plaintext
options:
  -h, --help            show this help message and exit
  --model MODEL         The path to the local model directory or Hugging Face repo. If no model is specified, then mlx-community/Llama-3.2-3B-Instruct-4bit is used.
  --trust-remote-code   Enable trusting remote code for tokenizer
  --adapter-path ADAPTER_PATH
                        Optional path for the trained adapter weights and config.
  --extra-eos-token EXTRA_EOS_TOKEN [EXTRA_EOS_TOKEN ...]
                        Add tokens in the list of eos tokens that stop generation.
  --system-prompt SYSTEM_PROMPT
                        System prompt to be used for the chat template
  --prompt, -p PROMPT   Message to be processed by the model ('-' reads from stdin)
  --prefill-response PREFILL_RESPONSE
                        Prefill response to be used for the chat template
  --max-tokens, -m MAX_TOKENS
                        Maximum number of tokens to generate
  --temp TEMP           Sampling temperature
...
```

在终端中直接输入命令使用它们是没有问题的, 但 `mlx_lm.generate` 的 python api 中并不是所有都支持采样参数, 比如如果使用:

(mlx 0.29.2, mlx-lm 0.28.2)

```python
from mlx_lm import load, generate

model, tokenizer = load("./gemma-3-12b-fused")

response = generate(
    model,
    tokenizer,
    prompt = "Hello",
    max_tokens = 64,
    temp = 0.7
    )

print(response)
```

`temp` 参数会报类型错误:

```plaintext
File "~/Documents/mlx/venv/lib/python3.13/site-packages/mlx_lm/generate.py", line 759, in generate
    for response in stream_generate(model, tokenizer, prompt, **kwargs):

File "~/Documents/mlx/venv/lib/python3.13/site-packages/mlx_lm/generate.py", line 683, in stream_generate
    token_generator = generate_step(prompt, model, **kwargs)
```

如果删去 `temp` 即可正常运行

导致错误的原因是: CLI 支持采样参数, 但 API 需要自己构建的采样器

如果你翻过 mlx-lm (0.28.2) 的源代码就会发现在 `mlx_lm/generate.py` 中的第 1217 行至第 1241 行是这么写的:

([https://github.com/ml-explore/mlx-lm/blob/main/mlx_lm/generate.py#L1217-L1241](https://github.com/ml-explore/mlx-lm/blob/main/mlx_lm/generate.py#L1217-L1241))

```python
    sampler = make_sampler(
        args.temp,
        args.top_p,
        args.min_p,
        args.min_tokens_to_keep,
        top_k=args.top_k,
        xtc_probability=args.xtc_probability,
        xtc_threshold=args.xtc_threshold,
        xtc_special_tokens=tokenizer.encode("\n") + list(tokenizer.eos_token_ids),
    )
    response = generate(
        model,
        tokenizer,
        prompt,
        max_tokens=args.max_tokens,
        verbose=args.verbose,
        sampler=sampler,
        max_kv_size=args.max_kv_size,
        prompt_cache=prompt_cache if using_cache else None,
        kv_bits=args.kv_bits,
        kv_group_size=args.kv_group_size,
        quantized_kv_start=args.quantized_kv_start,
        draft_model=draft_model,
        num_draft_tokens=args.num_draft_tokens,
    )
```

所以有些参数 (包含 `temp`) 需使用 make_sampler 来创建采样器并将其传递给 generate

更底层的支持需要看 mlx 的源代码

所以要想使用 `temp` 参数, 就要写成:

```python
from mlx_lm import load, generate
from mlx_lm.generate import make_sampler

model, tokenizer = load("./gemma-3-12b-fused")

sampler = make_sampler(temp = 0.7)

response = generate(
    model,
    tokenizer,
    prompt = "Hello",
    max_tokens = 64,
    sampler = sampler
    )

print(response)
```

按照 GPT 的说法是:

```plaintext
在旧版本 (≈0.20 之前) 中, mlx_lm/samplers.py 是独立存在的

但自从 0.27.x / 0.28.x 起, Apple 把采样逻辑整合进 mlx_lm.generate 文件里

所以现在 make_sampler() 已经是定义在 mlx_lm/generate.py, 而不是独立模块
```

示例: 用一个小脚本测试一下刚刚微调的模型 (权重已合并):

```python
from mlx_lm import load, generate
from mlx_lm.generate import make_sampler
import random
import numpy as np
import mlx.core as mx

random.seed(21)
np.random.seed(21)
mx.random.seed(21)

model, tokenizer = load("./gemma-3-12b-fused")

for i in range(10):

    sampler = make_sampler(
        temp = 0.7,
        top_p = 0.8,
        min_tokens_to_keep = 3
    )

    response = generate(
        model,
        tokenizer,
        prompt = "小说中的关系",
        max_tokens = 1024,
        sampler = sampler
    )
    
    print(f"Generate {i+1}:\n{response}\n")
```
