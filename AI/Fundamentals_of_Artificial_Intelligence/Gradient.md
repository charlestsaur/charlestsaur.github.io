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

# Gradient

## 一、最直观的理解

**梯度 = 在某一点，函数“增长最快的方向”，以及这个最快增长的“速度”**

想象你在一座山上：

- 地面高度 = 函数值

- 站的位置 = 自变量取值

- 朝不同方向走，高度上升/下降速度不同

**梯度指向爬得最快的方向**

**梯度的长度表示“爬得有多快”**

## 二、从一元函数到多元函数

### 一元函数：导数

对一元函数

$$
f(x)
$$

导数：

$$
f'(x)
$$

含义：

- 正数：向右走，函数上升

- 负数：向右走，函数下降

- 绝对值越大：变化越快

**一元函数的导数，就是梯度的“低维版本”**

### 多元函数：偏导数

考虑二元函数：

$$
f(x, y)
$$

你有两个方向可以走：

- $x$ 方向

- $y$ 方向

于是定义：

$$
\frac{\partial f}{\partial x}, \quad \frac{\partial f}{\partial y}
$$

它们分别表示：

- 只在 $x$ 方向微小移动时，函数怎么变

- 只在 $y$ 方向微小移动时，函数怎么变

## 三、梯度的数学定义（核心）

### 梯度定义

对函数

$$
f(x_1, x_2, \dots, x_n)
$$

**梯度是一个向量**：

$$
\nabla f =
\begin{pmatrix}
\frac{\partial f}{\partial x_1} \\
\frac{\partial f}{\partial x_2} \\
\vdots \\
\frac{\partial f}{\partial x_n}
\end{pmatrix}
$$

也常写作：

$$
\mathrm{grad}, f
$$

### 二元函数例子

$$
f(x,y) = x^2 + y^2
$$

偏导：

$$
\frac{\partial f}{\partial x} = 2x, \quad
\frac{\partial f}{\partial y} = 2y
$$

梯度：

$$
\nabla f = (2x, 2y)
$$

## 四、几何意义

### 梯度方向

梯度向量：

- **垂直于等值线**

- 指向函数值**增大最快**的方向

例如：

$$
x^2 + y^2 = c
$$

是一个圆

在圆上任一点：

- 梯度指向圆心外（或内，取决于函数）

这也是为什么：

> 梯度 ⟂ 等高线（等值面）

### 梯度大小（模长）

$$
|\nabla f|
$$

表示：

- 函数在该点变化得有多“陡”

山越陡，梯度越大
山顶/谷底，梯度 = $0$

## 五、梯度与“方向导数”的关系

### 方向导数

沿单位方向向量 $\mathbf{u}$ 的变化率：

$$
D_{\mathbf{u}} f = \nabla f \cdot \mathbf{u}
$$

点积！

### 结论

- 最大方向导数 = $|\nabla f|$

- 取得最大值时的方向 = 梯度方向

**梯度 = 所有方向变化率的“总信息”**

## 六、典型例子（一步步）

### 例 1：平面函数

$$
f(x,y) = 3x + 4y
$$

梯度：

$$
\nabla f = (3, 4)
$$

含义：

- 函数在任何点变化率都一样

- 增长最快方向固定

- 增长最快速度 = $\sqrt{3^2 + 4^2} = 5$

### 例 2：非线性函数

$$
f(x,y) = x^2y
$$

$$
\nabla f = (2xy, x^2)
$$

在点 (1,2)：

$$
\nabla f(1,2) = (4,1)
$$

说明：

- 在这个点，往 (4,1) 方向走，上升最快

## 七、梯度为 0 的意义（极值点）

$$
\nabla f = \mathbf{0}
$$

意味着：

- 所有方向的一阶变化率都为 $0$

- 可能是：

  - 极大值

  - 极小值

  - 鞍点

这就是**多元函数极值的必要条件**

## 八、梯度在应用中的意义

### 梯度下降（机器学习核心）

为了最小化函数 $L(\theta)$：

$$
\theta \leftarrow \theta - \eta \nabla L(\theta)
$$

解释：

- 梯度指向“上升最快”

- 取负号 = “下降最快”

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
