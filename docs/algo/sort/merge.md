## 原理
归并排序（Merge Sort）采用分治的思想，将待排序的数据分块，并在块内排好序，块之间重新组织称有序数据。

以递归的方式实现，包含两步：

1）将带排序的数组分成两个子数组，通常是均匀分割，这里代排序的数组长度至少为 2。

2）两个子数组内是有序的，这是合并这两个数组

时间复杂度

$$
T(n) = 2T(\frac{n}{2}) + O(n) \Leftrightarrow T(n) = n \log n
$$

```cpp
template <typename T>
void vector<T>::mergeSort(Rank lo, Rank hi) {
	if (hi - lo < 2) return;
	int mi = (lo + hi) >> 1;
	mergeSort(lo, mi);
	mergeSort(mi, hi);
	merge(lo, mi, hi);
}

template <typename T>
void vector<T>::merge(Rank lo, Rank mi, Rank hi) {
	T *A = _elem + lo;
	int lb = mi - lo;
	T *B = new T[b];
	for (Rank i = 0; i < lb; B[i]=A[i++]);
	int lc = hi - mi;
	T *C = _elem + mi;
	for (Rank i = 0, j = 0, k = 0; (j < lb) || (k < lc); ) {
		if ((j < lb) && (lc <= k || (B[i] <= C[k]))) A[i++] = B[j++];
		if ((k < lc) && (lb <= j || (C[k] < B[j]))) A[i++] = C[k++];
	}
	delete []B;
}
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(n)$。


## 题目
### 数组中的逆序对
题目描述：在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007，例如输入 [1,2,3,4,5,6,7,0]，输出7

```C++
class Solution {
public：
    int InversePairs(vector<int> data) {
        int n = int(data.size());
        if (n < 2) { return 0; }
        long count = 0;
        mergeSort(data, 0, n-1, count);
        return (count % 1000000007);
    }
    void mergeSort(vector<int> &arr, int b, int e, long &count) {
        if (b >= e) { return; }
        int m = (b + e) / 2;
        mergeSort(arr, b, m, count);
        mergeSort(arr, m+1, e, count);
        merge(arr, b, m, e, count);
    }
    void merge(vector<int> &arr, int b, int m, int e, long &count) {
        int *temp = new int [e-b+1]{};
        int i = b, j = m+1, right = 0, pos = 0;
        while (i <= m && j <= e) {
            if (arr[i] < arr[j]) {
                temp[pos++] = arr[i++];
                count += right;
            } else {
                temp[pos++] = arr[j++];
                right++;
            }
        }
        count += (m-i+1) * right;
        while (i <= m) { temp[pos++] = arr[i++]; }
        while (j <= e) { temp[pos++] = arr[j++]; }
        for (int i = b; i <= e; i++) { arr[i] = temp[i-b]; }
        delete []temp;
    }
};
```
时间复杂度 $O(n \log n)$，空间复杂度 $O(n)$
