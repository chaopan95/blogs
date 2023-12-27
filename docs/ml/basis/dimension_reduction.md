## 降维
降维，顾名思义，降低维度。因为数据集的维度较高，我们很难了解具体的分布，需要采用某些方法降低其维度。


## PCA
主成分分析（Principal Component Analysis，PCA）是一种利用矩阵分解对数据降维的方法。对 n 维数据降低到 k 维，需要找到 k 个特征向量 $u^{(1)}, u^{(2)}, ..., u^{(k)}$，原数据在上面的投影与其自身的 diff 最小。

### 算法
数据集 $x^{(1)}$, $x^{(2)}$, ..., $x^{(m)}$，预处理包括特征归一化（feature normalization / scaling，使不同特征具有可比性）。

$$
\begin{aligned}
& \mu_{j} = \frac{1}{m} \sum_{i=1}^{m} x_{j}^{(i)} \\
& x_{j}^{(i)} \Leftarrow x_{j} - \mu_{j}
\end{aligned}
$$

$$
\begin{aligned}
& x_{j}^{(i)} \leftarrow \frac{x_{j}^{(i)} - \mu_{j}}{s_{j}} \\
& s_{j} = \max(x_{j}) - \min(x_{j})
\end{aligned}
$$

计算协方差矩阵（covariance matrix）

$$
\Sigma = \frac{1}{m} \sum_{i=1}^{n} (x^{(i)})(x^{(i)})^{T}
$$

这里，$\Sigma$ 是一个 n-by-n 矩阵。

$$
U =
\begin{bmatrix}
| & | &   & | \\
u^{(1)} & u^{(2)} & \cdots & u^{(n)} \\
| & | &   & | 
\end{bmatrix} \in \mathbb{R}^{n \times n}
$$

得到 U 后，我们可以将 X 在 U 上投影。

$$
X \in R^{m \times n}, U \in R^{n \times n}, Z \in R^{m \times n}
$$
