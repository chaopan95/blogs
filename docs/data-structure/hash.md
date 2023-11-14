## 简介
哈希表是一种时间复杂度为 $O(1)$ 的数据结构


## 题目
### 数组中重复的数据
给出一个长度为 n 的数组，每一个元素的值位于 [1, n]，他们出现的次数为 1 次或 2 次，请找出所有的出现 2 次的元素。线性时间和常数空间。

**「分析」**

【原地哈希】每一个值都位于 [1, n]，那么这些值就可以成为元素下标，相同的值对应相同的下标。我们第一次访问的数据元素改写成相反数，如果遇到相同的下标，此时对应的值就是负数。

```cpp
vector<int> findDuplicates(vector<int>& nums) {
    vector<int> ans;
    for (int num : nums) {
        if (nums[abs(num) - 1] < 0) {
            ans.emplace_back(abs(num));
        } else {
            nums[abs(num) - 1] *= -1;
        }
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$

### 全 O(1) 结构
设一个数据结构，可以在平均时间复杂度 $O(1)$ 下实现如下功能：

1）对字符串 key 计数加一

2）对字符串 key 计数减一，如果减为 0，删除这个字符串

3）返回当前计数最大的任意一个字符串

4）返回当前计数最小的任意一个字符串

**「分析」**

【哈希表 + 双向链表】

```cpp
struct BiListNode {
    int val;
    BiListNode *prev, *next;
    BiListNode(int x) : val(x), prev(nullptr), next(nullptr) {}
};

class AllOne {
    unordered_map<string, int> str2int;
    unordered_map<int, BiListNode*> int2node;
    unordered_map<int, unordered_set<string>> int2str;
    BiListNode *head, *tail;
public:
    AllOne() {
        head = new BiListNode(0);
        tail = new BiListNode(INT_MAX);
        head->next = tail;
        tail->prev = head;
    }
    
    void deleteNode(BiListNode *prev, BiListNode *cur) {
        prev->next = cur->next;
        cur->next->prev = prev;
        delete cur;
    }
    
    void addNode(BiListNode *prev, BiListNode *cur) {
        cur->next = prev->next;
        cur->prev = prev;
        prev->next->prev = cur;
        prev->next = cur;
    }
    
    void inc(string key) {
        if (str2int.count(key)) {
            int cnt = str2int[key];
            str2int[key]++;
            int2str[cnt + 1].emplace(key);
            if (!int2node.count(cnt + 1)) {
                BiListNode *node = new BiListNode(cnt + 1);
                addNode(int2node[cnt], node);
                int2node[cnt + 1] = node;
            }
            int2str[cnt].erase(key);
            if (int2str[cnt].empty()) {
                int2str.erase(cnt);
                deleteNode(int2node[cnt]->prev, int2node[cnt]);
                int2node.erase(cnt);
            }
        } else {
            str2int[key] = 1;
            int2str[1].emplace(key);
            if (!int2node.count(1)) {
                BiListNode *node = new BiListNode(1);
                addNode(head, node);
                int2node[1] = node;
            }
        }
    }
    
    void dec(string key) {
        int cnt = str2int[key];
        if (cnt == 1) {
            str2int.erase(key);
        } else {
            str2int[key]--;
            int2str[cnt-1].emplace(key);
            if (!int2node.count(cnt - 1)) {
                BiListNode *node = new BiListNode(cnt - 1);
                addNode(int2node[cnt]->prev, node);
                int2node[cnt - 1] = node;
            }
        }
        int2str[cnt].erase(key);
        if (int2str[cnt].empty()) {
            int2str.erase(cnt);
            deleteNode(int2node[cnt]->prev, int2node[cnt]);
            int2node.erase(cnt);
        }
    }
    
    string getMaxKey() {
        if (int2node.empty()) {
            return "";
        }
        int val = tail->prev->val;
        return *int2str[val].begin();
    }

    string getMinKey() {
        if (int2node.empty()) {
            return "";
        }
        int val = head->next->val;
        return *int2str[val].begin();
    }
};
```
时间复杂度：$O(1)$，空间复杂度：$O(n)$

### 字符流中第一个不重复的字符
请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。如果当前字符流没有存在出现一次的字符，返回#字符。

**「分析」**

用一个长为 256 的数组表示 ACSII 字符，如果重复的字符出现，把对应的位置置成 -1，最后返回最小的索引。

```C++
class Solution {
    int *ascii;
    int N;
    int flow;
public：
    Solution() {
        N = 256;
        ascii = new int [N]{};
        flow = 1;
    }
    ~Solution() { delete []ascii; }
    //Insert one char from stringstream
    void Insert(char ch) {
        int idx = int(ch);
        if (ascii[idx] == 0) { ascii[idx] = flow; }
        else { ascii[idx] = -1; }
        flow++;
    }
  //return the first appearence once char in current stringstream
    char FirstAppearingOnce() {
        int pos = flow, ch = 0;
        for (int i = 0; i < N; i++) {
            if (ascii[i] < 1) { continue; }
            if (pos > ascii[i]) {
                pos = ascii[i];
                ch = i;
            }
        }
        if (pos == flow) { return '#'; }
        return char(ch);
    }
};
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$

### 缺失的第一个正数
给你一个未排序的整数数组 nums ，请你找出其中没有出现的最小的正整数。请你实现时间复杂度为 O(n) 并且只使用常数级别额外空间的解决方案。例如，[3,4,-1,1] 返回 2。

**「分析」**

「原地哈希」

$$
\begin{matrix}
3 & 4 & -1 & 1 \\
 & & \Downarrow & \\
3 & 4 & 5 & 1 \\
 & & \Downarrow & \\
3 & 4 & -5 & 1 \\
 & & \Downarrow & \\
3 & 4 & -5 & -1 \\
 & & \Downarrow & \\
-3 & 4 & -5 & -1
\end{matrix}
$$

```cpp
int firstMissingPositive(vector<int>& nums) {
    int n = int(nums.size());
    for (int i = 0; i < n; i++) {
        if (nums[i] <= 0) { nums[i] = n + 1; }
    }
    for (int i = 0; i < n; i++) {
        int num = abs(nums[i]);
        if (num <= n) {
            nums[num-1] = -abs(nums[num-1]);
        }
    }
    for (int i = 0; i < n; i++) {
        if (nums[i] > 0) {
            return i + 1;
        }
    }
    return n + 1;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$。

### 存在重复元素
给你一个整数数组 nums 和两个整数 k 和 t 。找出满足下述条件的下标对 (i, j)：

i != j,

abs(i - j) <= k

abs(nums[i] - nums[j]) <= t

如果存在，返回 true ；否则，返回 false 。

**「分析」**

「哈希表」

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
时间复杂度：$O(n \log n)$，空间复杂度：$O(n)$。

### 最小覆盖子串
给你一个字符串 s 、一个字符串 t 。返回 s 中涵盖 t 所有字符的最小子串。如果 s 中不存在涵盖 t 所有字符的子串，则返回空字符串 "" 。

**「分析」**

「滑动窗口 + 哈希表」

```cpp
/*
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
*/
class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<char, int> tDict, sDict;
        for (const auto &c : t) {
            tDict[c]++;
        }
        int ns = int(s.length());
        int l = 0, r = -1, ansL = -1, minLen = INT_MAX;
        while (r < ns) {
            if (tDict.find(s[++r]) != tDict.end()) {
                sDict[s[r]]++;
            }
            while (isCover(tDict, sDict) && l <= r) {
                if (r - l + 1 < minLen) {
                    minLen = r - l + 1;
                    ansL = l;
                }
                if (tDict.find(s[l]) != tDict.end()) {
                    sDict[s[l]]--;
                }
                l++;
            }
        }
        return -1 == ansL ? string() : s.substr(ansL, minLen);
    }
    bool isCover(unordered_map<char, int> &tDict,
                 unordered_map<char, int> &sDict) {
        for (const auto &item : tDict) {
            if (sDict[item.first] < item.second) {
                return false;
            }
        }
        return true;
    }
};
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$。

### 最长连续序列
给定一个未排序的整数数组 nums ，找出数字连续的最长序列（不要求序列元素在原数组中连续）的长度。请你设计并实现时间复杂度为 O(n) 的算法解决此问题。例如，nums = [100,4,200,1,3,2]，返回 4，最长连续序列是 [1, 2, 3, 4]。

**「分析」**

「哈希表」

```cpp
int longestConsecutive(vector<int>& nums) {
    if (nums.empty()) { return 0; }
    int ans = 1;
    unordered_set<int> hash;
    for (const auto &ele : nums) { hash.insert(ele); }
    for (const int &ele : nums) {
        if (hash.find(ele - 1) != hash.end()) {
            int curNum = ele;
            int len = 1;
            while (hash.find(curNum) != hash.end()) {
                curNum++;
                len++;
            }
            ans = max(ans, len);
        }
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$。

### 至少有 K 个重复字符的最长子串
给你一个字符串 s 和一个整数 k ，请你找出 s 中的最长子串， 要求该子串中的每一字符出现次数都不少于 k 。返回这一子串的长度。如果不存在这样的子字符串，则返回 0。例如，s = "ababbc", k = 2，返回 5，因为最长子串为 "ababb" ，其中 'a' 重复了 2 次， 'b' 重复了 3 次。

**「分析」**

「哈希表」

```cpp
int longestSubstring(string s, int k) {
    int n = int(s.length()), ans = 0;
    for (int t = 1; t <= 26; t++) {
        int l = 0, r = 0, tot = 0, numLessK = 0;
        int *count = new int [26]{};
        while (r < n) {
            count[s[r] - 'a']++;
            if (count[s[r] - 'a'] == 1) {
                tot++;
                numLessK++;
            }
            if (count[s[r] - 'a'] == k) {
                numLessK--;
            }
            while (tot > t) {
                count[s[l] - 'a']--;
                if (count[s[l] - 'a'] == k-1) {
                    numLessK++;
                }
                if (count[s[l] - 'a'] == 0) {
                    numLessK--;
                    tot--;
                }
                l++;
            }
            if (numLessK == 0) {
                ans = max(ans, r - l + 1);
            }
            r++;
        }
        delete []count;
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$。
