## 简介
单调栈是一种特殊的数据结构，栈内元素值（严格）单调变化。

单调栈适用于求解「Next Greater Number」这一类问题
给定一个序列 e.g. {1,3,4,2}，求每一个数字右侧第一个比自己大的数字，如果没有返回-1，模板如下
```cpp
vector<int> nextGreaterNumber(vector<int>& nums) {
    vector<int> ans(nums.size(), -1), stk;
    for (int i = 0; i < nums.size(); i++) {
        while (!stk.empty() && nums[stk.back()] < nums[i]) {
            ans[stk.back()] = nums[i];
            stk.pop_back();
        }
        stk.emplace_back(i);
    }
    return ans;
}
```
时间复杂度 $O(n)$，空间复杂度 $O(n)$

进一步，如果是循环数组（某一位置的元素从右侧开始查找为数组尾后，继续从数组头查找）时，只需要这个数组后面拼接自己即可，模板如下
```cpp
vector<int> nextGreaterNumber(vector<int>& nums) {
    vector<int> ans(nums.size(), -1), stk;
    for (int i = 0; i < nums.size() * 2; i++) {
        int j = i % nums.size();
        while (!stk.empty() && nums[stk.back()] < nums[j]) {
            ans[stk.back()] = nums[j];
            stk.pop_back();
        }
        stk.emplace_back(j);
    }
    return ans;
}
```
时间复杂度 $O(n)$，空间复杂度 $O(n)$


## 题目
### 最长正值区间
给定一个序列e.g.[1,3,-2,-4,1]，请找出最大的一个子区间，使得这个区间元素的和为正数

「分析」
容易想到「二分+滑动窗口」的方法，这里「二分」导致错误的原因是：正值区间的长度不是单调的，即无法根据一个中间值，确定下一步进入到左区间还是右区间。
本题的方法是「前缀和+单调栈」，「前缀和」求解区间和(i, j)，我们希望这个区间|j - i|可以最大。如果固定区间的左端点i，要使|j - i|尽可能的大，就要求j对应的数字比i对应的数字大，也就是说找到最后一个j

$$\text{presum}[j_{\text{max}}] - \text{presum}[i] \geq 0$$

可以利用「单调栈」实现，只要对「Next Greater Number」略加改动即可

```cpp
int longest_postive_value_interval(vector<int>& nums) {
    vector<int> presum(nums.size() + 1), stk {0};
    for (int i = 0; i < nums.size(); i++) {
        presum[i + 1] = presum[i];
        if (nums[i] > 8) {
            presum[i + 1] += 1;
        } else {
            presum[i + 1] += -1;
        }
        if (presum[stk.back()] > presum[i + 1]) {
            stk.emplace_back(i + 1);
        }
    }
    int ans = 0;
    for (int i = (int)presum.size() - 1; i > 0; i--) {
        while (!stk.empty() && presum[stk.back()] < presum[i]) {
            ans = max(ans, i - stk.back());
            stk.pop_back();
        }
    }
    return ans;
}
```
时间复杂度 $O(n)$，空间复杂度 $O(n)$

### 下下个更大的元素
给定一个序列[2,4,0,9,6]，请找出每个元素右侧第二个大于自身的元素

「分析」
一个「单调栈」可以决解「Next Greater Number」，两个单调栈可以解决「Next Next Greater Number」。我们有两个「单调栈」s1、s2筛选出来更小的栈定元素。

```cpp
vector<int> nextNextGreaterNumber(vector<int>& nums) {
    vector<int> ans(nums.size(), -1), s1, s2;
    for (int i = 0; i < nums.size(); i++) {
        while (!s2.empty() && nums[s2.back()] < nums[i]) {
            ans[s2.back()] = nums[i];
            s2.pop_back();
        }
        int j = (int)s1.size() - 1;
        while (j >= 0 && nums[s1[j]] < nums[i]) {
            j--;
        }
        s2.insert(s2.end(), s1.begin() + (j + 1), s1.end());
        s1.resize(j + 1);
        s1.emplace_back(i);
    }
    return ans;
}
```
时间复杂度 $O(n)$，空间复杂度 $O(n)$

### 去重复字母
给定一个字符串 s，将 s 中的所有重复字母去掉，返回一个不改变原字符串内字母相对位置且字典序最小的无重复字符串。如 s = cbacdcbc -> s' = acdb

**「分析」**

考虑一个简单的情况：去除一个字符 ch，使得 s' 的字典序列最小。方法是找到最小的 i 满足 

$$
i = \arg \min_{i \in [0, n - 1)} \text{s[ i ]} \geq \text{s[i + 1]}
$$

只需要重复上述的方法即可，但这样的时间复杂度是 $O(n^{2})$。我们发现，有些字符可以一次性找到，这就用到了【单调栈】。

```cpp
string removeDuplicateLetters(string s) {
    string ans = "";
    vector<int> cnt(26, 0);
    bool *in = new bool [26]{};
    for (char ch : s) {
        cnt[ch - 'a']++;
    }
    int n = (int)s.length();
    for (int i = 0; i < n; i++) {
        if (!in[s[i] - 'a']) {
            while (!ans.empty() && ans.back() >= s[i] &&
                   cnt[ans.back() - 'a']) {
                in[ans.back() - 'a'] = false;
                ans.pop_back();
            }
            ans.push_back(s[i]);
            in[s[i] - 'a'] = true;
        }
        cnt[s[i] - 'a']--;
    }
    delete []in;
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(\Sigma)$，这里的 $\Sigma$ 指的是字符的集合，本题是 26。

### 判断二叉搜索树的先序遍历
给定一个二叉搜索树的遍历序列，判断是否是先序遍历序列

**「分析」**

【单调栈】先序遍历满足 根节点 - 左子树 - 右子树 这样的结构，并且以递归的方式呈现，即左右子树也会有类似的结构。从左到右依次遍历，我们发现，以第一个大于根节点的元素为分界线，左侧的均小于根节点，右侧的均大于根节点。于是，我们把小于根节点的元素都加入到栈中，直到遇到大于栈顶的元素，一次把栈内的元素弹出。此外，我们需要保存根节点的大小，用于比较右子树的值。

```cpp
bool verifyPreorder(vector<int>& preorder) {
    vector<int> stk;
    int MIN = INT_MIN;
    for (auto val : preorder) {
        if (val < MIN) { return false; }
        while (!stk.empty() && stk.back() < val) {
            MIN = stk.back();
            stk.pop_back();
        }
        stk.emplace_back(val);
    }
    return true;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$

### 移除 k 个字符后的最小值
给定一个只包含数字的字符串 num = “10200” 和一个整数 k = 1，删除 k 个字符，使得剩下的数字值最小，结果不包含前置 0.

**「分析」**

【单调栈】考虑删除一个字符的情况，以 “14235” 为例，只有删除 “4” 时，得到的值最小，我们发现删除的字符要比它的后一个字符大，并且被删除的字符位置尽可能靠前。如果每次扫描删除一个，总共需要 $O(kn)$ 的时间复杂度，我们使用【单调栈】来优化。

```cpp
string removeKdigits(string num, int k) {
    string ans = "";
    for (char ch : num) {
        while (k && !ans.empty() && ans.back() > ch) {
            ans.pop_back();
            k--;
        }
        if (ans.empty() && ch == '0') { continue; }
        ans.push_back(ch);
    }
    while (k-- && !ans.empty()) {
        ans.pop_back();
    }
    return ans.empty() ? "0" : ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$

### 柱状图中最大的矩形
给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。求在该柱状图中，能够勾勒出来的矩形的最大面积。

**「分析」**

「单调栈」

```cpp
/*
Input: heights = [2,1,5,6,2,3]
Output: 10
Explanation: The above is a histogram where width of each bar is 1.
The largest rectangle is shown in the red area, which has an area = 10 units.
*/

int largestRectangleArea(vector<int>& heights) {
    int ans = 0, n = int(heights.size());
    if (n == 0) { return ans; }
    heights.emplace_back(0);
    stack<int> stk;
    for (int i = 0; i <= n; i++) {
        while (!stk.empty() && heights[stk.top()] > heights[i]) {
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
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$。

### 最大矩形
给定一个仅包含 0 和 1 、大小为 rows x cols 的二维二进制矩阵，找出只包含 1 的最大矩形，并返回其面积。

$$
\begin{matrix}
1 & 0 & 1 & 0 & 0 \\
1 & 0 & \color{red}1 & \color{red}1 & \color{red}1 \\
1 & 1 & \color{red}1 & \color{red}1 & \color{red}1 \\
1 & 0 & 0 & 1 & 0
\end{matrix}
$$

最大面积是 6

**「分析」**

「单调栈」将矩阵压缩成一行，每个位置的值表示当前列连续的 1 的个数。后续对这一列求最大面积。

```cpp
class Solution {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        int ans = 0;
        int nRow = int(matrix.size());
        if (nRow == 0) { return ans; }
        int nCol = int(matrix[0].size());
        vector<int> heights(nCol, 0);
        for (int i = 0; i < nRow; i++) {
            for (int j = 0; j < nCol; j++) {
                heights[j] = (heights[j] + 1) * (matrix[i][j] - '0');
            }
            ans = max(ans, getRecArea(heights));
        }
        return ans;
    }
    int getRecArea(vector<int> heights) {
        int ans = 0, n = int(heights.size());
        stack<int> stk;
        heights.emplace_back(0);
        for (int i = 0; i <= n; i++) {
            while (!stk.empty() && heights[stk.top()] > heights[i]) {
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
时间复杂度：$O(n^{2})$，空间复杂度：$O(n)$。
