## 01 背包
一个背包有一定的承重 W，有N件物品，每件都有自己的价值，记录在数组 v 中，也都有自己的重量，记录在数组 w 中，每件物品只能选择要装入背包还是不装入背包，要求在不超过背包承重的前提下，选出物品的总价值最大。例如，

$$
\begin{aligned}
& \text{v = [1, 2, 3]} \\
& \text{w = [1, 2, 4]} \\
& \text{W = 6}
\end{aligned}
$$

最大的价值为 5。

**「分析」**

设 dp[ i ][ j ] 为前 i 件物品在不超过 j 的重量下的最大价值。此时的状态只会有两个：（1）第 i 件物品没有被选中，（2）第 i 件物品被选中。状态转移方程如下：

$$
\text{dp[ i ][ j ]} = \max(\text{dp[i - 1]][ j ], dp[i - 1][j - w[ i ]]} + v[ i ])
$$

（1）如果第 i 件物品没有被选中，那么前 i - 1 件物品的重量就是 j；（2）如果第 i 件物品被选中了，那么前 i - 1 件物品的重量就是 j - v[ i ]，这里可以认为，第 i 件物品被加入后，总重量将会达到 j，那么加入之前的重量自然就等于 j - v[ i ]。当然，在计算的时候需要判定，当前的总重量 j 是否大于第 i 件物品的重量。

因为状态的转移只发生在相邻物品之间，所以空间复杂度得以优化。

```cpp
int knapsack01(vector<int> v, vector<int> w, int W) {
    int N = (int)v.size();
    vector<int> dp(W+1, 0);
    for (int i = 0; i < N; i++) {
        for (int j = W; j >= w[i]; j--) {
            dp[j] = max(dp[j], dp[j-w[i]] + v[i]);
        }
    }
    return dp[W];
}
```
时间复杂度 $O(N \times W)$，空间复杂度 $O(W)$，N 为物品的数目，W 为限定的重量

### 目标和
给定一个非负数组, 如 nums = [1, 1, 1, 1, 1] 和一个目标值 target = 3。对于数组中的每一个数值，我们可以在其前方放置一个运算符 + 或者 - 来构成一个计算表达式，试问这样的表达式结果恰好为 target 的个数为多少？

**「分析」**

题意可以被解释为，将一个数组分成两组，其中一组全部为标志为正数，另一组标志为负数，这样求和后结果为 target。本题是 01 背包问题求方案数问题。假设数组和为 sum，正数组的和为 pos，负数组的和为 neg（绝对值），可以得到如下关系

$$
\begin{aligned}
& \text{pos + neg = sum} \\
& \text{pos - neg = target}
\end{aligned} \Rightarrow
2 \times \text{pos = sum - target}
$$

所以，sum 必须不小于 target，并且 sum - targte 必须是偶数。

设 dp[ i ][ j ] 为后 i 个数中求和等于 j 的方案数目。状态转移方程如下：

$$
\text{dp[ i ][ j ]} = 
\begin{cases}
\text{dp[i + 1][ j ]}, & \quad \text{j } < \text{nums[ i ]} \\
\text{dp[i + 1][ j ] + dp[i + 1][j - nums[ i ]]}, &\quad \text{j } \geq \text{nums[ i ]}
\end{cases}
$$

关注边界条件：如果 j = 0，即负数和的绝对值为 0，表明我们应该不选取任何元素，因此 dp[ i ][ 0 ] = 1，这里 i 从 0 到 n - 1。当 i == n，即当前数组没有元素，只有 j == 0 时，方案数目才为 1。如果从后向前递推，最终的答案是 dp[ 0 ][ target ]。优化后的空间复杂度可以是线性。

```cpp
int findTargetSumWays(vector<int>& nums, int target) {
    int sum = 0;
    for (int num : nums) { sum += num; }
    if (target > sum || (sum + target) % 2 != 0) { return 0; }
    int neg = (sum + target) >> 1;
    vector<int> dp(neg + 1, 0);
    dp[0] = 1;
    for (int num : nums) {
        for (int j = neg; j >= num; j--) {
            dp[j] += dp[j - num];
        }
    }
    return dp[neg];
}
```
时间复杂度 $O(target \times n)$，空间复杂度 $O(target)$，这里 target 是目标值大小，n 是数组长度。

### 分割等和子集
给定一个正整数构成的数组，如 nums = [1, 5, 11, 5]，判断能否将此数组分割成两个数组（不要求连续），使两个子数组的和相等。给出的例子可以被分成 [1, 5, 5] 和 [ 11 ]

**「分析」**

按照题意，我们将一个数组分成两个部分，可以理解成选取若干个数，其和是原数组和的一半。因此，可以将此题转换成 01 背包问题。设 dp[ i ][ j ] 表示后 i 个元素中选取某几个值，其和是否等于 j。如果原数组的和 sum 是奇数，那么直接返回 false，因为两个相等数的和一定是偶数。状态转移方程如下

$$
\text{dp[ i ][ j ]} = 
\begin{cases}
\text{dp[i + 1][ j ]}, &\quad \text{j } < \text{nums[ i ]} \\
\text{dp[i + 1][ j ] or dp[i + 1][j - nums[ i ]]}, &\quad \text{j } \geq \text{nums[ i ]}
\end{cases}
$$

考虑边界条件，当 j == 0 时，说明两个数组均为空，那么整个数组应该为空，对应的 i == n （从后往前递推）。最后的答案应该为 dp[ 0 ][ sum / 2 ]。

```cpp
bool canPartition(vector<int>& nums) {
    int n = (int)nums.size(), sum = 0;
    for (int num : nums) { sum += num; }
    if (sum % 2 != 0) { return false; }
    sum /= 2;
    vector<vector<bool>> dp(n + 1, vector<bool> (sum + 1, 0));
    dp[n][0] = true;
    for (int i = n - 1; i >= 0; i--) {
        for (int j = 0; j <= sum; j++) {
            dp[i][j] = dp[i + 1][j];
            if (j >= nums[i]) {
                dp[i][j] = dp[i][j] || dp[i + 1][j - nums[i]];
            }
        }
    }
    return dp[0][sum];
}
```

空间优化后的代码

```cpp
bool canPartition(vector<int>& nums) {
    int sum = 0;
    for (int num : nums) { sum += num; }
    if (sum % 2 != 0) { return false; }
    sum /= 2;
    bool *dp = new bool [sum + 1]{};
    dp[0] = true;
    for (int num : nums) {
        for (int j = sum; j >= num; j--) {
            dp[j] = dp[j] || dp[j - num];
        }
    }
    return dp[sum];
}
```
时间复杂度 $O(sum \times n)$，空间复杂度 $O(sum)$，这里 sum 是原数组的和的一半，n 是数组长度。

### 最后一块石头的重量
给定一个数组 stones = [2, 7, 4, 1, 8, 1] 表示一组石头的重量。从当前的石头里任取两个，质量是 x 和 y，如果两块质量相等即 x = y，那么这两块石头被移除；反之，移除较小的那一块，并且较大的那一块变成这两块质量的差值（绝对值）。重复这个过程，直到剩余一块或者无剩余，求剩余的最小质量是多少。

**「分析」**

我们可以原数组分割成两组 + 和 -，我们用 + 这一组减去 - 这一组，所得结果的最小值是最终结果，这样问题转化成 01 背包问题。设 neg 为 - 这一组的和，pos 为 + 这一组的和。满足下列方程组

$$
\begin{aligned}
& \text{pos + neg = sum} \\
& \text{pos - neg = res}
\end{aligned} \quad \Rightarrow \quad 2 \times \text{neg = sum - res}
$$

那么我们可以得知

$$
0 \leq \text{neg} \leq \left \lfloor \frac{\text{sum}}{2} \right \rfloor
$$

因此，我们可以理解成，从原数组中选取若干个值，其和等于 neg，并且使得

$$
\arg \min_{\text{neg}} \text{(sum} - \text{neg} \times \text{2)}
$$

设 dp[ i ][ j ] 表示后 i 个数字中选取若干个，它们的和在不超过 j 的情况下最大值是多少。状态转移方程为

$$
\text{dp[ i ][ j ]} = 
\begin{cases}
\text{dp[i + 1][ j ]}, &\quad \text{j } < \text{stones[ i ]} \\
\max(\text{dp[i + 1][ j ], dp[i + 1][j - stones[ i ]] + stones[ i ]]}), &\quad \text{j } \geq \text{stones[ i ]}
\end{cases}
$$

```cpp
int lastStoneWeightII(vector<int>& stones) {
    int sum = 0;
    for (int stone : stones) { sum += stone; }
    int tar = (sum >> 1);
    vector<int> dp(tar + 1, 0);
    for (int stone : stones) {
        for (int j = tar; j >= stone; j--) {
            dp[j] = max(dp[j], dp[j - stone] + stone);
        }
    }
    return sum - 2 * dp[tar];
}
```

时间复杂度 $O(tar \times n)$，空间复杂度 $O(tar)$，这里 tar 是原数组的和的一半，n 是数组长度。

## 完全背包
一个背包承重总量是 W，有 N 种物品，每一件有自己的价值 $v_{i}$ 和 重量 $w_{i}$，每一种类的物品无限供应，试问在不超过背包承重总量下，背包的价值最大。

**「分析」**

完全背包问题种，每一件物品的供应是无限的，但因为背包承重总量有限，可以放入的数目依然有限 $0 \leq c_{i} \leq \frac{W}{w_{i}}$，那么我们依然可以设 dp[ i ][ j ] 为前 i 件物品中选取若干件，在重量和不超过 j 的情况下最大价值。

$$
\text{dp[ i ][ j ]} =
\begin{cases}
\text{dp[i + 1][ j ]}, &\quad \text{j } < \text{w[ i ]} \\
\max
\begin{cases}
\text{dp[i + 1][ j ]} \\
\text{dp[i + 1][j - w[ i ]] + v[ i ]} \\
\text{dp[ i ][j - w[ i ]] + v[ i ]}
\end{cases}, &\quad \text{j } \geq \text{w[ i ]}
\end{cases}
$$

```cpp
int completeKnapsack(vector<int> v, vector<int> w, int W) {
    int n = (int)v.size();
    vector<vector<int>> dp(n + 1, vector<int>(W + 1, 0));
    for (int i = n - 1; i >= 0; i--) {
        for (int j = 0; j <= W; j++) {
            dp[i][j] = dp[i + 1][j];
            if (j >= w[i]) {
                dp[i][j] = max(dp[i][j], max(dp[i + 1][j - w[i]],
                                             dp[i][j - w[i]]) + v[i]);
            }
        }
    }
    return dp[0][W];
}
```

空间上进行优化

```cpp
int completeKnapsack(vector<int> v, vector<int> w, int W) {
    int n = (int)v.size();
    vector<int> dp(W + 1, 0);
    for (int i = n - 1; i >= 0; i--) {
        for (int j = w[i]; j <= W; j++) {
            if (j >= w[i]) {
                dp[j] = max(dp[j], dp[j - w[i]] + v[i]);
            }
        }
    }
    return dp[W];
}
```

这里与 01 背包的代码只有一行不同，即 j 的遍历方向上。完全背包问题是有小到大遍历，因为某一件物品可以被多次放入；反而 01 背包问题对任一件物品最多只允许放入一次。

### 换零钱
