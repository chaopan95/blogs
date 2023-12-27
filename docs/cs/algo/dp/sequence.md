## 基础
序列型动态规划表示状态成一个序列，可以是无环图、也可以是有环图。


## 题目
### 正则表达式匹配
给你一个字符串 s 和一个字符规律 p，请你来实现一个支持 '.' 和 '*' 的正则表达式匹配。'.' 匹配任意单个字符，'*' 匹配零个或多个前面的那一个元素。例如，s = "ab"，p = ".*"，返回 true

**「分析」**

「区间型dp」dp[i][j] 表示 s[i:] 和 p[j:] 匹配。

1）检查 s[i] 和 p[j] 是否匹配，p[j] 可以是 '.'

2）如果 p[j+1] 是 '*'，dp[i][j] 可以由 dp[i][j+2] 转移，也可以由 dp[i+1][j] 转移；反之，dp[i][j] 只会由 dp[i+1][j] 转移。考虑 dp[i+1][j] 时，不能忽略 s[i] 和 p[j] 是否匹配。

```cpp
bool isMatch(string s, string p) {
    int ns = int(s.length()), np = int(p.length());
    bool **dp = new bool *[ns+1];
    for (int i = 0; i <= ns; i++) { dp[i] = new bool [np+1]{}; }
    dp[ns][np] = true;
    for (int i = ns; i >= 0; i--) {
        for (int j = np-1; j >= 0; j--) {
            bool ijMatch = i < ns && (s[i] == p[j] || p[j] == '.');
            if (j < np - 1 && p[j+1] == '*') {
                dp[i][j] = dp[i][j+2] || (ijMatch && dp[i+1][j]);
            } else {
                dp[i][j] = ijMatch && dp[i+1][j+1];
            }
        }
    }
    int res = dp[0][0];
    for (int i = 0; i <= ns; i++) { delete []dp[i]; }
    delete []dp;
    return res;
}
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(n^{2})$。

### 最长回文子串
给你一个字符串 s，找到 s 中最长的回文子串。例如，s = "babad"，返回 “bab”

**「分析」**

「动态规划」设 dp[i][j] 表示子串 s[i:j] 是否是回文串。转移方程如下：

$$
dp[i][j] = 
\begin{cases}
dp[i+1][j-1], & s[i] == s[j] \\
\text{False}, &\text{else}
\end{cases}
$$

```cpp
string longestPalindrome(string s) {
    int n = int(s.length()), pos = 0, len = 1;
    if (n < 2) { return s; }
    bool **dp = new bool *[n];
    for (int i = 0; i < n; i++) {
        dp[i] = new bool [n]{};
        dp[i][i] = true;
    }
    for (int i = 0; i < n-1; i++) {
        if (s[i] == s[i+1]) {
            dp[i][i+1] = true;
            pos = i;
            len = 2;
        }
    }
    for (int k = 2; k < n; k++) {
        for (int i = 0; i < n-k; i++) {
            int j = i + k;
            if (s[i] == s[j] && dp[i+1][j-1]) {
                pos = i;
                len = k + 1;
                dp[i][j] = true;
            }
        }
    }
    for (int i = 0; i < n; i++) { delete []dp[i]; }
    delete []dp;
    return s.substr(pos, len);
}
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(n^{2})$，这里 n 表示 s 的长度。

这里可以使用中心扩散的方法，对于每一个位置的字符可以向两侧扩散，直到碰到不能构成回文子串停止。需要分子串为奇数和偶数两种情况。

### 不同的二叉搜索树个数
给你一个整数 n ，求恰由 n 个节点组成且节点值从 1 到 n 互不相同的 二叉搜索树 有多少种？返回满足题意的二叉搜索树的种数。例如，n = 3，返回 5。

**「分析」**

Catalan number

$$
\begin{aligned}
& C_{n} =
\begin{cases}
1, &\quad n = 0 \\
\frac{2(2n+1)}{n+2} C_{n-1}, &\quad n = 1, 2, 3, ...
\end{cases} \\
& C_{n} = \frac{(2n)!}{(n+1)! n!}
\end{aligned}
$$

```cpp
int numTrees(int n) {
    long C = 1;
    for (int i = 0; i < n; i++) {
        C = C * 2 * (2 * i + 1) / (i + 2) ;
    }
    return int(C);
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$。

### 删除并获得点数
给你一个整数数组 nums = [3, 4, 2]，你可以对它进行一些操作。每次操作中，选择任意一个 nums[i] ，删除它并获得 nums[i] 的点数。之后，你必须删除每个等于 nums[i] - 1 或 nums[i] + 1 的元素。开始你拥有 0 个点数。返回你能通过这些操作获得的最大点数。给出的例子的结果是 6（删除 4 获得 4 个点数，因此 3 也被删除，之后，删除 2 获得 2 个点数。总共获得 6 个点数）

```cpp
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


### 优美序列
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
时间复杂度：$O(n 2^{n})$，空间复杂度：$O(2^{n})$

### 至多 k 次中转的最低价格
给一定一个航班列表 flights = [[0, 1, 100], [1, 2, 100], [0, 2, 500]]，表示从 i 到 j 的单向航班的价格，试问至多 k 次中转，从 src 到 dst 的最低价格是多少？

**「分析」**

【动态规划】设 dp[ t ][ i ] 为从 src 经过 t 次中转到 i 的最低价格，状态转移方程如下

$$
\text{dp[ t ][ i ]} = \min_{(j, i) \in E} \text{dp[t - 1][ j ] + cost[ j ][ i ]}
$$

初始状态

$$
\text{dp[ 0 ][ i ]} =
\begin{cases}
0, &\quad i = \text{dst} \\
\text{INF}, &\quad i \neq \text{dst}
\end{cases}
$$

```cpp
int findCheapestPrice(int n, vector<vector<int>>& flights,
                      int src, int dst, int k) {
    int MAX = 10000 * n + 1;
    vector<vector<int>> dp(k + 2, vector<int>(n, MAX));
    dp[0][src] = 0;
    for (int t = 1; t <= k + 1; t++) {
        for (auto flight : flights) {
            dp[t][flight[1]] = min(dp[t][flight[1]],
                                   dp[t-1][flight[0]] +
                                   flight[2]);
        }
    }
    int ans = MAX;
    for (int t = 0; t <= k + 1; t++) {
        ans = min(ans, dp[t][dst]);
    }
    return ans == MAX ? -1 : ans;
}
```
时间复杂度：$O(km)$，空间复杂度：$O(kn)$，其中 m 是航班列表的长度，n 是节点的个数

### 斐波那契数列第 n 项
$$
\text{fib}(n) = 
\begin{cases}
0, & \quad n = 0 \\
1, & \quad n = 1 \\
\text{fib}(n - 1) + \text{fib}(n), & \quad n \geq 2
\end{cases}
$$

**「分析」**
【动态规划 + 快速幂】根据递推公示构造状态转移矩阵

$$
\begin{aligned}
\begin{bmatrix}
f_{n} \\
f_{n - 1}
\end{bmatrix} &=
\begin{bmatrix}
f_{n - 1} + f_{n - 2} \\
f_{n - 1}
\end{bmatrix} \\
&=
\begin{bmatrix}
1 & 1 \\
1 & 0
\end{bmatrix} 
\begin{bmatrix}
f_{n - 1} \\
f_{n - 2}
\end{bmatrix} \\
&=
\begin{bmatrix}
1 & 1 \\
1 & 0
\end{bmatrix}^{2} 
\begin{bmatrix}
f_{n - 2} \\
f_{n - 3}
\end{bmatrix} \\
&= \quad \quad \quad \vdots \\
&=
\begin{bmatrix}
1 & 1 \\
1 & 0
\end{bmatrix}^{n - 1}
\begin{bmatrix}
f_{1} \\
f_{0}
\end{bmatrix}
\end{aligned}
$$

```cpp
class Solution {
public:
    int mod = 1000000007;
    int fib(int n) {
        if (n < 2) { return n; }
        vector<vector<long>> M {
            {1, 1},
            {1, 0}
        };
        M = nMultiply(M, n - 1);
        return M[0][0];
    }
    vector<vector<long>> nMultiply(vector<vector<long>> a, int n) {
        vector<vector<long>> ans {
            {1, 0},
            {0, 1}
        };
        while (n) {
            if (n % 2 != 0) {
                ans = multiply(ans, a);
            }
            n >>= 1;
            a = multiply(a, a);
        }
        return ans;
    }
    vector<vector<long>> multiply(vector<vector<long>> a,
                                  vector<vector<long>> b) {
        vector<vector<long>> ans {
            {0, 0},
            {0, 0}
        };
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                ans[i][j] = ((a[i][0] * b[0][j]) % mod +
                             (a[i][1] * b[1][j]) % mod) % mod;
            }
        }
        return ans;
    }
};
```
时间复杂度：$O(\log n)$，空间复杂度：$O(1)$

### 访问完所有房间的第一天
你需要访问 n 个房间，房间从 0 到 n - 1 编号。同时，每一天都有一个日期编号，从 0 开始，依天数递增。你每天都会访问一个房间。

最开始的第 0 天，你访问 0 号房间。给你一个长度为 n 且 下标从 0 开始 的数组 nextVisit 。在接下来的几天中，你访问房间的 次序 将根据下面的 规则 决定：

假设某一天，你访问 i 号房间。
* 如果算上本次访问，访问 i 号房间的次数为 奇数 ，那么 第二天 需要访问 nextVisit[i] 所指定的房间，其中 0 <= nextVisit[i] <= i 。
* 如果算上本次访问，访问 i 号房间的次数为 偶数 ，那么 第二天 需要访问 (i + 1) mod n 号房间。

请返回你访问完所有房间的第一天的日期编号。题目数据保证总是存在这样的一天。由于答案可能很大，返回对 109 + 7 取余后的结果。

**「分析」**

【动态规划】设 dp[ i ] 为第一次访问第 i 个房间的所需要的天数。根据题意，如果要第一次访问第 i 个房间，那么前面的 i - 1 个房间访问次数必须是偶数，并且一定是由第 i - 1 个房间【加一天】而来的。如果 nextVisit[i - 1] == i- 1，那么下一天访问的依据是 i - 1 房间，再加一天才能到 i 房间。如果 nextVisit[i - 1] != i- 1，我们需要从 nextVisit[i - 1] 访问到 i - 1此时需要 dp[i - 1] - dp[nextVisit[i - 1]]（前缀和）

$$
\text{dp[ i ]} = 
\begin{cases}
\text{dp[i - 1]} + 2, &\quad \text{nextVist[i - 1] == i - 1} \\
\text{dp[i - 1]} + 1 + \text{dp[i - 1] - dp[nextVisit[i - 1]]} + 1, &\quad \text{otherwise}
\end{cases}
$$

整理得到状态转移方程

$$
\text{dp[ i ] = dp[i - 1]} + 1 + \text{dp[i - 1] - dp[nextVisit[i - 1]]} + 1
$$

```cpp
int firstDayBeenInAllRooms(vector<int>& nextVisit) {
    int mod = 1000000007, n = (int)nextVisit.size();
    vector<int> dp(n, 0);
    for (int i = 1; i < n; i++) {
        dp[i] = (2 * dp[i - 1] - dp[nextVisit[i - 1]] + 2) % mod;
        if (dp[i] < 0) {
            dp[i] += mod;
        }
    }
    return dp[n - 1];
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$，n 为 nextVisit 的长度

### 圆圈中最后剩下的数字
0,1,···,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字（删除后从下一个数字开始计数）。求出这个圆圈里剩下的最后一个数字。例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

**【分析】**

【动态规划】假设长度为 n - 1 的圆圈最后剩下的数字是 x，那么长度为 n 的圆圈剩下的元素是从 x 开始的第 m 个元素，因为我们只需要从 n - 1 的状态多走一步即可到达 n。

$$
\text{dp[ n ] = (m + dp[n - 1]) mod n}
$$

```cpp
int lastRemaining(int n, int m) {
    int a = 0;
    for (int i = 1; i < n; i++) {
        a = (a + m) % (i + 1);
    }
    return a;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$

### n 个骰子的点数概率
给定一个正整数 n 表示骰子的个数，求这 n 个骰子出现的点和概率，结果按照点和（非概率）从小到大的顺序，如 n = 2，点和为 [6, 7, 8, 9, 10, 11, 12]，对应的概率是 [0.02778, 0.05556, 0.08333, 0.11111, 0.13889, 0.16667, 0.13889, 0.11111, 0.08333, 0.05556, 0.02778]。

**「分析」**

【动态规划】设 dp[ i ][ j ] 表示 i 个骰子组成点和为 j 的概率

$$
\text{dp[ i ][ j ]} = \sum_{k = 6}^{6} \frac{\text{dp[i - 1][j - k]}}{6}
$$

因为第 i 个骰子的结果只跟第 i - 1 个骰子的结果相关，所以可以用一维数组表示。

```cpp
vector<double> dicesProbability(int n) {
    vector<double> ans(6, 1.0 / 6);
    for (int i = 2; i <= n; i++) {
        vector<double> tmp(6*i - i + 1, 0.0);
        for (int j = 0; j < ans.size(); j++) {
            for (int k = 0; k < 6; k++) {
                tmp[j + k] += ans[j] * 1.0 / 6;
            }
        }
        ans = tmp;
    }
    return ans;
}
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(n)$

### 获得最多答题分数
给定一个二维列表 questions = [[1, 1], [2, 2], [3, 3], [4, 4], [5, 5]] 表示一系列的题目，questions[ i ] = [score, skipNum]，即如果作答第 i 题，将获得对应的分数 score，但是接下来需要跳过 skipNum 个题目；当然，第 i 道题目也可以选择不作答，试问，最多可以获得的分数是多少？

**「分析」**

【动态规划】设 dp[ i ] 为处于第 i 题时，可以获得的最大分数，**从后向前**遍历

$$
\text{dp[ i ]} =
\begin{cases}
\text{max ( dp[i + 1], dp[i + questions[ i ][ 1 ]] + questions[ i ][ 0 ]}, & \quad \text{i + questions[ i ][ 1 ] + 1} \geq n \\
\text{max ( dp[i + 1], questions[ i ][ 0 ])}, & \quad \text{otherwise}
\end{cases}
$$

```cpp
long long mostPoints(vector<vector<int>>& questions) {
    typedef long long ll;
    int n = (int)questions.size();
    vector<ll> dp(n, 0);
    dp[n - 1] = questions[n - 1][0];
    for (int i = n - 2; i >= 0; i--) {
        dp[i] = dp[i + 1];
        if (i + questions[i][1] >= n) {
            dp[i] = max(dp[i], (ll)questions[i][0]);
        } else {
            dp[i] = max(dp[i], questions[i][0] + dp[i + questions[i][1]]);
        }
    }
    return dp[0];
}
```
时间复杂度：$O(n)$， 空间复杂度：$O(n)$

### 剪绳子
题目描述：给你一根长度为n的绳子，请把绳子剪成整数长的m段（m、n都是整数，n>1并且m>1），每段绳子的长度记为k[0],k[1],...,k[m]。请问k[0]xk[1]x...xk[m]可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

示例1：输入8输出18

**「分析」**

设 f[ i ] 为长度 i 的绳子所能得到的最大乘积。那么，逐个单位切分成两段，找到最大的切割方案。

$$
f(n) =
\begin{cases}
  n, \quad 0 < n < 4 \\
  \max_{i} f(i)f(n-i), \quad i = 1, 2, ..., \left \lfloor \frac{n}{2} \right \rfloor, \quad \text{otherwise}
\end{cases}
$$

```C++
class Solution {
public：
    int cutRope(int number) {
        int n = number;
        if (n < 2) { return 0; }
        if (n == 2) { return 1; }
        if (n == 3) { return 2; }
        int *f = new int[n+1]{};
        f[1] = 1;
        f[2] = 2;
        f[3] = 3;
        int prod = 1;
        for (int i = 4; i <= n; i++) {
            int max_prod = 1;
            for (int j = 1; j <= i/2; j++) {
                prod = f[j] * f[i-j];
                if (max_prod < prod) { max_prod = prod; }
            }
            f[i] = max_prod;
        }
        int res = f[n];
        delete []f;
        return res;
    }
};
```
时间复杂度：$O(n^{2})$， 空间复杂度：$O(n)$

### 给 N x 3 网格图涂色的方案数
你有一个 n x 3 的网格图 grid ，你需要用 红，黄，绿 三种颜色之一给每一个格子上色，且确保相邻格子颜色不同（也就是有相同水平边或者垂直边的格子颜色不同）。给你网格图的行数 n 。请你返回给 grid 涂色的方案数。由于答案可能会非常大，请你返回答案对 10^9 + 7 取余的结果。

**「分析」**

「序列型dp」

```cpp
/*
Input: n = 1
Output: 12
Explanation: There are 12 possible way to paint the grid as shown.
*/
class Solution {
public:
    int numOfWays(int n) {
        int aba = 6, abc = 6;
        /*
        aba = aba * 3 + abc * 2
        abc = aba * 2 + abc * 2;
        */
        const int MOD = 1000000007;
        for (int i = 1; i < n; i++) {
            int new_aba = (aba * 3ll + abc * 2ll) % MOD;
            int new_abc = (aba * 2ll + abc * 2ll) % MOD;
            aba = new_aba;
            abc = new_abc;
        }
        return (aba + abc) % MOD;
    }
};
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$。

### 最长有效括号
给你一个只包含 '(' 和 ')' 的字符串，找出最长有效（格式正确且连续）括号子串的长度。例如，s = ")()())"，返回 4，最长有效括号子串是 "()()"。

**「分析」**

「序列型DP」

```cpp
int longestValidParentheses(string s) {
    int maxLen = 0, n = int(s.length()), leftCount = 0;
    if (n < 2) { return maxLen; }
    int *dp = new int [n]{};
    for (int i = 0; i < n; i++) {
        if (leftCount == 0 && s[i] == ')') {
            continue;
        }
        if (s[i] == '(') {
            leftCount++;
        } else {
            leftCount--;
            dp[i] = dp[i-1] + 1;
            if (i - dp[i] * 2 >= 0) {
                dp[i] += dp[i-dp[i]*2];
            }
        }
        maxLen = maxLen > dp[i] ? maxLen : dp[i];
    }
    delete []dp;
    return maxLen * 2;
}

// 常数空间
int longestValidParentheses(string s) {
    int ans = 0, L = 0, R = 0;
    for (char c : s) {
        if (c == '(') {
            L++;
        } else {
            R++;
        }
        if (L == R) {
            ans = max(ans, 2 * R);
        } else if (L < R) {
            L = R = 0;
        }
    }
    L = R = 0;
    for (int i = (int)s.length() - 1; i >= 0; i--) {
        if (s[i] == '(') {
            L++;
        } else {
            R++;
        }
        if (L == R) {
            ans = max(ans, 2 * R);
        } else if (L > R) {
            L = R = 0;
        }
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$。

### 不同的子序列
给你两个字符串 s 和 t ，统计并返回在 s 的 子序列 中 t 出现的个数，结果需要对 100000007 取模。例如，s = "babgbag", t = "bag"，返回 5，因为 s 有 5 个子序列满足要求，即 [babgbag, babgbag, babgbag, babgbag, babgbag]

**「分析」**

「序列型」设 dp[i][j] 表示 s[:i] 满足 t[:j] 的子序列数目。转移方程如下：

$$
dp[i][j] =
\begin{cases}
dp[i - 1][j - 1] + dp[i - 1][j], &s[i] == t[j] \\
dp[i - 1][j], &\text{else}
\end{cases}
$$

因为 s 的子序列必须包含 t，所以状态转移来自 s 的前一位。

```cpp
int numDistinct(string s, string t) {
    const int MOD = 1000000007;
    vector<vector<int>> dp(s.size() + 1, vector<int>(t.size() + 1));
    for (int i = 0; i <= s.size(); i++) {
        for (int j = 0; j <= t.size(); j++) {
            if (j == 0) {
                dp[i][j] = 1;
            } else if (i == 0) {
                dp[i][j] = 0;
            } else {
                dp[i][j] = dp[i - 1][j] % MOD;
                if (s[i - 1] == t[j - 1]) {
                    dp[i][j] = (dp[i][j] % MOD + dp[i - 1][j - 1] % MOD) % MOD;
                }
            }
        }
    }
    return dp[s.size()][t.size()];
}
```
时间复杂度：$O(nm)$，空间复杂度：$O(nm)$，这里，n 是 s 的长度，m 是 t 的长度。

### 扰乱字符串
使用下面描述的算法可以扰乱字符串 s 得到字符串 t ：

如果字符串的长度为 1 ，算法停止

如果字符串的长度 > 1 ，执行下述步骤：

在一个随机下标处将字符串分割成两个非空的子字符串。即，如果已知字符串 s ，则可以将其分成两个子字符串 x 和 y ，且满足 s = x + y 。随机 决定是要「交换两个子字符串」还是要「保持这两个子字符串的顺序不变」。即，在执行这一步骤之后，s 可能是 s = x + y 或者 s = y + x 。在 x 和 y 这两个子字符串上继续从步骤 1 开始递归执行此算法。给你两个 长度相等 的字符串 s1 和 s2，判断 s2 是否是 s1 的扰乱字符串。如果是，返回 true ；否则，返回 false 。

**「分析」**

「序列型」设 dp[i][j][k] 表示 s[i:i+k] 和 t[j:j+k] 是扰乱字符。

```cpp
bool isScramble(string s1, string s2) {
    int n = int(s1.length());
    if (n != int(s2.length())) { return false; }
    if (n == 0) { return true; }
    bool ***dp = new bool **[n];
    for (int i = 0; i < n; i++) {
        dp[i] = new bool *[n];
        for (int j = 0; j < n; j++) {
            dp[i][j] = new bool [n+1]{};
        }
    }
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            dp[i][j][1] = s1[i] == s2[j];
        }
    }
    for (int len = 2; len <= n; len++) {
        for (int i = 0; i < n - len + 1; i++) {
            for (int j = 0; j < n - len + 1; j++) {
                for (int k = 1; k < len; k++) {
                    dp[i][j][len] = (
                        (dp[i][j][k] && dp[i+k][j+k][len-k]) ||
                        (dp[i+k][j][len-k] && dp[i][j+len-k][k])
                    );
                    if (dp[i][j][len]) { break; }
                }
            }
        }
    }
    bool ans = dp[0][0][n];
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) { delete []dp[i][j]; }
        delete []dp[i];
    }
    delete []dp;
    return ans;
}
```
时间复杂度：$O(n^{4})$，空间复杂度：$O(n^{3})$。

### 青蛙过河
一只青蛙想要过河。 假定河流被等分为若干个单元格，并且在每一个单元格内都有可能放有一块石子（也有可能没有）。 青蛙可以跳上石子，但是不可以跳入水中。给你石子的位置列表 stones（用单元格序号 升序 表示）， 请判定青蛙能否成功过河（即能否在最后一步跳至最后一块石子上）。开始时， 青蛙默认已站在第一块石子上，并可以假定它第一步只能跳跃 1 个单位（即只能从单元格 1 跳至单元格 2 ）。如果青蛙上一步跳跃了 k 个单位，那么它接下来的跳跃距离只能选择为 k - 1、k 或 k + 1 个单位。 另请注意，青蛙只能向前方（终点的方向）跳跃。

**「分析」**

「序列型」

```cpp
/*
输入：stones = [0,1,2,3,4,8,9,11]
输出：false
解释：这是因为第 5 和第 6 个石子之间的间距太大，没有可选的方案供青蛙跳
跃过去。
*/

bool canCross(vector<int>& stones) {
    unordered_map<int, int> dict;
    int n = (int)stones.size();
    if (n <= 1) { return true; }
    vector<vector<int>> dp(n, vector<int>(n, 0));
    dp[0][0] = true;
    for (int i = 1; i < n; i++) {
        if (stones[i] - stones[i-1] > i) {
            return false;
        }
        for (int j = i - 1; j >= 0; j--) {
            int k = stones[i] - stones[j];
            if (k > j + 1) {
                break;
            }
            dp[i][k] = dp[j][k-1] || dp[j][k] || dp[j][k+1];
            if (i == n - 1 && dp[i][k]) {
                return true;
            }
        }
    }
    return false;
}
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(n^{2})$。

### 最大整除子集
给你一个由 无重复 正整数组成的集合 nums ，请你找出并返回其中最大的整除子集 answer ，子集中每一元素对 (answer[i], answer[j]) 都应当满足：

answer[i] % answer[j] == 0 ，或

answer[j] % answer[i] == 0

如果存在多个有效解子集，返回其中任何一个均可。

**「分析」**

「排序 + 动态规划」

```cpp
/*
Input: nums = [1,2,3]
Output: [1,2]
Explanation: [1,3] is also accepted.
*/

vector<int> largestDivisibleSubset(vector<int>& nums) {
    vector<int> ans;
    int n = (int)nums.size();
    if (n < 2) { return nums; }
    sort(nums.begin(), nums.end());
    vector<int> dp(n, 1);
    vector<int> par(n, -1);
    int len = 1;
    for (int i = 1; i < n; i++) {
        for (int j = i - 1; j >= 0; j--) {
            if (nums[i] % nums[j] == 0 && dp[j] + 1 > dp[i]) {
                dp[i] = max(dp[i], dp[j] + 1);
                par[i] = j;
            }
        }
        len = max(len, dp[i]);
    }
    ans.resize(len);
    for (int i = n - 1; i >= 0;) {
        if (dp[i] == len) {
            ans[--len] = nums[i];
            i = par[i];
        } else {
            i--;
        }
    }
    return ans;
}
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(n)$。
