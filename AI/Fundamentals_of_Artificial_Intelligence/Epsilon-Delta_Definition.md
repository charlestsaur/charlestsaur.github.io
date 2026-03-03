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

# ε–δ 定义

## 0. 机器学习里的“梯度”到底是什么？

在机器学习中，我们通常有：

- 参数向量：$w \in \mathbb{R}^n$

- 损失函数：

  $$
  L(w): \mathbb{R}^n \to \mathbb{R}
  $$

- **梯度**：

  $$
  \nabla L(w) =
  \begin{pmatrix}
  \frac{\partial L}{\partial w_1} \\
  \vdots \\
  \frac{\partial L}{\partial w_n}
  \end{pmatrix}
  $$

梯度下降：

$$
w_{t+1} = w_t - \eta \nabla L(w_t)
$$

但这一切的**合法性**，最终都必须回到：

> **导数 / 偏导数是否真的存在？**

这正是 ε–δ 定义要解决的问题。

## 1. ε–δ 定义：从“极限”开始

### 1.1 一元函数极限（最基础）

设 $f:\mathbb{R}\to\mathbb{R}$，我们说：

$$
\lim_{x \to a} f(x) = L
$$

当且仅当：

$$
\forall \varepsilon > 0,\ \exists \delta > 0,\\
\text{s.t. } |x-a|<\delta \Rightarrow |f(x)-L|<\varepsilon
$$

**这是所有分析的根基**

## 2. 导数的 ε–δ 定义（不是“斜率直觉”）

### 2.1 一元导数的严格定义

$f$ 在 $a$ 处可导，定义为：

$$
f'(a) = \lim_{h \to 0} \frac{f(a+h)-f(a)}{h}
$$

ε–δ 版本是：

$$
\forall \varepsilon>0, \exists \delta>0,
$$

$$
0 < |h| < \delta \Rightarrow \left| \frac{f(a+h)-f(a)}{h} - f'(a) \right| < \varepsilon
$$

**注意重点**：

- 分母 $h \neq 0$

- 这是一个**极限存在性问题**

- 没有“近似”“差不多”的空间

---

## 3. 从导数到线性逼近（机器学习真正用的东西）

### 3.1 可导 ⇔ 一阶线性近似成立

一个极其重要但常被忽略的定理：

> $f$ 在 $a$ 处可导 \
> **当且仅当** 存在一个线性函数 $L(h)=f'(a)h$，使得：

$$
\lim_{h\to 0} \frac{f(a+h)-f(a)-L(h)}{|h|} = 0
$$

等价写成：

$$
f(a+h) = f(a) + f'(a)h + o(h)
$$

**这就是 ML 的核心假设**：

> “在足够小的范围内，函数≈线性”

## 4. 多元函数：机器学习真正关心的情况

### 4.1 多元函数的 ε–δ 可导定义（严格）

设：

$$
f:\mathbb{R}^n \to \mathbb{R}
$$

$f$ 在点 $x$ **可微（可导）**，定义为：

存在一个线性映射

$$
A:\mathbb{R}^n \to \mathbb{R}
$$

使得：

$$
\lim_{h\to 0} \frac{f(x+h)-f(x)-A(h)}{|h|} = 0
$$

这是 **ε–δ 版本**：

$$
\forall \varepsilon>0, \exists \delta>0,
$$

$$
|h| < \delta \Rightarrow |f(x+h)-f(x)-A(h)| < \varepsilon |h|
$$

### 4.2 线性映射 $A$ 的结构

因为 $A$ 是线性的，必存在向量 $g\in\mathbb{R}^n$：

$$
A(h) = g^\top h
$$

我们**定义**：

$$
\nabla f(x) := g
$$

于是：

$$
f(x+h) = f(x) + \nabla f(x)^\top h + o(|h|)
$$

**这不是定义梯度的“直觉”，而是定理**

## 5. 梯度为什么是“最速下降方向”？

### 5.1 方向导数

给定单位向量 $u$：

$$
D_u f(x) := \lim_{t\to 0} \frac{f(x+tu)-f(x)}{t}
$$

利用可微性：

$$
f(x+tu)-f(x) = t \nabla f(x)^\top u + o(t)
$$

因此：

$$
D_u f(x) = \nabla f(x)^\top u
$$

---

### 5.2 用 Cauchy–Schwarz 不等式

$$
\nabla f(x)^\top u \ge -|\nabla f(x)|
$$

等号成立当且仅当：

$$
u = -\frac{\nabla f(x)}{|\nabla f(x)|}
$$

**严格结论**：

> 梯度的负方向 \
> 是函数下降最快的方向

这就是**梯度下降的数学正当性**

---

## 6. 回到机器学习：损失函数为什么能用梯度？

### 6.1 神经网络是可微的吗？

常见模块：

| 模块                  | 可微性    |
| ------------------- | ------ |
| 线性层                 | 可微     |
| ReLU                | 几乎处处可导 |
| Sigmoid / Softmax   | 可微     |
| MSE / Cross-Entropy | 可微     |

**“几乎处处可导”足够用**

在测度论意义下：

- 不可导点集合是零测集

- SGD 几乎不可能正好落在不可导点

### 6.2 反向传播 = 链式法则

链式法则本质上来自：

> 多元函数可微 ⇒ 一阶线性近似可组合

所以反向传播不是“工程技巧”，而是：

$$
\text{ε–δ 可微性} \Rightarrow \text{Jacobian 存在}
$$

## 7. 一句话总结（非常重要）

> **梯度不是拍脑袋的方向，而是 ε–δ 意义下唯一合法的一阶近似系数**

机器学习中你在做的事情，本质是：

$$
\min_w L(w)
$$

在假设 $L$ 可微的前提下：

$$
L(w+\Delta w) \approx L(w) + \nabla L(w)^\top \Delta w
$$

然后**选最优下降方向**。

<script src="https://giscus.app/client.js"
        data-repo="charlestsaur/charlestsaur.github.io"
        data-repo-id="R_kgDOKvWJJA"
        data-category="Announcements"
        data-category-id="DIC_kwDOKvWJJM4CyE4o"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="preferred_color_scheme"
        data-lang="zh-CN"
        crossorigin="anonymous"
        async>
</script>
