## 基础
给定数组arr，设长度为n，输出arr的最长递增子序列。（如果有多个答案，请输出其中字典序最小的）。input: 1, 2, 8, 6, 4。output: 1, 2, 4

```cpp
vector<int> LIS(vector<int>& arr) {
    vector<int> ans;
    int n = (int)arr.size();
    if (n == 0) { return ans; }
    vector<int> dp(n, 1);
    int len = 0;
    for (int i = 0; i < n; i++) {
        int ele = arr[i];
        if (ans.empty() || ans.back() < ele) {
            ans.emplace_back(ele);
            len++;
            dp[i] = len;
        }
        else {
            int l = 0, r = len - 1;
            while (l <= r) {
                int m = (l + r) >> 1;
                if (ans[m] >= ele) { r = m - 1; }
                else { l = m + 1; }
            }
            ans[l] = ele;
            dp[i] = l + 1;
        }
    }
    for (int i = n - 1; i >= 0; i--) {
        if (dp[i] == len) {
            ans[--len] = arr[i];
        }
    }
    return ans;
}
```
```cpp
vector<int> lengthOfLIS(vector<int>& nums) {
    int len = 0;
    vector<int> dp;
    for (int i = 0; i < nums.size(); i++) {
        int num = nums[i];
        if (dp.empty() || dp.back() < num) {
            dp.emplace_back(num);
            len++;
        } else {
            int l = 0, r = len;
            while (l < r) {
                int m = (l + r) >> 1;
                if (dp[m] < num) {
                    l = m + 1;
                } else {
                    r = m;
                }
            }
            dp[l] = num;
        }
    }
    return dp;
}
```
时间复杂度：$O(n \log n)$，空见复杂度：$O(n)$


## 最小填充次数
给定两个数组 target = [6, 4, 8, 1, 3, 2] 和 arr = [4, 7, 6, 2, 3, 8, 6, 1]，其中 target 内的元素各不相同，每次可以在 arr 的任意位置添加一个元素，试问最少添加多少次，使得 target 成为 arr 的一个子序列。

**「分析」**

直观地想法是求 arr 和 target 的LCS，时间复杂度 $O(nm)$，本题超时，考虑到 targte 中的元素各不相同，如果给 targte 数具有唯一的下标，那么映射到 arr 上，就可以求 LIS，最终的答案就是 len(targte) - (len)LIS。

```cpp
int LIS(vector<int> nums) {
    int n = (int)nums.size();
    vector<int> lis;
    for (int i = 0; i < n; i++) {
        if (lis.empty() || lis.back() < nums[i]) {
            lis.emplace_back(nums[i]);
        }
        else {
            int l = 0, r = (int)lis.size();
            while (l < r) {
                int m = (l + r) >> 1;
                if (lis[m] < nums[i]) {
                    l = m + 1;
                }
                else {
                    r = m;
                }
            }
            lis[l] = nums[i];
        }
    }
    return (int)lis.size();
}
int minOperations(vector<int>& target, vector<int>& arr) {
    int n1 = (int)target.size();
    unordered_map<int, int> idx;
    for (int i = 0; i < n1; i++) {
        idx[target[i]] = i;
    }
    vector<int> nums;
    for (int ele : arr) {
        if (idx.count(ele)) {
            nums.emplace_back(idx[ele]);
        }
    }
    return n1 - LIS(nums);
}
```
时间复杂度：$O(m + n \log n)$，空见复杂度：$O(n)$，m 是 arr 的长度，n 是 target 的长度
