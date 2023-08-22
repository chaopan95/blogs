## 基础
数位型动态规划通常针对位数较多的整数，一般是二进制、十进制。


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
    if (!isLim && M[i][mask] != -1) {
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


## 2出现的次数
给定一个自然数 n（n <= 10^9），计算从 0 到 n (含 n) 中数字 2 出现的次数。如 n = 25，返回9(2, 12, 20, 21, 22, 23, 24, 25)(注意 22 应该算作两次)。

「分析」
记忆化搜索，M[i][cnt] 表示长度为i时的数字含有cnt个2的数字个数。isLim 表示当前的数字是否受限，因为我们构造的数字不能大于n。通常记忆化DP将一个大问题，逐步拆分成小问题，并将小问题的结果保存下来，避免重复计算。这里从左到右构造数字，看上去不符合记忆化DP的过程，但是因为我们加入了isNum，优先遍历高位数字不填充的情况，所以这里相当于子问题。


```cpp
int dfs(vector<vector<int>> &M, string &s, int i, int cnt, bool isLim, bool isNum) {
	if (i == s.length()) {
		if (isNum) {
			return cnt;
		}
		return 0;
	}
	if (!isLim && M[i][cnt] != -1) {
		return M[i][cnt];
	}
	int ans = 0;
	if (!isNum) {
		ans = dfs(M, s, i + 1, cnt, false, isNum);
	}
	int up = isLim ? s[i] - '0' : 9;
	for (int d = 1 - isNum; d <= up; d++) {
		ans += dfs(M, s, i + 1, cnt + (d == 2), isLim && d == up, true);
	}
	M[i][cnt] = ans;
	return ans;
}
int numberOf2sInRange(int n) {
	string s = to_string(n);
	vector<vector<int>> M(s.length(), vector<int>(s.length(), -1));
	return dfs(M, s, 0, 0, true, false);
}
```
时间复杂度 $O(m^{3})$，空间复杂度 $O(m^{2})$，m 是n转化为字符串后的长度。
