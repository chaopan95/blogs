## 最大公约数 & 最小公倍数
### 两数最大公约数
```cpp
/*
Brutal force: enumerate all numbers from 1 to min(a, b), save the greatest.
*/
int GCD_enumerate(int a, int b) {
    int c = (a < b ? a : b);
    int gcd = 1;
    for (int i = 1; i <= c; i++) {
        if (a % i == 0 && b % i == 0) {
            gcd = i;
        }
    }
    return gcd;
}

int GCD_Euclidean_Algorithm_divide(int a, int b) {
    while (a * b) {
        if (a < b) { b %= a; }
        else { a %= b; }
    }
    return (a > b ? a : b );
}

int GCD_Euclidean_Algorithm_subtract(int a, int b) {
    while (a * b) {
        if (a < b) { b -= a; }
        else { a -= b; }
    }
    return (a > b ? a : b);
}
```

### 数组的最大公约数
```cpp
int GCD_Array(vector<int> arr) {
    int n = int(arr.size());
    if (n == 1) { return arr[0]; }
    int gcd = GCD_Euclidean_Algorithm_divide(arr[0], arr[1]);
    for (int i = 2; i < n; i++) {
        gcd = GCD_Euclidean_Algorithm_divide(gcd, arr[i]);
    }
    return gcd;
}
```

### 最小公倍数
$$
\text{LCM} = \frac{a * b}{\text{GCD}(a, b)}
$$

```cpp
int LCM(int a, int b) {
    int gcd = GCD_Euclidean_Algorithm_divide(a, b);
    return (a * b) / gcd;
}
```

## 位运算
### 原码 反码 补码
**「原码」**

第一位是符号位，其余表示值

$$
\begin{matrix}
\text{[ +1 ]}_{\text{原}} &= \text{0000 0001} \\
\text{[ -1 ]}_{\text{原}} &= \text{1000 0001}
\end{matrix}
$$

8 位二进制原码的可以表示的范围是 [1111 111, 0111 111] 即 [-127, 127]

**「反码」**

正数的反码是其本身，负数的反码是符号位不变，其余位置逐个取反。

$$
\begin{matrix}
\text{[ +1 ]} &= \text{[0000 0001]}_{\text{原}} &= \text{[0000 0001]}_{\text{反}} \\
\text{[ -1 ]} &= \text{[1000 0001]}_{\text{原}} &= \text{[1111 1110]}_{\text{反}}
\end{matrix}
$$

**「补码」**

正数的补码是其本身，负数的补码在其反码的最后一位加 1。

$$
\begin{matrix}
\text{[ +1 ]} &= \text{[0000 0001]}_{\text{原}} &= \text{[0000 0001]}_{\text{反}} &= \text{[0000 0001]}_{\text{补}} \\
\text{[ -1 ]} &= \text{[1000 0001]}_{\text{原}} &= \text{[1111 1110]}_{\text{反}} &= \text{[1111 1111]}_{\text{补}}
\end{matrix}
$$

**「引入反码的原因」**

计算机二进制运算做加减乘除计算时，希望其设计模式比较简单，但是对于负数而言，原码存在符号位，如果参与二进制计算的话，容易出现错误答案。

$$
\begin{aligned}
\text{1 - 1} &= \text{1 + (-1)} \\
&= \text{[0000 0001]}_{\text{原}} + \text{[1000 0001]}_{\text{原}} \\
&= \text{[1000 0002]}_{\text{原}} \\
&= -2
\end{aligned}
$$

所以需要引入反码

$$
\begin{aligned}
\text{1 - 1} &= \text{1 + (-1)} \\
&= \text{[0000 0001]}_{\text{反}} + \text{[1111 1110]}_{\text{反}} \\
&= \text{[1111 1111]}_{\text{反}} = \text{[1000 0000]}_{\text{原}} \\
&= -0
\end{aligned}
$$

为了解决 -0 问题，补码应运而生

$$
\begin{aligned}
\text{1 - 1} &= \text{1 + (-1)} \\
&= \text{[0000 0001]}_{\text{补}} + \text{[1111 1111]}_{\text{补}} \\
&= \text{[0 0000 0000]}_{\text{补}} \\
&= \text{[0 0000 0000]}_{\text{原}} \\
&= 0
\end{aligned}
$$

此外[1000 0000]表示 -128

$$
\begin{aligned}
\text{-1 - 127} &= \text{[1111 1111]}_{\text{补}} + \text{[1000 0001]}_{\text{补}} \\
&= \text{[1000 0000]}_{\text{补}} \\
&= \text{[11 0000 000]}_{\text{原}} \\
&= -128
\end{aligned}
$$

因此 8 位二进制可以表示的数字范围是 [-128, 127]。

### XOR
按位异或 (XOR) 对于二进制的两个数a， b在相同位置上的做位运算：同为 1 或者同为 0 则为 0； 反之为 1。

$$
\begin{matrix}
4 & = & 0 & 1 & 0 & 0 \\
\text{XOR} & & & & & \\
10 & = & 1 & 0 & 1 & 0 \\
\Downarrow & & & & & \\
14 & = & 1 & 1 & 1 & 0
\end{matrix}
$$

两个性质

$$
\begin{aligned}
& \text{a XOR a = 0} \\
& \text{a XOR b = c} \leftrightarrow \text{a = c XOR b}
\end{aligned}
$$

**「题目」**

??? note "[「Leetcode 1734. 解码异或后的排列」](https://leetcode-cn.com/problems/decode-xored-permutation/)"

    ```cpp
    /*
    给你一个整数数组 perm ，它是前 n 个正整数的排列，且 n 是个 奇数 。
    它被加密成另一个长度为 n - 1 的整数数组 encoded ，满足
    encoded[i] = perm[i] XOR perm[i + 1] 。比方说，如果 perm = [1,3,2] ，
    那么 encoded = [2,1] 。
    给你 encoded 数组，请你返回原始数组 perm 。题目保证答案存在且唯一。

    示例 1：
    输入：encoded = [3,1]
    输出：[1,2,3]
    解释：如果 perm = [1,2,3] ，那么 encoded = [1 XOR 2,2 XOR 3] = [3,1]

    示例 2：
    输入：encoded = [6,5,4,6]
    输出：[2,4,1,5,3]
    */
    /*
    perm包含了1-n+1的所有数字，n为encoded的长度
    total = perm[0] ^ ... ^ perm[n-1]
    perm[0] ^ perm[1] = encoded[0]
    perm[1] ^ perm[2] = encoded[1]
    perm[2] ^ perm[3] = encoded[2]
    perm[3] ^ perm[4] = encoded[3]
    所以，encoded数组中奇数位置的元素XOR，对应了perm1-n的XOR，
    由此，可以求得perm[0]的大小
    */
    vector<int> decode(vector<int>& encoded) {
        int n = (int)encoded.size();
        vector<int> perm;
        int total = 0;
        for (int i = 1; i <= n+1; i++) { total ^= i; }
        int odd = 0;
        for (int i = 1; i < n; i += 2) { odd ^= encoded[i]; }
        perm.emplace_back(total ^ odd);
        for (int i = 0; i < n; i++) {
            perm.emplace_back(perm.back() ^ encoded[i]);
        }
        return perm;
    }
    ```

??? note "[「Leetcode 810. 黑板异或游戏](https://leetcode-cn.com/problems/chalkboard-xor-game/)"

    ```cpp
    /*
    黑板上写着一个非负整数数组 nums[i] 。Alice 和 Bob 轮流从黑板上擦掉一个数字，Alice 先手。
    如果擦除一个数字后，剩余的所有数字按位异或运算得出的结果等于 0 的话，当前玩家游戏失败。 
    (另外，如果只剩一个数字，按位异或运算得到它本身；如果无数字剩余，按位异或运算结果为 0。）
    换种说法就是，轮到某个玩家时，如果当前黑板上所有数字按位异或运算结果等于 0，这个玩家获胜。
    假设两个玩家每步都使用最优解，当且仅当 Alice 获胜时返回 true。

    示例：
    输入: nums = [1, 1, 2]
    输出: false
    解释: 
    Alice 有两个选择: 擦掉数字 1 或 2。
    如果擦掉 1, 数组变成 [1, 2]。剩余数字按位异或得到 1 XOR 2 = 3。那么 Bob 可以擦掉任意数字，
    因为 Alice 会成为擦掉最后一个数字的人，她总是会输。
    如果 Alice 擦掉 2，那么数组变成[1, 1]。剩余数字按位异或得到 1 XOR 1 = 0。Alice 仍然会输
    掉游戏。
    */

    /*
    分析：
    在某一回合中，当前剩余的个数 n 是偶数的时候，不论 Alice 选取哪一个值，得到的剩余和都不可能为 0，
    并且此时，剩余奇数个数字 n-1，那么 Bob 面对的局面就是：（1）当前异或和为 0 ，或者（2）进入下一回
    合。当进入下一回合时， Alice 又会面临相同的局面，所以，n 为偶数时，Alice 不会输。
    当 n 为奇数时，角色反转，Bob 肯定会赢。
    */
    bool xorGame(vector<int>& nums) {
        int n = (int)nums.size();
        if (n % 2 == 0) { return true; }
        int sum = 0;
        for (int num : nums) {
            sum ^= num;
        }
        return sum == 0;
    }
    ```

### Brian Kernighan 算法
计算一个整数在二进制形式下 1 的个数。

```cpp
int cnt = 0;
while (num) {
    num &= (num - 1);
    cnt++;
}
return cnt;
```

时间复杂度为$O(\log C)$，空间复杂度为 $O(1)$，其中 C 为被计算的整数值。

### 汉明距离
两个整数之间的汉明距离（Hamming Distance）指的是这两个数字对应二进制位不同的位置的数目。

$$
\begin{matrix}
1: & ( & 0 & 0 & 0 & 1 &) \\
4: & ( & 0 & 1 & 0 & 0 & ) \\
& & & \Uparrow & & \Uparrow
\end{matrix}
$$

**「分析」**
按照定义，汉明距离是二进制不同的位置数目和，这与异或运算恰好吻合。我们可以率先计算异或的结果，然后使用 Brian Kernighan 算法求这数值的二进制个数。C++ 中有内置函数 *__builtin_popcount()*，可以调用。

```cpp
int hammingDistance(int x, int y) {
    return __builtin_popcount(x ^ y);
}
```

**「拓展」**

如果给定一个数组，长度小于等于 10000，求数组内任意两个数的汉名距离和。例如 arr = [4, 14, 2], 其汉明距离和是（4，14）+（4，2）+（14，2）= 2 + 2 + 2 = 6

```cpp
int totalHammingDistance(vector<int>& nums) {
    int n = (int)nums.size();
    int ans = 0;
    for (int i = 0; i < 30; i++) {
        int cnt = 0;
        for (int num : nums) {
            cnt += (num >> i) & 1;
        }
        ans += cnt * (n - cnt);
    }
    return ans;
}
```

## 阶乘
### 阶乘尾数0的个数
计算 n 的阶乘有多少个尾0。

**「分析」**

一个数的尾0个数代表能够对 10 取余数的次数。10 = 2 * 5，因此是求 2 和 5 整除的个数，并且被 5 整除的个数是少于被 2 整除的个数。

```cpp
int trailingZeroes(int n) {
    int ans = 0;
    while (n >= 5) {
        n /= 5;
        ans += n;
    }
    return ans;
}
```
时间复杂度：$O(\log n)$，空间复杂度：$O(1)$
