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

### 组合总和
**「只能使用一次」**

给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。candidates 中的每个数字在每个组合中只能使用一次。解集不能包含重复的组合。 


```cpp
/*
示例:
输入: candidates = [2,5,2,1,2], target = 5,
所求解集为:
[
  [1,2,2],
  [5]
]
*/
vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
    vector<vector<int>> res;
    vector<int> arr;
    int n = int(candidates.size());
    if (n == 0) { return res; }
    map<int, int> dict;
    map<int, int>::iterator iter;
    for (int num: candidates) {
        iter = dict.find(num);
        if (iter == dict.end()) { dict[num] = 1; }
        else { dict[num]++; }
    }
    vector<pair<int, int>> nums;
    int size = 0;
    for (iter = dict.begin(); iter != dict.end(); iter++) {
        nums.emplace_back(iter->first, iter->second);
        size++;
    }
    DFS(res, arr, candidates, target, 0, size, nums);
    return res;
}
void DFS(vector<vector<int>> &res, vector<int> &arr,
          vector<int> candidates, int target, int idx,
          int size, vector<pair<int, int>> nums) {
    if (target <= 0 || idx >= size) {
        if (target == 0) { res.push_back(arr); }
        return;
    }
    for (int j = 1; j <= nums[idx].second; j++) {
        for (int k = 0; k < j; k++) { arr.push_back(nums[idx].first); }
        DFS(res, arr, candidates, target-j*nums[idx].first, idx+1, size, nums);
        for (int k = 0; k < j; k++) { arr.pop_back(); }
    }
    DFS(res, arr, candidates, target, idx+1, size, nums);
}
```
时间复杂度 $O(2^{n})$，空间复杂度 $O(n)$。

**「无限使用」**

给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。candidates 中的数字可以无限制重复被选取。解集不能包含重复的组合。 

```cpp
/*
示例：
输入：candidates = [2,3,6,7], target = 7,
所求解集为：
[
  [7],
  [2,2,3]
]
*/
vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
    vector<vector<int>> res;
    vector<int> arr;
    int n = int(candidates.size());
    if (n == 0) { return res; }
    DFS(res, arr, candidates, n, target, 0);
    return res;
}
void DFS(vector<vector<int>> &res, vector<int> &arr,
          vector<int> candidates, int n, int target,
          int idx) {
    if (target == 0) {
        res.push_back(arr);
        return;
    }
    for (int i = idx; i < n; i++) {
        if (target-candidates[i] >= 0) {
            arr.push_back(candidates[i]);
            DFS(res, arr, candidates, n, target-candidates[i], i);
            arr.resize(arr.size()-1);
        }
    }
}
```
时间复杂度 $O(2^{n})$，空间复杂度 $O(n)$。

### 子集
**「序列元素不重复」**

给你一个整数数组 nums ，数组中的元素互不相同 。返回该数组所有可能的子集（幂集）。例如，nums = [1,2,3]，返回 [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]

```cpp
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> ans;
        int n = int(nums.size());
        vector<int> arr;
        dfs(ans, arr, nums, n, 0);
        return ans;
    }
    void dfs(vector<vector<int>> &ans,
             vector<int> &arr,
             vector<int> &nums,
             int n, int idx) {
        if (idx == n) {
            ans.emplace_back(arr);
            return;
        }
        arr.emplace_back(nums[idx]);
        dfs(ans, arr, nums, n, idx+1);
        arr.pop_back();
        dfs(ans, arr, nums, n, idx+1);
    }
};
```
时间复杂度：$O(2^{n})$，空间复杂度：$O(2^{n})$。

**「序列元素有重复」**

```cpp
/*
Input: nums = [1,2,2]
Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]
*/
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> ans;
        int n = int(nums.size());
        sort(nums.begin(), nums.end());
        vector<int> arr;
        dfs(ans, arr, nums, 0, n);
        return ans;
    }
    void dfs(vector<vector<int>> &ans,
             vector<int> &arr,
             vector<int> &nums,
             int idx, int n) {
        for (int i = idx; i < n; i++) {
            if (i > idx && nums[i] == nums[i-1]) { continue; }
            arr.emplace_back(nums[i]);
            dfs(ans, arr, nums, i+1, n);
            arr.pop_back();
        }
        ans.emplace_back(arr);
    }
};
```
时间复杂度：$O(2^{n})$，空间复杂度：$O(2^{n})$。

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

### 括号生成
数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。例如，n = 3，返回 ["((()))","(()())","(())()","()(())","()()()"]。

**「分析」**

「DFS」

```cpp
void dfs(vector<string> &ans, string s, int n, int a, int b) {
    if (a == n && b == n) {
        ans.emplace_back(s);
        return;
    }
    if (a > n || b > n) {
        return;
    }
    if (a == b) {
        dfs(ans, s + "(", n, a + 1, b);
    } else if (a > b) {
        dfs(ans, s + "(", n, a + 1, b);
        dfs(ans, s + ")", n, a, b + 1);
    }
}
vector<string> generateParenthesis(int n) {
    vector<string> ans;
    dfs(ans, "", n, 0, 0);
    return ans;
}
```
时间复杂度：$O(2^{n})$，空间复杂度：$O(n)$。


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

### 最小高度树
树是一个无向图，其中任何两个顶点只通过一条路径连接。 换句话说，一个任何没有简单环路的连通图都是一棵树。

给你一棵包含 n 个节点的树，标记为 0 到 n - 1 。给定数字 n 和一个有 n - 1 条无向边的 edges 列表（每一个边都是一对标签），其中 edges[i] = [ai, bi] 表示树中节点 ai 和 bi 之间存在一条无向边。

可选择树中任何一个节点作为根。当选择节点 x 作为根节点时，设结果树的高度为 h 。在所有可能的树中，具有最小高度的树（即，min(h)）被称为 最小高度树 。

请你找到所有的 最小高度树 并按 任意顺序 返回它们的根节点标签列表。

树的 高度 是指根节点和叶子节点之间最长向下路径上边的数量。

$$
\begin{matrix}
& & & 1 \\
& & & \uparrow\\
& & & 0 \\
& & \swarrow & & \searrow \\
& 2 & & & & 3
\end{matrix}
$$

最小的高度为 1，根节点是 0

**「分析」**

【BFS + 拓扑排序】对于一个无向图，找到所有度为 1 的节点，在此基础上不断向内蔓延，直到最内一层的节点。

```cpp
vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
    vector<int> ans, degree(n, 0);
    if (n == 1) {
        return {0};
    }
    unordered_map<int, vector<int>> neighbors;
    for (vector<int> edge : edges) {
        neighbors[edge[0]].emplace_back(edge[1]);
        neighbors[edge[1]].emplace_back(edge[0]);
        degree[edge[0]]++;
        degree[edge[1]]++;
    }
    unordered_set<int> vis;
    queue<int> q;
    for (int i = 0; i < n; i++) {
        if (degree[i] == 1) {
            vis.emplace(i);
            q.push(i);
            degree[i]--;
        }
    }
    while (!q.empty()) {
        ans.clear();
        size_t q_size = q.size();
        for (int _ = 0; _ < q_size; _++) {
            int front = q.front();
            q.pop();
            ans.emplace_back(front);
            for (int neighbor : neighbors[front]) {
                if (vis.count(neighbor)) {
                    continue;
                }
                degree[neighbor]--;
                if (degree[neighbor] == 1) {
                    q.push(neighbor);
                    vis.emplace(neighbor);
                }
            }
        }
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$

### 打开转盘锁
你有一个带有四个圆形拨轮的转盘锁。每个拨轮都有10个数字： '0', '1', '2', '3', '4', '5', '6', '7', '8', '9' 。每个拨轮可以自由旋转：例如把 '9' 变为 '0'，'0' 变为 '9' 。每次旋转都只能旋转一个拨轮的一位数字。锁的初始数字为 '0000' ，一个代表四个拨轮的数字的字符串。列表 deadends 包含了一组死亡数字，一旦拨轮的数字和列表里的任何一个元素相同，这个锁将会被永久锁定，无法再被旋转。字符串 target 代表可以解锁的数字，你需要给出解锁需要的最小旋转次数，如果无论如何不能解锁，返回 -1 。

**「分析」**

「BFS」

```cpp
int openLock(vector<string>& deadends, string target) {
    string init = "0000";
    int n = int(init.length()), ans = 0;
    unordered_set<string> vis;
    for (string &str : deadends) { vis.insert(str); }
    if (vis.find(init) != vis.end()) { return -1; }
    queue<string> qLock;
    qLock.push(init);
    while (!qLock.empty()) {
        int num = int(qLock.size());
        while (num--) {
            string str = qLock.front();
            qLock.pop();
            if (str == target) { return ans; }
            for (int i = 0; i < n; i++) {
                string tmp = str;
                for (int d = -1; d <= 1; d += 2) {
                    tmp[i] = (str[i] - '0' + 10 + d) % 10 + '0';
                    if (vis.find(tmp) == vis.end()) {
                        vis.insert(tmp);
                        qLock.push(tmp);
                    }
                }
            }
        }
        ans++;
    }
    return -1;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$。
