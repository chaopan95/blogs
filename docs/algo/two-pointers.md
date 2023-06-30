## 基础
双指针是一种$O(n)$时间复杂度的算法，通常配合排序算法和哈希表一起使用。


## 接雨水
给定一个数组 height = [0, 1, 0, 2, 1, 0, 1, 3, 2, 1, 2, 1]，表示地阶的高度，试问能接到最多的雨水量是多少？给出的例子对应答案是 6。

**「分析」**

【双指针】每一个位置能否接到雨水，取决于左右两侧的高度是否均大于当前位置的高度。

```cpp
int trap(vector<int>& height) {
    int ans = 0, n = int(height.size());
    if (n < 3) { return ans; }
    int i = 0, j = n-1;
    int leftMax = 0, rightMax = 0;
    while (i < j)
    {
        if (height[i] < height[j])
        {
            if (height[i] >= leftMax) { leftMax = height[i]; }
            else { ans += leftMax - height[i]; }
            i++;
        }
        else
        {
            if (height[j] >= rightMax) { rightMax = height[j]; }
            else { ans += rightMax - height[j]; }
            j--;
        }
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$


## 三数之和小于 target 的个数
给定一个列表 nums = [0, 1, -2, 3] 和一个整数 target = 2，从 nums 中找出三个下标 $0 \leq i < j < k < n$ 满足对应的元素之和小于 target，试问这样的组合数是多少？

**「分析」**

【双指针】将 nums 排序，外层循环控制 i，内层循环控制 j、k，当 nums[ i ] + nums[ j ] + nums[ k ] < target 时，所有的 (j, k] 也都满足

```cpp
int threeSumSmaller(vector<int>& nums, int target) {
    int n = (int)nums.size(), ans = 0;
    sort(nums.begin(), nums.end());
    for (int i = 0; i < n; i++) {
        int j = i + 1, k = n - 1;
        while (j < k) {
            if (nums[i] + nums[j] + nums[k] < target) {
                ans += k - j;
                j++;
            }
            else {
                k--;
            }
        }
    }
    return ans;
}
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(1)$


## 无重复字符的最长子串
给定一个字符串 s = "abcabcbb"，请你找出其中不含有重复字符的最长子串的长度。

**「分析」**

【双指针 + 哈希去重】

```cpp
int lengthOfLongestSubstring(string s) {
    int n = int(s.length());
    if (n == 0) { return 0; }
    int i = 0, j = 0, maxLen = 0;
    unordered_set<char> hash;
    while (j < n) {
        if (hash.count(s[j])) { hash.erase(s[i++]); }
        else {
            hash.insert(s[j++]);
            maxLen = max(maxLen, j - i);
        }
    }
    return maxLen;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$


## 三数之和
给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。答案中不可以包含重复的三元组。

**「分析」**

【双指针 + 排序】

```cpp
vector<vector<int>> threeSum(vector<int>& nums) {
    int n = int(nums.size());
    vector<vector<int>> ans;
    if (n < 3) { return ans; }
    sort(nums.begin(), nums.end());
    for (int i = 0; i < n - 2; i++) {
        int j = i + 1, k = n - 1;
        while (j < k) {
            int sum = nums[i] + nums[j] + nums[k];
            if (sum < 0) { j++; }
            else if (sum > 0) { k--; }
            else {
                while (i < k && nums[i] == nums[i+1]) { i++; }
                while (j < k && nums[j] == nums[j+1]) { j++; }
                while (j < k && nums[k] == nums[k-1]) { k--; }
                ans.push_back({nums[i], nums[j], nums[k]});
                j++;
                k--;
            }
        }
    }
    return ans;
}
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(1)$


## 乘积小于 k 的连续子数组个数
给定一个正整数数组 nums = [10, 5, 2, 6] 和一个整数 k，求所有连续子数组乘积小于 k 的个数

**「分析」**

【双指针】如果一个长度为 n 的连续数组乘积小于 k，那么满足条件的子数组个数是 $\frac{n(n + 1)}{2}$，因此，我们用 i、j 两个指针，i 每次向右移动一个单位，满足子数组的个数就加【i - j + 1】

```cpp
int numSubarrayProductLessThanK(vector<int>& nums, int k) {
    int n = (int)nums.size(), prod = 1, ans = 0;
    for (int i = 0, j = 0; i < n; i++) {
        prod *= nums[i];
        while (j <= i && prod >= k) {
            prod /= nums[j++];
        }
        ans += i - j + 1;
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$


## 最多连续 1 的个数
给定一个只包含 0 和 1 的数组 nusm = [1,1,1,0,0,0,1,1,1,1,0]，如果最多 k 次将 0 变成 1，试问，数组中连续 1 子串最大长度是多少

**「分析」**

【双指针】

```cpp
int longestOnes(vector<int>& nums, int k) {
    int ans = 0;
    for (int i = 0, j = 0, cnt = 0; j < nums.size() && i < nums.size(); ) {
        if (nums[j] == 1) {
            j++;
        } else if (cnt < k) {
            cnt++;
            j++;
        } else {
            cnt -= nums[i++] == 0;
        }
        ans = max(ans, j - i);
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$
