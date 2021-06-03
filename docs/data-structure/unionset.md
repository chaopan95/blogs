## 简介
并查集是一种树形的数据结构，主要处理不交集的查询和合并问题，它有两种操作方式：

* 查找（Find）：确定当前结点属于哪一个集合

* 合并（Union）：将两个不同的集合合并在一起

我们通常使用一个数组 *fa[ x ]*，下标 x 代表某一个结点，*fa[ x ]* 表示这个结点的父结点。

## 初始化
起始状态，每一个结点都是自成一个集合

```cpp
for (int i = 0; i < n; i++) {
    fa[i] = i;
}
```

## 查找
我们希望在这一步可以快速地找到当前结点的根结点

```cpp
int find(int x) {
    return x != fa[x] ? fa[x] = find(fa[x]) : x;
}
```

这里用到了*路径压缩*的方法。在寻找根结点的过程中，将所有的子节点连接到根结点。

$$
\begin{matrix}
& & & & & & 1 \\
& & & & \nearrow & & & & & \nwarrow \\
& & & 2 & & & & & & & 3 \\
& & \nearrow & & \nwarrow & & & & & \nearrow & & & \nwarrow \\
& 4 & & & & 5 & & & 6 & & & & & 7 \\
& & & & \nearrow & & \nwarrow \\
& & & 8 & & & & 9 
\end{matirx} \quad \Rightarrow \quad
\begin{matrix}
& & 9 & & & 2 & & & 3\\
& & & \searrow & & \downarrow & & \swarrow \\
& & 8 & \rightarrow & & 1 & & \leftarrow & 4 \\
& & & \nearrow && \uparrow & & \nwarrow \\
& & 7 & & & 6 & & & 5
\end{matrix}
$$

## 合并
由于某些约束，两个原本不相交的集合发生了联系，这个时候，需要叫其中的一个集合合并到另一个集合中。

```cpp
void Union(int x, int y) {
    fa[find(x)] = find(y);
}
```

这里我们处理地比较简单，将其中一个集合的根结点当作另一个集合的根结点的孩子。如果我们考虑将深度小的树合并到深度较大的树下，查询效率会提高。