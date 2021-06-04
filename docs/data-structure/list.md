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
struct ListNode
{
    int val;
    ListNode *next;
    ListNode(): val(0), next(nullptr) {}
    ListNode(int x): val(x), next(nullptr) {}
    ListNode(int x, ListNode *next): val(x), next(next) {}
};
```

## 找出两个链表的相交结点
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