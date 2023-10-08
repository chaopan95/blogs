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
