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