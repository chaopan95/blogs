## 定义
对比学习（contrastive learning）是一机器学习的方法，用于区分相似和不相似的数据。


## 原理


## 损失函数
### InfoNCE
InfoNCE 是 NCE（noise contrastive estimation）的一个变种。NCE 又是来源于 softmax 和 cross entropy。假设网络结构输出结果是 Z，对其进行 softmax 后再取对数得到预测值 $\hat{Y}$，ground truth 为 $Y$，对应相乘即可得到在分类问题上常用的交叉熵损失函数。

$$
\begin{aligned}
& \hat{Y} = \text{softmax}(Z) = \frac{\exp(z^{+})}{\sum_{j \in K}\exp(z_{j})} \\
& L(Y, \hat{Y}) = - \sum_{i \in K} y_{i} \log y_{i}
\end{aligned}
$$

这里的 k 表示了分类的类别，在图像领域每一张图片都是一个类别。逐一计算成本太高，这里引出了 NCE，用负采样的方式简化了计算。K 个类别简化成 2 个类别：数据和噪声（也可以看成正样本和负样本），体现出 noise contrastive；因为没有计算全样本，而是采样若干负样本，因此是 estimation。

有些时候，二分类模型略显粗糙，但全分类计算成本有过大。如果在两者之间去一个折中，便可以兼顾彼此，这就诞生了 InfoNCE。

$$
L_{q} = -log \frac{\exp(q \cdot k_{+} / \tau)}{\sum_{i=0}^{K} \exp(q \cdot k_{i} / \tau)}
$$

这里的 K 表示采集的负样本的个数，即每一个样本为一个类别，总共 K + 1 个类别。$q \cdot k$ 表达了模型的预估分，通常使用相似度。温度系数 $\tau$ 反映了对正负样本的敏感程度，越小越敏感，模型便会更关注困难样本（误分）。
