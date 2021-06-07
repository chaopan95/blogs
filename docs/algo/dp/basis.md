## 简介
动态规划（Dynamic programming，简称 DP）将原问题拆分成若干子问题（子问题有重叠），通过分别求解子问题，得到原问题解的一种算法。

## 要素
如果一个问题具有以下两个要素，则可以通过动态规划的方法求解

* `最优子结构（optimal structure）`
    - 原问题的解是可以由子问题得到的（递推或者递归），反之，不能称之为具有最优子结构

* `重叠子问题（overlap subproblem）`
    - 当某些子问题被重复遍历（或者原问题被分解成多个相同的子问题）时，这种情况称为重叠子问题

## 计算步骤
* `（1）`描述最优解的结构，即用一个状态表示最优解

* `（2）`以状态转移方程的形式，递归地定义最优解的值

* `（3）`自下而上（bottom-top）或自上而下（bottom-top）地计算最优解的值

* `（4）`根据（3）中的过程值，构造最优解；如果只需要最优解的值，则无需本步骤

## 动态规划的特点
* 以空间换时间：保存了计算过程的中间值，避免了重复计算

* 自上而下（bottom-top）或者递归是一种备忘录（memorization）方法

* 严格来说，动态规划只有自下而上的方式；如果所有的子问题都会被计算一次，那么动态规划具有优势，反之（剪枝情况），备忘录法具有优势

## 动态规划与贪心的比较
* `相同点：`具备最优子结构

* `不同点：`
    - 动态规划的子问题是重叠的，贪心的子问题不重叠
    - 动态规划不具有贪心选择的性质
    - 贪心的前进路线是一条线，动态规划是一个DAG

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

??? note "[「Leetcode 1186. 删除一次得到子数组最大和」](https://leetcode-cn.com/problems/maximum-subarray-sum-with-one-deletion/)"
    给你一个整数数组，返回它的某个非空子数组（连续元素）在执行一次可选的删除操作后，所能得到的最大元素总和。换句话说，你可以从原数组中选出一个子数组，并可以决定要不要从中删除一个元素（只能删一次哦），（删除后）子数组中至少应当有一个元素，然后该子数组（剩下）的元素总和是所有子数组之中最大的。注意，删除一个元素后，子数组 不能为空。

    请看示例：

    示例 1：
    
    输入：arr = [1,-2,0,3]
    
    输出：4
    
    解释：我们可以选出 [1, -2, 0, 3]，然后删掉 -2，这样得到 [1, 0, 3]，和最大。

    示例 2：
    
    输入：arr = [1,-2,-2,3]
    
    输出：3
    
    解释：我们直接选出 [3]，这就是最大和。

    示例 3：
    
    输入：arr = [-1,-1,-1,-1]
    
    输出：-1
    
    解释：最后得到的子数组不能为空，所以我们不能选择 [-1] 并从中删去 -1 来得到 0。
    
    我们应该直接选择 [-1]，或者选择 [-1, -1] 再从中删去一个 -1。

    ```cpp
    class Solution {
    public:
        int maximumSum(vector<int>& arr) {
            int n = (int)arr.size();
            if (n == 0) { return 0; }
            const int minEle = *min_element(arr.begin(), arr.end());
            int dp0 = arr[0], dp1 = minEle, ans = dp0;
            for (int i = 1; i < n; i++) {
                int tmp0 = max(dp0 + arr[i], arr[i]);
                int tmp1 = max(dp0, dp1 + arr[i]);
                dp0 = tmp0;
                dp1 = tmp1;
                ans = max(ans, max(dp0, dp1));
            }
            return ans;
        }
    };
    ```

## 序列型DP
??? note "[「Leetcode 740. 删除并获得点数」]()"

    ```cpp
    /*
    给你一个整数数组 nums ，你可以对它进行一些操作。

    每次操作中，选择任意一个 nums[i] ，删除它并获得 nums[i] 的点数。
    之后，你必须删除每个等于 nums[i] - 1 或 nums[i] + 1 的元素。

    开始你拥有 0 个点数。返回你能通过这些操作获得的最大点数。

    示例 1：
    输入：nums = [3,4,2]
    输出：6
    解释：
    删除 4 获得 4 个点数，因此 3 也被删除。
    之后，删除 2 获得 2 个点数。总共获得 6 个点数。

    示例 2：
    输入：nums = [2,2,3,3,3,4]
    输出：9
    解释：
    删除 3 获得 3 个点数，接着要删除两个 2 和 4 。
    之后，再次删除 3 获得 3 个点数，再次删除 3 获得 3 个点数。
    总共获得 9 个点数。
    */
    class Solution {
    public:
        int deleteAndEarn(vector<int>& nums) {
            if (nums.empty()) { return 0; }
            map<int, int> cnt;
            for (int num : nums) { cnt[num]++; }
            map<int, int>::iterator iter = ++cnt.begin();
            int lastN = cnt.begin()->first, lastC = cnt.begin()->second;
            int a = 0, b = lastN * lastC;
            for (; iter != cnt.end(); iter++) {
                int na = max(a, b), nb = iter->first * iter->second;
                if (lastN + 1 == iter->first) {
                    nb += a;
                }
                else {
                    nb += max(a, b);
                }
                a = na;
                b = nb;
                lastN = iter->first;
            }
            return max(a, b);
        }
    };
    ```

## 区间形DP
### 删除子串的最小操作数
给定一个字符串如“aaabbcbaa”，每一次可以删除一个由同一字符组成的子串，删除后剩余部分自动拼接。重复进行直到字符串为空，求最小的删除次数。举例如下：

$$
\begin{matrix}
& aaabb\underline{c}baa \\
& \downarrow \\
& aaa\underline{bbb}aa \\
& \downarrow \\
& \underline{aaaaa}
\end{matrix}
$$

**「分析」**

设 dp[ i ][ j ] 为字符串 s[i : j] 删除有相同字符组成的子串的最小次数。如果两端的字符相同，那么状态跳转到 s[i : j - 1]；如果两端字符串不想等，那么我们寻找一个最佳切分点 k ，使切分出来的两段和最小。特别地，对于每一个单字符，最小切割次数是 1 。

$$
\text{dp[ i ][ j ]} =
\begin{cases}
\text{dp[ i ][j - 1]}, & \quad \text{s[ i ] == s[ j ]} \\
\min_{k \in [i, j)}(\text{dp[ i ][ k ] + dp[k + 1][ j ]}), & \quad \text{otherwise}
\end{cases}
$$

```cpp
vector<vector<int>> dp(n, vector<int>(n, 0));
for (int i = n - 1; i >= 0; i--) {
    dp[i][i] = 1;
    for (int j = i + 1; j < n; j++) {
        if (s[i] == s[j]) {
            dp[i][j] = dp[i][j-1];
        }
        else {
            int _min = INT_MAX;
            for (int k = i; k < j; k++) {
                _min = min(_min, dp[i][k] + dp[k+1][j]);
            }
            dp[i][j] = _min;
        }
    }
}
return dp[0][n-1];
```
时间复杂度 $O(n^{3})$，空间复杂度 $O(n^{2})$

### 删除回文串的最小次数
给定一个字符串如“aabc”，每一次删除一个回文子串，直到字符串为空，求最小删除次数。

**「分析」**

设 dp[ i ][ j ] 为字符串 s[i : j] 的最小删除次数。任何一个字符都会面临下面的两种情况

$$
\begin{matrix}
\text{case 1: } & {\color{Red} a}xxxx \\
\text{case 2: } & {\color{Red} a}xx{\color{Red} a}xxx
\end{matrix}
$$

case 1 只删除一个字符，case 2 删除一个区间的字符

$$
\text{dp[ i ][ j ]} =
\begin{cases}
\text{1}, & \quad i == j \\
\max(\text{dp[i + 1][ j ] + 1, dp[i + 2][ j ] + 1}), & \quad \text{s[ i ] == s[i + 1]} \\
\max_{k \in [i + 2, j]} (\text{dp[i + 1][k - 1] + dp[k + 1][ j ]}), & \quad \text{s[ i ] == s[ k ]}
\end{cases}
$$

```cpp
vector<vector<int>> dp(n+1, vector<int>(n+1, 0));
dp[n - 1][n - 1] = 1;
for (int i = n - 2; i >= 0; i--) {
    dp[i][i] = 1;
    dp[i][i + 1] = 1 + (s[i] != s[i + 1]);
    for (int j = i + 2; j < n; j++) {
        dp[i][j] = 1 + dp[i+1][j];
        if (s[i] == s[i+1]) {
            dp[i][j] = min(dp[i][j], 1 + dp[i+2][j]);
        }
        for (int k = i + 2; k <= j; k++) {
            if (s[i] == s[k]) {
                dp[i][j] = min(dp[i][j], dp[i+1][k-1] + dp[k+1][j]);
            }
        }
    }
}
return dp[0][n-1];
```
时间复杂度 $O(n^{3})$，空间复杂度 $O(n^{2})$