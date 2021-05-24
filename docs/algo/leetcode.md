## 0001. Two Sum
Given an array of integers, return indices of the two numbers such that they add up to a specific target. You may assume that each input would have exactly one solution, and you may not use the same element twice.

```cpp
/*
Example
Given nums = [2, 7, 11, 15], target = 9,
Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
*/
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> res;
        int n = int(nums.size());
        if (n < 2) { return res; }
        unordered_map<int, int> M;
        unordered_map<int, int>::iterator iter;
        for (int i = 0; i < n; i++)
        {
            int diff = target - nums[i];
            iter = M.find(diff);
            if (iter != M.end())
            {
                res.push_back(i);
                res.push_back(iter->second);
                return res;
            }
            else { M[nums[i]] = i; }
        }
        return res;
    }
};
```

## 0002. Add Two Numbers
You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list. You may assume the two numbers do not contain any leading zero, except the number 0 itself.

```cpp
/*
Example:
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
*/
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode *pre = new ListNode(0), *cur = pre;
        int val = 0, pos = 0;
        while (l1 != nullptr && l2 != nullptr)
        {
            val = l1->val + l2->val + pos;
            pos = 0;
            if (val > 9)
            {
                val -= 10;
                pos = 1;
            }
            cur->next = new ListNode(val);
            cur = cur->next;
            l1 = l1->next;
            l2 = l2->next;
        }
        while (l1 != nullptr)
        {
            val = l1->val + pos;
            pos = 0;
            if (val > 9)
            {
                val -= 10;
                pos = 1;
            }
            cur->next = new ListNode(val);
            cur = cur->next;
            l1 = l1->next;
        }
        while (l2 != nullptr)
        {
            val = l2->val + pos;
            pos = 0;
            if (val > 9)
            {
                val -= 10;
                pos = 1;
            }
            cur->next = new ListNode(val);
            cur = cur->next;
            l2 = l2->next;
        }
        if (pos == 1) { cur->next = new ListNode(1); }
        ListNode *head = pre->next;
        delete pre;
        return head;
    }
};
```

## 0004. Median of Two Sorted Arrays*
There are two sorted arrays nums1 and nums2 of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)). You may assume nums1 and nums2 cannot be both empty.

Example:
nums1 = [1, 3]
nums2 = [2]
The median is 2.0

nums1 = [1, 2]
nums2 = [3, 4]
The median is (2 + 3)/2 = 2.5

Solution:
This problem requires a time complexity in O(log(m+n)), which determines that we cannot merge sort the twos arrays then get a median value because of o(m+n). A faisible solution is to find k-th value in two sorted arrays based on binary search.

$\bigstar$ Cut two sorted arrays
In fact, median is a statistique term, which means a value in middle position. If we want acquire a median value in one sorted array, it is easy to take the value in middlle position.

$$
\begin{matrix}
 & &  & & \text{cut} & &  & &  \\
 & &  & & \Downarrow & &  & &  \\
1 & & 2 & & 3 & & 4 & & 5 \\
 & &  & & \Uparrow & &  & &  \\
 & &  & & \text{cut} & &  & &
\end{matrix}
\quad \quad \quad \text{or} \quad \quad \quad
\begin{matrix}
 & &  & &  & \text{cut} &  & &  & & \\
 & &  & &  & \Downarrow &  & &  & & \\
1 & & 2 & & 3 & & 4 & & 5 & & 6\\
 & &  & &  & \Uparrow &  & &  & & \\
 & &  & &  & \text{cut} &  & &  & &
\end{matrix} \quad
$$

For example, the cutting line split an array into two parts with same size, l1 and r1 are left value and right value of cut postion.
Left example is odd array, l1 = r1 = 3, while right example is even array, l1 = 3, r1 = 4.

Similarly, we can generalize this to two arrays. If we can find such a cut for two arrays that the number of left cut and the number of right cut are equal, we can determine a median value for the 2 arrays. For example, we determine cut position k1, k2 for A, B respectively.

$$
\begin{matrix}
 & & & \text{cut} & & & \\
 & & & \Downarrow & & & \\
1 & & 3 & & 5 & & 7\\
\\
 & 2 & & 4 & & 6 \\
 & & & \Uparrow & & \\
 & & & \text{cut} & & &
\end{matrix}
\quad \quad
\begin{matrix}
l_{1} \text{ = 3, } r_{1} \text{ = 5} \\
\\
l_{2} \text{ = 4, } r_{2} \text{ = 4} \\
\end{matrix}
\Rightarrow
\text{Median} = \frac{\max(l_{1}, l_{2}) + \min(r_{1}, r_{2})}{2} = 4
$$

$\bigstar$ Unify odd and even number

According to traditional way, we have to treat case-by-case: it is different to calculate median value for odd array and even array.
In order to conquer this problem, we introuce a virtual placeholder. For example, an original array A is 1 2 3 4 5 and we insert a virtual placeholder # into A, which becomes A' = #1#2#3#4#5#. We see, lenghth of the original array is 5, while length of the new array is 5*2+1. If we cut A' at position 4(5th character from left). With this method, it's easy to get value in the original. Cut position c = 4

$$
\begin{aligned}
&
\begin{matrix}
 & & & & \text{cut} & & & & & \\
 & & & & \Downarrow & & & & & \\
\# & 1 & \# & 2 & \# & 3 & \# & 4 & \# & 5 & \# \\
 & & & & \Uparrow & & & & & \\
 & & & & \text{cut} & & & & &
\end{matrix} \\
& l_{1} = A[\frac{c-1}{2}] = A[1] = 2 \\
& r_{1} = A[\frac{c}{2}] = A[2] = 3
\end{aligned}
$$

If cut position for A' is in first # or last #, it will cause a overflow problem.

$$
\begin{matrix}
 & & & &  & & \text{cut} & & & & &  & \\
 & & & &  & & \Downarrow & & & & &  & \\
\# & 1 & \# & 2 & \#  & 3 & \# & & & & & & \\
\\
 & & & & & & \# & 4 & \# & 5 & \# & 6 & \# \\
 & & & &  & & \Uparrow & & & & &  & \\
 & & & &  & & \text{cut} & & & & &  &
\end{matrix}
\quad \quad
\begin{matrix}
l_{1} \text{ = 3, } r_{1} \text{ = 6} \\
\\
l_{2} \text{ = 1, } r_{2} \text{ = 4} \\
\end{matrix}
$$

As the example shows, if cut position at first #, left = min(A[0], B[0]), while cut position at last #, right = max(A[-1], B[-1]).

```cpp
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int n1 = int(nums1.size()), n2 = int(nums2.size());
        if (n1 == 0) { return (nums2[(n2-1)/2]+nums2[n2/2])/2.0; }
        if (n2 == 0) { return (nums1[(n1-1)/2]+nums1[n1/2])/2.0; }
        int MAX = nums1[n1-1] > nums2[n2-1] ? nums1[n1-1] : nums2[n2-1];
        int MIN = nums1[0] < nums2[0] ? nums1[0] : nums2[0];
        if (n1 < n2) { return findK(nums1, nums2, 0, 2*n1, n1, n2, MAX, MIN); }
        else { return findK(nums2, nums1, 0, 2*n2, n2, n1, MAX, MIN); }
    }
    double findK(vector<int> nums1, vector<int> nums2,
                 int b, int e, int n1, int n2,
                 int MAX, int MIN)
    {
        int l1 = 0, l2 = 0, r1 = 0, r2 = 0;
        int k1 = (b + e) / 2, k2 = (n1 + n2 - k1);
        if (k1 == 0) { l1 = MIN; r1 = nums1[0]; }
        else if (k1 >= 2*n1) { l1 = nums1[n1-1]; r1 = MAX; }
        else { l1 = nums1[(k1-1)/2]; r1 = nums1[k1/2]; }
        if (k2 == 0) { l2 = MIN; r2 = nums2[0]; }
        else if (k2 >= 2*n2) { l2 = nums2[n2-1]; r2 = MAX; }
        else { l2 = nums2[(k2-1)/2]; r2 = nums2[k2/2]; }
        if (l1 <= r2 && l2 <= r1)
        {
            return ((l1>l2?l1:l2)+(r1 < r2?r1:r2))/2.0;
        }
        else if (l1 > r2)
        {
            return findK(nums1, nums2, b, k1-1, n1, n2, MAX, MIN);
        }
        else { return findK(nums1, nums2, k1+1, e, n1, n2, MAX, MIN); }
    }
};
```

## 0005. Longest Palindromic Substring*
Given a string s, find the longest palindromic substring in s. You may assume that the maximum length of s is 1000.

Example 1:
Input: "babad"
Output: "bab"
Note: "aba" is also a valid answer.

Example 2:
Input: "cbbd"
Output: "bb"

Solution:
Dynamic programming:
We construct a table, such that table[i][j] represent a substring fron s[i] to s[j] is palindromic or not. Note that, each single character is palindromic, namely table[i][i] is true. We also assignment table[i][i+1] = true if two adjacent characters are same. Then we visit other substirngs to find the longest one.

```cpp
class Solution {
public:
    string longestPalindrome(string s) {
        int n = int(s.length()), pos = 0, len = 1;
        if (n < 2) { return s; }
        bool **dp = new bool *[n];
        for (int i = 0; i < n; i++)
        {
            dp[i] = new bool [n]{};
            dp[i][i] = true;
        }
        for (int i = 0; i < n-1; i++)
        {
            if (s[i] == s[i+1])
            {
                dp[i][i+1] = true;
                pos = i;
                len = 2;
            }
        }
        for (int k = 2; k < n; k++)
        {
            for (int i = 0; i < n-k; i++)
            {
                int j = i + k;
                if (s[i] == s[j] && dp[i+1][j-1])
                {
                    pos = i;
                    len = k + 1;
                    dp[i][j] = true;
                }
            }
        }
        for (int i = 0; i < n; i++) { delete []dp[i]; }
        delete []dp;
        return s.substr(pos, len);
    }
};
```

## 0006. ZigZag Conversion
The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)
P   A   H   N
A P L S I I G
Y   I   R

And then read line by line: "PAHNAPLSIIGYIR"

Write the code that will take a string and make this conversion given a number of rows:
string convert(string s, int numRows);

Example 1:
Input: s = "PAYPALISHIRING", numRows = 3
Output: "PAHNAPLSIIGYIR"

Example 2:
Input: s = "PAYPALISHIRING", numRows = 4
Output: "PINALSIGYAHRPI"
Explanation:
P     I    N
A   L S  I G
Y A   H R
P     I

Solution:

```cpp
class Solution {
public:
    string convert(string s, int numRows) {
        int n = int(s.length());
        if (n == 0 || numRows < 2) { return s; }
        string res = "";
        int nRow = numRows, num = 2*numRows - 2;
        int nCol = (n / num) * (num - nRow + 1);
        if (n % num < nRow) { nCol++; }
        else { nCol += (n % num) - nRow + 1; }
        char *mat = new char [nRow*nCol]{};
        for (int k = 0; k < n; k++)
        {
            int quo = k / num, mod = k % num;
            int i = -1, j = quo * (num - nRow + 1);
            if (mod < nRow) { i = mod; }
            else
            {
                i = nRow - 2 - (mod - nRow);
                j += mod - nRow + 1;
            }
            mat[i*nCol+j] = s[k];
        }
        for (int i = 0; i < nRow; i++)
        {
            for (int j = 0; j < nCol; j++)
            {
                if (mat[i*nCol+j]) { res.push_back(mat[i*nCol+j]); }
            }
        }
        delete []mat;
        return res;
    }
};
```

## 0007. 整数反转
```cpp
/*
给你一个 32 位的有符号整数 x ，返回将 x 中的数字部分反转后的结果。
如果反转后整数超过 32 位的有符号整数的范围 [−231,  231 − 1] ，就返回 0。
假设环境不允许存储 64 位整数（有符号或无符号）。

输入：x = 123
输出：321

输入：x = -123
输出：-321

输入：x = 120
输出：21

输入：x = 0
输出：0
*/
class Solution {
public:
    int reverse(int x) {
        int ans = 0;
        if (x == INT_MIN) { return 0; }
        while (x) {
            if (ans > INT_MAX / 10 || ans < INT_MIN / 10) {
                return 0;
            }
            ans = ans * 10 + (x % 10);
            x /= 10;
        }
        return ans;
    }
};
```

## 0008. String to Integer (atoi)
Implement atoi which converts a string to an integer. The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.

If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.

If no valid conversion could be performed, a zero value is returned.

Note:
Only the space character ' ' is considered as whitespace character.
Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [$-2^{31}$,  $2^{31}$−1]. If the numerical value is out of the range of representable values, INT_MAX ($2^{31}$ − 1) or INT_MIN (-$2^{31}$) is returned.

Example:
Input: "42"
Output: 42

Example 2:
Input: "   -42"
Output: -42
Explanation: The first non-whitespace character is '-', which is the minus sign. Then take as many numerical digits as possible, which gets 42.

Example 3:
Input: "4193 with words"
Output: 4193
Explanation: Conversion stops at digit '3' as the next character is not a numerical digit.

Example 4:
Input: "words and 987"
Output: 0
Explanation: The first non-whitespace character is 'w', which is not a numerical digit or a +/- sign. Therefore no valid conversion could be performed.

Example 5:
Input: "-91283472332"
Output: -2147483648
Explanation: The number "-91283472332" is out of the range of a 32-bit signed integer. Thefore INT_MIN (−$2^{31}$) is returned.

Solution:

```cpp
class Solution {
public:
    int myAtoi(string s) {
        int n = int(s.length());
        if (n == 0) { return 0; }
        bool isNeg = false;
        long res = 0;
        int pos = 0;
        while (s[pos] == ' ') { pos++; }
        if (s[pos] == '-') { isNeg = true; }
        else if (s[pos] == '+') { isNeg = false; }
        else if (s[pos] >= '0' && s[pos] <= '9') { res += s[pos] - '0'; }
        else { return 0; }
        for (++pos; pos < n; pos++)
        {
            if (s[pos] < '0' || s[pos] > '9') { break; }
            res = res * 10 + s[pos] - '0';
            if (res >= (1ll << 31))
            {
                if (isNeg) { return -(1ll << 31); }
                else { return (1ll << 31) - 1; }
            }
        }
        if (isNeg) { return -res; }
        return res;
    }
};
```

## 0009. Palindrome Number
Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.

Example 1:
Input: 121
Output: true

Example 2:
Input: -121
Output: false
Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.

Example 3:
Input: 10
Output: false
Explanation: Reads 01 from right to left. Therefore it is not a palindrome.

Follow up:
Coud you solve it without converting the integer to a string?

Solution:

```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0) { return false; }
        if (x < 10) { return true; }
        int count = 0, a = x;
        while (a)
        {
            count++;
            a /= 10;
        }
        count--;
        while (x)
        {
            int deno = int(pow(10, count));
            int left = x / deno, right = x % 10;
            if (left != right) { return false; }
            x = x % deno;
            x = x / 10;
            count -= 2;
        }
        return true;
    }
};
```

## 0010. Regular Expression Matching*
Given an input string (s) and a pattern (p), implement regular expression matching with support for '.' and '*'.

'.' Matches any single character.
'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

Note:
s could be empty and contains only lowercase letters a-z.
p could be empty and contains only lowercase letters a-z, and characters like . or *.

Example 1:
Input:
s = "aa"
p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".

Example 2:
Input:
s = "aa"
p = "a*"
Output: true
Explanation: '*' means zero or more of the preceding element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".

Example 3:
Input:
s = "ab"
p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".

Example: 4
Input:
s = "aab"
p = "c*a*b"
Output: true
Explanation: c can be repeated 0 times, a can be repeated 1 time. Therefore, it matches "aab".

Example 5:
Input:
s = "mississippi"
p = "mis*is*p*."
Output: false

Solution:
Consider two string s, p. i, j denote some position in s and p.

We use dynamic programming with a matrix dp $(ns+1) \times (np+1)$. ns and np are length of s and p. dp[i][j] denote if s[i:] and p[j:] match. The last entry dp[ns][np] is true. because '' and '' match.

Firstly, we check if s[i] and p[j] match ij_match is false or true.
Secondly, if p[j+1] is *, dp[i][j] has two independent sources: dp[i][j+2] and dp[i+1][j]. In detail, dp[i][j+2] represents we jump over * to check dp[i][j+2]. Similarly, dp[i+1][j] influences dp[i][j] but we have to consider ij_match. Both ij_match and dp[i+1][j] are true, dp[i][j] is true.
Otherwise, p[j+1] is not *, dp[i][j] is dependent of ij_match and dp[i+1][j+1].
Finally, return dp[0][0]

Remark: why we don't consider ij_match for dp[i][j+2]? Because we jump over *.

```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int ns = int(s.length()), np = int(p.length());
        bool **dp = new bool *[ns+1];
        for (int i = 0; i <= ns; i++) { dp[i] = new bool [np+1]{}; }
        dp[ns][np] = true;
        for (int i = ns; i >= 0; i--)
        {
            for (int j = np-1; j >= 0; j--)
            {
                bool ijMatch = i < ns && (s[i] == p[j] || p[j] == '.');
                if (j < np - 1 && p[j+1] == '*')
                {
                    dp[i][j] = dp[i][j+2] || (ijMatch && dp[i+1][j]);
                }
                else { dp[i][j] = ijMatch && dp[i+1][j+1]; }
            }
        }
        int res = dp[0][0];
        for (int i = 0; i <= ns; i++) { delete []dp[i]; }
        delete []dp;
        return res;
    }
};
```

## 0011. Container With Most Water*
Given n non-negative integers a1, a2, ..., an , where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water. Note: You may not slant the container and n is at least 2.

Example:
Input: [1,8,6,2,5,4,8,3,7]
Output: 49

Solution:
Brutal force cause Time Limit Exceeded. We use two points method.
1) Firstly, we set two extremities as initial values.
2) Then we move the shorter side towards the longer side one step, e.g. moving 1 towards 7.
3) We repeat 2) until two side are adjacent.
During this process, we calculate the max area.

```cpp
/*
 	|	 	 	 	 	|	 	|
 	|	|	 	 	 	|	 	|
 	|	|	 	|	 	|	 	|
 	|	|	 	|	|	|	 	|
 	|	|	 	|	|	|	|	|
 	|	|	|	|	|	|	|	|
|	|	|	|	|	|	|	|	|
The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7].
In this case, the max area of water (blue section) the container can
contain is 49.
*/

class Solution {
public:
    int maxArea(vector<int>& height) {
        int n = int(height.size());
        if (n < 2) { return 0; }
        int i = 0, j = n-1, res = 0;
        while (i < j)
        {
            int a = height[i], b = height[j];
            int B = a < b ? a : b, H = j - i;
            if (B * H > res) { res = B * H; }
            if (a < b) { i++; }
            else { j--; }
        }
        return res;
    }
};
```

## 0012. Integer to Roman
Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.
<table class="a">
  <tr><th>Symbol</th><th>Value</th></tr>
  <tr><td>I</td><td>1</td></tr>
  <tr><td>V</td><td>5</td></tr>
  <tr><td>X</td><td>10</td></tr>
  <tr><td>L</td><td>50</td></tr>
  <tr><td>C</td><td>100</td></tr>
  <tr><td>D</td><td>500</td></tr>
  <tr><td>M</td><td>1000</td></tr>
</table>

For example, two is written as II in Roman numeral, just two one's added together. Twelve is written as, XII, which is simply X + II. The number twenty seven is written as XXVII, which is XX + V + II.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not IIII. Instead, the number four is written as IV. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as IX. There are six instances where subtraction is used:

I can be placed before V (5) and X (10) to make 4 and 9.
X can be placed before L (50) and C (100) to make 40 and 90.
C can be placed before D (500) and M (1000) to make 400 and 900.
Given an integer, convert it to a roman numeral. Input is guaranteed to be within the range from 1 to 3999.

Example:
Input: 3
Output: "III"

Input: 4
Output: "IV"

Input: 9
Output: "IX"

Input: 58
Output: "LVIII"
Explanation: L = 50, V = 5, III = 3.

Input: 1994
Output: "MCMXCIV"
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.

Solution:

```cpp
class Solution {
public:
    string intToRoman(int num) {
        string res = "";
        int nums[4] = {1000, 100, 10, 1};
        char roman[4] = {'M', 'C', 'X', 'I'};
        for (int i = 0; i < 4; i++)
        {
            int quo = num / nums[i], mod = num % nums[i];
            num = mod;
            if (quo == 0) { continue; }
            if (quo == 9)
            {
                if (roman[i] == 'C') { res += "CM"; }
                if (roman[i] == 'X') { res += "XC"; }
                if (roman[i] == 'I') { res += "IX"; }
                continue;
            }
            if (quo == 4)
            {
                if (roman[i] == 'C') { res += "CD"; }
                if (roman[i] == 'X') { res += "XL"; }
                if (roman[i] == 'I') { res += "IV"; }
                continue;
            }
            if (quo >= 5)
            {
                quo -= 5;
                if (roman[i] == 'C') { res.push_back('D'); }
                if (roman[i] == 'X') { res.push_back('L'); }
                if (roman[i] == 'I') { res.push_back('V'); }
            }
            while (quo--) { res.push_back(roman[i]); }
        }
        return res;
    }
};
```

## 0013. Roman to Integer
Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.
<table class="a">
  <tr><th>Symbol</th><th>Value</th></tr>
  <tr><td>I</td><td>1</td></tr>
  <tr><td>V</td><td>5</td></tr>
  <tr><td>X</td><td>10</td></tr>
  <tr><td>L</td><td>50</td></tr>
  <tr><td>C</td><td>100</td></tr>
  <tr><td>D</td><td>500</td></tr>
  <tr><td>M</td><td>1000</td></tr>
</table>

For example, two is written as II in Roman numeral, just two one's added together. Twelve is written as, XII, which is simply X + II. The number twenty seven is written as XXVII, which is XX + V + II.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not IIII. Instead, the number four is written as IV. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as IX. There are six instances where subtraction is used:

I can be placed before V (5) and X (10) to make 4 and 9.
X can be placed before L (50) and C (100) to make 40 and 90.
C can be placed before D (500) and M (1000) to make 400 and 900.

Given a roman numeral, convert it to an integer. Input is guaranteed to be within the range from 1 to 3999.

Example 1:
Input: "III"
Output: 3

Example 2:
Input: "IV"
Output: 4

Example 3:
Input: "IX"
Output: 9

Example 4:
Input: "LVIII"
Output: 58
Explanation: L = 50, V= 5, III = 3.

Example 5:
Input: "MCMXCIV"
Output: 1994
Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.

Solution:

```cpp
class Solution {
public:
    int romanToInt(string s) {
        int res = 0, n = int(s.length());
        if (n == 0) { return res; }
        int nums[4] = {1000, 100, 10, 1};
        char roman[4] = {'M', 'C', 'X', 'I'};
        map<char, int> dictRomanToInt;
        dictRomanToInt['M'] = 1000;
        dictRomanToInt['D'] = 500;
        dictRomanToInt['C'] = 100;
        dictRomanToInt['L'] = 50;
        dictRomanToInt['X'] = 10;
        dictRomanToInt['V'] = 5;
        dictRomanToInt['I'] = 1;
        map<string, int> dictRomanpairToInt;
        map<string, int>::iterator iter;
        dictRomanpairToInt["IV"] = 4;
        dictRomanpairToInt["IX"] = 9;
        dictRomanpairToInt["XL"] = 40;
        dictRomanpairToInt["XC"] = 90;
        dictRomanpairToInt["CD"] = 400;
        dictRomanpairToInt["CM"] = 900;
        int pos = 0;
        while (pos < n)
        {
            iter = dictRomanpairToInt.find(s.substr(pos, 2));
            if (iter != dictRomanpairToInt.end())
            {
                res += iter->second;
                pos += 2;
                continue;
            }
            res += dictRomanToInt[s[pos]];
            pos++;
        }
        return res;
    }
};
```

## 0014. Longest Common Prefix
Write a function to find the longest common prefix string amongst an array of strings.

If there is no common prefix, return an empty string "".

Example 1:
Input: ["flower","flow","flight"]
Output: "fl"

Example 2:
Input: ["dog","racecar","car"]
Output: ""
Explanation: There is no common prefix among the input strings.

Note:
All given inputs are in lowercase letters a-z.

Solution:

```cpp
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        int n = int(strs.size());
        if (n == 0) { return ""; }
        string str = "";
        for (int j = 0; j < int(strs[0].length()); j++)
        {
            char flag = strs[0][j];
            for (int i = 1; i < n; i++)
            {
                if (strs[i][j] != flag) { return str; }
            }
            str.push_back(flag);
        }
        return str;
    }
};
```

## 0015. 3Sum*
Given an array nums of n integers, are there elements a, b, c in nums such that a + b + c = 0? Find all unique triplets in the array which gives the sum of zero. Note: The solution set must not contain duplicate triplets.

Solution:
$$
\begin{matrix}
-4 & -1 & -1 & 0 & 1 & 2 \\
\Uparrow & \Uparrow &  &  &  & \Uparrow \\
i & j &   &  &  & k
\end{matrix}
$$

1) sort the array
2) set 3 position variable i, j, k: i is from 0 to n-3, j is initialised as i+1 and k is n-1 initially.
3) compute sum of nums for i, j, k. if sum < 0, j++; if sum > 0, k--; if sum is 0, append this tuple into our resuslt array. Conitnue to move j rightward until no repetiton and continue to move k until no duplicate.
4) repeat 3) until i ends



## 0017. Letter Combinations of a Phone Number
Given a string containing digits from 2-9 inclusive, return all possible letter combinations that the number could represent.

A mapping of digit to letters (just like on the telephone buttons) is given below. Note that 1 does not map to any letters.
$$
\begin{bmatrix}
\text{1: } \infty & \text{2: abc} & \text{3: def} \\
4: ghi & 5: jkl & 6: mno \\
7: pqrs & 8: tuv & 9: wxyz
\end{bmatrix}
$$

Example:
Input: "23"
Output: ["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].

Note: Although the above answer is in lexicographical order, your answer could be in any order you want.

Solution:

```cpp
class Solution {
public:
    vector<string> letterCombinations(string digits) {
        vector<string> res;
        int n = int(digits.length());
        if (n == 0) { return res; }
        string *arr = new string [10]{};
        for (int i = 2; i <= 6; i++)
        {
            string str = "";
            for (int j = 0; j < 3; j++) { str.push_back('a' + (i-2)*3+j); }
            arr[i] = str;
        }
        arr[7] = "pqrs";
        arr[8] = "tuv";
        arr[9] = "wxyz";
        string str = digits;
        combine(str, res, 0, n, arr, digits);
        delete []arr;
        return res;
    }
    void combine(string &str, vector<string> &res, int idx, int n,
                 string *arr, string digits)
    {
        if (idx == n) { res.push_back(str); }
        else
        {
            for (int i = 0; i < int(arr[digits[idx]-'0'].length()); i++)
            {
                str[idx] = arr[digits[idx]-'0'][i];
                combine(str, res, idx+1, n, arr, digits);
            }
        }
    }
};
```

## 0018. 4Sum
Given an array nums of n integers and an integer target, are there elements a, b, c, and d in nums such that a + b + c + d = target? Find all unique quadruplets in the array which gives the sum of target. Note: The solution set must not contain duplicate quadruplets.

```cpp
/*
Given array nums = [1, 0, -1, 0, -2, 2], and target = 0.
A solution set is:
[[-1,  0, 0, 1], [-2, -1, 1, 2], [-2,  0, 0, 2]]
*/
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        int n = int(nums.size());
        vector<vector<int>> res;
        if (n < 4) { return res; }
        sort(nums.begin(), nums.end());
        for (int i = 0; i < n-3; i++)
        {
            for (int j = i+1; j < n-2; j++)
            {
                int b = j + 1, e = n-1;
                while (b < e)
                {
                    int sum = nums[i] + nums[j] + nums[b] + nums[e];
                    if (sum < target) { b++; }
                    else if (sum > target) { e--; }
                    else
                    {
                        while (i < n-3 && nums[i] == nums[i+1]) { i++; }
                        while (j < n-2 && nums[j] == nums[j+1]) { j++; }
                        while (b < e && nums[b] == nums[b+1]) { b++; }
                        while (b < e && nums[e] == nums[e-1]) { e--; }
                        vector<int> arr;
                        arr.push_back(nums[i]);
                        arr.push_back(nums[j]);
                        arr.push_back(nums[b]);
                        arr.push_back(nums[e]);
                        res.push_back(arr);
                        b++;
                        e--;
                    }
                }
            }
        }
        return res;
    }
};
```

## 0019. Remove Nth Node From End of List*
```cpp
/*
Given linked list: 1->2->3->4->5, and n = 2.
After removing the second node from the end, the linked
list becomes 1->2->3->5.
*/
// Two pointers
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        if (n < 1 || head == nullptr) { return head; }
        ListNode *pre = new ListNode(0, head);
        ListNode *p1 = pre, *p2 = head, *p3 = head;
        while (n--)
        {
            if (p3 == nullptr) { return head; }
            p3 = p3->next;
        }
        while (p3 != nullptr)
        {
            p3 = p3->next;
            p2 = p2->next;
            p1 = p1->next;
        }
        p1->next = p2->next;
        delete p2;
        head = pre->next;
        delete pre;
        return head;
    }
};
```

## 0020. Valid Parentheses
```cpp
/*
Input: s = "()"
Output: true

Input: s = "()[]{}"
Output: true

Input: s = "(]"
Output: false

Input: s = "([)]"
Output: false

Input: s = "{[]}"
Output: true
*/
class Solution {
public:
    bool isValid(string s) {
        int n = int(s.length());
        stack<char> st;
        for (int i = 0; i < n; i++)
        {
            if (s[i] == '[' || s[i] == '{' || s[i] == '(')
            {
                st.push(s[i]);
            }
            else if ((s[i] == ')' && !st.empty() && st.top() == '(') ||
                     (s[i] == ']' && !st.empty() && st.top() == '[') ||
                     (s[i] == '}' && !st.empty() && st.top() == '{'))
            {
                st.pop();
            }
            else { return false; }
        }
        return st.empty();
    }
};
```

## 0021. Merge Two Sorted Lists
```cpp
/*
Input: 1->2->4, 1->3->4
Output: 1->1->2->3->4->4
*/
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        if (l1 == nullptr) { return l2; }
        if (l2 == nullptr) { return l1; }
        ListNode *head = new ListNode(0);
        ListNode *curNode = head;
        while (l1 != nullptr && l2 != nullptr)
        {
            if (l1->val < l2->val)
            {
                curNode->next = l1;
                l1 = l1->next;
            }
            else
            {
                curNode->next = l2;
                l2 = l2->next;
            }
            curNode = curNode->next;
        }
        if (l1 != nullptr) { curNode->next = l1; }
        if (l2 != nullptr) { curNode->next = l2; }
        curNode = head->next;
        delete head;
        return curNode;
    }
};
```

## 0022. Generate Parentheses
```cpp
/*
For example, given n = 3, a solution set is:
[
"((()))",
"(()())",
"(())()",
"()(())",
"()()()"
]
*/
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> res;
        if (n == 0) { return res; }
        string str = "";
        append(res, str, n, 0, 0, 0);
        return res;
    }
    void append(vector<string> &res, string str, int n,
                int idx, int nLeft, int nRight)
    {
        if (idx == 2*n)
        {
            if (nRight == nLeft) { res.push_back(str); }
            return;
        }
        if (nLeft >= nRight && nLeft < n)
        {
            append(res, str+'(', n, idx+1, nLeft+1, nRight);
        }
        append(res, str+')', n, idx+1, nLeft, nRight+1);
    }
};
```

## 0023. Merge k Sorted Lists
```cpp
/*
Input: lists = [[1,4,5],[1,3,4],[2,6]]
Output: [1,1,2,3,4,4,5,6]
*/
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        int n = int(lists.size());
        if (n == 0) { return nullptr; }
        ListNode *pre = new ListNode(0), *head = pre, *cur = pre;
        while (true)
        {
            int idx = -1, min = (1ll << 31) - 1;
            for (int i = 0; i < n; i++)
            {
                if (lists[i] == nullptr) { continue; }
                if (lists[i]->val < min)
                {
                    idx = i;
                    min = lists[i]->val;
                }
            }
            if (idx == -1) { break; }
            cur->next = lists[idx];
            lists[idx] = lists[idx]->next;
            cur = cur->next;
        }
        head = pre->next;
        delete pre;
        return head;
    }
};
```

## 0024. Swap Nodes in Pairs
```cpp
/*
Given 1->2->3->4, you should return the list as 2->1->4->3.
*/
class Solution {
public:
    ListNode* swapPairs(ListNode* head) {
        if (head == nullptr || head->next == nullptr) { return head; }
        ListNode *pre = new ListNode(0);
        ListNode *par = head, *son = head->next, *gra = pre;
        pre->next = head;
        while (son != nullptr)
        {
            par->next = son->next;
            son->next = par;
            gra->next = son;
            gra = par;
            par = par->next;
            if (par == nullptr) { break; }
            son = par->next;
        }
        head = pre->next;
        delete pre;
        return head;
    }
};
```

## 0025. Reverse Nodes in k-Group
```cpp
/*
Given this linked list: 1->2->3->4->5
For k = 2, you should return: 2->1->4->3->5
For k = 3, you should return: 3->2->1->4->5
*/
class Solution {
public:
    ListNode* reverseKGroup(ListNode* head, int k) {
        if (head == nullptr || head->next == nullptr ||
            k < 2) { return head; }
        ListNode *front = head;
        for (int i = 0; i < k; i++)
        {
            if (front == nullptr) { return head; }
            front = front->next;
        }
        bool isEnd = false;
        ListNode *pre = new ListNode(0, head);
        ListNode *gra = pre, *par = head, *son = head->next;
        while (true)
        {
            ListNode *temp = front, *p1 = gra, *p2 = par, *p3 = son;
            while (p2 != temp)
            {
                if (front == nullptr) { isEnd = true; }
                else { front = front->next; }
                p2->next = p1;
                p1 = p2;
                p2 = p3;
                if (p3 == nullptr) { break; }
                p3 = p3->next;
            }
            par->next = p2;
            gra->next = p1;
            gra = par;
            par = p2;
            son = p3;
            if (isEnd || p3 == nullptr) { break; }
        }
        head = pre->next;
        delete pre;
        return head;
    }
};
```

## 0026. Remove Duplicates from Sorted Array
```cpp
/*
Given nums = [0,0,1,1,1,2,2,3,3,4]
Your function should return length = 5
*/
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int n = int(nums.size());
        if (n < 2) { return n; }
        int len = 1;
        for (int i = 1; i < n; i++)
        {
            if (nums[i] == nums[i-1]) { continue; }
            nums[len++] = nums[i];
        }
        return len;
    }
};
```

## 0027. Remove Element
```cpp
/*
Given nums = [0,1,2,2,3,0,4,2], val = 2,
Your function should return length = 5, 
*/
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int n = int(nums.size()), len = 0;
        if (n == 0) { return 0; }
        for (int i = 0; i < n; i++)
        {
            if (nums[i] != val) { nums[len++] = nums[i]; }
        }
        return len;
    }
};
```

## 0028. Implement strStr()*
```cpp
/*
Input: haystack = "hello", needle = "ll"
Output: 2
*/
class Solution {
public:
    int strStr(string haystack, string needle) {
        string T = haystack, P = needle;
        int nT = int(T.length()), nP = int(P.length());
        if (nP == 0) { return 0; }
        if (nT == 0) { return -1; }
        int *next = new int [nP]{};
        int i = 0, j = 0, t = next[0] = -1, k = 0;
        while (k < nP-1)
        {
            if (t < 0 || P[t] == P[k]) { next[++k] = ++t; }
            else { t = next[t]; }
        }
        while (i < nT && j < nP)
        {
            if (j < 0 || T[i] == P[j]) { i++; j++; }
            else { j = next[j]; }
        }
        delete []next;
        if (i-j <= nT-nP) { return i - j; }
        else { return -1; }
    }
};
```

## 0029. Divide Two Integers*
Given two integers dividend and divisor, divide two integers without using multiplication, division and mod operator.
Return the quotient after dividing dividend by divisor.
The integer division should truncate toward zero, which means losing its fractional part. For example, truncate(8.345) = 8 and truncate(-2.7335) = -2.

Example 1:
Input: dividend = 10, divisor = 3
Output: 3
Explanation: 10/3 = truncate(3.33333..) = 3.
Example 2:
Input: dividend = 7, divisor = -3
Output: -2
Explanation: 7/-3 = truncate(-2.33333..) = -2.

Solution:

```cpp
class Solution {
public:
    int divide(int dividend, int divisor) {
        int MAX = (1ll << 31) - 1;
        long m = abs(long(dividend)), n = abs(long(divisor)), quo = 0;
        while (m >= n)
        {
            long s = n, incre = 1;
            while ((s << 1) <= m) { s <<= 1; incre <<= 1; }
            quo += incre;
            m -= s;
        }
        if ((dividend < 0) ^ (divisor < 0)) { quo *= -1; }
        return quo > MAX ? MAX : int(quo);
    }
};
```

## 0030. Substring with Concatenation of All Words*
You are given a string s and an array of strings words of the same length. Return all starting indices of substring(s) in s that is a concatenation of each word in words exactly once, in any order, and without any intervening characters.
You can return the answer in any order.

Example:
Input: s = "barfoothefoobarman", words = ["foo","bar"]
Output: [0,9]
Explanation: Substrings starting at index 0 and 9 are "barfoo" and "foobar" respectively.
The output order does not matter, returning [9,0] is fine too.

Input: s = "wordgoodgoodgoodbestword", words = ["word","good","best","word"]
Output: []

Input: s = "barfoofoobarthefoobarman", words = ["bar","foo","the"]
Output: [6,9,12]

Constraints:
1 <= s.length <= $10^{4}$
s consists of lower-case English letters.
1 <= words.length <= 5000
1 <= words[i].length <= 30
words[i] consists of lower-case English letters.

Solution:

```cpp
class Solution {
public:
    vector<int> findSubstring(string s, vector<string>& words) {
        vector<int> res;
        int n = int(words.size()), sLen = int(s.length());
        if (n == 0 || n * words[0].length() > sLen) { return res; }
        sort(words.begin(), words.end());
        int i = 0, wordLen = int(words[0].length()), wordsLen = n * wordLen;
        while (i + wordsLen <= sLen)
        {
            string subStr = s.substr(i, wordsLen);
            vector<string> tmp;
            for (int j = 0; j < wordsLen; j += wordLen)
            {
                tmp.push_back(subStr.substr(j, wordLen));
            }
            sort(tmp.begin(), tmp.end());
            bool isOk = true;
            for (int k = 0; k < n; k++)
            {
                if (tmp[k] != words[k])
                {
                    isOk = false;
                    break;
                }
            }
            if (isOk) { res.push_back(i); }
            i++;
        }
        return res;
    }
};
```

## 0031. Next Permutation*
Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers.
If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).
The replacement must be in-place and use only constant extra memory.
Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.

Example:
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1

Solution:

```cpp
class Solution {
public:
    void nextPermutation(vector<int>& nums) {
        int n = int(nums.size()), idx = -1;
        if (n < 2) { return; }
        for (int i = n - 1; i > 0; i--)
        {
            if (nums[i-1] < nums[i])
            {
                idx = i - 1;
                break;
            }
        }
        if (idx == -1)
        {
            sort(nums.begin(), nums.end());
            return;
        }
        for (int i = n - 1; i > idx; i--)
        {
            if (nums[i] > nums[idx])
            {
                swap(nums[i], nums[idx]);
                sort(nums.begin()+idx+1, nums.end());
                return;
            }
        }
    }
};
```

## 0032. Longest Valid Parentheses*
Given a string containing just the characters '(' and ')', find the length of the longest valid (well-formed) parentheses substring.

Example 1:
Input: "(()"
Output: 2
Explanation: The longest valid parentheses substring is "()"
Example 2:
Input: ")()())"
Output: 4
Explanation: The longest valid parentheses substring is "()()"

Solution:

```cpp
class Solution {
public:
    int longestValidParentheses(string s) {
        int maxLen = 0, n = int(s.length()), leftCount = 0;
        if (n < 2) { return maxLen; }
        int *dp = new int [n]{};
        for (int i = 0; i < n; i++)
        {
            if (leftCount == 0 && s[i] == ')') { continue; }
            if (s[i] == '(') { leftCount++; }
            else
            {
                leftCount--;
                dp[i] = dp[i-1] + 1;
                if (i - dp[i] * 2 >= 0) { dp[i] += dp[i-dp[i]*2]; }
            }
            maxLen = maxLen > dp[i] ? maxLen : dp[i];
        }
        delete []dp;
        return maxLen * 2;
    }
};
```

## 0034. Find First and Last Position of Element in Sorted Array
Given an array of integers nums sorted in ascending order, find the starting and ending position of a given target value. If target is not found in the array, return [-1, -1]. Follow up: Could you write an algorithm with O(log n) runtime complexity?

Example:
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]

Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]

Input: nums = [], target = 0
Output: [-1,-1]

Constraints:
0 <= nums.length <= $10^{5}$
-$10^{9}$ <= nums[i] <= $10^{9}$
nums is a non-decreasing array.
-$10^{9}$ <= target <= $10^{9}$

Solution:

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int n = int(nums.size());
        vector<int> res;
        res.push_back(-1);
        res.push_back(-1);
        if (n < 1) { return res; }
        res[0] = binSearFir(nums, target, 0, n-1);
        res[1] = binSearSec(nums, target, 0, n);
        return res;
    }
    int binSearFir(vector<int> &nums, int target, int b, int e)
    {
        if (b == e)
        {
            if (nums[b] == target) { return b; }
            else { return -1; }
        }
        int m = (b + e) / 2;
        if (nums[m] < target) { return binSearFir(nums, target, m+1, e); }
        else { return binSearFir(nums, target, b, m); }
    }
    int binSearSec(vector<int> &nums, int target, int b, int e)
    {
        if (b + 1 == e)
        {
            if (nums[b] == target) { return b; }
            else { return -1; }
        }
        int m = (b + e) / 2;
        if (nums[m] <= target) { return binSearSec(nums, target, m, e); }
        else { return binSearSec(nums, target, b, m); }
    }
};
```

## 0035. 搜索插入位置
```cpp
/*
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。
如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
你可以假设数组中无重复元素。

示例 1:
输入: [1,3,5,6], 5
输出: 2

示例 2:
输入: [1,3,5,6], 2
输出: 1

示例 3:
输入: [1,3,5,6], 7
输出: 4
*/
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int l = 0, r = (int)nums.size();
        while (l < r) {
            int m = (l + r) >> 1;
            if (nums[m] < target) { l = m + 1; }
            else { r = m; }
        }
        return l;
    }
};
```

## 0036. Valid Sudoku*
Determine if a 9 x 9 Sudoku board is valid. Only the filled cells need to be validated according to the following rules:
Each row must contain the digits 1-9 without repetition.
Each column must contain the digits 1-9 without repetition.
Each of the nine 3 x 3 sub-boxes of the grid must contain the digits 1-9 without repetition.
Note:
A Sudoku board (partially filled) could be valid but is not necessarily solvable.
Only the filled cells need to be validated according to the mentioned rules.

$$
\begin{bmatrix}
5 & 3 & . & . & 7 & . & . & . & . \\
6 & . & . & 1 & 9 & 5 & . & . & . \\
. & 9 & 8 & . & . & . & . & 6 & . \\
8 & . & . & . & 6 & . & . & . & 3 \\
4 & . & . & 8 & . & 3 & . & . & 1 \\
7 & . & . & . & 2 & . & . & . & 6 \\
. & 6 & . & . & . & . & 2 & 8 & . \\
. & . & . & 4 & 1 & 9 & . & . & 5 \\
. & . & . & . & 8 & . & . & 7 & 9
\end{bmatrix}
$$

Example:
Input: board = 
[["5","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: true

Input: board = 
[["8","3",".",".","7",".",".",".","."]
,["6",".",".","1","9","5",".",".","."]
,[".","9","8",".",".",".",".","6","."]
,["8",".",".",".","6",".",".",".","3"]
,["4",".",".","8",".","3",".",".","1"]
,["7",".",".",".","2",".",".",".","6"]
,[".","6",".",".",".",".","2","8","."]
,[".",".",".","4","1","9",".",".","5"]
,[".",".",".",".","8",".",".","7","9"]]
Output: false
Explanation: Same as Example 1, except with the 5 in the top left corner being modified to 8. Since there are two 8's in the top left 3x3 sub-box, it is invalid.

Constraints:
board.length == 9
board[i].length == 9
board[i][j] is a digit or '.'.

Solution:

```cpp
class Solution {
public:
    bool isValidSudoku(vector<vector<char>>& board) {
        for (int i = 0; i < 9; i++)
        {
            for (int j = 0; j < 9; j++)
            {
                if (board[i][j] == '.') { continue; }
                if (!isValidForij(board, i, j)) { return false; }
            }
        }
        return true;
    }
    bool isValidForij(vector<vector<char>> board, int i, int j)
    {
        int dupRow = 0, dupCol = 0;
        for (int k = 0; k < 9; k++)
        {
            if (board[k][j] == board[i][j]) { dupRow++; }
            if (board[i][k] == board[i][j]) { dupCol++; }
        }
        if (dupRow > 1 || dupCol > 1) { return false; }
        int m = i / 3, n = j / 3, dupSubBox = 0;
        for (int x = 0; x < 3; x++)
        {
            for (int y = 0; y < 3; y++)
            {
                if (board[m*3+x][n*3+y] == board[i][j]) { dupSubBox++; }
            }
        }
        if (dupSubBox > 1) { return false; }
        return true;
    }
};
```

## 0037. Sudoku Solver*
Write a program to solve a Sudoku puzzle by filling the empty cells. A sudoku solution must satisfy all of the following rules:
Each of the digits 1-9 must occur exactly once in each row.
Each of the digits 1-9 must occur exactly once in each column.
Each of the digits 1-9 must occur exactly once in each of the 9 3x3 sub-boxes of the grid.
The '.' character indicates empty cells.
$$
\begin{bmatrix}
5 & 3 & . & . & 7 & . & . & . & . \\
6 & . & . & 1 & 9 & 5 & . & . & . \\
. & 9 & 8 & . & . & . & . & 6 & . \\
8 & . & . & . & 6 & . & . & . & 3 \\
4 & . & . & 8 & . & 3 & . & . & 1 \\
7 & . & . & . & 2 & . & . & . & 6 \\
. & 6 & . & . & . & . & 2 & 8 & . \\
. & . & . & 4 & 1 & 9 & . & . & 5 \\
. & . & . & . & 8 & . & . & 7 & 9
\end{bmatrix} \Rightarrow
\begin{bmatrix}
5 & 3 & {\color{Red} 4} & {\color{Red} 6} & 7 & {\color{Red} 8} & {\color{Red} 9} & {\color{Red} 1} & {\color{Red} 2} \\
6 & {\color{Red} 7} & {\color{Red} 2} & 1 & 9 & 5 & {\color{Red} 3} & {\color{Red} 4} & {\color{Red} 8} \\
{\color{Red} 1} & 9 & 8 & {\color{Red} 3} & {\color{Red} 4} & {\color{Red} 2} & {\color{Red} 5} & 6 & {\color{Red} 7} \\
8 & {\color{Red} 5} & {\color{Red} 9} & {\color{Red} 7} & 6 & {\color{Red} 1} & {\color{Red} 4} & {\color{Red} 2} & 3 \\
4 & {\color{Red} 2} & {\color{Red} 6} & 8 & {\color{Red} 5} & 3 & {\color{Red} 7} & {\color{Red} 9} & 1 \\
7 & {\color{Red} 1} & {\color{Red} 3} & {\color{Red} 9} & 2 & {\color{Red} 4} & {\color{Red} 8} & {\color{Red} 5} & 6 \\
{\color{Red} 9} & 6 & {\color{Red} 1} & {\color{Red} 5} & {\color{Red} 3} & {\color{Red} 7} & 2 & 8 & {\color{Red} 4} \\
{\color{Red} 2} & {\color{Red} 8} & {\color{Red} 7} & 4 & 1 & 9 & {\color{Red} 6} & {\color{Red} 3} & 5 \\
{\color{Red} 3} & {\color{Red} 4} & {\color{Red} 5} & {\color{Red} 2} & 8 & {\color{Red} 6} & {\color{Red} 1} & 7 & 9
\end{bmatrix}
$$

Example:
Input: board =
[["5","3",".",".","7",".",".",".","."],
["6",".",".","1","9","5",".",".","."],
[".","9","8",".",".",".",".","6","."],
["8",".",".",".","6",".",".",".","3"],
["4",".",".","8",".","3",".",".","1"],
["7",".",".",".","2",".",".",".","6"],
[".","6",".",".",".",".","2","8","."],
[".",".",".","4","1","9",".",".","5"],
[".",".",".",".","8",".",".","7","9"]]
Output:
[["5","3","4","6","7","8","9","1","2"],
["6","7","2","1","9","5","3","4","8"],
["1","9","8","3","4","2","5","6","7"],
["8","5","9","7","6","1","4","2","3"],
["4","2","6","8","5","3","7","9","1"],
["7","1","3","9","2","4","8","5","6"],
["9","6","1","5","3","7","2","8","4"],
["2","8","7","4","1","9","6","3","5"],
["3","4","5","2","8","6","1","7","9"]]

Constraints:
board.length == 9
board[i].length == 9
board[i][j] is a digit or '.'.
It is guaranteed that the input board has only one solution.

Solution:

```cpp
class Solution {
public:
    void solveSudoku(vector<vector<char>>& board) {
        isSolver(board);
    }
    bool isSolver(vector<vector<char>> &board)
    {
        for (int i = 0; i < 9; i++)
        {
            for (int j = 0; j < 9; j++)
            {
                if (board[i][j] == '.')
                {
                    for (char c = '1'; c <= '9'; c++)
                    {
                        if (isFeasible(board, i, j, c))
                        {
                            board[i][j] = c;
                            if (isSolver(board)) { return true; }
                            else { board[i][j] = '.'; }
                        }
                    }
                    // No feasible number
                    return false;
                }
            }
        }
        return true;
    }
    bool isFeasible(vector<vector<char>> board, int row, int col, char c)
    {
        for(int i = 0; i < 9; i++)
        {
            if(board[row][i] == c) { return false; }
            if(board[i][col] == c) { return false; }
            if(board[3*(row/3)+i/3][3*(col/3)+i%3] == c) { return false; }
        }
        return true;
    }
};
```

## 0038. Count and Say
The count-and-say sequence is a sequence of digit strings defined by the recursive formula:
countAndSay(1) = "1"
countAndSay(n) is the way you would "say" the digit string from countAndSay(n-1), which is then converted into a different digit string.
To determine how you "say" a digit string, split it into the minimal number of groups so that each group is a contiguous section all of the same character. Then for each group, say the number of characters, then say the character. To convert the saying into a digit string, replace the counts with a number and concatenate every saying.

Example:
Input: n = 1
Output: "1"
Explanation: This is the base case.

Input: n = 4
Output: "1211"
Explanation:
countAndSay(1) = "1"
countAndSay(2) = say "1" = one 1 = "11"
countAndSay(3) = say "11" = two 1's = "21"
countAndSay(4) = say "21" = one 2 + one 1 = "12" + "11" = "1211"

Constraints:
1 <= n <= 30

Solution:

```cpp
class Solution {
public:
    string countAndSay(int n) {
        string res = "1";
        if (n == 1) { return res; }
        for (int i = 2; i <= n; i++)
        {
            string cur = "";
            res.push_back('0');
            int len = int(res.length()), count = 1;
            char ch = res[0];
            for (int i = 1; i < len; i++)
            {
                if (res[i] == res[i-1]) { count++; }
                else
                {
                    cur += to_string(count);
                    cur.push_back(ch);
                    count = 1;
                    ch = res[i];
                }
            }
            res = cur;
        }
        return res;
    }
};
```

## 0041. First Missing Positive*
Given an unsorted integer array, find the smallest missing positive integer.

Example:
Input: [1,2,0]
Output: 3

Input: [3,4,-1,1]
Output: 2

Input: [7,8,9,11,12]
Output: 1

Follow up:
Your algorithm should run in O(n) time and uses constant extra space.

Solution:

$$
\begin{matrix}
3 & 4 & -1 & 1 \\
 & & \Downarrow & \\
3 & 4 & 5 & 1 \\
 & & \Downarrow & \\
3 & 4 & -5 & 1 \\
3 & 4 & -5 & -1 \\
-3 & 4 & -5 & -1
\end{matrix}
$$

```cpp
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        int n = int(nums.size());
        for (int i = 0; i < n; i++)
        {
            if (nums[i] <= 0) { nums[i] = n + 1; }
        }
        for (int i = 0; i < n; i++)
        {
            int num = abs(nums[i]);
            if (num <= n)
            {
                nums[num-1] = -abs(nums[num-1]);
            }
        }
        for (int i = 0; i < n; i++)
        {
            if (nums[i] > 0) { return i + 1; }
        }
        return n + 1;
    }
};
```

## 0042. Trapping Rain Water*
```cpp
/*
Input: height = [0,1,0,2,1,0,1,3,2,1,2,1]
Output: 6
Explanation: The above elevation map (black section) is
represented by array [0,1,0,2,1,0,1,3,2,1,2,1]. In this
case, 6 units of rain water (blue section) are being
trapped.
*/
class Solution {
public:
    int trap(vector<int>& height) {
        int ans = 0, n = int(height.size());
        if (n < 3) { return ans; }
        int i = 0, j = n-1;
        int leftMax = 0, rightMax = 0;
        while (i < j)
        {
            if (height[i] < height[j])
            {
                if (height[i] >= leftMax) { leftMax = height[i]; }
                else { ans += leftMax - height[i]; }
                i++;
            }
            else
            {
                if (height[j] >= rightMax) { rightMax = height[j]; }
                else { ans += rightMax - height[j]; }
                j--;
            }
        }
        return ans;
    }
};
```

## 0043. Multiply Strings
```cpp
/*
Input: num1 = "123", num2 = "456"
Output: "56088"
*/
class Solution {
public:
    string multiply(string num1, string num2) {
        string ans = "";
        if (num1 == "0" || num2 == "0") { return "0"; }
        int n2 = int(num2.length());
        for (int j = n2-1; j >= 0; j--)
        {
            string str = strMultiChar(num1, num2[j]);
            str.insert(str.length(), n2-1-j, '0');
            ans = strAdd(ans, str);
        }
        return ans;
    }
    string strAdd(string s1, string s2)
    {
        int n1 = int(s1.length()), n2 = int(s2.length());
        if (n1 < n2)
        {
            swap(s1, s2);
            swap(n1, n2);
        }
        string ans = "";
        int i = n1-1, j = n2-1, add1 = 0;
        while (i >= 0 && j >= 0)
        {
            int sum = (s1[i--] - '0') + (s2[j--] - '0') + add1;
            add1 = 0;
            if (sum > 9)
            {
                sum -= 10;
                add1 = 1;
            }
            char insertChar = '0' + sum;
            ans.insert(0, 1, insertChar);
        }
        while (i >= 0)
        {
            int sum = (s1[i--] - '0') + add1;
            add1 = 0;
            if (sum > 9)
            {
                sum -= 10;
                add1 = 1;
            }
            char insertChar = '0' + sum;
            ans.insert(0, 1, insertChar);
        }
        if (add1) { ans.insert(0, 1, '1'); }
        return ans;
    }
    string strMultiChar(string str, char c)
    {
        string ans = "";
        int n = int(str.length()), add1 = 0;
        for (int i = n-1; i >= 0; i--)
        {
            int res = (str[i] - '0') * (c - '0') + add1;
            add1 = 0;
            if (res > 9)
            {
                add1 = res / 10;
                res = res % 10;
            }
            char insertChar = '0' + res;
            ans.insert(0, 1, insertChar);
        }
        if (add1) { ans = to_string(add1) + ans; }
        return ans;
    }
};
```

## 0044. Wildcard Matching
```cpp
/*
Given an input string (s) and a pattern (p),
implement wildcard pattern matching with support
for '?' and '*' where:
'?' Matches any single character.
'*' Matches any sequence of characters (including
the empty sequence).
The matching should cover the entire input string
(not partial).
Input: s = "adceb", p = "*a*b"
Output: true
*/
class Solution {
public:
    bool isMatch(string s, string p) {
        int ns = int(s.length()), np = int(p.length());
        int **dp = new int *[ns+1];
        for (int i = 0; i <= ns; i++) { dp[i] = new int [np+1]{}; }
        dp[ns][np] = true;
        for (int i = ns; i >= 0; i--)
        {
            for (int j = np-1; j >= 0; j--)
            {
                bool ijMat = i < ns && (s[i] == p[j] || p[j] == '?' ||
                                        p[j] == '*');
                if (p[j] == '*')
                {
                    dp[i][j] = dp[i][j+1] || (ijMat && dp[i+1][j]);
                }
                else { dp[i][j] = ijMat && dp[i+1][j+1]; }
            }
        }
        bool ans = dp[0][0];
        for (int i = 0; i <= ns; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
};
```

## 0048. Rotate Image
```cpp
/*
1   2   3       7   4   1
4   5   6   ->  8   5   2
7   8   9       9   6   3
*/
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = int(matrix.size());
        if (n <= 1) { return; }
        for (int i = 0; i < n / 2; i++)
        {
            for (int j = i; j < n - 1 - i; j++)
            {
                int b = i, e = n - 1 - i;
                int first = matrix[i][j];
                matrix[i][j] = matrix[b+e-j][b];
                matrix[b+e-j][b] = matrix[n-1-i][n-1-j];
                matrix[n-1-i][n-1-j] = matrix[b+j-i][e];
                matrix[b+j-i][e] = first;
            }
        }
    }
};
```

## 0049. Group Anagrams
```cpp
/*
An Anagram is a word or phrase formed by rearranging
the letters of a different word or phrase, typically
using all the original letters exactly once.
Input: strs = ["eat","tea","tan","ate","nat","bat"]
Output: [["bat"],["nat","tan"],["ate","eat","tea"]]
*/
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<vector<string>> ans;
        int n = int(strs.size());
        if (n == 0) { return ans; }
        unordered_map<string, vector<string>> dict;
        for (string &str : strs)
        {
            string tmp = str;
            sort(tmp.begin(), tmp.end());
            dict[tmp].emplace_back(str);
        }
        unordered_map<string, vector<string>>::iterator iter;
        for (iter = dict.begin(); iter != dict.end(); iter++)
        {
            ans.emplace_back(iter->second);
        }
        return ans;
    }
};
```

## 0050. Pow(x, n)
```cpp
/*
Input: x = 2.00000, n = -2
Output: 0.25000
Explanation: 2-2 = 1/22 = 1/4 = 0.25
*/
class Solution {
public:
    double myPow(double x, int n) {
        double ans = 1.0, tmp = x;
        bool isNeg = false;
        long pow = n;
        if (n < 0)
        {
            pow = -pow;
            isNeg = true;
        }
        long k = 1;
        while (pow > 0)
        {
            if (pow == 1)
            {
                ans *= x;
                break;
            }
            tmp *= tmp;
            k *= 2;
            if (k * 2 > pow)
            {
                ans *= tmp;
                tmp = x;
                pow = pow - k;
                k = 1;
            }
        }
        if (isNeg) { return 1 / ans; }
        return ans;
    }
};
```

## 0051. N-Queens
```cpp
/*
Input: n = 4
Output: [[".Q..","...Q","Q...","..Q."],
        ["..Q.","Q...","...Q",".Q.."]]
*/
class Solution {
public:
    vector<vector<string>> solveNQueens(int n) {
        vector<vector<string>> ans;
        string str = "";
        for (int i = 0; i < n; i++) { str += "."; }
        vector<string> res(n, str);
        unordered_set<int> col, diag1, diag2;
        DFS(col, diag1, diag2, n, 0, ans, res);
        return ans;
    }
    void DFS(unordered_set<int> &col,
             unordered_set<int> &diag1,
             unordered_set<int> &diag2,
             int n, int i,
             vector<vector<string>> &ans,
             vector<string> &res)
    {
        if (i == n)
        {
            ans.emplace_back(res);
            return;
        }
        for (int j = 0; j < n; j++)
        {
            if (isOkPos(col, diag1, diag2, i, j))
            {
                res[i][j] = 'Q';
                col.insert(j);
                diag1.insert(i-j);
                diag2.insert(i+j);
                DFS(col, diag1, diag2, n, i+1, ans, res);
                res[i][j] = '.';
                col.erase(j);
                diag1.erase(i-j);
                diag2.erase(i+j);
            }
        }
    }
    bool isOkPos(unordered_set<int> &col, unordered_set<int> &diag1,
                    unordered_set<int> &diag2, int i, int j)
    {
        if (col.find(j) != col.end() ||
            diag1.find(i-j) != diag1.end() ||
            diag2.find(i+j) != diag2.end()) { return false; }
        return true;
    }
};
```

## 0052. N-Queens II
```cpp
/*
Input: n = 4
Output: 2
*/
class Solution {
public:
    int totalNQueens(int n) {
        int ans = 0;
        unordered_set<int> col, dg1, dg2;
        DFS(col, dg1, dg2, 0, n, ans);
        return ans;
    }
    void DFS(unordered_set<int> col, unordered_set<int> dg1,
             unordered_set<int> dg2, int i, int n, int &ans)
    {
        if (i == n) { ans++; return; }
        for (int j = 0; j < n; j++)
        {
            if (isOkQueenPos(col, dg1, dg2, i, j))
            {
                col.insert(j);
                dg1.insert(i-j);
                dg2.insert(i+j);
                DFS(col, dg1, dg2, i+1, n, ans);
                col.erase(j);
                dg1.erase(i-j);
                dg2.erase(i+j);
            }
        }
    }
    bool isOkQueenPos(unordered_set<int> col,
                      unordered_set<int> dg1,
                      unordered_set<int> dg2,
                      int i, int j)
    {
        if (col.find(j) != col.end() ||
            dg1.find(i-j) != dg1.end() ||
            dg2.find(i+j) != dg2.end()) { return false; }
        return true;
    }
};
```

## 0054. Spiral Matrix
```cpp
/*
1 -> 2 -> 3
          |
          v
4 -> 5    6
^         |
|         v
7 <- 8 <- 9
*/
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> ans;
        int m = int(matrix.size());
        if (m == 0) { return ans; }
        int n = int(matrix[0].size());
        if (n == 0) { return ans; }
        bool *vis = new bool [m*n]{};
        int i = 0, j = 0;
        while (true)
        {
            bool updated = false;
            while (true)
            {
                if (j >= n || vis[i*n+j])
                {
                    j--;
                    i++;
                    break;
                }
                ans.emplace_back(matrix[i][j]);
                vis[i*n+j] = true;
                updated = true;
                j++;
                
            }
            while (true)
            {
                if (i >= m || vis[i*n+j])
                {
                    i--;
                    j--;
                    break;
                }
                ans.emplace_back(matrix[i][j]);
                vis[i*n+j] = true;
                updated = true;
                i++;
            }
            while (true)
            {
                if (j < 0 || vis[i*n+j])
                {
                    j++;
                    i--;
                    break;
                }
                ans.emplace_back(matrix[i][j]);
                vis[i*n+j] = true;
                updated = true;
                j--;
            }
            while (true)
            {
                if (i < 0 || vis[i*n+j])
                {
                    i++;
                    j++;
                    break;
                }
                ans.emplace_back(matrix[i][j]);
                vis[i*n+j] = true;
                updated = true;
                i--;
            }
            if (!updated) { break; }
        }
        delete []vis;
        return ans;
    }
};
```

## 0056. Merge Intervals
```cpp
/*
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps,
merge them into [1,6].
*/
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> ans;
        int n = int(intervals.size());
        sort(intervals.begin(), intervals.end());
        int b = 0, rightMax = intervals[b][1];
        for (int i = 1; i < n; i++)
        {
            if (rightMax < intervals[i-1][1]) { rightMax = intervals[i-1][1]; }
            if (rightMax < intervals[i][0])
            {
                ans.emplace_back(vector<int> {
                    intervals[b][0], rightMax
                });
                b = i;
                rightMax = intervals[i][1];
            }
        }
        ans.emplace_back(vector<int> {
            intervals[b][0], max(intervals[n-1][1], rightMax)
        });
        return ans;
    }
};
```

## 0057. Insert Interval
```cpp
/*
Input: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]],
newInterval = [4,8]
Output: [[1,2],[3,10],[12,16]]
Explanation: Because the new interval [4,8] overlaps
with [3,5],[6,7],[8,10].
*/
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals,
                               vector<int>& newInterval)
    {
        vector<vector<int>> ans;
        int n = int(intervals.size());
        if (n == 0 || (intervals[0][0] >= newInterval[0] &&
                       intervals[n-1][1] <= newInterval[1]))
        {
            ans.emplace_back(newInterval);
            return ans;
        }
        int b = 0, e = -1;
        while (b < n)
        {
            if (intervals[b][0] > newInterval[1])
            {
                ans.emplace_back(newInterval);
                for (int i = b; i < n; i++) { ans.emplace_back(intervals[i]); }
                return ans;
            }
            if (intervals[b][0] <= newInterval[0] &&
                intervals[b][1] >=newInterval[1]) { return intervals; }
            if (intervals[b][0] <= newInterval[0] &&
                intervals[b][1] >= newInterval[0]) { break; }
            if (intervals[b][0] > newInterval[0])
            {
                intervals[b][0] = newInterval[0];
                break;
            }
            ans.emplace_back(intervals[b++]);
        }
        if (b == n)
        {
            ans.emplace_back(newInterval);
            return ans;
        }
        e = b + 1;
        while (e < n &&
               (
                (intervals[e][0] <= newInterval[1] &&
               intervals[e][1] >= newInterval[1]) ||
                (intervals[e][0] >= newInterval[0] &&
                intervals[e][1] <= newInterval[1])
                )
               ) { e++; }
        if (e == n)
        {
            ans.emplace_back(vector<int> {
                min(newInterval[0], intervals[b][0]),
                max(newInterval[1], intervals[n-1][1])
            });
            return ans;
        }
        ans.emplace_back(vector<int> {
            intervals[b][0], max(intervals[e-1][1], newInterval[1])
        });
        for (int i = e; i < n; i++) { ans.emplace_back(intervals[i]); }
        return ans;
    }
};
```

## 0058. Length of Last Word
Given a string s consists of some words separated by spaces, return the length of the last word in the string. If the last word does not exist, return 0. A word is a maximal substring consisting of non-space characters only.

```cpp
/*
Input: s = "Hello World"
Output: 5
*/
class Solution {
public:
    int lengthOfLastWord(string s) {
        int ans = 0, n = int(s.length());
        if (n == 0) { return ans; }
        bool isCount = false;
        int i = n - 1;
        while (i >= 0)
        {
            if (s[i] == ' ' && isCount) { break; }
            if (s[i] == ' ')
            {
                i--;
                continue;
            }
            ans++;
            i--;
            isCount = true;
        }
        return ans;
    }
};
```

## 0059. Spiral Matrix II
```cpp
/*
Input: n = 3
Output: [[1,2,3],[8,9,4],[7,6,5]]
1 -> 2 -> 3
          |
          v
8 -> 9    4
^         |
|         v
7 <- 6 <- 5
*/
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> ans(n, vector<int>(n, 0));
        int i = 0, j = 0, num = 1;
        while (num <= n * n)
        {
            while (true)
            {
                if (j >= n || ans[i][j])
                {
                    j--;
                    i++;
                    break;
                }
                ans[i][j++] = num++;
            }
            while (true)
            {
                if (i >= n || ans[i][j])
                {
                    i--;
                    j--;
                    break;
                }
                ans[i++][j] = num++;
            }
            while (true)
            {
                if (j < 0 || ans[i][j])
                {
                    j++;
                    i--;
                    break;
                }
                ans[i][j--] = num++;
            }
            while (true)
            {
                if (i <= 0 || ans[i][j])
                {
                    i++;
                    j++;
                    break;
                }
                ans[i--][j] = num++;
            }
        }
        return ans;
    }
};
```

## 0061. Rotate List
```cpp
/*
Input: head = [1,2,3,4,5], k = 2
Output: [4,5,1,2,3]
*/
class Solution {
public:
    ListNode* rotateRight(ListNode* head, int k) {
        if (head == nullptr || head->next == nullptr || k <= 0)
        {
            return head;
        }
        int cnt = 0;
        ListNode *curNode = head, *tail = head;
        while (curNode != nullptr)
        {
            cnt++;
            if (curNode->next == nullptr) { tail = curNode; }
            curNode = curNode->next;
        }
        k %= cnt;
        k = cnt - k;
        ListNode *p1 = head;
        for (int i = 1; i < k; i++)
        {
            p1 = p1->next;
        }
        tail->next = head;
        head = p1->next;
        p1->next = nullptr;
        return head;
    }
};
```

## 0066. Plus One
Given a non-empty array of decimal digits representing a non-negative integer, increment one to the integer. The digits are stored such that the most significant digit is at the head of the list, and each element in the array contains a single digit. You may assume the integer does not contain any leading zero, except the number 0 itself.

```cpp
/*
Input: digits = [1,2,3]
Output: [1,2,4]
Explanation: The array represents the integer 123.

Input: digits = [4,3,2,1]
Output: [4,3,2,2]
Explanation: The array represents the integer 4321.

Input: digits = [0]
Output: [1]
*/
class Solution {
public:
    vector<int> plusOne(vector<int>& digits) {
        vector<int> ans;
        vector<int>::iterator iter;
        int n = int(digits.size()), add = 1;
        for (int i = n-1; i >= 0; i--)
        {
            int res = digits[i] + add;
            iter = ans.begin();
            if (res >= 10)
            {
                ans.insert(iter, res-10);
                add = 1;
            }
            else
            {
                ans.insert(iter, res);
                add = 0;
            }
        }
        iter = ans.begin();
        if (add) { ans.insert(iter, 1); }
        return ans;
    }
};
```

## 0067. Add Binary
Given two binary strings a and b, return their sum as a binary string.

```cpp
/*
Input: a = "11", b = "1"
Output: "100"

Input: a = "1010", b = "1011"
Output: "10101"
*/
class Solution {
public:
    string addBinary(string a, string b) {
        string str = "";
        int aLen = int(a.length()), bLen = int(b.length());
        int i = aLen - 1, j = bLen - 1, add = 0;
        if (aLen < bLen)
        {
            swap(a, b);
            swap(aLen, bLen);
            swap(i, j);
        }
        while (i >= 0 && j >= 0)
        {
            int sum = a[i] - '0' + b[j] - '0' + add;
            if (sum >= 2)
            {
                char tempChar = '0' + sum - 2;
                str.insert(0, 1, tempChar);
                add = 1;
            }
            else
            {
                char tempChar = '0' + sum;
                str.insert(0, 1, tempChar);
                add = 0;
            }
            i--;
            j--;
        }
        while (i >= 0)
        {
            int sum = a[i] - '0' + add;
            if (sum >= 2)
            {
                char tempChar = '0' + sum - 2;
                str.insert(0, 1, tempChar);
                add = 1;
            }
            else
            {
                char tempChar = '0' + sum;
                str.insert(0, 1, tempChar);
                add = 0;
            }
            i--;
        }
        if (add == 1) { str.insert(0, 1, '1'); }
        return str;
    }
};
```

## 0069 Sqrt(x)*
Given a non-negative integer x, compute and return the square root of x. Since the return type is an integer, the decimal digits are truncated, and only the integer part of the result is returned.

```cpp
/*
Input: x = 4
Output: 2

Input: x = 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and
since the decimal part is truncated, 2 is returned.
*/
class Solution {
public:
    int mySqrt(int x)
    {
        // Binary search
        long lo = 1, hi = x;
        while (lo <= hi)
        {
            long mi = (lo + hi) / 2;
            if (mi * mi < x) { lo = mi + 1; }
            else if (mi * mi > x) { hi = mi - 1; }
            else { return int(mi); }
        }
        return int(hi);
    }
};
```

## 0070. Climbing Stairs
You are climbing a staircase. It takes n steps to reach the top. Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

```cpp
/*
Example:
Input: n = 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps

Input: n = 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step

Constraints:
1 <= n <= 45
*/
class Solution {
public:
    int climbStairs(int n) {
        int a = 1, b = 2;
        if (n <= 2) { return n; }
        for (int i = 3; i <= n; i++)
        {
            int c = a + b;
            a = b;
            b = c;
        }
        return b;
    }
};
```

## 0071. Simplify Path
```cpp
/*
Input: path = "/a/./b/../../c/"
Output: "/c"
*/
class Solution {
public:
    string simplifyPath(string path) {
        int n = int(path.length());
        if (n <= 1) { return "/"; }
        stack<string> stk;
        int i = 0;
        string file = "";
        path.push_back('/');
        while (i <= n)
        {
            if (path[i] == '/')
            {
                if (file.length() == 0) { i++; continue; }
                if (file == "..")
                {
                    if (!stk.empty()) { stk.pop(); }
                }
                else if (file != ".") { stk.push(file); }
                file = "";
                i++;
                continue;
            }
            if (path[i] != '/')
            {
                file.push_back(path[i]);
            }
            i++;
        }
        if (stk.empty()) { return "/"; }
        file = stk.top();
        stk.pop();
        while (!stk.empty())
        {
            file = stk.top() + "/" + file;
            stk.pop();
        }
        file = "/" + file;
        return file;
    }
};
```

## 0073. Set Matrix Zeroes
```cpp
/*
0   1   2   0       0   0   0   0
3   4   5   2   ->  0   4   5   0
1   3   1   5       0   3   1   0
*/
class Solution {
public:
    void setZeroes(vector<vector<int>>& matrix) {
        int nRow = int(matrix.size());
        int nCol = int(matrix[0].size());
        unordered_set<int> cols, rows;
        for (int i = 0; i < nRow; i++)
        {
            for (int j = 0; j < nCol; j++)
            {
                if (matrix[i][j] == 0)
                {
                    rows.insert(i);
                    cols.insert(j);
                }
            }
        }
        unordered_set<int>::iterator iter;
        for (iter = rows.begin(); iter != rows.end(); iter++)
        {
            int i = *iter;
            for (int j = 0; j < nCol; j++) { matrix[i][j] = 0; }
        }
        for (iter = cols.begin(); iter != cols.end(); iter++)
        {
            int j = *iter;
            for (int i = 0; i < nRow; i++) { matrix[i][j] = 0; }
        }
    }
};
```

## 0075. Sort Colors*
```cpp
/*
Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
*/
class Solution {
public:
    void sortColors(vector<int>& nums) {
        int n = int(nums.size());
        if (n == 0) { return; }
        int p0 = 0, p1 = 0;
        for (int i = 0; i < n; i++)
        {
            if (nums[i] == 0)
            {
                swap(nums[i], nums[p0]);
                if (p0 < p1) { swap(nums[i], nums[p1]); }
                p0++;
                p1++;
            }
            else if (nums[i] == 1) { swap(nums[i], nums[p1++]); }
        }
    }
};
```

## 0076. Minimum Window Substring*
```cpp
/*
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
*/
class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<char, int> tDict, sDict;
        for (const auto &c : t) { tDict[c]++; }
        int ns = int(s.length());
        int l = 0, r = -1, ansL = -1, minLen = INT_MAX;
        while (r < ns)
        {
            if (tDict.find(s[++r]) != tDict.end()) { sDict[s[r]]++; }
            while (isCover(tDict, sDict) && l <= r)
            {
                if (r - l + 1 < minLen)
                {
                    minLen = r - l + 1;
                    ansL = l;
                }
                if (tDict.find(s[l]) != tDict.end())
                {
                    sDict[s[l]]--;
                }
                l++;
            }
        }
        return -1 == ansL ? string() : s.substr(ansL, minLen);
    }
    bool isCover(unordered_map<char, int> &tDict,
                 unordered_map<char, int> &sDict)
    {
        for (const auto &item : tDict)
        {
            if (sDict[item.first] < item.second) { return false; }
        }
        return true;
    }
};
```

## 0077. Combinations
```cpp
/*
Input: n = 4, k = 2
Output:
1, 4
3, 4
2, 3
1, 2
1, 3
1, 4
*/
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> ans;
        if (n < 0 || k < 0 || n < k) { return ans; }
        vector<int> arr;
        combination(ans, arr, n, k, 1);
        return ans;
    }
    void combination(vector<vector<int>> &ans,
                     vector<int> &arr,
                     int n, int k, int idx)
    {
        if (k == 0) { ans.emplace_back(arr); return; }
        for (int i = idx; i <= n; i++)
        {
            arr.emplace_back(i);
            combination(ans, arr, n, k-1, i+1);
            arr.pop_back();
        }
    }
};
```

## 0078. Subsets
```cpp
/*
Input: nums = [1,2,3]
Output: [[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]
*/
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> ans;
        int n = int(nums.size());
        vector<int> arr;
        dfs(ans, arr, nums, n, 0);
        return ans;
    }
    void dfs(vector<vector<int>> &ans,
             vector<int> &arr,
             vector<int> &nums,
             int n, int idx)
    {
        if (idx == n) { ans.emplace_back(arr); }
        else
        {
            arr.emplace_back(nums[idx]);
            dfs(ans, arr, nums, n, idx+1);
            arr.pop_back();
            dfs(ans, arr, nums, n, idx+1);
        }
    }
};
```

## 0079. Word Search
```cpp
/*
board =
A   B   C   E
S   F   C   S
A   D   E   E
word = "ABCCED", return true
*/
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        int nRow = int(board.size());
        if (nRow == 0) { return false; }
        int nCol = int(board[0].size());
        if (nCol == 0) { return false; }
        int len = int(word.length());
        bool ans = false;
        bool *vis = new bool [nRow*nCol ]{};
        for (int i = 0; i < nRow; i++)
        {
            for (int j = 0; j < nCol; j++)
            {
                if (board[i][j] != word[0]) { continue; }
                ans = isExistWord(board, word, nRow, nCol, i, j, vis, len, 0);
                if (ans) { break; }
            }
            if (ans) { break; }
        }
        delete []vis;
        return ans;
    }
    bool isExistWord(vector<vector<char>> &board, string word,
                     int nRow, int nCol, int i, int j, bool *vis,
                     int len, int k)
    {
        if (i < 0 || i >= nRow || j < 0 || j >= nCol || vis[i*nCol+j ] ||
            board[i][j] != word[k]) { return false; }
        if (k == len-1) { return true; }
        vis[i*nCol+j ] = true;
        bool u = isExistWord(board, word, nRow, nCol, i-1, j, vis, len, k+1);
        if (u) { return true; }
        bool d = isExistWord(board, word, nRow, nCol, i+1, j, vis, len, k+1);
        if (d) { return true; }
        bool l = isExistWord(board, word, nRow, nCol, i, j-1, vis, len, k+1);
        if (l) { return true; }
        bool r = isExistWord(board, word, nRow, nCol, i, j+1, vis, len, k+1);
        if (r) { return true; }
        vis[i*nCol+j ] = false;
        return false;
    }
};
```

## 0080. Remove Duplicates from Sorted Array II*
Given a sorted array nums, remove the duplicates in-place such that duplicates appeared at most twice and return the new length. Do not allocate extra space for another array; you must do this by modifying the input array in-place with O(1) extra memory.

```cpp
/*
Input: nums = [1,1,1,2,2,3]
Output: 5, nums = [1,1,2,2,3]

Input: nums = [0,0,1,1,1,1,2,3,3]
Output: 7, nums = [0,0,1,1,2,3,3]
*/
class Solution {
public:
    int removeDuplicates(vector<int>& nums) {
        int n = int(nums.size());
        if (n < 3) { return n; }
        int len = 1, count = 1;
        for (int i = 1; i < n; i++)
        {
            if (nums[i] == nums[i-1]) { count++; }
            else { count = 1; }
            if (count <= 2) { nums[len++] = nums[i]; }
        }
        return len;
    }
};
```

## 0082. Remove Duplicates from Sorted List II*
```cpp
/*
1 -> 2 -> 3 -> 3 -> 4 -> 4 ->5
1 -> 2 -> 5
*/
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == nullptr || head->next == nullptr) { return head; }
        ListNode *pre = new ListNode(0);
        pre->next = head;
        ListNode *p1 = pre, *p2 = head;
        while (p2 != nullptr)
        {
            bool isDup = false;
            while (p2->next != nullptr &&
                   p2->next->val == p2->val)
            {
                p2 = p2->next;
                isDup = true;
            }
            p2 = p2->next;
            if (isDup) { p1->next = p2; }
            else { p1 = p1->next; }
        }
        head = pre->next;
        delete pre;
        return head;
    }
};
```

## 0083. Remove Duplicates from Sorted List
```cpp
/*
Input: 1 -> 1 -> 2
Output: 1 -> 2
*/
class Solution {
public:
    ListNode* deleteDuplicates(ListNode* head) {
        if (head == nullptr) { return head; }
        ListNode *p1 = head, *p2 = head->next;
        while (true)
        {
            while (p2 != nullptr && p1->val == p2->val) { p2 = p2->next; }
            p1->next = p2;
            p1 = p2;
            if (p2 == nullptr) { break; }
            p2 = p2->next;
        }
        return head;
    }
};
```

## 0084. Largest Rectangle in Histogram*
```cpp
/*
Input: heights = [2,1,5,6,2,3]
Output: 10
Explanation: The above is a histogram where width of each bar is 1.
The largest rectangle is shown in the red area, which has an area = 10 units.
*/
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        int ans = 0, n = int(heights.size());
        if (n == 0) { return ans; }
        heights.emplace_back(0);
        stack<int> stk;
        for (int i = 0; i <= n; i++)
        {
            while (!stk.empty() && heights[stk.top()] > heights[i])
            {
                int H = heights[stk.top()];
                int W = i;
                stk.pop();
                if (!stk.empty()) { W = i - stk.top() - 1; }
                int area = H * W;
                if (ans < area) { ans = area; }
            }
            stk.push(i);
        }
        heights.pop_back();
        return ans;
    }
};
```

## 0085. Maximal Rectangle*
```cpp
/*
1 0 1 0 0
1 0 1 1 1
1 1 1 1 1
1 0 0 1 0
max area = 6
*/
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        int ans = 0;
        int nRow = int(matrix.size());
        if (nRow == 0) { return ans; }
        int nCol = int(matrix[0].size());
        vector<int> heights(nCol, 0);
        for (int i = 0; i < nRow; i++)
        {
            for (int j = 0; j < nCol; j++)
            {
                heights[j] = (heights[j] + 1) * (matrix[i][j] - '0');
            }
            ans = max(ans, getRecArea(heights));
        }
        return ans;
    }
    int getRecArea(vector<int> heights)
    {
        int ans = 0, n = int(heights.size());
        stack<int> stk;
        heights.emplace_back(0);
        for (int i = 0; i <= n; i++)
        {
            while (!stk.empty() && heights[stk.top()] > heights[i])
            {
                int H = heights[stk.top()];
                int W = i;
                stk.pop();
                if (!stk.empty()) { W = i - stk.top() - 1; }
                ans = max(ans, W * H);
            }
            stk.push(i);
        }
        return ans;
    }
};
```

## 0086. Partition List
Given the head of a linked list and a value x, partition it such that all nodes less than x come before nodes greater than or equal to x. You should preserve the original relative order of the nodes in each of the two partitions.

```cpp
/*
from 1 -> 4 -> 3 -> 2 -> 5 -> 2
to   1 -> 2 -> 2 -> 4 -> 3 -> 5
*/
Input: head = [1,4,3,2,5,2], x = 3
Output: [1,2,2,4,3,5]

class Solution {
public:
    ListNode* partition(ListNode* head, int x) {
        if (head == nullptr) { return head; }
        ListNode *h1 = new ListNode(0), *h2 = new ListNode(0);
        ListNode *p1 = head, *p2 = head->next, *cur1 = h1, *cur2 = h2;
        while (p1 != nullptr)
        {
            if (p1->val < x)
            {
                cur1->next = p1;
                cur1 = p1;
            }
            else
            {
                cur2->next = p1;
                cur2 = p1;
            }
            p1 = p2;
            if (p2 == nullptr) { break; }
            p2 = p2->next;
        }
        cur2->next = nullptr;
        cur1->next = h2->next;
        head = h1->next;
        delete h1;
        delete h2;
        return head;
    }
};
```

## 0087. Scramble String*
```cpp
/*
We can scramble a string s to get a string t using the
following algorithm:
If the length of the string is 1, stop.
If the length of the string is > 1, do the following:
Split the string into two non-empty substrings at a random index,
i.e., if the string is s, divide it to x and y where s = x + y.
Randomly decide to swap the two substrings or to keep them in the
same order. i.e., after this step, s may become s = x + y or s = y + x.
Apply step 1 recursively on each of the two substrings x and y.
Given two strings s1 and s2 of the same length, return true if s2 is
a scrambled string of s1, otherwise, return false.

Input: s1 = "great", s2 = "rgeat"
Output: true
*/
class Solution {
public:
    bool isScramble(string s1, string s2) {
        int n = int(s1.length());
        if (n != int(s2.length())) { return false; }
        if (n == 0) { return true; }
        bool ***dp = new bool **[n];
        for (int i = 0; i < n; i++)
        {
            dp[i] = new bool *[n];
            for (int j = 0; j < n; j++) { dp[i][j] = new bool [n+1]{}; }
        }
        for (int i = 0; i < n; i++)
        {
            for (int j = 0; j < n; j++)
            {
                dp[i][j][1] = s1[i] == s2[j];
            }
        }
        for (int len = 2; len <= n; len++)
        {
            for (int i = 0; i < n - len + 1; i++)
            {
                for (int j = 0; j < n - len + 1; j++)
                {
                    for (int k = 1; k < len; k++)
                    {
                        dp[i][j][len] = (
                            (dp[i][j][k] && dp[i+k][j+k][len-k]) ||
                            (dp[i+k][j][len-k] && dp[i][j+len-k][k])
                        );
                        if (dp[i][j][len]) { break; }
                    }
                }
            }
        }
        bool ans = dp[0][0][n];
        for (int i = 0; i < n; i++)
        {
            for (int j = 0; j < n; j++) { delete []dp[i][j]; }
            delete []dp[i];
        }
        delete []dp;
        return ans;
    }
};
```

## 0088. Merge Sorted Array
```cpp
/*
Input: nums1 = [1,2,3,0,0,0], m = 3, nums2 = [2,5,6], n = 3
Output: [1,2,2,3,5,6]
*/
class Solution {
public:
    void merge(vector<int>& nums1, int m, vector<int>& nums2, int n) {
        int pos = int(nums1.size()) - 1;
        if (pos < 0) { return; }
        int i = m - 1, j = n - 1;
        while (i >= 0 && j >= 0)
        {
            if (nums1[i] > nums2[j]) { nums1[pos--] = nums1[i--]; }
            else { nums1[pos--] = nums2[j--]; }
        }
        while (i >= 0) { nums1[pos--] = nums1[i--]; }
        while (j >= 0) { nums1[pos--] = nums2[j--]; }
    }
};
```

## 0090. Subsets II*
```cpp
/*
Input: nums = [1,2,2]
Output: [[],[1],[1,2],[1,2,2],[2],[2,2]]
*/
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> ans;
        int n = int(nums.size());
        sort(nums.begin(), nums.end());
        vector<int> arr;
        dfs(ans, arr, nums, 0, n);
        return ans;
    }
    void dfs(vector<vector<int>> &ans,
             vector<int> &arr,
             vector<int> &nums,
             int idx, int n)
    {
        for (int i = idx; i < n; i++)
        {
            if (i > idx && nums[i] == nums[i-1]) { continue; }
            arr.emplace_back(nums[i]);
            dfs(ans, arr, nums, i+1, n);
            arr.pop_back();
        }
        ans.emplace_back(arr);
    }
};
```

## 0091. Decode Ways
```cpp
/*
'A' -> "1"
'B' -> "2"
...
'Z' -> "26"

Input: s = "226"
Output: 3
Explanation: "226" could be decoded as "BZ" (2 26),
"VF" (22 6), or "BBF" (2 2 6).

Input: s = "06"
Output: 0
Explanation: "06" cannot be mapped to "F" because
of the leading zero ("6" is different from "06").
*/
class Solution {
public:
    int numDecodings(string s) {
        int n = (int)s.length();
        if (n == 0 || s[0] == '0') { return 0; }
        if (n == 1) { return 1; }
        int a = (s[n-1] != '0');
        int b = 0;
        if (s[n-2] != '0') {
            b = a + ((s[n-2]-'0')*10 + s[n-1]-'0' <= 26);
        }
        if (n == 2) { return b; }
        for (int i = n - 3; i >= 0; i--) {
            int c = 0;
            if (s[i] == '0') {
                a = b;
                b = c;
                continue;
            }
            c += b;
            if (i < n - 1 && (s[i] - '0') * 10 + (s[i+1] - '0') <= 26) {
                c += a;
            }
            a = b;
            b = c;
        }
        return b;
    }
};
```

## 0092. Reverse Linked List II
```cpp
/*
Input: head = [1,2,3,4,5], left = 2, right = 4
Output: [1,4,3,2,5]
1 -> 2 -> 3 -> 4 -> 5
          |
          v
1 -> 4 -> 3 -> 2 -> 5
*/
class Solution {
public:
    ListNode* reverseBetween(ListNode* head, int left, int right) {
        if (left >= right || head == nullptr) { return head; }
        ListNode *pre = new ListNode(0);
        pre->next = head;
        ListNode *p1 = head, *p0 = pre;
        int i = 1;
        while (i < left)
        {
            p1 = p1->next;
            p0 = p0->next;
            i++;
            if (p1 == nullptr) { delete pre; return head; }
        }
        ListNode *tail = p1, *p2 = p1->next;
        if (p2 == nullptr)
        {
            delete pre;
            return head;
        }
        ListNode *p3 = p2->next;
        while (i < right)
        {
            p2->next = p1;
            p1 = p2;
            p2 = p3;
            if (p3 == nullptr)
            {
                p0->next = p1;
                tail->next = p2;
                head = pre->next;
                delete pre;
                return head;
            }
            p3 = p3->next;
            i++;
        }
        p0->next = p1;
        tail->next = p2;
        head = pre->next;
        delete pre;
        return head;
    }
};
```

## 0093. Restore IP Addresses
```cpp
/*
Input: s = "25525511135"
Output: ["255.255.11.135","255.255.111.35"]
*/
class Solution {
public:
    vector<string> restoreIpAddresses(string s) {
        vector<string> ans;
        int n = int(s.length());
        if (n < 4 || n > 12) { return ans; }
        unordered_map<string, bool> dict;
        vector<string> sol;
        find(ans, s, 0, n, dict, sol);
        return ans;
    }
    void find(vector<string> &ans, string &s, int i,
              int n, unordered_map<string, bool> &dict,
              vector<string> &sol)
    {
        if (sol.size() > 4) { return; }
        if (i >= n)
        {
            if (sol.size() != 4) { return; }
            string res = "";
            for (int i = 0; i < 3; i++)
            {
                res += sol[i];
                res.push_back('.');
            }
            res += sol[3];
            ans.emplace_back(res);
            return;
        }
        for (int k = 1; k <= 3; k++)
        {
            if (i + k > n) { continue; }
            string str = s.substr(i, k);
            if (dict.find(str) == dict.end())
            {
                dict[str] = isLegalIp(str);
            }
            if (!dict[str]) { continue; }
            sol.emplace_back(str);
            find(ans, s, i + k, n, dict, sol);
            sol.pop_back();
        }
    }
    bool isLegalIp(string str)
    {
        int len = int(str.length());
        if (len > 1 && str[0] == '0') { return false; }
        int val = 0;
        for (int i = 0; i < len; i++)
        {
            if (str[i] > '9' || str[i] < '0') { return false; }
            val = val * 10 + (str[i] - '0');
        }
        return val < 256;
    }
};
```

## 0094. Binary Tree Inorder Traversal*
```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> st;
        TreeNode *curNode = root;
        while (curNode != nullptr)
        {
            while (curNode != nullptr)
            {
                if (curNode->right != nullptr) { st.push(curNode->right); }
                st.push(curNode);
                curNode = curNode->left;
            }
            curNode = st.top();
            st.pop();
            while (!st.empty() && curNode->right == nullptr)
            {
                res.push_back(curNode->val);
                curNode = st.top();
                st.pop();
            }
            res.push_back(curNode->val);
            if (!st.empty())
            {
                curNode = st.top();
                st.pop();
            }
            else { curNode = nullptr; }
        }
        return res;
    }
};
```

## 0096. Unique Binary Search Trees
Given an integer n, return the number of structurally unique BST's (binary search trees) which has exactly n nodes of unique values from 1 to n.

$$
\begin{aligned}
& C_{n} =
\begin{cases}
1, &\quad n = 0 \\
\frac{2(2n+1)}{n+2} C_{n-1}, &\quad n = 1, 2, 3, ...
\end{cases} \\
& C_{n} = \frac{(2n)!}{(n+1)! n!}
\end{aligned}
$$

```cpp
/*
Input: n = 3
Output: 5

Input: n = 1
Output: 1
*/
class Solution {
public:
    int numTrees(int n) {
        long C = 1;
        for (int i = 0; i < n; i++)
        {
            C = C * 2 * (2 * i + 1) / (i + 2) ;
        }
        return int(C);
    }
};
```

## 0098. Validate Binary Search Tree
Given the root of a binary tree, determine if it is a valid binary search tree (BST). A valid BST is defined as follows:
The left subtree of a node contains only nodes with keys less than the node's key.
The right subtree of a node contains only nodes with keys greater than the node's key.
Both the left and right subtrees must also be binary search trees.

```cpp
/*
   2
 /   \
1     3
is a BST

    5
  /   \
 1     4
      / \
     3   6
is not a BST
*/
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        TreeNode *prec = nullptr;
        return isBST(root, prec);
    }
    bool isBST(TreeNode* root, TreeNode *&prec)
    {
        if (root != nullptr)
        {
            bool left = isBST(root->left, prec);
            if (left == false) { return false; }
            if (prec == nullptr) { prec = root; }
            else if (prec->val >= root->val) { return false; }
            prec = root;
            bool right = isBST(root->right, prec);
            if (right == false) { return false; }
        }
        return true;
    }
};
```

## 0100. Same Tree
```cpp
/*
Input: p = [1,2,3], q = [1,2,3]
Output: true
*/
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == nullptr && q == nullptr) { return true; }
        if ((p == nullptr && q != nullptr) ||
            (p != nullptr && q == nullptr) ||
            (p->val != q->val)) { return false; }
        bool left = isSameTree(p->left, q->left);
        if (!left) { return false; }
        bool right = isSameTree(p->right, q->right);
        if (!right) { return false; }
        return true;
    }
};
```

## 0101. Symmetric Tree
```cpp
/*
    1
   / \
  2   2
 / \ / \
3  4 4  3
*/
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if (root == nullptr) { return true; }
        return isSymmetricTree(root->left, root->right);
    }
    bool isSymmetricTree(TreeNode *leftTree, TreeNode *rightTree)
    {
        if (leftTree == nullptr && rightTree == nullptr) { return true; }
        if ((leftTree != nullptr && rightTree == nullptr) ||
            (leftTree == nullptr && rightTree != nullptr) ||
            leftTree->val != rightTree->val) { return false; }
        bool left = isSymmetricTree(leftTree->left, rightTree->right);
        if (!left) { return false; }
        bool right = isSymmetricTree(leftTree->right, rightTree->left);
        if (!right) { return false; }
        return true;
    }
};
```

## 0102. Binary Tree Level Order Traversal
```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        vector<int> arr;
        if (root == nullptr) { return ans; }
        queue<TreeNode*> qTree;
        qTree.push(root);
        TreeNode *front = root, *last = root, *nextLast = root;
        while(!qTree.empty())
        {
            front = qTree.front();
            qTree.pop();
            arr.emplace_back(front->val);
            if (front->left != nullptr)
            {
                qTree.push(front->left);
                nextLast = front->left;
            }
            if (front->right != nullptr)
            {
                qTree.push(front->right);
                nextLast = front->right;
            }
            if (front == last)
            {
                ans.emplace_back(arr);
                arr.clear();
                last = nextLast;
            }
        }
        return ans;
    }
};
```

## 0103. Binary Tree Zigzag Level Order Traversal
```cpp
/*
    3
   / \
  9  20
    /  \
   15   7
[
  [3],
  [20,9],
  [15,7]
]
*/
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        vector<int> arr;
        if (root == nullptr) { return ans; }
        queue<TreeNode*> qTree;
        qTree.push(root);
        TreeNode *front = root, *last = root, *nextLast = root;
        int level = 1;
        while(!qTree.empty())
        {
            front = qTree.front();
            qTree.pop();
            arr.emplace_back(front->val);
            if (front->left != nullptr)
            {
                qTree.push(front->left);
                nextLast = front->left;
            }
            if (front->right != nullptr)
            {
                qTree.push(front->right);
                nextLast = front->right;
            }
            if (front == last)
            {
                if (level % 2 == 0)
                {
                    int n = int(arr.size());
                    for (int i = 0; i < n/2; i++)
                    {
                        swap(arr[i], arr[n-1-i]);
                    }
                }
                ans.emplace_back(arr);
                arr.clear();
                last = nextLast;
                level++;
            }
        }
        return ans;
    }
};
```

## 0104. Maximum Depth of Binary Tree
Given the root of a binary tree, return its maximum depth. A binary tree's maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

```cpp
/*
    3
  /   \
 9    20
     /  \
    15  7
max depth = 3
*/
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) { return 0; }
        int leftDep = maxDepth(root->left);
        int rightDep = maxDepth(root->right);
        return 1 + max(leftDep, rightDep);
    }
};
```

## 0110. Balanced Binary Tree
```cpp
class Solution {
public:
    bool isBalanced(TreeNode* root) {
        if (root == nullptr) { return true; }
        bool ans = true;
        getDepth(root, ans);
        return ans;
    }
    int getDepth(TreeNode *root, bool &isBal)
    {
        if (root == nullptr) { return 0; }
        int leftDep = getDepth(root->left, isBal);
        int rightDep = getDepth(root->right, isBal);
        if (abs(leftDep - rightDep) > 1) { isBal = false; }
        return 1 + max(leftDep, rightDep);
    }
};
```

## 0111. Minimum Depth of Binary Tree
Given a binary tree, find its minimum depth. The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node. Note: A leaf is a node with no children.

```cpp
/*
    3
  /   \
 9    20
     /  \
    15  7
min depth = 2
*/
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (root == nullptr) { return 0; }
        int leftDep = minDepth(root->left);
        int rightDep = minDepth(root->right);
        if (leftDep != 0 && rightDep != 0)
        {
            return 1 + min(leftDep, rightDep);
        }
        else { return 1 + max(leftDep, rightDep); }
    }
};
```

## 0112. Path Sum
Given the root of a binary tree and an integer targetSum, return true if the tree has a root-to-leaf path such that adding up all the values along the path equals targetSum. A leaf is a node with no children.

```cpp
/*
                    5
                /       \
               4         8
              /        /   \
            11        13    4
           /  \            / \
          7    2          5   1
Input: root = [5,4,8,11,null,13,4,7,2,null,null,null,1], targetSum = 22
Output: true

     1
   /   \
  2     3
Input: root = [1,2,3], targetSum = 5
Output: false
*/
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if (root == nullptr) { return false; }
        if (root->left == nullptr && root->right == nullptr)
        {
            if (root->val == targetSum) { return true; }
            return false;
        }
        bool isInLeft = hasPathSum(root->left, targetSum-root->val);
        if (isInLeft) { return true; }
        bool isInRight = hasPathSum(root->right, targetSum-root->val);
        if (isInRight) { return true; }
        return false;
    }
};
```

## 0113. Path Sum II
Given the root of a binary tree and an integer targetSum, return all root-to-leaf paths where each path's sum equals targetSum. A leaf is a node with no children.

```cpp
/*
                    5
                /       \
               4         8
              /        /   \
            11        13    4
           /  \            / \
          7    2          5   1
Input: root = [5,4,8,11,null,13,4,7,2,null,null,5,1], targetSum = 22
Output: [[5,4,11,2],[5,8,4,5]]
*/
class Solution {
public:
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        vector<vector<int>> ans;
        if (root == nullptr) { return ans; }
        vector<int> arr;
        findPaths(root, targetSum, ans, arr);
        return ans;
    }
    void findPaths(TreeNode *root, int resVal, vector<vector<int>> &ans,
                   vector<int> &arr)
    {
        if (root == nullptr) { return; }
        if (root->left == nullptr && root->right == nullptr)
        {
            if (resVal == root->val)
            {
                arr.emplace_back(root->val);
                ans.emplace_back(arr);
                arr.pop_back();
            }
            return;
        }
        arr.emplace_back(root->val);
        findPaths(root->left, resVal-root->val, ans, arr);
        findPaths(root->right, resVal-root->val, ans, arr);
        arr.pop_back();
    }
};
```

## 0114. Flatten Binary Tree to Linked List
```cpp
/*
        1                1
      /   \               \
    2       5       ->      2
   / \       \               \
  3   4       6               3
                               \
                                4
                                 \
                                  5
                                   \
                                    6
*/
class Solution {
public:
    void flatten(TreeNode* root) {
        TreeNode *prec = nullptr;
        preOrder(root, prec);
    }
    void preOrder(TreeNode *root, TreeNode *&prec)
    {
        if (root != nullptr)
        {
            TreeNode *left = root->left, *right = root->right;
            if (prec != nullptr)
            {
                prec->left = nullptr;
                prec->right = root;
            }
            prec = root;
            preOrder(left, prec);
            preOrder(right, prec);
        }
    }
};
```

## 0115. Distinct Subsequences*
```cpp
/*
Input: s = "babgbag", t = "bag"
Output: 5
Explanation:
As shown below, there are 5 ways you can generate "bag" from S.
babgbag
babgbag
babgbag
babgbag
babgbag
*/
class Solution {
public:
    int numDistinct(string s, string t) {
        int ns = int(s.length()), nt = int(t.length());
        if (ns < nt) { return 0; }
        long **dp = new long *[ns+1];
        for (int i = 0; i <= ns; i++) { dp[i] = new long [nt+1]{}; }
        for (int i = ns; i >= 0; i--)
        {
            for (int j = nt; j >= 0; j--)
            {
                if (j == nt) { dp[i][j] = 1; }
                else if (i == ns) { dp[i][j] = 0; }
                else
                {
                    if (s[i] == t[j])
                    {
                        dp[i][j] = dp[i+1][j+1] + dp[i+1][j];
                    }
                    else { dp[i][j] = dp[i+1][j]; }
                }
            }
        }
        int ans = dp[0][0];
        for (int i = 0; i <= ns; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
};
```

## 0120. Triangle
```cpp
/*
   2
  3 4
 6 5 7
4 1 8 3
The minimum path sum from top to bottom
is 2 + 3 + 5 + 1 = 11 (underlined above).
*/
class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = int(triangle.size());
        if (n == 0) { return 0; }
        int **dp = new int *[n];
        for (int i = 0; i < n; i++) { dp[i] = new int [n]{}; }
        dp[0][0] = triangle[0][0];
        for (int i = 1; i < n; i++)
        {
            dp[i][0] = dp[i-1][0] + triangle[i][0];
            for (int j = 1; j < i; j++)
            {
                dp[i][j] = min(dp[i-1][j-1], dp[i-1][j]) + triangle[i][j];
            }
            dp[i][i] = dp[i-1][i-1] + triangle[i][i];
        }
        int ans = *min_element(dp[n - 1], dp[n - 1] + n);
        for (int i = 0; i < n; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
};
```

## 0121. Best Time to Buy and Sell Stock
Say you have an array for which the ith element is the price of a given stock on day i. If you were only permitted to complete at most one transaction (i.e., buy one and sell one share of the stock), design an algorithm to find the maximum profit. Note that you cannot sell a stock before you buy one.

Example:
Input: [7,1,5,3,6,4]
Output: 5
Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5. Not 7-1 = 6, as selling price needs to be larger than buying price.

Input: [7,6,4,3,1]
Output: 0
Explanation: In this case, no transaction is done, i.e. max profit = 0.

Solution:

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = int(prices.size()), maxPro = 0, minPri = (1ll << 31) - 1;
        for (int i = 0; i < n; i++)
        {
            minPri = minPri < prices[i] ? minPri : prices[i];
            maxPro = maxPro > prices[i] - minPri ? maxPro : prices[i] - minPri;
        }
        return maxPro;
    }
};
```

## 0126. Word Ladder II*
```cpp
/*
Input: beginWord = "hit", endWord = "cog",
wordList = ["hot", "dot", "dog", "lot", "log", "cog"]
Output: [
    ["hit", "hot", "dot", "dog", "cog"],
    ["hit", "hot", "lot", "log", "cog"]
]
Explanation: There are 2 shortest transformation sequences:
"hit" -> "hot" -> "dot" -> "dog" -> "cog"
"hit" -> "hot" -> "lot" -> "log" -> "cog"
*/
class Solution {
public:
    unordered_map<string, int> wordId;
    vector<string> idWord;
    vector<vector<int>> edges;
    unordered_set<string> dict;
    int numNode = 0;
    void addWord(string &word)
    {
        if (wordId.find(word) == wordId.end())
        {
            wordId[word] = numNode++;
            idWord.emplace_back(word);
            edges.emplace_back();
        }
    }
    void addEdge(string &word)
    {
        addWord(word);
        int i = wordId[word];
        for (char &c : word)
        {
            char tmp = c;
            c = '*';
            addWord(word);
            int j = wordId[word];
            edges[i].emplace_back(j);
            edges[j].emplace_back(i);
            c = tmp;
        }
    }
    void dfs(vector<vector<string>> &ans, int &maxDep, int dep,
             vector<string> &arr, int id, int &t,
             vector<int> &level)
    {
        if (dict.find(idWord[id]) != dict.end())
        {
            arr.emplace_back(idWord[id]);
        }
        if (id == t) { ans.emplace_back(arr); }
        for (int &nextId : edges[id])
        {
            if (level[nextId] > level[id])
            {
                dfs(ans, maxDep, dep+1, arr, nextId, t, level);
            }
        }
        if (dict.find(idWord[id]) != dict.end())
        {
            arr.pop_back();
        }
    }
    vector<vector<string>> findLadders(string beginWord, string endWord,
                                       vector<string>& wordList) {
        vector<vector<string>> ans;
        for (string &word : wordList)
        {
            addEdge(word);
            dict.insert(word);
        }
        if (wordId.find(endWord) == wordId.end()) { return ans; }
        if (dict.find(beginWord) == dict.end())
        {
            addEdge(beginWord);
            dict.insert(beginWord);
        }
        vector<int> level(numNode, INT_MAX);
        int s = wordId[beginWord], t = wordId[endWord];
        queue<int> qWord;
        qWord.push(s);
        level[s] = 0;
        while (!qWord.empty())
        {
            int front = qWord.front();
            qWord.pop();
            if (front == t) { break; }
            else
            {
                for (int nextId : edges[front])
                {
                    if (level[nextId] >= level[front] + 1)
                    {
                        qWord.push(nextId);
                        level[nextId] = level[front] + 1;
                    }
                }
            }
        }
        if (level[t] == INT_MAX) { return ans; }
        vector<string> arr;
        dfs(ans, level[t], 0, arr, s, t, level);
        return ans;
    }
};
```

## 0127. Word Ladder*
```cpp
/*
Input: beginWord = "hit", endWord = "cog",
wordList = "hot","dot","dog","lot","log","cog"
Output: 5
Explanation: One shortest transformation sequence
is "hit" -> "hot" -> "dot" -> "dog" -> cog", which
is 5 words long.
*/
class Solution {
public:
    unordered_map<string, int> wordId;
    vector<vector<int>> edges;
    int numNode = 0;
    void addWord(string &word)
    {
        if (wordId.find(word) == wordId.end())
        {
            wordId[word] = numNode++;
            edges.emplace_back();
        }
    }
    void addEdge(string &word)
    {
        addWord(word);
        int i = wordId[word];
        for (char &c : word)
        {
            char tmp = c;
            c = '*';
            addWord(word);
            int j = wordId[word];
            edges[i].emplace_back(j);
            edges[j].emplace_back(i);
            c = tmp;
        }
    }
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        for (string &word : wordList) { addEdge(word); }
        if (wordId.find(endWord) == wordId.end()) { return 0; }
        if (wordId.find(beginWord) == wordId.end()) { addEdge(beginWord); }
        vector<int> level(numNode, INT_MAX);
        int s = wordId[beginWord], t = wordId[endWord];
        queue<int> qWord;
        qWord.push(s);
        level[s] = 0;
        while (!qWord.empty())
        {
            int front = qWord.front();
            qWord.pop();
            if (front == t) { return level[t] / 2 + 1; }
            else
            {
                for (int nextId : edges[front])
                {
                    if (level[nextId] >= level[front] + 1)
                    {
                        qWord.push(nextId);
                        level[nextId] = level[front] + 1;
                    }
                }
            }
        }
        return 0;
    }
};
```

## 0128. Longest Consecutive Sequence*
```cpp
/*
Input: nums = [100,4,200,1,3,2]
Output: 4
Explanation: The longest consecutive elements sequence
is [1, 2, 3, 4]. Therefore its length is 4.
*/
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        if (nums.empty()) { return 0; }
        int ans = 1;
        unordered_set<int> hash;
        for (const auto &ele : nums) { hash.insert(ele); }
        for (const int &ele : nums)
        {
            if (hash.find(ele - 1) != hash.end())
            {
                int curNum = ele;
                int len = 1;
                while (hash.find(curNum) != hash.end())
                {
                    curNum++;
                    len++;
                }
                ans = max(ans, len);
            }
        }
        return ans;
    }
};
```

## 0129. Sum Root to Leaf Numbers
```cpp
/*
            4
          /   \
         9     0
        / \
       5   1
sum = 495 + 491 + 40 = 1026
*/
class Solution {
public:
    int sumNumbers(TreeNode* root) {
        int num = 0;
        return dfs(root, num);
    }
    int dfs(TreeNode *root, int num) {
        if (root == nullptr) { return 0; }
        if (root->left == nullptr && root->right == nullptr) {
            return num * 10 + root->val;;
        }
        int left = dfs(root->left, num * 10 + root->val);
        int right = dfs(root->right, num * 10 + root->val);
        return left + right;
    }
};
```

## 0130. Surrounded Regions*
```cpp
/*
X   X   X   X               X   X   X   X
X   O   O   X       ->      X   X   X   X
X   X   O   X               X   X   X   X
X   O   X   X               X   O   X   X
*/
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        int nRow = (int)board.size();
        if (nRow <= 2) { return; }
        int nCol = (int)board[0].size();
        if (nCol <= 2) { return; }
        for (int i = 0; i < nRow; i++) {
            if (board[i][0] == 'O') { dfs(board, nRow, nCol, i, 0); }
            if (board[i][nCol-1] == 'O') {
                dfs(board, nRow, nCol, i, nCol-1);
            }
        }
        for (int j = 0; j < nCol; j++) {
            if (board[0][j] == 'O') { dfs(board, nRow, nCol, 0, j); }
            if (board[nRow-1][j] == 'O') {
                dfs(board, nRow, nCol, nRow-1, j);
            }
        }
        for (int i = 0; i < nRow; i++) {
            for (int j = 0; j < nCol; j++) {
                if (board[i][j] == 'O') { board[i][j] = 'X'; }
                if (board[i][j] == 'A') { board[i][j] = 'O'; }
            }
        }
    }
    void dfs(vector<vector<char>> &board, int nRow, int nCol,
             int i, int j) {
        if (i < 0 || i >= nRow || j < 0 || j >= nCol ||
            board[i][j] == 'A' || board[i][j] == 'X') { return; }
        board[i][j] = 'A';
        dfs(board, nRow, nCol, i+1, j);
        dfs(board, nRow, nCol, i-1, j);
        dfs(board, nRow, nCol, i, j+1);
        dfs(board, nRow, nCol, i, j-1);
    }
};
```

## 0131. Palindrome Partitioning
```cpp
/*
Input: s = "aab"
Output: [["a","a","b"],["aa","b"]]
*/
class Solution {
public:
    vector<vector<string>> partition(string s) {
        vector<vector<string>> ans;
        int n = (int)s.length();
        if (n == 0) { return ans; }
        bool **dp = new bool *[n];
        for (int i = 0; i < n; i++)
        {
            dp[i] = new bool [n]{};
            dp[i][i] = true;
        }
        for (int i = 0; i < n-1; i++)
        {
            if (s[i] == s[i+1]) { dp[i][i+1] = true; }
        }
        for (int k = 2; k < n; k++)
        {
            for (int i = 0; i < n-k; i++)
            {
                int j = i + k;
                if (s[i] == s[j] && dp[i+1][j-1]) { dp[i][j] = true; }
            }
        }
        vector<string> arr;
        move(ans, arr, dp, n, 0, s);
        for (int i = 0; i < n; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
    void move(vector<vector<string>> &ans, vector<string> &arr,
              bool **dp, int n, int pos, string &s)
    {
        if (pos >= n)
        {
            if (!arr.empty()) { ans.emplace_back(arr); }
            return;
        }
        for (int len = 0; len < n - pos; len++)
        {
            if (dp[pos][pos+len])
            {
                arr.emplace_back(s.substr(pos, len+1));
                move(ans, arr, dp, n, pos+len+1, s);
                arr.pop_back();
            }
        }
    }
};
```

## 0132. Palindrome Partitioning II*
```cpp
/*
Input: s = "aab"
Output: 1
Explanation: The palindrome partitioning ["aa","b"]
could be produced using 1 cut.
*/
class Solution {
public:
    int minCut(string s) {
        int n = int(s.length());
        if (n < 2) { return 0; }
        bool **dp = new bool *[n];
        int *cut = new int [n]{};
        for (int i = 0; i < n; i++)
        {
            dp[i] = new bool [n]{};
            dp[i][i] = true;
        }
        for (int i = 0; i < n-1; i++)
        {
            if (s[i] == s[i+1]) { dp[i][i+1] = true; }
        }
        for (int k = 2; k < n; k++)
        {
            for (int i = 0; i < n - k; i++)
            {
                int j = i + k;
                if (s[i] == s[j] && dp[i+1][j-1]) { dp[i][j] = true; }
            }
        }
        for (int j = 1; j < n; j++)
        {
            if (dp[0][j])
            {
                cut[j] = 0;
                continue;
            }
            int cutNum = INT_MAX;
            for (int i = j; i > 0; i--)
            {
                if (dp[i][j] && cutNum > cut[i-1] + 1)
                {
                    cutNum = cut[i-1] + 1;
                }
            }
            cut[j] = cutNum;
        }
        int ans = cut[n-1];
        for (int i = 0; i < n; i++)
        {
            delete []dp[i];
        }
        delete []dp;
        delete []cut;
        return ans;
    }
};
```

## 0136. Single Number
```cpp
/*
Input: nums = [4,1,2,1,2]
Output: 4
*/
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int ans = 0;
        for (const int &ele : nums) {
            ans ^= ele;
        }
        return ans;
    }
};
```

## 0137. 只出现一次的数字 II
```cpp
/*
给你一个整数数组 nums ，除某个元素仅出现 一次 外，其余每个元素都
恰出现 三次 。请你找出并返回那个只出现了一次的元素。

示例 1：
输入：nums = [2,2,3,2]
输出：3

示例 2：
输入：nums = [0,1,0,1,0,1,99]
输出：99
*/
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        unordered_map<int, int> dict;
        for (int ele : nums) {
            dict[ele]++;
        }
        unordered_map<int, int>::iterator iter = dict.begin();
        for (; iter != dict.end(); iter++) {
            if (iter->second == 1) {
                return iter->first;
            }
        }
        return -1;
    }
};
```

## 0142. Linked List Cycle II
```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        if (head == NULL) { return head; }
        ListNode *p1 = head, *p2 = head;
        while (true)
        {
            if (p2 == NULL) { return NULL; }
            p2 = p2->next;
            if (p2 == NULL) { return NULL; }
            p2 = p2->next;
            p1 = p1->next;
            if (p1 == p2) { break; }
        }
        p1 = head;
        while (p1 != p2)
        {
            p2 = p2->next;
            p1 = p1->next;
        }
        return p1;
    }
};
```

## 0144. Binary Tree Preorder Traversal
```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> ans;
        if (root == nullptr) { return ans; }
        TreeNode *curNode = root;
        stack<TreeNode*> st;
        st.push(root);
        while (!st.empty())
        {
            curNode = st.top();
            st.pop();
            ans.push_back(curNode->val);
            if (curNode->right != nullptr) { st.push(curNode->right); }
            if (curNode->left != nullptr) { st.push(curNode->left); }
        }
        return ans;
    }
};

class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> ans;
        if (root == nullptr) { return ans; }
        TreeNode *curNode = root;
        stack<TreeNode*> st;
        while (true)
        {
            while (curNode != nullptr)
            {
                ans.push_back(curNode->val);
                if (curNode->right != nullptr) { st.push(curNode->right); }
                curNode = curNode->left;
            }
            if (st.empty()) { break; }
            curNode = st.top();
            st.pop();
        }
        return ans;
    }
};
```

## 0150. Evaluate Reverse Polish Notation
```cpp
/*
Input: tokens = [
    "10","6","9","3","+","-11","*","/","*","17","+","5","+"    
]
Output: 22
Explanation: ((10 * (6 / ((9 + 3) * -11))) + 17) + 5
= ((10 * (6 / (12 * -11))) + 17) + 5
= ((10 * (6 / -132)) + 17) + 5
= ((10 * 0) + 17) + 5
= (0 + 17) + 5
= 17 + 5
= 22
*/
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        stack<int> stk;
        int num1 = 0, num2 = 0;
        for (string &token : tokens)
        {
            if (token == "+")
            {
                num2 = stk.top();
                stk.pop();
                num1 = stk.top();
                stk.pop();
                stk.push(num1 + num2);
            }
            else if (token == "-")
            {
                num2 = stk.top();
                stk.pop();
                num1 = stk.top();
                stk.pop();
                stk.push(num1 - num2);
            }
            else if (token == "*")
            {
                num2 = stk.top();
                stk.pop();
                num1 = stk.top();
                stk.pop();
                stk.push(num1 * num2);
            }
            else if (token == "/")
            {
                num2 = stk.top();
                stk.pop();
                num1 = stk.top();
                stk.pop();
                stk.push(num1 / num2);
            }
            else { stk.push(stringToInt(token)); }
            
        }
        return stk.top();
    }
    int stringToInt(string num)
    {
        int n = int(num.length());
        int ans = 0;
        if (num[0] != '-') { ans = num[0] - '0'; }
        for (int i = 1; i < n; i++)
        {
            ans = ans * 10 + (num[i] - '0');
        }
        if (num[0] == '-') { ans = -ans; }
        return ans;
    }
};
```

## 0151. Reverse Words in a String
```cpp
/*
Input: s = "  hello world  "
Output: "world hello"
Explanation: Your reversed string should
not contain leading or trailing spaces.
*/
class Solution {
public:
    string reverseWords(string s) {
        int pos = 0;
        for (int i = 0; i < (int)s.length(); i++, pos++)
        {
            if (s[i] != ' ') { break; }
        }
        string str = "";
        for (int i = pos; i < (int)s.length(); i++)
        {
            if (i > 0 && s[i] == ' ' && s[i-1] == ' ') { continue; }
            str.push_back(s[i]);
        }
        int n = int(str.length());
        if (str[n-1] == ' ') { n--; }
        str.resize(n);
        for (int i = 0; i < n / 2; i++) { swap(str[i], str[n-1-i]); }
        str.push_back(' ');
        for (int i = 0, j = 0; i <= n; i++)
        {
            if (str[i] == ' ')
            {
                int b = j, e = i - 1;
                while (b < e) { swap(str[b++], str[e--]); }
                j = i + 1;
            }
        }
        str.resize(n);
        return str;
    }
};
```

## 0153. Find Minimum in Rotated Sorted Array
```cpp
/*
Input: nums = [3,4,5,1,2]
Output: 1
*/
class Solution {
public:
    int findMin(vector<int>& nums) {
        int l = 0, r = int(nums.size()) - 1;
        while (l <= r)
        {
            if (nums[l] < nums[r]) { return nums[l]; }
            int m = (l + r) >> 1;
            if (nums[m] >= nums[l]) { l = m + 1; }
            else { r = m; }
        }
        return nums[r];
    }
};
```

## 0154. Find Minimum in Rotated Sorted Array II
```cpp
/*
Input: nums = [2,2,2,0,1]
Output: 0
*/
class Solution {
public:
    int findMin(vector<int>& nums) {
        int n = int(nums.size());
        int l = 0, r = n - 1;
        while (l <= r)
        {
            if (nums[l] < nums[r]) { return nums[l]; }
            int m = (l + r) >> 1;
            if (nums[l] == nums[r])
            {
                while (l < r && nums[l] == nums[l + 1]) { l++; }
                while (l < r && nums[r] == nums[r - 1]) { r--; }
                if ((l + 1 <= r && nums[l] < nums[l + 1] &&
                     nums[r] < nums[r - 1]) || l == r) { return nums[l]; }
                l++;
            }
            else
            {
                if (nums[m] >= nums[l]) { l = m + 1; }
                else { r = m; }
            }
        }
        return nums[r];
    }
};
```

## 0155. Min Stack
```cpp
/*
Input
["MinStack","push","push","push","getMin","pop","top","getMin"]
[[],[-2],[0],[-3],[],[],[],[]]

Output
[null,null,null,null,-3,null,0,-2]

Explanation
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin(); // return -3
minStack.pop();
minStack.top();    // return 0
minStack.getMin(); // return -2
*/
class MinStack {
    vector<int> arr1, arr2;
public:
    /** initialize your data structure here. */
    MinStack() {

    }
    
    void push(int val) {
        arr1.emplace_back(val);
        if (arr2.empty() || arr2.back() > val) {
            arr2.emplace_back(val);
        }
        else { arr2.emplace_back(arr2.back()); }
    }
    
    void pop() {
        arr1.pop_back();
        arr2.pop_back();
    }
    
    int top() {
        return arr1.back();
    }
    
    int getMin() {
        return arr2.back();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(val);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```

## 0160. Intersection of Two Linked Lists*
```cpp
/*
A: 4 -> 2 -> 8 -> 4 -> 5
B: 5 -> 6 -> 1 -> 8 -> 4 -> 5
*/
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if (headA == nullptr || headB == nullptr) { return nullptr; }
        if (headA == headB) { return headA; }
        ListNode *p1 = headA, *p2 = headB;
        while (p1 != nullptr && p2 != nullptr)
        {
            if (p1 == p2) { return p1; }
            p1 = p1->next;
            p2 = p2->next;
        }
        if (p1 == nullptr) { p1 = headB; }
        if (p2 == nullptr) { p2 = headA; }
        while (p1 != nullptr && p2 != nullptr)
        {
            if (p1 == p2) { return p1; }
            p1 = p1->next;
            p2 = p2->next;
        }
        if (p1 == nullptr) { p1 = headB; }
        if (p2 == nullptr) { p2 = headA; }
        while (p1 != nullptr && p2 != nullptr)
        {
            if (p1 == p2) { return p1; }
            p1 = p1->next;
            p2 = p2->next;
        }
        return nullptr;
    }
};
```

## 0173. Binary Search Tree Iterator
```cpp
/*
Input
["BSTIterator", "next", "next", "hasNext", "next",
"hasNext", "next", "hasNext", "next", "hasNext"]
[[[7, 3, 15, null, null, 9, 20]], [], [], [], [], [], [], [], [], []]
Output
[null, 3, 7, true, 9, true, 15, true, 20, false]

Explanation
BSTIterator bSTIterator = new BSTIterator([7, 3, 15, null, null, 9, 20]);
bSTIterator.next();    // return 3
bSTIterator.next();    // return 7
bSTIterator.hasNext(); // return True
bSTIterator.next();    // return 9
bSTIterator.hasNext(); // return True
bSTIterator.next();    // return 15
bSTIterator.hasNext(); // return True
bSTIterator.next();    // return 20
bSTIterator.hasNext(); // return False
*/
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class BSTIterator {
    vector<int> arr;
    vector<int> ::iterator iter;
public:
    BSTIterator(TreeNode* root) {
        inOrder(root);
        iter = arr.begin();
    }
    void inOrder(TreeNode *root)
    {
        if (root != nullptr)
        {
            inOrder(root->left);
            arr.emplace_back(root->val);
            inOrder(root->right);
        }
    }
    int next() {
        if (hasNext()) { return *iter++; }
        else { return 0; }
    }
    
    bool hasNext() {
        return iter != arr.end();
    }
};

/**
 * Your BSTIterator object will be instantiated and called as such:
 * BSTIterator* obj = new BSTIterator(root);
 * int param_1 = obj->next();
 * bool param_2 = obj->hasNext();
 */
```

## 0174. Dungeon Game*
The demons had captured the princess (P) and imprisoned her in the bottom-right corner of a dungeon. The dungeon consists of M x N rooms laid out in a 2D grid. Our valiant knight (K) was initially positioned in the top-left room and must fight his way through the dungeon to rescue the princess. The knight has an initial health point represented by a positive integer. If at any point his health point drops to 0 or below, he dies immediately. Some of the rooms are guarded by demons, so the knight loses health (negative integers) upon entering these rooms; other rooms are either empty (0's) or contain magic orbs that increase the knight's health (positive integers). In order to reach the princess as quickly as possible, the knight decides to move only rightward or downward in each step.

```cpp
/*
-2(K)  -3    3
-5     -10   1
10     30   -5(P)
For example, given the dungeon below, the initial health of the
knight must be at least 7 if he follows the optimal path
RIGHT-> RIGHT -> DOWN -> DOWN.
*/
class Solution {
public:
    int calculateMinimumHP(vector<vector<int>>& dungeon) {
        int m = int(dungeon.size());
        if (m == 0) { return 1; }
        int n = int(dungeon[0].size());
        if (n == 0) { return 1; }
        vector<vector<int>> dp (m+1, vector<int>(n+1, INT_MAX));
        dp[m][n-1] = dp[m-1][n] = 1;
        for (int i = m-1; i >= 0; i--)
        {
            for (int j = n-1; j >= 0; j--)
            {
                int minVal = min(dp[i+1][j], dp[i][j+1]);
                dp[i][j] = max(minVal - dungeon[i][j], 1);
            }
        }
        int ans = dp[0][0];
        return ans;
    }
};
```

## 0179. Largest Number*
```cpp
/*
Input: nums = [3,30,34,5,9]
Output: "9534330"
*/
class Solution {
public:
    string largestNumber(vector<int>& nums) {
        string ans = "";
        if (nums.empty()) { return ans; }
        sort(nums.begin(), nums.end(), [](const int &x, const int &y) {
            long i = 10, j = 10;
            while (i <= x) { i *= 10; }
            while (j <= y) { j *= 10; }
            return j * x + y > i * y + x;
        });
        if (nums[0] == 0) { return "0"; }
        for (const int &ele : nums) { ans += to_string(ele); }
        return ans;
    }
};
```

## 0190. Reverse Bits*
```cpp
/*
Input: n = 00000010100101000001111010011100
Output:    964176192 (00111001011110000010100101000000)
Explanation: The input binary string 00000010100101000001111010011100
represents the unsigned integer 43261596, so return 964176192 which its
binary representation is 00111001011110000010100101000000.
*/
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        uint32_t ans = 0;
        for (int i = 0; i < 32; i++)
        {
            ans |= (n & 1) << (31 - i);
            n = n >> 1;
        }
        return ans;
    }
};
class Solution {
public:
    uint32_t reverseBits(uint32_t n) {
        const uint32_t M1 = 0x55555555; // 01010101010101010101010101010101
        const uint32_t M2 = 0x33333333; // 00110011001100110011001100110011
        const uint32_t M4 = 0x0f0f0f0f; // 00001111000011110000111100001111
        const uint32_t M8 = 0x00ff00ff; // 00000000111111110000000011111111
        n = (n >> 1 & M1) | (n & M1) << 1;
        n = (n >> 2 & M2) | (n & M2) << 2;
        n = (n >> 4 & M4) | (n & M4) << 4;
        n = (n >> 8 & M8) | (n & M8) << 8;
        return n >> 16 | n << 16;
    }
};
```

## 0191. Number of 1 Bits
```cpp
/*
Input: n = 00000000000000000000000000001011
Output: 3
Explanation: The input binary string 00000000000000000000000000001011
has a total of three '1' bits.
*/
class Solution {
public:
    int hammingWeight(uint32_t n) {
        int ans = 0;
        while (n)
        {
            n &= n - 1;
            ans++;
        }
        return ans;
        //return __builtin_popcount(n);
    }
};
```

## 0198. House Robber
```cpp
/*
Input: nums = [2,7,9,3,1]
Output: 12
Explanation: Rob house 1 (money = 2), rob house 3 (money = 9) and
rob house 5 (money = 1). Total amount you can rob = 2 + 9 + 1 = 12.
*/class Solution {
public:
    int rob(vector<int>& nums) {
        int n = int(nums.size());
        if (n == 0) { return 0; }
        vector<vector<int>> dp(n, vector<int>(2, 0));
        dp[0][1] = nums[0];
        for (int i = 1; i < n; i++)
        {
            dp[i][0] = max(dp[i-1][0], dp[i-1][1]);
            dp[i][1] = dp[i-1][0] + nums[i];
        }
        return max(dp[n-1][0], dp[n-1][1]);
    }
};

```

## 0199. Binary Tree Right Side View
```cpp
/*
   1            <---
 /   \
2     3         <---
 \     \
  5     4       <---
*/
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> ans;
        if (root == nullptr) { return ans; }
        int maxLevel = -1;
        rightOrder(root, ans, 0, maxLevel);
        return ans;
    }
    void rightOrder(TreeNode *root, vector<int> &ans, int level, int &maxLevel)
    {
        if (root != nullptr)
        {
            if (level > maxLevel)
            {
                ans.emplace_back(root->val);
                maxLevel = level;
            }
            rightOrder(root->right, ans, level+1, maxLevel);
            rightOrder(root->left, ans, level+1, maxLevel);
        }
    }
};
```

## 0200. Number of Islands
```cpp
/*
grid = {
    {'1', '1', '0', '0', '0'},
    {'1', '1', '0', '0', '0'},
    {'0', '0', '1', '0', '0'},
    {'0', '0', '0', '1', '1'}
}
number of islands is 3
*/
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int ans = 0;
        int nRow = int(grid.size());
        if (nRow == 0) { return 0; }
        int nCol = int(grid[0].size());
        if (nCol == 0) { return 0; }
        for (int i = 0; i < nRow; i++)
        {
            for (int j = 0; j < nCol; j++)
            {
                if (grid[i][j] == '0') { continue; }
                if (isIsland(grid, nRow, nCol, i, j)) { ans++; }
            }
        }
        return ans;
    }
    bool isIsland(vector<vector<char>> &grid, int nRow, int nCol,
              int i, int j)
    {
        if (i < 0 || i >= nRow || j < 0 || j >= nCol ||
            grid[i][j] == '0') { return false; }
        bool res = true;
        grid[i][j] = '0';
        bool u = isIsland(grid, nRow, nCol, i-1, j);
        bool d = isIsland(grid, nRow, nCol, i+1, j);
        bool l = isIsland(grid, nRow, nCol, i, j-1);
        bool r = isIsland(grid, nRow, nCol, i, j+1);
        return res || u || d || l || r;
    }
};
```

## 0201. Bitwise AND of Numbers Range*
```cpp
/*
Input: left = 5, right = 7
Output: 4
*/
class Solution {
public:
    int rangeBitwiseAnd(int left, int right) {
        while (left < right)
        {
            right &= right - 1;
        }
        return right;
    }
};
```

## 0209. Minimum Size Subarray Sum
Given an array of positive integers nums and a positive integer target, return the minimal length of a contiguous subarray [numsl, numsl+1, ..., numsr-1, numsr] of which the sum is greater than or equal to target. If there is no such subarray, return 0 instead.

```cpp
/*
Input: target = 7, nums = [2,3,1,2,4,3]
Output: 2
*/
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = int(nums.size());
        if (n == 0) { return 0; }
        int minLen = INT_MAX, l = 0, r = 0, sum = nums[0];
        while (r < n && l <= r)
        {
            if (sum < target)
            {
                if (r + 1 < n) { sum += nums[r+1]; }
                r++;
            }
            else
            {
                if (minLen > r - l + 1) { minLen = r - l + 1; }
                sum -= nums[l++];
            }
        }
        return minLen == INT_MAX ? 0 : minLen;
    }
};
```

## 0212. Word Search II
```cpp
/*
board = 
o   a   a   n
e   t   a   e
i   h   k   r
i   f   l   v
words = "oath","pea","eat","rain"
Output: 
"eat","oath"
*/
class Solution {
public:
    vector<string> findWords(vector<vector<char>>& board,
                             vector<string>& words) {
        vector<string> ans;
        int nRow = int(board.size());
        if (nRow == 0) { return ans; }
        int nCol = int(board[0].size());
        if (nCol == 0) { return ans; }
        unordered_set<string> hash;
        unordered_set<string> res;
        int maxLen = 0;
        for (string &word : words)
        {
            hash.insert(word);
            maxLen = max(maxLen, int(word.length()));
        }
        
        for (int i = 0; i < nRow; i++)
        {
            for (int j = 0; j < nCol; j++)
            {
                bool *vis = new bool [nRow * nCol]{};
                string str = "";
                collect(board, str, nRow, nCol, i, j, 0,
                        maxLen, hash, res, vis);
                delete []vis;
            }
        }
        unordered_set<string>::iterator iter;
        for (iter = res.begin(); iter != res.end(); iter++)
        {
            ans.emplace_back(*iter);
        }
        return ans;
    }
    void collect(vector<vector<char>> &board,
                 string &str,
                 int nRow, int nCol,
                 int i, int j, int len,
                 int maxLen,
                 unordered_set<string> &hash,
                 unordered_set<string> &res,
                 bool *vis)
    {
        if (i < 0 || i >= nRow || j < 0 || j >= nCol ||
            len > maxLen || vis[i*nCol + j]) { return; }
        str.push_back(board[i][j]);
        vis[i*nCol+j] = true;
        if (hash.find(str) != hash.end() &&
            res.find(str) == res.end())
        {
            res.insert(str);
        }
        collect(board, str, nRow, nCol, i-1, j, len+1, maxLen, hash, res, vis);
        collect(board, str, nRow, nCol, i+1, j, len+1, maxLen, hash, res, vis);
        collect(board, str, nRow, nCol, i, j-1, len+1, maxLen, hash, res, vis);
        collect(board, str, nRow, nCol, i, j+1, len+1, maxLen, hash, res, vis);
        str.pop_back();
        vis[i*nCol+j] = false;
    }
};
```

## 0213. House Robber II*
```cpp
/*
Input: nums = [2,3,2]
Output: 3
Explanation: You cannot rob house 1 (money = 2) and
then rob house 3 (money = 2), because they are
adjacent houses
*/
class Solution {
public:
    int rob(vector<int>& nums) {
        int n = int(nums.size());
        if (n == 0) { return 0; }
        if (n == 1) { return nums[0]; }
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
    }
};
```

## 0215. Kth Largest Element in an Array
```cpp
/*
Input: nums = [3,2,3,1,2,4,5,5,6], k = 4
Output: 4
*/
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int n = int(nums.size());
        k = n - k;
        int b = 0, e = n - 1;
        while (true)
        {
            int i = b, j = e;
            while (i < j)
            {
                while (i < j && nums[i] <= nums[j]) { j--; }
                swap(nums[i], nums[j]);
                while (i < j && nums[i] <= nums[j]) { i++; }
                swap(nums[i], nums[j]);
            }
            if (i == k) { return nums[i]; }
            else if (i > k) { e = i - 1; }
            else { b = i + 1; }
        }
        return 0;
    }
};
```

## 0216. Combination Sum III
```cpp
/*
Input: k = 3, n = 9
Output: [[1,2,6],[1,3,5],[2,3,4]]
Explanation:
1 + 2 + 6 = 9
1 + 3 + 5 = 9
2 + 3 + 4 = 9
There are no other valid combinations.
*/
class Solution {
public:
    vector<vector<int>> combinationSum3(int k, int n) {
        vector<vector<int>> ans;
        if (k == 0 || n > 45) { return ans; }
        vector<int> arr;
        dfs(ans, arr, k, n, 1);
        return ans;
    }
    void dfs(vector<vector<int>> &ans, vector<int> &arr,
             int k, int n, int idx) {
        if (k <= 0 || n <= 0 || idx > 9) {
            if (k == 0 && n == 0) { ans.emplace_back(arr); }
            return;
        }
        for (int i = idx; i < 10; i++) {
            arr.emplace_back(i);
            dfs(ans, arr, k-1, n-i, i+1);
            arr.pop_back();
        }
    }
};
```

## 0217. Contains Duplicate
```cpp
/*
Input: nums = [1,2,3,4]
Output: false
*/
class Solution {
public:
    bool containsDuplicate(vector<int>& nums) {
        set<int> hash;
        for (const int &ele : nums) {
            if (hash.find(ele) != hash.end()) { return true; }
            hash.insert(ele);
        }
        return false;
    }
};
```

## 0219. Contains Duplicate II
```cpp
/*
Input: nums = [1,2,3,1,2,3], k = 2
Output: false
*/
class Solution {
public:
    bool containsNearbyDuplicate(vector<int>& nums, int k) {
        int n = (int)nums.size();
        if (n <= 1) { return false; }
        set<int> hash;
        for (int i = 0; i < n; i++) {
            if (hash.find(nums[i]) != hash.end()) { return true; }
            hash.insert(nums[i]);
            if (i >= k) { hash.erase(nums[i-k]); }
        }
        return false;
    }
};
```

## 0220. Contains Duplicate III*
```cpp
/*
Input: nums = [1,5,9,1,5,9], k = 2, t = 3
Output: false
*/
class Solution {
public:
    bool containsNearbyAlmostDuplicate(vector<int>& nums, int k, int t) {
        int n = (int)nums.size();
        if (n <= 1) { return false; }
        set<int> hash;
        set<int>::iterator iter;
        for (int i = 0; i < n; i++) {
            iter = hash.lower_bound(max(nums[i], INT_MIN + t) - t);
            if (iter != hash.end() &&
                *iter <= min(nums[i], INT_MAX - t) + t) {
                return true;
            }
            hash.insert(nums[i]);
            if (i >= k) { hash.erase(nums[i-k]); }
        }
        return false;
    }
};
```

## 0222. 完全二叉树的节点个数*
```cpp
/*
            1
        /       \
       2         3
     /   \      /
    4     5    6
*/
class Solution {
public:
    int countNodes(TreeNode* root) {
        int ans = 0;
        if (root == nullptr) { return ans; }
        TreeNode *curNode = root;
        while (curNode != nullptr) {
            TreeNode *ll = curNode->left, *lr = curNode->left;
            TreeNode *rl = curNode->right, *rr = curNode->right;
            int lCnt = 1, rCnt = 1;
            while (ll != nullptr && lr != nullptr &&
                   rl != nullptr && rr != nullptr) {
                ll = ll->left;
                lr = lr->right;
                rl = rl->left;
                rr = rr->right;
                lCnt <<= 1;
                rCnt <<= 1;
            }
            if (ll == nullptr && rl == nullptr) {
                ans += lCnt - 1 + rCnt - 1 + 1;
                return ans;
            }
            if (lr != nullptr && rl == nullptr) {
                ans += (lCnt * 2) - 1 + rCnt -1 + 1;
                return ans;
            }
            if (lr == nullptr) {
                ans += rCnt - 1 + 1;
                curNode = curNode->left;
            }
            else {
                ans += lCnt * 2 - 1 + 1;
                curNode = curNode->right;
            }
        }
        return ans;
    }
};
```

## 0224. Basic Calculator
```cpp
/*
Input: s = "(1+(4+5+2)-3)+(6+8)"
Output: 23
*/
class Solution {
public:
    int calculate(string s) {
        stack<int> sn;
        stack<char> so;
        int num = 0, n = int(s.length()), i = n-1;
        while (i >= 0)
        {
            if (s[i] == ' ') { i--; continue; }
            if (s[i] == ')') { so.push(')'); }
            else if (s[i] == '(')
            {
                while (so.top() != ')')
                {
                    int n1 = sn.top();
                    sn.pop();
                    int n2 = sn.top();
                    sn.pop();
                    if (so.top() == '+') { n1 += n2; }
                    else if (so.top() == '-') { n1 -= n2; }
                    else { printf("wrong operator: %c\n", so.top()); }
                    so.pop();
                    sn.push(n1);
                }
                so.pop();
            }
            else if (s[i] == '+' || s[i] == '-') { so.push(s[i]); }
            else
            {
                long times = 1;
                while (i >= 0 && s[i] >= '0' && s[i] <= '9')
                {
                    num += (s[i--] - '0') * times;
                    times *= 10;
                }
                sn.push(num);
                //printf("num=%d\n", num);
                num = 0;
                continue;
            }
            i--;
        }
        if (s[0] == '-') { sn.push(0); }
        while (!so.empty())
        {
            int n1 = sn.top();
            sn.pop();
            if (sn.empty())
            {
                sn.push(n1);
                break;
            }
            int n2 = sn.top();
            sn.pop();
            if (so.top() == '+') { n1 += n2; }
            else if (so.top() == '-') { n1 -= n2; }
            else { printf("%c\n", so.top()); }
            so.pop();
            sn.push(n1);
        }

        if (!so.empty() && so.top() == '-') { return -sn.top(); }
        return sn.top();
    }
};
```

## 0227. Basic Calculator II
```cpp
/*
Input: s = " 3+5 / 2 "
Output: 5
*/
class Solution {
public:
   int calculate(string s) {
        stack<int> sn;
        stack<char> so;
        int n = int(s.length()), i = n-1;
        long num = 0;
        while (i >= 0)
        {
            if (s[i] == ' ') { i--; continue; }
            if (s[i] == '*' || s[i] == '/') { so.push(s[i]); }
            else if (s[i] == '+' || s[i] == '-')
            {
                while (!so.empty() && (so.top() == '*' || so.top() == '/'))
                {
                    int n1 = sn.top();
                    sn.pop();
                    int n2 = sn.top();
                    sn.pop();
                    if (so.top() == '*') { n1 *= n2; }
                    else { n1 /= n2; }
                    sn.push(n1);
                    so.pop();
                }
                so.push(s[i]);
            }
            else
            {
                long times = 1;
                while (i >= 0 && s[i] >= '0' && s[i] <= '9')
                {
                    num += (s[i] - '0') * times;
                    times *= 10;
                    i--;
                }
                sn.push(int(num));
                num = 0;
                continue;
            }
            i--;
        }
        while (!so.empty())
        {
            int n1 = sn.top();
            sn.pop();
            if (sn.empty())
            {
                sn.push(n1);
                break;
            }
            int n2 = sn.top();
            sn.pop();
            if (so.top() == '+') { n1 += n2; }
            else if (so.top() == '-') { n1 -= n2; }
            else if (so.top() == '*') { n1 *= n2; }
            else if (so.top() == '/') { n1 /= n2; }
            else { printf("wrong operator %c\n", so.top()); }
            so.pop();
            sn.push(n1);
        }
        if (!so.empty() && so.top() == '-') { return -sn.top(); }
        return sn.top();
    }
};
```

## 0230. Kth Smallest Element in a BST
Given the root of a binary search tree, and an integer k, return the kth (1-indexed) smallest element in the tree.

```cpp
/*
     3
   /   \
  1     4
   \
    2
k = 1, kth min = 1
*/
class Solution {
public:
    int kthSmallest(TreeNode* root, int &k) {
        if (root != nullptr)
        {
            int left = kthSmallest(root->left, k);
            if (left >= 0) { return left; }
            if (k == 1) { return root->val; }
            k--;
            int right = kthSmallest(root->right, k);
            if (right >= 0) { return right; }
        }
        return -1;
    }
};
```

## 0232. Implement Queue using Stacks
```cpp
/*
Input
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
Output
[null, null, null, 1, 1, false]

Explanation
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2] (leftmost is front of the queue)
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
*/

// s1 is input stack, s2 is output stack
class MyQueue {
    stack<int> s1, s2;
public:
    /** Initialize your data structure here. */
    MyQueue() {

    }
    
    /** Push element x to the back of queue. */
    void push(int x) {
        s1.push(x);
    }
    
    /** Removes the element from in front of queue and returns that element. */
    int pop() {
        int ans = peek();
        s2.pop();
        return ans;
    }
    
    /** Get the front element. */
    int peek() {
        if (s2.empty())
        {
            while (!s1.empty())
            {
                s2.push(s1.top());
                s1.pop();
            }
        }
        return s2.top();
    }
    
    /** Returns whether the queue is empty. */
    bool empty() {
        return s1.empty() && s2.empty();
    }
};
```

## 0236. Lowest Common Ancestor of a Binary Tree
```cpp
/*
                3
            /       \
           5         1
         /   \     /   \
        6     2   0     8
             / \
            7   4
p = 5,  q = 1
nearest common ancester is 3
*/
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        vector<TreeNode*> t1, t2, tmp;
        dfs(root, p, tmp, t1);
        tmp.clear();
        dfs(root, q, tmp, t2);
        int n1 = (int)t1.size(), n2 = (int)t2.size();
        int i = 0, j = 0;
        while (i < n1 && j < n2) {
            if (t1[i] != t2[j]) { break; }
            i++;
            j++;
        }
        if (i == n1) { return t1[i-1]; }
        if (j == n2) { return t2[j-1]; }
        return t1[i-1];
    }
    void dfs(TreeNode *root, TreeNode *&node,
             vector<TreeNode*> &t, vector<TreeNode*> &res) {
        if (root == nullptr) { return; }
        t.emplace_back(root);
        if (root == node) {
            res = t;
            return;
        }
        dfs(root->left, node, t, res);
        dfs(root->right, node, t, res);
        t.pop_back();
    }
};
```

## 0239. Sliding Window Maximum
```cpp
/*
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation: 
Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
*/
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> ans;
        multiset<int> hash;
        int n = (int)nums.size();
        for (int i = 0; i < k; i++) {
            hash.insert(nums[i]);
        }
        for (int i = k; i < n; i++) {
            ans.emplace_back(*--hash.end());
            hash.erase(hash.find(nums[i-k]));
            hash.insert(nums[i]);
        }
        ans.emplace_back(*--hash.end());
        return ans;
    }
};
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> ans;
        int n = (int)nums.size();
        priority_queue<pair<int, int>> pq;
        for (int i = 0; i < k; i++) {
            pq.emplace(nums[i], i);
        }
        for (int i = k; i <= n; i++) {
            while (pq.top().second + k < i) {
                pq.pop();
            }
            ans.emplace_back(pq.top().first);
            if (i < n) { pq.emplace(nums[i], i); }
        }
        return ans;
    }
};
```

## 0257. Binary Tree Paths
Given a binary tree, return all root-to-leaf paths. Note: A leaf is a node with no children.

```cpp
/*
Input:
   1
 /   \
2     3
 \
  5
Output: ["1->2->5", "1->3"]
Explanation: All root-to-leaf paths are: 1->2->5, 1->3
*/
class Solution {
public:
    vector<string> binaryTreePaths(TreeNode* root) {
        vector<int> arr;
        vector<string> ans;
        if (root == nullptr) { return ans; }
        getPaths(root, ans, arr);
        return ans;
    }
    void getPaths(TreeNode* root, vector<string> &ans,
                  vector<int> &arr)
    {
        if (root == nullptr) { return; }
        if (root->left == nullptr && root->right == nullptr)
        {
            string str = "";
            for (int ele : arr) { str += to_string(ele) + "->"; }
            str += to_string(root->val);
            ans.emplace_back(str);
            return;
        }
        arr.emplace_back(root->val);
        getPaths(root->left, ans, arr);
        getPaths(root->right, ans, arr);
        arr.pop_back();
    }
};
```

## 0263. Ugly Number
```cpp
/*
Input: n = 6
Output: true
Explanation: 6 = 2 × 3
*/
class Solution {
public:
    bool isUgly(int n) {
        if (n <= 0) { return false; }
        while (n % 2 == 0) { n /= 2; }
        while (n % 3 == 0) { n /= 3; }
        while (n % 5 == 0) { n /= 5; }
        return n == 1;
    }
};
```

## 0264. Ugly Number II
```cpp
/*
Input: n = 10
Output: 12
Explanation: [1, 2, 3, 4, 5, 6, 8, 9, 10, 12]
is the sequence of the first 10 ugly numbers.
*/
class Solution {
public:
    int nthUglyNumber(int n) {
        int ans = 0;
        if (n <= 6) { return n; }
        int *dp = new int [n]{};
        dp[0] = 1;
        int f2 = 0, f3 = 0, f5 = 0;
        for (int i = 1; i < n; i++)
        {
            dp[i] = min(dp[f2]*2, min(dp[f3]*3, dp[f5]*5));
            while (dp[f2]*2 <= dp[i]) { f2++; }
            while (dp[f3]*3 <= dp[i]) { f3++; }
            while (dp[f5]*5 <= dp[i]) { f5++; }
        }
        
        ans = dp[n-1];
        delete []dp;
        return ans;
    }
};
```

## 0279. Perfect Squares
```cpp
/*
Input: n = 12
Output: 3
Explanation: 12 = 4 + 4 + 4.
*/
class Solution {
public:
    int numSquares(int n) {
        if (n < 1) { return 0; }
        int *dp = new int [n+1]{};
        dp[1] = 1;
        for (int i = 2; i <= n; i++)
        {
            if (i * i <= n) { dp[i * i] = 1; }
            if (dp[i]) { continue; }
            int min = INT_MAX;
            for (int j = 1; j <= i/2; j++)
            {
                if (min > dp[j] + dp[i-j])
                {
                    min = dp[j] + dp[i-j];
                }
            }
            dp[i] = min;
        }
        int ans = dp[n];
        delete []dp;
        return ans;
    }
};
```

## 0300. Longest Increasing Subsequence*
```cpp
/*
Input: nums = [10,9,2,5,3,7,101,18]
Output: 4
Explanation: The longest increasing subsequence
is [2,3,7,101], therefore the length is 4.
*/
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = int(nums.size());
        if (n <= 1) { return n; }
        int *dp = new int [n+1]{}, len = 1;
        dp[1] = nums[0];
        for (int i = 1; i < n; i++)
        {
            if (nums[i] > dp[len]) { dp[++len] = nums[i]; }
            else
            {
                int l = 1, r = len;
                while (l <= r)
                {
                    int m = (l + r) / 2;
                    if (dp[m] >= nums[i]) { r = m - 1; }
                    else { l = m + 1; }
                }
                dp[l] = nums[i];
            }
        }
        delete []dp;
        return len;
    }
};

/*
给定数组arr，设长度为n，输出arr的最长递增子序列。
（如果有多个答案，请输出其中字典序最小的）
input: 1,2,8,6,4
output: 1,2,4
*/
class Solution {
public:
    /**
     * retrun the longest increasing subsequence
     * @param arr int整型vector the array
     * @return int整型vector
     */
    vector<int> LIS(vector<int>& arr) {
        // write code here
        vector<int> ans;
        int n = (int)arr.size();
        if (n == 0) { return ans; }
        vector<int> dp(n, 1);
        int len = 0;
        for (int i = 0; i < n; i++) {
            int ele = arr[i];
            if (ans.empty() || ans.back() < ele) {
                ans.emplace_back(ele);
                len++;
                dp[i] = len;
            }
            else {
                int l = 0, r = len - 1;
                while (l <= r) {
                    int m = (l + r) >> 1;
                    if (ans[m] >= ele) { r = m - 1; }
                    else { l = m + 1; }
                }
                ans[l] = ele;
                dp[i] = l + 1;
            }
        }
        for (int i = n - 1; i >= 0; i--) {
            if (dp[i] == len) {
                ans[--len] = arr[i];
            }
        }
        return ans;
    }
};
```

## 0303. Range Sum Query - Immutable
```cpp
/*
Input
["NumArray", "sumRange", "sumRange", "sumRange"]
[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]
Output
[null, 1, -1, -3]

Explanation
NumArray numArray = new NumArray([-2, 0, 3, -5, 2, -1]);
numArray.sumRange(0, 2); // return 1 ((-2) + 0 + 3)
numArray.sumRange(2, 5); // return -1 (3 + (-5) + 2 + (-1)) 
numArray.sumRange(0, 5); // return -3 ((-2) + 0 + 3 + (-5) + 2 + (-1))
*/
class NumArray {
    vector<int> arr;
public:
    NumArray(vector<int>& nums) {
        int n = int(nums.size());
        arr.resize(n + 1);
        for (int i = 0; i < n; i++)
        {
            arr[i+1] = arr[i] + nums[i];
        }
    }
    
    int sumRange(int i, int j) {
        return arr[j+1] - arr[i];
    }
};

/**
 * Your NumArray object will be instantiated and called as such:
 * NumArray* obj = new NumArray(nums);
 * int param_1 = obj->sumRange(i,j);
 */
```

## 0304. Range Sum Query 2D - Immutable*
```cpp
/*
Given matrix = [
  [3, 0, 1, 4, 2],
  [5, 6, 3, 2, 1],
  [1, 2, 0, 1, 5],
  [4, 1, 0, 1, 7],
  [1, 0, 3, 0, 5]
]

sumRegion(2, 1, 4, 3) -> 8
sumRegion(1, 1, 2, 2) -> 11
sumRegion(1, 2, 2, 4) -> 12
*/
class NumMatrix {
    vector<vector<int>> accum;
public:
    NumMatrix(vector<vector<int>>& matrix) {
        int nRow = int(matrix.size());
        if (nRow == 0) { return; }
        int nCol = int(matrix[0].size());
        if (nCol == 0) { return; }
        accum.resize(nRow, vector<int> (nCol+1, 0));
        for (int i = 0; i < nRow; i++)
        {
            for (int j = 0; j < nCol; j++)
            {
                accum[i][j+1] = accum[i][j] + matrix[i][j];
            }
        }
    }
    int sumRegion(int row1, int col1, int row2, int col2) {
        int ans = 0;
        for (int i = row1; i <= row2; i++)
        {
            ans += accum[i][col2+1] - accum[i][col1];
        }
        return ans;
    }
};

/**
 * Your NumMatrix object will be instantiated and called as such:
 * NumMatrix* obj = new NumMatrix(matrix);
 * int param_1 = obj->sumRegion(row1,col1,row2,col2);
 */
```

## 0312. Burst Balloons*
```cpp
/*
Input: nums = [3,1,5,8]
Output: 167
Explanation:
nums = [3,1,5,8] --> [3,5,8] --> [3,8] --> [8] --> []
coins =  3*1*5 + 3*5*8 + 1*3*8 + 1*8*1 = 167
*/
class Solution {
public:
    int maxCoins(vector<int>& nums) {
        int n = int(nums.size());
        if (n == 0) { return 0; }
        int **dp = new int *[n + 2];
        for (int i = 0; i <= n + 1; i++) { dp[i] = new int [n+2]{}; }
        for (int i = n - 1; i >= 0; i--)
        {
            for (int j = i + 2; j <= n + 1; j++)
            {
                int MAX = 0;
                for (int k = i + 1; k < j; k++)
                {
                    int left = (i == 0 ? 1 : nums[i-1]);
                    int mid = nums[k-1];
                    int right = (j == n + 1 ? 1 : nums[j-1]);
                    int prod = left * mid * right;
                    MAX = max(MAX, dp[i][k] + prod + dp[k][j]);
                }
                dp[i][j] = MAX;
            }
        }
        int ans = dp[0][n+1];
        for (int i = 0; i <= n+1; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
};
```

## 0331. Verify Preorder Serialization of a Binary Tree*
```cpp
/*
     _9_
    /   \
   3     2
  / \   / \
 4   1  #  6
/ \ / \   / \
# # # #   # #
*/
class Solution {
public:
    bool isValidSerialization(string preorder) {
        int n = int(preorder.length()), i = 0, numNull = 1;
        if (n == 0) { return true; }
        while (i < n)
        {
            if (numNull <= 0) { return false; }
            if (preorder[i] == '#')
            {
                numNull--;
                i++;
            }
            else if (preorder[i] == ',') { i++; }
            else
            {
                while (i < n && preorder[i] >= '0' && preorder[i] <= '9') { i++; }
                numNull++;
            }
        }
        return numNull == 0;
    }
};
```

## 0322. Coin Change*
```cpp
/*
Input: coins = [186, 419, 83, 408], amount = 6249
Output: 20
*/
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount+1, 0);
        for (int s = 1; s <= amount; s++) {
            int MIN = amount + 1;
            for (const int &coin : coins) {
                if (s >= coin) {
                    MIN = min(MIN, dp[s - coin] + 1);
                }
            }
            dp[s] = MIN;
        }
        return dp[amount] < amount + 1 ? dp[amount] : -1;
    }
};
```

## 0337. House Robber III*
```cpp
/*
            2
        /       \
       1         3
      /
     4
max = 3 + 4 = 7
*/
class Solution {
public:
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
};
```

## 0338. Counting Bits
```cpp
/*
Input: 5
Output: [0,1,1,2,1,2]
*/
class Solution {
public:
    vector<int> countBits(int num) {
        vector<int> ans(num+1, 0);
        for (int i = 1; i <= num; i++)
        {
            ans[i] = ans[i/2] + i % 2;
        }
        return ans;
    }
};
```

## 0341. Flatten Nested List Iterator*
```cpp
/*
Input: [1,[4,[6]]]
Output: [1,4,6]
Explanation: By calling next repeatedly until hasNext
returns false, the order of elements returned by next
should be: [1,4,6].
*/
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * class NestedInteger {
 *   public:
 *     // Return true if this NestedInteger holds a single integer, rather than a nested list.
 *     bool isInteger() const;
 *
 *     // Return the single integer that this NestedInteger holds, if it holds a single integer
 *     // The result is undefined if this NestedInteger holds a nested list
 *     int getInteger() const;
 *
 *     // Return the nested list that this NestedInteger holds, if it holds a nested list
 *     // The result is undefined if this NestedInteger holds a single integer
 *     const vector<NestedInteger> &getList() const;
 * };
 */

class NestedIterator {
    vector<int> vals;
    vector<int>::iterator iter;
public:
    NestedIterator(vector<NestedInteger> &nestedList) {
        dfs(nestedList);
        iter = vals.begin();
    }
    void dfs(const vector<NestedInteger> &nestedList) {
        for (auto &ni : nestedList)
        {
            if (ni.isInteger()) { vals.emplace_back(ni.getInteger()); }
            else { dfs(ni.getList()); }
        }
    }
    
    int next() {
        if (hasNext()) { return *iter++; }
        else { return -1; }
    }
    
    bool hasNext() {
        return iter != vals.end();
    }
};

/**
 * Your NestedIterator object will be instantiated and called as such:
 * NestedIterator i(nestedList);
 * while (i.hasNext()) cout << i.next();
 */
```

## 0343. Integer Break
```cpp
/*
Input: 10
Output: 36
Explanation: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36.
*/
class Solution {
public:
    int integerBreak(int n) {
        if (n < 2) { return n; }
        if (n == 2 || n == 3) { return n-1; }
        int *dp = new int [n+1]{};
        dp[1] = 1;
        dp[2] = 2;
        dp[3] = 3;
        for (int i = 4; i <= n; i++)
        {
            int prod = 1;
            for (int j = 1; j <= i/2; j++)
            {
                if (prod < dp[j] * dp[i-j])
                {
                    prod = dp[j] * dp[i-j];
                }
            }
            dp[i] = prod;
        }
        int ans = dp[n];
        delete []dp;
        return ans;
    }
};
```

## 0344. Reverse String
```cpp
/*
Input: ["h","e","l","l","o"]
Output: ["o","l","l","e","h"]
*/
class Solution {
public:
    void reverseString(vector<char>& s) {
        int n = int(s.size());
        if (n == 0) { return; }
        for (int i = 0; i < n/2; i++)
        {
            swap(s[i], s[n-1-i]);
        }
    }
};
```

## 0354. Russian Doll Envelopes*
```cpp
/*
Input: envelopes = [[5,4],[6,4],[6,7],[2,3]]
Output: 3
Explanation: The maximum number of envelopes
you can Russian doll is 3 ([2,3] => [5,4] => [6,7]).
*/
class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        int n = int(envelopes.size()), len = 1;
        if (n <= 1) { return n; }
        sort(envelopes.begin(), envelopes.end(),
             [](const auto& e1, const auto& e2) {
            return e1[0] < e2[0] || (e1[0] == e2[0] && e1[1] > e2[1]);
                });
        vector<vector<int>> dp(n+1, vector<int>{0, 0});
        dp[len] = envelopes[0];
        for (int i = 1; i < n; i++)
        {
            if (envelopes[i][0] > dp[len][0] && envelopes[i][1] > dp[len][1])
            {
                dp[++len] = envelopes[i];
            }
            else
            {
                int l = 1, r = len;
                while (l <= r)
                {
                    int m = (l + r) / 2;
                    if (dp[m][0] < envelopes[i][0] &&
                        dp[m][1] < envelopes[i][1])
                    {
                        l = m + 1;
                    }
                    else { r = m - 1; }
                }
                dp[l] = envelopes[i];
            }
        }
        return len;
    }
};
```

## 0365. Water and Jug Problem
```cpp
/*
You are given two jugs with capacities jug1Capacity and jug2Capacity
liters. There is an infinite amount of water supply available. Determine
whether it is possible to measure exactly targetCapacity liters using
these two jugs.

If targetCapacity liters of water are measurable, you must have
targetCapacity liters of water contained within one or both buckets
by the end.

Operations allowed:
Fill any of the jugs with water.
Empty any of the jugs.
Pour water from one jug into another till the other jug is completely
full, or the first jug itself is empty.

Input: jug1Capacity = 3, jug2Capacity = 5, targetCapacity = 4
Output: true
Explanation: The famous Die Hard example 
*/
class Solution {
public:
    bool canMeasureWater(int jug1Capacity, int jug2Capacity,
                         int targetCapacity) {
        int x = jug1Capacity;
        int y = jug2Capacity;
        int z = targetCapacity;
        if (z > x + y) { return false; }
        if (z == x || z == y) { return true; }
        if (x > y) { swap(x, y); }
        int diff = y % x;
        if (diff == 0) { return z % x == 0; }
        for (int i = 0; i < x; i++) {
            if (diff == z || x + diff == z || y + diff == z ||
                y - x + diff == z) { return true; }
            diff = (y - (x - diff)) % x;
        }
        return false;
    }
};
```

## 0367. Valid Perfect Square
```cpp
/*
Input: num = 16
Output: true

Input: num = 14
Output: false
*/
class Solution {
public:
    bool isPerfectSquare(int num) {
        long l = 1, r = num;
        while (l <= r) {
            long m = (l + r) >> 1;
            long res = m * m;
            if (res == num) { return true; }
            else if (res < num) { l = m + 1; }
            else { r = m - 1; }
        }
        return false;
    }
};
```

## 0368. Largest Divisible Subset
```cpp
/*
Given a set of distinct positive integers nums, return the largest
subset answer such that every pair (answer[i], answer[j]) of elements
in this subset satisfies:
answer[i] % answer[j] == 0, or
answer[j] % answer[i] == 0
If there are multiple solutions, return any of them.

Input: nums = [1,2,3]
Output: [1,2]
Explanation: [1,3] is also accepted.
*/
class Solution {
public:
    vector<int> largestDivisibleSubset(vector<int>& nums) {
        vector<int> ans;
        int n = (int)nums.size();
        if (n < 2) { return nums; }
        sort(nums.begin(), nums.end());
        vector<int> dp(n, 1);
        vector<int> par(n, -1);
        int len = 1;
        for (int i = 1; i < n; i++) {
            for (int j = i - 1; j >= 0; j--) {
                if (nums[i] % nums[j] == 0 && dp[j] + 1 > dp[i]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                    par[i] = j;
                }
            }
            len = max(len, dp[i]);
        }
        ans.resize(len);
        for (int i = n - 1; i >= 0;) {
            if (dp[i] == len) {
                ans[--len] = nums[i];
                i = par[i];
            }
            else { i--; }
        }
        return ans;
    }
};
```

## 0377. Combination Sum IV*
```cpp
/*
Input: nums = [1,2,3], target = 4
Output: 7
Explanation:
The possible combination ways are:
(1, 1, 1, 1)
(1, 1, 2)
(1, 2, 1)
(1, 3)
(2, 1, 1)
(2, 2)
(3, 1)
Note that different sequences are counted as different combinations.
*/
class Solution {
public:
    int combinationSum4(vector<int>& nums, int target) {
        int n = (int)nums.size();
        if (n == 0) { return 0; }
        vector<int> dp(target+1, 0);
        dp[0] = 1;
        for (int i = 1; i <= target; i++) {
            for (int &ele : nums) {
                if (i >= ele && dp[i - ele] <= INT_MAX - dp[i]) {
                    dp[i] += dp[i - ele];
                }
            }
        }
        return dp[target];
    }
};
```

## 0395. Longest Substring with At Least K Repeating Characters*
```cpp
/*
Input: s = "ababbc", k = 2
Output: 5
Explanation: The longest substring is "ababb",
as 'a' is repeated 2 times and 'b' is repeated
3 times.
*/
class Solution {
public:
    int longestSubstring(string s, int k) {
        int n = int(s.length()), ans = 0;
        for (int t = 1; t <= 26; t++)
        {
            int l = 0, r = 0, tot = 0, numLessK = 0;
            int *count = new int [26]{};
            while (r < n)
            {
                count[s[r] - 'a']++;
                if (count[s[r] - 'a'] == 1)
                {
                    tot++;
                    numLessK++;
                }
                if (count[s[r] - 'a'] == k)
                {
                    numLessK--;
                }
                while (tot > t)
                {
                    count[s[l] - 'a']--;
                    if (count[s[l] - 'a'] == k-1)
                    {
                        numLessK++;
                    }
                    if (count[s[l] - 'a'] == 0)
                    {
                        numLessK--;
                        tot--;
                    }
                    l++;
                }
                if (numLessK == 0)
                {
                    ans = max(ans, r - l + 1);
                }
                r++;
            }
            delete []count;
        }
        return ans;
    }
};
```

## 0403. 青蛙过河*
```cpp
/*
一只青蛙想要过河。 假定河流被等分为若干个单元格，并且在每一个单元格内
都有可能放有一块石子（也有可能没有）。 青蛙可以跳上石子，但是不可以跳
入水中。

给你石子的位置列表 stones（用单元格序号 升序 表示）， 请判定青蛙能
否成功过河（即能否在最后一步跳至最后一块石子上）。

开始时， 青蛙默认已站在第一块石子上，并可以假定它第一步只能跳跃一个单
位（即只能从单元格 1 跳至单元格 2 ）。

如果青蛙上一步跳跃了 k 个单位，那么它接下来的跳跃距离只能选择为 k - 1、
k 或 k + 1 个单位。 另请注意，青蛙只能向前方（终点的方向）跳跃。

输入：stones = [0,1,3,5,6,8,12,17]
输出：true
解释：青蛙可以成功过河，按照如下方案跳跃：跳 1 个单位到第 2 块石子,
然后跳 2 个单位到第 3 块石子, 接着 跳 2 个单位到第 4 块石子, 然后跳 3
个单位到第 6 块石子, 跳 4 个单位到第 7 块石子, 最后，跳 5 个单位到第 8
个石子（即最后一块石子）。

输入：stones = [0,1,2,3,4,8,9,11]
输出：false
解释：这是因为第 5 和第 6 个石子之间的间距太大，没有可选的方案供青蛙跳
跃过去。
*/
class Solution {
public:
    bool canCross(vector<int>& stones) {
        unordered_map<int, int> dict;
        int n = (int)stones.size();
        if (n <= 1) { return true; }
        vector<vector<int>> dp(n, vector<int>(n, 0));
        dp[0][0] = true;
        for (int i = 1; i < n; i++) {
            if (stones[i] - stones[i-1] > i) { return false; }
            for (int j = i - 1; j >= 0; j--) {
                int k = stones[i] - stones[j];
                if (k > j + 1) { break; }
                dp[i][k] = dp[j][k-1] || dp[j][k] || dp[j][k+1];
                if (i == n - 1 && dp[i][k]) { return true; }
            }
        }
        return false;
    }
};
```

## 0407. 接雨水 II*
```cpp
/*
给你一个 m x n 的矩阵，其中的值均为非负整数，代表二维高度图每个单
元的高度，请计算图中形状最多能接多少体积的雨水。

示例：
给出如下 3x6 的高度图:
[
  [1,4,3,1,3,2],
  [3,2,1,3,2,4],
  [2,3,3,2,3,1]
]
返回 4 。
*/
class Solution {
public:
    int trapRainWater(vector< vector< int>>& heightMap) {
        int nRow = (int)heightMap.size();
        if (nRow == 0) { return 0; }
        int nCol = (int)heightMap[0].size();
        if (nCol < 3) { return 0; }
        typedef pair < int, pair< int, int>> PIII;
        priority_queue < PIII, vector < PIII>, greater< PIII>> heap;
        bool **vis = new bool *[nRow];
        for (int i = 0; i < nRow; i++) {
            vis[i] = new bool [nCol]{};
            heap.push({heightMap[i][0], {i, 0}});
            heap.push({heightMap[i][nCol-1], {i, nCol-1}});
            vis[i][0] = vis[i][nCol-1] = true;
        }
        for (int j = 0; j < nCol; j++) {
            heap.push({heightMap[0][j], {0, j}});
            heap.push({heightMap[nRow-1][j], {nRow-1, j}});
            vis[0][j] = vis[nRow-1][j] = true;
        }
        int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, -1, 0, 1};
        int ans = 0;
        while (!heap.empty()) {
            auto cur = heap.top();
            heap.pop();
            int x = cur.second.first, y = cur.second.second;
            int h = cur.first;
            for (int i = 0; i < 4; i++) {
                int xx = x + dx[i];
                int yy = y + dy[i];
                if (xx >= 0 && xx < nRow && yy >= 0 && yy < nCol &&
                    !vis[xx][yy]) {
                    vis[xx][yy] = true;
                    ans += max(h - heightMap[xx][yy], 0);
                    heap.push({max(h, heightMap[xx][yy]), {xx, yy}});
                }
            }
        }
        for (int i = 0; i < nRow; i++) {
            delete []vis[i];
        }
        delete []vis;
        return ans;
    }
};
```

## 0415. Add Strings
```cpp
/*
Input: num1 = "456", num2 = "77"
Output: "533"
*/
class Solution {
public:
    string addStrings(string num1, string num2) {
        string ans = "";
        int len1 = (int)num1.length(), len2 = (int)num2.length();
        if (len1 < len2)
        {
            swap(len1, len2);
            swap(num1, num2);
        }
        int i = len1 - 1, j = len2 - 1;
        int res = 0;
        while (j >= 0)
        {
            int sum = (num1[i] - '0') + (num2[j] - '0') + res;
            res = 0;
            if (sum >= 10)
            {
                res = 1;
                sum -= 10;
            }
            char sumChar = sum + '0';
            ans.insert(0, 1, sumChar);
            i--;
            j--;
        }
        while (i >= 0)
        {
            int sum = (num1[i] - '0') + res;
            res = 0;
            if (sum >= 10)
            {
                res = 1;
                sum -= 10;
            }
            char sumChar = sum + '0';
            ans.insert(0, 1, sumChar);
            i--;
        }
        if (res) { ans.insert(0, 1, '1'); }
        return ans;
    }
};
```

## 0470. Implement Rand10() Using Rand7()*
```cpp
/*
Input: n = 3
Output: [3,8,10]
*/
// The rand7() API is already defined for you.
// int rand7();
// @return a random integer in the range 1 to 7

class Solution {
public:
    int rand10() {
        int ans = 0, i = 0, j = 0;
        do {
            i = rand7();
            j = rand7();
            ans = (i - 1) * 7 + j;
        }
        while (ans > 40);
        return ans % 10 + 1;
    }
};
```

## 0503. Next Greater Element II*
```cpp
/*
Input: [1,2,1]
Output: [2,-1,2]
Explanation: The first 1's next greater number is 2; 
The number 2 can't find next greater number; 
The second 1's next greater number needs to search
circularly, which is also 2.
*/
class Solution {
public:
    vector<int> nextGreaterElements(vector<int>& nums) {
        int n = int(nums.size());
        if (n == 0) { return vector<int> {}; }
        vector<int> ans(n, -1);
        stack<int> st;
        for (int i = 0; i < 2 * n - 1; i++)
        {
            while (!st.empty() && nums[st.top()] < nums[i % n])
            {
                ans[st.top()] = nums[i % n];
                st.pop();
            }
            st.push(i % n);
        }
        return ans;
    }
};
```

## 0516. Longest Palindromic Subsequence*
```cpp
/*
Given a string s, find the longest palindromic subsequence's
length in s. You may assume that the maximum length of s is
1000.
Input: "bbbab"
Output: 4
*/
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        int n = int(s.length());
        if (n <= 1) { return n; }
        int **dp = new int *[n];
        for (int i = 0; i < n; i++)
        {
            dp[i] = new int [n]{};
            dp[i][i] = 1;
        }
        for (int i = n-2; i >= 0; i--)
        {
            for (int j = i+1; j < n; j++)
            {
                if (s[i] == s[j]) { dp[i][j] = dp[i+1][j-1] + 2; }
                else { dp[i][j] = max(dp[i+1][j], dp[i][j-1]); }
            }
        }
        int ans = dp[0][n-1];
        for (int i = 0; i < n; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
};
```

## 0530. Minimum Absolute Difference in BST
```cpp
/*
        1
          \
           3
          /
         2
output: 1
*/
class Solution {
public:
    int getMinimumDifference(TreeNode* root) {
        TreeNode *last = nullptr;
        int ans = INT_MAX;
        inOrder(root, last, ans);
        return ans;
    }
    void inOrder(TreeNode *root, TreeNode *&last, int &diff)
    {
        if (root != nullptr)
        {
            inOrder(root->left, last, diff);
            if (last == nullptr) { last = root; }
            else { diff = min(diff, abs(root->val - last->val)); }
            last = root;
            inOrder(root->right, last, diff);
        }
    }
};
```

## 0543. Diameter of Binary Tree
Given a binary tree, you need to compute the length of the diameter of the tree. The diameter of a binary tree is the length of the longest path between any two nodes in a tree. This path may or may not pass through the root.

```cpp
/*
Given a binary tree
          1
         / \
        2   3
       / \
      4   5
Return 3, which is the length of the path [4,2,1,3] or [5,2,1,3].
Note: The length of path between two nodes is represented by the
number of edges between them.
*/
class Solution {
public:
    int diameterOfBinaryTree(TreeNode* root) {
        int max = 0;
        getDep(root, max);
        return max;
    }
    int getDep(TreeNode *root, int &maxLen)
    {
        if (root == nullptr) { return 0; }
        int leftDep = getDep(root->left, maxLen);
        int rightDep = getDep(root->right, maxLen);
        int curLen = leftDep + rightDep;
        maxLen = maxLen > curLen ? maxLen : curLen;
        return (leftDep > rightDep ? leftDep : rightDep) + 1;
    }
};
```

## 0554. 砖墙*
```cpp
/*
你的面前有一堵矩形的、由 n 行砖块组成的砖墙。这些砖块高度相同（也就是一个单位高）
但是宽度不同。每一行砖块的宽度之和应该相等。

你现在要画一条 自顶向下 的、穿过 最少 砖块的垂线。如果你画的线只是从砖块的边缘经过，
就不算穿过这块砖。你不能沿着墙的两个垂直边缘之一画线，这样显然是没有穿过一块砖的。

给你一个二维数组 wall ，该数组包含这堵墙的相关信息。其中，wall[i] 是一个代表从左
至右每块砖的宽度的数组。你需要找出怎样画才能使这条线 穿过的砖块数量最少 ，并且返回
穿过的砖块数量 。

输入：wall = [[1,2,2,1],[3,1,2],[1,3,2],[2,4],[3,1,2],[1,3,1,1]]
输出：2

输入：wall = [[1],[1],[1]]
输出：3
*/
class Solution {
public:
    int leastBricks(vector<vector<int>>& wall) {
        int n = (int)wall.size();
        if (n == 0) { return 0; }
        int ans = 0;
        unordered_map<int, int> cnt;
        for (auto arr : wall) {
            int len = (int)arr.size();
            int sum = 0;
            for (int i = 0; i < len - 1; i++) {
                sum += arr[i];
                cnt[sum]++;
            }
        }
        unordered_map<int, int>::iterator iter;
        for (iter = cnt.begin(); iter != cnt.end(); iter++) {
            ans = max(ans, iter->second);
        }
        return n - ans;
    }
};
```

## 0633. 平方数之和*
```cpp
/*
给定一个非负整数 c ，你要判断是否存在两个整数 a 和 b，使得 a^2 + b^2 = c 。

示例 1：
输入：c = 5
输出：true
解释：1 * 1 + 2 * 2 = 5

示例 2：
输入：c = 3
输出：false

示例 3：
输入：c = 4
输出：true

示例 4：
输入：c = 2
输出：true

示例 5：
输入：c = 1
输出：true
*/
class Solution {
public:
    bool judgeSquareSum(int c) {
        long l = 0, r = (long)sqrt(c);
        while (l <= r) {
            long sqaureSum = l * l + r * r;
            if (sqaureSum == c) { return true; }
            else if (sqaureSum < c) { l++; }
            else { r--; }
        }
        return false;
    }
};
```

## 0647. Palindromic Substrings
```cpp
/*
Input: "aaa"
Output: 6
Explanation: Six palindromic strings: "a", "a",
"a", "aa", "aa", "aaa".
*/
class Solution {
public:
    int countSubstrings(string s) {
        int n = int(s.length()), ans = n;
        if (n == 0) { return ans; }
        bool **dp = new bool *[n];
        for (int i = 0; i < n; i++)
        {
            dp[i] = new bool [n]{};
            dp[i][i] = true;
        }
        for (int i = 1; i < n; i++)
        {
            if (s[i-1] == s[i])
            {
                dp[i-1][i] = true;
                ans++;
            }
        }
        for (int k = 2; k < n; k++)
        {
            for (int i = 0; i < n-k; i++)
            {
                int j = i + k;
                if (s[i] == s[j] && dp[i+1][j-1])
                {
                    dp[i][j] = true;
                    ans++;
                }
            }
        }
        for (int i = 0; i < n; i++) { delete []dp[i]; }
        delete []dp;
        return ans;
    }
};
```

## 0690. 员工的重要性
```cpp
/*
给定一个保存员工信息的数据结构，它包含了员工 唯一的 id ，重要度和直系下属的 id 。
比如，员工 1 是员工 2 的领导，员工 2 是员工 3 的领导。他们相应的重要度为 15 , 10 , 5 。
那么员工 1 的数据结构是 [1, 15, [2]] ，员工 2的 数据结构是 [2, 10, [3]] ，员工3
的数据结构是 [3, 5, []] 。注意虽然员工 3 也是员工 1 的一个下属，但是由于 并不是直系
下属，因此没有体现在员工 1 的数据结构中。

现在输入一个公司的所有员工信息，以及单个员工 id ，返回这个员工和他所有下属的重要度之和。

示例：
输入：[[1, 5, [2, 3]], [2, 3, []], [3, 3, []]], 1
输出：11
解释：
员工 1 自身的重要度是 5 ，他有两个直系下属 2 和 3 ，而且 2 和 3 的重要度均为 3 。
因此员工 1 的总重要度是 5 + 3 + 3 = 11 。
*/
/*
// Definition for Employee.
class Employee {
public:
    int id;
    int importance;
    vector< int> subordinates;
};
*/

class Solution {
public:
    int dfs(unordered_set< int> &vis, unordered_map< int, int> &dict,
            unordered_map< int, vector< int>> &next, int id) {
        int ans = 0;
        for (int sub : next[id]) {
            if (vis.find(sub) == vis.end()) {
                vis.insert(sub);
                ans += dfs(vis, dict, next, sub);
            }
        }
        return ans + dict[id];
    }
    
    int getImportance(vector<Employee*> employees, int id) {
        unordered_map<int, int> dict;
        unordered_map<int, vector<int>> next;
        unordered_set<int> vis;
        for (auto employee : employees) {
            dict[employee->id] = employee->importance;
            next[employee->id] = employee->subordinates;
        }
        return dfs(vis, dict, next, id);
    }
};
```

## 0704. Binary Search
```cpp
/*
Input: nums = [-1,0,3,5,9,12], target = 9
Output: 4
*/
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int n = (int)nums.size();
        if (n == 0) { return -1; }
        int l = 0, r = n - 1;
        while (l <= r) {
            int m = (l + r) >> 1;
            if (nums[m] == target) { return m; }
            else if (nums[m] < target) { l = m + 1; }
            else { r = m - 1; }
        }
        return -1;
    }
};
```

## 0738. Monotone Increasing Digits*
Given a non-negative integer N, find the largest number that is less than or equal to N with monotone increasing digits. (Recall that an integer has monotone increasing digits if and only if each pair of adjacent digits x and y satisfy x <= y.)

```cpp
/*
Input: N = 10
Output: 9

Input: N = 1234
Output: 1234

Input: N = 332
Output: 299
*/
class Solution {
public:
    int monotoneIncreasingDigits(int N) {
        int ans = 0;
        string str = to_string(N);
        int len = int(str.length()), idx = -1;
        for (int i = 1; i < len; i++)
        {
            if (str[i] < str[i-1])
            {
                idx = i;
                break;
            }
        }
        if (idx == -1) { return N; }
        int pos = idx - 1;
        while (pos >= 1 && str[pos] == str[pos-1]) { pos--; }
        str[pos]--;  // current position decrease by 1
        for (int i = pos + 1; i < len; i++) { str[i] = '9'; }
        for (int i = len-1, pow = 1; i >= 0; i--, pow *= 10)
        {
            ans += (str[i] - '0') * pow;
        }
        return ans;
    }
};
```

## 0741. Cherry Pickup*
```cpp
/*
You are given an n x n grid representing a field of cherries,
each cell is one of three possible integers. 0 means the cell is
empty, so you can pass through, 1 means the cell contains a
cherry that you can pick up and pass through, or -1 means
the cell contains a thorn that blocks your way. Return the
maximum number of cherries you can collect by following
the rules below:
Starting at the position (0, 0) and reaching (n - 1, n - 1) by
moving right or down through valid path cells (cells with value 0 or 1).
After reaching (n - 1, n - 1), returning to (0, 0) by moving left
or up through valid path cells. When passing through a path cell
containing a cherry, you pick it up, and the cell becomes an empty cell 0.
If there is no valid path between (0, 0) and (n - 1, n - 1), then no
cherries can be collected.

Input: grid = [[0,1,-1],[1,0,-1],[1,1,1]]
Output: 5
Explanation: The player started at (0, 0) and went down, down,
right right to reach (2, 2). 4 cherries were picked up during
this single trip, and the matrix becomes [[0,1,-1],[0,0,-1],[0,0,0]].
Then, the player went left, up, up, left to return home, picking up
one more cherry. The total number of cherries picked up is 5, and
this is the maximum possible.

Input: grid = [[1,1,-1],[1,-1,1],[-1,1,1]]
Output: 0
*/
class Solution {
public:
    int cherryPickup(vector<vector<int>>& grid) {
        int n = int(grid.size());
        if (n == 0) { return 0; }
        vector<vector<vector<int>>> 
            dp(n, vector<vector<int>>(n, vector<int>(n, -1)));
        int ans = move(dp, grid, 0, 0, 0, n);
        return max(0, ans);
    }

    int move(vector<vector<vector<int>>> &dp,
             vector<vector<int>> &grid,
             int r1, int c1, int c2, int n)
    {
        int r2 = r1 + c1 - c2;
        if (r1 >= n || c1 >= n || r2 >= n || c2 >= n ||
            grid[r1][c1] == -1 ||
            grid[r2][c2] == -1) { return -2; }
        if (r1 == n-1 && c1 == n-1) { return grid[r1][c1]; }
        if (dp[r1][c1][c2] != -1) { return dp[r1][c1][c2]; }
        int ans = move(dp, grid, r1, c1+1, c2+1, n);
        ans = max(ans, move(dp, grid, r1+1, c1, c2+1, n));
        ans = max(ans, move(dp, grid, r1, c1+1, c2, n));
        ans = max(ans, move(dp, grid, r1+1, c1, c2, n));
        if (ans >= 0)
        {
            ans += grid[r1][c1] + (c1 != c2 || r1 != r2) *
                grid[r2][c2];
        }
        dp[r1][c1][c2] = ans;
        return ans;
    }
};
```

## 0752. Open the Lock*
```cpp
/*
Input: deadends = ["0201","0101","0102","1212","2002"],
target = "0202"
Output: 6
Explanation:
A sequence of valid moves would be "0000" -> "1000" ->
"1100" -> "1200" -> "1201" -> "1202" -> "0202".
Note that a sequence like "0000" -> "0001" -> "0002" ->
"0102" -> "0202" would be invalid,
because the wheels of the lock become stuck after the
display becomes the dead end "0102".
*/
class Solution {
public:
    int openLock(vector<string>& deadends, string target) {
        string init = "0000";
        int n = int(init.length()), ans = 0;
        unordered_set<string> vis;
        for (string &str : deadends) { vis.insert(str); }
        if (vis.find(init) != vis.end()) { return -1; }
        queue<string> qLock;
        qLock.push(init);
        while (!qLock.empty())
        {
            int num = int(qLock.size());
            while (num--)
            {
                string str = qLock.front();
                qLock.pop();
                if (str == target) { return ans; }
                for (int i = 0; i < n; i++)
                {
                    string tmp = str;
                    for (int d = -1; d <= 1; d += 2)
                    {
                        tmp[i] = (str[i] - '0' + 10 + d) % 10 + '0';
                        if (vis.find(tmp) == vis.end())
                        {
                            vis.insert(tmp);
                            qLock.push(tmp);
                        }
                    }
                }
            }
            ans++;
        }
        return -1;
    }
};
```

## 0766. Toeplitz Matrix
```cpp
/*
1 2 3 4
5 1 2 3
9 5 1 2
*/
class Solution {
public:
    bool isToeplitzMatrix(vector<vector<int>>& matrix) {
        int m = int(matrix.size());
        if (m == 0) { return false; }
        int n = int(matrix[0].size());
        if (n == 0) { return false; }
        for (int j = 0; j < n; j++)
        {
            int first = matrix[0][j];
            for (int i = 0; i < min(m, n-j); i++)
            {
                if (first != matrix[i][i+j]) { return false; }
            }
        }
        for (int i = 1; i < m; i++)
        {
            int first = matrix[i][0];
            for (int j = 0; j < min(n, m-i); j++)
            {
                if (first != matrix[i+j][j]) { return false; }
            }
        }
        return true;
    }
};
```

## 0769. Max Chunks To Make Sorted*
```cpp
/*
Input: arr = [1,0,2,3,4]
Output: 4
Explanation:
We can split into two chunks, such as [1, 0], [2, 3, 4].
However, splitting into [1, 0], [2], [3], [4] is
the highest number of chunks possible.
*/
class Solution {
public:
    int maxChunksToSorted(vector<int>& arr) {
        int ans = 1, n = (int)arr.size();
        if (n <= 1) { return ans; }
        vector<int> rightMin(n, 0);
        rightMin[n-1] = arr[n-1];
        for (int i = n - 2; i >= 0; i--)
        {
            rightMin[i] = min(rightMin[i+1], arr[i]);
        }
        int leftMax = arr[0];
        for (int i = 1; i < n; i++)
        {
            if (leftMax <= rightMin[i]) { ans++; }
            leftMax = max(leftMax, arr[i]);
        }
        return ans;
    }
};
```

## 0781. Rabbits in Forest
```cpp
/*
Examples:
Input: answers = [1, 1, 2]
Output: 5
Explanation:
The two rabbits that answered "1" could both be
the same color, say red. The rabbit than answered "2"
can't be red or the answers would be inconsistent.
Say the rabbit that answered "2" was blue.
Then there should be 2 other blue rabbits in the forest
that didn't answer into the array.
The smallest possible number of rabbits in the forest is
therefore 5: 3 that answered plus 2 that didn't.

Input: answers = [10, 10, 10]
Output: 11

Input: answers = []
Output: 0
*/
class Solution {
public:
    int numRabbits(vector<int>& answers) {
        int ans = 0;
        unordered_map<int, int> dict;
        for (int &ele : answers) { dict[ele]++; }
        unordered_map<int, int>::iterator iter;
        for (iter = dict.begin(); iter != dict.end(); iter++)
        {
            if (iter->first == 0) { ans += iter->second; }
            else
            {
                if (iter->first + 1 >= iter->second)
                {
                    ans += iter->first + 1;
                }
                else
                {
                    if (iter->second % (iter->first + 1) == 0)
                    {
                        ans += iter->second;
                    }
                    else
                    {
                        ans += (iter->second / (iter->first + 1) + 1) *
                                (iter->first + 1);
                    }
                }
            }
        }
        return ans;
    }
};
```

## 0783. Minimum Distance Between BST Nodes
```cpp
/*
        4
      /   \
     2     6
   /  \
  1    5  
output: 1
*/
class Solution {
public:
    int minDiffInBST(TreeNode* root) {
        int ans = INT_MAX;
        TreeNode *last = nullptr;
        inOrder(root, last, ans);
        return ans;
    }
    void inOrder(TreeNode *root, TreeNode *&last, int &ans)
    {
        if (root != nullptr)
        {
            inOrder(root->left, last, ans);
            if (last == nullptr) { last = root; }
            else { ans = min(ans, abs(root->val - last->val)); }
            last = root;
            inOrder(root->right, last, ans);
        }
    }
};
```

## 0832. Flipping an Image
```cpp
/*
Input: [[1,1,0],[1,0,1],[0,0,0]]
Output: [[1,0,0],[0,1,0],[1,1,1]]
Explanation: First reverse each row: [[0,1,1],[1,0,1],[0,0,0]].
Then, invert the image: [[1,0,0],[0,1,0],[1,1,1]]
*/
class Solution {
public:
    vector<vector<int>> flipAndInvertImage(vector<vector<int>>& A) {
        int m = int(A.size()), n = int(A[0].size());
        for (int i = 0; i < m; i++)
        {
            for (int j = 0; j < n/2; j++)
            {
                swap(A[i][j], A[i][n-1-j]);
            }
            for (int j = 0; j < n; j++)
            {
                A[i][j] = 1 - A[i][j];
            }
        }
        return A;
    }
};
```

## 0845. Longest Mountain in Array*
```cpp
/*
Input: arr = [2,1,4,7,3,2,5]
Output: 5
Explanation: The largest mountain is [1,4,7,3,2] which has length 5.
*/
class Solution {
public:
    int longestMountain(vector<int>& arr) {
        int ans = 0, n = int(arr.size());
        if (n < 3) { return 0; }
        vector<int> left(n, 0), right(n, 0);
        for (int i = 1; i < n - 1; i++)
        {
            if (arr[i] > arr[i-1]) { left[i] = left[i-1] + 1; }
            if (arr[n-1-i] > arr[n-i]) { right[n-1-i] = right[n-i] + 1; }
        }
        if (arr[n-1] > arr[n-2]) { left[n-1] = left[n-2] + 1; }
        if (arr[0] > arr[1]) { right[0] = right[1] + 1; }
        for (int i = 0; i < n; i++)
        {
            if (left[i] && right[i])
            {
                ans = max(left[i] + right[i] + 1, ans);
            }
        }
        return ans;
    }
};
```

## 0867. Transpose Matrix
```cpp
/*
Input: matrix = [[1,2,3],[4,5,6],[7,8,9]]
Output: [[1,4,7],[2,5,8],[3,6,9]]
*/
class Solution {
public:
    vector<vector<int>> transpose(vector<vector<int>>& matrix) {
        int m = int(matrix.size());
        if (m == 0) { return matrix; }
        int n = int(matrix[0].size());
        if (n == 0) { return matrix; }
        vector<vector<int>> ans(n, vector<int>(m, 0));
        for (int i = 0; i < m; i++)
        {
            for (int j = 0; j < n; j++)
            {
                ans[j][i] = matrix[i][j];
            }
        }
        return ans;
    }
};
```

## 0896. Monotonic Array
```cpp
/*
Input: [1,2,2,3]
Output: true
*/
class Solution {
public:
    bool isMonotonic(vector<int>& A) {
        int n = int(A.size());
        if (n == 1) { return true; }
        bool isIncrease = false, isDecrease = false;
        for (int i = 1; i < n; i++)
        {
            if (A[i] > A[i-1]) { isIncrease = true; }
            if (A[i] < A[i-1]) { isDecrease = true; }
            if (isIncrease && isDecrease) { return false; }
        }
        return true;
    }
};
```

## 0897. Increasing Order Search Tree*
```cpp
/*
            5           1
          /   \           \
         1     7            5
                              \
                                7
*/
class Solution {
public:
    TreeNode* increasingBST(TreeNode* root) {
        TreeNode *last = nullptr;
        TreeNode *head = nullptr;
        inOrder(root, last, head);
        return head;
    }
    void inOrder(TreeNode *root, TreeNode *&last, TreeNode *&head) {
        if (root != nullptr) {
            inOrder(root->left, last, head);
            if (last != nullptr) {
                last->right = root;
                last->left = nullptr;
                root->left = nullptr;
            }
            else { head = root; }
            last = root;
            inOrder(root->right, last, head);
        }
    }
};
```

## 0912. Sort an Array
```cpp
/*
Input: nums = [5,2,3,1]
Output: [1,2,3,5]
*/
class Solution {
public:
    vector<int> sortArray(vector<int>& nums) {
        int n = int(nums.size());
        quickSort(nums, 0, n-1);
        return nums;
    }
    void quickSort(vector<int> &nums, int b, int e)
    {
        if (b < e)
        {
            int i = b, j = e;
            while (i < j)
            {
                while (i < j && nums[i] <= nums[j]) { j--; }
                swap(nums[i], nums[j]);
                while (i < j && nums[i] <= nums[j]) { i++; }
                swap(nums[i], nums[j]);
            }
            quickSort(nums, b, i-1);
            quickSort(nums, j+1, e);
        }
    }
};
```

## 0921. Minimum Add to Make Parentheses Valid*
```cpp
/*
Input: "()))(("
Output: 4
*/
class Solution {
public:
    int minAddToMakeValid(string S) {
        int left = 0, ans = 0;
        for (const char &c : S)
        {
            if (c == '(') { left++; }
            else
            {
                if (left > 0) { left--; }
                else { ans++; }
            }
        }
        return ans + left;
    }
};
```

## 0938. 二叉搜索树的范围和
```cpp
/*
                10
             /      \
           5         15
         /   \         \
        3     7         18
输入：root = [10,5,15,3,7,null,18], low = 7, high = 15
输出：32
*/
class Solution {
public:
    int rangeSumBST(TreeNode* root, int low, int high) {
        if (root == nullptr) { return 0; }
        int left = rangeSumBST(root->left, low, high);
        int val = 0;
        if (root->val >= low && root->val <= high) {
            val = root->val;
        }
        int right = rangeSumBST(root->right, low, high);
        return left + val + right;
    }
};
```

## 1006. Clumsy Factorial*
```cpp
/*
Input: 10
Output: 12
Explanation: 12 = 10 * 9 / 8 + 7 - 6 * 5 / 4 + 3 - 2 * 1
*/
class Solution {
public:
    int clumsy(int N) {
        if (N == 1) { return 1; }
        else if (N == 2) { return 2; }
        else if (N == 3) { return 6; }
        else if (N == 4) { return 7; }
        else
        {
            if (N % 4 == 0) { return N + 1; }
            else if (N % 4 == 1) { return N + 2; }
            else if (N % 4 == 2) { return N + 2; }
            else { return N - 1; }
        }
    }
};
```

## 1047. Remove All Adjacent Duplicates In String
```cpp
/*
Input: "abbaca"
Output: "ca"
Explanation: 
For example, in "abbaca" we could remove "bb" since
the letters are adjacent and equal, and this is the
only possible move.  The result of this move is that
the string is "aaca", of which only "aa" is possible,
so the final string is "ca".
*/
class Solution {
public:
    string removeDuplicates(string S) {
        int n = int(S.length());
        if (n <= 1) { return S; }
        string ans = "";
        for (const char &c : S)
        {
            if (!ans.empty() && ans.back() == c)
            {
                ans.pop_back();
            }
            else { ans.push_back(c); }
        }
        return ans;
    }
};
```

## 1052. Grumpy Bookstore Owner
```cpp
/*
Input: customers = [1,0,1,2,1,1,7,5], grumpy = [0,1,0,1,0,1,0,1], X = 3
Output: 16
Explanation: The bookstore owner keeps themselves not grumpy for the last 3
minutes. The maximum number of customers that can be satisfied = 1 + 1 + 1 +
1 + 7 + 5 = 16.
*/
class Solution {
public:
    int maxSatisfied(vector<int>& customers, vector<int>& grumpy, int X) {
        int ans = 0, n = int(customers.size());
        if (n == 0) { return ans; }
        for (int i = 0; i < n; i++)
        {
            if (grumpy[i] == 0) { ans += customers[i]; }
        }
        int add = 0, maxAdd = 0;
        for (int i = 0; i < X; i++) { ans += customers[i] * grumpy[i]; }
        for (int i = 1; i < n - X + 1; i++)
        {
            add += -customers[i-1] * grumpy[i-1] +
            customers[i+X-1] * grumpy[i+X-1];
            if (maxAdd < add) { maxAdd = add; }
        }
        return ans + maxAdd;
    }
};
```

## 1178. Number of Valid Words for Each Puzzle*
With respect to a given puzzle string, a word is valid if both the following conditions are satisfied:
word contains the first letter of puzzle.
For each letter in word, that letter is in puzzle.
For example, if the puzzle is "abcdefg", then valid words are "faced", "cabbage", and "baggage"; while invalid words are "beefed" (doesn't include "a") and "based" (includes "s" which isn't in the puzzle).
Return an array answer, where answer[i] is the number of words in the given word list words that are valid with respect to the puzzle puzzles[i].

```cpp
/*
words = ["aaaa","asas","able","ability","actt","actor","access"], 
puzzles = ["aboveyz","abrodyz","abslute","absoryz","actresz","gaswxyz"]
Output: [1,1,3,2,4,0]
Explanation:
1 valid word for "aboveyz" : "aaaa" 
1 valid word for "abrodyz" : "aaaa"
3 valid words for "abslute" : "aaaa", "asas", "able"
2 valid words for "absoryz" : "aaaa", "asas"
4 valid words for "actresz" : "aaaa", "asas", "actt", "access"
There're no valid words for "gaswxyz" cause none of the words in
the list contains letter 'g'.
*/
class Solution {
public:
    vector<int> findNumOfValidWords(vector<string>& words,
                                    vector<string>& puzzles) {
        int n = int(puzzles.size());
        unordered_map<int, int> dict;
        for (const string &word : words)
        {
            int mask = 0;
            for (const char &c : word)
            {
                mask |= (1 << (c - 'a'));
            }
            if (__builtin_popcount(mask) <= 7)
            {
                dict[mask]++;
            }
        }
        vector<int> ans;
        for (const string &puzzle : puzzles)
        {
            int tot = 0;
            for (int choose = 0; choose < (1 << 6); choose++)
            {
                int mask = 0;
                for (int i = 0; i < 6; i++)
                {
                    if (choose & (1 << i))
                    {
                        mask |= (1 << (puzzle[i + 1] - 'a'));
                    }
                }
                mask |= (1 << (puzzle[0] - 'a'));
                tot += dict[mask];
            }
            ans.emplace_back(tot);
        }
        return ans;
    }
};
```

## 1411. Number of Ways to Paint N × 3 Grid
```cpp
/*
Input: n = 1
Output: 12
Explanation: There are 12 possible way to paint the grid as shown.
*/
class Solution {
public:
    int numOfWays(int n) {
        int aba = 6, abc = 6;
        /*
        aba = aba * 3 + abc * 2
        abc = aba * 2 + abc * 2;
        */
        const int MOD = 1000000007;
        for (int i = 1; i < n; i++)
        {
            int new_aba = (aba * 3ll + abc * 2ll) % MOD;
            int new_abc = (aba * 2ll + abc * 2ll) % MOD;
            aba = new_aba;
            abc = new_abc;
        }
        return (aba + abc) % MOD;
    }
};
```

## 1553. 吃掉 N 个橘子的最少天数*
```cpp
/*
厨房里总共有 n 个橘子，你决定每一天选择如下方式之一吃这些橘子：

吃掉一个橘子。
如果剩余橘子数 n 能被 2 整除，那么你可以吃掉 n/2 个橘子。
如果剩余橘子数 n 能被 3 整除，那么你可以吃掉 2*(n/3) 个橘子。
每天你只能从以上 3 种方案中选择一种方案。

请你返回吃掉所有 n 个橘子的最少天数。

示例 1：
输入：n = 10
输出：4
解释：你总共有 10 个橘子。
第 1 天：吃 1 个橘子，剩余橘子数 10 - 1 = 9。
第 2 天：吃 6 个橘子，剩余橘子数 9 - 2*(9/3) = 9 - 6 = 3。（9 可以被 3 整除）
第 3 天：吃 2 个橘子，剩余橘子数 3 - 2*(3/3) = 3 - 2 = 1。
第 4 天：吃掉最后 1 个橘子，剩余橘子数 1 - 1 = 0。
你需要至少 4 天吃掉 10 个橘子。
*/
class Solution {
public:
    unordered_map<int, int> dict;
    int minDays(int n) {
        if (n <= 1) {
            return 1;
        }
        if (dict.find(n) != dict.end()) {
            return dict[n];
        }
        else {
            return dict[n] = min(n % 2 + 1 + minDays(n / 2),
                                 n % 3 + 1 + minDays(n / 3));
        }
    }
};
```

## 1585. Check If String Is Transformable With Substring Sort Operations*
Given two strings s and t, you want to transform string s into string t using the following operation any number of times:
Choose a non-empty substring in s and sort it in-place so the characters are in ascending order.
For example, applying the operation on the underlined substring in "14234" results in "12344".
Return true if it is possible to transform string s into string t. Otherwise, return false.
A substring is a contiguous sequence of characters within a string.

Example:
Input: s = "84532", t = "34852"
Output: true
Explanation: You can transform s into t using the following sort operations:
"84532" (from index 2 to 3) -> "84352"
"84352" (from index 0 to 2) -> "34852"

Input: s = "34521", t = "23415"
Output: true
Explanation: You can transform s into t using the following sort operations:
"34521" -> "23451"
"23451" -> "23415"

Input: s = "12345", t = "12435"
Output: false

Input: s = "1", t = "2"
Output: false

Constraints:
s.length == t.length
1 <= s.length <= 105
s and t only contain digits from '0' to '9'.

Solution:

```cpp
class Solution {
public:
    bool isTransformable(string s, string t) {
        vector<vector<int>> idx(10);
        vector<int> count(10);
        for (int i = 0; i < int(s.length()); i++)
        {
            idx[s[i]-'0'].push_back(i);
        }
        for (int i = 0; i < int(t.length()); i++)
        {
            int digit = t[i] - '0';
            if (count[digit] == idx[digit].size()) { return false; }
            for (int j = 0; j < digit; j++)
            {
                if (count[j] != idx[j].size() &&
                    idx[j][count[j]] < idx[digit][count[digit]])
                {
                    return false;
                }
            }
            count[digit]++;
        }
        return true;
    }
};
```

## 1603. Design Parking System
ParkingSystem(int big, int medium, int small) Initializes object of the ParkingSystem class. The number of slots for each parking space are given as part of the constructor. bool addCar(int carType) Checks whether there is a parking space of carType for the car that wants to get into the parking lot. carType can be of three kinds: big, medium, or small, which are represented by 1, 2, and 3 respectively. A car can only park in a parking space of its carType. If there is no space available, return false, else park the car in that size space and return true.

```cpp
class ParkingSystem {
    vector<int> park;
public:
    ParkingSystem(int big, int medium, int small) {
        park.resize(3, 0);
        park[0] = big;
        park[1] = medium;
        park[2] = small;
    }
    
    bool addCar(int carType) {
        if (!park[carType-1]) { return false; }
        park[carType-1]--;
        return true;
    }
};

/**
 * Your ParkingSystem object will be instantiated and called as such:
 * ParkingSystem* obj = new ParkingSystem(big, medium, small);
 * bool param_1 = obj->addCar(carType);
 */
```

## 1748. Sum of Unique Elements
You are given an integer array nums. The unique elements of an array are the elements that appear exactly once in the array. Return the sum of all the unique elements of nums.

```cpp
/*
Input: nums = [1,2,3,2]
Output: 4
Explanation: The unique elements are [1,3], and the sum is 4.
*/
class Solution {
public:
    int sumOfUnique(vector<int>& nums) {
        int ans = 0;
        unordered_map<int, bool> dict;
        unordered_map<int, bool>::iterator iter;
        for (int ele : nums)
        {
            iter = dict.find(ele);
            if (iter == dict.end())
            {
                ans += ele;
                dict[ele] = true;
            }
            else if (dict[ele])
            {
                dict[ele] = false;
                ans -= ele;
            }
        }
        return ans;
    }
};
```

## 1848. 到目标元素的最小距离
```cpp
/*
给你一个整数数组 nums （下标 从 0 开始 计数）以及两个整数 target 和 start ，
请你找出一个下标 i ，满足 nums[i] == target 且 abs(i - start) 最小化 。
注意：abs(x) 表示 x 的绝对值。

返回 abs(i - start) 。

题目数据保证 target 存在于 nums 中。

示例 1：
输入：nums = [1,2,3,4,5], target = 5, start = 3
输出：1
解释：nums[4] = 5 是唯一一个等于 target 的值，所以答案是 abs(4 - 3) = 1 。

示例 2：
输入：nums = [1], target = 1, start = 0
输出：0
解释：nums[0] = 1 是唯一一个等于 target 的值，所以答案是 abs(0 - 0) = 1 。

示例 3：
输入：nums = [1,1,1,1,1,1,1,1,1,1], target = 1, start = 0
输出：0
解释：nums 中的每个值都是 1 ，但 nums[0] 使 abs(i - start) 的结果得以最小化，
所以答案是 abs(0 - 0) = 0 。
*/
class Solution {
public:
    int getMinDistance(vector<int>& nums, int target, int start) {
        int n = (int)nums.size(), ans = INT_MAX;
        for (int i = 0; i < n; i++) {
            if (nums[i] == target && ans > abs(i - start)) {
                ans = abs(i - start);
            }
        }
        return ans;
    }
};
```