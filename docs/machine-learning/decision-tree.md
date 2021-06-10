## ID3
$$
\begin{aligned}
& H(p) = -\sum_{i=1}^{N} p_{i} \log p_{i} \\
& H(D) = -\sum_{k=1}^{K} \frac{C_{k}}{D} \log \frac{C_{k}}{D} \\
& H(D \mid A) = \sum_{i=1}^{N} \frac{D_{i}}{D} H(D_{i}) = -\sum_{i=1}^{N} \frac{D_{i}}{D} \sum_{k=1}^{K} \frac{D_{ik}}{D_{i}} \log \frac{D_{ik}}{D_{i}} \\
& g(D, A) = H(D) - H(D \mid A)
\end{aligned}
$$

## C4.5

$$g_{R}(D, A) = \frac{H(D) - H(D \mid A)}{H(D)}$$

## CART
### 分类树
$$
\begin{aligned}
& \text{Gini}(p) = \sum_{k=1}^{K} p_{k} (1 - p_{k}) = 1 - \sum_{k=1}^{K} p_{k}^{2} \\
& \text{Gini}(p) = 2p(1-p) \\
& \text{Gini}(D) = 1 - \sum_{k=1}^{K} (\frac{C_{k}}{D})^{2} \\
& D_{1} = \{(x, y) \in D \mid A(x) = a\}, \quad D_{2} = D - D_{1} \\
& \text{Gini}(D, A) = \frac{D_{1}}{D} \text{Gini}(D_{1}) + \frac{D_{2}}{D} \text{Gini}(D_{2})
\end{aligned}
$$

### 决策树
