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