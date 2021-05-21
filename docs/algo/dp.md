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

在状态转移过程，记录最大长度的大小以及最大长度的位置，最终可输出最长子数组

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

**分析**

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

### 多次交易
对于一只股票的，允许多次交易（买入、卖出为一次交易），未卖出股票时不允许买入，买入和卖出不在同一天。求最大收益。

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

### 股票价格的跨度
给定一个股票价格的序列stocks = [100, 80, 60, 70, 60, 75, 85], 求其每一天的股票跨度（从i天开始向前计数，股票价格小于等于当前价格），输出结果[1, 1, 1, 2, 1, 4, 6]

**分析**

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


## 背包问题
??? note "[「01背包」]()"
    一个背包有一定的承重cap，有N件物品，每件都有自己的价值，记录在数组v中，也都有自己的重量，记录在数组w中，每件物品只能选择要装入背包还是不装入背包，要求在不超过背包承重的前提下，选出物品的总价值最大。给定物品的重量w价值v及物品数n和承重cap。请返回最大总价值。

    测试样例： [1,2,3],[1,2,3],3,6
    
    返回：6

    动态规划：dp[i][j]表示前i件物品在最大重量j的条件下的价值

    $$
    dp[i][j] =\max
    \begin{cases}
    dp[i-1]][j], &\quad \text{we don't put i-th item in our bag} \\
    dp[i-1][j - w[i-1]], &\quad \text{otherwise, but } j > w[i-1], i = 1, 2, ..., N
    \end{cases}
    $$

    ```cpp
    /* *
    * v[]: value for each item
    * w[]: weight for each item
    * W: capacity limit
    * N: number of item
    */
    int knapsack(int v[], int w[], int W, int N) {
        int **dp = new int *[N+1];
        for (int i = 0; i <= N; i++) { dp[i] = new int [W+1]{}; }
        for (int i = 1; i <= N; i++)
        {
            for (int j = 1; j <= W; j++) {
                if (j < w[i-1]) { dp[i][j] = dp[i-1][j]; }
                else {
                    dp[i][j] = max(dp[i-1][j], dp[i-1][j-w[i-1]] + v[i-1]);
                }
            }
        }
        int res = dp[N][W];
        for (int i = 0; i <= N; i++) { delete []dp[i]; }
        delete []dp;
        return res;
    }
    ```

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

## 正方形
??? note "[「Leetcode 221. 最大正方形」]()"

    ```cpp
    /*
    1 0 1 0 0
    1 0 1 1 1
    1 1 1 1 1
    1 0 0 1 0

    输出4
    */
    int maximalSquare(vector<vector<char>>& matrix) {
        int nRow = (int)matrix.size();
        if (nRow == 0) { return 0; }
        int nCol = (int)matrix[0].size();
        if (nCol == 0) { return 0; }
        vector<vector<int>> dp(nRow + 1, vector<int> (nCol + 1, 0));
        int ans = 0;
        for (int i = nRow - 1; i >= 0; i--) {
            for (int j = nCol - 1; j >= 0; j--) {
                if (matrix[i][j] == '1') {
                    dp[i][j] = min(dp[i+1][j],
                                min(dp[i][j+1], dp[i+1][j+1])) + 1;
                }
                ans = max(ans, dp[i][j] * dp[i][j]);
            }
        }
        return ans;
    }
    ```

??? note "[「Leetcode 1277. 统计全为 1 的正方形子矩阵」]()"

    ```cpp
    /*
    0 1 1 1
    1 1 1 1
    0 1 1 1

    输出15
    */
    int countSquares(vector<vector<int>>& matrix) {
        int nRow = (int)matrix.size();
        if (nRow == 0) { return 0; }
        int nCol = (int)matrix[0].size();
        if (nCol == 0) { return 0; }
        vector<vector<int>> dp(nRow + 1, vector<int>(nCol + 1, 0));
        int ans = 0;
        for (int i = nRow - 1; i >= 0; i--) {
            for (int j = nCol - 1; j >= 0; j--) {
                if (matrix[i][j]) {
                    dp[i][j] = min(dp[i+1][j],
                                min(dp[i][j+1], dp[i+1][j+1])) + 1;
                }
                ans += dp[i][j];
            }
        }
        return ans;
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

## 棋盘形DP
??? note "[「Leetcode 62. 不同路径」]()"

    $$
    \begin{aligned}
    & dp[i][1] = 1, \quad i = 1, 2, ..., m \\
    & dp[1][j] = 1, \quad j = 1, 2, ..., n \\
    & dp[i][j] = dp[i-1][j] + dp[i][j-1], \quad i = 2, 3, ..., m \quad j = 2, 3, ..., n
    \end{aligned}
    $$

    ```cpp
    /*
    一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为 “Start” ）。

    机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为 “Finish” ）。

    问总共有多少条不同的路径？

    Input: m = 3, n = 2
    Output: 3
    Explanation:
    From the top-left corner, there are a total of 3 ways to reach the
    bottom-right corner:
    1. Right -> Down -> Down
    2. Down -> Down -> Right
    3. Down -> Right -> Down
    */
    int uniquePaths(int m, int n) {
        int **dp = new int *[m];
        for (int i = 0; i < m; i++) { dp[i] = new int [n]{}; }
        for (int i = 0; i < m; i++) { dp[i][0] = 1; }
        for (int j = 0; j < n; j++) { dp[0][j] = 1; }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = dp[i-1][j] + dp[i][j-1];
            }
        }
        int ans = dp[m-1][n-1];
        for (int i = 0; i < m; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
    ```

??? note "[「Leetcode 63. 不同路径II」]()"

    ```cpp
    /*
    一个机器人位于一个 m x n 网格的左上角 （起始点在下图中标记为“Start” ）。

    机器人每次只能向下或者向右移动一步。机器人试图达到网格的右下角（在下图中标记为“Finish”）。

    现在考虑网格中有障碍物。那么从左上角到右下角将会有多少条不同的路径？

    Input: obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]
    Output: 2
    Explanation: There is one obstacle in the middle of the 3x3 grid above.
    There are two ways to reach the bottom-right corner:
    1. Right -> Right -> Down -> Down
    2. Down -> Down -> Right -> Right
    */
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        vector<vector<int>> mat = obstacleGrid;
        int m = int(mat.size());
        if (m == 0) { return 0; }
        int n = int(mat[0].size());
        if (n == 0) { return 0; }
        int **dp = new int *[m];
        for (int i = 0; i < m; i++) { dp[i] = new int [n]{}; }
        if (mat[0][0]) { return 0; }
        dp[0][0] = 1;
        for (int i = 1; i < m; i++) {
            if (mat[i][0]) { dp[i][0] = 0; }
            else { dp[i][0] = dp[i-1][0]; }
        }
        for (int j = 1; j < n; j++) {
            if (mat[0][j]) { dp[0][j] = 0; }
            else { dp[0][j] = dp[0][j-1]; }
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                if (mat[i][j]) { dp[i][j] = 0; }
                else { dp[i][j] = dp[i-1][j] + dp[i][j-1]; }
            }
        }
        int ans = dp[m-1][n-1];
        for (int i = 0; i < m; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
    ```

??? note "[「Leetcode 63. 最小路径和」]()"

    ```cpp
    /*
    给定一个包含非负整数的 m x n 网格 grid ，请找出一条从左上角
    到右下角的路径，使得路径上的数字总和为最小。
    说明：每次只能向下或者向右移动一步。

    1 3 1
    1 5 1
    4 2 1
    Input: grid = [[1,3,1],[1,5,1],[4,2,1]]
    Output: 7
    Explanation: Because the path 1 → 3 → 1 → 1 → 1 minimizes the sum.
    */
    int minPathSum(vector<vector<int>>& grid) {
        int m = int(grid.size());
        if (m == 0) { return 0; }
        int n = int(grid[0].size());
        if (n == 0) { return 0; }
        int **dp = new int *[m];
        for (int i = 0; i < m; i++) { dp[i] = new int [n]{}; }
        dp[0][0] = grid[0][0];
        for (int i = 1; i < m; i++) { dp[i][0] = dp[i-1][0] + grid[i][0]; }
        for (int j = 1; j < n; j++) { dp[0][j] = dp[0][j-1] + grid[0][j]; }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + grid[i][j];
            }
        }
        int ans = dp[m-1][n-1];
        for (int i = 0; i < m; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
    ```

??? note "[「程序员代码面试指南 17. 机器人达到指定位置方法数」]()"

    ```cpp
    /*
    题目描述
    假设有排成一行的N个位置，记为1~N，开始时机器人在M位置，机器人可以往左或者往右走，
    如果机器人在1位置，那么下一步机器人只能走到2位置，如果机器人在N位置，那么下一步机
    器人只能走到N-1位置。规定机器人只能走k步，最终能来到P位置的方法有多少种。由于方
    案数可能比较大，所以答案需要对1e9+7取模。
    输入描述:
    输出包括一行四个正整数N（2<=N<=5000）、M(1<=M<=N)、K(1<=K<=5000)、P(1<=P<=N)。
    输出描述:
    输出一个整数，代表最终走到P的方法数对10^9+7取模后的值。
    示例1
    输入
    复制
    5 2 3 3
    输出
    复制
    3
    说明
    1).2->1,1->2,2->3
    2).2->3,3->2,2->3
    3).2->3,3->4,4->3
    */
    #include<iostream>
    #include<vector>
    using namespace std;

    int moveRobot(int N, int M, int K, int P) {
        const int MOD = 1000000007;
        vector<vector<int>> dp(N+2, vector<int>(2, 0));
        dp[M][0] = 1;
        while (K--) {
            for (int i = 1; i <= N; i++) {
                dp[i][1] = (dp[i-1][0] + dp[i+1][0]) % MOD;
            }
            for (int i = 1; i <= N; i++) {
                dp[i][0] = dp[i][1];
            }
        }
        return dp[P][1];
    }


    int main(int argc, const char *argv[]) {
        int N = 5, M = 2, K = 3, P = 3;
        scanf("%d %d %d %d", &N, &M, &K, &P);
        printf("%d\n", moveRobot(N, M, K, P));
        return 0;
    }
    ```

??? note "[「Leetcode 1269. 停在原地的方案数」]()"

    ```cpp
    /*
    有一个长度为 arrLen 的数组，开始有一个指针在索引 0 处。
    每一步操作中，你可以将指针向左或向右移动 1 步，或者停在原地
    （指针不能被移动到数组范围外）。
    给你两个整数 steps 和 arrLen ，请你计算并返回：在恰好执行
    steps 次操作以后，指针仍然指向索引 0 处的方案数。
    由于答案可能会很大，请返回方案数 模 10^9 + 7 后的结果。

    示例 1：
    输入：steps = 3, arrLen = 2
    输出：4
    解释：3 步后，总共有 4 种不同的方法可以停在索引 0 处。
    向右，向左，不动
    不动，向右，向左
    向右，不动，向左
    不动，不动，不动

    示例  2
    输入：steps = 2, arrLen = 4
    输出：2
    解释：2 步后，总共有 2 种不同的方法可以停在索引 0 处。
    向右，向左
    不动，不动

    示例 3：
    输入：steps = 4, arrLen = 2
    输出：8
    */
    int numWays(int steps, int arrLen) {
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
    }
    ```
