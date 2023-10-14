## 用途
正则化（Regularization）是防止过拟合的方法之一。如果一个模型的参数较大的话，会引起模型过拟合，正则化驱使模型参数变小甚至成为0。

$$
J(w, b) = \frac{1}{m} \sum_{i=1}^{m} L(\hat{y^{i}}, y^{i}) + \lambda R(w), \quad \text{where } R(w) \text{ is a regularizor}
$$

### L1 norm
$\bigstar$ 让参数 w 变的系数，某些值变为 0

$\bigstar$ 不能微分

$$
\begin{aligned}
L_{reg}(w) &= L(w) + \lambda \left \| w \right \|_{1} \\
&= L(w) + \frac{\lambda}{2m} \sum_{j=1}^{m} \left | w_{j} \right | \\
&\rightarrow \min_{w}
\end{aligned}
$$


## L2 norm
$\bigstar$ 驱使参数靠近 0

$\bigstar$ 因为可微分，所以可以用梯度下降来优化

$\bigstar$ 增大正则系数，导致参数越靠近 0

$$
\frac{\lambda}{2m} \left \| w \right \|_{2}^{2} = \frac{\lambda}{2m} \sum_{j = 1}^{m} w_{j}^{2} = \frac{\lambda}{2m} w^{T}w
$$


## Frobenius norm

$$
\begin{aligned}
& J(W^{[1]}, b^{[1]}, ..., W^{[L]}, b^{[L]}) = \frac{1}{m} \sum_{i=1}^{m} L(\hat{y^{i}}, y^{i}) + \frac{\lambda}{2m} \sum_{l=1}^{L} \left \| W^{[l]} \right \|_{F}^{2} \\
& \left \| W^{[l]} \right \|_{F}^{2} = \sum_{i=1}^{n^{l}} \sum_{j=1}^{n^{l-1}} (W_{ij}^{[l]})^{2}
\end{aligned}
$$

## Dropout
$\bigstar$ 按一定的概率 p 保持神经元活跃，p = 0.8 意味着连接 2 个神经元的边有 20% 的概率被移除。增大 p 会减弱正则作用。

$\bigstar$ Inverted dropout

$\bigstar$ 测试集上不使用

```python
d3 = np.random.rand(a3.shape[0], a3.shape[1]) < keep_prob
a3 = np.multiply(a3, d3)  # a3 = a3 * d3
a3 = a3 / keep_prob
```
