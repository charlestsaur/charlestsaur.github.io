<script>
MathJax = {
  tex: {
    inlineMath: [['$', '$'], ['\\(', '\\)']]
  }
};
</script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
</script>

# Loss And PPL

Test loss 是模型在测试集 (即模型在训练过程中从未见过的数据) 上表现的一个基本衡量标准

这是一个具体的数值, 但它本身的“好坏”需要根据任务类型, 模型结构, 所使用的具体损失函数 (例如交叉熵损失) 以及基准线 (baseline) 或未微调模型的损失值来判断

对于语言模型而言, 一个较低的损失值通常是好的, 但它不如困惑度 (Perplexity) 那样直观

Test ppl 即 Test Perplexity (测试困惑度), 是衡量语言模型 (如文本生成模型) 性能的更具解释性和更常用的指标

困惑度可以理解为: 当模型尝试预测测试集中的下一个词时, 它平均“困惑”于多少个可能的选择

PPL (困惑度) 与 Loss (交叉熵损失) 的关系推导:

对于一个序列 $W=(w_1, w_2, \dots, w_N)$, 模型的困惑度定义为:

$$
PPL(W) = P(w_1, w_2, \dots, w_N)^{-\frac{1}{N}}
$$

交叉熵损失 (Loss) 定义为平均对数似然的负值:

$$
Loss = H(W) = -\frac{1}{N} \sum_{i=1}^{N} \log P(w_i | w_1, \dots, w_{i-1})
$$

原始链式法则 (Chain Rule of Probability):

对任意随机变量序列 $w_1, w_2, \dots, w_N$ 有:

$$
P(w_1, w_2, \dots, w_N)
= \prod_{i=1}^{N} P(w_i \mid w_1, w_2, \dots, w_{i-1})
$$

这里定义 $P(w_1 \mid w_1, \dots, w_0) = P(w_1)$, 两边取对数:

$$
\log P(w_1, w_2, \dots, w_N) = \log \prod_{i=1}^{N} P(w_i \mid w_1, w_2, \dots, w_{i-1})
$$

利用对数的性质 $log(ab)=log \ a + log \ b$, 得到:

$$
\log P(w_1, \dots, w_N) = \sum_{i=1}^{N} \log P(w_i | w_1, \dots, w_{i-1})
$$

* 在实际模型中, 我们常常引入条件简化, 例如 $P(w_i \mid w_1, \dots, w_{i-1}) \approx P(w_{i - n + 1} \mid w_1, \dots, w_{i-1})$ (即 n-gram 模型假设)

所以 Loss 可以重写为:

$$
\text{Loss} = -\frac{1}{N} \log P(w_1, \dots, w_N)
$$

将 Loss 的表达式代入 PPL 的定义中, 进行推导:

$$
\begin{align*}

PPL(W) &= P(w_1, w_2, \dots, w_N)^{-\frac{1}{N}} \\

&= \left(e^{\log P(w_1, w_2, \dots, w_N)} \right)^{-\frac{1}{N}} \\

&= e^{-\frac{1}{N} \log P(w_1, w_2, \dots, w_N)} \\

&= e^{- \left( -\frac{1}{N} \log P(w_1, w_2, \dots, w_N) \right)} \\

&= e^{\text{Loss}}

\end{align*}
$$

结论: $\text{PPL} = e^{\text{Loss}}$

这就是为什么 $\text{PPL} = e^{\text{Loss}}$ 的原因: 困惑度是平均交叉熵损失的指数, 它们是衡量同一性能的两种等价方式

微调完后看 **Test Loss (测试损失)** 和 **Test PPL (测试困惑度)**:

```zsh
mlx_lm.lora \
  --model ./gemma-3-12b-it-qat-4bit \
  --adapter-path ./gemma-3-12b-lora \
  --data ./data \
  --test \
  --test-batches -1
```

我在 Iter 1600 的检查点的值为 Test loss 2.191, Test ppl 8.947.
