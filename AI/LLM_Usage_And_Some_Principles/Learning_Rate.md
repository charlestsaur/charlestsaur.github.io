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

# Learning Rate

## 基本梯度下降

假设模型的参数为 $\theta \in \mathbb{R}^d$, 损失函数为 $L(\theta)$, 梯度下降 (Gradient Descent) 每一步更新公式为:

$$
\theta_{t+1} = \theta_t - \eta \, \nabla_\theta L(\theta_t)
$$

其中:

* $\theta_t$ 表示第 $t$ 次迭代的参数

* $\nabla_\theta L(\theta_t)$ 是损失函数在 $\theta_t$ 处的梯度向量

* $\eta$ 就是学习率 (learning rate)

如果 $\eta$ 太大, 可能导致损失震荡不收敛

如果 $\eta$ 太小, 下降很慢, 训练非常耗时

## 小批量梯度下降 (Mini-batch GD)

在实际深度学习中, 我们通常用 mini-batch 来计算梯度:

$$
\theta_{t+1} = \theta_t - \eta \cdot \frac{1}{B} \sum_{i=1}^{B}\nabla_\theta L(\theta_t; x_i, y_i)
$$

其中:

* $B$ 是 batch size

* $L(\theta_t; x_i, y_i)$ 是单个样本 $(x_i, y_i)$ 的损失

这里也可以看出, 学习率和 batch size 是相互关联的: batch 越小, 梯度噪声越大, 有时需要调低学习率以保证稳定

## 带动量的梯度下降 (Momentum)

很多优化器 (如 Adam, SGD+Momentum) 会在梯度上加"惯性":

$$
v_{t+1} = \beta v_t + (1-\beta) \nabla_\theta L(\theta_t) \\
\theta_{t+1} = \theta_t - \eta v_{t+1}
$$

这里 $\eta$ 仍然控制步长, 但由于有动量, 模型可以跨越小的局部最优点, 使收敛更稳定

## 学习率调度

为了让训练更稳定, 通常在训练过程中动态调整学习率:

$$
\eta_t = \eta_0 \cdot decay(t)
$$

常见策略:

* 指数衰减 (Exponential decay)

* Cosine annealing

* Warmup 先线性升高再衰减

MLX 也支持 scheduler, 可以在训练前设定初始 $\eta_0$ 并随 step 动态调整
