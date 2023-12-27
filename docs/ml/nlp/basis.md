## 符号
token：单词

文本：一个序列的token

输入：$x_{t}, t \in [1, T_{x}]$，表示序列中第 t 个 token，$T_{x}$ 代表文本长度。

输出：$\hat{y}_{t}, t \in [1, T_{y}]$，表示第 t 个预测 token，$T_{y}$ 代表输出长度。

中间结果：$h_{t}, t \in [1, T_{x}]$

真实标签：$y_{t}$

损失函数：$L_{t}(y_{t}, \hat{y}_{t})$，衡量预测值与真实值的差距

EOS：end of sentence，一个文本的终止符

UNK：unknow words，未知词汇
