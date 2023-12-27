## 无障碍到达终点的路径条数
一个机器人在 $m \times n$ 的网格的左上角，每次只能向下或者向右移动一格，求到达终点的路径条数。

$$
\begin{matrix}
\text{start} & \cdot & \cdot & \cdot & \cdot & \cdot \\
\cdot & \cdot & \cdot & \cdot & \cdot & \cdot \\
\cdot & \cdot & \cdot & \cdot & \cdot & \cdot \\
\cdot & \cdot & \cdot & \cdot & \cdot & \text{end}
\end{matrix}
$$

**「分析」**

对于任意一个网格（i， j），能够到达这个网格只有两个位置即（i-1，j）和（i，j+1）。我们可以设 dp[ i ][ j ] 为从（0，0）到（i，j）的路径条数，写出状态转移方程。注意到，从左上角到右下角的路径条数与从右下角到左上角的路径条数是等价的。

$$
\begin{aligned}
& dp[i][1] = 1, \quad i = 1, 2, ..., m \\
& dp[1][j] = 1, \quad j = 1, 2, ..., n \\
& dp[i][j] = dp[i-1][j] + dp[i][j-1], \quad i = 2, 3, ..., m \quad j = 2, 3, ..., n
\end{aligned}
$$

我们可以发现，当前网格只与上方网格和左边网格有关，那么空间复杂度可以进一步优化。

```cpp
vector<int> next(n+1, 0), cur(n+1, 0);
next[n-1] = 1;
for (int i = m - 1; i >= 0; i--) {
    for (int j = n - 1; j >= 0; j--) {
        next[j] += cur[j] + next[j+1];
    }
    cur = next;
    next = vector<int>(n+1, 0);
}
return cur[0];
```
时间复杂度 $O(m \times n)$，空间复杂度 $O(n)$

## 有障碍到达终点的路径条数
一个机器人在 $m \times n$ 的网格的左上角，每次只能向下或者向右移动一格，但是某些网格有障碍，机器人不能到达这些障碍所在网格，求到达终点的路径条数。

$$
\begin{matrix}
0 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 0
\end{matrix}
$$

**「分析」**

同样地，设 dp[ i ][ j ] 为（0，0）到（i，j）之间地有效路径条数，其状态转移方程如下

$$
\text{dp[ i ][ j ]} =
\begin{cases}
0, & \quad \text{grid[ i ][ j ] == 1} \\
\text{dp[i + 1][ j ] + dp[ i ][j + 1]}, & \quad \text{otherwise}
\end{cases}
$$

```cpp
vector<int> dp(n, 0);
if (grid[0][0]) { return 0; }
dp[0] = 1;
for (int j = 1; j < n; j++) {
    dp[j] = dp[j-1] * (1 - grid[0][j]);
}
for (int i = 1; i < m; i++) {
    vector<int> pre = dp;
    dp[0] = pre[0] * (1 - grid[i][0]);
    for (int j = 1; j < n; j++) {
        dp[j] = (pre[j] + dp[j-1]) * (1 - grid[i][j]);
    }
}
return dp[n-1];
```
时间复杂度 $O(m \times n)$，空间复杂度 $O(n)$

## 最小路径和
一个包含非负整数的 $m \times n$ 的网格，找出一条从左上角到右下角的路径，使得这条路径上的数字和最小。

**「分析」**

设 dp[ i ][ j ] 为从（0，0）到（i，j）最小路径和，其状态转移方程如下

$$
\text{dp[ i ][ j ]} = \max(\text{dp[i - 1][ j ], dp[ i ][j - 1]}) + \text{grid[ i ][ j ]}
$$

```cpp
vector<int> dp(n+1, INT_MAX);
dp[n-1] = 0;
for (int i = m - 1; i >= 0; i--) {
    vector<int> tmp = dp;
    for (int j = n - 1; j >= 0; j--) {
        dp[j] = min(tmp[j], dp[j+1]) + grid[i][j];
    }
}
return dp[0];
```
时间复杂度 $O(m \times n)$，空间复杂度 $O(n)$

## 停在原地的方案数
有一个长度为 arrLen 的数组，开始有一个指针在索引 0 处。每一步操作中，你可以将指针向左或向右移动 1 步，或者停在原地（指针不能被移动到数组范围外）。求在恰好执行 steps 次操作以后，指针仍然指向索引 0 处的方案数。例如 steps = 3, arrLen = 2，那么 3 步后，总共有 4 种不同的方法可以停在索引 0 处。 分别是：

向右，向左，不动

不动，向右，向左

向右，不动，向左

不动，不动，不动

**「分析」**

设 dp[ k ][ i ] 为经过 k 步后跳到第 i 个位置时的方法数，状态转移如下

$$
\text{dp[ k ][ i ] = dp[k - 1][i - 1] + dp[k - 1][ i ] + dp[k - 1][i + 1]}
$$

可以观察到，状态转移只会发生在相邻两步和相邻的位置，那么可以使用两个一位数组代替二维数组

```cpp
const int MOD = 1000000007;
int n = min(arrLen, steps / 2 + 1);
vector<int> dp(n, 0);
dp[0] = 1;
while (steps--) {
    vector<int> tmp = dp;
    for (int i = 0; i < n; i++) {
        long a = i == 0 ? 0 : tmp[i - 1];
        long b = i == n - 1 ? 0 : tmp[i + 1];
        long c = tmp[i];
        dp[i] = (a + b + c) % MOD;
    }
}
return dp[0];
```
时间复杂度 $O(k \times n)$，空间复杂度 $O(n)$，k 为移动的步数，n 为数组的长度。

## 杨辉三角路径最小和
给定一个三角形，找出自顶向下的最小路径和，每一步只能移动到下一行，且与当前节点相邻的节点

$$
\begin{matrix}
& & & 1 \\
& & & \wedge \\
& & 3 & & 4 \\
& & \wedge & & \wedge \\
& 6 & & 5 & & 7 \\
& \wedge & & \wedge & & \wedge \\
4 & & 1 & & 8 & & 3
\end{matrix}
$$

**「分析」**

每一个内节点（非顶点），与上一层的两个节点相连，每一个边界节点（非顶点），与上一层的一个节点相连。设 dp[ i ][ j ] 表示从顶点到（i，j）点的最小路径和，状态转移方程如下

$$
\text{dp[ i ][ j ]} = \max(\text{dp[i + 1][ j ], dp[i + 1][j + 1]}), \quad i = 0, 1, \cdots, n - 2
$$

我们可以由上到下写动态规划的方程，也可以反过来递推。我们观察到当前层只与下一层的两个两个状态相关，因此只需要 $O(n)$ 的空间即可，但如果由下到上递推的话，我们可以借助传入矩阵，不必开辟额外的空间（本题较为特殊）。

```cpp
for (int i = n - 2; i >= 0; i--) {
    for (int j = 0; j <= i; j++) {
        triangle[i][j] = min(triangle[i+1][j],
                                triangle[i+1][j+1]) +
                            triangle[i][j];
    }
}
return triangle[0][0];
```
时间复杂度 $O(n^{2})$，空间复杂度 $O(1)$