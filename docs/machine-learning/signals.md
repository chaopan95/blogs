## 卷积
卷积三步骤：反转、平移、求和。

$$
y(n_{1}, n_{2}) = x(k_{1}, k_{2})**h(n_{1}, n_{2}) = \sum_{k_{1}=-\infty}^{\infty}\sum_{k_{2}=-\infty}^{\infty} x(k_{1}, k_{2}) h(n_{1}-k_{1}, n_{2}-k_{2})
$$

$$
B(i, j) = \sum_{m=0}\sum_{n=0}K(m, n)A(i-m, j-n)
$$

$$
S(n) = (f*g)[ n ] = \sum_{m=0}^{N-1} f(m)g(n-m)
$$

N 代表信号 f[ n ] 的长度，S(n) 的长度等于 f[ n ] 与 g[ n ] 的和减1；

例如，f[ n ] = [1, 2, 3]，g[ n ] = [2, 3, 1]

$$
\begin{aligned}
S(0) &= \sum_{m=0}^{2} f(m)g(0-m) \\
&= f(0)g(0) + f(1)g(-1) + f(2)g(-2) \\
&= 1\cdot 2 + 2\cdot 0 + 3\cdot 0 \\
&= 2
\end{aligned}
$$

$$
\begin{aligned}
S(1) &= \sum_{m=0}^{2} f(m)g(1-m) \\
&= f(0)g(1) + f(1)g(0) + f(2)g(-1) \\
&= 1\cdot 3 + 2\cdot 2 + 3\cdot 0 \\
&= 7
\end{aligned}
$$

$$
\begin{aligned}
S(2) &= \sum_{m=0}^{2} f(m)g(2-m) \\
&= f(0)g(2) + f(1)g(1)+ f(2)g(0) \\
&= 1\cdot 1 + 2\cdot 3 + 3\cdot 2 \\
&= 13
\end{aligned}
$$

$$
\begin{aligned}
S(3) &= \sum_{m=0}^{2} f(m)g(3-m) \\
&= f(0)g(3) + f(1)g(2) + f(2)g(1) \\
&= 1\cdot 0 + 2\cdot 1 + 3\cdot 3 \\
&= 11
\end{aligned}
$$

$$
\begin{aligned}
S(4) &= \sum_{m=0}^{2} f(m)g(4-m) \\
&= f(0)g(4) + f(1)g(3) + f(2)g(2) \\
&= 1\cdot 0 + 2\cdot 0 + 3\cdot 1 \\
&= 3
\end{aligned}
$$


## 滤波
### 高通滤波
对比增强（连接区域的明暗变化）后，一张图像会变的尖锐。高通滤波（high pass filter）能捕获高频信息同时丢弃低频信息。相对邻居像素，高频率波核可以增加明亮程度。核通常的范式为中心是正值，四周为负值，形如

$$
\frac{1}{9}
\begin{vmatrix}
-1 & -1 & -1\\
-1 & 8 & -1\\
-1 & -1 & -1
\end{vmatrix}
$$

### 低通滤波
平均附近的像素来减少像素之间的差异，最终让一张图像变平滑。低通滤波（low pass filter）能捕获低频信息同时过滤高频信息。一个低通滤波形如：

$$
\frac{1}{9}
\begin{vmatrix}
1 & 1 & 1\\
1 & 1 & 1\\
1 & 1 & 1
\end{vmatrix}
$$


## 傅立叶变换
二位傅立叶变换（2D Fourier transform）

$$
x(n_{1}, n_{2}) = e^{\omega_{1}^{'}n_{1} + \omega_{2}^{'}n_{2}} \rightarrow h(n_{1}, n_{2}) \rightarrow y(n_{1}, n_{2})
$$

$$
\begin{aligned}
y(n_{1}, n_{2}) &= x(n_{1}, n_{2})**h(n_{1}, n_{2}) \\
& = \sum_{k_{1}=-\infty}^{\infty}\sum_{k_{2}=-\infty}^{\infty} e^{j\omega_{1}^{'}(n_{1}-k_{1})} e^{j\omega_{2}^{'}(n_{2}-k_{2})} h(k_{1}, k_{2}) \\
& = e^{j\omega_{1}^{'}n_{1}} e^{j\omega_{2}^{'}n_{2}} \sum_{k_{1}=-\infty}^{\infty}\sum_{k_{2}=-\infty}^{\infty} h(k_{1}, k_{2}) e^{-j\omega_{1}^{'}k_{1}} e^{-j\omega_{2}^{'}k_{2}}
\end{aligned}
$$

频率响应（Frequency response）

$$
H(\omega_{1}^{'}, \omega_{2}^{'}) = \sum_{k_{1}=-\infty}^{\infty}\sum_{k_{2}=-\infty}^{\infty} h(k_{1}, k_{2}) e^{-j\omega_{1}^{'}k_{1}} e^{-j\omega_{2}^{'}k_{2}}
$$

$$
X(\omega_{1}, \omega_{2}) = \sum_{n_{1}=-\infty}^{\infty}\sum_{n_{2}=-\infty}^{\infty} x(n_{1}, n_{2}) e^{-j\omega_{1}n_{1}} e^{-j\omega_{2}n_{2}}
$$

$$
x(n_{1}, n_{2}) = \frac{1}{4\pi^{2}} \int_{-\pi}^{\pi}\int_{-\pi}^{\pi} X(\omega_{1}, \omega_{2}) e^{j\omega_{1}n_{1}} e^{j\omega_{2}n_{2}} d\omega_{1}d\omega_{2}
$$

性质

$$
X(\omega_{1}, \omega_{2}) = X(\omega_{1}+2\pi, \omega_{2}+2\pi)
$$

$$
x(n_{1}-m_{1}, n_{2}-m_{2}) \leftrightarrow e^{-j\omega_{1}n_{1}} e^{-j\omega_{2}n_{2}} X(\omega_{1}, \omega_{2})
$$

$$
x(n_{1}, n_{2})e^{j\theta_{1}n_{1}+j\theta_{2}n_{2}} \leftrightarrow X(\omega_{1}-\theta_{1}, \omega_{2}-\theta_{2})
$$

对 $x(n_{1}, n_{2})$

$$
|X(\omega_{1}, \omega_{2})| = |X(-\omega_{1}, -\omega_{2})|
$$

$$
arg X(\omega_{1}, \omega_{2}) = -argX(-\omega_{1}, -\omega_{2})
$$

$$
\int_{n_{1}}\int_{n_{2}} |x(n_{1}, n_{2})|^{2} = \frac{1}{4\pi^{2}} \int_{-\pi}^{\pi}\int_{-\pi}^{\pi} |x(\omega_{1}, \omega_{2})|^{2} d\omega_{1}d\omega_{2}
$$

例如

$$
h(n_{1}, n_{2}) = 
\begin{vmatrix}
h(-1, 1) & h(0, 1) & h(1, 1)\\
h(-1, 0) & h(0, 0) & h(0, 1)\\
h(-1, -1) & h(0, -1) & h(1, -1)
\end{vmatrix} = 
\begin{vmatrix}
0.075 & 0.124 & 0.075\\
0.124 & 0.204 & 0.124\\
0.075 & 0.124 & 0.075
\end{vmatrix}
$$

$$
\begin{aligned}
H(\omega_{1}, \omega_{2}) &= 0.204 + 0.124 \cdot 2 \cdot cos\omega_{1} + 0.124\cdot 2 \cdot cos\omega_{2} \\
&+ 0.075 \cdot cos(\omega_{1}+\omega_{2}) + 0.075 \cdot 2 \cdot cos(\omega_{1} - \omega_{2})
\end{aligned}
$$

$$
h(n_{1}, n_{2}) = 
\begin{vmatrix}
h(-1, 1) & h(0, 1) & h(1, 1)\\
h(-1, 0) & h(0, 0) & h(0, 1)\\
h(-1, -1) & h(0, -1) & h(1, -1)
\end{vmatrix} = 
\begin{vmatrix}
-1 & -1 & -1\\
-1 & 9 & -1\\
-1 & -1 & -1
\end{vmatrix}
$$

$$
H(\omega_{1}, \omega_{2}) = 9 - 2 \cdot cos\omega_{1} - 2 \cdot cos\omega_{2} - 2 \cdot cos(\omega_{1}+\omega_{2}) - 2 \cdot cos(\omega_{1} - \omega_{2})
$$
