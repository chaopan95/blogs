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

## 05. 用两个栈实现队列
题目描述：用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。

```C++
class Solution
{
public：
    void push(int node) {
        stack1.push(node);
    }

    int pop() {
        while (!stack1.empty())
        {
            stack2.push(stack1.top());
            stack1.pop();
        }
        int res = stack2.top();
        stack2.pop();
        while(!stack2.empty())
        {
            stack1.push(stack2.top());
            stack2.pop();
        }
        return res;
    }

private：
    stack<int> stack1;
    stack<int> stack2;
};
```

## 23. 二叉搜索树的后序遍历序列*
题目描述：输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。

```C++
class Solution {
public：
    bool VerifySquenceOfBST(vector<int> sequence) {
        int n = int(sequence.size());
        if (n == 0) { return false; }
        return isPostOrder(sequence, 0, n-1);
    }
    bool isPostOrder(vector<int> arr, int b, int e)
    {
        if (b >= e) { return true; }
        int idx = b;
        for (; idx <= e; idx++)
        {
            if (arr[idx] >= arr[e]) { break; }
        }
        for (int i = idx; i < e; i++)
        {
            if (arr[i] < arr[e]) { return false; }
        }
        return isPostOrder(arr, b, idx-1) && isPostOrder(arr, idx, e-1);
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

## 46. 孩子们的游戏(圆圈中最后剩下的数)
题目描述：每年六一儿童节,牛客都会准备一些小礼物去看望孤儿院的小朋友,今年亦是如此。HF作为牛客的资深元老,自然也准备了一些小游戏。其中,有个游戏是这样的：首先,让小朋友们围成一个大圈。然后,他随机指定一个数m,让编号为0的小朋友开始报数。每次喊到m-1的那个小朋友要出列唱首歌,然后可以在礼品箱中任意的挑选礼物,并且不再回到圈中,从他的下一个小朋友开始,继续0...m-1报数....这样下去....直到剩下最后一个小朋友,可以不用表演,并且拿到牛客名贵的“名侦探柯南”典藏版(名额有限哦!!^_^)。请你试着想下,哪个小朋友会得到这份礼品呢？(注：小朋友的编号是从0到n-1)。如果没有小朋友，请返回-1。

```C++
class Solution {
public：
    int LastRemaining_Solution(int n, int m)
    {
        int i = 0, j = 0, numOut = 0, *out = new int [n]{};
        while (numOut < n - 1)
        {
            while (out[i])
            {
                i++;
                if (i == n) { i = 0; }
            }
            if (j == m-1)
            {
                out[i] = true;
                numOut++;
            }
            i++;
            j++;
            if (i == n) { i = 0; }
            if (j == m) { j = 0; }
        }
        int res = -1;
        for (int i = 0; i < n; i++) { if (!out[i]) { res = i; } }
        delete []out;
        return res;
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

## 61. 序列化二叉树*
题目描述：请实现两个函数，分别用来序列化和反序列化二叉树
二叉树的序列化是指：把一棵二叉树按照某种遍历方式的结果以某种格式保存为字符串，从而使得内存中建立起来的二叉树可以持久保存。序列化可以基于先序、中序、后序、层序的二叉树遍历方式来进行修改，序列化的结果是一个字符串，序列化时通过 某种符号表示空节点（#），以 ！ 表示一个结点值的结束（value!）。二叉树的反序列化是指：根据某种遍历顺序得到的序列化字符串结果str，重构二叉树。例如，我们可以把一个只有根节点为1的二叉树序列化为"1,"，然后通过自己的函数来解析回这个二叉树

```C++
/*
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x) ：
            val(x), left(NULL), right(NULL) {
    }
};
*/
class Solution {
public：
    char* Serialize(TreeNode *root) {
        string pre = "", in = "";
        preOrder(root, pre);
        inOrder(root, in);
        pre.push_back('#');
        pre += in;
        int n = int(pre.length());
        char *str = new char [n+1]{};
        for (int i = 0; i < n; i++) { str[i] = pre[i]; }
        str[n] = '\0';
        return str;
    }
    void preOrder(TreeNode* root, string &str)
    {
        if (root != NULL)
        {
            str += to_string(root->val);
            str.push_back(',');
            preOrder(root->left, str);
            preOrder(root->right, str);
        }
    }
    void inOrder(TreeNode* root, string &str)
    {
        if (root != NULL)
        {
            inOrder(root->left, str);
            str += to_string(root->val);
            str.push_back(',');
            inOrder(root->right, str);
        }
    }
    TreeNode* Deserialize(char *str) {
        vector<int> pre, in;
        bool isPre = true;
        int val = 0;
        for (int i = 0; i < strlen(str); i++)
        {
            if (str[i] == '#') { isPre = false; continue; }
            if (str[i] == ',')
            {
                if (isPre) { pre.push_back(val); }
                else { in.push_back(val); }
                val = 0;
                continue;
            }
            val = val * 10 + (str[i] - '0');
        }
        int n = int(pre.size());
        if (n == 0) { return NULL; }
        return constructTree(pre, in, 0, n-1, 0, n-1);
    }
    TreeNode* constructTree(vector<int> pre, vector<int> in,
                            int b1, int e1, int b2, int e2)
    {
        if (b1 > e1 || b2 > e2) { return NULL; }
        TreeNode *root = new TreeNode(pre[b1]);
        int numLeft = 0;
        for (int i = b2; i <= e2; i++, numLeft++)
        {
            if (in[i] == pre[b1]) { break; }
        }
        root->left = constructTree(pre, in, b1+1, b1+numLeft,
                                   b2, b2+numLeft-1);
        root->right = constructTree(pre, in, b1+numLeft+1, e1,
                                    b2+numLeft+1, e2);
        return root;
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
