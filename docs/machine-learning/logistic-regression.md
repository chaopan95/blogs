## 基础
特征空间 $x \in R^{n}$，预测值：

$$
\hat{y_{}} = P(y = 1 \mid x) \rightarrow \hat{y_{}} \in [0, 1]
$$

需要学习的参数：$w \in R^{n}, b \in R$，预估值

$$
\begin{aligned}
\hat{y_{}} &= \sigma(w^{T}x + b) \\
&= \frac{1}{1 + e^{-(w^{T}x + b)}}
\end{aligned}
$$


## 损失函数
平方差损失函数不是一个好的选择，因为它不是凸函数，容易陷入极小值陷阱。

$$
L(\hat{y_{}}, y) = \frac{1}{2} (\hat{y_{}} - y)^{2}
$$

逻辑回归的损失函数：**交叉熵**

$$
L(\hat{y_{}}, y) = -[y \log\hat{y_{}} + (1-y)\log(1-\hat{y_{}})]
$$

直观地，当 y = 1 时，我们会让 $\hat{y_{i}}$ 大一点从而获得一个较小的损失；当 y = 0 时，我们让  $\hat{y_{i}}$ 小一点。

推广到全样本 {($x^{1}$, $y^{1}$), ..., ($x^{m}$, $y^{m}$)}, 我们希望 $\hat{y^{i}} \approx y^{i}$

$$
\begin{aligned}
J(w, b) &= \frac{1}{m} \sum_{i=1}^{m} L(\hat{y^{i}}, y^{i}) \\
&= -\frac{1}{m} \sum_{i=1}^{m} [y^{i} \log\hat{y^{i}} + (1-y^{i}) \log(1-\hat{y^{i}})] \\
& \Rightarrow \arg\min_{w, b} J(w, b)
\end{aligned}
$$


## 梯度下降

$$
\begin{aligned}
& z = w^{T}x + b \\
& \hat{y_{}} = a = \sigma(z) \\
& L(a, y) = -[y \log a + (1-y) \log(1-a)] \\
& 
\begin{aligned}
\frac{\partial L(a, y)}{\partial z} &= - [y \frac{1}{a} - (1- y)\frac{1}{1-a}] \frac{\partial a}{\partial z}  \\
&= - [y \frac{1}{a} - (1- y)\frac{1}{1-a}] a(1-a) \\
&= a-y
\end{aligned}
\end{aligned}
$$

$$
\begin{aligned}
& w := w - \alpha \frac{\partial J(w, b)}{\partial w} \\
& b := b - \alpha \frac{\partial J(w, b)}{\partial b}
\end{aligned}
$$


## 矩阵化
考虑 m 条训练样本，每条样本有 n 个特征。用矩阵表示，X 的尺寸是 $n \times m$，W 的尺寸 $n \times 1$，Y 的尺寸 $1 \times m$，b 是一个标量。

$$
\begin{aligned}
& X =
\begin{bmatrix}
x_{11} & \cdots  & x_{1m}\\
\vdots  & \ddots  & \vdots\\
x_{n1} & \cdots & x_{nm}
\end{bmatrix}, \quad Y = 
\begin{bmatrix}
y_{11} & \cdots & y_{1m}
\end{bmatrix} \\
& W = 
\begin{bmatrix}
w_{11} \\
\vdots \\
w_{n1}
\end{bmatrix}, \quad b = \begin{bmatrix} R \end{bmatrix} \\
& Z = W^{T}X + b \\
& A = \sigma(Z) \\
& dZ = A - Y \\
& dW = \frac{1}{m} X (dZ)^{T} \\
& db = \frac{1}{m} \sum_{i=1}^{m}dZ
\end{aligned}
$$

假设 10 条样本，每条样本 5 个特征，X 总共有 10*(5+1)=60 个值。


## 正则化
损失函数

$$
J(\theta) = -\frac{1}{m} [\sum_{i=1}^{m} y^{i} \log(h_{\theta}(x^{i})) + (1 - y^{i}) \log(1 - h_{\theta}(x^{i}))] + \frac{\lambda}{2m} \sum_{j=1}^{m} \theta_{j}^{2}
$$

梯度下降

$$
\begin{aligned}
& \frac{\partial J(\theta)}{\partial \theta_{j}} = -\frac{1}{m} \sum_{i}^{m} x^{i}(y^{i} - h_{\theta}(x^{i})) + \frac{\lambda}{m} \theta_{j} \\
& i = 1, ..., m \\
& j = 0, 1, .., n \\
&x_{0}^{i} = 1
\end{aligned}
$$
