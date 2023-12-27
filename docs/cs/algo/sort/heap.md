## 原理
堆排序（Heap Sort）计算流程如下：

1）在 $O(n)$ 的时间内对序列建堆

2）取堆顶元素放入带排序空间里，并删除堆顶

3）重复 2）直到堆为空

时间复杂度 $O(n + \log(n!)) = O(n\log n)$

$$
\begin{aligned}
\log(n!) &= \log(1) + \log(2) + \cdots + log(n) \\
& \leq log(n) + log(n) + \cdots + log(n) \\
&= nlog(n)
\end{aligned}
$$

$$
\begin{aligned}
\log(n!) &\geq \log(\frac{n}{2}) + \log(\frac{n}{2}+1) + \cdots + \log(n) \\
&\geq \log(\frac{n}{2}) + \log(\frac{n}{2}) + \cdots + \log(\frac{n}{2}) \\
&= \frac{n}{2} \log(\frac{n}{2})
\end{aligned}
$$