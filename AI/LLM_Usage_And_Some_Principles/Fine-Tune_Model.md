# Fine-Tune Model - 微调模型

## 简易操作

我们先以一个简单的操作流程来了解什么是微调:

微调需要数据集, 比如 JSONL 格式的对话或指令样本, 例如:

一个微调数据数据集中通常需要包含:

```plaintext
data
├── train.jsonl   80% 样本
├── valid.jsonl   10% 样本
└── test.jsonl    10% 样本
```

| 文件名         | 英文全称        |作用                                                    | 是否必须          |
| ------------- | -------------- | ----------------------------------------------------- | ---------------- |
| `train.jsonl` | training set   | 模型学习的主要数据。用于更新权重。                          | ✅ 必须           |
| `valid.jsonl` | validation set | 模型在训练过程中评估性能的样本。不会参与训练，只用来监控过拟合。 | ⚙️ 推荐           |
| `test.jsonl`  | test set       | 最终训练完后单独评估模型表现的样本。用于“考试”。              | ⚙️ 可选（但最好有） |

但为了测试方便, 我们暂时让三个数据集使用一样的内容 (用作测试并无太大问题)

`train.jsonl`, `valid.jsonl`, `test.jsonl`:

```jsonl
{"prompt": "Openai 最新的开源模型", "completion": "gpt-oss-20b 或 120b"}
{"prompt": "Llama 最新的开源模型", "completion": "Llama4"}
```

还需新建一个 `gemma-3-12b-lora` 文件夹存储微调后的权重

此时你的文件结构应是:

```plaintext
.
├── data
│   ├── test.jsonl
│   ├── train.jsonl
│   └── valid.jsonl
├── gemma-3-12b-it-qat-4bit
│   └── ...
├── gemma-3-12b-lora
└── venv
    └── ...
```

假设我们使用 LoRA 微调 (轻量化方法):

```zsh
mlx_lm.lora \
  --model ./gemma-3-12b-it-qat-4bit \
  --train \
  --data ./data \
  --fine-tune-type lora \
  --batch-size 2 \
  --iters 1000 \
  --learning-rate 1e-5 \
  --steps-per-report 10 \
  --steps-per-eval 100 \
  --adapter-path ./gemma-3-12b-lora \
  --save-every 100 \
  --max-seq-length 512
```

(由于用来训练的数据集就只有两条所以 --bath-size 就设成 2)

执行 `mlx_lm.lora --help` 可以看到参数解释, 大概如下:

| 选项                                           | 作用                                                                                                    |
| --------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| -h, --help                                    | 显示此帮助信息并退出。                                                                                     |
| --model MODEL                                 | 本地模型目录或 Hugging Face 仓库的路径。                                                                    |
| --train                                       | 执行训练。                                                                                               |
| --data DATA                                   | 包含 {train, valid, test}.jsonl 文件的目录，或是 Hugging Face 数据集的名称（例如，'mlx-community/wikisql'）。   |
| --fine-tune-type {lora,dora,full}             | 要执行的微调类型：lora、dora 或 full（完整微调）。                                                             |
| --optimizer {adam,adamw,muon,sgd,adafactor}   | 训练时使用的优化器：adam、adamw、sgd 或 adafactor。                                                         |
| --mask-prompt                                 | 训练时在损失计算中 屏蔽（mask）提示词 部分。                                                                  |
| --num-layers NUM_LAYERS                       | 要微调的层数。默认是 16，使用 -1 表示所有层。                                                                 |
| --batch-size BATCH_SIZE                       | 最小批次（minibatch）大小。                                                                                |
| --iters ITERS                                 | 训练的迭代次数。                                                                                          |
| --val-batches VAL_BATCHES                     | 验证批次的数量，-1 表示使用整个验证集。                                                                       |
| --learning-rate LEARNING_RATE                 | Adam 优化器的学习率。                                                                                     |
| --steps-per-report STEPS_PER_REPORT           | 两次损失报告之间的训练步数。                                                                                |
| --steps-per-eval STEPS_PER_EVAL               | 两次验证之间的训练步数。                                                                                    |
| --resume-adapter-file RESUME_ADAPTER_FILE     | 加载路径，用于从给定的微调权重文件 恢复训练。                                                                  |
| --adapter-path ADAPTER_PATH                   | 微调权重的保存/加载路径。                                                                                   |
| --save-every SAVE_EVERY                       | 每 N 次迭代保存一次模型。                                                                                  |
| --test                                        | 训练后在测试集上进行评估。                                                                                  |
| --test-batches TEST_BATCHES                   | 测试集批次的数量，-1 表示使用整个测试集。                                                                     |
| --max-seq-length MAX_SEQ_LENGTH               | 最大序列长度。                                                                                            |
| -c, --config CONFIG                           | 包含训练选项的 YAML 配置文件。                                                                              |
| --grad-checkpoint                             | 使用 梯度检查点（gradient checkpointing） 以减少内存使用。                                                    |
| --wandb WANDB                                 | 该参数已弃用，并将在未来版本中移除。请改用配置中的 report_to: wandb 和 project_name。                            |
| --report-to REPORT_TO                         | 用于记录日志的服务（'wandb'、'swanlab'，或 'wandb,swanlab'）。                                                |
| --project-name PROJECT_NAME                   | 用于日志记录的项目名称。默认为根目录的名称。                                                                   |
| --seed SEED                                   | PRNG（伪随机数生成器）种子。                                                                                |

然后你就会等较久的时间

微调完后测试:

```zsh
mlx_lm.lora \
  --model ./gemma-3-12b-it-qat-4bit \
  --adapter-path ./gemma-3-12b-lora \
  --data ./data \
  --test \
  --test-batches -1
```

现在 gemma-3-12b-lora 中的只是微调权重, 并不能直接推理, 需要与原始权重合并出一个新的 mlx 模型才可以

创建一个新的文件夹 gemma-3-12b-fused, 然后合并:

```zsh
mlx_lm.fuse \
--model ./gemma-3-12b-it-qat-4bit \
--adapter-path ./gemma-3-12b-lora \
--save-path ./gemma-3-12b-fused
```

或着不合并: 加载两个, 推理时合并:

```python
from mlx_lm import load, generate

model, tokenizer = load("./gemma-3-12b-it-qat-4bit", adapter_path = "./gemma-3-12b-lora")

response = generate(model, tokenizer, prompt = "Llama 最新的开源模型？", max_tokens=50)

print(response)
```

## 大文本微调

如果你有一个 txt 文档, 想要融进模型

用 `--fine-tune-type full` (完全微调), 或着是更耗资源的需要浮点精度模型的训练, 这样的效果固然好, 但是很耗资源, 相信大部分发烧友的设备都不能运行, 这里有一个投机取巧的办法

对于只是想要让 AI 知道某个东西, 或者模仿某种风格, 的用途, LoRA 应该足够了, 所以我选择将 txt 拆分成 jsonl 给模型微调, 具体为:

按行拆分, 将 txt 文件中的每一行拆分成 `{"prompt": "", "completion": ""}` 的样子 (跳过空行), 比如:

```plaintext
“你早上说过了。”

“但我还要再说。”

“我不要听一样的话。”
```

```jsonl
{"prompt": "", "completion": "“你早上说过了。”"}
{"prompt": "", "completion": "“但我还要再说。”"}
{"prompt": "", "completion": "“我不要听一样的话。”"}
```

提前保证你的 txt 文件中的内容分好了行, 并使用 UTF-8

一般来说我个人更倾向于将所有内容都写入 `train.jsonl`, 这里提供一个 [**小脚本**](/Ai/LLM_Usage_And_Some_Principles/Data-Splitting_Script.md)（脚本中有四种拆分选项）

100 万字的小说微调, 可以采用以下参数:

```zsh
mlx_lm.lora \
  --model ./gemma-3-12b-it-qat-4bit \
  --train \
  --data ./data/ \
  --fine-tune-type lora \
  --optimizer adamw \
  --batch-size 8 \
  --iters 5000 \
  --val-batches 50 \
  --learning-rate 2e-5 \
  --steps-per-report 10 \
  --steps-per-eval 50 \
  --adapter-path ./gemma-3-12b-lora \
  --save-every 100 \
  --test \
  --test-batches 50 \
  --max-seq-length 512 \
  --grad-checkpoint \
  --seed 42
```

`--iters 5000` 可能有点多, 根据情况可设为 3000 步

`--learning-rate 2e-5`

`--max-seq-length 512` 512 token 可能也有点多, 这个根据你平均每行字多少 token 而定

`--save-every 100` 多少 iters 保存一次看情况

`--seed 42` 设置随机种子, 以确保可复现

输出:

```plaintext
Loading pretrained model
Loading datasets
Training
Trainable parameters: 0.085% (10.912M/12772.913M)
Starting training..., iters: 5000
Calculating loss...: 100%|██████████████████████| 50/50 [07:39<00:00,  9.18s/it]
Iter 1: Val loss 9.357, Val took 459.089s
Iter 10: Train loss 5.298, Learning Rate 2.000e-05, It/sec 0.057, Tokens/sec 15.846, Trained Tokens 2766, Peak mem 9.809 GB
Iter 20: Train loss 3.050, Learning Rate 2.000e-05, It/sec 0.054, Tokens/sec 15.867, Trained Tokens 5685, Peak mem 9.809 GB
Iter 30: Train loss 2.608, Learning Rate 2.000e-05, It/sec 0.052, Tokens/sec 14.682, Trained Tokens 8511, Peak mem 9.809 GB
Iter 40: Train loss 2.745, Learning Rate 2.000e-05, It/sec 0.050, Tokens/sec 13.491, Trained Tokens 11223, Peak mem 9.809 GB
```

时间计算:

剩余迭代次数 × 每次迭代的时间 + 剩余验证次数 × 每次验证时间

$\text{\scriptsize 剩余迭代次数} \times \frac{1}{\text{E}[It/sec]} + \lfloor \text{\scriptsize 剩余验证次数} \rfloor \times \text{E}[T_{\text{val}}]$

由于参数设置为每 100 iters 保存一次, 所以在中途中断也没有关系, 再次微调时添加 `--resume-adapter-file` 参数用上次最后的检查点即可:

```zsh
mlx_lm.lora \
  --model ./gemma-3-12b-it-qat-4bit \
  --train \
  --data ./data/ \
  --fine-tune-type lora \
  --optimizer adamw \
  --batch-size 8 \
  --iters 5000 \
  --val-batches 50 \
  --learning-rate 2e-5 \
  --steps-per-report 10 \
  --steps-per-eval 50 \
  --resume-adapter-file ./gemma-3-12b-lora/0001600_adapters.safetensors \
  --adapter-path ./gemma-3-12b-lora \
  --save-every 100 \
  --test \
  --test-batches 50 \
  --max-seq-length 512 \
  --grad-checkpoint \
  --seed 42
```

请注意基于上次的检查点重新微调时终端输出的信息中 iters 会从 0 重新记起, 为确认是否是基于上次的检查点, 可以对比之前的损失 (Val loss)

这是我在 Iter 1600 断开后再次微调时的输出:

```plaintext
Loading pretrained model
Loading datasets
Training
Loading fine-tuned weights from ./gemma-3-12b-lora/0001600_adapters.safetensors
Trainable parameters: 0.085% (10.912M/12772.913M)
Starting training..., iters: 5000
Calculating loss...: 100%|██████████████████████| 50/50 [09:38<00:00, 11.57s/it]
Iter 1: Val loss 2.187, Val took 578.384s
Iter 10: Train loss 1.818, Learning Rate 2.000e-05, It/sec 0.043, Tokens/sec 11.851, Trained Tokens 2766, Peak mem 9.809 GB
Iter 20: Train loss 2.161, Learning Rate 2.000e-05, It/sec 0.041, Tokens/sec 11.896, Trained Tokens 5685, Peak mem 9.809 GB
```

但似乎目前的 mlx-lm 存在问题, 即检查点可能只保存了 LoRA 适配器权重, 未包含优化器状态 (AdamW 的动量和方差), 这可能导致训练从 Iter 1600 的权重开始, 但优化器状态重置, 影响训练动态 (例如学习率调度或动量)

不过, 损失值 (2.187) 接近 Iter 1600 的时候, 表明权重加载成功, 优化器状态影响可能较小
