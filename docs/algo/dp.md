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


## 编辑距离
莱文斯特距离（Levenshtein distance）有三种操作：增加、删除和替换

$$
\text{LD}_{a, b}(i, j) =
\begin{cases}
\max(i, j), & \min(i, j) = 0 \\
\min
\begin{cases}
\text{LD}_{a, b}(i-1, j) + 1 \\
\text{LD}_{a, b}(i, j-1) + 1 \\
\text{LD}_{a, b}(i-1, j-1) + 1_{a_{i} \neq b_{j}}
\end{cases}, & \text{otherwise}
\end{cases}
$$

a, b denote two strings to compare.

If $\text{LD}_{a, b}(i, j) = \text{LD}_{a, b}(i-1, j) + 1$, delete $a_{i}$

If $\text{LD}_{a, b}(i, j) = \text{LD}_{a, b}(i, j-1) + 1$, insert $b_{j}$ at $a_{i}$

If $\text{LD}_{a, b}(i, j) = \text{LD}_{a, b}(i-1, j-1) + 1_{a_{i} \neq b_{j}}$, replace $a_{i}$ with $b_{j}$

例如：word1 = "horse", word2 = "ros"，莱文斯特距离为3

解释：

horse -> rorse (将 'h' 替换为 'r')

rorse -> rose (删除 'r')

rose -> ros (删除 'e')

??? note "[「Levenshtein distance的代码」]()"

    ```cpp
    void EDprocess(string s1, string s2, int i, int j, int **dp, string str)
    {
        if (i == 0 && j == 0) { return; }
        else if (i == 0 || (j > 0 && dp[i][j] == dp[i][j-1] + 1))
        {
            string ss = str.substr(0, i);
            ss.push_back(s2[j-1]);
            str = ss + str.substr(i, str.length()-i);
            printf("insert %c: %s\n", s2[j-1], str.c_str());
            EDprocess(s1, s2, i, j-1, dp, str);
        }
        else if (j == 0 || (i > 0 && dp[i][j] == dp[i-1][j] + 1))
        {
            string ss = str.substr(0, i-1);
            str = ss + str.substr(i, str.length()-i);
            printf("delete %c: %s\n", s1[i-1], str.c_str());
            EDprocess(s1, s2, i-1, j, dp, str);
        }
        else
        {
            if (s1[i-1] != s2[j-1])
            {
                str[i-1] = s2[j-1];
                printf("replace %c with %c: %s\n", s1[i-1], s2[j-1], str.c_str());
            }
            EDprocess(s1, s2, i-1, j-1, dp, str);
        }
    }

    void printDP(string s1, string s2, int n1, int n2, int **dp)
    {
        for (int i = 0; i <= n1; i++)
        {
            for (int j = 0; j <= n2; j++)
            {
                printf("%d\t", dp[i][j]);
            }
            printf("\n");
        }
        printf("s1 = %s\n", s1.c_str());
        EDprocess(s1, s2, n1, n2, dp, s1);
        printf("s2 = %s\n", s2.c_str());
    }

    int LD(string s1, string s2)
    {
        int n1 = int(s1.length()), n2 = int(s2.length());
        int **dp = new int *[n1+1];
        for (int i = 0; i <= n1; i++) { dp[i] = new int [n2+1]{}; }
        for (int i = 0; i <= n1; i++)
        {
            for (int j = 0; j <= n2; j++)
            {
                if (i == 0 || j == 0) { dp[i][j] = i > j ? i : j; }
                else
                {
                    int a = dp[i-1][j] + 1;
                    int b = dp[i][j-1] + 1;
                    int c = dp[i-1][j-1] + (s1[i-1] != s2[j-1]);
                    dp[i][j] = min(min(a, b), c);
                }
            }
        }
        printDP(s1, s2, n1, n2, dp);
        int ans = dp[n1][n2];
        for (int i = 0; i <= n1; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
    /*
    0	1	2	3	4	5	6	
    1	0	1	2	3	4	5	
    2	1	0	1	2	3	4	
    3	2	1	1	1	2	3	
    4	3	2	1	2	2	3	
    5	4	3	2	2	3	2	
    6	5	4	3	3	2	3	
    s1 = ecoles
    insert e: ecolese
    replace e with o: ecolose
    delete o: eclose
    s2 =  eclose
    Edit distance = 3
    */
    ```

Damerau-Levenshtein distance

$$
\text{DLD}_{a, b}(i, j) =
\begin{cases}
\max(i, j), & \quad \text{if } \min(i, j) = 0 \\
\min
\begin{cases}
\text{DLD}_{a, b}(i-1, j) + 1 \\
\text{DLD}_{a, b}(i, j-1) + 1 \\
\text{DLD}_{a, b}(i-1, j-1) + 1_{a_{i} \neq b_{j}} \\
\text{DLD}_{a, b}(i-2, j-2) + 1
\end{cases}, & \quad \text{if } i, j > 1 \text{ and } a_{i} = b_{j-1} \text{ and } a_{i-1} = b_{j} \\
\min
\begin{cases}
\text{DLD}_{a, b}(i-1, j) + 1 \\
\text{DLD}_{a, b}(i, j-1) + 1 \\
\text{DLD}_{a, b}(i-1, j-1) + 1_{a_{i} \neq b_{j}}
\end{cases}, & \quad \text{otherwise}
\end{cases}
$$

**相似题目**
??? note "[「牛客题霸-算法篇 35. 最小编辑代价」](https://www.nowcoder.com/practice/05fed41805ae4394ab6607d0d745c8e4?tpId=196&tqId=37134&rp=1&ru=%2Fta%2Fjob-code-total&qru=%2Fta%2Fjob-code-total%2Fquestion-ranking&tab=answerKey)"

    ```cpp
    /*
    题目描述
    给定两个字符串str1和str2，再给定三个整数ic，dc和rc，分别代表插入、
    删除和替换一个字符的代价，请输出将str1编辑成str2的最小代价。
    示例1
    输入
    "abc","adc",5,3,2
    返回值
    2

    示例2
    输入
    "abc","adc",5,3,100
    返回值
    8
    */
    /**
    * min edit cost
    * @param str1 string字符串 the string
    * @param str2 string字符串 the string
    * @param ic int整型 insert cost
    * @param dc int整型 delete cost
    * @param rc int整型 replace cost
    * @return int整型
    */
    int minEditCost(string str1, string str2, int ic, int dc, int rc) {
        // write code here
        int n1 = (int)str1.length(), n2 = (int)str2.length();
        vector<vector<int>> dp(n1+1, vector<int>(n2+1, 0));
        for (int i = 0; i <= n1; i++) {
            for (int j = 0; j <= n2; j++) {
                if (i == 0) { dp[i][j] = ic * j; }
                else if (j == 0) { dp[i][j] = dc * i; }
                else {
                    int ist = dp[i][j-1] + ic;
                    int dlt = dp[i-1][j] + dc;
                    int rpl = dp[i-1][j-1] + rc * (str1[i-1] != str2[j-1]);
                    dp[i][j] = min(ist, min(dlt, rpl));
                }
            }
        }
        return dp[n1][n2];
    }
    ```

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

??? note "[「最长01等量子串」]()"
    给定一个只包含0和1的字符串如"0010011001001010110"，求其最长子串，这个子串中0和1的个数相等

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

## 最长公共子序列
### 子序列连续
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

**相似题目**
??? note "[「牛客题霸-算法篇 127. 最长公共子串」](https://www.nowcoder.com/practice/f33f5adc55f444baa0e0ca87ad8a6aac?tpId=196&tqId=37132&rp=1&ru=%2Fta%2Fjob-code-total&qru=%2Fta%2Fjob-code-total%2Fquestion-ranking&tab=answerKey)"

    ```cpp
    /*
    给定两个字符串str1和str2,输出两个字符串的最长公共子串
    题目保证str1和str2的最长公共子串存在且唯一。

    示例1
    输入
    "1AB2345CD","12345EF"
    返回值
    "2345"
    */
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

### 子序列不连续
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

??? note "[「牛客网 程序员代码面试指南 31. 最长公共子序列」](https://www.nowcoder.com/practice/4727c06b9ee9446cab2e859b4bb86bb8?tpId=101&tqId=33099&rp=1&ru=%2Fta%2Fprogrammer-code-interview-guide&qru=%2Fta%2Fprogrammer-code-interview-guide%2Fquestion-ranking&tab=answerKey)"

    ```cpp
    /*
    题目描述
    给定两个字符串str1和str2，输出连个字符串的最长公共子序列。如过最长公共子序列为空，则输出-1。
    输入描述:
    输出包括两行，第一行代表字符串str1，第二行代表str2。
    输出描述:
    输出一行，代表他们最长公共子序列。如果公共子序列的长度为空，则输出-1。
    示例1
    输入
    1A2C3D4B56
    B1D23CA45B6A
    输出
    123456
    说明
    "123456"和“12C4B6”都是最长公共子序列，任意输出一个。
    */
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

## 股票买卖
### 一次交易
给定一个数组prices，它的第i个元素 prices[i] 表示一支给定股票第 i 天的价格。你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。设计一个算法来计算你所能获取的最大利润。返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 0 。

输入：[7, 1, 5, 3, 6, 4] 输出：5

解释：在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。

**「分析」**

我们的目标是求最大利润 = 卖出时的价格 - 买入时的价格，那么针对每一天i，我们都可以得到一个这一天的最大利润

$$
\text{maxProfit[ i ] = stocks[ i ] - } \min_{j \in [0, i)} \text{stocks[ j ]}
$$

很容易可以写出一个$O(n^{2})$时间复杂度的方法。其实我们可以观察到，对于每一个i，我们重复求解i之前的最小股票价格，这一步是冗余的，因为如果我们知道了[0, i)的最小值x，那么[0, i]的最小值 a‘ = min(a, stocks[ i ])，从而避免了重复遍历。于是我们有一个高效的算法

```cpp
int minPrice = INT_MAX, maxProfit = INT_MIN;
for (int stock : stocks) {
    minPrice = min(minPrice, stock);
    maxProfit = max(maxProfit, stock - minPrice);
}
return maxProfit;
```
时间复杂度$O(n)$，空间复杂度$O(1)$

### 多次交易
对于一只股票的，允许多次交易（买入、卖出为一次交易），未卖出股票时不允许买入，买入和卖出不在同一天。求最大收益。

**「分析」**

对于某一天 i ，我们会有两种状态：持有股票和不持有股票，那么我们可以设 dp[ i ][ 0 ] 表示 i 天不持有股票的最大受益，dp[ i ][ 1 ] 表示 i 天持有股票的最大收益。

$$
\begin{aligned}
& \text{dp[ i ][ 0 ] = } \max(\text{dp[i - 1][ 0 ], dp[i - 1][ 1 ] + prices[ i ]}) \\
& \text{dp[ i ][ 1 ] = } \max(\text{dp[i - 1][ 0 ] - prices[ i ], dp[i - 1][ 1 ]})
\end{aligned}
$$

我们注意到，第 i 天的两种状态只与第 i-1 天的两种状态有关系，因此我们可以对时间复杂度进行优化。最后一天，不持有股票的收益一定高于持有股票的收益。

```cpp
int n = (int)prices.size();
int dp0 = 0, dp1 = -prices[0];
for (int i = 1; i < n; i++) {
    int tmp0 = max(dp0, dp1 + prices[i]);
    int tmp1 = max(dp0 - prices[i], dp1);
    dp0 = tmp0;
    dp1 = tmp1;
}
return dp0;
```
时间复杂度$O(n)$，空间复杂度$O(1)$

### 至多k次交易
对于一只股票，允许最多k次交易（买入和卖出为一次交易），求其最大的利润。

**「分析」**

因为题目要求持有股票时不能再买入，那么对于任意一天 i ，只有两种情况：这一天有股票和这一天没有股票。再根据交易的次数，设 dp[ i ][ j ][ 0 ] 为第 i 天处于第 j 次交易期间且这一天结束后未持有股票，dp[ i ][ j ][ 1 ] 为第 i 天处于第 j 次交易且这一天结束后持有股票。我们可以写出状态转移方程

$$
\begin{aligned}
& \text{dp[ i ][ j ][ 0 ] = } \max(\text{dp[i - 1][ j ][ 0 ], dp[i - 1][ j ][ 1 ] + prices[ i ]}) \\
& \text{dp[ i ][ j ][ 1 ] = } \max(\text{dp[i - 1][j - 1][ 0 ] - prices[ i ], dp[i - 1][ j ][ 1 ]})
\end{aligned}
$$

对于 dp[ i ][ j ][ 0 ] 这一天未持有股票，那么可能的状态来源是(1) i-1 这一天也未持有股票，或者(2) i-1这一天持有股票而在 i 这一天卖出，那么这一天持有的股票一定是第 j 次交易产生的。同样地，对于 dp[ i ][ j ][ 1 ] 这一天持有股票，可能的状态来源是(1) i-1 这天未持有而 i 这一天买入股票并且 i-1 属于第 j-1 次交易或者（1）i-1这一天持有股票，直接过渡而来。i = 0 表示的是第一天的股价，对于第 j = 1, 2, ..., k 次交易，如果在 i = 0 处发生卖出，dp[ 0 ][ j ][ 1 ] = -prices[ 0 ]。最终的利润和发生在最后一天不持有股票中的某一个 j 值，即

$$
\text{target} = \max_{j \in {1, 2, ..., k}} \text{dp[n - 1][ j ][ 0 ]}
$$

我们还可以发现，第 i 天的状态只与第 i-1天的状态有关系，因为在空间上可以优化。设 dp0[ j ] 表示第 j 次交易未持有股票的最大利润，dp1[ j ] 表示第 j 次交易持有股票的最大利润

$$
\begin{aligned}
& \text{dp0[ j ] = } \max(\text{dp0[ j ], dp1[ j ] + prices[ i ]}) \\
& \text{dp1[ j ] = } \max(\text{dp0[j - 1] - prices[ i ], dp1[ j ]})
\end{aligned}
$$

```cpp
int maxProfit(int k, vector<int>& prices) {
    int n = (int)prices.size();
    if (n == 0) { return 0; }
    vector<int> dp0(k+1, 0), dp1(k+1, -prices[0]);
    for (int i = 1; i < n; i++) {
        for (int j = 1; j <= k; j++) {
            dp0[j] = max(dp0[j], dp1[j] + prices[i]);
            dp1[j] = max(dp0[j-1] - prices[i], dp1[j]);
        }
    }
    return *max_element(dp0.begin(), dp0.end());
}
```
时间复杂度$O(nk)$，空间复杂度$O(k)$

### 带有冷冻期的交易
对于一只股票，允许多次交易（买入和卖出为一次交易），但是每一次交易完成后的下一天为冷冻期，不能购买股票，求其最大的利润。

**「分析」**

对于第 i 天结束，我们会出现三种状态：（1）这一天结束后准备进入未持有股票且不会处于冷冻期的状态，（2）这一天结束准备进入未持有股票但是处于冷冻期的状态，（3）这一天结束会进入持有股票的状态

dp[ i ][ 0 ] 表示状态（1），前一天 i-1 的状态可能是（1）和（2）；dp[ i ][ 1 ] 表示状态（2），前一天的状态一定是（3），并且在第 i 天卖出股票，因为只有这样才会进入冷冻期；dp[ i ][ 2 ] 表示状态（3），前一天的可能状态是（1）和（3），因为第 i 天结束是要进入持有股票的状态，那么这一天不可能是冷冻期。

$$
\begin{aligned}
& \text{dp[ i ][ 0 ] = } \max(\text{dp[i - 1][ 0 ], dp[i - 1][ 1 ]}) \\
& \text{dp[ i ][ 1 ] = dp[i - 1][ 2 ] + prices[ i ]} \\
& \text{dp[ i ][ 2 ] = } \max(dp[i - 1][ 0 ] - prices[ i ], dp[i - 1][ 2 ])
\end{aligned}
$$

我们观察到，第 i 天的状态只会与前一天的状态有关，因此，我们只需要保留前一天的状态即可。

$$
\begin{aligned}
& \text{dp0 } \leftarrow \max(\text{dp0, dp1}) \\
& \text{dp1 } \leftarrow \text{dp2 + prices[i]} \\
& \text{dp2 } \leftarrow \max(\text{dp0 - prices[ i ], dp2})
\end{aligned}
$$

```cpp
int n = (int)prices.size();
if (n == 0) { return 0; }
int dp0 = 0, dp1 = 0, dp2 = -prices[0];
for (int i = 1; i < n; i++) {
    dp2 = max(dp0 - prices[i], dp2);
    dp0 = max(dp0, dp1);
    dp1 = dp2 + prices[i];
}
return max(dp0, dp1);
```
时间复杂度$O(n)$，空间复杂度$O(1)$

### 股票价格的跨度
给定一个股票价格的序列stocks = [100, 80, 60, 70, 60, 75, 85], 求其每一天的股票跨度（从i天开始向前计数，股票价格小于等于当前价格），输出结果[1, 1, 1, 2, 1, 4, 6]

**「分析」**

按照题意，可以写出一个 $O(n^{2})$ 的算法，但是这样会超时。我们可以观察到，对于某一天的股票 stocks[ i ]，必然会逐个与i之前的元素比较，终于碰到一个比它大的或者没有，这时，我们将这个股票价格的跨度记录下来，对于下一个股价 stocks[i + 1]

$$
\text{count[ stocks[i + 1] ]} =
\begin{cases}
\text{count[ stocks[ i ] ]} + 1, & \quad \text{stocks[ i + 1 ]} \geq \text{stocks[ i ]} \\
1, & \quad \text{otherwise}
\end{cases}
$$

单调栈适合解决此类问题

```cpp
stack<int> stk, cnt;
vector<int> ans;
for (int s : stocks) {
    int num = 1;
    while (!stk.empty() && stk.top() <= s) {
        num += cnt.top();
        cnt.pop();
        stk.pop();
    }
    ans.emplace_back(num);
    cnt.push(num);
    stk.push(s);
}
return ans;
```
时间复杂度$O(n)$，空间复杂度$O(n)$

## 打家劫舍
### 成排
给定一个非负整数数组表示房屋内的金额，一个小偷可以偷取若干房屋内的所有金钱，但是偷窃的房屋不能相邻，求可以获得最大金额数目。

**「分析」**

设 dp[ i ][ 0 ] 表示第 i 个房屋没有被盗窃，dp[ i ][ 1 ] 为第 i 个房屋被盗窃

$$
\begin{aligned}
& \text{dp[ i ][ 0 ]} = \max(\text{dp[i - 1][ 0 ], dp[i - 1][ 1 ]}) \\
& \text{dp[ i ][ 1 ]} = \text{dp[i - 1][ 0 ] + A[ i ]}
\end{aligned}
$$

```cpp
int dp0 = 0, dp1 = nums[0];
for (int i = 1; i < n; i++) {
    int _dp0 = max(dp0, dp1);
    dp1 = dp0 + nums[i];
    dp0 = _dp0;
}
return max(dp0, dp1);
```
时间复杂度$O(n)$，空间复杂度$O(1)$

### 成环
同样一个非负数组表示房屋内的金额，但是数组的首尾是相连的，求此时的最大金额

**「分析」**

数组成环的状态下，首尾元素至少有一个不会被盗窃，那么我们可以将分成两种情况：（1）不选择首元素，（2）不选择尾元素。由此，将一个成环问题转换成两个成排问题，各自的解法如上。

```cpp
int dp0 = 0, dp1 = nums[0];
for (int i = 1; i < n - 1; i++) {
    int _dp0 = max(dp0, dp1);
    int _dp1 = dp0 + nums[i];
    dp0 = _dp0;
    dp1 = _dp1;
}
int ans = max(dp0, dp1);
dp0 = 0;
dp1 = nums[1];
for (int i = 2; i < n; i++) {
    int _dp0 = max(dp0, dp1);
    int _dp1 = dp0 + nums[i];
    dp0 = _dp0;
    dp1 = _dp1;
}
ans = max(ans, max(dp0, dp1));
return ans;
```
时间复杂度$O(n)$，空间复杂度$O(1)$

### 成树
这次的房屋排列成一颗二叉树，相邻的房屋不能同时被盗窃，求最大金额。

$$
\begin{matrix}
& & & & & 3 \\
& & & & & \wedge \\
& & & 4 & & & & & 5 \\
& & & \wedge & & & & & \wedge \\
& & 1 & & 3 & & & 2 & & 1
\end{matrix}
$$

最大金额是 10。

**「分析」**

构成一颗二叉树的情况下，当前节点只与左右孩子相关联（自底向上遍历），设 f[ root ] 为当前节点不盗窃， t[ root ] 为当前节点被盗窃，其状态转移方程如下

$$
\begin{aligned}
& \text{t[ root ] = } \max(\text{f[ root} \rightarrow \text{right ], f[ root} \rightarrow \text{left ]}) + \text{root} \rightarrow \text{val} \\
& 
\begin{aligned}
\text{f[ root ] = } & \max(\text{f[ root } \rightarrow \text{ left ], t[ root } \rightarrow \text{ left ]}) \\
& + \max(\text{f[ root } \rightarrow \text{ right ], t[ root } \rightarrow \text{ right ]})
\end{aligned}
\end{aligned}
$$

```cpp
unordered_map<TreeNode*, int> t, f;
int rob(TreeNode* root) {
    if (root == nullptr) { return 0; }
    rob(root->left);
    rob(root->right);
    t[root] = f[root->left] + f[root->right] + root->val;
    f[root] = max(f[root->left], t[root->left]) +
              max(f[root->right], t[root->right]);
    return max(t[root], f[root]);
}
```
时间复杂度$O(n)$，空间复杂度$O(n)$

## 背包问题
### 01背包
一个背包有一定的承重 W，有N件物品，每件都有自己的价值，记录在数组 v 中，也都有自己的重量，记录在数组 w 中，每件物品只能选择要装入背包还是不装入背包，要求在不超过背包承重的前提下，选出物品的总价值最大。例如，

$$
\begin{aligned}
& \text{v = [1, 2, 3]} \\
& \text{w = [1, 2, 4]} \\
& \text{W = 6}
\end{aligned}
$$

最大的价值为 5。

**「分析」**

设 dp[ i ][ j ] 为前 i 件物品在不超过 j 的重量下的最大价值。此时的状态只会有两个：（1）第 i 件物品没有被选中，（2）第 i 件物品被选中。状态转移方程如下：

$$
\text{dp[ i ][ j ]} = \max(\text{dp[i - 1]][ j ], dp[i - 1][j - w[ i ]]} + v[ i ])
$$

（1）如果第 i 件物品没有被选中，那么前 i - 1 件物品的重量就是 j；（2）如果第 i 件物品被选中了，那么前 i - 1 件物品的重量就是 j - v[ i ]，这里可以认为，第 i 件物品被加入后，总重量将会达到 j，那么加入之前的重量自然就等于 j - v[ i ]。当然，在计算的时候需要判定，当前的总重量 j 是否大于第 i 件物品的重量。

因为状态的转移只发生在相邻物品之间，所以空间复杂度得以优化。

```cpp
int knapsack01(vector<int> v, vector<int> w, int W) {
    int N = (int)v.size();
    vector<int> dp(W+1, 0);
    for (int i = 0; i < N; i++) {
        for (int j = W; j >= w[i]; j--) {
            dp[j] = max(dp[j], dp[j-w[i]] + v[i]);
        }
    }
    return dp[W];
}
```
时间复杂度$O(N \times W)$，空间复杂度$O(W)$，N 为物品的数目，W 为限定的重量

??? note "[「Leetcode 416. 分割等和子集」]()"

    ```cpp
    /*
    给你一个 只包含正整数 的 非空 数组 nums 。请你判断是否可以将这个数组分割
    成两个子集，使得两个子集的元素和相等。

    示例 1：
    输入：nums = [1,5,11,5]
    输出：true
    解释：数组可以分割成 [1, 5, 5] 和 [11] 。

    示例 2：
    输入：nums = [1,2,3,5]
    输出：false
    解释：数组不能分割成两个元素和相等的子集。
    */
    class Solution {
    public:
        bool canPartition(vector<int>& nums) {
            int n = (int)nums.size();
            if (n <= 1) { return false; }
            int sum = 0;
            for (int &num : nums) { sum += num; }
            if (sum % 2 == 1) { return false; }
            int target = sum / 2;
            bool *dp = new bool [target+1]{};
            for (int i = 0; i < n; i++) {
                for (int j = target; j >= nums[i]; j--) {
                    if (i == 0) { dp[j] = (j == nums[i]); }
                    else if (j == 0) { dp[j] = true; }
                    else {
                        dp[j] = dp[j] || dp[j-nums[i]];
                    }
                }
            }
            bool ans = dp[target];
            delete []dp;
            return ans;
        }
    };
    ```

## 子矩阵
### 最大正方形
给定一个由 0 和 1 构成的矩阵，求最大的全 1 子正方形。例如

$$
\begin{matrix}
1 & 0 & 1 & 0 & 0 \\
1 & 0 & {\color{Red} 1} & {\color{Red} 1} & 1 \\
1 & 1 & {\color{Red} 1} & {\color{Red} 1} & 1 \\
1 & 0 & 0 & 1 & 0
\end{matrix}
$$

最大面积为 4。

**「分析」**

设 dp[ i ][ j ] 为（i，j）到（nRow，nCol）的最大正方形边长，其状态转移矩阵如下：

$$
\text{dp[ i ][ j ]} =
\begin{cases}
\max(\text{dp[i + 1][ j ], dp[ i ][j + 1], dp[i + 1][j + 1]}) + 1, & \quad \text{grid[ i ][ j ] == 1} \\
0, & \quad \text{otherwise}
\end{cases}
$$

```cpp
vector<int> dp(nCol + 1, 0);
int ans = 0;
for (int i = nRow - 1; i >= 0; i--) {
    vector<int> tmp = dp;
    for (int j = nCol - 1; j >= 0; j--) {
        dp[j] = 0;
        if (matrix[i][j] == '1') {
            dp[j] = min(tmp[j], min(dp[j+1], tmp[j+1])) + 1;
        }
        ans = max(ans, dp[j] * dp[j]);
    }
}
return ans;
```
时间复杂度$O(m \times n)$，空间复杂度$O(n)$

### 统计全为 1 的正方形子矩阵
给定一个由 0 和 1 组成的矩阵，现在统计出所有只由 1 构成的子方阵的个数。

$$
\begin{matrix}
0 & 1 & 1 & 1 \\
1 & 1 & 1 & 1 \\
0 & 1 & 1 & 1
\end{matrix}
$$

矩阵中共有 15 个子方阵（10 个 $1 \times 1$，4 个 $2 \times 2$，1 个 $3 \times 3$）

**「分析」**

同上一题目，设 dp[ i ][ j ] 为（i，j）到（nRow，nCol）的最大正方形边长，用一个整型变量，在遍历过程中，记录每一个边长大于 1 的正方形，即统计全 1 正方形的个数。

```cpp
vector<int> dp(nCol + 1, 0);
int ans = 0;
for (int i = nRow - 1; i >= 0; i--) {
    vector<int> tmp = dp;
    for (int j = nCol - 1; j >= 0; j--) {
        dp[j] = 0;
        if (matrix[i][j]) {
            dp[j] = min(tmp[j], min(dp[j+1], tmp[j+1])) + 1;
        }
        ans += dp[j];
    }
}
return ans;
```
时间复杂度$O(m \times n)$，空间复杂度$O(n)$

### 最大边长全为 0 的正方形
给定一个由 0 和 1 组成的方阵，先求一个最大的子方阵，其四个边长全部由 0 构成，内部元素无要求。返回结果是一个数组，形如 {r, c, len}，其中 r 最小，如果 r 相等的情况下，取 c 最小的解。如下例子

$$
\begin{matrix}
{\color{Red} 0} & {\color{Red} 0} & {\color{Red} 0} & 1 \\
{\color{Red} 0} & 1 & {\color{Red} 0} & 1 \\
{\color{Red} 0} & {\color{Red} 0} & {\color{Red} 0} & 1 \\
1 & 0 & 0 & 0
\end{matrix}
$$

**「分析」**
设 dpRight[ i ][ j ] 为（i，j）向右连续为 0 的最大长度，dpDown[ i ][ j ] 为（i，j）向下连续为 0 的最大长度。其状态转移方程如下

$$
\begin{cases}
\text{dpRight[ i ][ j ] = dpDown[ i ][ j ] = 0}, &\text{matrix[ i ][ j ] == 1} \\
\begin{cases}
\text{dpRight[ i ][ j ] = dpRight[ i ][j + 1] + 1} \\
\text{dpDown[ i ][ j ] = dpDown[i + 1][ j ] + 1}
\end{cases}, & \text{matrix[ i ][ j ] == 0}
\end{cases}
$$

但是一个矩阵有四条边，所有需要考虑从（i，j）过渡到（i+len-1，j+len-1）。那么如何确定边长 len 呢？

$$
\text{len = } \min(\text{dpRight[ i ][ j ], dpDown[ i ][ j ]})
$$

进而确定了左下方和右上方的点 (i+len-1，j)、（i，j+len-1）。紧接着，从左下点向右 len - 1 个单位或者从右上点向下 len - 1 个单位都会到达（i+len-1，j+len-1），只需要确定是否存在这样的 len。

```cpp
vector<int> findSquare(vector<vector<int>>& matrix) {
    vector<int> ans {-1, -1, 0};
    int n = (int)matrix.size();
    if (n == 0) { return ans; }
    vector<vector<int>> dpRight(n+1, vector<int>(n+1, 0));
    vector<vector<int>> dpDown(n+1, vector<int>(n+1, 0));
    for (int i = n - 1; i >= 0; i--) {
        for (int j = n - 1; j >= 0; j--) {
            if (matrix[i][j] == 1) {
                dpRight[i][j] = dpDown[i][j] = 0;
            }
            else {
                dpRight[i][j] = dpRight[i][j + 1] + 1;
                dpDown[i][j] = dpDown[i + 1][j] + 1;
                int len = min(dpRight[i][j], dpDown[i][j]);
                while (len >= ans[2]) {
                    if (dpRight[i+len-1][j] >= len &&
                        dpDown[i][j+len-1] >= len) {
                        ans = {i, j, len};
                        break;
                    }
                    len--;
                }
            }
        }
    }
    return ans[0] == -1 && ans[1] == -1 ? vector<int> {} : ans;
}
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

## 路径问题
### 无障碍到达终点的路径条数
一个机器人在 $m \times n$ 的网格的左上角，每次只能向下或者向右移动一格，求到达终点的路径条数。

$$
\begin{matrix}
\text{start} & \cdot & \cdot & \cdot & \cdot & \cdot \\
\cdot & \cdot & \cdot & \cdot & \cdot & \cdot \\
\cdot & \cdot & \cdot & \cdot & \cdot & \cdot \\
\cdot & \cdot & \cdot & \cdot & \cdot & \text{end}
\end{matrix}
$$

**「分析」**

对于任意一个网格（i， j），能够到达这个网格只有两个位置即（i-1，j）和（i，j+1）。我们可以设 dp[ i ][ j ] 为从（0，0）到（i，j）的路径条数，写出状态转移方程。注意到，从左上角到右下角的路径条数与从右下角到左上角的路径条数是等价的。

$$
\begin{aligned}
& dp[i][1] = 1, \quad i = 1, 2, ..., m \\
& dp[1][j] = 1, \quad j = 1, 2, ..., n \\
& dp[i][j] = dp[i-1][j] + dp[i][j-1], \quad i = 2, 3, ..., m \quad j = 2, 3, ..., n
\end{aligned}
$$

我们可以发现，当前网格只与上方网格和左边网格有关，那么空间复杂度可以进一步优化。

```cpp
vector<int> next(n+1, 0), cur(n+1, 0);
next[n-1] = 1;
for (int i = m - 1; i >= 0; i--) {
    for (int j = n - 1; j >= 0; j--) {
        next[j] += cur[j] + next[j+1];
    }
    cur = next;
    next = vector<int>(n+1, 0);
}
return cur[0];
```
时间复杂度$O(m \times n)$，空间复杂度$O(n)$

### 有障碍到达终点的路径条数
一个机器人在 $m \times n$ 的网格的左上角，每次只能向下或者向右移动一格，但是某些网格有障碍，机器人不能到达这些障碍所在网格，求到达终点的路径条数。

$$
\begin{matrix}
0 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 0
\end{matrix}
$$

**「分析」**

同样地，设 dp[ i ][ j ] 为（0，0）到（i，j）之间地有效路径条数，其状态转移方程如下

$$
\text{dp[ i ][ j ]} =
\begin{cases}
0, & \quad \text{grid[ i ][ j ] == 1} \\
\text{dp[i + 1][ j ] + dp[ i ][j + 1]}, & \quad \text{otherwise}
\end{cases}
$$

```cpp
vector<int> dp(n, 0);
if (grid[0][0]) { return 0; }
dp[0] = 1;
for (int j = 1; j < n; j++) {
    dp[j] = dp[j-1] * (1 - grid[0][j]);
}
for (int i = 1; i < m; i++) {
    vector<int> pre = dp;
    dp[0] = pre[0] * (1 - grid[i][0]);
    for (int j = 1; j < n; j++) {
        dp[j] = (pre[j] + dp[j-1]) * (1 - grid[i][j]);
    }
}
return dp[n-1];
```
时间复杂度$O(m \times n)$，空间复杂度$O(n)$

### 最小路径和
一个包含非负整数的 $m \times n$ 的网格，找出一条从左上角到右下角的路径，使得这条路径上的数字和最小。

**「分析」**

设 dp[ i ][ j ] 为从（0，0）到（i，j）最小路径和，其状态转移方程如下

$$
\text{dp[ i ][ j ]} = \max(\text{dp[i - 1][ j ], dp[ i ][j - 1]}) + \text{grid[ i ][ j ]}
$$

```cpp
vector<int> dp(n+1, INT_MAX);
dp[n-1] = 0;
for (int i = m - 1; i >= 0; i--) {
    vector<int> tmp = dp;
    for (int j = n - 1; j >= 0; j--) {
        dp[j] = min(tmp[j], dp[j+1]) + grid[i][j];
    }
}
return dp[0];
```
时间复杂度$O(m \times n)$，空间复杂度$O(n)$

### 停在原地的方案数
有一个长度为 arrLen 的数组，开始有一个指针在索引 0 处。每一步操作中，你可以将指针向左或向右移动 1 步，或者停在原地（指针不能被移动到数组范围外）。求在恰好执行 steps 次操作以后，指针仍然指向索引 0 处的方案数。例如 steps = 3, arrLen = 2，那么 3 步后，总共有 4 种不同的方法可以停在索引 0 处。 分别是：

向右，向左，不动

不动，向右，向左

向右，不动，向左

不动，不动，不动

**「分析」**

设 dp[ k ][ i ] 为经过 k 步后跳到第 i 个位置时的方法数，状态转移如下

$$
\text{dp[ k ][ i ] = dp[k - 1][i - 1] + dp[k - 1][ i ] + dp[k - 1][i + 1]}
$$

可以观察到，状态转移只会发生在相邻两步和相邻的位置，那么可以使用两个一位数组代替二维数组

```cpp
const int MOD = 1000000007;
int n = min(arrLen, steps / 2 + 1);
vector<int> dp(n, 0);
dp[0] = 1;
while (steps--) {
    vector<int> tmp = dp;
    for (int i = 0; i < n; i++) {
        long a = i == 0 ? 0 : tmp[i - 1];
        long b = i == n - 1 ? 0 : tmp[i + 1];
        long c = tmp[i];
        dp[i] = (a + b + c) % MOD;
    }
}
return dp[0];
```
时间复杂度$O(k \times n)$，空间复杂度$O(n)$，k 为移动的步数，n 为数组的长度。

### 杨辉三角路径最小和
给定一个三角形，找出自顶向下的最小路径和，每一步只能移动到下一行，且与当前节点相邻的节点

$$
\begin{matrix}
& & & 1 \\
& & & \wedge \\
& & 3 & & 4 \\
& & \wedge & & \wedge \\
& 6 & & 5 & & 7 \\
& \wedge & & \wedge & & \wedge \\
4 & & 1 & & 8 & & 3
\end{matrix}
$$

**「分析」**

每一个内节点（非顶点），与上一层的两个节点相连，每一个边界节点（非顶点），与上一层的一个节点相连。设 dp[ i ][ j ] 表示从顶点到（i，j）点的最小路径和，状态转移方程如下

$$
\text{dp[ i ][ j ]} = \max(\text{dp[i + 1][ j ], dp[i + 1][j + 1]}), \quad i = 0, 1, \cdots, n - 2
$$

我们可以由上到下写动态规划的方程，也可以反过来递推。我们观察到当前层只与下一层的两个两个状态相关，因此只需要 $O(n)$ 的空间即可，但如果由下到上递推的话，我们可以借助传入矩阵，不必开辟额外的空间（本题较为特殊）。

```cpp
for (int i = n - 2; i >= 0; i--) {
    for (int j = 0; j <= i; j++) {
        triangle[i][j] = min(triangle[i+1][j],
                                triangle[i+1][j+1]) +
                            triangle[i][j];
    }
}
return triangle[0][0];
```
时间复杂度$O(n^{2})$，空间复杂度$O(1)$

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
时间复杂度$O(n^{3})$，空间复杂度$O(n^{2})$

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
时间复杂度$O(n^{3})$，空间复杂度$O(n^{2})$