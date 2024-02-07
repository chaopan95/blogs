## 原理
桶排序（Bucket Sort）采用哈希表的方式排序，假设待排序数组的范围是已知的，我们可以对这个范围进行分桶。

1）遍历待排序数组，将每一个元素根据哈希值放到对应的桶里

2）重新遍历每一个桶，将桶内的元素输出，便可得到有序序列

时间复杂度：$O(n+m)$，空间复杂度：$O(m)$。