## 必要性
首先要明确，一系列的线性变化的结果依旧是线性变换，这不会有助于神经网络表达能力的提高。因此，引入非线形变化是必要的。通常的方法是加入激活函数。

## ReLU

$$
\begin{aligned}
&
g(z) =
\begin{cases}
  z, \quad z > 0 \\
  0, \quad \text{otherwise}
\end{cases} \\
&
g(z)' =
\begin{cases}
  1, \quad z > 0 \\
  0, \quad \text{otherwise}
\end{cases}
\end{aligned}
$$

$\bigstar$ 便于计算，实际情况下能快速收敛

$\bigstar$ x > 0 时 梯度不会爆炸也不会消失；x < 0 时，无梯度更新

$\bigstar$ 不是 0 中心对称


## Leaky ReLU

$$
\begin{aligned}
f(x) &=
\begin{cases}
x, \quad & x \geq 0 \\
ax, \quad & x < 0
\end{cases} \\
&= \max(ax, x), \quad \text{where } a \in (0, 1)
\end{aligned}
$$

$\bigstar$ 不会出现梯度为0的情况

$\bigstar$ $a \neq 1$, 因为 a = 1 会失去激活函数的作用


## Sigmoid

$$
\begin{aligned}
& \text{Sigmoid}(Z) = \pi(Z) = \frac{1}{1 + e^{-Z}} \\
& \frac{\partial \pi(Z)}{\partial Z} = \pi(Z) (1 - \pi(Z)) \leq \frac{1}{4}
\end{aligned}
$$

$\bigstar$ 二分累问题的输出层，用作隐藏层激活函数容易导致

$\bigstar$ 非 0 中心

$\bigstar$ $e^{x}$ 计算成本大


## Tanh

$$
\begin{aligned}
& \text{tanh}(x) = g(x) = \frac{2}{1 + e^{-2x}} - 1 = \frac{e^{x} - e^{-x}}{e^{x} + e^{-x}} \\
& \frac{\partial}{\partial x} \text{tanh}(x) = \frac{\partial}{\partial x} g(x) = 4 \frac{e^{-2x}}{(1 + e^{-2x})^{2}} = 1 - (g(x))^{2} \leq 1
\end{aligned}
$$

$\bigstar$ 0 中心有助于数值计算，双边饱和导致梯度消失。


## Softmax

$$
\begin{aligned}
& Z = (Z_{1}, Z_{2}, ..., Z_{K}) = (w_{1}^{T}x, w_{2}^{T}x, ..., w_{K}^{T}x) \\
& \text{Softmax}(Z) = (\frac{e^{Z_{1}}}{\sum_{k=1}^{K}e^{Z_{k}}}, \frac{e^{Z_{2}}}{\sum_{k=1}^{K}e^{Z_{k}}},..., \frac{e^{Z_{K}}}{\sum_{k=1}^{K}e^{Z_{k}}}) \\
& \text{e.g. } Z = (7, -7.5, 10) \rightarrow \text{Softmax}(Z) \approx (0.05, 0, 0.95)
\end{aligned}
$$

$\bigstar$ 交叉熵作为分类问题的损失函数容易微分

$$
\begin{aligned}
J(w,b) &= -\sum_{i=1}^{m} \sum_{k=1}^{K} \mathbf{I}_{y_{i}=k} \log \frac{e^{Z_{i, k}}}{\sum_{j=1}^{K} e^{Z_{i, j}} } \\
&= -\sum_{i=1}^{m} \sum_{k=1}^{K} \mathbf{I}_{y_{i}=k} \log \frac{e^{w_{k}^{T}x_{i}}}{\sum_{j=1}^{K}e^{w_j^{T}x_{j}}} \\
&= -\sum_{i=1}^{m} \log\frac{e^{w_{y_{i}}^{T}x_{i}}}{\sum_{j=1}^{K}e^{w_j^{T}x_{j}}} \\
&= -\sum_{i=1}^{m} (w_{y_{i}}^{T}x_{i} - \log \sum_{j=1}^{K}e^{w_j^{T}x_{j}}) \\
&= -\sum_{i=1}^{m} (Z_{i, y_{i}} - \log \sum_{j=1}^{K} e^{Z_{i, j}}) \\
&\rightarrow \min_{w,b}
\end{aligned}
$$

$\bigstar$ Log-softmax 好于 naive log(softmax(a))，因为 1）数值计算上稳定，2）容易微分，3）容易求 margin
