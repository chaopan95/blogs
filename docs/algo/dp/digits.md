## 至少有 1 位重复的数字
给定正整数 n，返回在 [1, n] 范围内具有 至少 1 位 重复数字的正整数的个数。如，n = 20，具有至少 1 位重复数字的正数（<= 20）只有 11，应当返回 1。

「分析」
题目的要求是求至少有一个位置重复，可以转化为n - 无重复数字的个数。数据范围是 [1, 1e9]，用暴力方法易超时，考虑使用数位DP。设想一个整数a的位数大于10，因为每一位只能是0-9中的一个数填充，那么a一定是一个有重复的数字。这里的思路如下：将n转化成字符串s，从左到右遍历每一个位置i，同时枚举此位置上的所有可能数字（不超过s[i]），同时记录符合条件的方案。这里有一个注意的地方，我们应该覆盖1-n的所有可能，假设 n = 123，那么转化成字符串的长度就是3，我们可以从百位枚举，也可以从十位或者个位枚举，这样能保证[1, n]区间的所有值。

为了检查当前字符串的所有数字不重复，即10个数字无重复，我们使用$2^{10}$长度的整数至mask记录。例如，想知道「3」这个数字有被使用过了，只需要$\text{mask} \And (1 << 3)$是否位1即可。

```cpp
int dfs(string &s, vector<vector<int>> &M, int i, int mask, bool isLim, bool isNum) {
    if (i == s.length()) {
        return isNum;
    }
    if (!isLim && !isNum && M[i][mask] != -1) {
        return M[i][mask];
    }
    int ans = 0;
    if (!isNum) {
        ans = dfs(s, M, i + 1, mask, false, isNum);
    }
    int up = isLim ? s[i] - '0' : 9;
    for (int d = 1 - isNum; d <= up; d++) {
        if (mask >> d & 1) {
            continue;
        }
        ans += dfs(s, M, i + 1, mask | (1 << d), d == up && isLim, true);
    }
    M[i][mask] = ans;
    return ans;
}

int numDupDigitsAtMostN(int n) {
    string s = to_string(n);
    vector<vector<int>> M(s.length(), vector<int>(1 << 10, -1));
    return n - dfs(s, M, 0, 0, true, false);
}
```
时间复杂度 $O(mD2^{D})$，空间复杂度 $O(m2^{D})$，这里 m 是n转化为字符串的长度，D = 10，表示所有数字的集合大小
