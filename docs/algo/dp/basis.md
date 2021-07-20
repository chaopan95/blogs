## 简介
动态规划（Dynamic programming，简称 DP）将原问题拆分成若干子问题（子问题有重叠），通过分别求解子问题，得到原问题解的一种算法。

## 要素
如果一个问题具有以下两个要素，则可以通过动态规划的方法求解

* `最优子结构（optimal structure）`
    - 原问题的解是可以由子问题得到的（递推或者递归），反之，不能称之为具有最优子结构

* `重叠子问题（overlap subproblem）`
    - 当某些子问题被重复遍历（或者原问题被分解成多个相同的子问题）时，这种情况称为重叠子问题

## 计算步骤
* `（1）`描述最优解的结构，即用一个状态表示最优解

* `（2）`以状态转移方程的形式，递归地定义最优解的值

* `（3）`自下而上（bottom-top）或自上而下（bottom-top）地计算最优解的值

* `（4）`根据（3）中的过程值，构造最优解；如果只需要最优解的值，则无需本步骤

## 动态规划的特点
* 以空间换时间：保存了计算过程的中间值，避免了重复计算

* 自上而下（bottom-top）或者递归是一种备忘录（memorization）方法

* 严格来说，动态规划只有自下而上的方式；如果所有的子问题都会被计算一次，那么动态规划具有优势，反之（剪枝情况），备忘录法具有优势

## 动态规划与贪心的比较
* `相同点：`具备最优子结构

* `不同点：`
    - 动态规划的子问题是重叠的，贪心的子问题不重叠
    - 动态规划不具有贪心选择的性质
    - 贪心的前进路线是一条线，动态规划是一个DAG

## 序列型DP
??? note "[「Leetcode 740. 删除并获得点数」]()"

    ```cpp
    /*
    给你一个整数数组 nums ，你可以对它进行一些操作。

    每次操作中，选择任意一个 nums[i] ，删除它并获得 nums[i] 的点数。
    之后，你必须删除每个等于 nums[i] - 1 或 nums[i] + 1 的元素。

    开始你拥有 0 个点数。返回你能通过这些操作获得的最大点数。

    示例 1：
    输入：nums = [3,4,2]
    输出：6
    解释：
    删除 4 获得 4 个点数，因此 3 也被删除。
    之后，删除 2 获得 2 个点数。总共获得 6 个点数。

    示例 2：
    输入：nums = [2,2,3,3,3,4]
    输出：9
    解释：
    删除 3 获得 3 个点数，接着要删除两个 2 和 4 。
    之后，再次删除 3 获得 3 个点数，再次删除 3 获得 3 个点数。
    总共获得 9 个点数。
    */
    class Solution {
    public:
        int deleteAndEarn(vector<int>& nums) {
            if (nums.empty()) { return 0; }
            map<int, int> cnt;
            for (int num : nums) { cnt[num]++; }
            map<int, int>::iterator iter = ++cnt.begin();
            int lastN = cnt.begin()->first, lastC = cnt.begin()->second;
            int a = 0, b = lastN * lastC;
            for (; iter != cnt.end(); iter++) {
                int na = max(a, b), nb = iter->first * iter->second;
                if (lastN + 1 == iter->first) {
                    nb += a;
                }
                else {
                    nb += max(a, b);
                }
                a = na;
                b = nb;
                lastN = iter->first;
            }
            return max(a, b);
        }
    };
    ```

## 区间形DP
### 删除子串的最小操作数
给定一个字符串如“aaabbcbaa”，每一次可以删除一个由同一字符组成的子串，删除后剩余部分自动拼接。重复进行直到字符串为空，求最小的删除次数。举例如下：

$$
\begin{matrix}
& aaabb\underline{c}baa \\
& \downarrow \\
& aaa\underline{bbb}aa \\
& \downarrow \\
& \underline{aaaaa}
\end{matrix}
$$

**「分析」**

设 dp[ i ][ j ] 为字符串 s[i : j] 删除有相同字符组成的子串的最小次数。如果两端的字符相同，那么状态跳转到 s[i : j - 1]；如果两端字符串不想等，那么我们寻找一个最佳切分点 k ，使切分出来的两段和最小。特别地，对于每一个单字符，最小切割次数是 1 。

$$
\text{dp[ i ][ j ]} =
\begin{cases}
\text{dp[ i ][j - 1]}, & \quad \text{s[ i ] == s[ j ]} \\
\min_{k \in [i, j)}(\text{dp[ i ][ k ] + dp[k + 1][ j ]}), & \quad \text{otherwise}
\end{cases}
$$

```cpp
vector<vector<int>> dp(n, vector<int>(n, 0));
for (int i = n - 1; i >= 0; i--) {
    dp[i][i] = 1;
    for (int j = i + 1; j < n; j++) {
        if (s[i] == s[j]) {
            dp[i][j] = dp[i][j-1];
        }
        else {
            int _min = INT_MAX;
            for (int k = i; k < j; k++) {
                _min = min(_min, dp[i][k] + dp[k+1][j]);
            }
            dp[i][j] = _min;
        }
    }
}
return dp[0][n-1];
```
时间复杂度 $O(n^{3})$，空间复杂度 $O(n^{2})$

### 删除回文串的最小次数
给定一个字符串如“aabc”，每一次删除一个回文子串，直到字符串为空，求最小删除次数。

**「分析」**

设 dp[ i ][ j ] 为字符串 s[i : j] 的最小删除次数。任何一个字符都会面临下面的两种情况

$$
\begin{matrix}
\text{case 1: } & {\color{Red} a}xxxx \\
\text{case 2: } & {\color{Red} a}xx{\color{Red} a}xxx
\end{matrix}
$$

case 1 只删除一个字符，case 2 删除一个区间的字符

$$
\text{dp[ i ][ j ]} =
\begin{cases}
\text{1}, & \quad i == j \\
\max(\text{dp[i + 1][ j ] + 1, dp[i + 2][ j ] + 1}), & \quad \text{s[ i ] == s[i + 1]} \\
\max_{k \in [i + 2, j]} (\text{dp[i + 1][k - 1] + dp[k + 1][ j ]}), & \quad \text{s[ i ] == s[ k ]}
\end{cases}
$$

```cpp
vector<vector<int>> dp(n+1, vector<int>(n+1, 0));
dp[n - 1][n - 1] = 1;
for (int i = n - 2; i >= 0; i--) {
    dp[i][i] = 1;
    dp[i][i + 1] = 1 + (s[i] != s[i + 1]);
    for (int j = i + 2; j < n; j++) {
        dp[i][j] = 1 + dp[i+1][j];
        if (s[i] == s[i+1]) {
            dp[i][j] = min(dp[i][j], 1 + dp[i+2][j]);
        }
        for (int k = i + 2; k <= j; k++) {
            if (s[i] == s[k]) {
                dp[i][j] = min(dp[i][j], dp[i+1][k-1] + dp[k+1][j]);
            }
        }
    }
}
return dp[0][n-1];
```
时间复杂度 $O(n^{3})$，空间复杂度 $O(n^{2})$