## 原理
快速排序（Quick Sort）找到一个中轴（pivot）使得右侧的数值不小于中轴值且左侧的数值不大于中轴值。

$$
\begin{aligned}
& S = S_{1} + S_{2} \\
& max(S_{1}) \leq min(S_{2})
\end{aligned}
$$

如果两部分各自有序，那么整个数组也有序。分隔两段这一操作，可以表述成找一个中轴（pivot），用双指针的方法：

1）对于给定的一段数组，在头和尾各放置一个指针 i 和 j

2）如果 S[i] >= S[j] 交换 i 和 j 对应的元素，然后移动 i 或者 j，直到 i == j

```cpp
void quickSort(int *pnt, int start, int end) {
    int i, j;
    i = start;
    j = end;
    if (start < end) {
        while(i < j) {
            while(pnt[i] <= pnt[j] && i < j) {
                j--;
            }
            swap(pnt, i, j);
            while(pnt[i]<=pnt[j] && i < j) {
                i++;
            }
            swap(pnt, i, j);
        }
        quickSort(pnt, start, i-1);
        quickSort(pnt, i+1, end);
    }
}
```
平均时间复杂度：$O(n \log n)$，最差时间复杂度：$O(n^{2})$，空间复杂度：$O(\log n)$。快速排序改变了数组的相对位置，因此它是不稳定排序。

$$
\begin{aligned}
T(n) &= (n + 1) + \frac{1}{n} \\
&= \sum_{k=0}^{n-1}[T(k) + T(n-k-1)] \\
&= (n + 1) + \frac{2}{n} \sum_{k=0}^{n-1}T(k) \\
nT(n) &= n(n+1) + 2\sum_{k=0}^{n-1}T(k) \\
(n-1)T(n-1) &= (n-1)n + 2\sum_{k=0}^{n-2}T(k)
\end{aligned}
$$

$$
\begin{aligned}
nT(n) - (n-1)T(n-1) &= 2n + 2T(n-1) \\
nT(n) - (n+1)T(n-1) &= 2n
\end{aligned}
$$

$$
\begin{aligned}
\frac{T(n)}{n+1} &= \frac{2}{n+1} + \frac{T(n-1)}{n} \\
&= \frac{2}{n+1} + \frac{2}{n} + \frac{T(n-2)}{n-1}
\end{aligned}
$$

$$
\begin{aligned}
\frac{T(n)}{n+1} &= \frac{2}{n+1} + \frac{T(n-1)}{n} \\
&= \frac{2}{n+1} + \frac{2}{n} + \frac{T(n-2)}{n-1} \\
&= \frac{2}{n+1} + \frac{2}{n} + \frac{2}{n-1} + \cdots + \frac{2}{2} + \frac{T(0)}{1} \\
&= (2ln2)\log n \\
&= 1.39\log n
\end{aligned}
$$

## 题目
### 数组中的第K个最大元素
给定整数数组 nums 和整数 k，请返回数组中第 k 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。你必须设计并实现时间复杂度为 O(n) 的算法解决此问题。例如，[3,2,1,5,6,4], k = 2，返回 5。

**「分析」**

「改进版快速排序」

```cpp
int findKthLargest(vector<int>& nums, int k) {
    int n = int(nums.size());
    k = n - k;
    int b = 0, e = n - 1;
    while (true) {
        int i = b, j = e;
        while (i < j) {
            while (i < j && nums[i] <= nums[j]) { j--; }
            swap(nums[i], nums[j]);
            while (i < j && nums[i] <= nums[j]) { i++; }
            swap(nums[i], nums[j]);
        }
        if (i == k) { return nums[i]; }
        else if (i > k) { e = i - 1; }
        else { b = i + 1; }
    }
    return 0;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$。