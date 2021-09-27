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
