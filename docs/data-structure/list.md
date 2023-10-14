## 简介
链表是一种寻位访问的数据结构，以指针的方式，将两块数据连接起来。存储空间上通常是不连续的，增删的时间复杂度为 $O(1)$，查找的时间复杂度为 $O(n)$。通常而言，链表有如下几种

* 单链表

* 双链表

* 循环链表

## 链表结点设计
结点包含两个部分：

* 数据域

* 指针域

对单链表而言，其结点的定义方式如下

```cpp
struct ListNode {
    int val;
    ListNode *next;
    ListNode(): val(0), next(nullptr) {}
    ListNode(int x): val(x), next(nullptr) {}
    ListNode(int x, ListNode *next): val(x), next(next) {}
};
```


## 题目
### 反转链表
```cpp
/*
from 1 -> 2 -> 3 -> 4 -> 5
to   5 -> 4 -> 3 -> 2 -> 1
*/
// 迭代
ListNode* reverseList(ListNode* head) {
    if (head == nullptr || head->next == nullptr) { return head; }
    ListNode *p1 = head, *p2 = head->next, *p3 = head->next->next;
    while (p3 != nullptr) {
        p2->next = p1;
        p1 = p2;
        p2 = p3;
        p3 = p3->next;
    }
    p2->next = p1;
    head->next = nullptr;
    return p2;
}

// 递归
ListNode* reverseList(ListNode* head) {
    if (head == nullptr || head->next == nullptr) { return head; }
    ListNode *ans = reverse(head, head->next);
    head->next = nullptr;
    return ans;
}
ListNode *reverse(ListNode *p1, ListNode *p2) {
    if (p2->next != nullptr) {
        ListNode *p3 = p2->next;
        p2->next = p1;
        return reverse(p2, p3);
    }
    p2->next = p1;
    return p2;
}
```
时间复杂度 $O(n)$，递归版本的空间复杂度 $O(\log n)$，迭代版本的空间复杂度 $O(1)$


### 找出两个链表的相交结点
给定两个链表A、B，找出两者相交的结点，如果不相交，返回为空指针。

$$
\begin{matrix}
1 & \rightarrow & 2 & \rightarrow & 3 & \rightarrow & 4 \\
& & & & & & & \searrow \\
& & & & & & & & {\color{Red} 8} & \rightarrow & 9 & \rightarrow & 10 \\
& & & & & & & \nearrow \\
& & 5 & \rightarrow & 6 & \rightarrow & 7
\end{matrix}
$$

**「分析」**

朴素的方法是将这两个链表用栈保存起来，比对最后一个结点，如果不相同，则必定不相交，如果相同，反向遍历栈即可找到第一个相交的结点。但是常数空间的算法是存在的。问题的难点在于两个链表的长度不一致，但是这两个链表的长度和是一定的，即我们先遍历链表 pHead1 再遍历链表 pHead2 与先遍历 pHead2 再遍历 pHead1 的长度是一样的。那么，如果两个链表是在某个结点相交的，按照上述的遍历方法，两个指针一定在相交的结点相会。

首先，初始状态下，两个指针分别处于两个链表的头结点

$$
\begin{matrix}
p_{1} \\
\Downarrow \\
1 & \rightarrow & 2 & \rightarrow & 3 & \rightarrow & 4 \\
& & & & & & & \searrow \\
& & & & & & & & {\color{Red} 8} & \rightarrow & 9 & \rightarrow 10 \\
& & & & & & & \nearrow \\
& & 5 & \rightarrow & 6 & \rightarrow & 7 \\
& & \Uparrow \\
& & p_{2}
\end{matrix}
$$

$p_{1}$ 和 $p_{2}$ 同时向后移动直到某一个率先到达末结点。本例中，$p_{2}$ 先到末结点，转而从 pHead1 的头结点开始遍历。

$$
\begin{matrix}
1 & \rightarrow & 2 & \rightarrow & 3 & \rightarrow & 4 & & & & & & p_{1} \\
& & & & & & & \searrow & & & & & \Downarrow \\
& & & & & & & & {\color{Red} 8} & \rightarrow & 9 & \rightarrow & 10 \\
& & & & & & & \nearrow & & & & & & \Uparrow \\
& & 5 & \rightarrow & 6 & \rightarrow & 7 & & & & & & & p_{2}
\end{matrix}
$$

$$
\begin{matrix}
p_{2} \\
\Downarrow \\
1 & \rightarrow & 2 & \rightarrow & 3 & \rightarrow & 4 & & & & & & & p_{1} \\
& & & & & & & \searrow & & & & & & \Downarrow \\
& & & & & & & & {\color{Red} 8} & \rightarrow & 9 & \rightarrow & 10 \\
& & & & & & & \nearrow \\
& & 5 & \rightarrow & 6 & \rightarrow & 7
\end{matrix}
$$

同样地，$p_{1}$ 也会到达末结点，转而从 phead2 的头结点开始遍历。这个时候我们就会发现，$p_{1}$ 和 $p_{2}$ 处在相同位置，这意味着它们可以同时达到各自的尾结点。

$$
\begin{matrix}
& & p_{2} \\
& & \Downarrow \\
1 & \rightarrow & 2 & \rightarrow & 3 & \rightarrow & 4 \\
& & & & & & & \searrow \\
& & & & & & & & {\color{Red} 8} & \rightarrow & 9 & \rightarrow 10 \\
& & & & & & & \nearrow \\
& & 5 & \rightarrow & 6 & \rightarrow & 7 \\
& & \Uparrow \\
& & p_{1}
\end{matrix}
$$

最终可以找到相交位置处的结点

$$
\begin{matrix}
1 & \rightarrow & 2 & \rightarrow & 3 & \rightarrow & 4 & & p_{2} \\
& & & & & & & \searrow & \Downarrow \\
& & & & & & & & {\color{Red} 8} & \rightarrow & 9 & \rightarrow 10 \\
& & & & & & & \nearrow & \Uparrow \\
& & 5 & \rightarrow & 6 & \rightarrow & 7 & & p_{1}
\end{matrix}
$$

```cpp
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
    if (headA == nullptr || headB == nullptr) {
        return nullptr;
    }
    ListNode *p1 = headA, *p2 = headB;
    for (int i = 0; i < 3; i++) {
        while (p1 != nullptr && p2 != nullptr) {
            if (p1 == p2) { return p1; }
            p1 = p1->next;
            p2 = p2->next;
        }
        if (p1 == nullptr) { p1 = headB; }
        if (p2 == nullptr) { p2 = headA; }
    }
    return nullptr;
}
```
时间复杂度 $O(n)$，空间复杂度 $O(1)$


### 链表的中心节点
给定一个单链表，返回链表的中心节点，如果链表的结点个数是偶数，则返回靠后位置的结点。

**「分析」**

使用【快慢指针】方法

```cpp
ListNode* middleNode(ListNode* head) {
    ListNode *p1 = head, *p2 = head;
    while (p2 != nullptr) {
        p2 = p2->next;
        if (p2 == nullptr) {
            return p1;
        }
        p2 = p2->next;
        p1 = p1->next;
    }
    return p1;
}
```
时间复杂度 $O(n)$，空间复杂度 $O(1)$


### LRU 缓存机制
```cpp
/*
运用你所掌握的数据结构，设计和实现一个LRU(最近最少使用)缓存机制 。
实现 LRUCache 类：

LRUCache(int capacity) 以正整数作为容量 capacity 初始化 LRU 缓存
int get(int key) 如果关键字 key 存在于缓存中，则返回关键字的值，否则返回 -1 。
void put(int key, int value) 如果关键字已经存在，则变更其数据值；如果关键
字不存在，则插入该组「关键字-值」。当缓存容量达到上限时，它应该在写入新数据之前
删除最久未使用的数据值，从而为新的数据值留出空间。

输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
*/
struct BiNode {
    int key, val;
    BiNode *prec, *next;
    BiNode(int k, int v): key(k), val(v), prec(nullptr), next(nullptr) {}
};


class LRUCache {
    BiNode *head;
    BiNode *tail;
    int cap;
    unordered_map<int, BiNode*> hash;
public:
    LRUCache(int capacity) {
        cap = capacity;
        head = new BiNode(0, 0);
        tail = new BiNode(0, 0);
        head->next = tail;
        tail->prec = head;
    }
    ~LRUCache () {
        BiNode *curNode = head->next;
        while (curNode != nullptr) {
            delete curNode->prec;
            curNode = curNode->next;
        }
        delete tail;
    }
    
    int get(int key) {
        if (hash.find(key) != hash.end()) {
            BiNode *node = hash[key];
            int value = node->val;
            isolateNode(node);
            setFirstNode(node);
            return value;
        }
        else { return -1; }
    }
    
    void put(int key, int value) {
        BiNode *node;
        if (hash.find(key) == hash.end()) {
            node = new BiNode(key, value);
            cap--;
        }
        else {
            node = hash[key];
            node->val = value;
            isolateNode(node);
        }
        setFirstNode(node);
        hash[key] = node;
        if (cap < 0) {
            hash.erase(tail->prec->key);
            deleteNode(tail->prec);
            cap++;
        }
    }
    
    void setFirstNode(BiNode *node) {
        head->next->prec = node;
        node->next = head->next;
        node->prec = head;
        head->next = node;
    }
    
    void isolateNode(BiNode *node) {
        node->prec->next = node->next;
        node->next->prec = node->prec;
    }
    
    void deleteNode(BiNode *node) {
        isolateNode(node);
        delete node;
    }
};
/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```
时间复杂度 $O(1)$，空间复杂度 $O(1)$


### 复杂链表的复制
题目描述：输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针random指向一个随机节点），请对此链表进行深拷贝，并返回拷贝后的头结点。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）
Solution：We copy each node to append itself.

```C++
/*
struct RandomListNode {
    int label;
    struct RandomListNode *next, *random;
    RandomListNode(int x) ：
            label(x), next(NULL), random(NULL) {
    }
};
*/
class Solution {
public：
    RandomListNode* Clone(RandomListNode* pHead) {
        if (pHead == NULL) { return NULL; }
        RandomListNode *p1 = pHead;
        while (p1 != NULL) {
            RandomListNode *temp = new RandomListNode(p1->label);
            temp->next = p1->next;
            p1->next = temp;
            p1 = p1->next->next;
        }
        p1 = pHead;
        while (p1 != NULL) {
            if (p1->random != NULL) { p1->next->random = p1->random->next; }
            p1 = p1->next->next;
        }
        RandomListNode *head = pHead->next, *p2 = pHead->next;
        p1 = pHead;
        while (p1 != NULL) {
            p1->next = p2->next;
            if (p2->next != NULL) { p2->next = p2->next->next; }
            p1 = p1->next;
            p2 = p2->next;
        }
        return head;
    }
};
```
时间复杂度 $O(n)$，空间复杂度 $O(1)$
