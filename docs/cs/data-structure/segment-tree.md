## 简介
线段树是一种高效地维护区间信息（和、最大值、最小值等）的一种树形结构。线段树有两个主要的接口

* 查询（query），例如求区间和

* 更新（update），更新某一位置的值，对应的所有区间和将被更新

两者的时间复杂度均为 $O(\log n)$。

## 原理
### 建树
线段树与最小堆类似，以二叉树来建立，承载的形式是一个数组。但是，线段树的根结点对应的索引是 1；线段树是一颗完满二叉树（full binary tree），每一个非叶子结点都有两个孩子结点，最后一层靠左排列。

叶子结点是奇数

$$
\begin{matrix}
& & 1 \\
& & \wedge \\
& 2 & & {\color{Red} 3} \\
& \wedge \\
{\color{Red} 4} & & {\color{Red} 5} \\
\end{matrix} \quad \quad \quad \text{3、4、5 是叶子结点}
$$

叶子结点是偶数

$$
\begin{matrix}
& & & 1 \\
& & & \wedge \\
& 2 & & & & 3 \\
& \wedge & & & & \wedge \\
{\color{Red} 4} & & {\color{Red} 5} & & {\color{Red} 6} & & {\color{Red} 7}
\end{matrix} \quad \quad \quad \text{4、5、6、7 是叶子结点}
$$

完满二叉树的叶子结点个数与非叶子结点个数有一个关系

$$
n_{\text{leaves}} = n_{non-leaves} + 1
$$

因此，线段树将原数组的每一个值都放在叶子结点上，自底向上地建树。

```cpp
void initialize(vector<int> nums) {
    int n = (int)nums.size();
    vector<int> A(n * 2, 0);
    for (int j = n, i = 0; i < n; i++, j++) {
        A[j] = nums[i];
    }
    for (int j = n - 1; j > 0; j--) {
        A[j] = A[j * 2] + A[j * 2 + 1];
    }
}
```
时间复杂度 $O(n)$，空间复杂度 $O(n)$

### 查询
线段树每一个非叶子结点都表示一个区间信息，以区间和为例，根结点表示整个区间和。理想情况下，我们希望直接返回一个区间的和，但往往给出的区间边界不是恰好对应一个非叶子结点，这个时候，我们需要将要求的区间分割，求每一个子区间的和，即可得到最终结果。

```cpp
int sumRange(int left, int right) {
    int sum = 0;
    left += n;
    right += n;
    while (left <= right) {
        if (left % 2 != 0) {
            sum += A[left++];
        }
        if (right % 2 == 0) {
            sum += A[right--];
        }
        left >>= 1;
        right >>= 1;
    }
    return sum;
}
```

### 更新
当原数组的一个或若干个数值发生改变时，对应的区间信息也要发生变化，从叶子结点开始更新路径上的所有的结点。以区间和为例

```cpp
void update(int index, int val) {
    index += n;
    int diff = val - A[index];
    while (index) {
        A[index] += diff;
        index >>= 1;
    }
}
```


## 题目
