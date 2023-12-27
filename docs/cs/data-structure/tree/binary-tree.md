## 简介
二叉树是树的一种，每一个结点最多有两个孩子结点。

## 性质
* 倘若根结点在第 0 层，那么每一层的结点数目最多有 $2^{i}$ 个。

* 一棵树的深度为 d，那么它最多有 $2^{d} - 1$ 个结点

* 如果一颗二叉树的叶子结点个数是 n，那么有两个孩子的结点个数为 n - 1

## 完全二叉树、完满二叉树和完美二叉树

>> A Complete Binary Tree（CBT) is a binary tree in which every level, except possibly the last, is completely filled, and all nodes are as far left as possible.

完全二叉树（complete binary tree）除了最后一层外全部填满，最后一层的结点靠左填充

$$
\begin{matrix}
& & & & & & 1 \\
& & & & & & \wedge \\
& & & 2 & & & & & & & & 3 \\
& & & \wedge & & & & & & & & \wedge \\
& 4 & & & & 5 & & & & 6 & & & & 7 \\
& \wedge & & & & \wedge \\
8 & & 9 & & 10 & & \text{null}
\end{matrix}
$$

>> A Full Binary Tree (FBT) is a tree in which every node other than the leaves has two children.

完满二叉树（full binary tree）所有非叶子结点的度是 2。

$$
\begin{matrix}
& & & & & & 1 \\
& & & & & & \wedge \\
& & & 2 & & & & & & & & 3 \\
& & & \wedge & & & & & & & & \wedge \\
& 4 & & & & 5 & & & & 6 & & & & 7 \\
& & & & & \wedge & & & & & & & & \wedge \\
& & & & 8 & & 9 & & & & & & 14 & & 15
\end{matrix}
$$

>> A Perfect Binary Tree(PBT) is a tree with all leaf nodes at the same depth. All internal nodes have degree 2.

完美二叉树（perfec binary tree）所有的叶子结点处于同一层，每一个非叶子结点的度都是 2。

$$
\begin{matrix}
& & & & & & & 1 \\
& & & & & & & \wedge \\
& & & 2 & & & & & & & & 3 \\
& & & \wedge & & & & & & & & \wedge \\
& 4 & & & & 5 & & & & 6 & & & & 7 \\
& \wedge & & & & \wedge & & & & \wedge & & & & \wedge \\
8 & & 9 & & 10 & & 11 & & 12 & & 13 & & 14 & & 15
\end{matrix}
$$

## 最近公共祖先
### 二叉树的最近公共祖先
给定一颗二叉树和两个节点p、q，求这两个节点的最近公共祖先。如下例

$$
\begin{matrix}
& & & & & {\color{Green} 1} \\
& & & \swarrow & & & & \searrow \\
& & 2 & & & & & & {\color{Red} 3} \\
& & & \searrow \\
& & & & {\color{Red} 4}
\end{matrix}
$$

**「分析」**

对于每一个节点，我们用「深度优先搜索」找到唯一路径。之后本题转化为给定两个链表，求分叉节点。

```cpp
vector<TreeNode*> dfs(TreeNode *root, TreeNode *t) {
    vector<TreeNode*> ans;
    stack<TreeNode*> stk;
    if (root == nullptr || t == nullptr) { return ans; }
    stk.push(root);
    while(!stk.empty()) {
        TreeNode *curNode = stk.top();
        stk.pop();
        ans.emplace_back(curNode);
        if (curNode == t) {
            return ans;
        }
        if (curNode->right != nullptr) {
            stk.push(curNode->right);
        }
        if (curNode->left != nullptr) {
            stk.push(curNode->left);
        }
        if (curNode->right == nullptr && curNode->left == nullptr) {
            while (!ans.empty() && ans.back()->right != stk.top()) {
                ans.pop_back();
            }
        }
    }
    return ans;
}

TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (root == nullptr || p == nullptr || q == nullptr) {
        return nullptr;
    }
    vector<TreeNode*> vp = dfs(root, p);
    vector<TreeNode*> vq = dfs(root, q);
    int np = (int)vp.size(), nq = (int)vq.size();
    for (int i = 1; i < min(np, nq); i++) {
        if (vp[i] != vq[i]) {
            return vp[i - 1];
        }
    }
    return vp[min(np, nq) - 1];
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$

### 二叉搜索树的最近公共祖先
二叉搜索树要求元素有序。

**「分析」**

我们利用【二叉搜索树】的特性，当前节点的值介于左孩子和右孩子的值之间

```cpp
TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
    if (root == nullptr || p == nullptr || q == nullptr) {
        return nullptr;
    }
    while (root != nullptr) {
        if (root->val < min(p->val, q->val)) {
            root = root ->right;
        }
        else if (root->val > max(p->val, q->val)) {
            root = root->left;
        }
        else {
            return root;
        }
    }
    return root;
}
```
时间复杂度：$O(\log n)$，空间复杂度：$O(1)$

### 二叉树序列化与反序列化

```cpp
class Codec {
public:
    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        if (root == nullptr) {
            return "#,";
        }
        string str = to_string(root->val) + ",";
        str += serialize(root->left) + serialize(root->right);
        return str;
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        return deserialization(data);
    }
    TreeNode* deserialization(string &data) {
        if (data.empty()) { return nullptr; }
        if (data.front() == '#') {
            data.erase(data.begin());
            data.erase(data.begin());
            return nullptr;
        }
        string val = "";
        while (data.front() != ',') {
            val.push_back(data.front());
            data.erase(data.begin());
        }
        data.erase(data.begin());
        TreeNode *root = new TreeNode(stoi(val));
        root->left = deserialization(data);
        root->right = deserialization(data);
        return root;
    }
};
```
