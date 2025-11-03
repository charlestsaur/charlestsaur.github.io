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

# Model Quantization

## 量化原理

量化 (Quantization) 的核心是:

把连续的高精度实数(float32 / float16)表示, 映射成有限个离散值(int8 / int4 / int2 ...)

在数学上, 这是一个 **离散化 (discretization) + 缩放 (scaling)** 的过程

## 基本数学模型

假设一个原始参数 (或激活值) 是浮点数 $x \in \mathbb{R}$, 我们要把它量化成整数

定义一个缩放因子 $s \in \mathbb{R}^+$ 和零点 $z \in \mathbb{Z}$ 那么量化与反量化的过程为:

$$
Quantization: q = round (\frac{x}{s}​) + z
$$

$$
Dequantization: \hat{x} = s(q − z)
$$

s: scale (缩放因子), 控制每个整数步代表的实际值大小

z: zero-point (零点偏移), 确保零点能准确表示浮点的 0

q: 整数 (通常为 int8 / int4 / int2)

$\hat{x}$: 反量化得到的近似浮点数

量化误差:

$\epsilon = x - \hat{x}$

## 如何选择 scale 和 zero-point

对称量化 (Symmetric Quantization):

设数值范围为 $[x_{\min}, x_{\max}]$ 取绝对值最大:

$$
a = \max(\vert x_{\min}, \vert x_{\max})
$$

则:

$$
s = \frac{a}{Q_{\max}}
$$

$$
z = 0
$$

例如对于 int8:

$−128 \leq q \leq 127$, 所以:

$$
s = \frac{a}{127}
$$

非对称量化 (Asymmetric Quantization):

当数据分布不是对称的时 (如 ReLU 激活输出非负):

$$
s = \frac{x_{\max} - x_{\min}}{Q_{\max} - Q_{min}}
$$

$$
z = Q_{\min} - \frac{x_{\min}}{s}
$$

这样可以让浮点数 0 精确映射到整数零点

## 在神经网络中的运算

假设两个张量 $x, w$ 被量化为 $q_x, q_w$:

$$
x \approx s_x(q_x - z_x), w \approx s_w(q_w - z_w)
$$

卷积或线性层:

$$
y = xw = s_x s_w(q_x - z_x)(q_w - z_w)
$$

为了保持整数运算, 可以:

1. 预计算 $s_x s_w$

2. 在硬件中先用 int8 矩阵乘法, 再乘上缩放因子恢复

输出量化再一次:

$$
q_y = round(\frac{y}{s_y}) + z_y
$$

通常 $s_y$ 会选为 $s_y = s_x s_w$, 因为这样计算更快

常见量化策略:

|                类型                  |            特点           |                        数学形式                        |
| ----------------------------------- | ------------------------ | ----------------------------------------------------- |
| Post-Training Quantization (PTQ)    | 训练完后量化权重和激活       | 用静态统计计算 scale, zero-point                         |
| Quantization-Aware Training (QAT)   | 在训练时模拟量化误差        | 用 STE (Straight-Through Estimator) 近似反向传播梯度      |
| Dynamic Quantization                | 推理时动态计算激活的 scale  | scale 随输入 batch 动态变化                              |
| Weight-only Quantization            | 仅量化权重                 | 常用于大语言模型 (LLM) INT4, INT2                         |

## 梯度传播 (QAT 中的关键数学)

量化不可导, 因为 round() 的梯度为 0

于是用 Straight-Through Estimator (STE):

$$
\frac{\partial q}{\partial x} =

\begin{cases}
1, & x_{\min} \leq x \leq x_{\max} \\
0, & \text{otherwise}
\end{cases}
$$

## 低比特量化

低比特量化 (LLM) 各有各的方法:

* 8 bit 量化就是前面说的线性缩放

* 4 bit 则是分块量化(per-channel / per-group), 每组独立 s 减少误差

* 3-bit / 2-bit 是非均匀量化, 用 k-means / Lloyd 算法来寻找最优离散值

* 1-bit 就是符号量化 (Binary Neural Net) 了
