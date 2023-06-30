## 基础
在某些问题里，一个位置的约束条件通常涉及到当前位置的左右两侧，这时候，我们将约束拆分，分别从左到右、从右到左遍历，最后结合预处理的结果求解。

## 分发糖果
给定一个列表 ratings = [1, 0, 2] 表示每一个位置的分数，先要给每一个位置分发糖果，如果某一个位置 i 的评分比两侧的分数都高的话，它的糖果数目也要高于两侧的糖果数目。试问最少准备多少糖果？

**「分析」**

【动态规划 + 两次遍历】我们将规则拆开，从左到右遍历，比较 i 和 i - 1 的分数，记录 i 的糖果数目，再从右到左遍历，比较 i 和 i + 1 的分数，记录 i 的糖果数目，最终的数目是 max(left, right)。

```cpp
int candy(vector<int>& ratings) {
    int n = (int)ratings.size();
    vector<int> left(n, 1);
    for (int i = 1; i < n; i++) {
        if (ratings[i] > ratings[i - 1]) {
            left[i] = left[i - 1] + 1;
        }
        else {
            left[i] = 1;
        }
    }
    int right = 1, ans = max(left[n - 1], right);
    for (int i = n - 2; i >= 0; i--) {
        if (ratings[i] > ratings[i + 1]) {
            right++;
        }
        else {
            right = 1;
        }
        ans += max(left[i], right);
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$

## 最小删除次数
给定一个只由 a 和 b 构成的字符串 s = "aababbab"，试问最小删除多少个字符，可以使得删除后的字符串满足所有的 a 排在 b 的前面？

**「分析」**

【动态规划】对于每一个位置，统计位于它之前的所有 a 和位于它之后的所有 b，最终求最大的和

```cpp
int minimumDeletions(string s) {
    int ans = 0, n = (int)s.length();
    vector<int> left(n, 0), right(n, 0);
    for (int i = 1; i < n; i++) {
        left[i] += left[i - 1] + (s[i - 1] == 'a');
        right[n - 1 - i] += right[n - i] + (s[n - i] == 'b');
    }
    
    for (int i = 0; i < n; i++) {
        ans = max(ans, left[i] + right[i]);
    }
    return n - ans - 1;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$

## 移除所有 1 的最小时间
给定一个只有 0 和 1 的字符串 s = "01110"，每次可以从 s 的左右两端移除一个字符，花费 1 单位时间，或者移除中间的某一个字符，花费 2 单位时间。试问，移除所有 1 需要最少多少时间？

**「分析」**

【动态规划】因为两端移除字符，因此考虑两端 dp。设 left[ i ] 表示前 i 个字符需要花费的最少时间，right[ i ] 表示后 i 个字符需要花费最少时间。

$$
\begin{aligned}
&
\text{left[ i ]} =
\begin{cases}
\text{left[i - 1]}, & \quad s[ i ] == 0 \\
\min(\text{left[i - 1] + 2}, i), & \quad \text{otherwise}
\end{cases} \\
&
\text{right[ i ]} =
\begin{cases}
\text{right[i - 1]}, & \quad s[ i ] == 0 \\
\min(\text{right[i - 1] + 2}, i), & \quad \text{otherwise}
\end{cases}
\end{aligned}
$$

最终在某一个位置取得最小的结果

```cpp
int minimumTime(string s) {
    int ans = INT_MAX, n = (int)s.length();
    vector<int> left(n + 1, 0);
    for (int i = 0; i < n; i++) {
        if (s[i] == '0') {
            left[i + 1] = left[i];
        } else {
            left[i + 1] = min(left[i] + 2, i + 1);
        }
    }
    vector<int> right(n + 1, 0);
    for (int i = n - 1; i >= 0; i--) {
        if (s[i] == '0') {
            right[i] = right[i + 1];
        } else {
            right[i] = min(right[i + 1] + 2, n - i);
        }
    }
    for (int i = 0; i <= n; i++) {
        ans = min(ans, left[i] + right[i]);
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$
