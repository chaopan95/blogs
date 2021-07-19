# JS-offer
## 03. 从尾到头打印链表
题目描述：输入一个链表，按链表从尾到头的顺序返回一个ArrayList。

```C++
/**
*  struct ListNode {
*        int val;
*        struct ListNode *next;
*        ListNode(int x) ：
*              val(x), next(NULL) {
*        }
*  };
*/
class Solution {
public：
    vector<int> printListFromTailToHead(ListNode* head) {
        vector<int> arr;
        recurPrintList(arr, head);
        return arr;
    }
    void recurPrintList(vector<int> &arr, ListNode* head)
    {
        if (head != nullptr)
        {
            recurPrintList(arr, head->next);
            arr.push_back(head->val);
        }
    }
};
```

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

## 09. 变态跳台阶
题目描述：一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

```C++
class Solution {
public：
    int jumpFloorII(int number) {
        int res = 1;
        while (--number) { res *= 2; }
        return res;
    }
};
```

## 10. 矩形覆盖
题目描述：我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？比如n=3时，2*3的矩形块有3种覆盖方法：

$$
f(n) =
\begin{cases}
  n, \quad n =1, 2 \\
  f(n-1) + f(n-2), \quad n = 3, 4, 5, ...
\end{cases}
$$

```C++
class Solution {
public：
    int rectCover(int number) {
        if (number <= 2) { return number; }
        int t1 = 1, t2 = 2, temp = 0;
        for (int i = 3; i <= number; i++)
        {
            temp = t2 + t1;
            t1 = t2;
            t2 = temp;
        }
        return t2;
    }
};
```

## 12. 数值的整数次方
题目描述：给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。保证base和exponent不同时为0。

```C++
class Solution {
public：
    double Power(double base, int exponent) {
        if (exponent == 0) { return 1; }
        double res = 1.0;
        bool isNegative = false;
        if (exponent < 0)
        {
            exponent = -exponent;
            isNegative = true;
        }
        while(exponent--) { res *= base; }
        if (isNegative) { return 1/res; }
        return res;
    }
};
```

## 14. 链表中倒数第k个结点
题目描述：输入一个链表，输出该链表中倒数第k个结点

```C++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) ：
			val(x), next(NULL) {
	}
};*/
class Solution {
public：
    ListNode* FindKthToTail(ListNode* pListHead, unsigned int k) {
        if (k <= 0) { return NULL; }
        ListNode *p1 = pListHead, *p2 = pListHead;
        for (int i = 0; i < k; i++)
        {
            if (p2 == NULL) { return p2; }
            p2 = p2->next;
        }
        while (p2 != NULL)
        {
            p2 = p2->next;
            p1 = p1->next;
        }
        return p1;
    }
};
```

## 21. 栈的压入、弹出序列
题目描述：输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）

```C++
class Solution {
public：
    bool IsPopOrder(vector<int> pushV,vector<int> popV) {
        int n = int(pushV.size());
        stack<int> st;
        int j = 0;
        for (int i = 0; i < n; i++)
        {
            if (pushV[i] == popV[j]) { j++; }
            else { st.push(pushV[i]); }
        }
        for (; j < n; j++)
        {
            if (st.empty() || st.top() != popV[j]) { return false; }
            st.pop();
        }
        return true;
    }
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

## 26. 二叉搜索树与双向链表 
题目描述：输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

```C++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) ：
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public：
    TreeNode* Convert(TreeNode* pRootOfTree)
    {
        if (pRootOfTree == NULL) { return NULL; }
        postOrder(pRootOfTree);
        TreeNode *head = pRootOfTree;
        while (head->left != NULL) { head = head->left; }
        return head;
    }
    void postOrder(TreeNode *root)
    {
        if (root != NULL)
        {
            postOrder(root->left);
            postOrder(root->right);
            TreeNode *left = root->left;
            while (left != NULL &&
                left->right != NULL) { left = left->right; }
            root->left = left;
            if (left != NULL) { left->right = root; }
            TreeNode *right = root->right;
            while (right != NULL &&
                right->left != NULL) { right = right->left; }
            root->right = right;
            if (right != NULL) { right->left = root; }
        }
    }
};
```

```C++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) ：
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public：
    TreeNode* Convert(TreeNode* pRootOfTree)
    {
        if (pRootOfTree == NULL) { return NULL; }
        TreeNode *head = pRootOfTree, *leftMost = NULL;
        inOrder(pRootOfTree, leftMost);
        while (head->left != NULL) { head = head->left; }
        return head;
    }
    void inOrder(TreeNode *root, TreeNode *&leftMost)
    {
        if (root != NULL)
        {
            inOrder(root->left, leftMost);
            if (leftMost == NULL) { leftMost = root; }
            else
            {
                leftMost->right = root;
                root->left = leftMost;
                leftMost = root;
            }
            inOrder(root->right, leftMost);
        }
    }
};
```

## 33. 丑数*
题目描述：把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

Solution：Ugly numbers： 1, 2, 3, 4, 5, 6, 8, 9, 10, 12, 15...Some ugly number is from a multiplaction of previous number by 2, 3 or 5.

```C++
class Solution {
public：
    int min(int a, int b, int c)
    {
        int temp = a < b ? a： b;
        return temp < c ? temp： c;
    }
    int GetUglyNumber_Solution(int index) {
        if (index < 2) { return index; }
        int *arr = new int [index]{};
        arr[0] = 1;
        int f2 = 0, f3 = 0, f5 = 0;
        for (int i = 1; i < index; i++)
        {
            arr[i] = min(arr[f2]*2, arr[f3]*3, arr[f5]*5);
            while (arr[f2]*2 <= arr[i]) { f2++; }
            while (arr[f3]*3 <= arr[i]) { f3++; }
            while (arr[f5]*5 <= arr[i]) { f5++; }
        }
        int res = arr[index-1];
        delete []arr;
        return res;
    }
};
```

## 34. 第一个只出现一次的字符位置
题目描述：在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.（从0开始计数）

```C++
class Solution {
public：
    int FirstNotRepeatingChar(string str) {
        int n = int(str.length());
        if (n == 0) { return -1; }
        int N = 256, pos = n + 1;
        int *ascii = new int [N]{};
        for (int i = 0; i < n; i++)
        {
            int id = int(str[i]);
            if (ascii[id] == 0) { ascii[id] = i + 1; }
            else { ascii[id] = -1; }
        }
        for (int i = 0; i < N; i++)
        {
            if (ascii[i] > 0 && ascii[i] < pos) { pos = ascii[i]; }
        }
        delete []ascii;
        if (pos == n + 1) { return -1; }
        return pos - 1;
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

## 36. 两个链表的第一个公共结点
题目描述：输入两个链表，找出它们的第一个公共结点。（注意因为传入数据是链表，所以错误测试数据的提示是用其他方式显示的，保证传入数据是正确的）

```C++
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) ：
			val(x), next(NULL) {
	}
};*/
class Solution {
public：
    ListNode* FindFirstCommonNode( ListNode* pHead1, ListNode* pHead2) {
        if (pHead1 == NULL || pHead2 == NULL) { return NULL; }
        ListNode *p1 = pHead1, *p2 = pHead2;
        stack<ListNode*> s1, s2;
        while (p1 != NULL) { s1.push(p1); p1 = p1->next; }
        while (p2 != NULL) { s2.push(p2); p2 = p2->next; }
        while (!s1.empty() && !s2.empty())
        {
            if (s1.top() != s2.top()) { return s1.top()->next; }
            s1.pop(); s2.pop();
        }
        if (s1.empty()) { return pHead1; }
        if (s2.empty()) { return pHead2; }
        return s1.top()->next;
    }
};
```

## 37. 数字在排序数组中出现的次数
题目描述：统计一个数字在排序数组中出现的次数。

```C++
class Solution {
public：
    int GetNumberOfK(vector<int> data ,int k) {
        int count = 0;
        for (int i = 0; i < int(data.size()); i++)
        {
            if (data[i] == k) { count++; }
        }
        return count;
    }
};
```

## 38. 二叉树的深度
题目描述：输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

```C++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) ：
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public：
    int TreeDepth(TreeNode* pRoot)
    {
        if (pRoot == NULL) { return 0; }
        int dep = 0;
        queue<TreeNode*> qTree;
        qTree.push(pRoot);
        TreeNode *front = pRoot, *last = pRoot, *nextLast = pRoot;
        while (!qTree.empty())
        {
            front = qTree.front();
            qTree.pop();
            if (front->left != NULL)
            {
                qTree.push(front->left);
                nextLast = front->left;
            }
            if (front->right != NULL)
            {
                qTree.push(front->right);
                nextLast = front->right;
            }
            if (front == last)
            {
                dep++;
                last = nextLast;
            }
        }
        return dep;
    }
};

```C++
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) ：
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public：
    int TreeDepth(TreeNode* pRoot)
    {
        if (pRoot == NULL) { return 0; }
        int leftDep = TreeDepth(pRoot->left);
        int rightDep = TreeDepth(pRoot->right);
        return (leftDep > rightDep ? leftDep ： rightDep) + 1;
    }
};
```

## 39. 平衡二叉树
题目描述：输入一棵二叉树，判断该二叉树是否是平衡二叉树。在这里，我们只需要考虑其平衡性，不需要考虑其是不是排序二叉树。

```C++
class Solution {
public：
    bool IsBalanced_Solution(TreeNode* pRoot) {
        bool res = true;
        postOrder(pRoot, res);
        return res;
    }
    int postOrder(TreeNode *root, bool &res)
    {
        if (root == NULL) { return 0; }
        int leftDep = postOrder(root->left, res);
        int rightDep = postOrder(root->right, res);
        int diff = abs(leftDep - rightDep);
        if (diff > 1) { res = false; }
        return (leftDep > rightDep ? leftDep ： rightDep) + 1;
    }
};
```

## 40. 数组中只出现一次的数字*
题目描述：一个整型数组里除了两个数字之外，其他的数字都出现了两次。请写程序找出这两个只出现一次的数字。

Solution：num XOR num = 0： one number XOR itself is 0.We XOR all entire array to get a result of the two single numbers. Then locate the first 1's position, e.g. 6 = 0110, first 1's position is 2 (starting 0 from right to left). We classify the array into two parts by checking if one integer is 1 at its first 1's position. Then do XOR once again for the two parts to find the two single numbers.

```C++
class Solution {
public：
    void FindNumsAppearOnce(vector<int> data,int* num1,int *num2) {
        int n = int(data.size());
        if (n < 2) { return; }
        int XOR = 0, pos = 0;
        for (int i = 0; i < n; i++) { XOR ^= data[i]; }
        while (XOR >> 1) { XOR = XOR >> 1; pos++; }
        bool is1Ok = false, is2Ok = false;
        int n1 = 0, n2 = 0;
        for (int i = 0; i < n; i++)
        {
            if ((data[i] >> pos) & 1)
            {
                if (is1Ok) { n1 ^= data[i]; }
                else
                {
                    is1Ok = true;
                    n1 = data[i];
                }
            }
            else
            {
                if (is2Ok) { n2 ^= data[i]; }
                else
                {
                    is2Ok = true;
                    n2 = data[i];
                }
            }
            *num1 = n1 < n2 ? n1 ： n2;
            *num2 = n1 >= n2 ? n1 ： n2;
        }
    }
};
```

## 41. 和为S的连续正数序列
题目描述：小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列：18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!输出所有和为S的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序。

```C++
class Solution {
public：
    vector<vector<int> > FindContinuousSequence(int sum) {
        vector<vector<int>> res;
        if (sum < 3) { return res; }
        for (int n = sum; n >= 2; n--)
        {
            int numerator = 2 * sum + n - n * n;
            int denominator = 2 * n;
            if (numerator % denominator == 0)
            {
                int a = numerator / denominator;
                if (a < 1) { continue; }
                vector<int> arr;
                for (int i = a; i <= a + n - 1; i++) { arr.push_back(i); }
                res.push_back(arr);
            }
        }
        return res;
    }
};
```

## 42. 和为S的两个数字
题目描述：输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。对应每个测试案例，输出两个数，小的先输出。

```C++
class Solution {
public：
    vector<int> FindNumbersWithSum(vector<int> array, int sum) {
        vector<int> res;
        int n = int(array.size());
        if (n == 0) { return res; }
        int i = 0, j = n - 1;
        while (i < j)
        {
            if (array[i] + array[j] < sum) { i++; }
            else if (array[i] + array[j] > sum) { j--; }
            else
            {
                res.push_back(array[i]);
                res.push_back(array[j]);
                break;
            }
        }
        return res;
    }
};
```

## 43. 左旋转字符串
题目描述：汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！

```C++
class Solution {
public：
    string LeftRotateString(string str, int n) {
        int len = int(str.length());
        if (len == 0 || n == 0 || len <= n) { return str; }
        string firtN(str, 0, n);
        string rest(str, n, len);
        return rest + firtN;
    }
};
```

## 44. 翻转单词顺序列
题目描述：牛客最近来了一个新员工Fish，每天早晨总是会拿着一本英文杂志，写些句子在本子上。同事Cat对Fish写的内容颇感兴趣，有一天他向Fish借来翻看，但却读不懂它的意思。例如，“student. a am I”。后来才意识到，这家伙原来把句子单词的顺序翻转了，正确的句子应该是“I am a student.”。Cat对一一的翻转这些单词顺序可不在行，你能帮助他么？

```C++
class Solution {
public：
    string ReverseSentence(string str) {
        int n = int(str.length());
        if (n == 0) { return str; }
        string res = "", word = "";
        stack<string> s;
        for (int i = 0; i < n; i++)
        {
            if (str[i] == ' ')
            {
                s.push(word);
                word = "";
                continue;
            }
            word.push_back(str[i]);
        }
        s.push(word);
        while (!s.empty())
        {
            res += s.top();
            s.pop();
            if (!s.empty()) { res.push_back(' '); }
        }
        return res;
    }
};
```

```C++
class Solution {
public：
    string ReverseSentence(string str) {
        int n = int(str.length());
        if (n == 0) { return str; }
        for (int i = 0; i < n/2; i++)
        {
            char temp = str[i];
            str[i] = str[n-1-i];
            str[n-1-i] = temp;
        }
        str.push_back(' ');
        for (int i = 0, j = 0; i <= n; i++)
        {
            if (str[i] == ' ')
            {
                int n = i - 1, m = j;
                while (m < n)
                {
                    char temp = str[m];
                    str[m] = str[n];
                    str[n] = temp;
                    m++;
                    n--;
                }
                j = i + 1;
            }
        }
        str[n] = '\0';
        return str;
    }
};
```

## 45. 扑克牌顺子
题目描述：LL今天心情特别好,因为他去买了一副扑克牌,发现里面居然有2个大王,2个小王(一副牌原本是54张^_^)...他随机从中抽出了5张牌,想测测自己的手气,看看能不能抽到顺子,如果抽到的话,他决定去买体育彩票,嘿嘿！！“红心A,黑桃3,小王,大王,方片5”,“Oh My God!”不是顺子.....LL不高兴了,他想了想,决定大\小 王可以看成任何数字,并且A看作1,J为11,Q为12,K为13。上面的5张牌就可以变成“1,2,3,4,5”(大小王分别看作2和4),“So Lucky!”。LL决定去买体育彩票啦。 现在,要求你使用这幅牌模拟上面的过程,然后告诉我们LL的运气如何， 如果牌能组成顺子就输出true，否则就输出false。为了方便起见,你可以认为大小王是0。

```C++
class Solution {
public：
    bool IsContinuous( vector<int> numbers ) {
        int n = int(numbers.size());
        if (n == 0) { return 0; }
        //sort(numbers.begin(), numbers.end());
        quickSort(numbers, 0, n-1);
        int num0 = 0, head = 0;
        for (int i = 0; i < n; i++)
        {
            if (numbers[i] == 0) { num0++; continue; }
            if (head == 0) { head = numbers[i]; }
            else
            {
                if (numbers[i] == numbers[i-1]) { return false; }
                if (numbers[i] == head + 1) { head++; }
                else
                {
                    num0 -= numbers[i] - head - 1;
                    if (num0 < 0) { return false; }
                    head = numbers[i];
                }
            }
        }
        return true;
    }
    void quickSort(vector<int> &arr, int b, int e)
    {
        if (b < e)
        {
            int i = b, j = e;
            while (i < j)
            {
                while (i < j && arr[i] <= arr[j]) { j--; }
                swap(arr, i, j);
                while (i < j && arr[i] <= arr[j]) { i++; }
                swap(arr, i, j);
            }
            quickSort(arr, b, i-1);
            quickSort(arr, j+1, e);
        }
    }
    void swap(vector<int> &arr, int i, int j)
    {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
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

## 47. 求1+2+3+...+n
题目描述：求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B：C）。

```C++
class Solution {
public：
    int Sum_Solution(int n) {
        return (n+1)*n/2;
    }
};
```

## 48. 不用加减乘除做加法*
题目描述：写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。
Solution：XOR + &A = 000101 = 5, B = 010001 = 17

1) sum = 000101 XOR 010001 = 010100 = 20, pos = 000101 & 010001 = 000001 = 1. We have to move left for 000001 at 0-th position (starting from rightmost).

2) if pos is not 0, A = sum, B = (pos << 1) then continue 1)

```C++
class Solution {
public：
    int Add(int num1, int num2)
    {
        int res = num1;
        int pos = num2;
        while(true)
        {
            res = num1^num2;
            pos = num1 & num2;
            if (pos == 0) { break; }
            num1 = res;
            num2 = pos << 1;
        }
        return res;
    }
};
```

## 49. 把字符串转换成整数
题目描述：将一个字符串转换成一个整数，要求不能使用字符串转换整数的库函数。数值为0或者字符串不是一个合法的数值则返回0。输入一个字符串,包括数字字母符号,可以为空。如果是合法的数值表达则返回该数字，否则返回0。

输入+21474836471a33

输出21474836470

```C++
class Solution {
public：
    int StrToInt(string str) {
        int n = int(str.length());
        if (n == 0) { return 0; }
        int num = 0;
        bool isPlus = true;
        for (int i = 0; i < n; i++)
        {
            if (i == 0 && str[0] == '-') { isPlus = false; continue; }
            if (i == 0 && str[0] == '+') { continue; }
            if (str[i] > '9' || str[i] < '0') { return 0; }
            num = num * 10 + str[i] - '0';
        }
        if (!isPlus) { return -num; }
        return num;
    }
};
```

## 50. 数组中重复的数字
题目描述：在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。返回描述：如果数组中有重复的数字，函数返回true，否则返回false。如果数组中有重复的数字，把重复的数字放到参数duplication[0]中。（ps：duplication已经初始化，可以直接赋值使用。

```C++
class Solution {
public：
    // Parameters：
    //        numbers：     an array of integers
    //        length：      the length of array numbers
    //        duplication： (Output) the duplicated number in the array number
    // Return value：       true if the input is valid, and there are some
    //                     duplications in the array number
    //                     otherwise false
    bool duplicate(int numbers[], int length, int* duplication) {
        if (length == 0) { return false; }
        int *arr = new int [length]{};
        for (int i = 0; i < length; i++)
        {
            if (arr[numbers[i]] == length) { arr[numbers[i]] = i; }
            if (arr[numbers[i]] == 0) { arr[numbers[i]] = length; }
        }
        int idx = length;
        for (int i = 0; i < length; i++)
        {
            if (arr[i] == 0) { continue; }
            if (idx > arr[i]) { idx = arr[i]; }
        }
        delete []arr;
        if (idx == length) { return false; }
        *duplication = numbers[idx];
        return true;
    }
};
```

## 51. 构建乘积数组
题目描述：给定一个数组A[0,1,...,n-1], 请构建一个数组B[0,1,...,n-1], 其中B中的元素 B[i] = A[0] * A[1] * ... * A[i-1] * A[i+1] * ... * A[n-1]。不能使用除法。（注意：规定B[0] = A[1] * A[2] * ... * A[n-1]，B[n-1] = A[0] * A[1] * ... * A[n-2];）

```C++
class Solution {
public：
    vector<int> multiply(const vector<int>& A) {
        vector<int> res;
        int n = int(A.size());
        if (n == 0) { return res; }
        for (int i = 0; i < n; i++)
        {
            int mul = A[0];
            for (int j = 1; j < n; j++)
            {
                if (j == i) { continue; }
                if (A[j] == 0)
                {
                    mul = 0;
                    break;
                }
                mul *= A[j];
            }
            res.push_back(mul);
        }
        return res;
    }
};
```

## 52. 正则表达式匹配*
题目描述：请实现一个函数用来匹配包括'.'和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（包含0次）。在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但是与"aa.a"和"ab*a"均不匹配。

```C++
class Solution {
public：
    bool match(char* str, char* pattern)
    {
        size_t ns = strlen(str), np = strlen(pattern);
        bool **dp = new bool *[ns+1];
        for (int i = 0; i <= ns; i++) { dp[i] = new bool [np+1]{}; }
        dp[ns][np] = true;
        for (int i = ns; i >= 0; i--)
        {
            for (int j = np-1; j >= 0; j--)
            {
                bool ijMatch = (i < ns) && (str[i] == pattern[j] ||
                                            pattern[j] == '.');
                if (j < np-1 && pattern[j+1] == '*')
                {
                    dp[i][j] = dp[i][j+2] || (ijMatch && dp[i+1][j]);
                }
                else { dp[i][j] = ijMatch && dp[i+1][j+1]; }
            }
        }
        bool res = dp[0][0];
        for (int i = 0; i <= ns; i++) { delete []dp[i]; }
        delete []dp;
        return res;
    }
};
```

## 53. 表示数值的字符串*
题目描述：请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100", "5e2", "-123", "3.1416" 和 "-1E-16" 都表示数值。 但是 "12e", "1a3.14", "1.2.3", "+-5" 和 "12e+4.3" 都不是。

```C++
class Solution {
public：
    bool isNumeric(char* string)
    {
        size_t n = strlen(string);
        if (n == 0) { return false; }
        bool isEx = false, isPt = false;
        for (int i = 0; i < n; i++)
        {
            if ((string[i] == '+' || string[i] == '-'))
            {
                if (i == 0) { continue; }
                if (string[i-1] == 'E' || string[i-1] == 'e') { continue; }
                return false;
            }
            if (string[i] == 'e' || string[i] == 'E')
            {
                if (isEx || i == n-1) { return false; }
                isEx = true;
                continue;
            }
            if (string[i] == '.')
            {
                if (isPt || isEx || i == n-1) { return false; }
                isPt = true;
                continue;
            }
            if (string[i] > '9' || string[i] < '0') { return false; }
        }
        return true;
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

## 55. 链表中环的入口结点*
题目描述：给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。
![Image text](https://github.com/chaopan95/JS-offer/blob/main/_img/3.png)

For the case of circle. Set two pointors p1 and p2 staring from head. p1 moves one step each time; p2 moves 2 steps each time. Because of a loop, p1 and p2 will definitely come across at some node in the loop. Suppose p1 moves x steps, then p2 moves evidently 2x steps. Besides, p2 has covered at least one circile.

$$2x - x = kn, \quad k = 1, 2, 3, ...$$

where n denotes the length of loop.

We can also see both p1 and p2 have covered the part beyond the loop, says L1.

$$x = L1 + L2 + k_{1}n = k_{2}n, \quad k_{1} = 0, 1, 2, ...; k_{2} = 1, 2, 3, ...$$

$$L1 + L2 = kn, \quad k = 0, 1, 2, ...$$

So, we one pointor moves starting from M and the other moves starting from head and both of them move with 1 step. Then they will meet again at the statrt of loop.

```C++
/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) ：
        val(x), next(NULL) {
    }
};
*/
class Solution {
public：
    ListNode* EntryNodeOfLoop(ListNode* pHead)
    {
        if (pHead == NULL) { return NULL; }
        ListNode *p1 = pHead, *p2 = pHead;
        while (p1 != NULL && p2 != NULL)
        {
            p1 = p1->next;
            p2 = p2->next;
            if (p2 == NULL) { return NULL; }
            p2 = p2->next;
            if (p1 == p2) { break; }
        }
        p2 = pHead;
        while (p2 != p1)
        {
            p1 = p1->next;
            p2 = p2->next;
        }
        return p1;
    }
};
```

## 56. 删除链表中重复的结点
题目描述：在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5

```C++
/*
struct ListNode {
    int val;
    struct ListNode *next;
    ListNode(int x) ：
        val(x), next(NULL) {
    }
};
*/
class Solution {
public：
    ListNode* deleteDuplication(ListNode* pHead)
    {
        if (pHead == NULL) { return NULL; }
        ListNode *pre = new ListNode(0);
        pre->next = pHead;
        ListNode *par = pre, *cur = pHead;
        bool isDup = false;
        while (cur != NULL)
        {
            while (cur->next != NULL && cur->next->val == cur->val)
            {
                cur = cur->next;
                isDup = true;
            }
            cur = cur->next;
            if (isDup)
            {
                par->next = cur;
                isDup = false;
            }
            else { par = par->next; }
        }
        pHead = pre->next;
        delete pre;
        return pHead;
    }
};
```

## 57. 二叉树的下一个结点
题目描述：给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

```C++
/*
struct TreeLinkNode {
    int val;
    struct TreeLinkNode *left;
    struct TreeLinkNode *right;
    struct TreeLinkNode *next;
    TreeLinkNode(int x) ：val(x), left(NULL), right(NULL), next(NULL) {
        
    }
};
*/
class Solution {
public：
    TreeLinkNode* GetNext(TreeLinkNode* pNode)
    {
        if (pNode == NULL) { return NULL; }
        if (pNode->right != NULL)
        {
            TreeLinkNode *curNode = pNode->right;
            while (curNode->left != NULL) { curNode = curNode->left; }
            return curNode;
        }
        if (pNode->next == NULL) { return NULL; }
        if (pNode->next->left == pNode) { return pNode->next; }
        
        if (pNode->next->next != NULL &&
            pNode->next->next->left == pNode->next)
        {
            return pNode->next->next;
        }
        return NULL;
    }
};
```

## 58. 对称的二叉树
题目描述：请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。

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
    bool isSymmetrical(TreeNode* pRoot)
    {
        if (pRoot == NULL) { return true; }
        return isSymmetricalSubtree(pRoot->left, pRoot->right);
    }
    bool isSymmetricalSubtree(TreeNode *left, TreeNode *right)
    {
        if (left == NULL && right == NULL) { return true; }
        if (left == NULL || right == NULL) { return false; }
        if (left->val != right->val) { return false; }
        bool L = isSymmetricalSubtree(left->left, right->right);
        bool R = isSymmetricalSubtree(left->right, right->left);
        return (L && R);
    }
};
```

## 59. 按之字形顺序打印二叉树
题目描述：请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

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
    void inverse(vector<int>& arr)
    {
        int len = int(arr.size());
        for (int i = 0; i < len/2; i++)
        {
            int temp = arr[i];
            arr[i] = arr[len-1-i];
            arr[len-1-i] = temp;
        }
    }
    vector<vector<int> > Print(TreeNode* pRoot) {
        vector<vector<int>> res;
        if (pRoot == NULL) { return res; }
        queue<TreeNode*> tree;
        TreeNode *last, *nextlast, *front;
        tree.push(pRoot);
        last = nextlast = front = pRoot;
        vector<int> temp;
        int count = 1;
        while (!tree.empty())
        {
            front = tree.front();
            tree.pop();
            temp.push_back(front->val);
            if (front->left != NULL)
            {
                tree.push(front->left);
                nextlast = front->left;
            }
            if (front->right != NULL)
            {
                tree.push(front->right);
                nextlast = front->right;
            }
            if (last == front)
            {
                if (count % 2 == 0) { inverse(temp); }
                count++;
                res.push_back(temp);
                temp.clear();
                last = nextlast;
            }
        }
        return res;
    }
};
```

## 60. 把二叉树打印成多行
题目描述：从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。

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
        vector<vector<int> > Print(TreeNode* pRoot) {
            vector<vector<int> > res;
            if (pRoot == NULL) { return res; }
            queue<TreeNode*> tree;
            TreeNode *last, *nextlast, *front;
            tree.push(pRoot);
            last = nextlast = front = pRoot;
            vector<int> temp;
            while (!tree.empty())
            {
                front = tree.front();
                tree.pop();
                temp.push_back(front->val);
                if (front->left != NULL)
                {
                    tree.push(front->left);
                    nextlast = front->left;
                }
                if (front->right != NULL)
                {
                    tree.push(front->right);
                    nextlast = front->right;
                }
                if (last == front)
                {
                    res.push_back(temp);
                    temp.clear();
                    last = nextlast;
                }
            }
            return res;
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

## 62. 二叉树的第k个结点*
题目描述：给定一棵二叉搜索树，请找出其中的第k小的结点。例如，（5，3，7，2，4，6，8）中，按结点数值大小顺序第三小结点的值为4。

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
    TreeNode* KthNode(TreeNode* pRoot, int k)
    {
        TreeNode *res = NULL;
        inOrder(pRoot, k, res);
        return res;
    }
    void inOrder(TreeNode *root, int &k, TreeNode *&res)
    {
        if (root != NULL)
        {
            inOrder(root->left, k, res);
            k--;
            if (k == 0) { res = root; }
            inOrder(root->right, k, res);
        }
    }
};
```

## 63. 数据流中的中位数
题目描述：如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。

```C++
class Solution {
    vector<int> arr;
public：
    void Insert(int num)
    {
        arr.push_back(num);
    }
    void swap(vector<int>& arr, int i, int j)
    {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
    void quickSort(vector<int>& arr, int begin, int end, int n)
    {
        if (begin < end)
        {
            int i = begin, j = end;
            while (i < j)
            {
                while (arr[i] > arr[j] && i < j) { j--; }
                swap(arr, i, j);
                while (arr[i] > arr[j] && i < j) { i++; }
                swap(arr, i, j);
            }
            quickSort(arr, begin, i-1, n);
            quickSort(arr, j+1, end, n);
        }
    }
    double GetMedian()
    {
        int n = int(arr.size());
        quickSort(arr, 0, n-1, n);
        if (n%2) { return arr[n/2]; }
        else { return (arr[(n-1)/2] + arr[n/2])/2.0; }
    }
};
```

## 64. 滑动窗口的最大值
题目描述：给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。例如，如果输入数组{2,3,4,2,6,2,5,1}及滑动窗口的大小3，那么一共存在6个滑动窗口，他们的最大值分别为{4,4,6,6,6,5}； 针对数组{2,3,4,2,6,2,5,1}的滑动窗口有以下6个： {[2,3,4],2,6,2,5,1}， {2,[3,4,2],6,2,5,1}， {2,3,[4,2,6],2,5,1}， {2,3,4,[2,6,2],5,1}， {2,3,4,2,[6,2,5],1}， {2,3,4,2,6,[2,5,1]}。

```C++
class Solution {
public：
    vector<int> maxInWindows(const vector<int>& num, unsigned int size)
    {
        vector<int> res;
        int n = int(num.size());
        if (n < size || size < 1) { return res; }
        for (int i = 0; i < n - size + 1; i++)
        {
            int max = num[i];
            for (int j = i + 1; j <= i + size - 1; j++)
            {
                if (max < num[j]) { max = num[j]; }
            }
            res.push_back(max);
        }
        return res;
    }
};
```

## 65. 矩阵中的路径
请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则该路径不能再进入该格子。 例如

$$
\begin{bmatrix}
a & b & c & e\\
s & f & c & s\\
a & d & e & e
\end{bmatrix}
$$

(stock in an array [a, b, c, e, s, f, c, s, a, d, e, e])。 矩阵中包含一条字符串"bcced"的路径，但是矩阵中不包含"abcb"路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。

Solution：Backtracking： if we can find one character matched, we continue to explore next one (up, down, left, right); if one character doesn't match, we reset all previous positions.

```C++
class Solution {
public：
    bool hasPath(char* matrix, int rows, int cols, char* str)
    {
        bool *dp = new bool [rows*cols]{}, res = false;
        for (int i = 0; i < rows; i++)
        {
            for (int j = 0; j < cols; j++)
            {
                if (isPath(i, j, rows, cols, matrix, str, dp, 0))
                {
                    res = true;
                    break;
                }
            }
        }
        delete []dp;
        return res;
    }
    bool isPath(int i, int j, int nRow, int nCol, char *mat,
                char *str, bool *dp, int k)
    {
        int idx = i * nCol + j;
        if (i < 0 || i >= nRow || j < 0 || j >= nCol || dp[idx] ||
            mat[idx] != str[k]) { return false; }
        if (str[k+1] == '\0') { return true; }
        dp[idx] = true;
        bool u = isPath(i-1, j, nRow, nCol, mat, str, dp, k+1);
        bool d = isPath(i+1, j, nRow, nCol, mat, str, dp, k+1);
        bool l = isPath(i, j-1, nRow, nCol, mat, str, dp, k+1);
        bool r = isPath(i, j+1, nRow, nCol, mat, str, dp, k+1);
        dp[idx] = false;
        return u || d || l || r;
    }
};
```

## 66. 机器人的运动范围*
题目描述：地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？

Solution：Backtracking is one of brutal force method in searching.

We cannot simply count how many grids which are illegal because the robot can only move one cell once.

```C++
class Solution {
public：
    int movingCount(int threshold, int rows, int cols)
    {
        bool *dp = new bool [rows*cols]{};
        int count = move(threshold, rows, cols, 0, 0, dp);
        delete []dp;
        return count;
    }
    int move(int threshold, int nRow, int nCol, int i, int j,
              bool *dp)
    {
        int pos = i * nCol + j;
        if (i < 0 || i >= nRow || j < 0 || j >= nCol || dp[pos] ||
            toSum(i) + toSum(j) > threshold) { return 0; }
        dp[pos] = true;
        int u = move(threshold, nRow, nCol, i-1, j, dp);
        int d = move(threshold, nRow, nCol, i+1, j, dp);
        int l = move(threshold, nRow, nCol, i, j-1, dp);
        int r = move(threshold, nRow, nCol, i, j+1, dp);
        return 1 + u + d + l + r;
    }
    int toSum(int num)
    {
        int sum = 0;
        while (num)
        {
            sum += num % 10;
            num /= 10;
        }
        return sum;
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

## 68. OJ在线编程常见输入输出练习场
### 68.1 A+B(1)
题目描述：计算a+b输入描述：输入包括两个正整数a,b(1 <= a, b <= 10^9),输入数据包括多组。输出描述：输出a+b的结果

示例1

输入1 510 20

输出630

```C++
#include<iostream>
using namespace std;

int main()
{
    long a, b;
    while(cin >> a >> b)
    {
        cout << a + b << endl;
    }
    return 0;
}
```

### 68.2 A+B(2)
题目描述：计算a+b输入描述：输入第一行包括一个数据组数t(1 <= t <= 100)接下来每行包括两个正整数a,b(1 <= a, b <= 10^9)输出描述：输出a+b的结果

示例1

输入21 510 20

输出630

```C++
#include<iostream>
using namespace std;

int main()
{
    int t;
    long a, b;
    cin >> t;
    while(t--)
    {
        cin >> a >> b;
        cout << (a + b) << endl;
    }
    return 0;
}
```

### 68.3 A+B(3)
题目描述：计算a+b输入描述：输入包括两个正整数a,b(1 <= a, b <= 10^9),输入数据有多组, 如果输入为0 0则结束输入输出描述：输出a+b的结果

示例1

输入1 510 200 0

输出630

```C++
#include<iostream>
using namespace std;

int main()
{
    long a, b;
    while(cin >> a >> b)
    {
        if (a == 0 && b == 0)
        {
            break;
        }
        cout << (a + b) << endl;
    }
    return 0;
}
```

### 68.4 A+B(4)
题目描述：计算一系列数的和输入描述：输入数据包括多组。每组数据一行,每行的第一个整数为整数的个数n(1 <= n <= 100), n为0的时候结束输入。接下来n个正整数,即需要求和的每个正整数。输出描述：每组数据输出求和的结果

示例1

输入4 1 2 3 45 1 2 3 4 50

输出1015
```C++
#include<iostream>
using namespace std;

int main()
{
    long sum, a;
    int n;
    while(true)
    {
        sum = 0;
        cin >> n;
        if (n == 0) { break; }
        while(n--)
        {
            cin >> a;
            sum += a;
        }
        cout << sum << endl;
    }
    return 0;
}
```

### 68.5 A+B(5)
题目描述：计算一系列数的和输入描述：输入的第一行包括一个正整数t(1 <= t <= 100), 表示数据组数。接下来t行, 每行一组数据。每行的第一个整数为整数的个数n(1 <= n <= 100)。接下来n个正整数, 即需要求和的每个正整数。输出描述：每组数据输出求和的结果

示例1

输入24 1 2 3 45 1 2 3 4 5

输出1015

```C++
#include<iostream>
using namespace std;

int main()
{
    int t, n;
    long sum, a;
    cin >> t;
    while(t--)
    {
        cin >> n;
        sum = 0;
        while(n--)
        {
            cin >> a;
            sum += a;
        }
        cout << sum << endl;
    }
    return 0;
}
```

### 68.6 A+B(6)
题目描述：计算一系列数的和输入描述：输入数据有多组, 每行表示一组输入数据。每行的第一个整数为整数的个数n(1 <= n <= 100)。接下来n个正整数, 即需要求和的每个正整数。输出描述：每组数据输出求和的结果

示例1

输入4 1 2 3 45 1 2 3 4 5

输出1015

```C++
#include<iostream>
using namespace std;

int main()
{
    int n;
    long sum, a;
    while(cin >> n)
    {
        sum = 0;
        while(n--)
        {
            cin >> a;
            sum += a;
        }
        cout << sum << endl;
    }
    return 0;
}
```

### 68.7 A+B(7)
题目描述：计算一系列数的和输入描述：输入数据有多组, 每行表示一组输入数据。每行不定有n个整数，空格隔开。(1 <= n <= 100)。输出描述：每组数据输出求和的结果

示例1

输入1 2 34 50 0 0 0 0

输出690

```C++
#include<iostream>
using namespace std;

int main()
{
    long sum, a;
    sum = 0;
    while(cin >> a)
    {
        sum += a;
        if (cin.get() == '\n')
        {
            cout << sum << endl;
            sum = 0;
        }
    }
    return 0;
}
```

### 68.8 字符串排序(1)
题目描述：对输入的字符串进行排序后输出输入描述：输入有两行，第一行n第二行是n个空格隔开的字符串输出描述：输出一行排序后的字符串，空格隔开，无结尾空格

示例1

输入5c d a bb e

输出a bb c d e

```C++
#include<iostream>
#include<string>
using namespace std;

void swap(string *str, int i, int j)
{
    string temp = str[i];
    str[i] = str[j];
    str[j] = temp;
}

void quickSort(string *str, int begin, int end)
{
    if (begin < end)
    {
        int i = begin, j = end;
        while (i < j)
        {
            while (i < j && str[i] <= str[j]) { j--; }
            swap(str, i, j);
            while (i < j && str[i] <= str[j]) { i++; }
            swap(str, i, j);
        }
        quickSort(str, begin, i-1);
        quickSort(str, j+1, end);
    }
}


int main()
{
    int n;
    cin >> n;
    string *str = new string [n];
    for (int i = 0; i < n; i++)
    {
        cin >> str[i];
    }
    quickSort(str, 0, n-1);
    for (int i = 0; i < n-1; i++)
    {
        cout << str[i] << ' ';
    }
    cout << str[n-1] << endl;
    delete []str;
    return 0;
}
```

### 68.9 字符串排序(2)
题目描述：对输入的字符串进行排序后输出输入描述：多个测试用例，每个测试用例一行。每行通过空格隔开，有n个字符，n＜100输出描述：对于每组测试用例，输出一行排序过的字符串，每个字符串通过空格隔开

示例1

输入a c bbf ddddnowcoder

输出a bb cdddd fnowcoder

```C++
#include<iostream>
#include<string>
#include<algorithm>
#include<vector>
using namespace std;

int main()
{
    string temp;
    vector<string> str;
    while (cin >> temp)
    {
        str.push_back(temp);
        if (cin.get() == '\n')
        {
            sort(str.begin(), str.end());
            int n = int(str.size());
            for (int i = 0; i < n-1; i++)
            {
                cout << str[i] << ' ';
            }
            cout << str[n-1] << endl;
            str.clear();
        }
    }
}
```

### 68.10 字符串排序(3)
题目描述：对输入的字符串进行排序后输出输入描述：多个测试用例，每个测试用例一行。每行通过,隔开，有n个字符，n＜100输出描述：对于每组用例输出一行排序后的字符串，用','隔开，无结尾空格

示例1

输入a,c,bbf,ddddnowcoder

输出a,bb,cdddd,fnowcoder

```C++
#include<iostream>
#include<string>
#include<vector>
#include<algorithm>
using namespace std;

int main()
{
    string temp;
    vector<string> str;
    while (cin >> temp)
    {
        int n = int(temp.length());
        string s = "";
        for (int i = 0; i < n; i++)
        {
            if (temp[i] == ',')
            {
                str.push_back(s);
                s = "";
                continue;
            }
            s.push_back(temp[i]);
        }
        str.push_back(s);
        sort(str.begin(), str.end());
        n = int(str.size());
        for (int i = 0; i < n - 1; i++)
        {
            cout << str[i] << ',';
        }
        cout << str[n-1] << endl;
        str.clear();
    }
}
```


## 69. 序列和
题目描述：给出一个正整数N和长度L，找出一段长度大于等于L的连续非负整数，他们的和恰好为N。答案可能有多个，我我们需要找出长度最小的那个。例如 N = 18 L = 2：5 + 6 + 7 = 183 + 4 + 5 + 6 = 18都是满足要求的，但是我们输出更短的 5 6 7

示例1：

输入18 2

输出5 6 7

```C++
#include<iostream>
using namespace std;

void solution(int N, int L)
{
    for (int m = L; m <= 100; m++)
    {
        int x = N/m + (1-m)/2;
        //cout << x << endl;
        if ((2*x+m-1)*m != 2*N) { continue; }
        for (int i = 0; i < m-1; i++)
        {
            cout << (x + i) << ' ';
        }
        cout << (x + m-1) << endl;
        return;
    }
    cout << "No" << endl;
}


int main(int argc, const char * argv[]) {
    // insert code here...
    int N, L;
    scanf("%d%d", &N, &L);
    solution(N, L);
    return 0;
}
```