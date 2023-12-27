## 方阵旋转
给定一个方阵 $n \times n$，将其按照顺时针的方向旋转 90˚。要求使用常数空间。

**「分析」**

直接按照题意进行模拟，我们注意到一次旋转 90˚ 相当于所有的元素顺时针转动一次，例如 (0, 0) -> (0, n-1) -> (n-1, n-1) -> (n-1, 0) -> (0, 0)。并且，对于同一个外圈的元素均是如此。

```cpp
void rotate(vector<vector<int>>& matrix) {
    int n = (int)matrix.size();
    if (n <= 1) { return; }
    for (int i = 0; i < n / 2; i++) {
        for (int j = i; j < n - i - 1; j++) {
            int tmp = matrix[i][j];
            matrix[i][j] = matrix[n - 1 - j][i];
            matrix[n - 1 - j][i] = matrix[n - 1 - i][n - 1 - j];
            matrix[n - 1 - i][n - 1 - j] = matrix[j][n - 1 - i];
            matrix[j][n - 1 - i] = tmp;
        }
    }
}
```
时间复杂度 $O(n^{2})$，空间复杂度 $O(1)$