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

# Metric Balls and Topologies in $\mathbb{R}^n$: From $d'$ to $\rho$

[< Index >](/index.md)

---

$(a)$ Define in $\mathbb{R} ^{n}$

$$d'(x,y) = \vert x_1 - y_1 \vert + \ldots + \vert x_n - y_n \vert$$

Show that $d'$ is a metric that induces the usual topology of $\mathbb{R} ^{n}$. Draw the basis elements of $d'$ for $n = 2$.

(b) More generally, for $p \geq 1$ and $x,y \in \mathbb{R} ^{n}$, define

$$d'(x,y) = \Bigg[ \sum _{i=1} ^{n} \vert x _{i} - y _{i} \vert ^{p} \Bigg] ^{ \dfrac{1}{p} }$$

Definition of the $\rho$ square metric in (a)

$$\rho (x,y) = max \{ \vert x_1 - y_1 \vert , \ldots , \vert x_2 - y_2 \vert \}$$

We can get

$$\rho (x,y) \leq d'(x,y) \leq n \rho (x,y)$$

The first inequality implies that $B_{d'} (x,\varepsilon) \subset B_{\rho} (x,\varepsilon)$ holds for all $x$ and $\varepsilon$.

This is because if $d'(x,y) < \varepsilon$, then $\rho (x,y) < \varepsilon$, so for any $y \in B_{d'} (x,\varepsilon)$, there must be $y \in B_{\rho} (x,\varepsilon)$.

Similarly, the second inequality implies that $B_{ \rho } (x, \dfrac{\varepsilon}{n} ) \subset B_{ d' } (x,\varepsilon)$ holds for all $x$ and $\varepsilon$.

The metric topology induced by $d'$ is the same as the metric topology induced by $\rho$, and this metric topology is the usual topology of $\mathbb{R} ^{n}$.

The following figure shows a basis element $B _{d'}\Big((0,0),1 \Big)$ under $d'$:


[< Index >](/index.md)