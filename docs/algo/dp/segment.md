## M个子数组的最大和
给定一个整数数组nums和一个正整数M，返回nums数组中M个连续子数组的元素最大和

例如：nums = [-1, 4, -2, 3, -2, 3]，M = 2，最大和为8 = [ 4 ] + [3, -2, 3]

dp[ i ][ j ]表示，前j个元素在i个子数组中的最大和，其中nums[ j ]落在第i个子数组（1 <= i <= M，i <= j <= n）

状态转移

$$
\text{dp[ i ][ j ]} = \max
\begin{cases}
\text{dp[ i ][j - 1] + nums[ i - 1 ]} \\
\max_{t \in [i-1, j)}(\text{dp[i - 1][t] + nums[i - 1]})
\end{cases}, \text{ where } 1 \leq i \leq M \quad i \leq j \leq n
$$

注意到当前状态dp[ i ][ j ]只与dp[ i ][j - 1]、dp[i - 1][ t ]两个状态相关，因为可以使用O(n)的空间，pre[ j ]表示上一层的状态，cur[ j ]表示当前状态

$$
\begin{aligned}
& \text{cur[ j ] = dp[ i ][j - 1] + nums[ i - 1 ]} \\
& \text{pre[ j ] = dp[i - 1][t] + nums[i - 1]}
\end{aligned}
$$

```cpp
int maxMSum(vector<int> nums, int M) {
    int n = (int)nums.size(), maxSum = INT_MIN;
    vector<int> cur(n+1, 0), pre(n+1, 0);
    for (int i = 1; i <= M; i++) {
        maxSum = INT_MIN;
        for (int j = i; j <= n; j++) {
            cur[j] = max(pre[j-1], cur[j-1]) + nums[j-1];
            pre[j-1] = maxSum;
            maxSum = max(maxSum, cur[j]);
        }
        pre[n] = maxSum;
    }
    return maxSum;
}
```

当M = 1时
```cpp
vector<int> getMaxSumEpisode(vector<int> nums) {
    int n = int(nums.size());
    vector<int> ans;
    if (n == 0) { return ans; }
    int maxSum = -(1ll << 31), curSum = 0;
    int left = 0, right = 0, begin = 0;
    for (int i = 0; i < n; i++) {
        if (curSum >= 0) { curSum += nums[i]; }
        else {
            curSum = nums[i];
            begin = i;
        }
        if (maxSum < curSum) {
            maxSum = curSum;
            left = begin;
            right = i;
        }
    }
    for (int i = left; i <= right; i++) {
        ans.push_back(nums[i]);
    }
    return ans;
}
```

**相似题目**
??? note "[「Leetcode 152. 乘积最大子数组」](https://leetcode-cn.com/problems/maximum-product-subarray/)"
    给你一个整数数组 nums ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。

    输入: [2,3,-2,4]
    
    输出: 6

    解释: 子数组 [2,3] 有最大乘积 6。

    ```cpp
    int maxProduct(vector<int>& nums) {
        int n = int(nums.size());
        int minF = nums[0], maxF = nums[0], ans = nums[0];
        for (int i = 1; i < n; i++) {
            int _min = minF, _max = maxF;
            minF = min(nums[i], min(_min * nums[i], _max * nums[i]));
            maxF = max(nums[i], max(_min * nums[i], _max * nums[i]));
            ans = max(ans, maxF);
        }
        return ans;
    }
    ```

## 子数组长度不超过 k 的最大和
给定一个数组 nums = [1, 15, 7, 9, 2, 5, 10] 和一个整数 k = 3，表示将数组分成若干个连续子数组，但是子数组的长度不超过 k，分割后的子数组统统变成子数组的最大值，求变化后的数组和。上例对应的答案是 [15, 15, 15, 9, 10, 10, 10]，数组和是 84。

**「分析」**

对每一个数组元素来说，它只能处于一个区间内。设 dp[ i ] 表示从第 i 个元素到最后一个元素，在题目规则下，可以得到的最大和。状态转移方程如下：

$$
\text{dp[ i ]} = \max_{j \in [i, i + k)} \{ \text{dp[j + 1] + (j - i + 1) * } \max(\text{A[i : j]}) \}
$$

```cpp
int maxSumAfterPartitioning(vector<int>& arr, int k) {
    int n = (int)arr.size();
    if (n <= k) {
        return *max_element(arr.begin(), arr.end()) * n;
    }
    vector<int> dp(n + 1, 0);
    for (int i = n - 1; i >= 0; i--) {
        int _max = INT_MIN;
        for (int j = i; j < min(n, i + k); j++) {
            _max = max(_max, arr[j]);
            dp[i] = max(dp[i], dp[j + 1] + _max * (j - i + 1));
        }
    }
    return dp[0];
}
```
时间复杂度：$O(kn)$，空间复杂度：$O(n)$，n 是数组的长度。


## 分割成 m 个子数组的最大和最小值
给定一个非负整数数组 nums 和一个整数 m ，你需要将这个数组分成 m 个非空的连续子数组。试问这 m 个子数组各自和的最大值最小。

**「分析」**

“将数组划分成 m 个子数组”是【动态规划】的常见问法。设 dp[ i ][ j ] 将数组 [i: end] 的所有元素划分成 j 段时，所有 j 段和的最小值。状态转移方程

$$
\text{dp[ i ][ j ]} = \min_{k \in (i, \text{end}]} \{ \text{dp[ k ][j - 1]}, \sum_{t = i}^{k - 1} \text{A[ t ]} \}
$$

这里，我们认为第 i 个元素是属于第 j 个区间，我们从 [i + 1, end] 遍历切分点 k，使得 [i, k] 属于 j 区间，[k + 1, end] 属于前 j - 1 个区间。

```cpp
int splitArray(vector<int>& nums, int m) {
    int n = (int)nums.size();
    vector<vector<int>> dp(n + 1, vector<int>(m + 1, INT_MAX));
    vector<int> sum(n + 1, 0);
    for (int i = 0; i < n; i++) {
        sum[i + 1] = sum[i] + nums[i];
    }
    dp[n][0] = 0;
    for (int i = n - 1; i >= 0; i--) {
        for (int j = 1; j <= min(m, n - i); j++) {
            for (int k = n; k > i; k--) {
                dp[i][j] = min(dp[i][j], max(dp[k][j - 1], sum[k] - sum[i]));
            }
        }
    }
    return dp[0][m];
}
```
时间复杂度：$O(n^{2}m)$，空间复杂度：$O(nm)$


“使...的最大值最小”是【二分法】的常见问法。如果我们选取一个数 val 最为划分值，同一个子数组的和不超过 val，如果 val 较小，所得的子数组个数会超过 m；反之，子数组个数不足 m。由此我们得到一个单调序列，可以使用【二分法】。

```cpp
int splitArray(vector<int>& nums, int m) {
    int r = 0, l = 0;
    for (int num : nums) {
        r += num;
        l = max(l, num);
    }
    while (l < r) {
        int val = (l + r) >> 1;
        int segs = 1, tot = 0;
        for (int num : nums) {
            if (tot + num > val) {
                tot = num;
                segs++;
            }
            else {
                tot += num;
            }
        }
        if (segs > m) {
            l = val + 1;
        }
        else {
            r = val;
        }
    }
    return l;
}
```
时间复杂度：$O(n \log (sum - max))$，空间复杂度：$O(1)$，sum 表示数组的和，max 表示数组的最大值


## 删除一次得到子数组最大和
给你一个整数数组，返回它的某个非空子数组（连续元素）在执行一次可选的删除操作后，所能得到的最大元素总和。换句话说，你可以从原数组中选出一个子数组，并可以决定要不要从中删除一个元素（只能删一次哦），（删除后）子数组中至少应当有一个元素，然后该子数组（剩下）的元素总和是所有子数组之中最大的。注意，删除一个元素后，子数组 不能为空。e.g. A = [1,-2,0,3]，最大子数组和是 4。

「分析」
设 dp[ i ][ k ] 是数组在第 i 处结尾时，恰好删掉了 k 个元素的最大子数组之和，这里 k 只能取 0 和 1。状态 dp[ i ][ 1 ] 的上游状态是 dp[i - 1][ 0 ] 和 dp[i - 1][ 1 ] + A[ i ]，表示 i - 1 时删了一个后，i 处的元素需要加上；反之则不加。状态 dp[ i ][ 0 ] 的上游状态是 dp[i - 1][ 0 ] + A[ i ] 和 A[ i ]。状态转移如下：

$$
\begin{aligned}
& dp[i][0] = \max(dp[i-1][0], 0) + A[i] \\
& dp[i][1] = \max(dp[i-1][0], dp[i-1][1] + A[i])
\end{aligned}
$$

这里可能有疑问，为什么 dp[ i ][ 1 ] 不可以从 i 处单独开始呢？因为 dp[ i ][ 1 ] 表示必须删除一个元素，而题目要求一个元素时不能删除。

```cpp
int maximumSum(vector<int>& arr) {
    if (arr.empty()) {
        return 0;
    }
    int dp0 = arr[0], dp1 = 0, ans = dp0;
    for (int i = 1; i < arr.size(); i++) {
        dp1 = max(dp0, dp1 + arr[i]);
        dp0 = max(dp0, 0) + arr[i];
        ans = max(max(dp0, dp1), ans);
    }
    return ans;
}
```
时间复杂度 $O(n)$，空间复杂度 $O(1)$
