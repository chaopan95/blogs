## 监控二叉树
给定一个二叉树，我们在树的节点上安装摄像头。节点上的每个摄影头都可以监视其父对象、自身及其直接子对象。计算监控树的所有节点所需的最小摄像头数量。例如，

$$
\begin{matrix}
& & & & & 0 \\
& & & & & \downarrow \\
& & & & & {\color{Red} 1} \\
& & & \swarrow & & & & \searrow \\
& & 0 & & & & & & 0 \\
\end{matrix}
$$

只要在红色节点处放置一个监控即可。

**「分析」**

考虑简单的结构：一个根结点（v）和左（l）右（r）子节点，在所有节点被覆盖的条件下，有三种状态

a）v 处放置一个监控时，所需要的最少数目。v、l、r 被覆盖。左右子节点的子树也需要被覆盖

b）v 被覆盖但是不需要监控时的最少数目。此时 l 和 r 有一处放监控

c）l 和 r 被覆盖的最小数目，此时 v 不一定被覆盖

状态转移方程

$$
\begin{aligned}
& v_{a} = l_{c} + r_{c} + 1 \\
& v_{b} = \min {(v_{a}, l_{a} + r_{b}, l_{b} + r_{a})} \\
& v_{c} = \min {(v_{a}, l_{b} + r_{b})}
\end{aligned}
$$


```cpp
struct states {
    int a, b, c;
    states(int x, int y, int z): a(x), b(y), c(z) {}
};

class Solution {
public:
    int minCameraCover(TreeNode* root) {
        states s = dfs(root);
        return s.b;
    }
    states dfs(TreeNode *root) {
        states s(INT_MAX / 2, 0, 0);
        if (root == nullptr) {
            return s;
        }
        states l = dfs(root->left);
        states r = dfs(root->right);
        s.a = l.c + r.c + 1;
        s.b = min(s.a, min(l.a+r.b, l.b+r.a));
        s.c = min(l.b+r.b, s.a);
        return s;
    }
};

```
时间复杂度 $O(n)$，空间复杂度 $O(log(n))$。
