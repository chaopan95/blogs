## 分治 Divider and Conquer
### 数组求和
```cpp
int sum(int A[], int lo, int hi) {
    if (lo == hi) { return A[lo]; }
    int mi = (lo + hi) >> 1;
    return sum(A, lo, mi) + sum(A, mi+1, hi);
}
```

时间复杂度分析

$$
\begin{aligned}
T(n) &= 2T(\frac{n}{2}) \\
&= 2 \cdot 2 T(\frac{n}{4}) \\
&= 2^{\log_{2}n} \cdot T(1) \\
&= n
\end{aligned}
$$

时间复杂度 $O(hi - lo)$，空间复杂度 $O(hi - lo)$。


## 减治 Decrease and Conquer
### 反转数组

```cpp
void reverse(int *A, int lo, int hi) {
    if (lo < hi) {
        swap(A[lo], A[hi]);
        reverse(A, lo+1, hi-1);
    }
}
```

### 搜索二维矩阵
给定一个二维数组，列方向从小到大，行方向从小到大，又给定一个数字，试查询此数字是否存在这个二维数组中。

$$
\begin{matrix}
1 & 4 & 7 \\
2 & 5 & 8 \\
3 & 6 & 9
\end{matrix}
$$

查询 5 ，返回 true

```cpp
bool searchMatrix(vector<vector<int>>& matrix, int target) {
    int nRow = (int)matrix.size();
    if (nRow == 0) { return false; }
    int nCol = (int)matrix[0].size();
    if (nCol == 0) { return false; }
    int row = nRow - 1, col = 0;
    while (row >= 0 && col < nCol) {
        if (matrix[row][col] == target) { return true; }
        else if (matrix[row][col] > target) { row--; }
        else { col++; }
    }
    return false;
}
```
时间复杂度 $O(row + col)$，空间复杂度 $O(1)$。
