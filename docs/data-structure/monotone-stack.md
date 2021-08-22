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
