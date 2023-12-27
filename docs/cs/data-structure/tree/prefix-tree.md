## 前缀树
前缀树（字典树）是一种存储和查询都高效的数据结构，通常用于字符串数据，且公共前缀越多，效率越高。前缀树本质上是多叉树。如果我们知道一组数据中字符的全集 $\Sigma$，那么我们可以对每一个节点设置同等数目的分支。

```cpp
struct TrieNode {
    char val;
    vector<TrieNode*> nexts;
    bool isEnd;
    TrieNode() {
        val = ' ';
        nexts.resize(26, nullptr);
        isEnd = false;
    }
    TrieNode(char x) : val(x) {
        nexts.resize(26, nullptr);
        isEnd = false;
    }
};


class Trie {
    TrieNode *root;
public:
    Trie() {
        root = new TrieNode();
    }
    
    void insert(string word) {
        TrieNode *cur = root;
        for (const char &ch : word) {
            int idx = ch - 'a';
            if (cur->nexts[idx] == nullptr) {
                cur->nexts[idx] = new TrieNode(ch);
            }
            cur = cur->nexts[idx];
        }
        cur->isEnd = true;
    }
    
    bool search(string word) {
        TrieNode *cur = root;
        for (const char &ch : word) {
            if (cur == nullptr || cur->nexts[ch - 'a'] == nullptr) {
                return false;
            }
            cur = cur->nexts[ch - 'a'];
        }
        return cur->isEnd;
    }
    
    bool startsWith(string prefix) {
        TrieNode *cur = root;
        for (const char &ch : prefix) {
            if (cur == nullptr || cur->nexts[ch - 'a'] == nullptr) {
                return false;
            }
            cur = cur->nexts[ch - 'a'];
        }
        return true;
    }
};
```
空间复杂度：$O(\Sigma \times n)$；插入的时间复杂度：$O(n)$，查询的时间复杂度：$O(n)$。n 为插入/查询的字符串的长度，$\Sigma$ 为字符的集合个数


## 题目
### 数组中两个数的最大异或值
给你一个整数数组 nums ，返回 nums[i] XOR nums[j] 的最大运算结果，其中 0 ≤ i ≤ j < n 。

**「分析」**

【前缀树】将每一个数表示成二进制形式，从高位到低位插入到一颗前缀树中。每一个后续的数尽可能地找到每一位上不同的值。

```cpp
struct BinaryTrieNode {
    int val;
    bool isEnd;
    vector<BinaryTrieNode*> nexts;
    BinaryTrieNode(int x) {
        val = x;
        nexts.resize(2, nullptr);
    }
};

class BinaryTrie {
    BinaryTrieNode *root;
public:
    BinaryTrie() {
        root = new BinaryTrieNode(0);
    }
    void insert(int n) {
        BinaryTrieNode *cur = root;
        for (int i = 30; i >= 0; i--) {
            int val = (n >> i) & 1;
            
            if (cur->nexts[val] == nullptr) {
                cur->nexts[val] = new BinaryTrieNode(val);
            }
            cur = cur->nexts[val];
        }
    }
    int search(int n) {
        BinaryTrieNode *cur = root;
        int ans = 0;
        for (int i = 30; i >= 0; i--) {
            int val = 1 - ((n >> i) & 1);
            if (cur->nexts[val] == nullptr) {
                val = 1 - val;
            }
            if (cur->nexts[val] == nullptr) {
                break;
            }
            ans |= val * (1 << i);
            cur = cur->nexts[val];
        }
        return ans;
    }
};

class Solution {
public:
    int findMaximumXOR(vector<int>& nums) {
        BinaryTrie bt;
        int ans = 0;
        for (int num : nums) {
            bt.insert(num);
            ans = max(ans, bt.search(num) ^ num);
        }
        return ans;
    }
};
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$

### 连接词
给你一个 不含重复 单词的字符串数组 words ，请你找出并返回 words 中的所有 连接词 。连接词 定义为：一个完全由给定数组中的至少两个较短单词组成的字符串。例如，words = ["cat", "dog", "catdog"]，返回结果 ["catdog"]

**「分析」**

【字典树 + DFS】将 words 按照字符串的长度由小到大排序，这样保证所有的可能组成其他单词的“小单词”出现在前面

```cpp
struct TrieNode {
    char val;
    vector<TrieNode*> nexts;
    bool isEnd;
    TrieNode() {
        val = ' ';
        nexts.resize(26, nullptr);
        isEnd = false;
    }
    TrieNode(char x) : val(x) {
        nexts.resize(26, nullptr);
        isEnd = false;
    }
};

class Trie {
    TrieNode *root;
public:
    Trie() {
        root = new TrieNode();
    }
    
    void insert(string word) {
        TrieNode *cur = root;
        for (const char &ch : word) {
            int idx = ch - 'a';
            if (cur->nexts[idx] == nullptr) {
                cur->nexts[idx] = new TrieNode(ch);
            }
            cur = cur->nexts[idx];
        }
        cur->isEnd = true;
    }
    
    bool search(string word) {
        TrieNode *cur = root;
        for (const char &ch : word) {
            if (cur == nullptr || cur->nexts[ch - 'a'] == nullptr) {
                return false;
            }
            cur = cur->nexts[ch - 'a'];
        }
        return cur->isEnd;
    }
    
    bool startsWith(string prefix) {
        TrieNode *cur = root;
        for (const char &ch : prefix) {
            if (cur == nullptr || cur->nexts[ch - 'a'] == nullptr) {
                return false;
            }
            cur = cur->nexts[ch - 'a'];
        }
        return true;
    }
    
    bool search(string word, int b) {
        if (b == word.length()) {
            return true;
        }
        TrieNode *cur = root;
        for (int i = b; i < word.length(); i++) {
            char ch = word[i];
            if (cur == nullptr || cur->nexts[ch - 'a'] == nullptr) {
                return false;
            }
            cur = cur->nexts[ch - 'a'];
            if (cur->isEnd && search(word, i + 1)) {
                return true;
            }
        }
        return false;
    }
};


class Solution {
public:
    vector<string> findAllConcatenatedWordsInADict(vector<string>& words) {
        vector<string> ans;
        sort(words.begin(), words.end(), [](const string &s1, const string &s2){
            return s1.length() < s2.length();
        });
        Trie t;
        for (string word : words) {
            if (word.empty()) {
                continue;
            }
            if (t.search(word, 0)) {
                ans.emplace_back(word);
            } else {
                t.insert(word);
            }
        }
        return ans;
    }
};
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(n)$
