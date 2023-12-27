## 介绍
递归（recursion）是一种数学方法，通常表达为「调用自身」。


## 题目
### 汉诺塔

**「分析」**

「递归」

```cpp
int hanoi(int n, char x, char y, char z) {
    if (n == 1) {
        printf("%d: %c -> %c\n", n, x, z);
        return 1;
    }
    else {
        // 1至n-1移到y柱，借助z
        int res = hanoi(n-1, x, z, y);
        // n移到z
        printf("%d: %c -> %c\n", n, x, z);
        // 1至n-1移到z柱，借助x
        res += hanoi(n-1, y, x, z);
        return 1+res;
    }
}
```
时间复杂度：$O(2^{n})$，空间复杂度：$O(n)$。
