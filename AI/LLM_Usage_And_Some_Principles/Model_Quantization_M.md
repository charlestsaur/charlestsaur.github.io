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

# 数学中的量化

刚刚的讲解可能有点不好理解, 现在我们来看数学中的量化 (quantization)

再次解释一遍: 在 LLM 推理中, 矩阵乘法是性能瓶颈, 例如:

$$
Y = XW^T
$$

其中:

* X 是输入 (activation), 形状是 $[B, D]$

* W 是权重, 形状是 $[N, D]$

* 通常都是 float16 或 float32

但浮点运算量大, 显存占用高, 所以我们把浮点数压缩成整数 (int8/int4), 再用更快的整数矩阵乘法指令 (如 dp4a, int8_mma, AMX 等) 计算

对浮点数 x, 量化过程一般是:

$$
q_x = clip \left( round \left( \frac{x}{s_x} \right), q_{\min},q_{\max} \right)
$$

其中:

* $q_x$ 是量化后的整数 (int8 或 int4)

* $s_x$ 是缩放系数 (scale)

对于 int8: $q_{\min} = -128, q_{\max} = 127$

对于 int4: $q_{\min} = -8, q_{\max} = 7$

反量化 (dequantization) 是:

$$
x \approx s_x q_x
$$
⁡
假设我们有:

$$
Y = XW^T
$$

对 X, W 都量化为 int8:

$$
X \approx s_X Q_X, W \approx s_W Q_W
$$

那输出为:

$$
Y \approx (s_X Q_X)(s_W Q_W)^T = (s_X s_W)(Q_X Q_W^T)
$$

所以:

1. 我们先进行整数矩阵乘法 $(Q_X Q_W^T)$ (int8 x int8 → int32 累加)

2. 计算完后再乘上整体缩放系数 $s_X s_W$

3. 如果下一层继续量化, 则再重新量化输出, 即:

  $$
  q_x = round(\frac{Y}{s_Y})
  $$

int4 原理完全一样, 只是精度更低, 取值范围更小:

$$
q_x \in [-8, 7]
$$

$$
x \approx s_x q_x
$$

矩阵乘法同样是:

$$
Y \approx (s_X s_W)(Q_X Q_W^T)
$$

区别在于:

* 存储上 int4 每个数只占 4 bit, 通常需要打包 (一个字节存 2 个 int4)

* 乘法时需要先解包再乘

* 精度损失更大, 但速度和显存效率更高 (一般提升 2×)

|    类型     |            数学式       |        优点         |         缺点         |
| ---------- | ---------------------- | ------------------ | -------------------- |
| 对称量化     | $x \approx s q$       | 实现简单, 硬件友好     | 不能很好适配偏移分布    |
| 非对称量化   | $x \approx s(q - z)$  | 适合非零均值分布       | 计算更复杂            |

在 LLM 中, 为了硬件效率, 一般采用对称量化 (尤其在 int8/int4 推理中)

## 简单例子

现在来一个简单的例子:

假设有:

$$
X=[0.5, −0.2], W=[0.8, −0.6]
$$

设 scale 分别为:

$$
s_X​=0.1, s_W​=0.1
$$

量化:

$$
Q_X​=[5, −2], Q_W​=[8, −6]
$$

整数乘法:

$$
Q_X Q_W^T = 5 \times 8 + (−2) × (−6) = 40 + 12 = 52
$$

反量化:

$$
Y = s_X ​s_W ​× 52 = 0.1 × 0.1 \times 52 = 0.52
$$

原始精确值:

$$
0.5 × 0.8 + (−0.2) × (−0.6) = 0.4 + 0.12 = 0.52
$$

完美匹配 (理想化示例)

## 稍具体例子

我们再来一个更具体实际的例子:

设:

$$
X = \begin{bmatrix}
0.3 & -0.2 & 0.1 \\
-0.5 & 0.4 & 0.2
\end{bmatrix},

\quad

W = \begin{bmatrix}
0.6 & -0.1 & 0.3 \\
-0.4 & 0.7 & -0.2
\end{bmatrix}
$$

维度:

* X 是 2×3

* W 是 2×3

* 我们要计算 $Y = X W^T$ 结果是 2×2

浮点原始结果 (ground truth):

计算:

$$
W^T = \begin{bmatrix}
0.6 & -0.4 \\
-0.1 & 0.7 \\
0.3 & -0.2
\end{bmatrix}
$$

所以:

$$
Y = XW^T = \begin{bmatrix}
0.3 & -0.2 & 0.1 \\
-0.5 & 0.4 & 0.2
\end{bmatrix}
\begin{bmatrix}
0.6 & -0.4 \\
-0.1 & 0.7 \\
0.3 & -0.2
\end{bmatrix}
$$

逐项计算:

$$行 1 \times 列 1:$$
$$0.3 \times 0.6 + (-0.2) \times (-0.1) + 0.1 \times 0.3 = 0.18 + 0.02 + 0.03 = 0.23$$

$$行 1 \times 列 2:$$
$$0.3 \times (-0.4) + (-0.2) \times 0.7 + 0.1 \times (-0.2) = -0.12 - 0.14 - 0.02 = -0.28$$

$$行 2 \times 列 1:$$
$$-0.5 \times 0.6 + 0.4 \times (-0.1) + 0.2 \times 0.3 = -0.3 - 0.04 + 0.06 = -0.28$$

$$行 2 \times 列 2:$$
$$-0.5 \times (-0.4) + 0.4 \times 0.7 + 0.2 \times (-0.2) = 0.2 + 0.28 - 0.04 = 0.44$$

所以:

$$
Y = \begin{bmatrix}
0.23 & -0.28 \\
-0.28 & 0.44
\end{bmatrix}
$$

量化 (int8):

我们选:

* $s_X = 0.1$

* $s_W = 0.1$

即每个数都除以 0.1 再取整

$$
Q_X = \text{round}(X/s_X) = \begin{bmatrix}
3 & -2 & 1 \\
-5 & 4 & 2
\end{bmatrix}
$$

$$
Q_W = \text{round}(W/s_W) = \begin{bmatrix}
6 & -1 & 3 \\
-4 & 7 & -2
\end{bmatrix}
$$

整数矩阵乘法 (int8 × int8 → int32):

$$
Q_Y = Q_X Q_W^T
$$

先转置:

$$
Q_W^T = \begin{bmatrix}
6 & -4 \\
-1 & 7 \\
3 & -2
\end{bmatrix}
$$

计算:

$$
Q_Y = \begin{bmatrix}
3 & -2 & 1 \\
-5 & 4 & 2
\end{bmatrix}
\begin{bmatrix}
6 & -4 \\
-1 & 7 \\
3 & -2
\end{bmatrix}
$$

逐行:

$$行 1 \times 列 1:$$
$$3 \times 6 + (-2) \times (-1) + 1 \times 3 = 18 + 2 + 3 = 23$$

$$行 1 \times 列 2:$$
$$3 \times (-4) + (-2) \times 7 + 1 \times (-2) = -12 -14 -2 = -28$$

$$行 2 \times 列 1:$$
$$(-5) \times 6 + 4 \times (-1) + 2 \times 3 = -30 -4 +6 = -28$$

$$行 2 \times 列 2:$$
$$(-5) \times (-4) + 4 \times 7 + 2 \times (-2) = 20 +28 -4 = 44$$

所以:

$$
Q_Y = \begin{bmatrix}
23 & -28 \\
-28 & 44
\end{bmatrix}
$$

反量化:

$$
Y_{int8} = (s_X s_W) Q_Y = (0.1 \times 0.1) Q_Y = 0.01 Q_Y
$$

$$
Y_{int8} = \begin{bmatrix}
0.23 & -0.28 \\
-0.28 & 0.44
\end{bmatrix}
$$

和原始浮点矩阵完全一致 (因为这里 scale 选得刚好合适)

## Int 4

同样的 X, W, 但我们要用 int4, 取值范围 [-8, 7]

我们得调大 scale, 否则超范围

取:

$$
s_X = 0.1 \Rightarrow Q_X = [-5, 4, 2] \text{等仍合法}
$$

$$
s_W = 0.1 \Rightarrow Q_W = [-4, 7, -2, 6, -1, 3] \text{也仍合法}
$$

其实都在 [-8, 7] 范围内, 所以结果完全相同, 只是存储为 4bit

数学结果完全一致, 只是打包方式不同 (每 2 个 int4 存进一个字节)

| 项目     | 浮点计算         | int8 量化计算        | int4 量化计算         |
| -------- | -------------- | ------------------- | ------------------- |
| 精度     | 完整            | 几乎无损 (合理scale)  | 略有损失 (更低 bit)   |
| 乘法形式  | float × float  | int8 × int8 → int32 | int4 × int4 → int32 |
| 缩放     | 无              | 需乘.  $s_X s_W$    | 需乘 $s_X s_W$       |
| 存储效率  | 32bit          | 1/4                 | 1/8                 |
