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

设 dp[ i ][ j ] 为从第 i 件物品到最后一件物品在背包容量为 j 下的最大价值。此时的状态只会有两个：（1）第 i 件物品没有被选中，（2）第 i 件物品被选中。状态转移方程如下：

$$
\text{dp[ i ][ j ]} = \max(\text{dp[i + 1]][ j ], dp[i + 1][j - w[ i ]]} + v[ i ])
$$

（1）如果第 i 件物品没有被选中，那么第 i + 1 到最后一件物品的重量就是 j；（2）如果第 i 件物品被选中了，那么第 i + 1 到最后一件物品的重量就是 j - w[ i ]，这里可以认为，第 i 件物品被加入后，总重量将会达到 j，那么加入之前的重量自然就等于 j - w[ i ]。当然，在计算的时候需要判定，当前的总重量 j 是否大于第 i 件物品的重量。

**「线性空间」**

因为状态的转移只发生在相邻物品之间，所以空间复杂度得以优化，但用一维的空间时，应对容量 w 进行**倒序遍历**。

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

**「重构最优解」**

除了得到最终的总价值，我们也希望获知背包的方案，即挑选哪些物品放入背包中。在这一步，我们需要二维数组记录每一步的迭代。因为某一件物品只有两种选择，加入背包和不加入背包，并且，当前的物品只与后（前）一件物品有关系，我们只需要知道 dp[ i ][ j ] 是等于 dp[i + 1][ j ] 还是等于 dp[i + 1][j - w[ i ]] + v[ i ]。如果是后者，说明第 i 件物品被放入，反之，则未被放入。我们只需要记录哪些物品被放入即可，同时更新 j。

```cpp
int knapsack(vector<int> w, vector<int> v, int W) {
    int n = (int)w.size();
    vector<vector<int>> G(n + 1, vector<int>(W + 1, 0));
    vector<int> dp(W + 1, 0);
    for (int i = n - 1; i >= 0; i--) {
        for (int j = W; j >= w[i]; j--) {
            dp[j] = max(dp[j], dp[j - w[i]] + v[i]);
        }
        G[i] = dp;
    }
    vector<int> idx;
    for (int i = 0, j = W; i < n && j >= 0; i++) {
        if (G[i][j] == G[i + 1][j - w[i]] + v[i]) {
            idx.emplace_back(i);
            j -= w[i];
        }
    }
    for (int id : idx) {
        printf("w[%d]=%d\n", id, w[id]);
    }
    return dp[W];
}
```

**「初始化细节」**

一个容量为 W 的背包，有两种不同的要求：（1）背包要被装满和（2）背包可以不满。这两种的初始化是不同的。

对于（1），dp[ 0 ] 被设置为 0，dp[1 : W] 被设置为 $-\infty$；对于（2），dp[0 : W] 都为 0。可以这样理解，条件（1）要求背包装满，那么 W = 0 是满足的，因为空背包的容量就是 0，但是一旦背包的容量不是 0，那么未装任何物品（空载）是不符合背包装满的要求，$-\infty$ 表示无合法的解；反观条件（2），空载对于任意的 W 都是一个合法解，因此空载背包的价值就是 0。

这里举一个例子，v = [4, 3, 7, 5]，w = [2, 3, 5, 7]，W = 9。如果要求装满背包，结果是 9；反之是 11。

### 目标和
给定一个非负数组, 如 nums = [1, 1, 1, 1, 1] 和一个目标值 target = 3。对于数组中的每一个数值，我们可以在其前方放置一个运算符 + 或者 - 来构成一个计算表达式，试问这样的表达式结果恰好为 target 的个数为多少？

**「分析」**

题意可以被解释为，将一个数组分成两组，其中一组全部为标志为正数，另一组标志为负数，这样求和结果为 target。本题是 01 背包问题求方案数问题。假设数组和为 sum，正数组的和为 pos，负数组的和为 neg（绝对值），可以得到如下关系

$$
\begin{aligned}
& \text{pos + neg = sum} \\
& \text{pos - neg = target}
\end{aligned} \Rightarrow
2 \times \text{neg = sum - target}
$$

所以，sum 必须不小于 target，并且 sum - targte 必须是偶数。

设 dp[ i ][ j ] 为从第 i 个数到最后一个数中求和等于 j 的方案数目。状态转移方程如下：

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
    if (target > sum || (sum - target) % 2 != 0) { return 0; }
    int neg = (sum - target) >> 1;
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

按照题意，我们将一个数组分成两个部分，可以理解成选取若干个数，其和是原数组和的一半。因此，可以将此题转换成 01 背包问题。设 dp[ i ][ j ] 为从第 i 个元素到最后一个元素中选取某几个值，其和是否等于 j。如果原数组的和 sum 是奇数，那么直接返回 false，因为两个相等数的和一定是偶数。状态转移方程如下

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

设 dp[ i ][ j ] 为从第 i 个数字到最后一个数字中选取若干个，它们的和在不超过 j 的情况下最大值是多少。状态转移方程为

$$
\text{dp[ i ][ j ]} = 
\begin{cases}
\text{dp[i + 1][ j ]}, &\quad \text{j } < \text{stones[ i ]} \\
\max
\begin{cases}
\text{dp[i + 1][ j ]} \\
\text{dp[i + 1][j - stones[ i ]] + stones[ i ]]}
\end{cases}
, &\quad \text{j } \geq \text{stones[ i ]}
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

### 盈利计划
给定一个整数 n 代表人数，minProfit 代表最低盈利额，一串工作需要的人数 group = [2, 2]，每项工作对应的利润 profit = [2, 3]。要求实现最低盈利额，并且投入的人数和不能超过 n，这样的分配方案数目有多少个？

**「分析」**

在 01 背包问题的基础上，本题增加了最低盈利额的限制。设 dp[ i ][ j ][ k ] 为从 i 开始的工作中选取某些工作投入人力，在恰好 j 个人且盈利额不低于 k 的情况下，方案数目。状态转移方程如下

$$
\text{dp[ i ][ j ][ k ]} =
\begin{cases}
\text{dp[i + 1][ j ][ k ]}, &\quad \text{j } < \text{group[ i ]} \\
\begin{aligned}
& \text{dp[i + 1][ j ][ k ]} + \\
& \text{dp[i + 1][j - group[ i ]][max(0, k - profit[ i ])]}
\end{aligned}
, &\quad \text{j } \geq \text{group[ i ]}
\end{cases}
$$

边界条件 dp[ len ][ 0 ][ 0 ] = 1，表明不选任何工作时，投入的人数是 0，最低的盈利额也是 0，此时也是满足条件的一种方案。进一步，我们会发现，当 k = 0 即最低盈利额为 0，那么不论 i 和 j 为何值，dp[ i ][ j ][ 0 ] 均为 1。

$\text{k - profit[ i ]}$ 为负数，说明第 i 个工作的利润本身就超过 minProfit，这就意味着，在选择第 i 个工作前，最低盈利可以为 0，这个时候只要投入的人数不超过 j - group[ i ]，都被视为满足要求的方案。相反，如果我们约束 $\text{k} \geq \text{profit[ i ]}$，我们最终得到的结果是**恰好等于 minProfit**的方案数，因为 k - profit[ i ] 只会转移到第三维成为 0 的状态。

最终的答案是考察所有的工作且满足最低盈利额的条件下，对投入的人数遍历

$$
\text{ans} = \sum_{j = 0}^{n} \text{dp[ 0 ][ j ][ minProfit ]}
$$

```cpp
int profitableSchemes(int n, int minProfit, vector<int>& group,
                      vector<int>& profit) {
    int len = (int)group.size(), MOD = 1000000007;
    vector<vector<int>> dp(n + 1, vector<int> (minProfit + 1, 0));
    dp[0][0] = 1;
    for (int i = len - 1; i >= 0; i--) {
        for (int j = n; j >= group[i]; j--) {
            for (int k = 0; k <= minProfit; k++) {
                dp[j][k] += dp[j - group[i]][max(0, k - profit[i])];
                dp[j][k] %= MOD;
            }
        }
    }
    int ans = 0;
    for (int j = 0; j <= n; j++) {
        ans += dp[j][minProfit];
        ans %= MOD;
    }
    return ans;
}
```
时间复杂度 $O(len \times n \times minProfit)$，空间复杂度 $O(n \times minProfit)$，这里 len 是工作的个数 / 利润数组的长度，n 是总人数，minProfit 是最低盈利额。

这个题目也可以定义成 dp[ i ][ j ][ k ] 从第 i 个工作到最后一个工作中选取某几个，在投入不超过 j 个人且利润不低于 k 的条件下的方案数目。对应的初始化应该改成 dp[ i ][ j ][ 0 ] = 1，最后的结果是 dp[ 0 ][ n ][ minProfit ]。

```cpp
int profitableSchemes(int n, int minProfit, vector<int>& group,
                      vector<int>& profit) {
    int len = (int)group.size(), MOD = 1000000007;
    vector<vector<int>> dp(n + 1, vector<int> (minProfit + 1, 0));
    for (int j = 0; j <= n; j++) {
        dp[j][0] = 1;
    }
    for (int i = len - 1; i >= 0; i--) {
        for (int j = n; j >= group[i]; j--) {
            for (int k  = minProfit; k >= 0; k--) {
                dp[j][k] += dp[j - group[i]][max(0, k - profit[i])];
                dp[j][k] %= MOD;
            }
        }
    }
    return dp[n][minProfit];
}
```


## 完全背包
一个背包承重总量是 W，有 N 种物品，每一件有自己的价值 $v_{i}$ 和 重量 $w_{i}$，每一种类的物品无限供应，试问在不超过背包承重总量下，背包的价值最大。

**「分析」**

完全背包问题种，每一件物品的供应是无限的，但因为背包承重总量有限，可以放入的数目依然有限 $0 \leq c_{i} \leq \frac{W}{w_{i}}$，那么我们依然可以设 dp[ i ][ j ] 为从第 i 件物品到最后一件物品中选取若干件，在重量和不超过 j 的情况下最大价值。

$$
\text{dp[ i ][ j ]} =
\begin{cases}
\text{dp[i + 1][ j ]}, &\quad \text{j } < \text{w[ i ]} \\
\max
\begin{cases}
\text{dp[i + 1][ j ]} \\
\text{dp[ i ][j - w[ i ]] + v[ i ]}
\end{cases}, &\quad \text{j } \geq \text{w[ i ]}
\end{cases}
$$

在 01 背包问题中，dp[ i ][ j ] 是由 dp[i + 1][j - w[ i ]] + v[ i ] 转移而来，表达了首次将第 i 件物品加入到背包中。但是在完全背包问题里，每一件物品是可以多次放入的，那么 dp[ i ][j - w[ i ]] + v[ i ] 就已经包含了 dp[i + 1][j - w[ i ]] + v[ i ]。

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

$$
\text{dp[ j ]} = \max(\text{dp[ j ], dp[j - w[ i ]] + v[ i ]})
$$

```cpp
int completeKnapsack(vector<int> v, vector<int> w, int W) {
    int n = (int)v.size();
    vector<int> dp(W + 1, 0);
    for (int i = n - 1; i >= 0; i--) {
        for (int j = w[i]; j <= W; j++) {
            dp[j] = max(dp[j], dp[j - w[i]] + v[i]);
        }
    }
    return dp[W];
}
```

这里与 01 背包的代码只有一行不同，即 j 的遍历方向上。完全背包问题是有小到大遍历，因为某一件物品可以被多次放入；反而 01 背包问题对任一件物品最多只允许放入一次。

### 换零钱 I
给定一组硬币的面值 coins = [186, 419, 83, 408] 和一个总量 amount = 6249，试问用最少的硬币个数可以组成这个 amount。如果不能找到零钱组合，返回 -1。

**「分析」**

本题可以被理解成完全背包问题，每一枚硬币的价值为 1，求最小的价值。设 dp[ i ][ j ] 从第 i 个到最后一个硬币中选取若干个构成 j 数额的最少硬币个数。状态转移方程如下

$$
\text{dp[ i ][ j ]} =
\begin{cases}
\text{dp[i + 1][ j ]}, &\quad \text{j } < \text{coins[ i ]} \\
\max
\begin{cases}
\text{dp[i + 1][ j ]} \\
\text{dp[ i ][j - coins[ i ]] + 1}
\end{cases}
, &\quad \text{j } \geq \text{coins[ i ]}
\end{cases}
$$

因为求解的是最小个数，那么数组初始化成最大值，这里可以使用 amount + 1 作为最大值，因为，最小的零钱是 1。那么最多的硬币个数也不会超过 amount。当 anount = 0 时，我们不需要任何硬币，即 0。

```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount + 1, amount + 1);
    dp[0] = 0;
    for (int coin : coins) {
        for (int j = coin; j<= amount; j++) {
            dp[j] = min(dp[j], dp[j - coin] + 1);
        }
    }
    return dp[amount] == amount + 1 ? -1 : dp[amount];
}
```
时间复杂度 $O(n \times amount)$，空间复杂度 $O(amount)$，其中，n 表示零钱数组的长度，amount 表示需要组成的金额。

### 换零钱 II
同上题背景，求能够组合出给定金额的零钱方案数目

**「分析」**

设 dp[ i ][ j ] 从第 i 个到最后一个硬币中选取若干个组成金额是 j 的方案数目

$$
\text{dp[ i ][ j ]} = 
\begin{cases}
\text{dp[i + 1][ j ]}, &\quad \text{j } < \text{coins[ i ]} \\
\text{dp[i + 1][ j ] + dp[ i ][j - coins[ i ]]}, &\quad \text{j } \geq \text{coins[ i ]}
\end{cases}
$$

```cpp
int coinChange(int amount, vector<int>& coins) {
    vector<int> dp(amount + 1, 0);
    dp[0] = 1;
    for (int coin : coins) {
        for (int j = coin; j <= amount; j++) {
            dp[j] += dp[j - coin];
        }
    }
    return dp[amount];
}
```
时间复杂度 $O(\text{n} \times \text{amount})$，空间复杂度 $O(\text{amount})$，其中，n 表示零钱数组的长度，amount 表示需要组成的金额。

### 换零钱 III
同上题背景，依然求方案数目，但要求硬币具有先后顺序，例如 coins = [1, 2, 3]，组成 amount = 3 的方案有 [1, 1, 1]，[1, 2]，[2, 1]，[ 3 ] 4 种，注意到 [1, 2] 和 [2, 1] 是不一样的方案。

**「分析」**

同上题，设 dp[ j ] 为 金额为 j 的方案数目。不同之处在于两层循环的位置。**物品循环在外侧，数量循环在内测时，得到方案数的组合情况；反之，得到方案数的排列情况**

```cpp
int combinationSum41(vector<int>& coins, int amount) {
    vector<unsigned int> dp(amount + 1, 0);
    dp[0] = 1;
    for (int i = 0; i <= amount; i++) {
        for (int coin : coins) {
            if (i >= coin) {
                dp[i] += dp[i - coin];
            }
        }
    }
    return dp[amount];
}
```
时间复杂度 $O(n \times amount)$，空间复杂度 $O(amount)$，其中，n 表示零钱数组的长度，amount 表示需要组成的金额。

### 平方数的最小个数
给定一个正数 n = 13，它可以由若干个平方数相加而得，返回这些平方数的个数的最小值。13 = 4 + 9，即最小值为 2.

**「分析」**

方法 1：转化成完全背包问题，统计 1 - n 之间所有的平方数，之后按照完全背包问题的解法求值。

```cpp
int numSquares(int n) {
    if (n <= 0) { return 0; }
    vector<int> square;
    for (int i = 1; i <= n; i++) {
        int root = (int)sqrt(i);
        if (root * root == i) {
            square.emplace_back(i);
        }
    }
    vector<int> dp(n+1, n+1);
    dp[0] = 0;
    for (int s : square) {
        for (int j = s; j <= n; j++) {
            dp[j] = min(dp[j], dp[j - s] + 1);
        }
    }
    return dp[n];
}
```
时间复杂度 $O(n \sqrt{n})$，空间复杂度 $O(n)$，其中 n 是给定的值。

方法 2：设 dp[ i ] 为构成 i 的最小平方数的个数。

$$
\text{dp[ i ]} = \min_{k \in [1, i)} (\text{dp[ k ] + dp[i - k]})
$$

```cpp
int numSquares(int n) {
    if (n < 1) { return 0; }
    int *dp = new int [n+1]{};
    dp[1] = 1;
    for (int i = 2; i <= n; i++)
    {
        if (i * i <= n) { dp[i * i] = 1; }
        if (dp[i]) { continue; }
        int min = INT_MAX;
        for (int j = 1; j <= i/2; j++)
        {
            if (min > dp[j] + dp[i-j])
            {
                min = dp[j] + dp[i-j];
            }
        }
        dp[i] = min;
    }
    int ans = dp[n];
    delete []dp;
    return ans;
}
```
时间复杂度 $O(n^{2})$，空间复杂度 $O(n)$，其中 n 是给定的值。

