## 定义
回溯是一种计算机算法，也叫试探法，从初始状态开始访问所有状态，当无法沿着某一路径继续访问时，返回到上一个分叉路口。通常以递归的方式实现。


## 题目
### 单词分拆 I
给你一个字符串 s 和一个字符串列表 wordDict 作为字典。请你判断是否可以利用字典中出现的单词拼接出 s 。注意：不要求字典中出现的单词全部都使用，并且字典中的单词可以重复使用。输入: s = "leetcode", wordDict = ["leet", "code"]，输出: true。解释: 返回 true 因为 "leetcode" 可以由 "leet" 和 "code" 拼接成。

**「分析」**
「回溯 + 记忆化dp」

```cpp
class Solution {
public:
    unordered_map<string, bool> M;
    unordered_set<string> W;
    bool wordBreak(string s, vector<string>& wordDict) {
        for (const string &w : wordDict) {
            W.emplace(w);
        }
        return dfs(s);
    }
    bool dfs(string s) {
        if (M.count(s)) {
            return M[s];
        }
        bool ans = false;
        for (auto w : W) {
            const string &s1 = s.substr(0, w.length());
            if (s1 != w) {
                continue;
            }
            M[s1] = true;
            string s2 = s.substr(w.length(), s.length() - w.length());
            if (s2.empty()) {
                return true;
            }
            if (dfs(s2)) {
                M[s2] = true;
                return true;
            }
        }
        M[s] = ans;
        return ans;
    }
};
```
时间复杂度 $O(n 2^{n})$，空间复杂度 $O(n 2^{n})$，n 是单词的长度。


### 单词分拆 II
给定一个字符串 s 和一个字符串字典 wordDict ，在字符串 s 中增加空格来构建一个句子，使得句子中所有的单词都在词典中。以任意顺序 返回所有这些可能的句子。注意：词典中的同一个单词可能在分段中被重复使用多次。例如，输入:s = "catsanddog", wordDict = ["cat","cats","and","sand","dog"]，输出:["cats and dog","cat sand dog"]。


**「分析」**
「回溯 + 记忆化dp」

遍历 wordDict 的单词，并与 s 的前缀对比，如果匹配，递归地处理 s 的剩余部分。


```cpp
class Solution {
public:
    unordered_map<string, vector<string>> M;
    unordered_set<string> words;
    vector<string> wordBreak(string s, vector<string>& wordDict) {
        for (const string &w : wordDict) {
            words.emplace(w);
        }
        return dfs(s);
    }
    vector<string> dfs(string s) {
        vector<string> ans;
        if (s.empty()) {
            return ans;
        }
        if (M.count(s)) {
            return M[s];
        }
        
        for (auto w : words) {
            if (s.substr(0, w.length()) == w) {
                string s2 = s.substr(w.length(), s.length() - w.length());
                //printf("%s %s\n", w.c_str(), s2.c_str());
                if (s2.empty()) {
                    ans.emplace_back(w);
                    continue;
                }
                vector<string> t2 = dfs(s2);
                for (const string &t : t2) {
                    ans.emplace_back(w + " " + t);
                    //printf("%s\n", ans.back().c_str());
                }
            }
        }
        M[s] = ans;
        return ans;
    }
};
```
时间复杂度 $O(n 2^{n})$，空间复杂度 $O(n 2^{n})$，n 是单词的长度。
