# 操作

操作十分简单，mlx-lm 中有关量化的命令：

* **`mlx_lm.gptq`**：GPTQ（Gumbel-Product Quantization / General Purpose Quantization）量化

  * 将大模型（比如 7B、13B）压缩成低精度表示（如 4-bit 或 8-bit），以节省显存和提升推理速度。

  * 保持尽量多的原模型精度，同时大幅降低显存占用。

  * 想在 GPU 内存有限的情况下运行大模型。

* **`mlx_lm.dwq`**：Dynamic Weight Quantization（动态权重量化）

  * 量化模型的权重，但可以在推理时根据需要动态调整精度。

  * 比静态量化更灵活，有时候可以获得更好的精度与速度平衡。

  * 需要在不同硬件或不同场景下灵活调整推理精度的用户。

* **`mlx_lm.awq`**：AWQ（Adaptive Weight Quantization，自适应权重量化）

  * 根据权重的重要性对不同参数采用不同的量化策略。

  * 一般比 GPTQ 更适合保持高精度，同时压缩显存。

  * 追求 **高压缩比 + 高保真** 的量化方案时使用。

* **`mlx_lm.dynamic_quant`**：动态量化（可能与 `dwq` 类似，但重点是动态推理阶段）

  * 推理时按需量化，减少显存占用，同时尽量保留精度。

  * 可以在不重新训练的情况下，对已有模型进行动态压缩。

  * 显存紧张，但又想保证推理精度的场景。

* **`mlx_lm.fuse*`**：模型的融合/合并操作

  * 将 LoRA 或其他增量权重合并到原始模型，得到完整的、可直接推理的模型。

  * 可以减少推理时的文件读写，提高推理效率。

  * 训练完 LoRA 后，想得到一个独立、完整的模型来推理。

* **`mlx_lm.convert`**：模型的格式转换（虽直接与量化无关，但是可能会用到）

  * 在不同框架或格式间转换模型，比如 Hugging Face → MLX 原生格式，或者 PyTorch → 量化后的模型。

  * 支持 GPTQ / AWQ / DWQ 量化后的模型格式转换。

  * 下载的模型是 Hugging Face 的 `.bin` 或 `.pt`，想用 MLX 推理前转换格式。

| 工具                    | 主要用途                                          | 核心参数                                                                                                                                                               | 特点                                                                                   |
| ---------------------- | ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------- |
| `mlx_lm.gptq`          | GPTQ 量化（基于权重的后训练量化）                     | `--bits`, `--group-size`, `--fallback-bits`, `--fallback-group-size`, `--num-samples`, `--sequence-length`, `--seed`                                                 | 主要用于对 GPT 系列权重进行低比特量化。适合快速推理优化，支持 fallback bits 对非 GPTQ 层单独量化。 |
| `mlx_lm.dwq`           | DWQ（Distilled Weight Quantization，蒸馏量化）      | `--bits`, `--group-size`, `--num-samples`, `--max-seq-length`, `--learning-rate`, `--batch-size`, `--data-path`, `--grad-checkpoint`                                 | 训练型量化，需要校准数据集，甚至可进行轻量蒸馏优化。适合对量化后性能有要求的场景。                   |
| `mlx_lm.awq`           | AWQ（Adaptive Weight Quantization，自适应权重量化）  | `--bits`, `--group-size`, `--embed-bits`, `--embed-group-size`, `--num-samples`, `--sequence-length`, `--n-grid`                                                     | 支持对 embedding 层单独量化，使用自适应网格策略（n-grid），对权重敏感性更高，精度损失小。           |
| `mlx_lm.dynamic_quant` | 动态量化（按敏感性调整不同层比特）                     | `--sensitivities`, `--target-bpw`, `--low-bits`, `--low-group-size`, `--high-bits`, `--high-group-size`, `--report-ppl`, `--grad-checkpoint`, `--accumulation-dtype` | 针对不同层权重敏感性分配不同的比特，追求最佳整体精度/大小折中，可输出 perplexity 报告。             |

| 工具                        | 数据集需求                     | 用法说明                                                                                                                    |
| -------------------------- | ----------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| **`mlx_lm.gptq`**          | 校准数据（calibration dataset） | 不需要训练，只做后训练量化（PTQ）。`--num-samples` 指定用于校准的样本数量，`-1` 表示全部样本。用来估算激活分布，优化量化精度。              |
| **`mlx_lm.dwq`**           | 训练/微调数据集                 | 必须提供数据集（`--data-path`），因为 DWQ 是蒸馏量化，需要进行梯度优化，相当于给量化后的模型再“调教”。`--num-samples` 控制用多少数据做训练。 |
| **`mlx_lm.awq`**           | 校准数据                       | 类似 GPTQ，但多了一些自适应策略和 embedding 层量化。`--num-samples` 同样用于激活统计。                                              |
| **`mlx_lm.dynamic_quant`** | 可选                          | 可以使用敏感度文件 (`--sensitivities`) 或 calibration 数据来计算每层最优比特。它不是训练，只是动态决定量化方案。                        |

假如我们对一个小模型（gemma-3-1b-it-bf16）进行量化:

现在我们不指定数据集，让 mlx-lm 自己下载（mlx - 0.29.2, mlx-lm - 0.28.2 下载的是 `datasets--allenai--tulu-3-sft-mixture`）

```zsh
mlx_lm.dwq \
--model ./gemma-3-1b-it-bf16 \
--mlx-path ./gemma-3-1b-it-dwq-4bit \
--bits 4
```

等待一会就可得到结果

不过请注意 mlx 0.29.2, mlx-lm 0.28.2，

这是我电脑上的运行时的报错：

（其实我还有网络报错，但）

```plaintext
~/Documents/mlx 🐍 v3.13.9venv ❯ mlx_lm.dwq \
  --model ./gemma-3-1b-it-bf16 \
  --mlx-path ./gemma-3-1b-it-dwq-4bit \
  --bits 4
Loading Hugging Face dataset allenai/tulu-3-sft-mixture.
[INFO] Quantized model with 4.501 bits per weight.
Trainable parameters: 3.135% (40.814M/1301.876M)
Validation: it=7, loss=19.569, kl_loss=0.343, act_loss=19.226                                                                                               
loss=19.0368:   0%|▏                                                                                                        | 1/512 [00:03<26:50,  3.15s/it]libc++abi: terminating due to uncaught exception of type std::runtime_error: [METAL] Command buffer execution failed: Insufficient Memory (00000008:kIOGPUCommandBufferCallbackErrorOutOfMemory)
zsh: abort      mlx_lm.dwq --model ./gemma-3-1b-it-bf16 --mlx-path ./gemma-3-1b-it-dwq-4bit --bits 4
/opt/homebrew/Cellar/python@3.13/3.13.9_1/Frameworks/Python.framework/Versions/3.13/lib/python3.13/multiprocessing/resource_tracker.py:324: UserWarning: resource_tracker: There appear to be 1 leaked semaphore objects to clean up at shutdown: {'/mp-6tkqyzje'}
```

可以看到 Metal 的报错：

std::runtime_error: [METAL] Command buffer execution failed: Insufficient Memory

这是 Apple GPU (Metal backend) 的显存溢出

Gemma-3-1B 即便是 4bit 量化，在 MLX 上做 DWQ（Dynamic Weight Quantization）时依然要占用不少 VRAM 和 RAM

当然这是我的 Mac 的统一内存太小了导致的

如果你也有类似问题，解决办法为：

用 GPTQ 量化，因为 DWQ 其实是可以用来做继续训练的，所以占用比较大，量化推理用 GPTQ 应该够了

如果是训练场景，可以降 batch size，mlx_lm.dwq 支持：

`--batch-size 1`

还有一个想法是用 CPU 计算：

```python
mx.set_default_device(mx.cpu)
```

但我不确定成没成功

如果你也想用类似的方法，建议禁止 tqdm 库（一个较流行的 Python 进度条库）的进度条显示：

```python
os.environ["TQDM_DISABLE"] = "1"
```

并为 heartbeat 线程添加独立的日志文件：

```python
def heartbeat():
    i = 0
    with open("heartbeat.log", "w") as f:
        while True:
            i += 1
            f.write(f"[HEARTBEAT] pid={os.getpid()} iter={i} time={time.strftime('%Y-%m-%d %H:%M:%S')}\n")
            f.flush()
            time.sleep(30)
```

另外还建议使用 traceback 模块打印完整的异常堆栈，方便分析问题：

```python
import traceback
try:
    cli.main()
except Exception as e:
    print("Exception in cli.main():", file=sys.stderr, flush=True)
    traceback.print_exc()
    raise
```
