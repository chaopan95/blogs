## 基础
排序算法旨在找到高效的方法让无序的数组变成有序

## 稳定排序
对于相同元素，排序前后的相对位置保持不变。

## 题目
### 下一个排列
整数数组的下一个排列是指其整数的下一个字典序更大的排列。如果不存在下一个更大的排列，那么这个数组必须重排为字典序最小的排列（即，其元素按升序排列）。例如，arr = [1,2,3] 的下一个排列是 [1,3,2] 。

**「分析」**

「排序」

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int n = int(nums.size()), idx = -1;
        if (n < 2) { return; }
        for (int i = n - 1; i > 0; i--) {
            if (nums[i-1] < nums[i]) {
                idx = i - 1;
                break;
            }
        }
        if (idx == -1) {
            sort(nums.begin(), nums.end());
            return;
        }
        for (int i = n - 1; i > idx; i--) {
            if (nums[i] > nums[idx]) {
                swap(nums[i], nums[idx]);
                sort(nums.begin()+idx+1, nums.end());
                return;
            }
        }
    }
};
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(1)$。

### 单调递增的数字
当且仅当每个相邻位数上的数字 x 和 y 满足 x <= y 时，我们称这个整数是单调递增的。给定一个整数 n ，返回 小于或等于 n 的最大数字，且数字呈 单调递增 。

**「分析」**

如果 N 整体逆序，则返回 n；否则，最后一个正序的位置后置成 9。

```cpp
/*
Input: N = 10
Output: 9

Input: N = 1234
Output: 1234

Input: N = 332
Output: 299
*/

int monotoneIncreasingDigits(int N) {
    int ans = 0;
    string str = to_string(N);
    int len = int(str.length()), idx = -1;
    for (int i = 1; i < len; i++) {
        if (str[i] < str[i-1]) {
            idx = i;
            break;
        }
    }
    if (idx == -1) { return N; }
    int pos = idx - 1;
    while (pos >= 1 && str[pos] == str[pos-1]) { pos--; }
    str[pos]--;  // current position decrease by 1
    for (int i = pos + 1; i < len; i++) {
        str[i] = '9';
    }
    for (int i = len-1, pow = 1; i >= 0; i--, pow *= 10) {
        ans += (str[i] - '0') * pow;
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$。

### 最大数
给定一组非负整数 nums，重新排列每个数的顺序（每个数不可拆分）使之组成一个最大的整数。注意：输出结果可能非常大，所以你需要返回一个字符串而不是整数。

```cpp
/*
Input: nums = [3,30,34,5,9]
Output: "9534330"
*/
class Solution {
public:
    string largestNumber(vector<int>& nums) {
        string ans = "";
        if (nums.empty()) { return ans; }
        sort(nums.begin(), nums.end(), [](const int &x, const int &y) {
            long i = 10, j = 10;
            while (i <= x) { i *= 10; }
            while (j <= y) { j *= 10; }
            return j * x + y > i * y + x;
        });
        if (nums[0] == 0) { return "0"; }
        for (const int &ele : nums) { ans += to_string(ele); }
        return ans;
    }
};
```
时间复杂度：$O(n\log n)$，空间复杂度：$O(1)$，这里 n 是 nums 的长度。
