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

## 位运算符
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