## 定义
感知机是一种分类模型。输入空间是 $X = \{x_{1}, x_{2}, ..., x_{n}\}$，输出空间是 $Y = \{+1 , -1\}$。

判别函数

$$
\begin{aligned}
f(x_{i}) &= \text{sign}(w \cdot x_{i} + b) \\
&=
\begin{cases}
1, \quad w \cdot x_{i} + b \geq 0 \\
-1, \quad \text{else}
\end{cases}
\end{aligned}
$$

$W \cdot X$ 代表的是点乘

## 算法
数据集 T = $\{(x_{1}, y_{1}), (x_{2}, y_{2}), ..., (x_{n}, y_{n})\}$，假设一个数据点 ($x_{i}$, $y_{i}$) 被误分类了。可以用如下方法表示：

$$
y_{i} \times f(x_{i}) = y_{i} \times \text{sign}(x_{i}) = -1
$$

所有被误分类的点构成集合 M，损失函数为

$$
L(w, b) = -\sum_{(x_{i}, y_{i}) \in M} y_{i}(w \cdot x_{i} + b)
$$

参数梯度

$$
\begin{aligned}
& \frac{\partial L(w, b)}{\partial w} = -\sum_{(x_{i}, y_{i}) \in M} y_{i} x_{i} \\
& \frac{\partial L(w, b)}{\partial b} = -\sum_{(x_{i}, y_{i}) \in M} y_{i}
\end{aligned}
$$

反向传播，学习率 $\eta \in (0, 1]$

$$
\begin{matrix}
& & 
\begin{aligned}
& w := w - \eta \frac{\partial L(w, b)}{\partial w} \\
& b := b - \eta \frac{\partial L(w, b)}{\partial b}
\end{aligned}
& & \\
& & \Downarrow & & \\
& & 
\begin{aligned}
& w := w - \eta (-y_{i} x_{i}) \\
& b := b - \eta (-y_{i})
\end{aligned}
& & \\
& & \Downarrow & & \\
& & 
\begin{aligned}
& w := w + \eta y_{i} x_{i} \\
& b := b + \eta y_{i}
\end{aligned}
& & 
\end{matrix}
$$

**伪代码**
```bash
Input: dataset T = $\{(x_{1}, y_{1}), (x_{2}, y_{2}), ..., (x_{n}, y_{n})\}$
Output: $w^{*}$, $b^{*}$
Initialise w, b with 0
While True do:
  Calculate misclassified set M
    If $M = \varnothing$:
      break
      Pick up one point form M, update w, b
return w, b
```


```python
import numpy as np

def sign(x):
    res = x.copy()
    idx = x >= 0
    res[:] = -1
    res[idx] = 1
    return res

def Perception(T, eta=0.1):
    X = T[:, :-1]
    Y = T[:, -1]
    n, m = X.shape
    w = np.zeros(m)
    b = 0
    while True:
        y_hat = sign(np.dot(X, w) + b)
        M = np.nonzero(Y != y_hat)[0]
        if len(M) == 0: break
        pickedUp = np.random.choice(M, 1)
        w = w + eta * Y[pickedUp] * X[pickedUp, :][0]
        b = b + eta * Y[pickedUp]
    return w, b

T = [[3, 3, 1],
     [4, 3, 1],
     [1, 1, -1]]
T = np.array(T)
w, b = Perception(T)
```

**Novikoff定理**
假设数据集 T 是线形可分的，

（1）存在一个超平面 $w^{*} \cdot x + b^{*} = 0$，使得正确区分所有的正负样本，即对所有的实例 $(x_{i}, y_{i}), i = 1, 2, ..., n$，存在 $\gamma > 0$ 使得

$$
y_{i} (w^{*} \cdot x_{i} + b^{*}) \geq \gamma
$$

（2）
(ii) 令 $R = \max_{i = 1, 2, ..., n} \left \| x_{i} \right \|$, 误分类的个数 k 满足

$$
k \leq (\frac{R}{\gamma})^{2}
$$

**证明**
1）任意一个实例 $x_{i}$ 能够被正确分类，必须满足

$$
y_{i} (w^{*} \cdot x + b^{*}) > 0
$$

因此，我们总能找到一个 $\gamma$ 满足（1）

$$
\gamma = \min_{i = 1, 2, ..., n} y_{i} (w^{*} \cdot x_{i} + b^{*})
$$

2）假设在第k次迭代里，我们的参数更新过程如下

$$
W_{k} =
\begin{bmatrix}
w_{k} \\
b_{k} 
\end{bmatrix} =
\begin{bmatrix}
w_{k-1} + \eta y_{i} x_{i} \\
b_{k-1} + \eta y_{i}
\end{bmatrix} =
\begin{bmatrix}
w_{k-1} \\
b_{k-1}
\end{bmatrix} + \eta y_{i}
\begin{bmatrix}
x_{i} \\
1
\end{bmatrix}
$$

两个不等式

$$
\begin{aligned}
& \begin{aligned}
w_{k} \cdot w^{*} & = (w_{k} + \eta y_{i} x_{i}) \cdot w^{*} \\
& \geq w_{k-1} \cdot w^{*} + \eta \gamma \\
& \geq w_{k-2} \cdot w^{*} + 2 \eta \gamma \\
& \geq k \eta \gamma
\end{aligned} \\
& \begin{aligned}
\left \| w_{k} \right \|^{2} & = (w_{k-1} + \eta y_{i} x_{i})^{2} \\
& = \left \| w_{k-1} \right \|^{2} + 2 w_{k-1} \eta y_{i} x_{i} + \eta^{2} \left \| x_{i} \right \|^{2} \\
& \leq \left \| w_{k-1} \right \|^{2} + \eta^{2} \left \| x_{i} \right \|^{2} \\
& \leq \left \| w_{k-1} \right \|^{2} + \eta^{2} R^{2} \\
& \leq k \eta^{2} \left \| x_{i} \right \|^{2} \\
& \leq k \eta^{2} R^{2}
\end{aligned}
\end{aligned}
$$

$$
\begin{matrix}
& k \eta \gamma \leq w^{k} \cdot w^{k} \leq \left \| w_{k} \right \| \left \| w^{*} \right \| \leq \sqrt{k} \eta R & \\
& \Downarrow & \\
& k \leq (\frac{R}{\gamma})^{2} &
\end{matrix}
$$


## 评价
* `优点`：容易实现
* `缺点`：不能处理非线性分裂问题，如异或XOR
