## 子矩阵
### 最大正方形
给定一个由 0 和 1 构成的矩阵，求最大的全 1 子正方形。例如

$$
\begin{matrix}
1 & 0 & 1 & 0 & 0 \\
1 & 0 & {\color{Red} 1} & {\color{Red} 1} & 1 \\
1 & 1 & {\color{Red} 1} & {\color{Red} 1} & 1 \\
1 & 0 & 0 & 1 & 0
\end{matrix}
$$

最大面积为 4。

**「分析」**

设 dp[ i ][ j ] 为（i，j）到（nRow，nCol）的最大正方形边长，其状态转移矩阵如下：

$$
\text{dp[ i ][ j ]} =
\begin{cases}
\max(\text{dp[i + 1][ j ], dp[ i ][j + 1], dp[i + 1][j + 1]}) + 1, & \quad \text{grid[ i ][ j ] == 1} \\
0, & \quad \text{otherwise}
\end{cases}
$$

```cpp
vector<int> dp(nCol + 1, 0);
int ans = 0;
for (int i = nRow - 1; i >= 0; i--) {
    vector<int> tmp = dp;
    for (int j = nCol - 1; j >= 0; j--) {
        dp[j] = 0;
        if (matrix[i][j] == '1') {
            dp[j] = min(tmp[j], min(dp[j+1], tmp[j+1])) + 1;
        }
        ans = max(ans, dp[j] * dp[j]);
    }
}
return ans;
```
时间复杂度 $O(m \times n)$，空间复杂度 $O(n)$

### 统计全为 1 的正方形子矩阵
给定一个由 0 和 1 组成的矩阵，现在统计出所有只由 1 构成的子方阵的个数。

$$
\begin{matrix}
0 & 1 & 1 & 1 \\
1 & 1 & 1 & 1 \\
0 & 1 & 1 & 1
\end{matrix}
$$

矩阵中共有 15 个子方阵（10 个 $1 \times 1$，4 个 $2 \times 2$，1 个 $3 \times 3$）

**「分析」**

同上一题目，设 dp[ i ][ j ] 为（i，j）到（nRow，nCol）的最大正方形边长，用一个整型变量，在遍历过程中，记录每一个边长大于 1 的正方形，即统计全 1 正方形的个数。

```cpp
vector<int> dp(nCol + 1, 0);
int ans = 0;
for (int i = nRow - 1; i >= 0; i--) {
    vector<int> tmp = dp;
    for (int j = nCol - 1; j >= 0; j--) {
        dp[j] = 0;
        if (matrix[i][j]) {
            dp[j] = min(tmp[j], min(dp[j+1], tmp[j+1])) + 1;
        }
        ans += dp[j];
    }
}
return ans;
```
时间复杂度 $O(m \times n)$，空间复杂度 $O(n)$

### 最大边长全为 0 的正方形
给定一个由 0 和 1 组成的方阵，先求一个最大的子方阵，其四个边长全部由 0 构成，内部元素无要求。返回结果是一个数组，形如 {r, c, len}，其中 r 最小，如果 r 相等的情况下，取 c 最小的解。如下例子

$$
\begin{matrix}
{\color{Red} 0} & {\color{Red} 0} & {\color{Red} 0} & 1 \\
{\color{Red} 0} & 1 & {\color{Red} 0} & 1 \\
{\color{Red} 0} & {\color{Red} 0} & {\color{Red} 0} & 1 \\
1 & 0 & 0 & 0
\end{matrix}
$$

**「分析」**
设 dpRight[ i ][ j ] 为（i，j）向右连续为 0 的最大长度，dpDown[ i ][ j ] 为（i，j）向下连续为 0 的最大长度。其状态转移方程如下

$$
\begin{cases}
\text{dpRight[ i ][ j ] = dpDown[ i ][ j ] = 0}, &\text{matrix[ i ][ j ] == 1} \\
\begin{cases}
\text{dpRight[ i ][ j ] = dpRight[ i ][j + 1] + 1} \\
\text{dpDown[ i ][ j ] = dpDown[i + 1][ j ] + 1}
\end{cases}, & \text{matrix[ i ][ j ] == 0}
\end{cases}
$$

但是一个矩阵有四条边，所有需要考虑从（i，j）过渡到（i+len-1，j+len-1）。那么如何确定边长 len 呢？

$$
\text{len = } \min(\text{dpRight[ i ][ j ], dpDown[ i ][ j ]})
$$

进而确定了左下方和右上方的点 (i+len-1，j)、（i，j+len-1）。紧接着，从左下点向右 len - 1 个单位或者从右上点向下 len - 1 个单位都会到达（i+len-1，j+len-1），只需要确定是否存在这样的 len。

```cpp
vector<int> findSquare(vector<vector<int>>& matrix) {
    vector<int> ans {-1, -1, 0};
    int n = (int)matrix.size();
    if (n == 0) { return ans; }
    vector<vector<int>> dpRight(n+1, vector<int>(n+1, 0));
    vector<vector<int>> dpDown(n+1, vector<int>(n+1, 0));
    for (int i = n - 1; i >= 0; i--) {
        for (int j = n - 1; j >= 0; j--) {
            if (matrix[i][j] == 1) {
                dpRight[i][j] = dpDown[i][j] = 0;
            }
            else {
                dpRight[i][j] = dpRight[i][j + 1] + 1;
                dpDown[i][j] = dpDown[i + 1][j] + 1;
                int len = min(dpRight[i][j], dpDown[i][j]);
                while (len >= ans[2]) {
                    if (dpRight[i+len-1][j] >= len &&
                        dpDown[i][j+len-1] >= len) {
                        ans = {i, j, len};
                        break;
                    }
                    len--;
                }
            }
        }
    }
    return ans[0] == -1 && ans[1] == -1 ? vector<int> {} : ans;
}
```
