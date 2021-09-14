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

### 两数和的最小值
给定一个长度位偶数的数组 nums = [3, 5, 4, 2, 4, 6]，将数组内的元素两两配对，并计算两者和，试问所有的配对和的最小值是多少？

**「分析」**

我们希望得到最小和，那么优先选择最小值和最大值配对，这符合【贪心】的想法。并且我们可以证明，有序的四个数：a、b、c、d，只有 （a + d）、(b + c) 这样的组合具有最小和。那么，对于 2n 个元素，最优配对如下

$$
s = \min \{x_{1}+x_{2n}, x_{2}+x_{xn-1}, \cdots, x_{n}+x_{n+1}\}
$$

假设我们调整两个值 $x_{i}$, $x_{j}$ 的位置，得到一个新的解 s‘，利用上面的 a、b、c、d 的证明姐结果，可知新解 s' 一定是不优于 s 的。

```cpp
int minPairSum(vector<int>& nums) {
    int ans = INT_MIN, n = (int)nums.size();
    sort(nums.begin(), nums.end());
    for (int i = 0; i < n / 2; i++) {
        ans = max(ans, nums[i] + nums[n - 1 - i]);
    }
    return ans;
}
```

### 在 D 天内完成运输
给定一个数组 nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10] 和一个整数 D = 5，要求在 D 天内将这些货物运输，求每一天能够运输量的最小值。

```cpp
int shipWithinDays(vector<int>& weights, int days) {
    int l = 0, r = 0;
    for (int w : weights) {
        l = max(l, w);
        r += w;
    }
    while (l < r) {
        int m = (l + r) >> 1;
        int ds = 1, tot = 0;
        for (int w : weights) {
            if (w + tot > m) {
                tot = w;
                ds++;
            }
            else {
                tot += w;
            }
        }
        if (ds > days) {
            l = m + 1;
        }
        else {
            r = m;
        }
    }
    return l;
}
```
时间复杂度：$O(n \log(sum - max))$，空间复杂度：$O(1)$，sum 是数组的和，max 是数组的最大值

### 最小的船数
给定一个人员体重的列表 people = [3, 5, 3, 4] 和一个整数 limit = 5，一艘船一次最多载两个人，并且重量和不超过 limit，试问最小需要多少艘船？

**「分析」**

【排序 + 贪心】尽可能的让一艘船载两个人，从体重大的人开始遍历，如果能遇到一个轻的人，就把他加入到船上。

```cpp
int numRescueBoats(vector<int>& people, int limit) {
    int ans = 0, l = 0, r = (int)people.size() - 1;
    sort(people.begin(), people.end());
    while (l < r) {
        if (people[l] + people[r] <= limit) {
            l++;
        }
        ans++;
        r--;
    }
    return ans + (l == r);
}
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(1)$

### 均分数字
给定一个列表 nums = [1, 0, 5]，每次可以选若干个位置，将数字减一，然后对相邻的一个位置加一，如下

$$
\begin{matrix}
\text{1st: } & 1 & & 0 & \leftarrow & 5 & & \Rightarrow & & 1 & & 1 & & 4 \\
\text{2nd: } & 1 & \leftarrow & 1 & \leftarrow & 4 & & \Rightarrow & & 2 & & 1 & & 3 \\
\text{3rd: } & 2 & \leftarrow & 1 & \leftarrow & 3 & & \Rightarrow & & 2 & & 2 & & 2 \\
\end{matrix}
$$

**「分析」**

【贪心】先判断数值之和能否被数组长度整除。如果可以的话，每一个数减去平均值，每一个位置如果是正数 k，那么意味着，最少 k 次【减一】，如果是负数，最少 k 次【加一】，并且如果出现连续几个正数或者负数，这意味着，【减一】或者【加一】需要传递到后面的位置，例如 [1, 1, -1, -1] 和 [1, -1, -1, 1] 是不同的，前者需要 2 次，而后者需要 1 次，这是因为 [1, 1] 毗邻出现。

```cpp
int findMinMoves(vector<int>& machines) {
    int sum = 0, cnt = 0;
    for (int m : machines) {
        sum += m;
        cnt++;
    }
    if (sum % cnt != 0) {
            return -1;
    }
    int ans = 0, avg = sum / cnt;
    for (int &m : machines) {
        m -= avg;
    }
    int presum = 0;
    for (int m : machines) {
        presum += m;
        ans = max(ans, max(abs(presum), m));
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$

### IPO
给你 n 个项目。对于每个项目 i ，它都有一个纯利润 profits[i] ，和启动该项目需要的最小资本 capital[i] 。最初，你的资本为 w 。当你完成一个项目时，你将获得纯利润，且利润将被添加到你的总资本中。总而言之，从给定项目中选择 最多 k 个不同项目的列表，以 最大化最终资本 ，并输出最终可获得的最多资本。

如k = 2, w = 0, profits = [1,2,3], capital = [0,1,1]

输出：4

解释：由于你的初始资本为 0，你仅可以从 0 号项目开始。在完成后，你将获得 1 的利润，你的总资本将变为 1。此时你可以选择开始 1 号或 2 号项目。由于你最多可以选择两个项目，所以你需要完成 2 号项目以获得最大的资本。因此，输出最后最大化的资本，为 0 + 1 + 3 = 4。

**「分析」**

【贪心 + 大顶堆】因为 w 一直在变换，我门排除了动态规划的可能性。题目的意思是，每次从所有不超过 w 的项目中选择最大的收益的那个，然后更新 w，这就是【贪心】的思想。

```cpp
int findMaximizedCapital(int k, int w,
                         vector<int>& profits,
                         vector<int>& capital) {
    priority_queue<int, vector<int>, less<int>> pq;
    vector<vector<int>> cp;
    int n = (int)profits.size();
    for (int i = 0; i < n; i++) {
        cp.emplace_back(vector<int>{capital[i],
                                    profits[i]});
    }
    sort(cp.begin(), cp.end());
    for (int i = 0, j = 0; i < k; i++) {
        for (; j < n && cp[j][0] <= w; j++) {
            pq.push(cp[j][1]);
        }
        if (pq.empty()) { break; }
        w += pq.top();
        pq.pop();
    }
    return w;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$

### 完成任务的最小初始能量
给出一个任务列表 tasks = [[1, 2],[2, 4],[4, 8]]，表示一个任务的所需要的能量和开启这项任务的能量，如任务1需要能量值 2 才能启动，完成后，扣除能量值 1.试问，最少要准备多少能量值，才能完成所有任务（可以无序）？

**「分析」**

【排序 + 贪心】任意两个任务，t1 和 t2，t1[ 1 ] - t1[ 0 ] > t2[ 1 ] - t2[ 0 ]，如果先完成 t1 后完成 t2，那么 t1 的差值有一部可以给 t2[ 1 ]，反过来，先完成 t2，对 t1 额外补充更多的能量。因此我们选择对 tasks 按照【所需能量值 - 消耗能量值】排序

```cpp
int minimumEffort(vector<vector<int>>& tasks) {
    int ans = 0;
    sort(tasks.begin(), tasks.end(), [](const vector<int> a,
                                        const vector<int> b){
        return a[1] - a[0] < b[1] - b[0];
    });
    for (auto task : tasks) {
        if (ans + task[0] < task[1]) {
            ans = task[1];
        }
        else {
            ans += task[0];
        }
    }
    return ans;
}
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(1)$

### 最小差值
给定一个数组 nums = [1, 3, 6] 和一个整数 k = 3，对数组中的每个元素【加k】或者【减k】，试问，最终的新数组的最大值与最小值之差的最小是多少？例子对应的结果是 3

**「分析」**

【排序 + 贪心】对数组进行排序后，有一个位置 i 满足，i 及其左侧的数值【加k】，i 右侧的数【减k】。并且，min(nums[ 0 ] + k, nums[i + 1] - k) 形成下界，max(nums[ i ] + k, nums[n - 1] - k) 形成下界

```cpp
int smallestRangeII(vector<int>& nums, int k) {
    int n = (int)nums.size();
    sort(nums.begin(), nums.end());
    int ans = nums[n - 1] - nums[0];
    for (int i = 0; i < n - 1; i++) {
        int hi = max(nums[i] + k, nums[n - 1] - k);
        int lo = min(nums[0] + k, nums[i + 1] - k);
        ans = min(ans, hi - lo);
    }
    return ans;
}
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(1)$
