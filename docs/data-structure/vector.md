## 定义
数组（向量）是一种排列有序的数据结构，这里的「有序」指的是数组的值在内存地址内线形存储。数组支持「寻址访问（call-by-rank）」，可以在 $O(1)$ 时间内找到数据。

$$
A[0], A[1], A[2], \cdots, A[n-1]
$$

Physical address:
内存中的物理地址：

$$
V[ i ]=V[ 0 ]+i\times s
$$

这里 s 代表每一个单元的空间大小。


## 基础运算
根据是否修改数据结构，大约可分为两种操作

| 静态 | 动态 |
| :----: | :----: |
| 读（查） | 写（增、删、改） |


## 可扩容数组
负载因子（load factor）

$$
\lambda = \frac{size}{capacity}
$$

使用可扩容数组的目的是避免数据溢出（overflow）或者过低负载（low load factor）。

|  | 增容（Incremental capacity | 倍容（Double capacity）|
| :----: | :----: | :----: |
| 总时间复杂度 | $O(n^{2})$ | $O(n)$ |
| 摊均时间复杂度 | $O(n)$ | $O(1)$ |
| 负载因子 | $\approx 100%$ | $50%$ |

### 增容
每次增加一个固定长度的空间

```cpp
T *oldElem = _elem;
_elem = new T[_capacity += INCREMENT]; // Increment
```

最坏的情况下，我们对一个初始长度为1的数组，持续插入元素 n = m*I，总体时间如下

$$
0, I, 2I, 3I, \cdots, (m-1)I = I\frac{(m-1)m}{2}
$$

摊均复杂度 $O(n)$。

### 倍容
每次对现有数组的长度加倍

```cpp
void Vector<T>::expand() {
	if (_size < _capacity) return
	_capacity = max(_capacity, DEFAULT_CAPACITY);
	T *oldElem = _elem;
	_elem = new T[_capacity <<= 1]; // Double
	for (int i = 0; i < _size; i++) {
		_elem[i] = oldElem[i];
	}
	delete []oldElem;
}
```

在最坏的情况下，我们对一个初始长度为1的数组，持续插入元素 n = $2^{m}$，我们需要在第 1 次，第 2 次，第 4 次，第 8 次，第 16 次 $\cdots$ 时倍容。总时间

$$
1, 2, 4, 8, \cdots, 2^m = n
$$

摊均复杂度 $O(1)$
