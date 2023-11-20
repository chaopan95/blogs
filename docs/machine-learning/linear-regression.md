## 定义
不同于分类问题，回归问题的 label 是一个连续值。线形回归则是用一次多变量的线形叠加模型 label。形如

$$
\hat{y} = \sum_{i = 0}^{m} a_{i}x_{i}
$$

## 解析解

$$
\begin{aligned}
(\frac{1}{n}\left \| Xw-y \right \|^{2})^{'} &= \frac{2}{n}X^{T}(Xw-y) \\
&= \frac{2}{n}(X^{T}Xw-X^{T}y) = 0 \\
w &= (X^{T}X)^{-1}X^{T}y
\end{aligned}
$$

$\bigstar$ 解析解需要对矩阵求逆，当维度较高时，成本大


## 数值解
损失函数：MSE (mean square error)

$$
\begin{aligned}
L(w) &= \frac{1}{n}\sum_{i=1}^{n}(w^{T}x_{i}-y_{i})^{2} \\
&= \frac{1}{n}\left \| Xw-y \right \|^{2} \\
&\rightarrow \min_{w}
\end{aligned}
$$


## 正则化
损失函数

$$
J(\theta) = \frac{1}{2m} [\sum_{i=1}^{m} (h_{\theta} (x^{i}) - y^{i})^{2} + \lambda \sum_{j=1}^{n} \theta_{j}^{2}]
$$

梯度下降

$$
\frac{\partial J(\theta)}{\partial \theta_{j}} = \frac{1}{m}\sum_{i=1}^{m} (h_{\theta} (x^{i}) - y^{i}) x_{j}^{i} + \frac{\lambda}{m} \theta_{j}, \quad i = 1, ..., m, \quad j = 0, 1, .., n, \quad x_{0}^{i} = 1
$$

解析解

$$
\begin{aligned}
J(\theta) & = (X\theta - Y)^{T} (X\theta - Y) + \lambda \theta^{T} \theta \\
& = ( \theta^{T} X^{T} - Y^{T}) (X\theta - Y) + \lambda \theta^{T} \theta \\
& = \theta^{T} X^{T} X \theta - \theta^{T} X^{T} Y - Y^{T} X \theta + Y^{T}Y + \lambda \theta^{T} \theta
\end{aligned}
$$

$$
\begin{aligned}
\frac{\partial J(\theta)}{\partial \theta} & = \frac{\theta^{T} X^{T} X \theta}{\partial \theta} - \frac{Y^{T} X \theta}{\partial \theta} - \frac{\partial \theta^{T} X^{T} Y}{\partial \theta} + \lambda \frac{\partial \theta^{T} \theta}{\partial \theta} \\
& = (X^{T}X + (X^{T}X)^{T}) \theta - X^{T}Y - X^{T}Y + 2 \lambda \theta \\
& = 2 X^{T} X \theta - 2 X^{T} Y + 2\lambda \theta = 0
\end{aligned}
$$

不对偏置项惩罚

$$
\theta = (X^{T}X + \lambda E)^{-1} X^{T} Y, \quad \text{where } E =
\begin{bmatrix}
0 &  &  &  &  & \\
   & 1 &  & &  & \\
   &  & \ddots &  &  & \\
   &  & & &  1  & \\
   &  &  &  &  & 1
\end{bmatrix}_{n \times n}
$$
