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

### 出勤方案数
给定一个正整数 n，表示天数。一个学生每天的出勤状态是：缺勤（A）、迟到（L）、出勤（P），如果在 n 天中，他能获得出勤奖励的条件是：（1）不超过 1 次缺勤，（2）连续迟到天数不超过 2 天，试问这样的出勤方式有多少种？

**「分析」**

设 dp[ i ][ j ][ k ] 表示前 i 天有 j 次缺勤（A）且以连续 k 个迟到（L）为结尾的出勤方案数目。因为最多缺勤一次，所以 $j \in \{0, 1\}$；最多连续两次迟到，$k \in \{0, 1, 2\}$。初始状态 dp[ 0 ][ 0 ][ 0 ] = 1。

* 当第 i 天是 P 时，结尾的连续 L 个数清零

$$
\text{dp[ i ][ j ][ 0 ]} = \sum_{k \in \{0, 1, 2\}} \text{dp[ i ][ j ][ 0 ] + dp[i - 1][ j ][ k ]}
$$

* 当第 i 天是 A 时，结尾的连续 L 个数清零，且上一个的状态一定是 0 个 A

$$
\text{dp[ i ][ 1 ][ 0 ]} = \text{dp[ i ][ 1 ][ 0 ] + dp[i - 1][ 0 ][ k ]}
$$

* 当第 i 天是 L 时，结尾的连续 L 个数加一

$$
\text{dp[ i ][ j ][ k ]} = \sum_{k \in \{1, 2\}} \text{dp[ i ][ j ][ k ] + dp[i - 1][ j ][k - 1]}
$$

总共 6 个状态，整理如下

$$
\begin{aligned}
& \text{dp00 = dp00 + dp01 + dp02} \\
& \text{dp01 = dp00} \\
& \text{dp02 = dp01} \\
& \text{dp10 = dp00 + dp01 + dp02 + dp10 + dp11 + dp12} \\
& \text{dp11 = dp10} \\
& \text{dp12 = dp11}
\end{aligned}
$$

状态转移矩阵

$$
M =
\begin{bmatrix}
1 & 1 & 0 & 1 & 0 & 1 \\
1 & 0 & 1 & 1 & 0 & 0 \\
1 & 0 & 0 & 1 & 0 & 0 \\
0 & 0 & 0 & 1 & 1 & 0 \\
0 & 0 & 0 & 1 & 0 & 1 \\
0 & 0 & 0 & 1 & 0 & 0
\end{bmatrix} \quad
\text{dp} =
\begin{bmatrix}
\text{dp00} \\
\text{dp01} \\
\text{dp02} \\
\text{dp10} \\
\text{dp11} \\
\text{dp12}
\end{bmatrix}
$$

$$
\text{dpNew} = \text{M dpOld} = M^{n} \text{ dpOld}
$$

```cpp
int checkRecord(int n) {
    vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>
                                   (2, vector<int> (3, 0)));
    dp[0][0][0] = 1;
    const int mod = 1000000007;
    for (int i = 1; i <= n; i++) {
        for (int j = 0; j <= 1; j++) {
            for (int k = 0; k <= 2; k++) {
                dp[i][j][0] = (dp[i][j][0] + dp[i - 1][j][k]) % mod;
            }
        }
        
        for (int k = 0; k <= 2; k++) {
            dp[i][1][0] = (dp[i][1][0] + dp[i - 1][0][k]) % mod;
        }
        
        for (int j = 0; j <= 1; j++) {
            for (int k = 1; k <= 2; k++) {
                dp[i][j][k] = (dp[i][j][k] + dp[i - 1][j][k - 1]) % mod;
            }
        }
    }
    int ans = 0;
    for (int j = 0; j <= 1; j++) {
        for (int k = 0; k <= 2; k++) {
            ans = (ans + dp[n][j][k]) % mod;
        }
    }
    return ans;
}
```
时间复杂度：$O(n)，$空间复杂度：$O(n)$

使用快速幂降低时间复杂度。

```cpp
const int mod = 1000000007;

vector<vector<int>> multiply(vector<vector<int>> a,
                             vector<vector<int>> b) {
    vector<vector<int>> ans(6, vector<int> (6, 0));
    for (int i = 0; i < 6; i++) {
        for (int j = 0; j < 6; j++) {
            for (int k = 0; k < 6; k++) {
                ans[i][j] = (ans[i][j] + (long)a[i][k] * b[k][j]) % mod;
            }
        }
    }
    return ans;
}

vector<vector<int>> nMultiply(vector<vector<int>> M, int n) {
    vector<vector<int>> ans {
        {1, 0, 0, 0, 0, 0},
        {0, 1, 0, 0, 0, 0},
        {0, 0, 1, 0, 0, 0},
        {0, 0, 0, 1, 0, 0},
        {0, 0, 0, 0, 1, 0},
        {0, 0, 0, 0, 0, 1}
    };
    while (n) {
        if (n & 1) {
            ans = multiply(ans, M);
        }
        M = multiply(M, M);
        n >>= 1;
    }
    return ans;
}

int checkRecord(int n) {
    int ans = 0;
    vector<vector<int>> M {
        {1, 1, 0, 1, 0, 0},
        {1, 0, 1, 1, 0, 0},
        {1, 0, 0, 1, 0, 0},
        {0, 0, 0, 1, 1, 0},
        {0, 0, 0, 1, 0, 1},
        {0, 0, 0, 1, 0, 0}
    };
    M = nMultiply(M, n);
    for (int j = 0; j < 6; j++) {
        ans = (ans + M[0][j]) % mod;
    }
    return ans;
}
```
时间复杂度：$O(\log n)，$空间复杂度：$O(1)$

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


## 优美序列
给定一个正整数 n，如果将 1 - n 形成一个排列 seq，且在 i 位置上满足 i % seq[ i ] == 0 或 seq[ i ] % i == 0，我们称之为一个【优美序列】。试问对于 n 而言，总共有多少个【优美序列】。

**「分析」**

【回溯】能不能产生一个新的序列，主要看某一个位置 i 和这个位置对应的数值是否整除。我们可以逐个位置填充 1 - n 的元素，如果能得到一个完整的序列，则总结果加一。

```cpp
vector<vector<int>> next;
int dfs(bool *vis, int idx, int n) {
    if (idx == n + 1) {
        return 1;
    }
    int ans = 0;
    for (int ele : next[idx]) {
        if (vis[ele]) { continue; }
        vis[ele] = true;
        ans += dfs(vis, idx + 1, n);
        vis[ele] = false;
    }
    return ans;
}
int countArrangement(int n) {
    bool *vis = new bool [n + 1] {};
    next.resize(n + 1, vector<int> {});
    for (int i = 1; i <= n; i++) {
        for (int j = 1; j <= n; j++) {
            if (i % j == 0 || j % i == 0) {
                next[i].emplace_back(j);
            }
        }
    }
    int ans = dfs(vis, 1, n);
    delete []vis;
    return ans;
}
```
时间复杂度：$O(n!)$，空间复杂度：$O(n^{2})$

【状态压缩 + 动态规划】形成一个序列，必然有某些元素先被填充，剩下的元素后被填充，那么我们可以用一个 32-bit 的整数表示这样的序列，这就叫【状态压缩】。

```cpp
int countArrangement(int n) {
    vector<int> dp(1 << n, 0);
    dp[0] = 1;
    for (int mask = 1; mask < (1 << n); mask++) {
        int numOf1 = __builtin_popcount(mask);
        for (int j = 0; j < n; j++) {
            if ((mask & (1 << j)) &&
                (numOf1 % (j + 1) == 0 || (j + 1) % numOf1 == 0)) {
                dp[mask] += dp[mask ^ (1 << j)];
            }
        }
    }
    return dp[(1 << n) - 1];
}
```
时间复杂度：$O(n 2^{n})$，空间复杂度：$O(2^{n}})$


