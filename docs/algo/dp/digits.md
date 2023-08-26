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

## 不含连续1的非负整数
给定一个正整数 n ，请你统计在 [0, n] 范围的非负整数中，有多少个整数的二进制表示中不存在 连续的 1 。例如，n = 5，在 [0, 5] 的非负整数中，只有 3 的二进制表示含有连续的1，因此答案是 5。

「分析」
将整数 n 转换成二进制的字符串形式，之后套用模版。mark 表示前一个当前构造的（二进制）数字的最后一位。

```cpp
int dfs(string &s, vector<vector<int>> &M, int mark, int i, bool isLim, bool isNum) {
	if (i == s.length()) {
		return isNum;
	}
	if (!isLim && M[i][mark] != -1) {
		return M[i][mark];
	}
	int ans = 0;
	if (!isNum) {
		ans = dfs(s, M, 0, i + 1, false, false);
	}
	int up = isLim ? s[i] - '0' : 1;
	for (int d = 1 - isNum; d <= up; d++) {
		if (d == 1 && mark == 1) {
			continue;
		}
		ans += dfs(s, M, d, i + 1, isLim && (d == up), true);
	}
	if (!isLim && isNum) {
		M[i][mark] = ans;
	}
	return ans;
}
int findIntegers(int n) {
	string s = "";
	int tmp = n;
	while (tmp > 0) {
		int r = tmp % 2;
		s.push_back(r + '0');
		tmp >>= 1;
	}
	reverse(s.begin(), s.end());
	vector<vector<int>> M(s.size(), vector<int>(2, -1));
	return 1 + dfs(s, M, 0, 0, true, false);
}
```
时间复杂度 $O(m)$，空间复杂度 $O(m)$，m 是n转化为二进制字符串后的长度。

## 范围中美丽整数的数目
给你正整数 low ，high 和 k 。如果一个数满足以下两个条件，那么它是 美丽的 ：

1)偶数数位的数目与奇数数位的数目相同。
2)这个整数可以被 k 整除。

例如，low = 10, high = 20, k = 3，给定范围中有 2 个美丽数字：[12,18]
- 12 是美丽整数，因为它有 1 个奇数数位和 1 个偶数数位，而且可以被 k = 3 整除。
- 18 是美丽整数，因为它有 1 个奇数数位和 1 个偶数数位，而且可以被 k = 3 整除。

15 不是美丽整数，因为它的奇数数位和偶数数位的数目不相等。

请返回范围 [low, high] 中美丽整数的数目。

「分析」
M[i][mask][diff] 表示我们构造第 i 位数字，这个数字对 k 取余数位 mask，且数字各数位的奇偶差为 diff 时，美丽数的方案数目。

```cpp
int dfs(string &s, vector<vector<vector<int>>> &M, int mask, int diff, int i, int k, bool isLim, bool isNum) {
	if (i == s.length()) {
		return isNum && mask == 0 && diff == s.length();
	}
	if (!isLim && M[i][mask][diff] != -1) {
		return M[i][mask][diff];
	}
	int ans = 0;
	if (!isNum) {
		ans = dfs(s, M, mask, diff, i + 1, k, false, false);
	}
	int up = isLim ? s[i] - '0' : 9;
	for (int d = 1 - isNum; d <= up; d++) {
		ans += dfs(s, M, (mask * 10 + d) % k, diff + (d % 2) * 2 - 1, i + 1, k, isLim && (d == up), true);
	}
	if (!isLim && isNum) {
		M[i][mask][diff] = ans;
	}
	return ans;
}

int calc(int num, int k) {
	int ans = 0;
	string s = to_string(num);
	vector<vector<vector<int>>> M(s.length(), vector<vector<int>> (k, vector<int>(2 * s.length() + 1, -1)));
	ans = dfs(s, M, 0, (int)s.length(), 0, k, true, false);
	return ans;
}

int numberOfBeautifulIntegers(int low, int high, int k) {
	return calc(high, k) - calc(low - 1, k);
}
```
时间复杂度 $O(m^{2}kD)$，空间复杂度 $O(m^{2}k)$，m 是n转化为字符串后的长度，k是余数，D=10。

## 最大为 N 的数字组合
给定一个按 非递减顺序 排列的数字数组 digits 。你可以用任意次数 digits[i] 来写的数字。例如，如果 digits = ['1','3','5']，我们可以写数字，如 '13', '551', 和 '1351315'。返回 可以生成的小于或等于给定整数 n 的正整数的个数 。

「分析」
M[i] 表示我们构造第 i 位数字时，生成符合条件的数目。

```cpp
int dfs(string &s, vector<int> &digits, vector<int> &M, int mask, int i, bool isLim, bool isNum) {
	if (i == s.length()) {
		return isNum;
	}
	if (!isLim && M[i] != -1) {
		return M[i];
	}
	int ans = 0;
	if (!isNum) {
		ans += dfs(s, digits, M, mask, i + 1, false, false);
	}
	int up = isLim ? s[i] - '0' : 9;
	for (int d : digits) {
		if (d > up) {
			continue;
		}
		ans += dfs(s, digits, M, d, i + 1, isLim && d == up, true);
	}
	if (!isLim && isNum) {
		M[i] = ans;
	}
	return ans;
}

int atMostNGivenDigitSet(vector<string>& digits, int n) {
	string s = to_string(n);
	vector<int> nums;
	for (string d : digits) {
		nums.emplace_back(stoi(d));
	}
	vector<int> M(s.length(), -1);
	return dfs(s, nums, M, 0, 0, true, false);
}
```
时间复杂度 $O(mk)$，空间复杂度 $O(m)$，m 是n转换成字符串后的长度，k是digits的长度。
