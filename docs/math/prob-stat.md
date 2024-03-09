## 概率
### 联合概率
联合概率（Joint probability）

$$
\begin{aligned}
P(X = x \text{ and } Y = y) &= P(X = x \mid Y = y) P(y = y) \\
&= P(Y = y \mid X = x) P(X = x)
\end{aligned}
$$

满足

$$
\begin{aligned}
& \sum_{x} \sum_{y} P(X = x \text{ and } Y = y) = 1 \\
& \int_{x} \int_{y} f_{X, Y} (x, y) dy dx = 1
\end{aligned}
$$

### 边际概率
边际概率（Marginal Probability）

$$
\begin{aligned}
& P(X = x) = \sum_{Y} P(X = a, Y = b) \\
& P(Y = b) = \sum_{X} P(X = a, Y = b)
\end{aligned}
$$

### 条件概率
条件概率（Conditional probability）

$$
\begin{aligned}
& \sum_{x} P(X = a \mid Y = b) = 1 \\
& P(X = a \mid Y = b) = \frac{P(X = a, Y = b)}{P(Y = b)}
\end{aligned}
$$

### 期望
期望（Expect）

$$
\begin{aligned}
& \text{E}(X, Y) = \sum_{i} x_{i} p(x_{i}), \quad i = 1, 2, \cdots, n \\
& \text{E}[f] = \sum_{i} f(x_{i}) p(x_{i}, y_{i}), \quad i = 1, 2, \cdots n
\end{aligned}
$$

### 方差
方差（Variance）

$$
\text{Var}(f(x)) = \text{E}[(f(x) - \text{E}[f(x)])^{2}]
$$

两个独立的标准正态分布 $X \sim (0, 1)$ $Y \sim (0, 1)$，两者的乘积也是服从标准正态分布

$$
E[XY] = E[x] * E[Y] = 0 * 0 = 0
$$

$$
\begin{aligned}
D[XY] &= E[(XY)^{2}] - (E[XY])^{2} \\
&= E[X^{2}Y^{2}] - (E[X]E[Y])^{2} \\
&= E[X^{2}]E[Y^{2}] - (E[X])^{2}(E[Y])^{2} \\
&= (E[X^{2}] - (E[X])^{2})(E[Y^{2}] - (E[Y])^{2}) - 0 \\
&= D[X]D[Y] \\
&= 1
\end{aligned}
$$

两个正态分布的之和也是服从正态分布 $N(\mu_{1} + \mu_{2}, \sigma_{1}^{2} + \sigma_{2}^{2})$

## 样本方差
设样本均值（sample mean）是 $\bar{X}$, 样本方差（sample variance）是 $S^{2}$, 总体均值（ensemble average）是 $\mu$, 总体方差（ensemble variance）是 $\sigma^{2}$

$$
\mathrm{S^{2} = \frac{1}{n-1} \sum_{i=1}^{n}(x_{i} - \bar{X})^{2}}
$$

## 无偏估计
假设我们想知道一个大学的学生身高，调查每一个学生不太现实（但是准确的），于是我们可以随机挑选100位同学，求一个均值。为了获得一个精确的估计，我们重复实验，然后计算样本均值，$\bar{X}_{2}$, $\bar{X}_{3}$, $\cdots$, $\bar{X}_{k}$。这里 $\bar{X}$ 就称为一个随机变量，$\bar{X}_{i}$ 是其可能的一个值。

多次重复实验下，期望趋向于真值。样本方差如下：

$$
S^{2} = \frac{1}{n} \sum_{i=1}^{n}(x_{i} - \bar{X})^{2}
$$

有

$$
\begin{aligned}
E(S^{2}) & = E(\frac{1}{n} \sum_{i=1}^{n}(x_{i} - \bar{X})^{2}) \\
&  = E(\frac{1}{n} \sum_{i=1}^{n}((x_{i} - \mu) - (\bar{X} - \mu))^{2}) \\
& = E(\frac{1}{n} \sum_{i=1}^{n}((x_{i} - \mu)^{2} - 2(x_{i} - \mu)(\bar{X} - \mu) + (\bar{X} - \mu)^{2})) \\
& = E(\frac{1}{n}\sum_{i=1}^{n}(x_{i} - \mu)^{2} - \frac{1}{n}\sum_{i=1}^{n}2(x_{i} - \mu)(\bar{X} - \mu) + \frac{1}{n}\sum_{i=1}^{n}(\bar{X} - \mu)^{2}) \\
& = E(\frac{1}{n}\sum_{i=1}^{n}(x_{i} - \mu)^{2} - 2(\bar{X} - \mu)(\bar{X}- \mu) + \frac{1}{n}\sum_{i=1}^{n}(\bar{X} - \mu)^{2}) \\
& = E(\frac{1}{n}\sum_{i=1}^{n}(x_{i} - \mu)^{2} - 2(\bar{X} - \mu)(\bar{X}- \mu) + (\bar{X} - \mu)^{2}) \\
& = E(\frac{1}{n}\sum_{i=1}^{n}(x_{i} - \mu)^{2} - (\bar{X} - \mu)^{2}) \\
& = E(\frac{1}{n}\sum_{i=1}^{n}(x_{i} - \mu)^{2}) - E((\bar{X} - \mu)^{2}) \\
& = Var(X) - Var(\bar{X}) \\
& = \sigma^{2} - \frac{1}{n}\sigma^{2} \\
& = \frac{n-1}{n}\sigma^{2} \leq \sigma^{2}
\end{aligned}
$$

于是

$$
E(S^{2}) = \frac{n-1}{n}\sigma^{2}
$$

如果我们处以n，样本方差会比总体方差要小，因此需要乘一个 $S^{2}$ by $\frac{n}{n-1}$ 得到无偏样本方差

$$
S^{2} = \frac{n}{n-1}(\frac{1}{n}\sum_{i=1}^{n}(x_{i} - \bar{X})^{2}) = \frac{1}{n-1}\sum_{i=1}^{n}(x_{i} - \bar{X})^{2}
$$

因此，无偏估计（Unbiased Estimate）

$$
\begin{aligned}
E(S^{2}) & = E(\frac{1}{n-1} \sum_{i=1}^{n}(x_{i} - \bar{X})^{2}) = E(\frac{1}{n-1} \sum_{i=1}^{n}((x_{i} - \mu) - (\bar{X} - \mu))^{2}) \\
& = E(\frac{1}{n-1} \sum_{i=1}^{n}((x_{i} - \mu)^{2} - 2(x_{i} - \mu)(\bar{X} - \mu) + (\bar{X} - \mu)^{2})) \\
& = E(\frac{1}{n-1}\sum_{i=1}^{n}(x_{i} - \mu)^{2} - \frac{1}{n-1}\sum_{i=1}^{n}2(x_{i} - \mu)(\bar{X} - \mu) + \frac{1}{n-1}\sum_{i=1}^{n}(\bar{X} - \mu)^{2}) \\
& = E(\frac{1}{n-1}\sum_{i=1}^{n}(x_{i} - \mu)^{2} - \frac{2n}{n-1}(\bar{X} - \mu)(\bar{X}- \mu) + \frac{1}{n-1}\sum_{i=1}^{n}(\bar{X} - \mu)^{2}) \\
& = E(\frac{1}{n-1}\sum_{i=1}^{n}(x_{i} - \mu)^{2} - \frac{2n}{n-1}(\bar{X} - \mu)(\bar{X}- \mu) + \frac{n}{n-1}(\bar{X} - \mu)^{2}) \\
& = E(\frac{1}{n-1}\sum_{i=1}^{n}(x_{i} - \mu)^{2} - \frac{n}{n-1}(\bar{X} - \mu)^{2}) \\
& = E(\frac{1}{n-1}\sum_{i=1}^{n}(x_{i} - \mu)^{2}) - E(\frac{n}{n-1}(\bar{X} - \mu)^{2}) \\
& = \frac{n}{n-1}E(\frac{1}{n}\sum_{i=1}^{n}(x_{i} - \mu)^{2}) - \frac{n}{n-1}E((\bar{X} - \mu)^{2}) \\
& = \frac{n}{n-1}\sigma^{2} - \frac{n}{n-1}\frac{\sigma^{2}}{n} \\
& = \sigma^{2}
\end{aligned}
$$

### 协方差
协方差（Covariance）

$$
\text{Cov}(f(x), g(y)) = \text{E}[(f(x) - \text{E}[f(x)])(g(y) - \text{E}[g(y)])]
$$


## 题目
### 两人同生日的概率
假设有 k 个人，每一个的生日随机落入365（不考虑闰年）中的每一天。期望至少有2个人的生日是同一天的情况下，k 的最小值是多少？

「分析」对任意一对人 i、j 而言，我们定一个随机变量（indicator）$X_{ij}$，i 和 j 同一天的生日为 1；否则，为 0。

$$
X_{ij} =
\begin{cases}
    1, \quad B_{i}=B_{j} \\
    0, \quad \text{otherwise}
\end{cases}
$$

i 和 j 的生日是同一天的概率

$$
Pr[ B_{i}=B_{j} ]= \frac{1}{365}*\frac{1}{365}*365 = \frac{1}{365}
$$

期望

$$
\begin{aligned}
E[ X_{ij} ] & = \sum_{i=1}^{k-1}\sum_{j=i+1}^{k}X_{ij}Pr[ B_{i}=B_{j} ] \\
& = \frac{1}{365}{}\sum_{i=1}^{k}\sum_{j=i+1}^{k}X_{ij} \\
& = \frac{1}{365}*\sum_{i=1}^{k-1}[ k-(i+1)+1 ] \\
& = \frac{1}{365}*(\sum_{i=1}^{k-1}k-\sum_{i=1}^{k-1}i) \\
& = \frac{1}{365}*[ k(k-1)-\frac{k(k-1)}{2} ] \\
& = \frac{1}{365}*\frac{k(k-1)}{2}=1
\end{aligned}
$$

求得

$$
\begin{aligned}
& k=1 \pm 27.02 \\
& k = 28
\end{aligned}
$$
