## 假设
$\bigstar$ 训练集、验证集和测试集服从 i.i.d 独立同分布

$\bigstar$ 训练集、验证集和测试集表现均要良好，泛化能力好

$\bigstar$ 训练集：验证集：测试集 = 7：3：1，对大规模数据，98：1：1


## 交叉验证
k-折交叉验证：讲训练数据随机分成 k 份，每次选择一份作为验证集，剩下的作为训练集，重复 k 次，获得 k 次损失值，取平均。

$\bigstar$ 适合于小样本

$\bigstar$ 在深度学历里，验证集（holdout samples）更适合


## Variance vs Bias
高偏差也叫欠拟合（underfitting），具体指训练损失值较大；高方差也叫过拟合（overfitting），在训练数据集上低误差，但测试数据集上高误差。

$\bigstar$ 避免高偏差：1）更复杂的模型，2）延长训练时间

$\bigstar$ 避免高方差：1）更多的训练数据，2）正则化，3）dropout，4）降低模型复杂度


## 端到端学习
端到端学习（End-to-end learning）将各个阶段的处理合并成一个。例如

$$
\begin{matrix}
& \text{Audio} \rightarrow \text{Features} \rightarrow \text{Phenome} \rightarrow \rightarrow \text{Words} \rightarrow \text{Transcript} & \\
& \Downarrow & \\
& \text{Audio} \rightarrow \text{Transcript} &
\end{matrix}
$$

$\bigstar$ 优点：较少的人工设计模块

$\bigstar$ 缺点：需要大量的数据