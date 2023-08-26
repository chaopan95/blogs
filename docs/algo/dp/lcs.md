## 子序列连续
给定两个序列（数组或字符串）A = [1, 2, 3, 2, 1]， B = [3, 2, 1, 4, 7]，那么这两者的最长公共部分是[3, 2, 1]，长度为3

设dp[i][j]为A[i: ]和B[j: ]两个数组的最长公共部分的长度，也就是状态，一共n*m个。因为要求子数组是连续的，所以当前状态只会与下一个（或上一个）状态相关，由此可以写出状态转移方程。

$$
\text{dp[ i ][ j ] = }
\begin{cases}
\text{dp[ i + 1 ][ j + 1] + 1}, &\quad \text{A[ i ] == B[ j ]} \\
0, & \quad \text{otherwise}
\end{cases}
$$

在状态转移过程，记录最大长度的大小以及最大长度的位置，最终可输出最长子数组。

### 最长公共子串
给定两个字符串str1和str2,输出两个字符串的最长公共子串，例如，"1AB2345CD","12345EF"的最长公共子串是"2345"。

```cpp
class Solution {
public:
    /**
    * longest common substring
    * @param str1 string字符串 the string
    * @param str2 string字符串 the string
    * @return string字符串
    */
    string LCS(string str1, string str2) {
        // write code here
        int n1 = (int)str1.length(), n2 = (int)str2.length();
        vector<vector<int>> dp(n1+1, vector<int> (n2+1, 0));
        int len = 1, pos = 0;
        for (int i = n1 - 1; i >= 0; i--) {
            for (int j = n2 - 1; j >= 0; j--) {
                if (str1[i] == str2[j]) {
                    dp[i][j] = dp[i+1][j+1] + 1;
                    if (len < dp[i][j]) {
                        len = dp[i][j];
                        pos = i;
                        
                    }
                }
            }
        }
        return str1.substr(pos, len);
    }
};
```

## 子序列不连续
当子序列不要求连续时，依然可以用dp[i][j]表示A[i: ]和B[j: ]两个数组的最长公共部分的长度，但是状态的转移方程发生改变

$$
\text{dp[ i ][ j ] = }
\begin{cases}
0, &\quad \text{i == len(A) or j == len(B)} \\
\text{dp[ i + 1 ][ j + 1] + 1}, &\quad \text{A[ i ] == B[ j ]} \\
\max(\text{dp[i + 1][ j ], dp[ i ][ j + 1 ]}), & \quad \text{otherwise}
\end{cases}
$$

如果要求输出公共子序列，则需要重新扫面一遍两个序列

```cpp
int m = (int)A.length(), n = (int)B.length(), i = 0, j = 0;
while (i < m && j < n) {
    if (A[i] == B[j]) {
        printf('%c', A[i]);
    }
    else if (dp[i + 1][j] > dp[i][j + 1]) { i++; }
    else { j++; }
}
```

### 最长公共子序列
给定两个字符串str1和str2，输出连个字符串的最长公共子序列。如过最长公共子序列为空，则输出-1。

```cpp
#include<iostream>
#include<string>
#include<vector>
using namespace std;

string LCS(string s1, string s2) {
    int n1 = (int)s1.length(), n2 = (int)s2.length();
    vector<vector<int>> dp(n1+1, vector<int>(n2+1, 0));
    string ans = "";
    for (int i = n1 - 1; i >= 0; i--) {
        for (int j = n2 - 1; j >= 0; j--) {
            if (s1[i] == s2[j]) { dp[i][j] = dp[i+1][j+1] + 1; }
            else { dp[i][j] = max(dp[i+1][j], dp[i][j+1]); }
        }
    }
    int i = 0, j = 0;
    while (i < n1 && j < n2) {
        if (s1[i] == s2[j]) {
            ans.push_back(s1[i]);
            i++;
            j++;
        }
        else if (dp[i+1][j] > dp[i][j+1]) {
            i++;
        }
        else { j++; }
    }
    return dp[0][0] ? ans : "-1";
}

int main(int argc, const char * argv[]) {
    // insert code here...
    string s1 = "1A2C3D4B56", s2 = "B1D23CA45B6A";
    cin >> s1 >> s2;
    printf("%s\n", LCS(s1, s2).c_str());
    return 0;
}
```

### 交错字符
给定三个字符串 s1 = “aabcc”， s2 = “dbbca”。s3 = “aadbbcbcac”，判断 s3 是否由 s1 和 s2 交错形成，即 s1 和 s2 是 s3 的子序列且无重叠。

**「分析」**

【动态规划】如果使用【双指针】求解本题的话，我们会得到错误结果，因为，【双指针】会贪心地找到最先遇到的相同字符。也就是说，本问题的子问题有重叠，所有使用【动态规划】。设 dp[ i ][ j ] 表示 s1 的前 i 个字符与 s2 的前 j 个字符是否形成 s3 的前 i + j 个字符。

$$
\begin{aligned}
\text{dp[ i ][ j ]} =& (\text{dp[i - 1][ j ] and s1[ i ] == s3[i + j]}) \\
& \text{or } (\text{dp[ i ][j - 1] and s2[ j ] == s3[i + j]})
\end{aligned}
$$

```cpp
bool isInterleave(string s1, string s2, string s3) {
    int n1 = (int)s1.length(), n2 = (int)s2.length(), n3 = (int)s3.length();
    if (n1 + n2 != n3) { return false; }
    bool **dp = new bool *[n1 + 1];
    for (int i = 0; i <= n1; i++) {
        dp[i] = new bool [n2 + 1]{};
    }
    dp[0][0] = true;
    for (int i = 0; i <= n1; i++) {
        for (int j = 0; j <= n2; j++) {
            if (i > 0) {
                dp[i][j] |= dp[i - 1][j] && s1[i - 1] == s3[i + j - 1];
            }
            if (j > 0) {
                dp[i][j] |= dp[i][j - 1] && s2[j - 1] == s3[i + j - 1];
            }
        }
    }
    bool ans = dp[n1][n2];
    for (int i = 0; i <= n1; i++) {
        delete []dp[i];
    }
    delete []dp;
    return ans;
}
```
时间复杂度：$O(n_{1} n_{2})$，空间复杂度：$O(n_{1} n_{2})$
