## 贪心
贪心算法是一种选择算法，具有「短视」特点，即优先选择当前最有利的方案。

## 题目
### 分配人员
公司有 2n 个应聘者参加面试，可以选择A、B其中一处，先给出一个二维数组 costs =[[259, 770], [448, 54], [926, 667], [184, 139], [840, 118], [577, 469]] 表示应聘者去A、B两处的费用，现在需要平均分别 2n 个人到 A、B 两处，试问，最小的成本是多少？

**「分析」**

考虑 2n 个人都去 B 时，总的费用是 $\sum_{i = 1}^{n} \text{costs[ i ][ 1 ]}$。此时，我们需要选择 n 个人放到 A 处，那么如何选择呢？我们注意到，如果将一个人从 B 放到 A，那么对应的总成本变化量是 $\text{costs[ i ][ 0 ] - costs[ i ][ 1 ]}$，刚好对应着成本A - B，那么对于 n 个人，我们希望选择前 n 个最小差值。假设说，我们有一个解，不是差值的前 n 个，那么我们总可以找到一个更小的差值，使得总的费用变小，这就是贪心的思想。

这里，我们用了排序的方法，但是实际上，我们只需要找到中间的某一个值，使得前 n 个差值均小雨后 n 个差值。改进的快排能够实现此功能。

```cpp
int twoCitySchedCost(vector<vector<int>>& costs) {
    int ans = 0, n = (int)costs.size();
    int l = 0, r = n - 1;
    while (true) {
        while (l < r) {
            while (l < r && costs[l][0] - costs[l][1] <=
                   costs[r][0] - costs[r][1]) {
                r--;
            }
            swap(costs[l], costs[r]);
            while (l < r && costs[l][0] - costs[l][1] <=
                   costs[r][0] - costs[r][1]) {
                l++;
            }
            swap(costs[l], costs[r]);
        }
        if (2 * l == n - 2) { break; }
        if (2 * l > n - 2) {
            r = l - 1;
            l = 0;
        }
        else {
            l++;
            r = n - 1;
        }
    }
    for (int i = 0; i < n / 2; i++) {
        ans += costs[i][0] + costs[n - 1 - i][1];
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$，其中 n 是应聘者的总人数。


### 买雪糕
给定一个长度为 n 的数组 costs = [1, 3, 2, 4, 1] 表示雪糕的价格，以及一个整数 coins = 7 表示持有的金额，试问，我们可以买多少支雪糕？

**「分析」**

将雪糕的价格从小到大排序，求累计花费不超过给定金额的雪糕数目。为什么这样是正确的？排序后的雪糕价格：

$$
c_{0} \leq c_{1} \leq ... \leq c_{k} \leq ... \leq c_{n-1}
$$

经过计算，发现前 k 支雪糕是能够买下的，第 k + 1 支雪糕是不能买下。此时是最优解。当然，会存在另一种情况，某支雪糕不是价格前 k 小的，但是已然能够买下，此时对应的给定的钱没有都花完。即便如此，雪糕的总数已然不变。

```cpp
int maxIceCream(vector<int>& costs, int coins) {
    int ans = 0;
    sort(costs.begin(), costs.end());
    for (int cost : costs) {
        if (cost > coins) { return ans; }
        coins -= cost;
        ans++;
    }
    return ans;
}
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(1)$，其中 n 是雪糕的数目。

### 由 X 到 Y 的最少操作数
给定两个正数 X 和 Y，只能将 X 加倍或者将 X 减去一，试问经过多少次上述操作，可以把 X 变成 Y？

**「分析」**

如果 X >= Y，那么只能执行减一操作；反之，执行加倍操作快速逼近 Y ，在执行减一操作。但是在计算过程中，考虑到数据范围问题，我们倾向使用逆向思维，即将 Y 减半或者加一变成 X。注意到，当 Y 为奇数时，只能执行加一操作。

```cpp
int brokenCalc(int x, int y) {
    if (x >= y) { return x - y; }
    int ans = 0;
    while (x < y) {
        if (y % 2 != 0) {
            y++;
        }
        else {
            y >>= 1;
        }
        ans++;
    }
    return ans + x - y;
}
```
时间复杂度：$O(\log y)$，空间复杂度：$O(1)$。

### 增加 k 后高频元素出现的次数
给定一个数组 nums = [1, 2, 4] 和一个整数 k = 5，每次允许选数组中的一个数加一，最多执行 k 次。试问，这样的操作后，数组中的最高频元素出现的次数是多少？上述例子的结果是 3。

**「分析」**

【加一】操作是单向行为，即只有将较小的数【加一】才有意义，并且，越靠近目标的数【加一】的次数越少，这符合了【贪心】的思想。因此，解题办法是【排序】+【二分查找】+【前缀和】，我们将数组排序后，相同的值靠在一起。任意一个可行解必然满足这样一个特征：它是一个区间 [l, r] 这个区间的所有值都会变成 nums[ r ]，我们想找最高频的元素出现的次数，也就是 [l, r] 这个区间的长度，自然地希望这个区间越长越好。但是，这个区间需要满足一个要求，【加一】的操作次数不超过 k，那么如何计算【加一】操作数呢？答案是【前缀和】，即【加一】操作数等于【操作后的区间和】减去【操作前的区间和】。

```cpp
int maxFrequency(vector<int>& nums, int k) {
    int n = (int)nums.size(), ans = 0;
    if (n == 0) { return 0; }
    sort(nums.begin(), nums.end());
    vector<long> sum(n+1, 0);
    for (int i = 0; i < n; i++) {
        sum[i + 1] = sum[i] + nums[i];
        int l = 0, r = i;
        while (l < r) {
            int m = (l + r) >> 1;
            if ((long)(i + 1 - m) * nums[i] - (sum[i + 1] - sum[m]) > k) {
                l = m + 1;
            }
            else {
                r = m;
            }
        }
        ans = max(ans, i - l + 1);
    }
    return ans;
}
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(n)$