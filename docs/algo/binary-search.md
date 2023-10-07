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

### 找到乘法表中第 k 小的数
给定三个整数 m、n、k 表示乘法口诀 $m \times n$ 中第 k 小的数字。例如，m = 3，n = 2，k = 2 则返回 2。

**「分析」**

【二分法】。二分查找是确定最终的返回值，内层遍历每一列确定个数。

```cpp
int findKthNumber(int m, int n, int k) {
    int l = 1, r = m * n + 1;
    if (m < n) {
        swap(m, n);
    }
    while (l < r) {
        int t = (l + r) >> 1;
        int cnt = 0;
        for (int i = 1; i <= n; i++) {
            cnt += min(t / i, m);
        }
        if (cnt < k) {
            l = t + 1;
        }
        else {
            r = t;
        }
    }
    return l;
}
```
时间复杂度：$O(n \log (mn))$，空间复杂度：$O(1)$

### 不用除法求商
```cpp
int divide(int dividend, int divisor) {
    if (dividend == INT_MIN) {
        if (divisor == -1) {
            return INT_MAX;
        }
        if (divisor == 1) {
            return INT_MIN;
        }
    }
    int m = dividend, n = divisor, quo = 0;
    if (m > 0) {
        m = -m;
    }
    if (n > 0) {
        n = -n;
    }
    while (m <= n) {
        long s = n, incre = 1;
        while ((s<<1) >= m) {
            s <<= 1;
            incre <<= 1;
        }
        quo += incre;
        m -= s;
    }
    if ((dividend < 0) ^ (divisor < 0)) { quo *= -1; }
    return quo;
}
```
时间复杂度：$O(n \log (m))$，空间复杂度：$O(1)$，其中 m 是被除数


### 搜索旋转排序数组
```cpp
/*
Input: nums = [4,5,6,7,0,1,2], target = 3
Output: -1
*/
int search(vector<int>& nums, int target) {
    int n = int(nums.size());
    int l = 0, r = n - 1;
    while (l <= r) {
        int m = (l + r) >> 1;
        if (nums[m] == target) { return m; }
        if (nums[l] <= nums[r]) {
            if (nums[m] < target) { l = m + 1; }
            else if (nums[m] > target) { r = m - 1; }
        } else {
            if (target >= nums[l] && nums[m] >= nums[l]) {
                if (nums[m] > target) { r = m - 1; }
                else { l = m + 1; }
            } else if (target >= nums[l] && nums[m] <= nums[r]) {
                r = m - 1;
            } else if (target <= nums[r] && nums[m] <= nums[r]) {
                if (nums[m] > target) { r = m - 1; }
                else { l = m + 1; }
            } else if (target <= nums[r] && nums[m] >= nums[l]) {
                l = m + 1;
            } else {
                if (nums[m] < target) { r = m - 1; }
                else { l = m + 1; }
            }
        }
    }
    return -1;
}
```
时间复杂度 $O(\log n)$，空间复杂度 $O(1)$。


### 搜索旋转排序数组II
```cpp
/*
Input: nums = [2,5,6,0,0,1,2], target = 0
Output: true
*/
bool search(vector<int>& nums, int target) {
    int n = int(nums.size());
    if (n == 0) { return false; }
    int l = 0, r = n - 1;
    while (l <= r) {
        int m = (l + r) >> 1;
        if (nums[m] == target) { return true; }
        if (nums[l] < nums[r]) {
            if (nums[m] > target) { r = m - 1; }
            else { l = m + 1; }
        }
        else {
            if (nums[m] == nums[l] && nums[m] == nums[r]) {
                l++; r--;
            } else if ((target >= nums[l] && nums[m] >= nums[l]) ||
                (target <= nums[r] && nums[m] <= nums[r])) {
                if (nums[m] > target) { r = m - 1; }
                else { l = m + 1; }
            } else if (target >= nums[l] && nums[m] <= nums[r]) {
                r = m - 1;
            } else if (target <= nums[r] && nums[m] >= nums[l]) {
                l = m + 1;
            } else {
                if (nums[m] > target) { l = m + 1; }
                else { r = m - 1; }
            }
        }
    }
    return false;
}
```
时间复杂度 $O(n)$，空间复杂度 $O(1)$。


### 搜索二维矩阵
```cpp
/*
编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：
每行中的整数从左到右按升序排列。
每行的第一个整数大于前一行的最后一个整数。
1   3   5   7
10  11  16  20
23  30  34  60
*/
bool searchMatrix(vector<vector<int>>& matrix, int target) {
    int nRow = int(matrix.size());
    if (nRow == 0) { return false; }
    int nCol = int(matrix[0].size());
    if (nCol == 0) { return false; }
    int l = 0, r = nRow * nCol - 1;
    while (l <= r) {
        int m = (l + r) >> 1;
        int i = m / nCol, j = m % nCol;
        if (matrix[i][j] < target) { l = m + 1; }
        else if (matrix[i][j] > target) { r = m - 1; }
        else { return true; }
    }
    return false;
}
```
时间复杂度 $O(\log(nm))$，空间复杂度 $O(1)$。


### 在 D 天内送达包裹的能力
传送带上的包裹必须在 D 天内从一个港口运送到另一个港口。传送带上的第 i 个包裹的重量为 weights[i]。每一天，我们都会按给出重量的顺序往传送带上装载包裹。我们装载的重量不会超过船的最大运载重量。返回能在 D 天内将传送带上的所有包裹送达的船的最低运载能力。

```cpp
/*
示例 1：
输入：weights = [1,2,3,4,5,6,7,8,9,10], D = 5
输出：15
解释：
船舶最低载重 15 就能够在 5 天内送达所有包裹，如下所示：
第 1 天：1, 2, 3, 4, 5
第 2 天：6, 7
第 3 天：8
第 4 天：9
第 5 天：10

请注意，货物必须按照给定的顺序装运，因此使用载重能力为 14 的船舶并将包装
分成 (2, 3, 4, 5), (1, 6, 7), (8), (9), (10) 是不允许的。 
*/
class Solution {
public:
    int shipWithinDays(vector<int>& weights, int D) {
        int l = 0, r = 0;
        for (int weight : weights) {
            if (l < weight) { l = weight; }
            r += weight;
        }
        while (l < r) {
            int m = (l + r) >> 1;
            int days = 1, weightSum = 0;
            for (int weight : weights) {
                if (weightSum + weight > m) {
                    days++;
                    weightSum = 0;
                }
                weightSum += weight;
            }
            if (days > D) { l = m + 1; }
            else { r = m; }
        }
        return l;
    }
};
```
时间复杂度 $O(n \log(W))$，空间复杂度 $O(1)$，n 是包裹个数，W 是包裹总重量。


### 制作 m 束花所需的最少天数
给你一个整数数组 bloomDay，以及两个整数 m 和 k 。现需要制作 m 束花。制作花束时，需要使用花园中 相邻的 k 朵花。花园中有 n 朵花，第 i 朵花会在 bloomDay[i] 时盛开，恰好可以用于一束花中。请你返回从花园中摘 m 束花需要等待的最少的天数。如果不能摘到 m 束花则返回 -1 。

```cpp
/*
示例 1：
输入：bloomDay = [1,10,3,10,2], m = 3, k = 1
输出：3
解释：让我们一起观察这三天的花开过程，x 表示花开，而 _ 表示花还未开。
现在需要制作 3 束花，每束只需要 1 朵。
1 天后：[x, _, _, _, _]   // 只能制作 1 束花
2 天后：[x, _, _, _, x]   // 只能制作 2 束花
3 天后：[x, _, x, _, x]   // 可以制作 3 束花，答案为 3

示例 2：
输入：bloomDay = [1,10,3,10,2], m = 3, k = 2
输出：-1
解释：要制作 3 束花，每束需要 2 朵花，也就是一共需要 6 朵花。而花园中只有 5
朵花，无法满足制作要求，返回 -1 。
*/
class Solution {
public:
    int minDays(vector<int>& bloomDay, int m, int k) {
        int n = (int)bloomDay.size();
        if (n < m * k) { return -1; }
        int minDay = INT_MAX, maxDay = 0;
        for (int day : bloomDay) {
            minDay = min(minDay, day);
            maxDay = max(maxDay, day);
        }
        while (minDay < maxDay) {
            int midDay = (minDay + maxDay) >> 1;
            if (canMake(bloomDay, m, k, midDay)) {
                maxDay = midDay;
            }
            else {
                minDay = midDay + 1;
            }
        }
        return maxDay;
    }
    bool canMake (vector<int> &bloomDay, int m, int k, int days) {
        int bouquets = 0, flowers = 0;
        for (int bd : bloomDay) {
            if (bd <= days) {
                flowers++;
                if (flowers == k) {
                    bouquets++;
                    flowers = 0;
                }
            }
            else {
                flowers = 0;
            }
        }
        return bouquets >= m;
    }
};
```
时间复杂度 $O(n \log(Tot))$，空间复杂度 $O(1)$，n 是花数，Tot 是 bloomDay 的总和。


### 小张刷题计划
为了提高自己的代码能力，小张制定了 LeetCode 刷题计划，他选中了 LeetCode 题库中的 n 道题，编号从 0 到 n-1，并计划在 m 天内按照题目编号顺序刷完所有的题目（注意，小张不能用多天完成同一题）。在小张刷题计划中，小张需要用 time[i] 的时间完成编号 i 的题目。此外，小张还可以使用场外求助功能，通过询问他的好朋友小杨题目的解法，可以省去该题的做题时间。为了防止“小张刷题计划”变成“小杨刷题计划”，小张每天最多使用一次求助。我们定义 m 天中做题时间最多的一天耗时为 T（小杨完成的题目不计入做题总时间）。请你帮小张求出最小的 T是多少。

```cpp
/*
示例：
输入：time = [1,2,3,3], m = 2
输出：3
解释：第一天小张完成前三题，其中第三题找小杨帮忙；第二天完成第四题，并且找小杨帮忙。这样
做题时间最多的一天花费了 3 的时间，并且这个值是最小的。
*/
int minTime(vector<int>& time, int m) {
    int n = int(time.size()), tot = 0, minT = INT_MAX;
    if (n == 0 || n <= m) { return 0; }
    for (int t : time) {
        tot += t;
        minT = min(minT, t);
    }
    int l = minT, r = tot;
    while (l < r) {
        int mid = (l + r) >> 1;
        int days = 1, curSum = 0, maxT = 0;
        for (int t : time) {
            if (t > maxT) {
                curSum += maxT;
                maxT = t;
            }
            else {
                curSum += t;
            }
            if (curSum > mid) {
                days++;
                curSum = 0;
                maxT = t;
            }
        }
        if (days > m) {
            l = mid + 1;
        }
        else {
            r = mid;
        }
    }
    return r;
}
```
时间复杂度 $O(n \log(Tot))$，空间复杂度 $O(1)$，n 是天数，Tot 是 time 的总和。
