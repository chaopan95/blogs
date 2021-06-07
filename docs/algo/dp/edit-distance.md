## 基础
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
