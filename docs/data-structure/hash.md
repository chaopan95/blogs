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
时间复杂福：$O(1)$，空间复杂度：$O(n)$
