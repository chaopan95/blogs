## 遍历
遍历是对二叉树的所有节点做一次无重复的访问。


## 前序遍历

```cpp
// 迭代版
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> ans;
    if (root == nullptr) { return ans; }
    TreeNode *curNode = root;
    stack<TreeNode*> st;
    st.push(root);
    while (!st.empty()) {
        curNode = st.top();
        st.pop();
        ans.push_back(curNode->val);
        if (curNode->right != nullptr) { st.push(curNode->right); }
        if (curNode->left != nullptr) { st.push(curNode->left); }
    }
    return ans;
}

// 递归版
vector<int> preorderTraversal(TreeNode* root) {
    vector<int> ans;
    if (root == nullptr) { return ans; }
    TreeNode *curNode = root;
    stack<TreeNode*> st;
    while (true) {
        while (curNode != nullptr) {
            ans.push_back(curNode->val);
            if (curNode->right != nullptr) { st.push(curNode->right); }
            curNode = curNode->left;
        }
        if (st.empty()) { break; }
        curNode = st.top();
        st.pop();
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(\log n)$。

### 二叉树的右视图
给定一个二叉树，从右向左看，返回看到的序列（由上及下）

```cpp
/*
   1            <---
 /   \
2     3         <---
 \     \
  5     4       <---
*/
class Solution {
public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> ans;
        if (root == nullptr) { return ans; }
        int maxLevel = -1;
        rightOrder(root, ans, 0, maxLevel);
        return ans;
    }
    void rightOrder(TreeNode *root, vector<int> &ans, int level, int &maxLevel) {
        if (root != nullptr) {
            if (level > maxLevel) {
                ans.emplace_back(root->val);
                maxLevel = level;
            }
            rightOrder(root->right, ans, level+1, maxLevel);
            rightOrder(root->left, ans, level+1, maxLevel);
        }
    }
};
```
时间复杂度：$O(n)$，空间复杂度：$O(\log n)$。


## 中序遍历


## 后序遍历
### 二叉树最近公共祖先

```cpp
/*
                3
            /       \
           5         1
         /   \     /   \
        6     2   0     8
             / \
            7   4
p = 5,  q = 1
nearest common ancester is 3
*/

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        vector<TreeNode*> t1, t2, t;
        dfs(root, p, t1, t);
        t.clear();
        dfs(root, q, t2, t);
        TreeNode *ans = nullptr;
        for (int i = 0; i < t1.size() && i < t2.size(); i++) {
            if (t1[i] != t2[i]) {
                break;
            }
            ans = t1[i];
        }
        return ans;
    }
    void dfs(TreeNode *root, TreeNode *node, vector<TreeNode*> &res, vector<TreeNode*> &t) {
        if (root == nullptr) { return; }
        t.emplace_back(root);
        if (root == node) {
            for (auto ele : t) {
                res.emplace_back(ele);
            }
            return;
        }
        dfs(root->left, node, res, t);
        dfs(root->right, node, res, t);
        t.pop_back();
    }
};
```

### 二叉树的直径
给你一棵二叉树的根节点，返回该树的直径。二叉树的直径是指树中任意两个节点之间最长路径的长度。这条路径可能经过也可能不经过根节点 root 。

$$
\begin{matrix}
& & & & & 1 \\
& & & & \swarrow & & \searrow \\
& & & 2 & & & & 3 \\
& & \swarrow & & \searrow \\
& 4 & & & & 5
\end{matrix}
$$

返回 3。

**「分析」**

「后序遍历」求出每一个子树的长度。

```cpp
int getDepth(TreeNode *root, int &maxDiameter) {
    if (root == nullptr) { return 0; }
    int left = getDepth(root->left, maxDiameter);
    int right = getDepth(root->right, maxDiameter);
    maxDiameter = max(maxDiameter, left + right);
    return max(left, right) + 1;
}
int diameterOfBinaryTree(TreeNode* root) {
    int ans = 0;
    getDepth(root, ans);
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(\log n)$。


## 层序遍历
层序遍历通常使用【队列】作为辅助数据结构。

```cpp
vector<vector<int>> levelOrder(TreeNode* root) {
    vector<vector<int>> ans;
    vector<int> arr;
    if (root == nullptr) { return ans; }
    queue<TreeNode*> qTree;
    qTree.push(root);
    TreeNode *front = root, *last = root, *nextLast = root;
    while(!qTree.empty()) {
        front = qTree.front();
        qTree.pop();
        arr.emplace_back(front->val);
        if (front->left != nullptr) {
            qTree.push(front->left);
            nextLast = front->left;
        }
        if (front->right != nullptr) {
            qTree.push(front->right);
            nextLast = front->right;
        }
        if (front == last) {
            ans.emplace_back(arr);
            arr.clear();
            last = nextLast;
        }
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$。

### 填充二叉树的 next 指针
给定一棵二叉树，每一个节点除了 left 和 right 指针，还有一个指向同层右侧 next 指针，初始为 nullptr，实现一个算法，填充 next。

$$
\begin{matrix}
& & & & 1 \\
& & & \swarrow & & \searrow \\
& & 2 & & {\color{Red} \Rightarrow} & & 3 \\
& \swarrow & & \searrow & & & & \searrow \\
4 & & {\color{Red} \Rightarrow} & &  5  & & {\color{Red} \Rightarrow} & & 6
\end{matrix}
$$

**「分析」**

【层序遍历】利用 next 指针，无需 queue 辅助空间 。假设当前节点是 cur，cur->left（如果存在）指向 cur->right 或者 cur->next->left / right（找到 cur 的第一个 left 或 right 不空的 next）。

```cpp
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};

Node* connect(Node* root) {
    if (root == nullptr) {
        return root;
    }
    Node *nextFirst = root, *cur = root;
    while (cur != nullptr) {
        Node *left = cur->left, *right = cur->right, *next = cur->next;
        if (left != nullptr) {
            if (right != nullptr) {
                left->next = right;
            }
            if (nextFirst == nullptr) {
                nextFirst = left;
            }
        }
        if (right != nullptr && nextFirst == nullptr) {
            nextFirst = cur->right;
        }
        while (next != nullptr) {
            if (next->left != nullptr) {
                if (left != nullptr && left->next == nullptr) {
                    left->next = next->left;
                }
                if (right != nullptr) {
                    right->next = next->left;
                }
                break;
            }
            if (next->right != nullptr) {
                if (left != nullptr && left->next == nullptr) {
                    left->next = next->right;
                }
                if (right != nullptr) {
                    right->next = next->right;
                }
                break;
            }
            next = next->next;
        }

        if (next == nullptr) {
            cur = nextFirst;
            nextFirst = nullptr;
        }
        else {
            cur = next;
        }
    }
    return root;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$
