## Normalizing inputs
归一化输入有利于梯度下降，损失函数更快收敛

$$
\begin{aligned}
& X = \frac{X - \mu}{\sigma} \\
& \mu = \frac{1}{m} \sum_{i=1}^{m} x^{i}, \quad \sigma^{2} = \frac{1}{m} \sum_{i=1}^{m} (x^{i} - \mu)^{2}
\end{aligned}
$$

假设输入的是一张图片，像素值归一化后

$$
x_{norm} = \frac{x}{255} - 0.5
$$


## Gradient checking
将参数（$W^{[1]}$, $b^{[1]}$, ..., $W^{[L]}$, $b^{[L]}$）和参数的导数（$dW^{[1]}$, $db^{[1]}$, ..., $dW^{[L]}$, $db^{[L]}$）取出并形成新的向量 $\theta$ 和 $d\theta$

$$
J(\theta) = J(\theta_{1}, \theta_{2}, ..., \theta_{n})
$$

对每一个参数 $\theta_{i}$，我们检查

$$
d\theta_{i}^{\text{approx}} = \frac{J(\theta_{1}, ..., \theta_{i} + \epsilon, ..., \theta_{n})-J(\theta_{1}, ..., \theta_{i} - \epsilon, ..., \theta_{n})}{2\epsilon} \approx d\theta_{i}
$$

另一种形式

$$
\frac{\left \| d\theta_{i}^{\text{approx}} - d\theta_{i} \right \|_{2}}{\left \| d\theta_{i}^{\text{approx}} \right \|_{2} + \left \| d\theta_{i} \right \|_{2}} < 10^{-7}
$$

$\bigstar$ 仅用作 debug，不用在训练阶段使用
