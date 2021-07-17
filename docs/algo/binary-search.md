## 基本概念
二分查找时一种高效的查找算法，它要求查找的数组是（局部）有序的，每次可以查找后，利用单调性，缩小一半的查找范围。

## 题目
### 实现lower_bound 和 upper_bound
lower_bound() 查找 nums 数组中第一个不小于 target 的下标（如果不存在，返回下标为 n，即数组的长度）；upper_bound(nums, target) 查找 nums 数组中第一个大于 target 的数值下标（如果不存在，同上）。

```cpp
int lower_bound(vector<int> &nums, int target) {
    int l = 0, r = (int)nums.size();
    while (l < r) {
        int m = (l + r) >> 1;
        if (nums[m] < target) {
            l = m + 1;
        }
        else {
            r = m;
        }
    }
    return l;
}
int upper_bound(vector<int> &nums, int target) {
    int l = 0, r = (int)nums.size();
    while (l < r) {
        int m = (l + r) >> 1;
        if (nums[m] <= target) {
            l = m + 1;
        }
        else {
            r = m;
        }
    }
    return l;
}
```

### 绝对值之和
给定两个数组 nums1 = [1, 28, 21] 和 nums2 = [9, 21, 20]，可以将 nums1 中的某一个值用另一个值替换，也可以不用替换，求两个数组相对位置的差的绝对值之和。上述例子的结果是 9。

**「分析」**

差的绝对值之和计算方式如下：

$$
\text{sum} = \sum_{i = 0}^{n-1} \left \| \text{nums1[ i ] - nums2[ i ]} \right \| 
$$

假设我们在 i 这个位置上发生变动

$$
\arg \min_{i \in [0, n)} -\left \| \text{nums1[ i ] - nums2[ i ]} \right \| + \left \| \text{nums1[ j ] - nums2[ i ]} \right \| 
$$

这里，j 是 [0, n) 中的任意一个值。我们希望找到一个 i 使得上式最小。我们使用二分查找的方式，找到最靠近 nums2[ i ] 的 j，需要将 nums1 排序。

```cpp
int minAbsoluteSumDiff(vector<int>& nums1, vector<int>& nums2) {
    const int mod = 1000000007;
    int n = (int)nums1.size(), ans = 0, diff = 0;
    if (n == 0) { return 0; }
    vector<int> arr = nums1;
    sort(arr.begin(), arr.end());
    for (int i = 0; i < n; i++) {
        ans += abs(nums1[i] - nums2[i]);
        ans %= mod;
        int chut = -abs(nums1[i] - nums2[i]);
        size_t a = distance(arr.begin(),
                            lower_bound(arr.begin(), arr.end(), nums2[i]));
        if (a < n) {
            diff = min(diff, chut + arr[a] - nums2[i]);
        }
        if (a > 0) {
            diff = min(diff, chut + nums2[i] - arr[a - 1]);
        }
    }
    return (ans + diff + mod) % mod;
}
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(n)$