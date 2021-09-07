## 简介
并查集是一种树形的数据结构，主要处理不交集的查询和合并问题，它有两种操作方式：

* 查找（Find）：确定当前结点属于哪一个集合

* 合并（Union）：将两个不同的集合合并在一起

我们通常使用一个数组 **fa[ x ]**，下标 x 代表某一个结点，fa[ x ] 表示这个结点的父结点。

## 原理
### 初始化
起始状态，每一个结点都是自成一个集合

```cpp
for (int i = 0; i < n; i++) {
    fa[i] = i;
}
```

### 查找
我们希望在这一步可以快速地找到当前结点的根结点

```cpp
int find(int x) {
    return x != fa[x] ? fa[x] = find(fa[x]) : x;
}
```

这里用到了**路径压缩**的方法。在寻找根结点的过程中，将所有的子节点连接到根结点。

$$
\begin{matrix}
\begin{matrix}
& & & & & & 1 \\
& & & & \nearrow & & & & & \nwarrow \\
& & & 2 & & & & & & & 3 \\
& & \nearrow & & \nwarrow & & & & & \nearrow & & & \nwarrow \\
& 4 & & & & 5 & & & 6 & & & & & 7 \\
& & & & \nearrow & & \nwarrow \\
& & & 8 & & & & 9 
\end{matrix} \\
\\
\Downarrow \text{路径压缩} \\
\\
\begin{matrix}
& & 9 & & & 2 & & & 3\\
& & & \searrow & & \downarrow & & \swarrow \\
& & 8 & \rightarrow & & 1 & & \leftarrow & 4 \\
& & & \nearrow && \uparrow & & \nwarrow \\
& & 7 & & & 6 & & & 5
\end{matrix}
\end{matrix}
$$

### 合并
由于某些约束，两个原本不相交的集合发生了联系，这个时候，需要将其中的一个集合合并到另一个集合中。

$$
\begin{matrix}
\begin{matrix}
& & & & & & & & & {\color{Blue} 1} \\
& & & & & & & & & \uparrow \\
& & {\color{Red} 2} & & & & & & & {\color{Blue} 3} \\
& \nearrow & & \nwarrow & & & & & \nearrow & & & \nwarrow \\
{\color{Red} 4} & & & & {\color{Red} 5} & & & {\color{Blue} 6} & & & & & {\color{Blue} 7} \\
\end{matrix} \\
\\
\Downarrow \text{合并}
\\
\begin{matrix}
& & & & & {\color{Blue} 1} \\
& & & \nearrow & & & & & \nwarrow \\
& & {\color{Red} 2} & & & & & & & {\color{Blue} 3} \\
& \nearrow & & \nwarrow & & & & & \nearrow & & & \nwarrow \\
{\color{Red} 4} & & & & {\color{Red} 5} & & & {\color{Blue} 6} & & & & & {\color{Blue} 7} \\
\end{matrix}
\end{matrix}
$$

```cpp
void Union(int x, int y) {
    fa[find(x)] = find(y);
}
```

这里我们处理地比较简单，将其中一个集合的根结点当作另一个集合的根结点的孩子。如果我们考虑将深度小的树合并到深度较大的树下，查询效率会提高。这种方法我们称之为**按秩合并**。

```cpp
void unify(int x, int y) {
    int fx = find(x), fy = find(y);
    if (dep[fx] < dep[fy]) {
        fa[fx] = fy;
        dep[fy] = max(dep[fy], dep[fx] + 1);
    }
    else {
        fa[fy] = fx;
        dep[fx] = max(dep[fx], dep[fy] + 1);
    }
}
```

当然，除了按照深度合并，我们也可以按照树的结点数目合并。因为树的结点数目和深度不总会出现的同一侧（即一个集合的结点数目多且深度大），我们通常选择其中一个作为合并的依据。

## 题目
### 朋友圈问题
给定一个 $n \times n$ 的矩阵 friends，friends[ i ][ j ] = 1 表示 i 和 j 为朋友，反之，表示非朋友，根据这个矩阵找出当前这 n 个人中有几个朋友圈。例如，A 与 B 是朋友，B 与 C 是朋友，那么A、B、C 是一个朋友圈的，即便是A 与 C 不是朋友。

**「分析」**

这是典型的并查集问题，我们需要将每一个人初始化为一个朋友圈，如果两人是朋友就将这两人所在的集合合并，最终只需要统计 fa[ i ] == i 的个数即可。

```cpp
vector<int> fa, dep;

int find(int x) {
    if (x != fa[x]) {
        fa[x] = find(fa[x]);
        dep[x] = 1;
    }
    dep[fa[x]] = 2;
    return fa[x];
}

void unify(int x, int y) {
    int fx = find(x), fy = find(y);
    if (dep[fx] < dep[fy]) {
        fa[fx] = fy;
        dep[fy] = max(dep[fy], dep[fx] + 1);
    }
    else {
        fa[fy] = fx;
        dep[fx] = max(dep[fx], dep[fy] + 1);
    }
}

int findCircleNum(vector<vector<int>>& isConnected) {
    int n = (int)isConnected.size();
    if (n < 2) { return n; }
    fa.resize(n, 0);
    dep.resize(n, 1);
    for (int i = 0; i < n; i++) {
        fa[i] = i;
    }
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (isConnected[i][j]) {
                unify(i, j);
            }
        }
    }
    int ans = 0;
    for (int i = 0; i < n; i++) {
        ans += (i == fa[i]);
    }
    return ans;
}
```
时间复杂度 $O(n^{2} \log n)$，空间复杂度 $O(n)$


### 按照公因数分群
给定一个数组 nums = [20, 50, 9, 63]，如果两个数有大于 1 的公因数，这两个数属于同一个群，群成员数目最多是多少？给出的例子是 2。

**「分析」**

【因子分解 + 并查集】。将每一个数与其质因子划分到同一个群，nums 中的数可以通过共同的质因子连接。

```cpp
vector<int> fa;

int find(int x) {
    return x != fa[x] ? fa[x] = find(fa[x]) : x;
}

void unify(int x, int y) {
    fa[find(y)] = find(x);
}

int largestComponentSize(vector<int>& nums) {
    int n = *max_element(nums.begin(), nums.end());
    fa.resize(n + 1);
    for (int i = 0; i <= n; i++) {
        fa[i] = i;
    }
    for (int num : nums) {
        for (int prime = 2; prime <= (int)sqrt(num); prime++) {
            if (num % prime == 0) {
                unify(num, prime);
                unify(num, num / prime);
            }
        }
    }
    vector<int> cnt(n + 1, 0);
    for (int num : nums) {
        cnt[find(num)]++;
    }
    return *max_element(cnt.begin(), cnt.end());
}
```
时间复杂度：$O(n \sqrt{n})$，空间复杂度：$O(n)$，其中 n 是 nums 列表中的最大值。


### 按公因子排序数组
给定一个数组 nums = [7, 21, 3]，如果两个元素的共因子大于 1，那么我们可以将其交换，试问，能否最终得到一个排序数组？

**「分析」**

【并查集】如果 a 和 b 可交换，b 和 c 可交换，那么 a 和 c 可交换。因此，同一个集合的元素任意交换，即可得到一个排序数组。我们借助一个数与它的所有因子，构造并查集，这样避免 $O(n^{2})$ 的时间复杂度。最终，我们用排序好的数组与为排序的数组逐一比较，如果两个数字不属于同一个集合，那么 nums 是不能有序的。

```cpp
vector<int> fa;

int find(int x) {
    return x != fa[x] ? fa[x] = find(fa[x]) : x;
}

void unify(int x, int y) {
    fa[find(y)] = find(x);
}

bool gcdSort(vector<int>& nums) {
    int n = *max_element(nums.begin(), nums.end()) + 1;
    fa.resize(n);
    for (int i = 0; i < n; i++) {
        fa[i] = i;
    }
    for (int num : nums) {
        for (int prime = 2; prime <= (int)sqrt(num); prime++) {
            if (num % prime == 0) {
                unify(num, prime);
                unify(num, num / prime);
            }
        }
    }
    vector<int> sortedNums = nums;
    sort(sortedNums.begin(), sortedNums.end());
    for (int i = 0; i < (int)nums.size(); i++) {
        if (nums[i] == sortedNums[i]) { continue; }
        if (find(nums[i]) != find(sortedNums[i])) {
            return false;
        }
    }
    return true;
}
```
时间复杂度：$O(m \sqrt{m})$，空间复杂度：$O(m)$，其中 m 是 nums 中的最大值
