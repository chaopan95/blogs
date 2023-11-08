## 原理
快速排序找到一个中轴（pivot）使得右侧的数值不小于中轴值且左侧的数值不大于中轴值。


## 题目
### 数组中的第K个最大元素
给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。你必须设计并实现时间复杂度为 O(n) 的算法解决此问题。例如，[3,2,1,5,6,4], k = 2，返回 5。

**「分析」**

「改进版快速排序」

```cpp
int findKthLargest(vector<int>& nums, int k) {
    int n = int(nums.size());
    k = n - k;
    int b = 0, e = n - 1;
    while (true) {
        int i = b, j = e;
        while (i < j) {
            while (i < j && nums[i] <= nums[j]) { j--; }
            swap(nums[i], nums[j]);
            while (i < j && nums[i] <= nums[j]) { i++; }
            swap(nums[i], nums[j]);
        }
        if (i == k) { return nums[i]; }
        else if (i > k) { e = i - 1; }
        else { b = i + 1; }
    }
    return 0;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$。