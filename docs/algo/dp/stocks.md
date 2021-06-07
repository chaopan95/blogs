## 一次交易
给定一个数组prices，它的第i个元素 prices[i] 表示一支给定股票第 i 天的价格。你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

输入：[7, 1, 5, 3, 6, 4] 输出：5

解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。

**「分析」**

我们的目标是求最大利润 = 卖出时的价格 - 买入时的价格，那么针对每一天i，我们都可以得到一个这一天的最大利润

$$
\text{maxProfit[ i ] = stocks[ i ] - } \min_{j \in [0, i)} \text{stocks[ j ]}
$$

很容易可以写出一个$O(n^{2})$时间复杂度的方法。其实我们可以观察到，对于每一个i，我们重复求解i之前的最小股票价格，这一步是冗余的，因为如果我们知道了[0, i)的最小值x，那么[0, i]的最小值 a‘ = min(a, stocks[ i ])，从而避免了重复遍历。于是我们有一个高效的算法

```cpp
int minPrice = INT_MAX, maxProfit = INT_MIN;
for (int stock : stocks) {
    minPrice = min(minPrice, stock);
    maxProfit = max(maxProfit, stock - minPrice);
}
return maxProfit;
```
时间复杂度 $O(n)$，空间复杂度 $O(1)$

## 多次交易
对于一只股票的，允许多次交易（买入、卖出为一次交易），未卖出股票时不允许买入，买入和卖出不在同一天。求最大收益。

**「分析」**

对于某一天 i ，我们会有两种状态：持有股票和不持有股票，那么我们可以设 dp[ i ][ 0 ] 表示 i 天不持有股票的最大受益，dp[ i ][ 1 ] 表示 i 天持有股票的最大收益。

$$
\begin{aligned}
& \text{dp[ i ][ 0 ] = } \max(\text{dp[i - 1][ 0 ], dp[i - 1][ 1 ] + prices[ i ]}) \\
& \text{dp[ i ][ 1 ] = } \max(\text{dp[i - 1][ 0 ] - prices[ i ], dp[i - 1][ 1 ]})
\end{aligned}
$$

我们注意到，第 i 天的两种状态只与第 i-1 天的两种状态有关系，因此我们可以对时间复杂度进行优化。最后一天，不持有股票的收益一定高于持有股票的收益。

```cpp
int n = (int)prices.size();
int dp0 = 0, dp1 = -prices[0];
for (int i = 1; i < n; i++) {
    int tmp0 = max(dp0, dp1 + prices[i]);
    int tmp1 = max(dp0 - prices[i], dp1);
    dp0 = tmp0;
    dp1 = tmp1;
}
return dp0;
```
时间复杂度$O(n)$，空间复杂度$O(1)$

## 至多k次交易
对于一只股票，允许最多k次交易（买入和卖出为一次交易），求其最大的利润。

**「分析」**

因为题目要求持有股票时不能再买入，那么对于任意一天 i ，只有两种情况：这一天有股票和这一天没有股票。再根据交易的次数，设 dp[ i ][ j ][ 0 ] 为第 i 天处于第 j 次交易期间且这一天结束后未持有股票，dp[ i ][ j ][ 1 ] 为第 i 天处于第 j 次交易且这一天结束后持有股票。我们可以写出状态转移方程

$$
\begin{aligned}
& \text{dp[ i ][ j ][ 0 ] = } \max(\text{dp[i - 1][ j ][ 0 ], dp[i - 1][ j ][ 1 ] + prices[ i ]}) \\
& \text{dp[ i ][ j ][ 1 ] = } \max(\text{dp[i - 1][j - 1][ 0 ] - prices[ i ], dp[i - 1][ j ][ 1 ]})
\end{aligned}
$$

对于 dp[ i ][ j ][ 0 ] 这一天未持有股票，那么可能的状态来源是(1) i-1 这一天也未持有股票，或者(2) i-1这一天持有股票而在 i 这一天卖出，那么这一天持有的股票一定是第 j 次交易产生的。同样地，对于 dp[ i ][ j ][ 1 ] 这一天持有股票，可能的状态来源是(1) i-1 这天未持有而 i 这一天买入股票并且 i-1 属于第 j-1 次交易或者（1）i-1这一天持有股票，直接过渡而来。i = 0 表示的是第一天的股价，对于第 j = 1, 2, ..., k 次交易，如果在 i = 0 处发生卖出，dp[ 0 ][ j ][ 1 ] = -prices[ 0 ]。最终的利润和发生在最后一天不持有股票中的某一个 j 值，即

$$
\text{target} = \max_{j \in {1, 2, ..., k}} \text{dp[n - 1][ j ][ 0 ]}
$$

我们还可以发现，第 i 天的状态只与第 i-1天的状态有关系，因为在空间上可以优化。设 dp0[ j ] 表示第 j 次交易未持有股票的最大利润，dp1[ j ] 表示第 j 次交易持有股票的最大利润

$$
\begin{aligned}
& \text{dp0[ j ] = } \max(\text{dp0[ j ], dp1[ j ] + prices[ i ]}) \\
& \text{dp1[ j ] = } \max(\text{dp0[j - 1] - prices[ i ], dp1[ j ]})
\end{aligned}
$$

```cpp
int maxProfit(int k, vector<int>& prices) {
    int n = (int)prices.size();
    if (n == 0) { return 0; }
    vector<int> dp0(k+1, 0), dp1(k+1, -prices[0]);
    for (int i = 1; i < n; i++) {
        for (int j = 1; j <= k; j++) {
            dp0[j] = max(dp0[j], dp1[j] + prices[i]);
            dp1[j] = max(dp0[j-1] - prices[i], dp1[j]);
        }
    }
    return *max_element(dp0.begin(), dp0.end());
}
```
时间复杂度 $O(nk)$，空间复杂度 $O(k)$

## 带有冷冻期的交易
对于一只股票，允许多次交易（买入和卖出为一次交易），但是每一次交易完成后的下一天为冷冻期，不能购买股票，求其最大的利润。

**「分析」**

对于第 i 天结束，我们会出现三种状态：（1）这一天结束后准备进入未持有股票且不会处于冷冻期的状态，（2）这一天结束准备进入未持有股票但是处于冷冻期的状态，（3）这一天结束会进入持有股票的状态

dp[ i ][ 0 ] 表示状态（1），前一天 i-1 的状态可能是（1）和（2）；dp[ i ][ 1 ] 表示状态（2），前一天的状态一定是（3），并且在第 i 天卖出股票，因为只有这样才会进入冷冻期；dp[ i ][ 2 ] 表示状态（3），前一天的可能状态是（1）和（3），因为第 i 天结束是要进入持有股票的状态，那么这一天不可能是冷冻期。

$$
\begin{aligned}
& \text{dp[ i ][ 0 ] = } \max(\text{dp[i - 1][ 0 ], dp[i - 1][ 1 ]}) \\
& \text{dp[ i ][ 1 ] = dp[i - 1][ 2 ] + prices[ i ]} \\
& \text{dp[ i ][ 2 ] = } \max(dp[i - 1][ 0 ] - prices[ i ], dp[i - 1][ 2 ])
\end{aligned}
$$

我们观察到，第 i 天的状态只会与前一天的状态有关，因此，我们只需要保留前一天的状态即可。

$$
\begin{aligned}
& \text{dp0 } \leftarrow \max(\text{dp0, dp1}) \\
& \text{dp1 } \leftarrow \text{dp2 + prices[i]} \\
& \text{dp2 } \leftarrow \max(\text{dp0 - prices[ i ], dp2})
\end{aligned}
$$

```cpp
int n = (int)prices.size();
if (n == 0) { return 0; }
int dp0 = 0, dp1 = 0, dp2 = -prices[0];
for (int i = 1; i < n; i++) {
    dp2 = max(dp0 - prices[i], dp2);
    dp0 = max(dp0, dp1);
    dp1 = dp2 + prices[i];
}
return max(dp0, dp1);
```
时间复杂度 $O(n)$，空间复杂度 $O(1)$

## 股票价格的跨度
给定一个股票价格的序列stocks = [100, 80, 60, 70, 60, 75, 85], 求其每一天的股票跨度（从i天开始向前计数，股票价格小于等于当前价格），输出结果[1, 1, 1, 2, 1, 4, 6]

**「分析」**

按照题意，可以写出一个 $O(n^{2})$ 的算法，但是这样会超时。我们可以观察到，对于某一天的股票 stocks[ i ]，必然会逐个与i之前的元素比较，终于碰到一个比它大的或者没有，这时，我们将这个股票价格的跨度记录下来，对于下一个股价 stocks[i + 1]

$$
\text{count[ stocks[i + 1] ]} =
\begin{cases}
\text{count[ stocks[ i ] ]} + 1, & \quad \text{stocks[ i + 1 ]} \geq \text{stocks[ i ]} \\
1, & \quad \text{otherwise}
\end{cases}
$$

单调栈适合解决此类问题

```cpp
stack<int> stk, cnt;
vector<int> ans;
for (int s : stocks) {
    int num = 1;
    while (!stk.empty() && stk.top() <= s) {
        num += cnt.top();
        cnt.pop();
        stk.pop();
    }
    ans.emplace_back(num);
    cnt.push(num);
    stk.push(s);
}
return ans;
```
时间复杂度 $O(n)$，空间复杂度 $O(n)$
