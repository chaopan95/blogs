## 随机初始化
如果权重初始化为0，那么每一个神经元会执行相同的计算，最终所有的神经元没有差异。所以我们需要对权重做随机初始化，这一操作叫「break symmetry」。


## Xavier初始化
在激活函数之前，我们做了一次全连接。因为权重是随机初始化，可以认为权重和特征的分布是独立的。

$$
\sum_{i=1}^{n}x_{i}w_{i}
$$

如果，特征和参数的期望值均为0的话，最后的乘积也是0期望。即

$$
\begin{matrix}
& E(x_{i}) = E(w_{i}) = 0 & \\
& \Downarrow & \\
& E(\sum_{i=1}^{n}x_{i}w_{i}) = 0 &
\end{matrix}
$$

但这样做的结果就是方差在后续的层中增加，即

$$
\begin{matrix}
& Var(\sum_{i=1}^{n}x_{i}w_{i}) = \sum_{i=1}^{n}Var(x_{i}w_{i}) & \\
& \Downarrow & \\
& Var(x_{i}w_{i}) = [ E(x_{i}) ]^{2}Var(w_{i}) + [ E(w_{i}) ]^{2}Var(x_{i}) + Var(x_{i})Var(w_{i}) & \\
& \Downarrow & \\
& Var(\sum_{i=1}^{n}x_{i}w_{i}) = \sum_{i=1}^{n}Var(x_{i})Var(w_{i}) = Var(x)[ nVar(w) ] &
\end{matrix}
$$

这不利于深层网络的收敛。如果我们想 $nVar(w) = 1$，我们需要对随机初始化的权重分布（$N(0, 1)$）乘一个系数 $a = \frac{1}{\sqrt{n}}$。Xavier初始化中乘的系数是 $\frac{\sqrt{2}}{\sqrt{n_{in}+n_{out}}}$。


## He初始化
对激活函数是ReLU，he初始化乘系数为 $\frac{\sqrt{2}}{\sqrt{n_{in}}}$