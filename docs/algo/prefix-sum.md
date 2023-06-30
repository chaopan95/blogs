## 简介
前缀和，顾名思义，将一个序列的前 n 个元素累加起来。

## 和为K的子数组
给定一个数组 nums 和一个 k 值，求所有的连续子数组和等于 k 的个数

**「分析」**

暴力枚举的时间复杂度高达 $n^{3}$，利用前缀和的复杂度也会达到 $n^{2}$，我们在这里使用前缀和 + 哈希表进一步减小时间复杂度。

```cpp
int ans = 0, sum = 0;
unordered_map<int, int> cnt;
for (int num : nums) {
    sum += num;
    if (sum == k) { ans++; }
    if (cnt.count(sum - k)) {
        ans += cnt[sum - k];
    }
    cnt[sum]++;
}
return ans;
```
时间复杂度$O(n)$，空间复杂度$O(n)$，n 为数组的长度。

## 元素和为目标值的子矩阵数量
给定一个矩阵 matrix （$m \times n$） 和一个目标值 target，计算出所有的子矩阵和等于目标值的数目。

**「分析」**

题目要求是矩阵和，很容易联想到前缀和。但是如果在前缀和的基础上，暴力循环求解的话，时间复杂度为 $O(m^{2}n^{2})$，这样会超时。为了降低时间复杂度，我们使用哈希表的方法。具体地，一个矩形有四个边长，我们首先确定上边和下边，在依次遍历左边的过程中，用哈希表记录此时的子矩阵的和及出现的频率，如果后续的更大矩阵和与目标值的差值存在与哈希表中，那么可以将具有这些矩阵和的个数累加起来。

$$
\begin{matrix}
0 & 1 & 0 \\
1 & 1 & 1 \\
0 & 1 & 0
\end{matrix} \xrightarrow[]{前缀和}
\begin{matrix}
j \\
\downarrow \\
0 & 1 & 0 & \leftarrow & \text{top} \\
1 & 1 & 1 & \leftarrow & \text{bottom} \\
0 & 1 & 0
\end{matrix}
$$

```cpp
vector<vector<int>> sum(nRow + 1, vector<int>(nCol + 1, 0));
for (int i = nRow - 1; i >= 0; i--) {
    for (int j = nCol - 1; j >= 0; j--) {
        sum[i][j] = sum[i+1][j] + sum[i][j+1] -
                    sum[i+1][j+1] + matrix[i][j];
    }
}
for (int tot = nRow - 1; tot >= 0; tot--) {
    for (int bot = nRow - 1; bot >= tot; bot--) {
        unordered_map<int, int> cnt;
        for (int j = nCol - 1; j >= 0; j--) {
            int curSum = sum[tot][j] - sum[bot + 1][j];
            if (curSum == target) { ans++; }
            if (cnt.count(curSum - target)) {
                ans += cnt[curSum - target];
            }
            cnt[curSum]++;
        }
    }
}
return ans;
```
时间复杂度$O(m^{2}n)$，空间复杂度$O(mn)$，m 为矩阵的行数，n 为矩阵的列数

## 连续的子数组和
给定一个数组 nums 如 [23, 2, 4, 6, 7] 和一个整数 k 如 6，先判断这个数组是否存在一个连续子数组，其和是 k 的整数倍。这里要求子数组的长度至少为 2。

**「分析」**

前缀和对 k 的余数 + 哈希表

存在这样一个关系，如果两个数对同一个数 k 取余的值相同，那么这两个值的差值一定是这个 k 的整数倍。

```cpp
int n = (int)nums.size();
if (n < 2) { return false; }
unordered_map<int, int> dict;
int sum = 0;
dict[0] = -1;
for (int i = 0; i < n; i++) {
    sum += nums[i];
    int remainder = sum % k;
    if (dict.count(remainder)) {
        if (i - dict[remainder] >= 2) {
            return true;
        }
    }
    else {
        dict[remainder] = i;
    }
}
return false;
```

## 最长01等量子串
给定一个只包含0和1的字符串如"0010011001001010110"，求其最长子串，这个子串中0和1的个数相等，给出的例子有一个最大长度为 16 的 01 子串

**「分析」**

前缀和 + 哈希表

我们按照顺序遍历字符串，计算当前时刻的 01 个数差别，并将这差值保存在一个哈希表值，如果后续再次出现相同的差值，那么我们就可以快速的计算出这两个索引之间的距离，并且这两个索引之间的子串一定是 01 等长的。需要注意的是，不要遗忘空字符串。

```cpp
int getLongest01SubstrLen(string str) {
    int len = 0, n = (int)str.length();
    vector<int> dp(n+1, 0);
    unordered_map<int, int> dict;
    dict[0] = n;
    int pos = n;
    for (int i = n - 1; i >= 0; i--) {
        if (str[i] == '0') {
            dp[i] = dp[i+1] + 1;
        }
        else {
            dp[i] = dp[i+1] - 1;
        }
        if (dict.find(dp[i]) != dict.end()) {
            len = max(len, dict[dp[i]] - i);
            pos = i;
        }
        else {
            dict[dp[i]] = i;
        }
    }
    printf("maxLenSubstr = %s\n", str.substr(pos, len).c_str());
    return len;
}
```

## 统计等量连续 01 的连续子字符
给定一个字符 s = '00110011'，求 s 的所有连续子字符串中，‘0’ 和 ’1‘ 的数量相等并且 ‘0’ 和 ‘1’ 连续的个数。

**「分析」**

与统计 01 等量的连续子字符串不同，本题要求所有的子字符串中的 ‘0’ 和 ‘1’ 也要连续，即 ‘0101’ 虽然等量，但是所有的 ‘0’ 和所有的 ‘1’ 并非连续。一个解决办法是：将同类的字符分块统计后累加相邻两块内容。

```cpp
int countBinarySubstrings(string s) {
    int ans = 0, n = (int)s.length();
    int cnt = 0, last = 0;
    char lastChar = ' ';
    for (int i = 0; i < n; i++) {
        if (lastChar == s[i]) {
            cnt++;
        }
        else {
            ans += min(last, cnt);
            last = cnt;
            cnt = 1;
            lastChar = s[i];
        }
    }
    ans += min(last, cnt);
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$

## 航班预定统计
给定一个列表 bookings = [[1, 2, 10], [2, 3, 20], [2, 5, 25]] 表示从 i 到 j 的航班预定人数，另给一个整数 n 表示航班数（从 1 开始），求每一航班的预定人数之和。

$$
\begin{matrix}
\text{flight num} & 1 & 2 & 3 & 4 & 5 \\
\text{booking 1} & 10 & 10 \\
\text{booking 2} & & 20 & 20 \\
\text{booking 3} & & 25 & 25 & 25 & 25 \\
\text{total sum} & 10 & 55 & 45 & 25 & 25
\end{matrix}
$$

**「分析」**

【差分 + 前缀和】 一个数组的【差分】和【前缀和】互为逆运算，题目中给出了一个条预定信息 [i, j, booking]，那么在 [i, j] 针对这个区间求差分，只有第 i 个位置是 booking，其他位置是 0，但是为了避免无限制累加，在第 j+1 的位置需要【减去】booking。

```cpp
vector<int> corpFlightBookings(vector<vector<int>>& bookings, int n) {
    vector<int> ans(n, 0);
    for (auto booking : bookings) {
        ans[booking[0] - 1] += booking[2];
        if (booking[1] < n) {
            ans[booking[1]] -= booking[2];
        }
    }
    for (int i = 1; i < n; i++) {
        ans[i] += ans[i - 1];
    }
    return ans;
}
```
时间复杂度：$O(n + m)$，空间复杂度：$O(1)$，其中 n 是航班数，m 是预定数

## 分割数组的最多方案数
给你一个下标从 0 开始且长度为 n 的整数数组 nums 。分割 数组 nums 的方案数定义为符合以下两个条件的 pivot 数目：

1) 1 <= pivot < n

2) nums[0] + nums[1] + ... + nums[pivot - 1] == nums[pivot] + nums[pivot + 1] + ... + nums[n - 1] 同时给你一个整数 k 。你可以将 nums 中 一个 元素变为 k 或 不改变 数组。

请你返回在 至多 改变一个元素的前提下，最多 有多少种方法 分割 nums 使得上述两个条件都满足。

**「分析」**

【前缀和 + 左右哈希】假设一个数组的和为 tot，从某一个位置将数组切分，使得左右两部分的和相等，那么 tot 一定是一个偶数，并且这个位置的前缀和是 tot / 2，因此，我们用一个哈希表记录所有前缀和出现的次数。针对替换元素的情况，如果第 i 个元素被替换，那么数值的变化量是 d = k - nums[ i ]，此时，切分点 j 的位置有两种：1）在 i 的左侧，presum[ j ] + d = tot - presum[ j ]，得 presum[ j ] = (tot + d) / 2；2）j 在 i 的右侧（包含），presum[ j ] = tot + d - presum[ j ]，得 presum[ j ] = (tot - d) / 2。因此，我们枚举 nums 中的每一个 num，用两个哈希表，记录 num 左右两侧的前缀和的个数。

```cpp
int waysToPartition(vector<int>& nums, int k) {
    int ans = 0;
    long tot = 0, sum = 0;
    unordered_map<int, int> cnt_left, cnt_right;
    for (int num : nums) {
        tot += num;
        cnt_right[tot]++;
    }
    cnt_right[tot]--;
    if (tot % 2 == 0) {
        ans = cnt_right[tot / 2];
    }
    for (int i = 0; i < nums.size(); i++) {
        sum += nums[i];
        int d = k - nums[i];
        int left = 0, right = 0;
        if ((tot + d) % 2 == 0) {
            left = cnt_left[(tot + d) / 2];
        }
        if ((tot - d) % 2 == 0) {
            right = cnt_right[(tot - d) / 2];
        }
        ans = max(ans, left + right);
        cnt_left[sum]++;
        cnt_right[sum]--;
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$
