## 删除子串的最小操作数
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

## 删除回文串的最小次数
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
