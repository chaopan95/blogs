## DFS
**「简介」**
深度优先搜索（Depth Ffirst Search）是一种检索方法，优先搜索某一节点的子节点，如果可以搜索到目标，即停止或将结果保存起来；反之，回溯到分叉处，依次遍历下一个节点。

### 全排列
**「序列元素不重复」**

对一个序列如 [1, 2, 3] ,元素互不相同，求其全排列

```cpp
void dfs(vector<int> &nums, int n, int idx,
         vector<vector<int>> &ans) {
    if (idx == n) {
        ans.emplace_back(nums);
        return;
    }
    for (int i = idx; i < n; i++) {
        swap(nums[i], nums[idx]);
        dfs(nums, n, idx + 1, ans);
        swap(nums[i], nums[idx]);
    }
}

vector<vector<int>> permute(vector<int>& nums) {
    vector<vector<int>> ans;
    int n = (int)nums.size();
    if (n == 0) { return ans; }
    dfs(nums, n, 0, ans);
    return ans;
}
```

**「序列元素有重复」**

对一个序列如 [3, 3, 0, 3] ，元素有重复，求其全排列

```cpp
void dfs(vector<vector<int>> &ans, vector<int> &nums,
         int n, int idx, bool *vis, vector<int> &arr) {
    if (idx == n) {
        ans.emplace_back(arr);
        return;
    }
    for (int i = 0; i < n; i++) {
        if (vis[i] || (i > 0 && nums[i] == nums[i-1] && !vis[i-1])) {
            continue;
        }
        vis[i] = true;
        arr.emplace_back(nums[i]);
        dfs(ans, nums, n, idx + 1, vis, arr);
        arr.pop_back();
        vis[i] = false;
    }
}

vector<vector<int>> permuteUnique(vector<int>& nums) {
    vector<vector<int>> ans;
    int n = (int)nums.size();
    if (n == 0) { return ans; }
    sort(nums.begin(), nums.end());
    vector<int> arr;
    bool *vis = new bool [n]{};
    dfs(ans, nums, n, 0, vis, arr);
    delete []vis;
    return ans;
}
```

### 二叉树某一节点所有距离为 k 的节点
给定一棵二叉树和节点 target，求所有的距离 target 为 k 的节点。这里的距离指的是边的个数

**「分析」**

如果二叉树的节点包含了父节点的信息，直接 dfs 遍历；如果不包含父节点信息，则先用哈希表保存每一个节点的父节点，再用 dfs 搜索。注意遍历时保存已经访问的节点。

```cpp
unordered_map<TreeNode*, TreeNode*> par;
unordered_set<TreeNode*> vis;
void findParNode(TreeNode *root) {
    if (root != nullptr) {
        if (root->left != nullptr) {
            par[root->left] = root;
        }
        if (root->right != nullptr) {
            par[root->right] = root;
        }
        findParNode(root->left);
        findParNode(root->right);
    }
}
void dfs(TreeNode *curNode, int k, vector<int> &ans) {
    if (curNode == nullptr || vis.count(curNode)) {
        return;
    }
    if (k == 0) {
        ans.emplace_back(curNode->val);
        return;
    }
    vis.insert(curNode);
    dfs(par[curNode], k-1, ans);
    dfs(curNode->left, k-1, ans);
    dfs(curNode->right, k-1, ans);
}
vector<int> distanceK(TreeNode* root, TreeNode* target, int k) {
    vector<int> ans;
    if (root == nullptr) {
        return ans;
    }
    par[root] = nullptr;
    findParNode(root);
    dfs(target, k, ans);
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$

### 我能赢游戏
给定两个正整数 a = 10，b = 11，两个玩家，每人依次出一个数字（1 - a之间），累加和先到 b 的获胜，试问，先手能赢么？

**「分析」**

【状态压缩+ DFS记忆优化】

```cpp
unordered_map<int, bool> memo;
bool dfs(int n, int m, int state) {
    if (memo.count(state)) {
        return memo[state];
    }
    for (int i = 1; i <= n; i++) {
        if (state & (1 << i)) { continue; }
        if (i >= m) {
            memo[state | (1 << i)] = true;
            return true;
        }
        else {
            bool oppo_win = dfs(n, m - i, state | (1 << i));
            if (!oppo_win) {
                memo[state] = true;
                return true;
            }
        }
    }
    memo[state] = false;
    return false;
}
bool canIWin(int maxChoosableInteger, int desiredTotal) {
    if (maxChoosableInteger >= desiredTotal) {
        return true;
    }
    if ((1 + maxChoosableInteger) * maxChoosableInteger < 2 * desiredTotal) {
        return false;
    }
    bool ans = dfs(maxChoosableInteger, desiredTotal, 0);
    return ans;
}
```

### 找路
给定一个二维数组 obstacleGrid = [[0, 0, 0], [0, 1, 0], [0, 0, 0]] 表示一个网格，其中 1 为障碍，不能通过。开始位置是左上角，结束位置是右下角。返回一条可行的路径（如果存在）。

**「分析」**

【DFS + 剪枝】一旦找到一条路径即返回。

```cpp
bool dfs(vector<vector<int>> &grid, int n, int m, int i, int j,
         vector<vector<int>> &ans) {
    if (i == n || j == m || grid[i][j] == 1) {
        return false;
    }
    grid[i][j] = 1;
    ans.emplace_back(vector<int> {i, j});
    if (i == n - 1 && j == m - 1) {
        return true;
    }
    if (dfs(grid, n, m, i + 1, j, ans)) {
        return true;
    }
    if (dfs(grid, n, m, i, j + 1, ans)) {
        return true;
    }
    ans.pop_back();
    return false;
}

vector<vector<int>> pathWithObstacles(vector<vector<int>>& obstacleGrid) {
    vector<vector<int>> ans;
    int n = (int)obstacleGrid.size(), m = (int)obstacleGrid[0].size();
    dfs(obstacleGrid, n, m, 0, 0, ans);
    return ans;
}
```
时间复杂度：$O(nm)$，空间复杂度：$O(nm)$


## BFS
**「简介」**
宽度优先搜索（Breadth Ffirst Search）是一种检索方法，优先搜索某一节点的所有直接邻居节点，一般而言，BFS 适用于求解最短长度的问题。在求解过程中，我们通常依赖 queue 这一数据结构。

BFS 有单向和双向两种方式，本质上没有区别，但双向在时间效率上具有优势。

例如，现有一张地图，由 0 和 1 组成，每次移动可以朝上、下、左、右、上左、上右、下左和下右这八个方向移动一格，并只允许落入 0 的格子，求从左上角到右下角的最短距离，如果不能到达，返回 -1。

$$
\begin{matrix}
{\color{Red} 0} & {\color{Red} 0} & {\color{Red} 0} & 0 & 1 \\
0 & 1 & 1 & {\color{Red} 0} & 1 \\
0 & {\color{Red} 0} & {\color{Red} 0} & 1 & 1 \\
{\color{Red} 0} & 1 & 1 & 1 & 0 \\
1 & {\color{Red} 0} & {\color{Red} 0} & {\color{Red} 0} & {\color{Red} 0}
\end{matrix}
$$

最短的长度是 11。

```cpp
int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
    int nRow = (int)grid.size();
    if (nRow == 0) { return -1; }
    if (nRow == 1) { return 1; }
    int nCol = (int)grid[0].size();
    int direction[8][2] = {
        {1, -1}, {1, 0}, {1, 1}, {0, 1},
        {0, -1}, {-1, -1}, {-1, 0}, {-1, 1}
    };
    if (grid[0][0] || grid[nRow-1][nCol-1]) { return -1; }
    queue<vector<int>> q1, q2;
    q1.push({0, 0});
    q2.push({nRow-1, nCol-1});
    int ans = 0;
    while (!q1.empty() && !q2.empty()) {
        int size1 = (int)q1.size();
        while (size1--) {
            int x = q1.front()[0];
            int y = q1.front()[1];
            q1.pop();
            if (grid[x][y] == 3) {
                return ans;
            }
            if (grid[x][y] == 1 || grid[x][y] == 2) {
                continue;
            }
            grid[x][y] = 2;
            for (auto d : direction) {
                int nx = x + d[0];
                int ny = y + d[1];
                if (nx < 0 || nx >= nRow || ny < 0 || ny >= nCol ||
                    grid[nx][ny] == 1 || grid[nx][ny] == 2) {
                    continue;
                }
                q1.push({nx, ny});
            }
        }
        ans++;
        int size2 = (int)q2.size();
        while (size2--) {
            int x = q2.front()[0];
            int y = q2.front()[1];
            q2.pop();
            if (grid[x][y] == 2) {
                return ans;
            }
            if (grid[x][y] == 1 || grid[x][y] == 3) {
                continue;
            }
            grid[x][y] = 3;
            for (auto d : direction) {
                int nx = x + d[0];
                int ny = y + d[1];
                if (nx < 0 || nx >= nRow || ny < 0 || ny >= nCol ||
                    grid[nx][ny] == 1 || grid[nx][ny] == 3) {
                    continue;
                }
                q2.push({nx, ny});
            }
        }
        ans++;
    }
    return -1;
}
```


### 访问所有节点的最短路径
给定一个无向连通图 G，找到一条最短的路径，满足所有的即节点都被访问到。例如，graph = [[1, 2, 3], [ 0 ], [ 0 ], [ 0 ]]，graph[ i ] 表示与 i 相邻的节点列表，最短路径是 1 -> 0 -> 2 -> 0 -> 3，长度是 4

**「分析」**

【状态压缩 + BFS搜索】，设置一个三元组 (u, mask, dist) 表示节点编号、已访问节点的压缩状态、当前节点的距离。

```cpp
int shortestPathLength(vector<vector<int>>& graph) {
    int n = (int)graph.size(), ans = 0;
    queue<vector<int>> q;
    bool **vis = new bool *[n];
    for (int i = 0; i < n; i++) {
        vis[i] = new bool [1 << n]{};
    }

    for (int i = 0; i < n; i++) {
        vis[i][1 << i] = true;
        q.push({i, 1 << i, 0});
    }
    while (!q.empty()) {
        auto tpl = q.front();
        q.pop();
        if (tpl[1] == (1 << n) - 1) {
            ans = tpl[2];
            break;
        }
        for (int i : graph[tpl[0]]) {
            int mask = tpl[1] | (1 << i);
            if (!vis[i][mask]) {
                vis[i][mask] = true;
                q.push({i, mask, tpl[2] + 1});
            }
        }
    }
    
    for (int i = 0; i < n; i++) {
        delete []vis[i];
    }
    delete []vis;
    return ans;
}
```
时间复杂度：$O(2^{n} n^{2})$，空间复杂度：$O(n 2^{n})$
