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


## 题目
### 最小填充次数
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

### 俄罗斯套娃信封问题
给你一个二维整数数组 envelopes ，其中 envelopes[i] = [wi, hi] ，表示第 i 个信封的宽度和高度。当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。请计算 最多能有多少个 信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。注意：不允许旋转信封。

例如，envelopes = [[5,4],[6,4],[6,7],[2,3]]，返回 3，及最多信封的个数为 3, 组合为: [2,3] => [5,4] => [6,7]。

**「分析」**

「LIS」按照一个方向排序，计算另一个方向的LIS。

```cpp
int maxEnvelopes(vector<vector<int>>& envelopes) {
    int n = int(envelopes.size()), len = 1;
    if (n <= 1) { return n; }
    sort(envelopes.begin(), envelopes.end(), [](const auto& e1, const auto& e2) {
        return e1[0] < e2[0] || (e1[0] == e2[0] && e1[1] > e2[1]);
    });
    vector<vector<int>> dp(n+1, vector<int>{0, 0});
    dp[len] = envelopes[0];
    for (int i = 1; i < n; i++) {
        if (envelopes[i][0] > dp[len][0] && envelopes[i][1] > dp[len][1]) {
            dp[++len] = envelopes[i];
        } else {
            int l = 1, r = len;
            while (l <= r) {
                int m = (l + r) / 2;
                if (dp[m][0] < envelopes[i][0] && dp[m][1] < envelopes[i][1]) {
                    l = m + 1;
                } else {
                    r = m - 1;
                }
            }
            dp[l] = envelopes[i];
        }
    }
    return len;
}
```
时间复杂度：$O(n\log n)$，空间复杂度：$O(n)$。
