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

## 裴蜀定理
两个整数a、b，设 d 是它们的最大公约数，那么 ax + by = m 有整数解（x，y）的充要条件是 m 是 d 的整数倍。当 m = 1时，a 与 b 互质。

推广到一组整数 $\{a_{1}, a_{2}, \cdots, a_{n}\}$，d 是它们的最大公约数，方程 $\sum_{i = 1}^{n} a_{i} x_{i} = m$ 有整数解 $(x_{1}, x_{2}, \cdots, x_{n})$ 的充要条件是 m 是 d 的整数倍。

```cpp
int get_gcd(int x, int y) {
    while (x != 0 && y != 0) {
        if (abs(x) > abs(y)) {
            x = x % y;
        }
        else {
            y = y % x;
        }
    }
    return x + y;
}

bool isGoodArray(vector<int>& nums) {
    if (nums.empty()) {
        return false;
    }
    int gcd = nums[0];
    for (int num : nums) {
        gcd = get_gcd(gcd, num);
    }
    return gcd == 1;
}
```

### 水壶问题
给定两个水壶，容量是 a 和 b，现有不限供应的水，请问能否用这两个水壶量出恰好容量为 c 的水？

```cpp
bool canMeasureWater(int jug1Capacity, int jug2Capacity, int targetCapacity) {
    if (jug1Capacity + jug2Capacity < targetCapacity) {
        return false;
    }
    while (jug1Capacity != 0 && jug2Capacity != 0) {
        if (jug1Capacity > jug2Capacity) {
            jug1Capacity %= jug2Capacity;
        }
        else {
            jug2Capacity %= jug1Capacity;
        }
    }
    return targetCapacity % (jug1Capacity + jug2Capacity) == 0;
}
```
时间复杂度：$O(\log \max(jug1Capacity, jug2Capacity))$，空间复杂度：$O(1)$


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

### 比特位计数
给你一个整数 n ，对于 0 <= i <= n 中的每个 i ，计算其二进制表示中 1 的个数 ，返回一个长度为 n + 1 的数组 ans 作为答案。例如，n = 5，返回 [0,1,1,2,1,2]，表示 0 - 5 这六个数字在二进制表示中分别含有 1 的个数。

**「分析」**

「动态规划」对每一个数字，可以用汉明方法（C++ 中的 __builtin_popcount）在 $O(\log n)$ 的时间复杂度下求的。但考虑递推关系时，可以降低到线形。一个数字 x。右移一位的到 y，那么 x 和 y 所含有的 1 的个数差取决于 x 的最低位是否为 1。

```cpp
vector<int> countBits(int num) {
    vector<int> ans(num+1, 0);
    for (int i = 1; i <= num; i++) {
        ans[i] = ans[i/2] + i % 2;
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$。

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

### 区间按位与
给定一个区间 [left, right] 求这个区间所有值的按位与，包括端点值。

**「分析」**

【Brian Kernighan 算法】对于 [left, right] 区间内的所有值，按照题意结果应为

$$
\text{res = left & left+1 &} \cdots \text{& right-1 & right}
$$

两个数按位与的结果势必不超过两数中较小的那个，因此，我们可以从右端点开始不断求两相邻数的按位与

```cpp
int rangeBitwiseAnd(int left, int right) {
    while (left < right) {
        right &= right - 1;
    }
    return right;
}
```
时间复杂度：$O(\log (right - left))$，空间复杂度：$O(1)$

### 不使用加减运算符求两数之和
给定两个整数a、b，在不使用 +、- 的情况下，求 a + b

**「分析」**

将 a、b转化为二进制数值进行运算

```cpp
int getSum(int a, int b) {
    while (true) {
        unsigned pos = unsigned(a & b);
        if (pos == 0) { break; }
        a ^= b;
        b = pos << 1;
    }
    return a ^ b;
}
```
时间复杂度：$O(\log \min(\left \| a \right \|, \left \| b \right \|))$，空间复杂度：$O(1)$

### 按位与后的异或和
给定两个数组 A = [1, 2, 3] 和 B = [5, 6]，A 和 B 的元素两两按位与，得到的结果形成一个 len(A) * len(B) 的数组，求这个数组元素的异或和。

**「分析」**

【位运算】假设最后的结果有 m 位，在所有的按位与结果中，第 k 位上，必须有奇数个 1，最终答案的第 k 位才会是 1。因为 len(A) * len(B) 是奇数，那么 len(A) 和 len(B) 都是奇数，所有 A 和 B 的异或和的第 k 位必须是 1。

```cpp
int getXORSum(vector<int>& arr1, vector<int>& arr2) {
    int sum1 = 0, sum2 = 0;
    for (int num : arr1) {
        sum1 ^= num;
    }
    for (int num : arr2) {
        sum2 ^= num;
    }
    return sum1 & sum2;
}
```
时间复杂度：$O(n + m)$，空间复杂度：$O(1)$

### 只出现一次的数字
给你一个非空整数数组 nums ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。要求线形时间复杂度和常数空间复杂度。例如，nums = [4,1,2,1,2]，最后返回 4。

**「分析」**

两个相等的数进行「异或」操作会变成0。

```cpp
int singleNumber(vector<int>& nums) {
    int ans = 0;
    for (const int &ele : nums) {
        ans ^= ele;
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$。

### 数组中出现一次的数字 I
给定一个数组 nums = [1, 2, 2, 3] 只有两个数组出现 1 次，其余都出现两次，求这个两个数字

**「分析」**

【异或运算】两个相同的数异或结果是 0。根据整个数组的异或和 res 的最高位来区分数组中的两个单次出现的数字。

```cpp
vector<int> singleNumbers(vector<int>& nums) {
    vector<int> ans;
    int XOR = 0;
    for (int num : nums) {
        XOR ^= num;
    }
    int pos = 0;
    while (XOR >> 1) {
        pos++;
        XOR >>= 1;
    }
    int a = 0, b = 0;
    for (int num : nums) {
        (num >> pos) & 1 ? a ^= num : b ^= num;
    }
    return vector<int> {a, b};
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$

### 数组中出现一次的数字 II
给定一个数组 nums = [4, 3, 3, 3]，只有一个数字出现一次，其余都出现三次，用线性的时间复杂度和常数的空间复杂度，找到这个数字。

**「分析」**

【位运算】每一位上的 1 的数目一定是 3 的倍数加一。

```cpp
int singleNumber(vector<int>& nums) {
    int ans = 0;
    vector<int> bits(32, 0);
    for (int num : nums) {
        for (int i = 0; (1l << i) <= num && i < 32; i++) {
            bits[i] += (num >> i) & 1;
        }
    }
    for (int i = 0; i < 32; i++) {
        ans |= (1 << i) * (bits[i] % 3);
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$


## 取模
一个公式：(A * B) % k = ((A % k) * (B % k)) % k

### 幂指数取模
给定一个整数 a = 2 和 一个数组 b = [1, 0] 表示幂为 10，求 $a^{b}$ 对 1337 取模。

**「分析」**

以给出的 a 和 b 为例

$$
a^{b} = 2^{[1, 0]} = (2^{[1]})^{10} \cdot 2^{0}
$$

```cpp
int mod = 1337;

int pow(int a, int p) {
    int ans = 1;
    while (p > 0) {
        if (p & 1) {
            ans = ((ans % mod) * (a % mod)) % mod;
        }
        p >>= 1;
        a = ((a % mod) * (a % mod)) % mod;
    }
    return ans;
}

int superPow(int a, vector<int>& b) {
    if (b.empty()) {
        return 1;
    }
    int res1 = pow(a, b.back());
    b.pop_back();
    int res2 = pow(superPow(a, b), 10);
    return ((res1 % mod) * (res2 % mod)) % mod;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$，n 是 b 的长度


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


## 数字
### 统计某个数字的出现个数
给定一个非负数 n 和一个 1 - 9 的数字 digit，统计 1 - n 所有数字中 digit 出现的次数。例如 n = 13，digit = 1，包含 1 的数字有：1、10、11、12、13，1 出现了 6 次。

**「分析」**

这是一个数学题目，以 n = 2593，digit = 5 为例

| 位 | 商 | 余数 | digit = '5' 个数 |
| :----: | :----: | :----: | :----: |
| 个位 i = 1 | Quotient = 2593 / (10 * i) = 259 | Mod = 2593 % (10 * i) = 3 |  259 * i + (3 > 5) * i = 259 |
| 个位 i = 10 | Quotient = 2593 / (10 * i) = 25 | Mod = 2593 % (10 * i) = 93 |  25 * i + (9 > 5) * i = 260 |
| 个位 i = 100 | Quotient = 2593 / (10 * i) = 2 | Mod = 2593 % (10 * i) = 593 |  2 * i + (5 == 5) * (93 + 1) = 294 |
| 个位 i = 1000 | Quotient = 2593 / (10 * i) = 0 | Mod = 2593 % (10 * i) = 2593 |  0 * i + (2 > 5) * i = 0 |

总计 '5' 的个数： 259 + 260 + 294 = 813.

```cpp
int countDigitOne(int n, int digit = 1) {
    int ans = 0;
    for (unsigned int i = 1; i <= n; i *= 10) {
        int quo = n / (10ll * i), mod = n % (10ll * i);
        ans += quo * i;
        if (mod / i > digit) {
            ans += i;
        }
        else if (mod / i == digit) {
            ans += mod % i + 1;
        }
    }
    return ans;
}
```
时间复杂度：$O(\log n)$，空间复杂度：$O(1)$


## 绝对值
### 绝对值差的最大和
给定两个数组 arr1 = [1, -2, -5, 0, 10], arr2 = [0, -2, -1, -7, -4]，求下式的最大值

$$
\max_{i, j \in [0, n)} \left | i - j \right | + \left | \text{A[ i ]} - \text{A[ j ]} \right | + \left | \text{B[ i ]} - \text{B[ j ]} \right |
$$

**「分析」**

如果求 |A[ i ] - A[ j ]| 的最大值，我们很容易的知道等价于 max(A) - min(A) 。同样地对于三个绝对值

$$
\begin{aligned}
& \text{A[ i ] - A[ j ] + B[ i ] - B[ j ] + i - j} = \text{(A[ i ] + B[ i ] + i) - (A[ j ] + B[ j ] + j)} \\
& \text{A[ i ] - A[ j ] + B[ i ] - B[ j ] - i + j} = \text{(A[ i ] + B[ i ] - i) - (A[ j ] + B[ j ] - j)} \\
& \text{A[ i ] - A[ j ] - B[ i ] + B[ j ] + i - j} = \text{(A[ i ] - B[ i ] + i) - (A[ j ] - B[ j ] + j)} \\
& \text{A[ i ] - A[ j ] - B[ i ] + B[ j ] - i + j} = \text{(A[ i ] - B[ i ] - i) - (A[ j ] - B[ j ] + j)} \\
& \text{- A[ i ] + A[ j ] + B[ i ] - B[ j ] + i - j} = \text{(- A[ i ] + B[ i ] + i) - (- A[ j ] + B[ j ] + j)} \\
& \text{- A[ i ] + A[ j ] + B[ i ] - B[ j ] - i + j} = \text{(- A[ i ] + B[ i ] - i) - (- A[ j ] + B[ j ] - j)} \\
& \text{- A[ i ] + A[ j ] - B[ i ] + B[ j ] + i - j} = \text{(- A[ i ] - B[ i ] + i) - (- A[ j ] - B[ j ] + j)} \\
& \text{- A[ i ] + A[ j ] - B[ i ] + B[ j ] - i + j} = \text{(- A[ i ] - B[ i ] - i) - (- A[ j ] - B[ j ] + j)} 
\end{aligned}
$$

因此可以将上式子归纳成

$$
\begin{aligned}
& p = \text{A[ i ] + B[ i ] + i} \\
& Q = \text{A[ i ] + B[ i ] - i} \\
& M = \text{A[ i ] - B[ i ] + i} \\
& N = \text{A[ i ] - B[ i ] - i}
\end{aligned}
$$

最终的结果为

$$
\max
\begin{cases}
\max(P) - \min(P) \\
\max(Q) - \min(Q) \\
\max(M) - \min(M) \\
\max(N) - \min(N)
\end{cases}
$$

```cpp
int maxAbsValExpr(vector<int>& arr1, vector<int>& arr2) {
    int n = (int)arr1.size(), ans = 0;
    vector<vector<int>> max_min(4, vector<int> {INT_MIN, INT_MAX});
    for (int i = 0; i < n; i++) {
        max_min[0][0] = max(max_min[0][0], arr1[i] + arr2[i] + i);
        max_min[0][1] = min(max_min[0][1], arr1[i] + arr2[i] + i);
        max_min[1][0] = max(max_min[1][0], arr1[i] + arr2[i] - i);
        max_min[1][1] = min(max_min[1][1], arr1[i] + arr2[i] - i);
        max_min[2][0] = max(max_min[2][0], arr1[i] - arr2[i] + i);
        max_min[2][1] = min(max_min[2][1], arr1[i] - arr2[i] + i);
        max_min[3][0] = max(max_min[3][0], arr1[i] - arr2[i] - i);
        max_min[3][1] = min(max_min[3][1], arr1[i] - arr2[i] - i);
    }
    for (int i = 0; i < 4; i++) {
        ans = max(ans, max_min[i][0] - max_min[i][1]);
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$


## 丑数
如果一个整数，它的质因子只在 2、3、5 之中，那么就是丑数。如 15 = 3 * 5

### 丑数 I
判断是否是丑数

```cpp
bool isUgly(int n) {
    if (n <= 0) { return false; }
    while (n % 2 == 0) { n /= 2; }
    while (n % 3 == 0) { n /= 3; }
    while (n % 5 == 0) { n /= 5; }
    return n == 1;
}
```
时间复杂度：$O(1)$，空间复杂度：$O(1)$

### 丑数 II
求第 n 个丑数 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 是前 10 个丑数

```cpp
int nthUglyNumber(int n) {
    if (n == 0) { return 1; }
    vector<int> dp(n, 0);
    int f2 = 0, f3 = 0, f5 = 0;
    dp[0] = 1;
    for (int i = 1; i < n; i++) {
        dp[i] = min(dp[f2] * 2, min(dp[f3] *3, dp[f5] * 5));
        if (dp[i] == dp[f2] * 2) { f2++; }
        if (dp[i] == dp[f3] * 3) { f3++; }
        if (dp[i] == dp[f5] * 5) { f5++; }
    }
    return dp[n - 1];
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$

### 丑数 III
给定一个质数数组 primes = [2, 7, 13, 19]，求第 n 个超级丑数，满足所有的质因子都在这个 primes 数组中。

```cpp
int nthSuperUglyNumber(int n, vector<int>& primes) {
    int m = (int)primes.size();
    vector<int> dp(n, 0), f(m, 0);
    dp[0] = 1;
    for (int i = 1; i < n; i++) {
        int minVal = INT_MAX;
        for (int j = 0; j < m; j++) {
            minVal = min(minVal, dp[f[j]] * primes[j]);
        }
        dp[i] = minVal;
        for (int j = 0; j < m; j++) {
            if (dp[f[j]] * primes[j] == dp[i]) { f[j]++; }
        }
    }
    return dp[n - 1];
}
```
时间复杂度：$O(nm)$，空间复杂度：$O(m)$，m 是 primes 的长度

### 丑数 IV
给定四个整数，n = 1000000000, a = 2, b = 217983653, c = 336916467，求第 n 个数，这个数可以整除 a、b、c其中的一个或多个。

**「分析」**

[1, X] 之间，有多少个数可以被 a e.g. 整除？答案是：$\left \lfloor \frac{X}{a} \right \rfloor$，那么对于 a、b、c 三个除数而言，[1, X] 区间中符合条件的个数是

$$
\begin{aligned}
\text{ans} =& \left \lfloor \frac{X}{a} \right \rfloor + \left \lfloor \frac{X}{b} \right \rfloor + \left \lfloor \frac{X}{c} \right \rfloor \\
&- \left \lfloor \frac{X}{\text{LCM}(a, b)} \right \rfloor - \left \lfloor \frac{X}{\text{LCM}(a, c)} \right \rfloor \\
&- \left \lfloor \frac{X}{\text{LCM}(b, c)} \right \rfloor + \left \lfloor \frac{X}{\text{LCM}(a, b, c)} \right \rfloor
\end{aligned}
$$

```cpp
long lcm(int a, int b) {
    long mul = (long)a * b;
    while (a != 0 && b != 0) {
        if (a > b) { a %= b; }
        else { b %= a; }
    }
    return mul / (a + b);
}

int nthUglyNumber(int n, int a, int b, int c) {
    long ab = lcm(a, b), bc = lcm(b, c), ac = lcm(a, c);
    long abc = lcm(ab, c), l = 0, r = INT_MAX;
    while (l < r) {
        long m = (l + r) >> 1;
        long res = m / a + m / b + m / c - m / ab -
                   m / ac - m / bc + m / abc;
        if (res < n) {
            l = m + 1;
        }
        else {
            r = m;
        }
    }
    return (int)l;
}
```
时间复杂度：$O(\log INT_MAX)$，空间复杂度：$O(1)$


## 摩尔投票法
摩尔投票法是一种时间复杂度$O(N)$，空间复杂度$O(1)$的算法，用来找到一个数组的多数元素。这里的多数，可以是超过整个数组长度的一半，也可以是超过整个数组的1/3。

算法的思想是用一个元素与另一个元素抵消。

### 找到超过一半的元素

```cpp
int majorityElement(vector<int>& nums) {
    int n = int(nums.size());
    int count = 0, maj = -1;
    for (int num : nums)
    {
        if (count == 0)
        {
            maj = num;
            count = 1;
            continue;
        }
        if (num == maj) { count++; }
        else { count--; }
    }
    return maj;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$

### 找到超过三分之一的元素（们）

```cpp
vector<int> majorityElement(vector<int>& nums) {
    vector<int> ans;
    if (nums.empty()) { return ans; }
    int cddt1 = nums[0], cddt2 = nums[0], cnt1 = 0, cnt2 = 0;
    for (int num : nums) {
        if (cddt1 == num) {
            cnt1++;
            continue;
        }
        if (cddt2 == num) {
            cnt2++;
            continue;
        }
        if (cnt1 == 0) {
            cnt1++;
            cddt1 = num;
            continue;
        }
        if (cnt2 == 0) {
            cnt2++;
            cddt2 = num;
            continue;
        }
        cnt1--;
        cnt2--;
    }

    cnt1 = 0;
    cnt2 = 0;
    for (int num : nums) {
        if (cddt1 == num) {
            cnt1++;
        }
        else if (cddt2 == num) {
            cnt2++;
        }
    }
    if (cnt1 > nums.size() / 3) {
        ans.emplace_back(cddt1);
    }
    if (cnt2 > nums.size() / 3) {
        ans.emplace_back(cddt2);
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$


## 编码
### 格雷编码
格雷编码是一个二进制数字系统，在该系统中，两个连续的数值仅有一个位数的差异。给定一个代表编码总位数的非负整数 n，打印其格雷编码序列。即使有多个不同答案，你也只需要返回其中一种。格雷编码序列必须以 0 开头。例如 n = 2，会得到 4 个编码

$$
\begin{matrix}
00 & \rightarrow & 0 \\
01 & \rightarrow & 1 \\
11 & \rightarrow & 3 \\
10 & \rightarrow & 2
\end{matrix}
$$

**「分析」**

格雷编码的公式

$$
\text{GrayCode}(i) = i \text{ XOR } \left \lfloor \frac{i}{2} \right \rfloor
$$

```cpp
vector<int> grayCode(int n) {
    vector<int> ans(1 << n, 0);
    for (int i = 1; i < (1 << n); i++) {
        ans[i] = i ^ (i >> 1);
    }
    return ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(1)$


## 信息论
### 可怜的小猪
有 buckets 桶液体，其中 正好 有一桶含有毒药，其余装的都是水。它们从外观看起来都一样。为了弄清楚哪只水桶含有毒药，你可以喂一些猪喝，通过观察猪是否会死进行判断。不幸的是，你只有 minutesToTest 分钟时间来确定哪桶液体是有毒的。

喂猪的规则如下：

选择若干活猪进行喂养

可以允许小猪同时饮用任意数量的桶中的水，并且该过程不需要时间。

小猪喝完水后，必须有 minutesToDie 分钟的冷却时间。在这段时间里，你只能观察，而不允许继续喂猪。

过了 minutesToDie 分钟后，所有喝到毒药的猪都会死去，其他所有猪都会活下来。

重复这一过程，直到时间用完。

给你桶的数目 buckets ，minutesToDie 和 minutesToTest ，返回在规定时间内判断哪个桶有毒所需的 最小 猪数。

**「分析」**

设 m 个小猪，实验轮数 n = $\left \lfloor \frac{\text{minutesToTest}}{\text{minutesToDie}} \right \rfloor$，那么对一个小猪而言，从实验开始到实验结束一共有 (1 + n) 个状态。这里的 1 指的是初始状态，之后每一轮实验都会有一个状态。因此 buckets 个桶确定有一个是毒药，所以，毒药的状态数是 buckets。因此，

$$
(n + 1)^{m} \geq \text{buckets}
$$

可以解出来 m 的最小值

$$
m = \left \lceil \log_{n + 1}\text{buckets} \right \rceil
$$

```cpp
int poorPigs(int buckets, int minutesToDie, int minutesToTest) {
    return ceil(log(buckets) / log(minutesToTest / minutesToDie + 1));
}
```
时间复杂度：$O(1)$，空间复杂度：$O(1)$
