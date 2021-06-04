## 简介
树状数组是一种对存储数字的列表进行高效地更新及求前缀和的数据结构。主要的操作为：

* 更新（update）

* 求和（sum）

树状数组以数组的形式保存数据，但某些结点是前方若干结点的总和

$$
\begin{matrix}
& & & & & & \rightarrow & \rightarrow & \rightarrow & \rightarrow & \rightarrow & \rightarrow & 8\\
& & & & & \nearrow & & & & & & & \uparrow \\
& & & & 4 & & & & & & & & \uparrow \\
& & & \nearrow & \uparrow & & & & & & & \nearrow \\
& & 2 & & \uparrow & & & & & & 6 & & \uparrow \\
& \nearrow & & & \uparrow & & & & & \nearrow & & & \uparrow \\
1 & & & & 3 & & & & 5 & & & & 7
\end{matrix}
$$

例如， 结点 1、3、5、7 只管理一个数值，结点 2 管理 1、2（共两个），结点 4 管理 1、2、3、4（共四个）。此处的数字均表示数组的秩（从 1 开始）。

## 原理
### lowBit
若给定一个数组的秩，我们求这个秩下一层秩，即当前结点可管理的结点数目。

```cpp
int lowBit(int x) {
    return x & (-x);
}
```

此处 x 表示一个数组的秩，x & (-x) 表示求 x 在二进制形式下，从左到右第一个 1 及其右边的所有 0 构成的值。

例如，

$$
\begin{matrix}
\text{x} & = & (6)_{10} & = & (0110)_{2} \\
\text{-x} & = & (-6)_{10} & = & (1001)_{2} \\
\text{x & (-x)} & = & (0010)_{2} & = & 2
\end{matrix}
$$

可以观察到，奇数的 lowBit 一定为 1，偶数的 lowBit 一定是偶数

### 更新
如果我们想对某一个数值更新，如累加一个数值，需要将当前结点及其所有的上级更新。

```cpp
vector<int> C;
void update(int k, int v = 1) {
    while (k < C.size()) {
        C[k] += v;
        k += lowBit(k);
    }
}
```

### 查询
若给定一个秩，应当返回截止到当前结点的前缀和。

```cpp
int query(int k) {
    int ans = 0;
    while (k > 0) {
        ans += C[k];
        k -= lowBit(k);
    }
    return ans;
}
```

### 初始化建树
如果给定了一个数组，我们希望有另一个数组在 $O(n)$ 的时间下建立树状数组。

```cpp
for (int i = 1; i <= n; i++) {
    C[i] += A[i];
    int j = i + lowBit(i);
    if (j <= n) { C[j] += C[i]; }
}
```

## 题目
### 统计右侧小于当前元素的个数
给定一个数组，如 nums = [5, 2, 6, 1, 1]，返回一个等长的数组，cnt = [3, 2, 2, 0, 0]。cnt[ i ] 表示在 (i : ] 范围内比 nums[ i ] 小的个数。

**「分析」**

朴素的算法是 $O(n^{2})$ 的时间复杂度，两层循环，但是容易超时。题意要求是求当前元素对应的统计值，很自然地我们会想到桶排：由后向前将数组中的元素放到桶中，这里的桶指的是排列后数组。但是，这样做依然不能减小时间复杂度。题目要求是统计个数，本质上是求和，区别在于每次更新操作时，只会 +1 。那么，我们可以使用树状数组求解。

`(1)`用 map 统计数组各个元素出现的个数

`(2)`根据统计后的结果确定树状数组的长度 len ，如果有重复元素出现，这个元素占两个位置，如果无重复元素，这个元素只占一个位置。这里给重复的元素设置两个位置，为了避免在后续求和操作中错误地将相等元素计算在内

`(3)`从后向前遍历数组，找到元素在树状数组的位置，先求和，再更新。如果是重复元素，它有两个位置， i 和 i-1两个为位置，我们用 i 求和，用 i-1 更新。

```cpp
vector<int> C;

int lowBit(int x) {
    return x & (-x);
}

void update(int k, int v = 1) {
    while (k < C.size()) {
        C[k] += v;
        k += lowBit(k);
    }
}

int getSum(int k) {
    int ans = 0;
    while (k > 0) {
        ans += C[k];
        k -= lowBit(k);
    }
    return ans;
}

vector<int> countSmaller(vector<int>& nums) {
    int n = (int)nums.size(), len = 0;
    vector<int> ans(n, 0);
    if (n < 2) { return ans; }
    map<int, int> cnt;
    for (int num : nums) {
        cnt[num]++;
    }
    for (auto iter = cnt.begin(); iter != cnt.end(); iter++) {
        len++;
        if (iter->second > 1) {
            len++;
        }
        iter->second = len;
    }
    C.resize(len + 1);
    for (int i = n - 1; i >= 0; i--) {
        ans[i] = getSum(cnt[nums[i]] - 1);
        update(cnt[nums[i]]);
    }
    return ans;
}
```
时间复杂度 $O(n \log n)$，空间复杂度 $O(n)$