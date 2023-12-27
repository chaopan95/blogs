## 原理

## 冒泡排序
冒泡排序（Bubble Sort）包含两步：

1）扫面待排序数组，如果相邻两个元素逆序，交换两者。

2）重复步骤 1）直到所有元素有序

```cpp
void BubbleSort(int A[], int n) {
	for (bool started = false; sorted = !sorted; n--) {
		for (int i = 1; i < n; i++) {
			if (A[i-1] > A[i]) {
				swap(A[i-1], A[i]);
				sorted = false;
			}
		}
	}
}
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(1)$。

因为我们检查是否存在相邻的元素逆序，那么某一次扫描后，无 swap 发生，便可以认为整个数组有序，提前结束。

```cpp
template <typename T>
bool vector<T>::bubble(Rank lo, Rank hi) {
	bool sorted = true;
	while (++lo < hi) {
		if (_elem[lo-1] > _elem[lo]) {
			sorted = false;
			swap(_elem[lo-1], _elem[lo]);
		}
	}
	return sorted;
}

template <typename T>
void vector<T>::bubbleSort(Rank lo, Rank hi) {
	while (!bubble(lo, hi--));
}
```
时间复杂度：$O(n^{2})$，空间复杂度：$O(1)$。
