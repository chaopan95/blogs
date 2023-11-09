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
