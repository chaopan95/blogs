## 简介
SVM 全称是支持向量机（support vector machine），是一种分类的算法，可以处理线形分类，也可以处理非线性分类。


## 原理
考虑一组数据集合

$$
T = \{ (x_{1}, y_{1}), (x_{2}, y_{2}), \cdots, (x_{N}, y_{N}) \}
$$

这里 $x_{i} \in R^{n}, y_{i} \in \{ +1, -1 \}, i = 1, 2, ..., N$

### 线形
如果我们能找到一个超平面，用来分隔所有的正负样本，就可以成为线形SVM。

$$w^{*}x + b^{*} = 0$$

$$f(x) = \text{sign}(w^{*}x + b^{*})$$

**函数间隔（Functional margin）** 指的是所有样本点与超平面的距离中最小值

$$
\begin{aligned}
& \hat{\gamma_{}} = \min_{i=1, 2, \cdots, N} \hat{\gamma_{i}} \\
& \hat{\gamma_{i}} = y_{i}(wx_{i}+b)
\end{aligned}
$$

**几何间隔（Geometric margin）** 指的是单位函数间隔，即 $\left \| w \right \| = 1$。

$$
\begin{aligned}
& \gamma = \min_{i=1, 2, \cdots, N} \gamma_{i} \\
& \gamma_{i} = y_{i}(\frac{w}{\left \| w \right \|}x_{i} + \frac{b}{\left \| w \right \|})
\end{aligned}
$$

函数间隔和几何间隔的关系如下

$$
\begin{aligned}
& \gamma_{i} = \frac{\hat{\gamma_{i}}}{\left \| w \right \|} \\
& \gamma = \frac{\hat{\gamma_{}}}{\left \| w \right \|}
\end{aligned}
$$

例如在一个2维平面里，超平面 $x_{1} + x_{2} = 0$ 实际上是一条线。假设有一个负样本 (2, 1)，那么间隔和距离分别如下

$$
\begin{aligned}
& w =
\begin{bmatrix}
1 \\
1
\end{bmatrix} \\
& \frac{w}{\left \| w \right \|} =
\begin{bmatrix}
\frac{\sqrt{2}}{2} \\
\frac{\sqrt{2}}{2}
\end{bmatrix}, \quad
b = 0 \\
& x =
\begin{bmatrix}
2 \\
1
\end{bmatrix} \\
& \text{Functional margin is } y(w^{T}x + b) = -3 \\
& \text{Geometric margin is } y(\frac{w^{T} x + b}{\left \| w \right \|}) = -\frac{3\sqrt{2}}{2}
\end{aligned}
$$

SVM 做的事情就是最大化几何间隔。

$$
\begin{aligned}
& \max_{w, b} \gamma \\
& \text{s.t.} \quad y_{i}(\frac{w}{\left \| w \right \|} \cdot x_{i} + \frac{b}{\left \| w \right \|}) \geq \gamma, \quad i = 1, 2, \cdots, N
\end{aligned}
$$

引入几何间隔与函数间隔的关系

$$
\begin{cases}
\max_{w, b}\frac{\hat{\gamma_{}}}{\left \| w \right \|} \\
\text{s.t.} \quad y_{i}(w \cdot x_{i}+b) \geq \hat{\gamma_{}}, \quad i = 1, 2, \cdots, N 
\end{cases}
$$

简单起见，我们令 $\hat{\gamma} = 1$，因为这不会影响最后的解。

$$
y_{i}(w \cdot x_{i}+b) \geq \hat{\gamma} \Leftrightarrow y_{i}(\lambda w \cdot x_{i} + \lambda b) \geq \lambda \hat{\gamma}
$$

$$
\max_{w, b}\frac{1}{\left \| w \right \|} \Leftrightarrow \min_{w, b}\frac{1}{2}\left \| w \right \|^{2}
$$

$$
\begin{cases}
\min_{w, b} \frac{1}{2} \left \| w \right \|^{2} & \quad \text{(1)}\\
\text{s.t.} \quad y_{i} (w \cdot x_{i} + b) -1 \geq 0, \quad i = 1, 2, \cdots, N & \quad \text{(2)}
\end{cases}
$$

这是一个凸优化问题，最优解 $w^{*}, b^{*}$ 代表一个超平面。如果数据集是线形可分，那么超平面是唯一的。证明如下：

**存在性**：我们总可以找到一个 $\left \| w \right \|^{2}$，其有一个下界。

**唯一性**：假设我们有两个最优解（$w_{1}^{*}$, $b_{1}^{*}$）和（$w_{2}^{*}$, $b_{2}^{*}$）

$$
\begin{aligned}
& \frac{1}{2} \left \| w_{1}^{*} \right \|^{2} = \frac{1}{2} \left \| w_{2}^{*} \right \|^{2} \rightarrow \left \| w_{1} \right \| = \left \| w_{2} \right \| = c, \quad c \text{ is a some scalar value} \\
\because \quad &  (w = \frac{w_{1}^{*} + w_{2}^{*}}{2}, \quad b = \frac{b_{1}^{*} + b_{2}^{*}}{2}) \text{ is also a feasible solution} \\
\therefore \quad & c \leq \left \| w \right \| = \left \| \frac{w_{1}^{*} + w_{2}^{*}}{2} \right \| \leq \frac{1}{2} \left \| w_{1}^{*} \right \| + \frac{1}{2} \left \| w_{2}^{*} \right \| = c \\
\therefore \quad & \left \| \frac{w_{1}^{*} + w_{2}^{*}}{2} \right \| \leq \frac{1}{2} \left \| w_{1}^{*} \right \| + \frac{1}{2} \left \| w_{2}^{*} \right \| \\
\therefore \quad & w_{1}^{*} = \lambda w_{2}^{*} \text{ (parallel)} \\
\because \quad & \left \| w_{1}^{*} \right \| = \left \| w_{2}^{*} \right \| \\
\therefore \quad & \left \| \lambda \right \| = 1
\end{aligned}
$$

$\lambda$ = -1, $w = \overrightarrow{\mathbf{0}}$ 是不可能的，因为 $\left \| w \right \| = 0$ 不是一个 $(1) \sim (2)$ 的可行解，因此， $\lambda$ = 1，进而 $w_{1}^{*} = w_{2}^{*}$

证明 $b_{1}^{*} = b_{2}^{*}$

$$
\begin{aligned}
& \quad \text{consider a positve and a negative instance } x_{1}^{+}, x_{1}^{-} \in w^{*} + b_{1}^{*} = \overrightarrow{\mathbf{0}} \\
& \quad \text{another positve negative instance } x_{2}^{+}, x_{2}^{-} \in w^{*} + b_{2}^{*} = \overrightarrow{\mathbf{0}} \\
\therefore & \quad
\begin{cases}
w^{*} \cdot x_{1}^{+} + b_{1}^{*} = 1 \\
w^{*} \cdot x_{1}^{-} + b_{1}^{*}  = -1 \\
\end{cases} \rightarrow
b_{1}^{*} = -\frac{1}{2} (w^{*} \cdot x_{1}^{+} + w^{*} \cdot x_{1}^{-}) \\
& \quad
\begin{cases}
w^{*} \cdot x_{2}^{+} + b_{2}^{*} = 1 \\
w^{*} \cdot x_{2}^{-} + b_{2}^{*}  = -1 \\
\end{cases} \rightarrow
b_{2}^{*} = -\frac{1}{2} (w^{*} \cdot x_{2}^{+} + w^{*} \cdot x_{2}^{-}) \\
\therefore & \quad b_{1}^{*} - b_{2}^{*} = -\frac{1}{2} [w^{*} (x_{1}^{+} - x_{2}^{+}) + w^{*} (x_{1}^{-} - x_{2}^{-})] \\
\because & \quad \text{instances } x_{1}^{+}, x_{1}^{-} \text{ also satisfies hyperplane } w^{*} + b_{2}^{*} = \overrightarrow{\mathbf{0}} \text{ and} \\
& \quad \text{instance } x_{2}^{+}, x_{2}^{-} \text{ also satisfies hyperplane } w^{*} + b_{1}^{*} = \overrightarrow{\mathbf{0}} \\
\therefore & \quad
\begin{cases}
(+1) (w^{*} \cdot x_{2}^{+} + b_{1}^{*}) \geq 1 = w^{*} \cdot x_{1}^{+} + b_{1}^{*} \\
(+1) (w^{*} \cdot x_{1}^{+} + b_{2}^{*}) \geq 1 = w^{*} \cdot x_{2}^{+} + b_{2}^{*}
\end{cases} \\
& \quad \rightarrow w^{*} \cdot x_{2}^{+} + b_{1}^{*} \geq w^{*} \cdot x_{1}^{+} + b_{1}^{*} \geq w^{*} \cdot x_{2}^{+} + b_{2}^{*} \\
& \quad
\begin{cases}
(-1) (w^{*} \cdot x_{2}^{-} + b_{1}^{*}) \geq 1 = (-1) (w^{*} \cdot x_{1}^{-} + b_{1}^{*}) \\
(-1) (w^{*} \cdot x_{1}^{-} + b_{2}^{*}) \geq 1 = (-1) (w^{*} \cdot x_{2}^{-} + b_{2}^{*})
\end{cases} \\
& \quad \rightarrow w^{*} \cdot x_{2}^{-} + b_{1}^{*} \leq w^{*} \cdot x_{1}^{-} + b_{1}^{*} \leq w^{*} \cdot x_{2}^{-} + b_{2}^{*} \\
\therefore & \quad
\begin{cases}
x_{1}^{+} = x_{2}^{+} \\
x_{1}^{-} = x_{2}^{-}
\end{cases} \\
\therefore & \quad
b_{1}^{*} - b_{2}^{*} = -\frac{1}{2} [w^{*} (x_{1}^{+} - x_{2}^{+}) + w^{*} (x_{1}^{-} - x_{2}^{-})] = 0 \\
\end{aligned}
$$

综上，最优解存在且唯一。


**支持向量**（support vector）指的是决定超平的那些实例（即有正样本也有负样本）。

$$
(y_{i}) (w^{*} \cdot x_{i} + b^{*}) = 1
$$

因此我们有两个平行超平面 $H^{+}$ 和 $H^{-}$

$$
\begin{cases}
H^{+}: \quad w^{*} \cdot x^{+} + b^{*} = 1 \\
H^{-}: \quad w^{*} \cdot x^{-} + b^{*} = -1
\end{cases}
$$

**间隔**（Margin）指的是 $H^{+}$ 和 $H^{-}$ 之间的距离

$$
d_{H^{+}, H^{-}} = \frac{\left | (b^{*} - 1) - (b^{*} + 1) \right |}{\left | w^{*} \right |} = \frac{2}{\left | w^{*} \right |}
$$


### 非线形SVM


### SMO
