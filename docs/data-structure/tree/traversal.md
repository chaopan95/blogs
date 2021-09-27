## 遍历
遍历是对二叉树的所有节点做一次无重复的访问。

## 层序遍历
层序遍历通常使用【队列】作为辅助数据结构。

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
