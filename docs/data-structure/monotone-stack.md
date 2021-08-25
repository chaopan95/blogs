## 简介
单调栈是一种特殊的数据结构，栈内元素值（严格）单调变化。

## 去重复字母
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


## 判断二叉搜索树的先序遍历
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
