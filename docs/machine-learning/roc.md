## 简介
接收者操作特征曲線（receiver operating characteristic curve，简称ROC）是一种选择最佳模型的坐标图式分析工具。

## 术语
* 阳性 P（positive）

* 阴性 N （negative）

* 真阳性 TP （true positive）

* 真阴性 TN （true negative）

* 假阳性 FP （false positive）

* 假阴性 FN （false negative）

* 精确度 $\text{precision} = \frac{\text{TP}}{\text{TP + FP}}$

* 召回率/真阳性率 $\text{recall / TPR} = \frac{\text{TP}}{\text{TP + FN}}$

* 假阳性率 $\text{FPR} = \frac{\text{FP}}{\text{FP + TN}}$

* 准确度 $\text{accuracy} = \frac{\text{TP + TN}}{\text{TP + TN + FP + FN}}$

|  | P | N |
| :----:  | :----: | :----: |
| T | TP | FN |
| F | FP | TN |

## AUC
AUC 全称是 area under curve ，指的是接受者操作特征曲线下的面积。

$$
\begin{matrix}
\text{TPR} \\
\wedge \\
1 & & & & & & & & & & & 1 \\
| & & & & & & & & & & \bullet & | \\
| & & & & & & & & & \bullet & & | \\
| & & & & & & & & \bullet & & & | \\
| & & & & & & & \bullet & & & & | \\
| & & & & & & \bullet & & & & & | \\
| & & & & & & \bullet & & & & & | \\
| & & & \bullet & \bullet & \bullet & & & \text{AUC} & & & | \\
| & & \bullet & & & & & & & & & | \\
| & & \bullet & & & & & & & & & | \\
| & \bullet & & & & & & & & & & | \\
0 & - & - & - & - & - & - & - & - & - & - & 1 & -> \text{FPR}
\end{matrix}
$$

### 物理含义
任意取一个正样本和一个负样本组成一个样本对 $(p_{i}, n_{i})$，使用训练好的模型对这一对随机样本进行预测，判断 正样本 $p_{i}$ 为正的概率为 $p_{1}$，判断负样本 $n_{i}$ 为正的概率为 $p_{0}$，auc 的大小即为 $p_{1}$ 大于 $p_{0}$ 的概率

$$
\text{AUC} = \mathbf{P}(p_{1} > p_{0})
$$

假设我们的分类器是一个随机分类器，对正负样本的划分概率是一样的，那么这个分类器的 AUC 就是0.5。

AUC 不受划分正负样本的阈值影响，也不受正负样本的不均影响，但是只适合二分类的问题。

### 计算方法
**「正负样本对」**

我们可以根据AUC的物理含义求解，穷举所有的正负样本对，然后计算正样本的预测值大于负样本预测值的概率。

$$
\begin{aligned}
& \text{AUC} = \frac{f(P_{\text{postive}}, P_{\text{negative}})}{N_{\text{positive}} \times N_{\text{nagetive}}} \\
& f(P_{\text{postive}}, P_{\text{negative}}) =
\begin{cases}
1, & \quad P_{\text{postive}} > P_{\text{negative}} \\
0.5, & \quad P_{\text{postive}} = P_{\text{negative}} \\
0, & \quad P_{\text{postive}} < P_{\text{negative}}
\end{cases}
\end{aligned}
$$

```cpp
double auc1(vector<double> predict, vector<int> label) {
    int n = (int)predict.size();
    int n1 = 0, n0 = 0;
    double I = 0.0;
    vector<double> p1, p0;
    for (int i = 0; i < n; i++) {
        if (label[i]) {
            p1.emplace_back(predict[i]);
            n1++;
        }
        else {
            p0.emplace_back(predict[i]);
            n0++;
        }
    }
    for (int i = 0; i < n1; i++) {
        for (int j = 0; j < n0; j++) {
            if (p1[i] > p0[j]) {
                I += 1;
            }
            else if (p1[i] == p0[j]) {
                I += 0.5;
            }
        }
    }
    return I / (n1 * n0);
}
```
时间复杂度 $O(n_{1} n_{0})$，空间复杂度 $O(n_{1} + n_{0})$，其中 $n_{1}$ 和 $n_{0}$ 分别是正负样本的数目

**「排序降低复杂度」**

将预测值按照从小到大的顺序排列，可使用如下公式

$$
\text{AUC} = \frac{\sum_{i \in \{+\}} \text{rank}_{i} - \frac{n_{\text{positive}} \times (n_{\text{positive}}+1)}{2}}{n_{\text{positive}} \times n_{\text{negative}}}
$$

我们只需要计算正样本在排序后的列表里面的序号（秩），从 1 开始。如果出现正负样本具有相同的预测分值，那么需要求这些正负样本的平均秩乘以同分值中正样本的个数。

```cpp
double auc2(vector<double> predict, vector<int> label) {
    vector<pair<double, int>> data;
    int n = (int)predict.size();
    for (int i = 0; i < n; i++) {
        data.emplace_back(pair<double, int>{predict[i], label[i]});
    }
    sort(data.begin(), data.end(), [](const pair<double, int> &x,
                                      pair<double, int> &y) {
        return x.first < y.first || (x.first == y.first &&
                                     x.second > y.second);
    });
    int row = 0, rankPos = 0, rankNeg = 0, numPos = 0,
        numNeg = 0, totPosSam = 0;
    double cnt = 0;
    double lastScore = -0.1;
    // 最后添加一个数对标志结束，不参与计算
    data.emplace_back(pair<double, int>{-0.1, -1});
    for (auto d : data) {
        row++;
        if (lastScore != d.first && row > 1) {
            lastScore = d.first;
            cnt += double(rankPos + rankNeg) /
                   (numPos + numNeg) * numPos;
            rankPos = rankNeg = numPos = numNeg = 0;
        }
        if (d.second == 1) {
            rankPos += row;
            numPos++;
            totPosSam++;
        }
        else {
            rankNeg += row;
            numNeg++;
        }
    }
    return (cnt - totPosSam * (totPosSam + 1) / 2.0) /
            (totPosSam * (n - totPosSam));
}
```
时间复杂度 $O(n \log n)$，空间复杂度 $O(n)$，其中 n 是正负样本的数目和

**「分桶降低复杂度」**

概率的范围是 [0, 1]，因此可以使用分桶方法统计每一个样本预测值落在哪个桶内（区间）。我们使用两个 hist 用来统计预测值落在某个桶内的个数。我们的目标是求概率对，假设预测值为 0.8 的正样本有 10 个，那么低于 0.8 的负样本总数乘以 10 即为这一预测值的概率对，此外，我们还应该计算预测值等于 0.8 的负样本数，假设为 8，这种情况下，此时正负样本概率相等，需要取一半，即 8 * 10 / 2。

```cpp
double auc3(vector<double> predict, vector<int> label,
            int bins = 100) {
    // 时间复杂度O(n)
    int cnt_pos = 0, cnt_neg = 0;
    int n = (int)predict.size();
    vector<int> pos_hist(bins, 0), neg_hist(bins, 0);
    double width = 1.0 / bins;
    for (int i = 0; i < n; i++) {
        int nth_bin = (int)(predict[i] / width);
        if (label[i]) {
            cnt_pos++;
            pos_hist[nth_bin]++;
        }
        else {
            cnt_neg++;
            neg_hist[nth_bin]++;
        }
    }
    int accumulated_negative = 0, satisfied_pair = 0;
    for (int i = 0; i < bins; i++) {
        satisfied_pair += pos_hist[i] * accumulated_negative +
        pos_hist[i] * neg_hist[i] * 0.5;
        accumulated_negative += neg_hist[i];
    }
    return satisfied_pair / double(cnt_pos * cnt_neg);
}
```

时间复杂度 $O(n)$，空间复杂度 $O(n)$，其中 n 是正负样本的数目和