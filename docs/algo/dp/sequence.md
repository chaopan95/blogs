## 基础
序列型动态规划表示状态成一个序列，可以是无环图、也可以是有环图。

## 删除并获得点数
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

## 出勤方案数
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
时间复杂度：$O(n 2^{n})$，空间复杂度：$O(2^{n})$


## 至多 k 次中转的最低价格
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


## 斐波那契数列第 n 项
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


## 访问完所有房间的第一天
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


## 圆圈中最后剩下的数字
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


## n 个骰子的点数概率
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


## 获得最多答题分数
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


## 剪绳子
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
