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
        if (vis[i] || (i > 0 && nums[i] == nums[i-1] && vis[i-1])) {
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
