## 匹配
### KMP
KMP讲求从左到右依次匹配，当出现误匹配的字符时，将模式串P向右移动若干距离。KMP借助P的前缀（历史匹配信息）构建next数组。将P前缀中与失配字符前最大重合的位置对齐。P中的第一个字符发生失配时，对应的下一个位置应该是-1，表示将整个P移动到失配字符的下一个位置。

$$
\begin{matrix}
a & c & e & a & c & {\color{Red} e} & a & f & d & b & e \\
a & c & e & a & c & {\color{Blue} f} \\
& & & & & \downarrow \\
& & & a & c & e & a & c & f
\end{matrix}
$$

为了更加高效地移动P，next数组表示地下一个位置应当尽可能地小

$$
\begin{matrix}
a & c & f & a & c & {\color{Red} e} & a & f & d & b & e \\
a & c & f & a & c & {\color{Blue} f} \\
& & & & \downarrow & & & \\
& & & a & c & {\color{Blue} f} & a & c & f \\
& & & & \downarrow & & & \\
& & & & & & a & c & f & a & c & f
\end{matrix}
$$

空间复杂度：$O(m)$, 最坏时间复杂度：$O(n + m)$，m是P的长度，n是T的长度

??? note "[「Leetcode 28. 实现 strStr()」]()"

    ```cpp
    /*
    示例 1：
    输入：haystack = "hello", needle = "ll"
    输出：2

    示例 2：
    输入：haystack = "aaaaa", needle = "bba"
    输出：-1

    示例 3：
    输入：haystack = "", needle = ""
    输出：0
    */
    int strStr(string haystack, string needle) {
        int n = (int)haystack.length(), m = (int)needle.length();
        if (m == 0) { return 0; }
        if (n == 0) { return -1; }
        vector<int> next(m, -1);
        int j = 0, k = -1;
        while (j < m - 1) {
            if (k < 0 || needle[j] == needle[k]) {
                j++;
                k++;
                next[j] = needle[j] != needle[k] ? k : next[k];
            }
            else { k = next[k]; }
        }
        int i = 0;
        j = 0;
        while (i < n && j < m) {
            if (j < 0 || haystack[i] == needle[j]) {
                i++;
                j++;
            }
            else { j = next[j]; }
        }
        return i - j > n - m ? -1 : i - j;
    }
    ```

??? note "[「Leetcode 214. 最短回文串」]()"

    ```cpp
    /*
    给定一个字符串 s，你可以通过在字符串前面添加字符将其转换为回文串。
    找到并返回可以用这种方式转换的最短回文串。

    示例 1：
    输入：s = "aacecaaa"
    输出："aaacecaaa"

    示例 2：
    输入：s = "abcd"
    输出："dcbabcd"
    */
    string shortestPalindrome(string s) {
        int n = (int)s.length();
        if (n <= 1) { return s; }
        vector<int> next(n, -1);
        int i = 0, pos = -1;
        while (i < n - 1) {
            if (pos < 0 || s[i] == s[pos]) {
                i++;
                pos++;
                next[i] = s[i] != s[pos] ? pos : next[pos];
            }
            else { pos = next[pos]; }
        }
        pos = 0;
        i = n - 1;
        while (i >= 0 && pos < n) {
            if (pos < 0 || s[i] == s[pos]) {
                i--;
                pos++;
            }
            else { pos = next[pos]; }
        }
        string ans = s.substr(pos, n - pos);
        reverse(ans.begin(), ans.end());
        return ans + s;
    }
    ```

### Boyer-Moore
#### 坏字符
当模式串P与文本串T在某以位置pos发生不匹配时，T[pos]叫做坏字符。

1.1 坏字符哈希表的构造，一遍扫描将P中的字符位置保存在字典中，key为字符本身，value为字符在P中的位置。

1.2 当坏字符不在模式串中，将模式串全部移动到坏字符后一位

$$
\begin{matrix}
a & c & {\color{Red} e} & a & f & d & b & e \\
a & f & {\color{Blue} d} \\
& & & & \downarrow \\
& & & a & f & d
\end{matrix}
$$

1.3 当坏字符出现在模式串中，将P中最近的字符与T中的坏字符对应

$$
\begin{matrix}
a & c & {\color{Red} e} & a & f & d & b & e \\
a & c & {\color{Blue} d} \\
& & & & \downarrow \\
& a & e & d
\end{matrix}
$$

但是如果坏字符在P中的位置大于不匹配的位置j时，移动的距离是负数

$$
\begin{matrix}
& {\color{Red} a} & c & d & a & f & d & b & e \\
& {\color{Blue} b} & a & d \\
& & & & \downarrow \\
{\color{Blue} b} & a & d
\end{matrix}
$$

为了避免这种情况，BM还需要好后缀规则。

#### 好后缀
因为T与P的匹配是从左到右（移动方向一直是向右），当出现坏字符时，P的后半部分（如果有）一定是匹配好的，这部分叫做好后缀。

* 好后缀在P的前缀中出现，将P的好后缀匹配的前缀部分与T对齐

$$
\begin{matrix}
c & a & c & {\color{Red} d} & {\color{Green} e} & {\color{Green} f} & a & d & e & f \\
a & e & f & {\color{Blue} c} & {\color{Green} e} & {\color{Green} f} \\
& & & & \downarrow \\
& & & a & {\color{Green} e} & {\color{Green} f} & c & e & f
\end{matrix}
$$

* 好后缀的部分（后缀）在P的前缀中出现，将部分后缀（P前缀中的）与T对齐

$$
\begin{matrix}
c & a & {\color{Red} d} & {\color{Green} c} & {\color{Green} e} & {\color{Green} f} & a & d & e & f \\
a & e & {\color{Blue} f} & {\color{Green} c} & {\color{Green} e} & {\color{Green} f} \\
& & & & \downarrow \\
& & & a & {\color{Green} e} & {\color{Green} f} & c & e & f
\end{matrix}
$$

* 好后缀在P的前缀中没有出现，将P整体移动lenP（P的长度）

$$
\begin{matrix}
a & c & {\color{Red} f} & {\color{Green} a} & {\color{Green} f} & d & b & e & c & f\\
& c & {\color{Blue} c} & {\color{Green} a} & {\color{Green} f} \\
& & & & \downarrow \\
& & & & & c & c & a & f
\end{matrix}
$$

* 好后缀的构造

$$
\begin{aligned}
& \text{suffix[ k ] = pos} \\
& \text{prefix[ k ] = T/F}
\end{aligned}
$$

k表示好后缀的长度（从1开始），pos表示好后缀在前缀出现的位置（若不存在为-1）, T/F表示长度为k的后缀在P的前缀中是否出现，例如

$$
\begin{aligned}
& \text{P = cefcef} \\
& \text{suffix[1] = 2,} \quad \text{prefix[1] = False} \\
& \text{suffix[2] = 1,} \quad \text{prefix[1] = False} \\
& \text{suffix[3] = 0,} \quad \text{prefix[1] = True} \\
& \text{suffix[4] = -1,} \quad \text{prefix[1] = False} \\
& \text{suffix[5] = -1,} \quad \text{prefix[1] = False} \\
\end{aligned}
$$

#### 坏字符与好后缀组合
分别计算两种规则下移动的距离，取较大者。空间复杂度：$O(m + \Sigma)$；时间复杂度：最好情况下$O(\frac{n}{m})$，最差情况下$O(n+m)$

??? note "[「BoyerMoore」]()"

    ```cpp
    class BoyerMoore{
        vector<int> bc;
        vector<int> suffix;
        bool *prefix;
        string T, P;
        int nT, nP;
    public:
        const int N = 256;
        BoyerMoore (string text, string pattern) {
            T = text;
            P = pattern;
            nT = (int)T.length();
            nP = (int)P.length();
        }
        
        ~BoyerMoore () {
            delete []prefix;
        }
        
        void buildBadCharacter() {
            bc.resize(N, -1);
            for (int i = 0; i < nP; i++) {
                bc[P[i]] = i;
            }
        }
        
        void buildGoodSuffix() {
            suffix.resize(nP, -1);
            prefix = new bool [nP]{};
            for (int i = 0; i < nP - 1; i++) {
                // k代表后缀的长度
                int j = i, k = 0;
                while (j >= 0 && P[j] == P[nP - 1 - k]) {
                    k++;
                    j--;
                    suffix[k] = j + 1;
                }
                if (j == -1) {
                    prefix[k] = true;
                }
            }
        }
        
        int stepsToMoveWithGoodSuffix(int idxBC) {
            int k = nP - idxBC - 1;
            if (k == 0) { return 0; }
            if (suffix[k] != -1) {
                return idxBC - suffix[k] + 1;
            }
            for (int i = k - 1; i > 0; i--) {
                if (prefix[i]) {
                    return nP - i - suffix[i];
                }
            }
            return nP;
        }
        
        vector<int> match() {
            buildBadCharacter();
            buildGoodSuffix();
            vector<int> ans;
            int k = nP - 1;
            while (k < nT) {
                int i = k, j = nP - 1;
                while (j >= 0 && T[i] == P[j]) {
                    i--;
                    j--;
                }
                if (j == -1) {
                    ans.emplace_back(i+1);
                    k++;
                    continue;
                }
                int stepsWithBC = nP - 1 - bc[T[i]] - (k - i);
                int stepsWithGS = stepsToMoveWithGoodSuffix(j);
                k += max(stepsWithBC, stepsWithGS);
            }
            return ans;
        }
        
        bool isMatch() {
            return !match().empty();
        }
    };
    /*
    string T = "ababab", P = "abab";
    // aaabaaabbbabaa,babb: -1
    // ababbbbaaabbbaaa,bbbb: 3
    BoyerMoore bm(T, P);
    vector<int> pos = bm.match();  // [0 2]
    */
    ```

### Rabin-Karp
利用哈希的方法，将字符串转化成一个数。由此，两字符串之间的比较转化为两数字的比较。如果出现散列冲突，逐一比较两字符串是否相等。

??? note "[「Rabin-Karp」]()"

    ```cpp
    int RabinKarp(string T, string P) {
        int n = (int)T.length(), m = (int)P.length();
        if (m == 0) { return 0; }
        if (n == 0) { return -1; }
        int key = 0, value = 0;
        for (int i = 0; i < m - 1; i++) {
            key += P[i];
            value += T[i];
        }
        key += P[m-1];
        for (int i = m - 1, j = -1; i < n; i++) {
            value += T[i];
            if (j >= 0) { value -= T[j]; }
            j++;
            if (value == key) {
                int x = j, y = 0;
                while (x <= i && y < m && T[x] == P[y]) {
                    x++;
                    y++;
                }
                if (y == m) { return j; }
            }
        }
        return -1;
    }

    int RabinKarp(string T, string P) {
        int n = (int)T.length(), m = (int)P.length();
        if (m == 0) { return 0; }
        if (n == 0) { return -1; }
        for (int i = 0; i <= n-m; i++) {
            if (T.substr(i, m) == P) {
                return i;
            }
        }
        return -1;
    }
    ```