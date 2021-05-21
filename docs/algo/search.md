## DFS
**「简介」**
深度优先搜索（Depth Ffirst Search）是一种检索方法，优先搜索某一节点的子节点，如果可以搜索到目标，即停止或将结果保存起来；反之，回溯到分叉处，依次遍历下一个节点。

### 全排列
**「序列元素不重复」**对一个序列如 [1, 2, 3] ,元素互不相同，求其全排列

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

**「序列元素有重复」**对一个序列如 [3, 3, 0, 3] ，元素有重复，求其全排列

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
