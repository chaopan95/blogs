# JS-offer
## 04. 重建二叉树
题目描述：输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。

```C++
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) ： val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public：
    TreeNode* reConstructBinaryTree(vector<int> pre,vector<int> vin) {
        int n = int(pre.size());
        if (n == 0) { return NULL; }
        return construct(pre, 0, n-1, vin, 0, n-1);
    }
    TreeNode *construct(vector<int> pre, int b1, int e1,
                        vector<int> vin, int b2, int e2)
    {
        if (b1 > e1 || b2 > e2) { return NULL; }
        TreeNode *root = new TreeNode(pre[b1]);
        int id = b2;
        for (; id <= e2; id++)
        {
            if (vin[id] == pre[b1]) { break; }
        }
        root->left = construct(pre, b1+1, b1+(id-b2), vin, b2, id-1);
        root->right = construct(pre, b1+(id-b2)+1, e1, vin, id+1, e2);
        return root;
    }
};
```

## 25. 复杂链表的复制*
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
    RandomListNode* Clone(RandomListNode* pHead)
    {
        if (pHead == NULL) { return NULL; }
        RandomListNode *p1 = pHead;
        while (p1 != NULL)
        {
            RandomListNode *temp = new RandomListNode(p1->label);
            temp->next = p1->next;
            p1->next = temp;
            p1 = p1->next->next;
        }
        p1 = pHead;
        while (p1 != NULL)
        {
            if (p1->random != NULL) { p1->next->random = p1->random->next; }
            p1 = p1->next->next;
        }
        RandomListNode *head = pHead->next, *p2 = pHead->next;
        p1 = pHead;
        while (p1 != NULL)
        {
            p1->next = p2->next;
            if (p2->next != NULL) { p2->next = p2->next->next; }
            p1 = p1->next;
            p2 = p2->next;
        }
        return head;
    }
};
```

## 35. 数组中的逆序对*
题目描述：在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007
题目保证输入的数组中没有的相同的数字数据范围：对于%50的数据,size<=10^4对于%75的数据,size<=10^5对于%100的数据,size<=2*10^5
输入1,2,3,4,5,6,7,0输出7

```C++
class Solution {
public：
    int InversePairs(vector<int> data) {
        int n = int(data.size());
        if (n < 2) { return 0; }
        long count = 0;
        mergeSort(data, 0, n-1, count);
        return (count % 1000000007);
    }
    void mergeSort(vector<int> &arr, int b, int e, long &count)
    {
        if (b >= e) { return; }
        int m = (b + e) / 2;
        mergeSort(arr, b, m, count);
        mergeSort(arr, m+1, e, count);
        merge(arr, b, m, e, count);
    }
    void merge(vector<int> &arr, int b, int m, int e, long &count)
    {
        int *temp = new int [e-b+1]{};
        int i = b, j = m+1, right = 0, pos = 0;
        while (i <= m && j <= e)
        {
            if (arr[i] < arr[j])
            {
                temp[pos++] = arr[i++];
                count += right;
            }
            else
            {
                temp[pos++] = arr[j++];
                right++;
            }
        }
        count += (m-i+1) * right;
        while (i <= m) { temp[pos++] = arr[i++]; }
        while (j <= e) { temp[pos++] = arr[j++]; }
        for (int i = b; i <= e; i++) { arr[i] = temp[i-b]; }
        delete []temp;
    }
};
```

## 54. 字符流中第一个不重复的字符*
题目描述：请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。如果当前字符流没有存在出现一次的字符，返回#字符。

Solution：int[0：256] = 0Give each ACSII a position index in the char flow starting from 1.If repeat appears, set int[i] = -1Return ACSII with minimum position index.

```C++
class Solution
{
    int *ascii;
    int N;
    int flow;
public：
    Solution()
    {
        N = 256;
        ascii = new int [N]{};
        flow = 1;
    }
    ~Solution() { delete []ascii; }
  //Insert one char from stringstream
    void Insert(char ch)
    {
        int idx = int(ch);
        if (ascii[idx] == 0) { ascii[idx] = flow; }
        else { ascii[idx] = -1; }
        flow++;
    }
  //return the first appearence once char in current stringstream
    char FirstAppearingOnce()
    {
        int pos = flow, ch = 0;
        for (int i = 0; i < N; i++)
        {
            if (ascii[i] < 1) { continue; }
            if (pos > ascii[i])
            {
                pos = ascii[i];
                ch = i;
            }
        }
        if (pos == flow) { return '#'; }
        return char(ch);
    }

};
```

## 67. 剪绳子*
题目描述：给你一根长度为n的绳子，请把绳子剪成整数长的m段（m、n都是整数，n>1并且m>1），每段绳子的长度记为k[0],k[1],...,k[m]。请问k[0]xk[1]x...xk[m]可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

示例1：输入8输出18

Solution：Suppose f(n) denote the max product. We have an interative equation

$$
f(n) =
\begin{cases}
  n, \quad 0 < n < 4 \\
  \max_{i} f(i)f(n-i), \quad i = 1, 2, ..., \left \lfloor \frac{n}{2} \right \rfloor, \quad \text{otherwise}
\end{cases}
$$

Attention, there is some special case, when n = 2, we should return 1. In iterative equation, f(2) denotes length of the rope.

```C++
class Solution {
public：
    int cutRope(int number) {
        int n = number;
        if (n < 2) { return 0; }
        if (n == 2) { return 1; }
        if (n == 3) { return 2; }
        int *f = new int[n+1]{};
        f[1] = 1;
        f[2] = 2;
        f[3] = 3;
        int prod = 1;
        for (int i = 4; i <= n; i++)
        {
            int max_prod = 1;
            for (int j = 1; j <= i/2; j++)
            {
                prod = f[j] * f[i-j];
                if (max_prod < prod) { max_prod = prod; }
            }
            f[i] = max_prod;
        }
        int res = f[n];
        delete []f;
        return res;
    }
};
```
