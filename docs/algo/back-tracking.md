## 定义
回溯是一种计算机算法，也叫试探法，从初始状态开始访问所有状态，当无法沿着某一路径继续访问时，返回到上一个分叉路口。通常以递归的方式实现。


## 题目
### 单词分拆 I
给你一个字符串 s 和一个字符串列表 wordDict 作为字典。请你判断是否可以利用字典中出现的单词拼接出 s 。注意：不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。输入: s = "leetcode", wordDict = ["leet", "code"]，输出: true。解释: 返回 true 因为 "leetcode" 可以由 "leet" 和 "code" 拼接成。

**「分析」**
「回溯 + 记忆化dp」

```cpp
class Solution {
public:
    unordered_map<string, bool> M;
    unordered_set<string> W;
    bool wordBreak(string s, vector<string>& wordDict) {
        for (const string &w : wordDict) {
            W.emplace(w);
        }
        return dfs(s);
    }
    bool dfs(string s) {
        if (M.count(s)) {
            return M[s];
        }
        bool ans = false;
        for (auto w : W) {
            const string &s1 = s.substr(0, w.length());
            if (s1 != w) {
                continue;
            }
            M[s1] = true;
            string s2 = s.substr(w.length(), s.length() - w.length());
            if (s2.empty()) {
                return true;
            }
            if (dfs(s2)) {
                M[s2] = true;
                return true;
            }
        }
        M[s] = ans;
        return ans;
    }
};
```
时间复杂度 $O(n 2^{n})$，空间复杂度 $O(n 2^{n})$，n 是单词的长度。

### 单词分拆 II
给定一个字符串 s 和一个字符串字典 wordDict ，在字符串 s 中增加空格来构建一个句子，使得句子中所有的单词都在词典中。以任意顺序 返回所有这些可能的句子。注意：词典中的同一个单词可能在分段中被重复使用多次。例如，输入:s = "catsanddog", wordDict = ["cat","cats","and","sand","dog"]，输出:["cats and dog","cat sand dog"]。


**「分析」**
「回溯 + 记忆化dp」

遍历 wordDict 的单词，并与 s 的前缀对比，如果匹配，递归地处理 s 的剩余部分。


```cpp
class Solution {
public:
    unordered_map<string, vector<string>> M;
    unordered_set<string> words;
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        for (const string &w : wordDict) {
            words.emplace(w);
        }
        return dfs(s);
    }
    vector<string> dfs(string s) {
        vector<string> ans;
        if (s.empty()) {
            return ans;
        }
        if (M.count(s)) {
            return M[s];
        }
        
        for (auto w : words) {
            if (s.substr(0, w.length()) == w) {
                string s2 = s.substr(w.length(), s.length() - w.length());
                //printf("%s %s\n", w.c_str(), s2.c_str());
                if (s2.empty()) {
                    ans.emplace_back(w);
                    continue;
                }
                vector<string> t2 = dfs(s2);
                for (const string &t : t2) {
                    ans.emplace_back(w + " " + t);
                    //printf("%s\n", ans.back().c_str());
                }
            }
        }
        M[s] = ans;
        return ans;
    }
};
```
时间复杂度 $O(n 2^{n})$，空间复杂度 $O(n 2^{n})$，n 是单词的长度。

### 吃掉 N 个橘子的最少天数
厨房里总共有 n 个橘子，你决定每一天选择如下方式之一吃这些橘子：

1）吃掉一个橘子。

2）如果剩余橘子数 n 能被 2 整除，那么你可以吃掉 n/2 个橘子。

3）如果剩余橘子数 n 能被 3 整除，那么你可以吃掉 2*(n/3) 个橘子。

每天你只能从以上 3 种方案中选择一种方案。请你返回吃掉所有 n 个橘子的最少天数。

示例 1：

输入：n = 10

输出：4

解释：你总共有 10 个橘子。

第 1 天：吃 1 个橘子，剩余橘子数 10 - 1 = 9。

第 2 天：吃 6 个橘子，剩余橘子数 9 - 2*(9/3) = 9 - 6 = 3。（9 可以被 3 整除）

第 3 天：吃 2 个橘子，剩余橘子数 3 - 2*(3/3) = 3 - 2 = 1。

第 4 天：吃掉最后 1 个橘子，剩余橘子数 1 - 1 = 0。

你需要至少 4 天吃掉 10 个橘子。

**「分析」**

「记忆化动态规划」优先考虑使用第 2 种和第 3 种方案，因为有大量重复子问题，所以需要用哈希表将中间结果保存下来，避免重复计算。

```cpp
unordered_map<int, int> dict;
int minDays(int n) {
    if (n <= 1) {
        return 1;
    }
    if (dict.count(n)) {
        return dict[n];
    }
    int ans = min(n % 2 + 1 + minDays(n / 2), n % 3 + 1 + minDays(n / 3));
    dict[n] = ans;
    return ans;
}
```
时间复杂度：$O(\log n)$，空间复杂度 $O(n)$。

### N 皇后
按照国际象棋的规则，皇后可以攻击与之处在同一行或同一列或同一斜线上的棋子。n 皇后问题 研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。给你一个整数 n ，返回所有不同的 n 皇后问题 的解决方案。每一种解法包含一个不同的 n 皇后问题 的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。例如，n = 4，返回 [ [".Q..", "...Q", "Q...", "..Q."], ["..Q.", "Q...", "...Q", ".Q.."] ]

```cpp
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans;
        string str = "";
        for (int i = 0; i < n; i++) { str += "."; }
        vector<string> res(n, str);
        unordered_set<int> col, diag1, diag2;
        DFS(col, diag1, diag2, n, 0, ans, res);
        return ans;
    }
    void DFS(unordered_set<int> &col,
             unordered_set<int> &diag1,
             unordered_set<int> &diag2,
             int n, int i,
             vector<vector<string>> &ans,
             vector<string> &res) {
        if (i == n) {
            ans.emplace_back(res);
            return;
        }
        for (int j = 0; j < n; j++) {
            if (isOkPos(col, diag1, diag2, i, j)) {
                res[i][j] = 'Q';
                col.insert(j);
                diag1.insert(i-j);
                diag2.insert(i+j);
                DFS(col, diag1, diag2, n, i+1, ans, res);
                res[i][j] = '.';
                col.erase(j);
                diag1.erase(i-j);
                diag2.erase(i+j);
            }
        }
    }
    bool isOkPos(unordered_set<int> &col, unordered_set<int> &diag1,
                    unordered_set<int> &diag2, int i, int j) {
        if (col.find(j) != col.end() ||
            diag1.find(i-j) != diag1.end() ||
            diag2.find(i+j) != diag2.end()) { return false; }
        return true;
    }
};
```
时间复杂度：$O(2^{n})$，空间复杂度：$O(n^{2})$。

### 解数独
编写一个程序，通过填充空格来解决数独问题。数独的解法需 遵循如下规则：

数字 1-9 在每一行只能出现一次。

数字 1-9 在每一列只能出现一次。

数字 1-9 在每一个以粗实线分隔的 3x3 宫内只能出现一次。（请参考示例图）

数独部分空格内已填入了数字，空白格用 '.' 表示。

$$
\begin{bmatrix}
5 & 3 & . & . & 7 & . & . & . & . \\
6 & . & . & 1 & 9 & 5 & . & . & . \\
. & 9 & 8 & . & . & . & . & 6 & . \\
8 & . & . & . & 6 & . & . & . & 3 \\
4 & . & . & 8 & . & 3 & . & . & 1 \\
7 & . & . & . & 2 & . & . & . & 6 \\
. & 6 & . & . & . & . & 2 & 8 & . \\
. & . & . & 4 & 1 & 9 & . & . & 5 \\
. & . & . & . & 8 & . & . & 7 & 9
\end{bmatrix} \Rightarrow
\begin{bmatrix}
5 & 3 & {\color{Red} 4} & {\color{Red} 6} & 7 & {\color{Red} 8} & {\color{Red} 9} & {\color{Red} 1} & {\color{Red} 2} \\
6 & {\color{Red} 7} & {\color{Red} 2} & 1 & 9 & 5 & {\color{Red} 3} & {\color{Red} 4} & {\color{Red} 8} \\
{\color{Red} 1} & 9 & 8 & {\color{Red} 3} & {\color{Red} 4} & {\color{Red} 2} & {\color{Red} 5} & 6 & {\color{Red} 7} \\
8 & {\color{Red} 5} & {\color{Red} 9} & {\color{Red} 7} & 6 & {\color{Red} 1} & {\color{Red} 4} & {\color{Red} 2} & 3 \\
4 & {\color{Red} 2} & {\color{Red} 6} & 8 & {\color{Red} 5} & 3 & {\color{Red} 7} & {\color{Red} 9} & 1 \\
7 & {\color{Red} 1} & {\color{Red} 3} & {\color{Red} 9} & 2 & {\color{Red} 4} & {\color{Red} 8} & {\color{Red} 5} & 6 \\
{\color{Red} 9} & 6 & {\color{Red} 1} & {\color{Red} 5} & {\color{Red} 3} & {\color{Red} 7} & 2 & 8 & {\color{Red} 4} \\
{\color{Red} 2} & {\color{Red} 8} & {\color{Red} 7} & 4 & 1 & 9 & {\color{Red} 6} & {\color{Red} 3} & 5 \\
{\color{Red} 3} & {\color{Red} 4} & {\color{Red} 5} & {\color{Red} 2} & 8 & {\color{Red} 6} & {\color{Red} 1} & 7 & 9
\end{bmatrix}
$$

```cpp
class Solution {
public:
    void solveSudoku(vector<vector<char>>& board) {
        isSolver(board);
    }
    bool isSolver(vector<vector<char>> &board) {
        for (int i = 0; i < 9; i++) {
            for (int j = 0; j < 9; j++) {
                if (board[i][j] == '.') {
                    for (char c = '1'; c <= '9'; c++) {
                        if (isFeasible(board, i, j, c)) {
                            board[i][j] = c;
                            if (isSolver(board)) { return true; }
                            else { board[i][j] = '.'; }
                        }
                    }
                    // No feasible number
                    return false;
                }
            }
        }
        return true;
    }
    bool isFeasible(vector<vector<char>> board, int row, int col, char c) {
        for(int i = 0; i < 9; i++) {
            if(board[row][i] == c) { return false; }
            if(board[i][col] == c) { return false; }
            if(board[3*(row/3)+i/3][3*(col/3)+i%3] == c) { return false; }
        }
        return true;
    }
};
```
时间复杂度：$O(n^{2}2^{n})$，空间复杂度：$O(n^{3})$。

### 摘樱桃
给你一个 n x n 的网格 grid ，代表一块樱桃地，每个格子由以下三种数字的一种来表示：

0 表示这个格子是空的，所以你可以穿过它。

1 表示这个格子里装着一个樱桃，你可以摘到樱桃然后穿过它。

-1 表示这个格子里有荆棘，挡着你的路。

请你统计并返回：在遵守下列规则的情况下，能摘到的最多樱桃数：

从位置 (0, 0) 出发，最后到达 (n - 1, n - 1) ，只能向下或向右走，并且只能穿越有效的格子（即只可以穿过值为 0 或者 1 的格子）；当到达 (n - 1, n - 1) 后，你要继续走，直到返回到 (0, 0) ，只能向上或向左走，并且只能穿越有效的格子；当你经过一个格子且这个格子包含一个樱桃时，你将摘到樱桃并且这个格子会变成空的（值变为 0 ）；如果在 (0, 0) 和 (n - 1, n - 1) 之间不存在一条可经过的路径，则无法摘到任何一个樱桃。

**「分析」**

「DFS」从起点到终点同时走两条路。

```cpp
/*
Input: grid = [[1,1,-1],[1,-1,1],[-1,1,1]]
Output: 0
*/
class Solution {
public:
    int cherryPickup(vector<vector<int>>& grid) {
        int n = int(grid.size());
        if (n == 0) { return 0; }
        vector<vector<vector<int>>> 
            dp(n, vector<vector<int>>(n, vector<int>(n, -1)));
        int ans = move(dp, grid, 0, 0, 0, n);
        return max(0, ans);
    }

    int move(vector<vector<vector<int>>> &dp,
             vector<vector<int>> &grid,
             int r1, int c1, int c2, int n) {
        int r2 = r1 + c1 - c2;
        if (r1 >= n || c1 >= n || r2 >= n || c2 >= n ||
            grid[r1][c1] == -1 ||
            grid[r2][c2] == -1) { return -2; }
        if (r1 == n-1 && c1 == n-1) { return grid[r1][c1]; }
        if (dp[r1][c1][c2] != -1) { return dp[r1][c1][c2]; }
        int ans = move(dp, grid, r1, c1+1, c2+1, n);
        ans = max(ans, move(dp, grid, r1+1, c1, c2+1, n));
        ans = max(ans, move(dp, grid, r1, c1+1, c2, n));
        ans = max(ans, move(dp, grid, r1+1, c1, c2, n));
        if (ans >= 0) {
            ans += grid[r1][c1] + (c1 != c2 || r1 != r2) *
                grid[r2][c2];
        }
        dp[r1][c1][c2] = ans;
        return ans;
    }
};
```
时间复杂度：$O(nm)$，空间复杂度：$O(nm)$。
