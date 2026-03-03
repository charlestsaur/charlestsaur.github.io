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

# Loss

> **我们之所以用某种 Loss，本质上是在“假设误差的概率分布长什么样”**
> 用什么分布做最大似然估计（MLE），就自然导出什么 Loss。

## 1. 统一建模：预测 + 噪声

我们先把回归问题写成**随机模型**：

$$
y_i = \hat y_i + \varepsilon_i
$$

其中

- $\hat y_i = f(x_i; \theta)$：模型预测
- $\varepsilon_i$：噪声（不可控）

**对 $\varepsilon_i$ 的分布假设，决定了 Loss 的形式**

## 2. 最大似然估计（MLE）怎么连到 Loss？

假设样本独立同分布（i.i.d.）：

$$
p(\mathbf y \mid \theta) = \prod_{i=1}^n p(y_i \mid \hat y_i) = \prod_{i=1}^n p(\varepsilon_i)
$$

最大似然估计：

$$
\max_\theta \prod_{i=1}^n p(\varepsilon_i)
$$

等价于：

$$
\min_\theta -\sum_{i=1}^n \log p(\varepsilon_i)
$$

**负对数似然 = Loss**

## 3. L2 Loss 高斯噪声

假设噪声是高斯分布

$$
\varepsilon_i \sim \mathcal N(0, \sigma^2)
$$

概率密度函数：

$$
p(\varepsilon_i)
= \frac{1}{\sqrt{2\pi\sigma^2}}
\exp!\left(-\frac{\varepsilon_i^2}{2\sigma^2}\right)
$$

写出似然函数

$$
p(y_i \mid \hat y_i)
= \frac{1}{\sqrt{2\pi\sigma^2}}
\exp!\left(-\frac{(y_i - \hat y_i)^2}{2\sigma^2}\right)
$$

负对数似然

$$
-\log p(y_i \mid \hat y_i) = \frac{(y_i - \hat y_i)^2}{2\sigma^2} + \frac{1}{2}\log(2\pi\sigma^2)
$$

对所有样本求和

$$
\sum_{i=1}^n -\log p(y_i \mid \hat y_i) = \frac{1}{2\sigma^2}\sum_{i=1}^n (y_i - \hat y_i)^2 + \text{常数}
$$

结论

- 常数项与 $\theta$ 无关 → 可忽略

- $\frac{1}{2\sigma^2}$ 只是缩放

等价于最小化：

$$
\sum_{i=1}^n (y_i - \hat y_i)^2
$$

**这就是 L2 / Squared Loss**

## 4. L1 Loss 拉普拉斯噪声

假设噪声服从拉普拉斯分布

$$
\varepsilon_i \sim \text{Laplace}(0, b)
$$

概率密度函数：

$$
p(\varepsilon_i) = \frac{1}{2b}\exp!\left(-\frac{|\varepsilon_i|}{b}\right)
$$

写成条件概率

$$
p(y_i \mid \hat y_i)
= \frac{1}{2b}
\exp!\left(-\frac{|y_i - \hat y_i|}{b}\right)
$$

负对数似然

$$
-\log p(y_i \mid \hat y_i)
= \frac{|y_i - \hat y_i|}{b}

* \log(2b)
$$

求和 + 忽略常数

$$
\sum_{i=1}^n |y_i - \hat y_i|
$$

**得到 L1 Loss**

## 5. 为什么“平方”与“绝对值”差别这么大？

### 直观对比

| 误差  | L1  | L2   |
| --- | --- | ---- |
| 0.1 | 0.1 | 0.01 |
| 1   | 1   | 1    |
| 10  | 10  | 100  |

L2 **极度厌恶大误差**

L1 **对异常值宽容**

### 概率分布形状

- 高斯：尾巴短 → 大误差“极不可能”

- 拉普拉斯：尾巴长 → 大误差“还能接受”

这就是它们的差异

## 6. 为什么 L2 Loss 流行？

- **光滑、处处可导**

- **梯度线性** $(\nabla = 2(y-\hat y))$

- **线性回归有解析解**

- **数值优化稳定**

- **符合高斯噪声的物理假设（测量误差）**

## 八、总结

- **L2 Loss = 假设误差是高斯噪声**

- **L1 Loss = 假设误差是拉普拉斯噪声**

- **Loss 是 概率建模的自然结果**

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
