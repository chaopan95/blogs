## 基础
### 几何解释
考虑一组两元一次方程组

$$
\begin{cases}
\mathrm{2x-y=0} \\
\mathrm{-x+2y=3}
\end{cases}
$$

如果将每一行看成一条直线，这个方程组可以被画出来

![](./images/la1.png)

我们引入矩阵的表达方式

$$
\begin{matrix}
\begin{bmatrix}
2 & -1 \\
-1 & 2
\end{bmatrix} &
\begin{bmatrix}
x \\
y
\end{bmatrix} &
= &
\begin{bmatrix}
0 \\
3
\end{bmatrix} \\
\mathbf{A} & \mathbf{X} & = & \mathbf{b}
\end{matrix}
$$

我们还可以将上述的一元二次方程组的求解过程看作是向量的线性组合

$$
\mathrm{x}
\begin{bmatrix}
2 \\
-1
\end{bmatrix}
+
\mathrm{y}
\begin{bmatrix}
-1 \\
2
\end{bmatrix}
=
\begin{bmatrix}
0 \\
3
\end{bmatrix}
$$

![](./images/la2.png)

线性代数的几何解释是，列向量的线性组合（linear combination of columns）。

对于上述的方程组，我们可以求的结果

$$
\begin{cases}
\mathrm{x = 1} \\
\mathrm{y = 2}
\end{cases}
$$

这里如果 x 和 y 可以取任意值，那么通过上面的组合，我们可以得到二维平面的任意向量，也就是说 Ax = b，无论 b 是何值，x 都有解。我们得到一个重要的发现，Ax 表示的是 A 在对应空间上的所有列向量的线性组合。

我们可以把矩阵乘法理解成列向量的线性组合，例如

$$
\begin{bmatrix}
2 & 5 \\
1 & 3
\end{bmatrix}
\begin{bmatrix}
1 \\
2
\end{bmatrix}
=
1
\begin{bmatrix}
2 \\
1
\end{bmatrix}
+
2
\begin{bmatrix}
5 \\
3
\end{bmatrix}
=
\begin{bmatrix}
12 \\
7
\end{bmatrix}
$$

### 消元法
考虑一个三元一次方程组

$$
\begin{cases}
\mathrm{x + 2y + z = 2} \\
\mathrm{3x + 8y + z = 12} \\
\mathrm{4y + z = 1}
\end{cases}
$$

在求解多元一次方程组的时候，我们使用的正是消元法，它的思想是用方程关系，不断减少未知数的个数。将上述的方程组，转化成 Ax = b 的形式后，A 的矩阵表达

$$
\mathrm{A} = 
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
3 & \boxed{\color{red} 8} & 1 \\
0 & 4 & \boxed{\color{red} 1}
\end{bmatrix}
$$

方框圈中的叫做主元（pivot）。消元的过程如下，用第一行消除剩下行的第一个元

$$
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
3 & \boxed{\color{red} 8} & 1 \\
0 & 4 & \boxed{\color{red} 1}
\end{bmatrix} \Leftrightarrow 
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
0 & \boxed{\color{red} 2} & -2 \\
0 & 4 & \boxed{\color{red} 1}
\end{bmatrix}
$$

用第二行消除剩下行的第二个元

$$
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
0 & \boxed{\color{red} 2} & -2 \\
0 & 4 & \boxed{\color{red} 1}
\end{bmatrix} \Leftrightarrow 
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
0 & \boxed{\color{red} 2} & -2 \\
0 & 0 & \boxed{\color{red} 5}
\end{bmatrix} = \mathrm{U}
$$

我们得到了上三角矩阵（upper-triangle）通常用 U 表示。继续用最后一行消除其他行

$$
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
0 & \boxed{\color{red} 2} & -2 \\
0 & 0 & \boxed{\color{red} 5}
\end{bmatrix} \Leftrightarrow
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 0 \\
0 & \boxed{\color{red} 2} & 0 \\
0 & 0 & \boxed{\color{red} 5}
\end{bmatrix} \Leftrightarrow
\begin{bmatrix}
\boxed{\color{red} 1} & 0 & 0 \\
0 & \boxed{\color{red} 2} & 0 \\
0 & 0 & \boxed{\color{red} 5}
\end{bmatrix}
$$

消元的过程实际上是将 A 转化成 U。我们在找主元时，需要找到某个非零值作为主元，这个过程可以交换矩阵的两行。得到 U 之后，需要带回到原来的方程组，因为我们对 A 进行了线性变换，需要对 b 也要做同样的变换，因此引入 增广矩阵（augment matrix），即在矩阵 A 的右侧并入 b。

$$
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 & 2 \\
3 & \boxed{\color{red} 8} & 1 & 12 \\
0 & 4 & \boxed{\color{red} 1} & 2
\end{bmatrix}
$$

消元所做的一系列线性变换，同样作用到增广矩阵上。

$$
\begin{matrix}
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 & 2 \\
3 & \boxed{\color{red} 8} & 1 & 12 \\
0 & 4 & \boxed{\color{red} 1} & 2
\end{bmatrix} \\
\Updownarrow \\
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 & 2\\
0 & \boxed{\color{red} 2} & -2 & 6 \\
0 & 4 & \boxed{\color{red} 1} & 2
\end{bmatrix} \\
\Updownarrow \\
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 & 2 \\
0 & \boxed{\color{red} 2} & -2 & 6 \\
0 & 0 & \boxed{\color{red} 5} & -10
\end{bmatrix} \\
\Updownarrow \\
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 0 & 4 \\
0 & \boxed{\color{red} 2} & 0 & 2 \\
0 & 0 & \boxed{\color{red} 5} & -10
\end{bmatrix} \\
\Updownarrow \\
\begin{bmatrix}
\boxed{\color{red} 1} & 0 & 0 & 2 \\
0 & \boxed{\color{red} 2} & 0 & 2 \\
0 & 0 & \boxed{\color{red} 5} & -10
\end{bmatrix}
\end{matrix}
$$

最终的解

$$
\begin{cases}
\mathrm{x = 2} \\
\mathrm{y = 1} \\
\mathrm{z = -2}
\end{cases}
$$

算法复杂度 $O(n^{3})$

$$
\mathrm{n^{2} + (n - 1)^{2} + \cdots + 1^{2} = \frac{n(n+1)(n+2)}{6}}
$$

考虑两个矩阵相乘

$$
\mathrm{AB} = 
\begin{bmatrix}
\mathrm{a}_{11} & \mathrm{a}_{12} \\
\mathrm{a}_{21} & \mathrm{a}_{22}
\end{bmatrix}
\begin{bmatrix}
\mathrm{b}_{11} & \mathrm{b}_{12} \\
\mathrm{b}_{21} & \mathrm{b}_{22}
\end{bmatrix} =
\begin{bmatrix}
\mathrm{a_{11}b_{11} + a_{12}b_{21}} & \mathrm{a_{11}b_{12} + a_{12}b_{22}} \\
\mathrm{a_{21}b_{11} + a_{22}b_{21}} & \mathrm{a_{21}b_{12} + a_{22}b_{22}}
\end{bmatrix} = \mathrm{C}
$$

如果我们以 A 为基准点，右乘 B 得倒 C。C 有两列，每一列可以看成 B 对应的列右乘到 A 上 

$$
\begin{aligned}
\mathrm{AB} &=
\begin{bmatrix}
\mathrm{a}_{11} & \mathrm{a}_{12} \\
\mathrm{a}_{21} & \mathrm{a}_{22}
\end{bmatrix}
\begin{bmatrix}
\mathrm{b}_{11} & \mathrm{b}_{12} \\
\mathrm{b}_{21} & \mathrm{b}_{22}
\end{bmatrix} \\
&=
\begin{bmatrix}
\mathrm{a}_{11} & \mathrm{a}_{12} \\
\mathrm{a}_{21} & \mathrm{a}_{22}
\end{bmatrix}
\begin{bmatrix}
\mathrm{B}_{1} & \mathrm{B}_{2} \\
\end{bmatrix}
\end{aligned}
$$

同样地，以 B 为基准点，C 相当于 B 左乘 A，即 A 的每一行左乘到 B 上。

$$
\begin{aligned}
\mathrm{AB} &=
\begin{bmatrix}
a_{11} & a_{12} \\
a_{21} & a_{22}
\end{bmatrix}
\begin{bmatrix}
b_{11} & b_{12} \\
b_{21} & b_{22}
\end{bmatrix} \\
&=
\begin{bmatrix}
A_{1} \\
A_{2}
\end{bmatrix}
\begin{bmatrix}
b_{11} & b_{12} \\
b_{21} & b_{22}
\end{bmatrix}
\end{aligned}
$$

根据上面的发现，一个矩阵 A 通过消元得倒 U，本质上是一个行向量的线性变行，因此也就是一个不断左乘矩阵的过程。

$$
\begin{matrix}
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
3 & \boxed{\color{red} 8} & 1 \\
0 & 4 & \boxed{\color{red} 1}
\end{bmatrix} \Leftrightarrow 
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
0 & \boxed{\color{red} 2} & -2 \\
0 & 4 & \boxed{\color{red} 1}
\end{bmatrix} \\
\Downarrow \\
\begin{bmatrix}
1 & 0 & 0 \\
-3 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
3 & \boxed{\color{red} 8} & 1 \\
0 & 4 & \boxed{\color{red} 1}
\end{bmatrix} =
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 1 \\
0 & \boxed{\color{red} 2} & -2 \\
0 & 4 & \boxed{\color{red} 1}
\end{bmatrix}
\end{matrix}
$$

这里我们把左乘的矩阵成为初等变换矩阵（elementary matrix）或者消元矩阵（elimination matrix），用 E 表示。仔细观察会发现，A 的第一行和第三行没有变换，对应的 E 的第一行和第三行同单位矩阵（identity matrix），第二行减去第一行的三倍，所以 E 的第二行为 (-3, 1, 0)。总结来说，一个行向量 a（$1 \times n$）左乘到一个矩阵 B（$n \times m$），得到一个行向量 c（$1 \times m$），这个行向量 c 的值就是 B 的行向量线性组合的结果，线性组合的系数来自于 a。

如果涉及到行交换，我们需要用到置换矩阵（permutation matrix），用 P 表示。例如，交换两行需要将 P 左乘

$$
\begin{bmatrix}
0 & 1 \\
1 & 0
\end{bmatrix}
\begin{bmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c} & \mathrm{d}
\end{bmatrix} =
\begin{bmatrix}
\mathrm{c} & \mathrm{d} \\
\mathrm{a} & \mathrm{b}
\end{bmatrix}
$$

交换两列需要将 P 右乘到矩阵上

$$
\begin{bmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c} & \mathrm{d}
\end{bmatrix}
\begin{bmatrix}
0 & 1 \\
1 & 0
\end{bmatrix} =
\begin{bmatrix}
\mathrm{b} & \mathrm{a} \\
\mathrm{d} & \mathrm{c}
\end{bmatrix}
$$

我们可以发现，置换矩阵其实是单位矩阵的一种变体，如果我们想交换矩阵 A 的某两列（行），只要需要将单位矩阵对应两列（行）交换得到 P，然后右（左）乘到 A 上。对于一个 n 阶单位矩阵，总共有 n! 个置换矩阵，这里包含单位矩阵。例如，n = 3 时

$$
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 0 & 0 \\
0 & 0 & 1 \\
0 & 1 & 0
\end{bmatrix}
\begin{bmatrix}
0 & 1 & 0 \\
1 & 0 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
0 & 1 & 0 \\
0 & 0 & 1 \\
1 & 0 & 0
\end{bmatrix}
\begin{bmatrix}
0 & 0 & 1 \\
0 & 1 & 0 \\
1 & 0 & 0
\end{bmatrix}
\begin{bmatrix}
0 & 0 & 1 \\
1 & 0 & 0 \\
0 & 1 & 0
\end{bmatrix}
$$

置换矩阵有一个性质

$$
\begin{aligned}
& \mathbf{P^{-1} = P^{T}} \\
& \mathbf{P^{T} P = I}
\end{aligned}
$$

### 乘法
考虑两个矩阵 $A_{n \times m}$ 和 $B_{m \times p}$，两者的尺寸表现在其下标上，乘积后的矩阵为 $C_{n \times p}$，C 中的某个位置的元素结果遵循着如下规则

$$
\mathrm{C_{ij} = \sum_{k = 1}^{m} A_{ik}B_{kj}}
$$

A 的列数和 B 的行数要保持一致。

如果我们将矩阵 B 还成一组列向量的组合，那么每一列和 A 想乘，便会得到 C 的每一列，这是矩阵乘法的另一种视角：**C 中的每一列都是 A 的列向量的线性组合，线性组合的系数来自于 B 对应的列。**

$$
\mathrm{A_{n \times m}}
\begin{bmatrix}
\mid & \mid & \cdots & \mid \\
\mathrm{B}_{1} & \mathrm{B}_{2} & \cdots & \mathrm{B_{p}}  \\
\mid & \mid & \cdots & \mid
\end{bmatrix}_{\mathrm{m \times p}}
=
\begin{bmatrix}
\mid & \mid & \cdots & \mid \\
\mathrm{C_{1}} & \mathrm{C_{2}} & \cdots & \mathrm{C_{p}}  \\
\mid & \mid & \cdots & \mid
\end{bmatrix}_{\mathrm{n \times p}}
$$

同样地，如果我们将 A 看成一组行向量，那么 A 的每一行左乘到 B 上，就会得到 C 的对应行。这是行向量的视角：**C 中的每一行都是 B 的行向量的线性组合，线性组合的系数来自 B 对应的行**。

$$
\begin{bmatrix}
- & \mathrm{A_{1}} & - \\
- & \mathrm{A_{2}} & - \\
\vdots & \vdots & \vdots \\
- & \mathrm{A_{n}} & -
\end{bmatrix}_{\mathrm{n \times m}}
\mathrm{B_{m \times p}}
=
\begin{bmatrix}
- & \mathrm{C_{1}} & - \\
- & \mathrm{C_{2}} & - \\
\vdots & \vdots & \vdots \\
- & \mathrm{C_{n}} & -
\end{bmatrix}_{\mathrm{n \times p}}
$$

结合列向量和行向量这两种视角，我们可以总结出矩阵乘积的核心是，矩阵 A 的列向量与矩阵 B 的行向量两两相乘，然后全部加起来。

$$
\mathrm{C = AB = \sum (\text{col of A})(\text{row of B})}
$$

例如

$$
\begin{aligned}
\mathrm{C} &= \mathrm{AB} \\
&=
\begin{bmatrix}
2 & 7 \\
3 & 8 \\
4 & 9
\end{bmatrix}
\begin{bmatrix}
1 & 6 \\
0 & 0
\end{bmatrix} \\
&=
\begin{bmatrix}
2 \\
3 \\
4
\end{bmatrix}
\begin{bmatrix}
1 & 6
\end{bmatrix} +
\begin{bmatrix}
2 \\
3 \\
4
\end{bmatrix}
\begin{bmatrix}
0 & 0
\end{bmatrix} +
\begin{bmatrix}
7 \\
8 \\
9
\end{bmatrix}
\begin{bmatrix}
1 & 6
\end{bmatrix} +
\begin{bmatrix}
7 \\
8 \\
9
\end{bmatrix}
\begin{bmatrix}
0 & 0
\end{bmatrix} \\
&=
\begin{bmatrix}
2 + 0 + 7 + 0 & 12 + 0 + 42 + 0 \\
3 + 0 + 8 + 0 & 18 + 0 + 48 + 0 \\
4 + 0 + 9 + 0 & 24 + 0 + 54 + 0
\end{bmatrix} \\
&=
\begin{bmatrix}
9 & 54 \\
11 & 66 \\
13 & 78
\end{bmatrix}
\end{aligned}
$$

有些时候，较大矩阵间乘积计算较为复杂，我们可以对其进行「分块」（block）处理。

$$
\left[\begin{array} {c:c}
\mathrm{A_{1}} & \mathrm{A_{2}} \\ \hline 
\mathrm{A_{3}} & \mathrm{A_{4}}
\end{array} \right]
\left[\begin{array} {c:c}
\mathrm{B_{1}} & \mathrm{B_{2}} \\ \hline 
\mathrm{B_{3}} & \mathrm{B_{4}}
\end{array} \right] =
\left[\begin{array} {c:c}
\mathrm{A_{1}B_{1}} + \mathrm{A_{2}B_{3}} & \mathrm{A_{1}B_{2}} + \mathrm{A_{2}B_{4}} \\ \hline 
\mathrm{A_{3}B_{1} + A_{4}B_{3}} & \mathrm{A_{3}B_{2} + A_{4}B_{4}}
\end{array} \right]
$$

### 逆
假设我们有一个矩阵 A，我们想找到一个矩阵 B 使得 B A = I。B 就是 A 的逆矩阵（inverse），也写作 $A^{-1}$，例如

$$
\mathrm{A} =
\begin{bmatrix}
1 & 0 & 0 \\
-3 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
$$

逆矩阵左乘

$$
\begin{bmatrix}
1 & 0 & 0 \\
3 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 0 & 0 \\
-3 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix} =
\begin{bmatrix}
1 & 0 & 0 \\
1 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
$$

如果 A 是一个方阵（square matrix），即列数和行数是相等的。那么 A 的逆矩阵 $A^{-1}$，左逆和右逆时等价的。

$$
\mathbf{A^{-1} A = I = A A^{-1}}
$$

如果有逆矩阵，我们称 A 时可逆的（invertible）或者是非奇异的（non-singular）。很自然地，我们会问什么时候，矩阵 A 是不可逆的？答案就是，如果我们能找打一个非零解 x，使得 Ax = 0（也就是 A 的线性组合等于 0），那么 A 就是不可逆（奇异）的。这里用反证法，假设 A 是可逆的，那么我们对 Ax 左乘逆矩阵 $A^{-1}$。

$$
\begin{matrix}
\mathbf{Ax = 0} \\
\mathbf{A^{-1} Ax = A^{-1} 0 = 0} \\
\mathbf{x = 0} \\
\end{matrix}
$$

与 x 非零解是矛盾的。

现在我们关心如何求一个矩阵的逆矩阵，例如

$$
\mathrm{A} =
\begin{bmatrix}
1 & 3 \\
2 & 7
\end{bmatrix}
$$

根据逆矩阵的定义

$$
\begin{bmatrix}
1 & 3 \\
2 & 7
\end{bmatrix}
\begin{bmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c} & \mathrm{d}
\end{bmatrix} =
\begin{bmatrix}
1 & 0 \\
0 & 1
\end{bmatrix}
$$

只要能求出 a b c d 的值，A 的逆矩阵就可以知道。根据前面矩阵乘法的列向量视角，我们把逆矩阵拆分成多个列向量，也就是把矩阵乘法问题转化成多个 Ax = b 问题。

$$
\begin{aligned}
\begin{bmatrix}
1 & 3 \\
2 & 7
\end{bmatrix}
\begin{bmatrix}
a \\
c
\end{bmatrix} =
\begin{bmatrix}
1 \\
0
\end{bmatrix} \\
\begin{bmatrix}
1 & 3 \\
2 & 7
\end{bmatrix}
\begin{bmatrix}
b \\
d
\end{bmatrix} =
\begin{bmatrix}
0 \\
1
\end{bmatrix}
\end{aligned}
$$

这样就可以使用消元法。有没有一种可以同事处理多个方程组求解的方法？答案就是「高斯-若尔」（Gauss-Jordan）方法，将矩阵 A 和单位矩阵拼接成一起。

$$
\mathrm{[A, I]} =
\left[\begin{array} {cc:cc}
1 & 3 & 1 & 0\\ 
2 & 7 & 0 & 1
\end{array} \right]
$$

对新矩阵进行消元

$$
\begin{matrix}
\left[\begin{array} {cc:cc}
1 & 3 & 1 & 0\\ 
2 & 7 & 0 & 1
\end{array} \right] \\
\Downarrow \\
\left[\begin{array} {cc:cc}
1 & 3 & 1 & 0\\ 
0 & 1 & -2 & 1
\end{array} \right] \\
\Downarrow \\
\left[\begin{array} {cc:cc}
1 & 0 & 7 & -3 \\ 
0 & 1 & -2 & 1
\end{array} \right]
\end{matrix}
$$

最终我们得到一个单位矩阵和一个矩阵的拼接，而这个矩阵就是 A 的逆矩阵。

$$
\left[\begin{array} {cc:cc}
1 & 0 & 7 & -3 \\ 
0 & 1 & -2 & 1
\end{array} \right] = [I, \mathrm{A^{-1}}]
$$

消元的本质是行向量的线性组合，实现的方式是左乘一个初等矩阵 E，如下

$$
\mathbf{E [A, I] = [EA, E] = [I, A^{-1}]}
$$

可知，EA = I，那么 E 就是 A 的逆矩阵。

### 转置
矩阵的转置指的是将 A 的列向量用行向量的方式排列

$$
\mathrm{A^{T}_{ji} = A_{ij}}
$$

例如

$$
\begin{aligned}
& \mathrm{A} =
\begin{bmatrix}
1 & 3 \\
2 & 3 \\
4 & 1
\end{bmatrix} \\
& \mathrm{A^{T}} = 
\begin{bmatrix}
1 & 2 & 4 \\
3 & 3 & 1
\end{bmatrix}
\end{aligned}
$$

如果矩阵的转置和自身相等，这样的矩阵成为「对称矩阵」（symmetric matrix）。

$$
\mathrm{A = A^{T}} =
\begin{bmatrix}
3 & 1 & 7 \\
1 & 2 & 9 \\
7 & 9 & 4
\end{bmatrix}
$$

显然，对称矩阵一定是方阵。但是非对称矩阵也可以变成对称矩阵，方法是左乘它的转置。

$$
\mathbf{(A^{T}A)^{T} = A^{T}(A^{T})^{T} = A^{T}A}
$$

### 左右逆
考虑一个矩阵 $\mathrm{A_{m \times n}}$，（1）如果列满秩且行满秩（r = m = n），那么有正常的逆

$$
\mathrm{A^{-1} A = I = A A^{-1}}
$$

（2）如果只有列满秩（r = n < m）

$$
\mathrm{(A^{T} A)^{-1}(A^{T} A) = I}
$$

A 的左逆

$$
\mathrm{A_{left}^{-1} = (A^{T}A)^{-1}A^{T}}
$$

（3）如果只有行满秩（r = m < n）

$$
\mathrm{(AA^{T})(AA^{T})^{-1} = I}
$$

A 的右逆

$$
\mathrm{A_{right}^{-1} = A^{T}(AA^{T})^{-1}}
$$

（4）如果行与列均不满秩（r < m, r < n），那么零空间和左零空间将会包含除了零向量的其他向量。假设行空间的向量为 x，通过 A 可以得到列空间的一个向量 Ax，如果这个向量通过某个变换 B 再回到 x，那么 B 就是 A 的伪逆（pseudo-inverse）

$$
\mathrm{x = B Ax = A^{+} Ax}
$$

通过 SVD 求解 A 的伪逆

$$
\begin{matrix}
\mathrm{A = U \Sigma V^{T}} \\
\Downarrow \\
\mathrm{A_{+} = V \Sigma^{+} U^{T}}
\end{matrix}
$$

$$
\begin{matrix}
\Sigma =
\begin{bmatrix}
\sigma_{1} & & & \\
& \sigma_{2} & & \\
& & 0 & \\
& & & \ddots
\end{bmatrix}_{\mathrm{m \times n}} \\
\Downarrow \\
\Sigma^{+} =
\begin{bmatrix}
\frac{1}{\sigma_{1}} & & & \\
& \frac{1}{\sigma_{2}} & & \\
& & 0 & \\
& & & \ddots
\end{bmatrix}_{\mathrm{n \times m}}
\end{matrix}
$$


## 向量空间
向量空间（vector space）可以看成是向量的一个集合，在这个集合里，我们可以进行线性运算（加乘 $\alpha v + \beta w$）。一个空间必须包含**原点**才能构成封闭空间。例如一个平面空间 $R^{2}$ 涵盖了所有 2 维的向量（只有两个实数分量）。第一象限并不能构成向量空间，因为它不是封闭的线性运算。

### 子空间
子空间（subspace）是原空间的自己。例如，实数平面 $R^{2}$ 是一个原空间，那么它的子空间有三个

1）实数平面本身（plane）

2）任何一条过远点的直线（line）

3）原点本身（point）

假设有两个子空间 S 和 T，那么两者的交集 $S \cap T$ 也是一个子空间。

### 列空间
下面考虑矩阵如何构成子空间，有一个矩阵如下

$$
\mathrm{A} =
\begin{bmatrix}
1 & 1 & 2 \\
2 & 1 & 3 \\
3 & 1 & 4 \\
4 & 1 & 5
\end{bmatrix}
$$

A 的列向量属于 $R^{4}$，因为有 4 个实数分量。列向量的所有线性组合构成了一个子空间，我们称之为**列空间**（column space），用 C(A) 表示。从上面的例子可知，第三列向量是前两列的线性组合，那么它们所有的线性组合将会是一个平面。

这时我们会问一个问题，Ax = b 是否有解？

$$
\begin{matrix}
\begin{bmatrix}
1 & 1 & 2 \\
2 & 1 & 3 \\
3 & 1 & 4 \\
4 & 1 & 5
\end{bmatrix} &
\begin{bmatrix}
x_{1} \\
x_{2} \\
x_{3}
\end{bmatrix} & = &
\begin{bmatrix}
b_{1} \\
b_{2} \\
b_{3} \\
b_{4}
\end{bmatrix} \\
\mathrm{A} & \mathrm{x} & = & \mathrm{b}
\end{matrix}
$$

这里有三个未知数，但是方程数目是 4，通常情况是无解的。但是根据前面总结，Ax 表示的 A 的列向量的线性组合，如果 b 是这个线性组合中的一员，也就是说 **b 属于 A 的列空间 C(A)**，那么方程组有解；反之，无解。

因为 A 的三个列向量是线性相关的，因此有一列是没有贡献的，我们可以去除任意一列。通常是最后一列，保留前面的线性无关的列，也称作主列。

### 零空间
零空间（null space）表示 Ax = 0 的所有解集合 x，记作 N(A)。从定义可以看出，零空间针对 x，而列空间针对 b。例如

$$
\begin{matrix}
\begin{bmatrix}
1 & 1 & 2 \\
2 & 1 & 3 \\
3 & 1 & 4 \\
4 & 1 & 5
\end{bmatrix} &
\begin{bmatrix}
x_{1} \\
x_{2} \\
x_{3}
\end{bmatrix} & = &
\begin{bmatrix}
0 \\
0 \\
0 \\
0
\end{bmatrix} \\
\mathrm{A} & \mathrm{x} & = & \mathrm{b}
\end{matrix}
$$

这里零空间 $x \in R^{3}$，因为有三个未知数。我们可以解这个方程组

$$
\mathrm{x = c}
\begin{bmatrix}
1 \\
1 \\
-1
\end{bmatrix}
$$

这里 c 可以取任意的实数，自然包含了全 0 解。可以看出，零空间实际上是一条直线。

下面我们来求解零空间，考虑这样的一个矩阵 A

$$
\begin{bmatrix}
1 & 2 & 2 & 2 \\
2 & 4 & 6 & 8 \\
3 & 6 & 8 & 10
\end{bmatrix}
$$

通过消元，转化成上三角矩阵 U 或者阶梯形式（echelon form）

$$
\mathrm{U} =
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 2 & 2 \\
0 & 0 & \boxed{\color{red} 2} & 4 \\
0 & 0 & 0 & 0
\end{bmatrix}
$$

这个例子里，只有两个主元，主元的个数叫做矩阵的秩（rank）。主元所在的列叫主元列（pivot columns），其他的列叫做自由列（free columns）。这里「自由」指的是我们可以给对应的未知数（$x_{2}, x_{4}$）任意值。根据消元后系数，方程组如下

$$
\begin{cases}
\mathrm{x_{1} + 2x_{2} + 2x_{3} + 2x_{4} = 0} \\
\mathrm{2x_{4} + 4x_{4} = 0}
\end{cases}
$$

我们不妨给两个自由变量 $\mathrm{x}_{2} = 1, \mathrm{x}_{4} = 0$

$$
\mathrm{x} =
\begin{bmatrix}
-2 \\
1 \\
0 \\
0
\end{bmatrix}
$$

也可以赋另外一组值 $\mathrm{x}_{2} = 0, \mathrm{x}_{4} = 1$

$$
\mathrm{x} =
\begin{bmatrix}
2 \\
0 \\
-2 \\
1
\end{bmatrix}
$$

这两个都称为特解，注意到有几个自由列，就有几个特解。最后**零空间的解为所有特解的线性组合**

$$
\mathrm{x} = \alpha_{1}
\begin{bmatrix}
-2 \\
1 \\
0 \\
0
\end{bmatrix} + \alpha_{2}
\begin{bmatrix}
2 \\
0 \\
-2 \\
1
\end{bmatrix}
$$

如果一个矩阵 A 的尺寸是 $m \times n$，它的秩为 r，那么它的主元个数和主元列数为 r，自由列数为 n - r。

这里总结一下求解零空间的算法：

1）消元得到 U

2）确定主元列和自由列

3）给自由变量赋值求的特解

4）所有特解的线性组合即为最终解

上述的消元还可以更精炼，U 向上消元，并且将主元值变成 1，得到**简化行阶梯形式**（reduced row echelon form），用 R 表示。

$$
\begin{matrix}
\mathrm{U} = \begin{bmatrix}
\boxed{\color{red} 1} & 2 & 2 & 2 \\
0 & 0 & \boxed{\color{red} 2} & 4 \\
0 & 0 & 0 & 0
\end{bmatrix} \\
\Downarrow \\
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 0 & -2 \\
0 & 0 & \boxed{\color{red} 2} & 4 \\
0 & 0 & 0 & 0
\end{bmatrix} \\
\Downarrow \\
\mathrm{R} = \begin{bmatrix}
\boxed{\color{red} 1} & 2 & 0 & -2 \\
0 & 0 & \boxed{\color{red} 1} & 2 \\
0 & 0 & 0 & 0
\end{bmatrix}
\end{matrix}
$$

这里为什么可以将主元值变成 1 的原因是 Ax = 0 无需考虑 b 的大小。如果我们将 R 中的主列全部排到左边，自由列放到右边

$$
\mathrm{R} =
\begin{bmatrix}
\boxed{\color{red} 1} & 2 & 0 & -2 \\
0 & 0 & \boxed{\color{red} 1} & 2 \\
0 & 0 & 0 & 0
\end{bmatrix} =
\left[\begin{array} {cc:cc}
\boxed{\color{red} 1} & 0 & 2 & -2 \\
0 & \boxed{\color{red} 1} & 0 & 2 \\ 
\hdashline
0 & 0 & 0 & 0
\end{array} \right] =
\left[\begin{array} {c:c}
\mathrm{I} & \mathrm{F} \\ 
\hdashline
0 & 0
\end{array} \right]
$$

F 块内的值刚好是特解中主元变量对应的值的相反数，这里很好理解，因为我们给自由变量特定的值，解出来的主元变量值一定是自由变量的系数相反数。按照这个方法，最后解的形式为

$$
\mathrm{x} =
\begin{bmatrix}
\mathrm{-F} \\
\mathrm{I}
\end{bmatrix}
$$

$$
\begin{bmatrix}
\mathrm{I} & \mathrm{F}
\end{bmatrix}
\begin{bmatrix}
\mathrm{x}_{\text{pivot}} \\
\mathrm{x}_{\text{free}}
\end{bmatrix} = \mathrm{I x_{\text{pivot}} + F x_{\text{free}}} = 0
$$


## 完整解
考虑求解 Ax = b 样例如下

$$
\begin{bmatrix}
1 & 2 & 2 & 2 \\
2 & 4 & 6 & 8 \\
3 & 6 & 8 & 10
\end{bmatrix}
\begin{bmatrix}
\mathrm{x}_{1} \\
\mathrm{x}_{2} \\
\mathrm{x}_{3} \\
\mathrm{x}_{4}
\end{bmatrix} =
\begin{bmatrix}
\mathrm{b}_{1} \\
\mathrm{b}_{2} \\
\mathrm{b}_{3}
\end{bmatrix}
$$

我们首先需要确定可解行（solvability），即 b 满足什么条件方程组有解。根据前面的总结，b 必须在 A 的列空间里，对增广矩阵进行消元

$$
\left[ \begin{array} {cccc:c}
1 & 2 & 2 & 2 & b_{1} \\
2 & 4 & 6 & 8 & b_{2} \\
3 & 6 & 8 & 10 & b_{3}
\end{array} \right] \Leftrightarrow
\left[ \begin{array} {cccc:c}
1 & 2 & 2 & 2 & \mathrm{b}_{1} \\
0 & 0 & 2 & 4 & \mathrm{b}_{2} - 2 \mathrm{b}_{1} \\
0 & 0 & 0 & 0 & \mathrm{b}_{3} - \mathrm{b}_{2} - \mathrm{b}_{1}
\end{array} \right]
$$

可知增广矩阵的最后一行必须要全为 0 才有解，即

$$
\mathrm{b_{3} - b_{2} - b_{1} = 0}
$$

总结一下，如果 A 的行向量线性组合产生了 0 行向量，那么 b 对应的线性组合也要产生 0，才会有解。

假设 Ax = b 是可解的，如下

$$
\begin{bmatrix}
1 & 2 & 2 & 2 \\
2 & 4 & 6 & 8 \\
3 & 6 & 8 & 10
\end{bmatrix}
\begin{bmatrix}
\mathrm{x}_{1} \\
\mathrm{x}_{2} \\
\mathrm{x}_{3} \\
\mathrm{x}_{4}
\end{bmatrix} =
\begin{bmatrix}
1 \\
5 \\
6
\end{bmatrix}
$$

求解的方法是特解 + 通解

1）特解：让所有的自由变量置成 0，求出主元变量的解，对于上例而言，$\mathrm{x}_{2}$ 和 $\mathrm{x}_{4}$ 是自由变量，置成 0。

$$
\begin{cases}
\mathrm{x_{1} + 2 x_{3} = 1} \\
\mathrm{2 x_{3} = 3}
\end{cases}
$$

特解为

$$
\mathrm{x_{\text{particular}}} =
\begin{bmatrix}
-2 \\
0 \\
3 / 2 \\
0
\end{bmatrix}
$$

2）通解，其实是零空间的解，即 $\mathrm{A x_{\text{null space}} = 0}$

$$
\mathrm{x_{n} = c_{1}}
\begin{bmatrix}
-2 \\
1 \\
0 \\
0
\end{bmatrix} + \mathrm{c_{2}}
\begin{bmatrix}
2 \\
0 \\
-2 \\
1
\end{bmatrix}
$$

而最终完整的解为二者之和

$$
\mathrm{x_{c} = x_{p} + x_{n}} =
\begin{bmatrix}
-2 \\
0 \\
3 / 2 \\
0
\end{bmatrix} +
\mathrm{c_{1}}
\begin{bmatrix}
-2 \\
1 \\
0 \\
0
\end{bmatrix} + \mathrm{c_{2}}
\begin{bmatrix}
2 \\
0 \\
-2 \\
1
\end{bmatrix}
$$

原因如下

$$
\begin{cases}
\mathrm{A x_{p} = b} \\
\mathrm{A x_{n} = 0}
\end{cases} \Leftrightarrow
\mathrm{A(x_{p} + x_{n}) = b}
$$

### 秩与解的关系
假设 A 是一个 $m \times n$ 的矩阵，它的秩为 r，那么 r 与 m 和 n 的数量关系如下

$$
\begin{matrix}
\mathrm{r \leq m} \\
\mathrm{r \leq n}
\end{matrix}
$$

1）列满秩代表 r = n，这时 n 个主元变量，0 个自由变量，零空间里只有零向量。例如

$$
\begin{bmatrix}
1 & 3 \\
2 & 1 \\
6 & 1 \\
5 & 1
\end{bmatrix} \Leftrightarrow
\begin{bmatrix}
1 & 3 \\
0 & -5 \\
0 & 0 \\
0 & 0
\end{bmatrix}
$$

b 的后两行不为 0 时，Ax = b 无解；反之，有唯一解。表面上时 4 个方程，但在有解的情况下，只有 2 个有效的方程或者叫线性无关方程。如果将 U 转化成 R（简化行阶梯形式）形如

$$
\mathrm{R} =
\begin{bmatrix}
\mathrm{I} \\
0
\end{bmatrix}
$$

2）行满秩代表 r = m，消元时不会出现零向量行，因此对任意的 b 有解。m 个主元变量，n - m 个自由变量。例如

$$
\begin{bmatrix}
1 & 2 & 6 & 5 \\
3 & 1 & 1 & 1
\end{bmatrix} \Leftrightarrow
\begin{bmatrix}
1 & 2 & 6 & 5 \\
0 & -5 & -17 & -14
\end{bmatrix}
$$

如果将 U 转化成 R 形如

$$
\mathrm{R} =
\begin{bmatrix}
\mathrm{I} & \mathrm{F}
\end{bmatrix}
$$

这里不一定全部的主元列在左侧，主元列和自由列交错也是可能的。

3）列满秩且行满秩代表 r = n = m，此时矩阵时方阵，并且时可逆的

$$
\begin{bmatrix}
1 & 2 \\
3 & 1
\end{bmatrix}
$$

如果将 U 转化成 R 形如

$$
\mathrm{R} =
\begin{bmatrix}
\mathrm{I}
\end{bmatrix}
$$

4）列和行均不满秩，即 r < m 且 r < n，此时 R 的形式

$$
\mathrm{R} =
\begin{bmatrix}
\mathrm{I} & \mathrm{F} \\
0 & 0
\end{bmatrix}
$$

此时有 0 个解或者 $\infty$ 个解

总结如下

| 秩与行列数的关系 | 主元变量个数 | 自由变量个数 | 零空间 N(A) | Ax = b 的解个数 | Ax = b 的解（如果有） |
| :----: | :----: | :----: | :----: | :----: | :----: |
| 列满秩（r = n < m） | n | 0 | 只有零向量 | 0 或者 1 | $\mathrm{x_{c} = x_{p}}$ |
| 行满秩（r = m < n） | m | n - m | 零向量和自由变量对应的向量 | $\infty$ | $\mathrm{x_{c} = x_{p} + x_{n}}$ |
| 列满秩且行满秩（r = n = m） | r | 0 | 零向量 | 1 | $\mathrm{x_{c} = x_{p}}$ |
| 列不满秩且行不满秩（r < n, r < m） | r | n - r | 零向量和自由变量对应的向量 | 0 或者 $\infty$ | $\mathrm{x_{c} = x_{p} + x_{n}}$ |


## 线性相关
线性相关（linear dependence）指的是一组向量是否通过某种线性组合得倒零向量。如果有非零的组合，即向量的系数至少一个不为 0，则称之为线性相关；反之称之为线性独立（independence）。例如，下面三个向量

$$
\mathrm{c}_{1}\begin{bmatrix}
2 \\
1
\end{bmatrix} + \mathrm{c}_{2}
\begin{bmatrix}
1 \\
2
\end{bmatrix} + \mathrm{c}_{3}
\begin{bmatrix}
3 \\
3
\end{bmatrix} =
\begin{bmatrix}
2 & 1 & 3 \\
1 & 2 & 3
\end{bmatrix}
\begin{bmatrix}
1 \\
1 \\
-1
\end{bmatrix} =
\begin{bmatrix}
0 \\
0
\end{bmatrix}
$$

我们能找到一组非 0 的系数 c，使得 Ac = 0，因此，这个三个向量是线性相关。我们会发现，这个三个向量构成一个矩阵，那么问题转化成 Ax = 0 是否有解。根据前面的结论，**如果零空间 N(A) 有非零向量（zero vector），那么 Ax = 0 有解，也就是说 A 的列向量线性相关**。同理，零空间只有零向量，那么 A 的列向量线性无关。

### 基
如果给定一组向量，那么我们可以对这一组向量做线性组合，所有的组合结果「生成」了一个空间 S。换句话说，S 包含了这一组向量的所有的线性组合。这里引出了**基**（basis）的概念：基是一组**线性无关**的向量，通过线性组合可以**生成**这个空间。例如，在 $R^{3}$ 实空间里的一组基

$$
\begin{bmatrix}
1 \\
0 \\
0
\end{bmatrix},
\begin{bmatrix}
0 \\
1 \\
0
\end{bmatrix},
\begin{bmatrix}
0 \\
0 \\
1
\end{bmatrix}
$$

这里如果只有两个向量能不能构成一组基呢？答案是否定的，因此，两个向量不能生成 $\mathbf{R^{3}}$，只能生成一个平面。此外，一个空间下，基并非唯一，我们还可以找出其他的基

$$
\begin{bmatrix}
1 \\
1 \\
2
\end{bmatrix},
\begin{bmatrix}
2 \\
2 \\
5
\end{bmatrix},
\begin{bmatrix}
3 \\
7 \\
8
\end{bmatrix}
$$

$\mathbf{R^{n}}$ 的空间需要 n 个线性独立的向量，它们构成了方阵 A，因此秩为 n，所有 A 是可逆的。

### 维数
空间的所有基含有相同个数的列向量，我们将这个数目称作为空间的维数（dimension）。

### 秩与维数的关系
假设有一个列空间 C(A)

$$
\begin{bmatrix}
1 & 2 & 3 & 1 \\
1 & 1 & 2 & 1 \\
1 & 2 & 3 & 1
\end{bmatrix}
$$

首先，列向量有三个元素，因此表示的是 $\mathbf{R^{3}}$ 空间；其次，4 个列向量线性相关，通过消元可知有 2 个主元列和 2 个自由列，矩阵的秩序为 2，维数是 2；然后，零空间 N(A) 有 2 个特解；最后列空间的前两个列向量可以构成一组基，零空间的两个特解也可以构成零空间的基。

$$
\mathrm{x_{n} = c_{1}}
\begin{bmatrix}
-1 \\
-1 \\
1 \\
0
\end{bmatrix} + \mathrm{c_{2}}
\begin{bmatrix}
-1 \\
0 \\
0 \\
1
\end{bmatrix}
$$

这里我们得到了两组数量关系

$$
\begin{aligned}
& \mathrm{\text{rank}(A) = \#(\text{主元列}) = \text{dim C}(A) = r} \\
& \mathrm{\text{dim N}(A) = \#(\text{自由列}) = n - r}
\end{aligned}
$$

### 4 个基本空间
假设有矩阵 $\mathrm{A_{m \times n}}$，4 个基本的空间如下

1）列空间 $\mathrm{C(A) \in R^{m}}$

2）零空间 $\mathrm{N(A) \in R^{n}}$

3）行空间 $\mathrm{R(A) = C(A^{T}) \in R^{n}}$，A 的行空间等价于 $A^{T}$ 的列空间

4）左零空间 $\mathrm{N(A^{T}) \in R^{m}}$，「左」来自于左乘

$$
\mathbf{(Ax)^{T} = 0^{T} = x^{T} A^{T}}
$$

| 基本空间 | 所属空间 | 维数/主元列数 |
| :----: | :----: | :----: |
| $\mathrm{C(A)}$ | $\mathrm{R^{m}}$ | r |
| $\mathrm{N(A^{T}})$ | $\mathrm{R^{m}}$ | m - r |
| $\mathrm{C(A^{T})}$ | $\mathrm{R^{n}}$ | r |
| $\mathrm{N(A)}$ | $\mathrm{R^{n}}$ | n - r |

注意到列空间 $\mathrm{C(A)}$ 和左零空间 $\mathrm{N(A^{T})}$ 隶属同空间，并且它们的维数之和是所属空间的维数，此外这两个空间是垂直的。行空间和零空间同理。


## 正交
正交向量（orthogonal vectors）意味着两个向量是垂直的（perpendicular），或者两者的内积为 0。

$$
\mathrm{x^{T} y = 0}
$$

零向量与任何向量正交。

如果我们将向量推广到子空间，即两个子空间是正交的，意味着，两个子空间内的任意向量是正交的。

$$
\mathrm{x^{T} y = 0, \forall x \in S, \forall y \in T \Leftrightarrow S \perp T}
$$

在 4 个基本空间一节里，我们说零空间和行空间是正交的，原因如下

$$
\begin{matrix}
\begin{bmatrix}
\cdots & \text{row 1 of A} & \cdots \\
\cdots & \text{row 2 of A} & \cdots \\
\vdots & \vdots & \vdots \\
\cdots & \text{row m of A} & \cdots
\end{bmatrix} &
\begin{bmatrix}
\mathrm{x}
\end{bmatrix} & = &
\begin{bmatrix}
0 \\
0 \\
\vdots \\
0
\end{bmatrix} \\
\mathrm{A} & \mathrm{x} & = & 0
\end{matrix}
$$

这里假设零空间有非零向量，那么 A 的每一行与 x 相乘，结果都是 0，因此 A 的行空间与零空间是正交的。更具体地，这两个子空间的维数和等于原空间 $\mathbf{R^{n}}$ 的维数。例如，在三维空间里，行空间的维数是 2，那么零空间的维数为 1。列空间和左零空间也是同样的道理。

我们前面知道任意一个矩阵 A，左乘它的转置，可以得到一个对称矩阵

$$
\mathrm{(A^{T})_{n \times m}A_{m \times n} = (A^{T}A)_{n \times n}}
$$

但是 $\mathrm{A^{T}A}$ 不一定可逆，反例如下

$$
\begin{bmatrix}
1 & 1 & 1 \\
3 & 3 & 3
\end{bmatrix}
\begin{bmatrix}
1 & 3 \\
1 & 3 \\
1 & 3
\end{bmatrix} =
\begin{bmatrix}
3 & 9 \\
9 & 27
\end{bmatrix}
$$

这里我们得到两个关系

$$
\begin{aligned}
& \mathrm{N(A^{T}A) = N(A)} \\
& \mathrm{\text{rank}(A^{T}A) = \text{rank}(A)}
\end{aligned}
$$

所以 $\mathrm{A^{T}A}$ 可逆的充要条件是 A 的列向量线性无关。

### 投影
投影（projection）指的是一个向量 b 在另一个向量 a 的反向上的分量 p。如图

![](./images/la3.png)

e 是误差分量，e 越小说明 b 和 a 越接近，e 与 a 是垂直关系。

$$
\mathrm{\overrightarrow{e} \perp \overrightarrow{a} \Leftrightarrow (\overrightarrow{b} - \overrightarrow{p}) \perp \overrightarrow{a}}
$$

用内积表示

$$
\mathrm{a^{T}(b - p) = a^{T}(b - xa) = 0}
$$

这里 x 是一个标量，a 和 b 是矢量。

$$
\mathrm{a^{T}xa = x a^{T} a = a^{T}b}
$$

可以求 x

$$
\mathrm{x = \frac{a^{T}b}{a^{T}a}}
$$

投影分量 p

$$
\mathrm{p = xa = \frac{a^{T}b}{a^{T}a} a = a \frac{a^{T}b}{a^{T}a} = \frac{aa^{T}}{a^{T}a} b = P b}
$$

我们可以定义投影矩阵

$$
\mathrm{P = \frac{aa^{T}}{a^{T}a}}
$$

分母 $\mathrm{a^{T}a}$ 是一个标量，a 表示一个列向量。这个投影矩阵的性质如下：

1）rank(P) = 1

2）P 是一个对称矩阵 $\mathrm{P^{T} = P}$

3）如果 b 对 a 做两次投影，得到的结果与投影一次的结果相同，即 $\mathrm{P^{2} = P}$

为什么需要投影？有些时候 Ax = b 是无解的，但我们还想求解，只能退而求其次，求一个近似解，即 $\mathrm{A\hat{x} = p}$，p 是 b 在列空间上的一个投影，而 $\mathrm{\hat{x}}$ 是这个 x 的近似解。

因为是投影，所以垂直分量是 $\mathrm{b - p}$，而 p 又是 A 的一个线性组合 $\mathrm{A\hat{x}}$，利用内积关系

$$
\mathrm{A^{T}e = A^{T}(b - Ax) = 0}
$$

如果这里是三维空间，上式表达的意思是 b 的垂直分量正交于 A 的列空间。这说明 e 属于 A 的零空间 N(A)

$$
\begin{aligned}
& \mathrm{e \perp C(A)} \\
& \mathrm{e \in N(A)}
\end{aligned}
$$

继续解上式

$$
\begin{matrix}
\mathrm{A^{T}b = A^{T}A \hat{x}} \\
\Downarrow \\
\mathrm{\hat{x} = (A^{T}A)^{-1}A^{T}b}
\end{matrix}
$$

b 在列空间上的分量 p

$$
\mathrm{p = A \hat{x} = A (A^{T}A)^{-1}A^{T}b = P b}
$$

这里的 A 代表的任意维，如果 A 是向量，上式退化成

$$
\mathrm{p = a \hat{x} = a (a^{T}a)^{-1} a^{T}b = \frac{aa^{T}}{a^{T}a}b}
$$

投影矩阵

$$
\mathrm{P = A (A^{T}A)^{-1}A^{T}}
$$

因为 A 不一定是可逆的，所以不能写成 $\mathrm{A A^{-1} (A^{T})^{-1} A^{T} = I}$。那如果 A 是可逆的，这意味着 A 零空间维数位 0，换句话说 b 是在列空间里，我们无需投影，原方程组有解。

如果 b 在列空间里，Pb = b，也就是无需投影；如果 b 正交于列空间，Pb = 0；如果 b 与列空间有夹脚，Pb 的结果是 b 在列空间上的投影。 

需要证明一个定理，**如果 A 的列向量线性无关的，那么 $\mathrm{A^{T}A}$ 是可逆的**。如果 $\mathrm{A^{T}A}$ 可逆，那么 $\mathrm{A^{T}A x = 0}$，x 必须是零向量。等式两侧左乘 $\mathrm{x^{T}}$

$$
\mathrm{x^{T} A^{T} A x = 0}
$$

根据转置的性质

$$
\mathrm{(Ax)^{T} Ax = 0}
$$

如果把 Ax 看成一个整体，那么 $\mathrm{(Ax)^{T} Ax}$ 表示的是 Ax 的模，这意味着

$$
\mathbf{Ax = 0}
$$

因为 A 的列向量是线性无关，可以推出 x = 0，这与我们的假设一直。

如果对投影矩阵转置，得到的也是投影矩阵，但是在行空间投影

$$
\begin{aligned}
\mathrm{P^{T}} &= \mathrm{(A (A^{T}A)^{-1}A^{T})^{T}} \\
&= \mathrm{A^{T} (AA^{T})^{-1}A}
\end{aligned}
$$

### 最小二乘法
最小二乘法（least sqaure）是一种线性回归的方法。如果我们有若干个点，我们需要绘制一条直线，尽可能的穿过这些点。例如，点 (1, 1)，(2, 2)，(3, 2)，假设直线为 y = C + D t

写出方程组

$$
\begin{cases}
\mathrm{C + D = 1} \\
\mathrm{C + 2D = 2} \\
\mathrm{C + 3D = 2}
\end{cases}
$$

写成矩阵形式

$$
\begin{matrix}
\begin{bmatrix}
1 & 1 \\
1 & 2 \\
1 & 3
\end{bmatrix} &
\begin{bmatrix}
\mathrm{C} \\
\mathrm{D}
\end{bmatrix} & = &
\begin{bmatrix}
1 \\
2 \\
3
\end{bmatrix} \\
\mathrm{A} & \mathrm{x} & = & \mathrm{b}
\end{matrix}
$$

根据前面的经验，这个方程组无解。我们现在先找到一组解，最接近 b。

$$
\mathrm{\min ||e||^{2} = \min ||Ax - b||^{2}}
$$

问题转化成求解 $\hat{x}$

$$
\begin{matrix}
\begin{bmatrix}
1 & 1 & 1 \\
1 & 2 & 3\\
\end{bmatrix} &
\begin{bmatrix}
1 & 1 \\
1 & 2 \\
1 & 3
\end{bmatrix} &
\begin{bmatrix}
\mathrm{\hat{C}} \\
\mathrm{\hat{D}}
\end{bmatrix} & = &
\begin{bmatrix}
1 & 1 & 1 \\
1 & 2 & 3\\
\end{bmatrix} &
\begin{bmatrix}
1 \\
2 \\
3
\end{bmatrix} \\
\mathrm{A^{T}} & \mathrm{A} & \mathrm{x} & = & \mathrm{A^{T}} & \mathrm{b}
\end{matrix}
$$

新的方程组

$$
\begin{bmatrix}
3 & 6 \\
6 & 14
\end{bmatrix}
\begin{bmatrix}
\mathrm{\hat{C}} \\
\mathrm{\hat{D}}
\end{bmatrix} =
\begin{bmatrix}
5 \\
11
\end{bmatrix} \Leftrightarrow
\begin{cases}
\mathrm{3 \hat{C} + 6 \hat{D} = 5} \\
\mathrm{6 \hat{C} + 14 \hat{D} = 11}
\end{cases}
$$

我们也可以用微分的方法求解

$$
\begin{aligned}
\min \mathrm{||e||^{2}} &= \min \mathrm{||Ax - b||^{2}} \\
&= \mathrm{(\hat{C} + \hat{D} - 1)^{2} + (\hat{C} + 2\hat{D} - 2)^{2} + (\hat{C} + 3\hat{D} - 2)^{2}}
\end{aligned}
$$

分别对两个分量求偏微分

$$
\begin{cases}
\mathrm{\hat{C} = 2/3} \\
\mathrm{\hat{D} = 1/2}
\end{cases}
$$

直线方程

$$
\mathrm{y = \frac{2}{3} + \frac{1}{2} t}
$$

### 正交基
正交基（orthogonal basis）指的是基向量之间相互正交，如果将基向量的模长变成单位长度，那么我们称之为标准正交基（orthonormal basis），用 q 表示。

$$
\mathrm{q_{i}^{T}q_{j}} =
\begin{cases}
1, & \mathrm{i = j} \\
0, & \mathrm{i \neq j}
\end{cases}
$$

我们将这些正交基（列向量的形式）放入到矩阵中，这个矩阵叫做标准正交矩阵 Q

$$
\begin{bmatrix}
| & | & \cdots & | \\
\mathrm{q_{1}} & \mathrm{q_{2}} & \cdots & \mathrm{q_{n}} \\
| & | & \cdots & |
\end{bmatrix}
$$

因为每一列与其他列相互正交，即

$$
\mathrm{Q^{T}Q} =
\begin{bmatrix}
- & \mathrm{q_{1}^{T}} & - \\
- & \mathrm{q_{2}^{T}} & - \\
\vdots & \vdots & \vdots \\
- & \mathrm{q_{n}^{T}} & -
\end{bmatrix}
\begin{bmatrix}
| & | & \cdots & | \\
\mathrm{q_{1}} & \mathrm{q_{2}} & \cdots & \mathrm{q_{n}} \\
| & | & \cdots & |
\end{bmatrix} =
\begin{bmatrix}
1 & 0 & \cdots & 0 \\
0 & 1 & \cdots & 0 \\
\vdots & \vdots  & \ddots  & \vdots \\
0 & 0 & \cdots & 1
\end{bmatrix} = \mathrm{I}
$$

一般情况，正交矩阵指的是方阵，Q 的转置等于 Q 的逆矩阵

$$
\mathbf{Q^{T} = Q^{-1}}
$$

现在我们考虑的是如何正交化？答案是格拉姆-施密特方法（Graham-Schmidt）。这个方法的思想是固定前面的列向量，将后一个列向量投影到前面的列向量空间上，保留垂直的分量作为当前的正交向量，为了保持所有的向量是单位模长，需要归一化。如图

![](./images/la4.png)

有两个向量 a 和 b，将 a 作为第一个正交向量 A，并对 A 归一化

$$
\mathrm{q_{1} = \frac{A}{||A||}}
$$

b 对 A 做投影，垂直分量为第二个正交向量 B

$$
\begin{aligned}
& \mathrm{B = b - Pb = b - \frac{A A^{T}}{A^{T} A} b} \\
& \mathrm{q_{2} = \frac{B}{||B||}}
\end{aligned}
$$

验证可知 B 与 A 垂直

$$
\mathrm{A^{T} B = A^{T} (b - \frac{A A^{T}}{A^{T} A} b) = A^{T} b - A^{T} \frac{A A^{T}}{A^{T} A} b = 0}
$$

假设此时还有一个向量 c，用同样的方法得到第三个正交向量

$$
\begin{aligned}
& \mathrm{C = c - P_{A}c - P_{B}c = c - \frac{A A^{T}}{A^{T} A} c - \frac{B B^{T}}{B^{T} B} c} \\
& \mathrm{q_{3} = \frac{C}{||C||}}
\end{aligned}
$$

利用 Q 可以对 A 进行分解

$$
\mathbf{A = Q R}
$$

其中 Q 是正交矩阵，R 是非奇异上三角矩阵。例如 A 有两列线性无关向量，可以得到正交向量

$$
\begin{aligned}
A &= \mathrm{[a, b]} \\
&= \mathrm{QR} \\
&= \mathrm{[q_{1}, q_{2}]}
\begin{bmatrix}
\mathrm{a^{T}q_{1}} & \mathrm{b^{T}q_{1}} \\
\mathrm{a^{T}q_{2}} & \mathrm{b^{T}q_{2}}
\end{bmatrix} \\
&= \mathrm{[q_{1}, q_{2}]}
\begin{bmatrix}
\mathrm{a^{T}q_{1}} & \mathrm{b^{T}q_{1}} \\
0 & \mathrm{b^{T}q_{2}}
\end{bmatrix}
\end{aligned}
$$


## 行列式
行列式（determinant）记作为 det(A) 用 |A| 表示，体现了方阵 A 的信息。行列式为 0 时，A 是奇异的。行列式的性质：

1）det(A) = 1

$$
\begin{vmatrix}
1 & 0 \\
0 & 1
\end{vmatrix} = 1
$$

2）交换两行或两列，改变行列式的符号

$$
\begin{vmatrix}
0 & 1 \\
1 & 0
\end{vmatrix} = -1
$$

3）行列式是一个线性函数：某一行同乘一个 t，等价于原式乘 t

$$
\begin{vmatrix}
\mathrm{ta} & \mathrm{tb} \\
\mathrm{c} & \mathrm{d}
\end{vmatrix} = \mathrm{t}
\begin{vmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c} & \mathrm{d}
\end{vmatrix}
$$

同一行线性可加

$$
\begin{vmatrix}
\mathrm{a + a'} & \mathrm{b + b'} \\
\mathrm{c} & \mathrm{d}
\end{vmatrix} =
\begin{vmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c} & \mathrm{d}
\end{vmatrix} + 
\begin{vmatrix}
\mathrm{a'} & \mathrm{b'} \\
\mathrm{c} & \mathrm{d}
\end{vmatrix}
$$

整个行列式并不满足可加性

$$\mathrm{\text{det}(A + B) \neq \text{det}(A) + \text{det}(B)}$$

4）如果 A 的两行（列）相等，那么 det(A) = 0，因为我们可以交换这两行（列），A 不变，但行列式符号改变。

5）某一行（列）加另一行（列）的 a 倍，行列式不变。

$$
\begin{vmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c} & \mathrm{d}
\end{vmatrix} =
\begin{vmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c + ia} & \mathrm{d + ib}
\end{vmatrix} =
\begin{vmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c} & \mathrm{d}
\end{vmatrix} + i
\begin{vmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{a} & \mathrm{b}
\end{vmatrix}
$$

6）某一行（列）为 0，行列式为 0。

7）将 A 转化成上三角 U，行列式等于对角元素之积。

$$
\text{det}(U) =
\begin{vmatrix}
\mathrm{d_{1}} & \mathrm{a_{12}} & \cdots & \mathrm{a_{1n}} \\
0 & \mathrm{d_{2}} & \cdots & \mathrm{a_{2n}} \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \mathrm{d_{n}}
\end{vmatrix}
$$

如果消元后，对角元素不为 0，那么通过向上消元将非对角元素消去

$$
\begin{aligned}
\mathrm{\det(U)} &=
\begin{vmatrix}
\mathrm{d_{1}} & \mathrm{a_{12}} & \cdots & \mathrm{a_{1n}} \\
0 & \mathrm{d_{2}} & \cdots & \mathrm{a_{2n}} \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \mathrm{d_{n}}
\end{vmatrix} \\
&=
\begin{vmatrix}
\mathrm{d_{1}} & 0 & \cdots & 0 \\
0 & \mathrm{d_{2}} & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \mathrm{d_{n}}
\end{vmatrix} \\
&= \mathrm{d_{1}}
\begin{vmatrix}
1 & 0 & \cdots & 0 \\
0 & \mathrm{d_{2}} & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \mathrm{d_{n}}
\end{vmatrix} \\
&= \mathrm{d_{1} d_{2}}
\begin{vmatrix}
1 & 0 & \cdots & 0 \\
0 & 1 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & \mathrm{d_{n}}
\end{vmatrix} \\
&= \mathrm{\prod_{i = 1}^{n} d_{i}}
\begin{vmatrix}
1 & 0 & \cdots & 0 \\
0 & 1 & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
0 & 0 & \cdots & 1
\end{vmatrix}
\end{aligned}
$$

8）当 A 是奇异的，A 的行列式为 0；当 A 时可逆的，det(A) 不为 0。

9）A 和 B 乘积的行列式等于各自行列式的乘积

$$
\begin{aligned}
& \mathrm{\det(AB) = \det(A) \det(B)} \\
& \mathrm{\det(A^{-1}) = \frac{1}{\det(A)}} \\
& \mathrm{\det(A^{2}) = \det(A)^{2}} \\
& \mathrm{\det(2A) = 2^{n} \det(A)}
\end{aligned}
$$

10）A 转置的行列式等于 A 的行列式

$$
\mathrm{\det(A^{T}) = \det(A)}
$$

### 公式
我们从一个 2 阶行列式出发，利用上面的性质逐步分解

$$
\begin{aligned}
\begin{vmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c} & \mathrm{d}
\end{vmatrix} &=
\begin{vmatrix}
\mathrm{a} & 0 \\
\mathrm{c} & \mathrm{d}
\end{vmatrix} +
\begin{vmatrix}
0 & \mathrm{b} \\
\mathrm{c} & \mathrm{d}
\end{vmatrix} \\
&=
\begin{vmatrix}
\mathrm{a} & 0 \\
\mathrm{c} & 0
\end{vmatrix} +
\begin{vmatrix}
\mathrm{a} & 0 \\
0 & \mathrm{d}
\end{vmatrix} +
\begin{vmatrix}
0 & \mathrm{b} \\
\mathrm{c} & 0
\end{vmatrix} +
\begin{vmatrix}
0 & \mathrm{b} \\
0 & \mathrm{d}
\end{vmatrix} \\
&= \begin{vmatrix}
\mathrm{a} & 0 \\
0 & \mathrm{d}
\end{vmatrix} +
\begin{vmatrix}
0 & \mathrm{b} \\
\mathrm{c} & 0
\end{vmatrix} \\
&= \begin{vmatrix}
\mathrm{a} & 0 \\
0 & \mathrm{d}
\end{vmatrix} -
\begin{vmatrix}
\mathrm{c} & 0 \\
0 & \mathrm{b}
\end{vmatrix} \\
&= \mathrm{ad - bc}
\end{aligned}
$$

这里我们可以得知，一个行列式按照上面的形式分解，最终每一行每一列只会保留一个元素。对 3 阶的行列式而言，分解如下

$$
\begin{aligned}
\begin{vmatrix}
\mathrm{a_{11}} & \mathrm{a_{12}} & \mathrm{a_{13}} \\
\mathrm{a_{21}} & \mathrm{a_{22}} & \mathrm{a_{23}} \\
\mathrm{a_{31}} & \mathrm{a_{32}} & \mathrm{a_{33}}
\end{vmatrix} &=
\begin{vmatrix}
\mathrm{a_{11}} & 0 & 0 \\
0 & \mathrm{a_{22}} & 0 \\
0 & 0 & \mathrm{a_{33}}
\end{vmatrix} +
\begin{vmatrix}
\mathrm{a_{11}} & 0 & 0 \\
0 & 0 & \mathrm{a_{23}} \\
0 & \mathrm{a_{32}} & 0
\end{vmatrix} \\
& +
\begin{vmatrix}
0 & \mathrm{a_{12}} & 0 \\
\mathrm{a_{21}} & 0 & 0 \\
0 & 0 & \mathrm{a_{33}}
\end{vmatrix} +
\begin{vmatrix}
0 & \mathrm{a_{12}} & 0 \\
0 & 0 & \mathrm{a_{23}} \\
\mathrm{a_{31}} & 0 & 0
\end{vmatrix} \\
& +
\begin{vmatrix}
0 & 0 & \mathrm{a_{13}} \\
\mathrm{a_{21}} & 0 & 0 \\
0 & \mathrm{a_{32}} & 0
\end{vmatrix} +
\begin{vmatrix}
0 & 0 & \mathrm{a_{13}} \\
0 & \mathrm{a_{22}} & 0 \\
\mathrm{a_{31}} & 0 & 0
\end{vmatrix} \\
&= \mathrm{a_{11}a_{22}a_{33} - a_{11}a_{23}a_{32} - a_{12}a_{21}a_{33}} \\
&+ \mathrm{a_{12}a_{23}a_{31} + a_{13}a_{21}a_{32} - a_{13}a_{22}a_{31}}
\end{aligned}
$$

那么对于 n 阶的公式如下

$$
\mathrm{\det(A) = \sum_{n!} \pm a_{1\alpha}a_{2\beta}a_{3\gamma} \cdots a_{n \omega}}
$$

其中，$\mathrm{(\alpha, \beta, \gamma, \cdots, \omega)}$ 是 $\mathrm{(1, 2, 3, \cdots, n)}$ 的一种排序，总共有 n! 中。

### 代数余子式
从 3 阶的行列式展开来看

$$
\begin{aligned}
\mathrm{\det(A)} &= \mathrm{a_{11}(a_{22}a_{33} - a_{23}a_{32})} \\
&+ \mathrm{a_{12}(-a_{21}a_{33} + a_{31}a_{23})} \\
&+ \mathrm{a_{13}(a_{21}a_{32} - a_{22}a_{31})}
\end{aligned}
$$

所以这是按照 A 的第一行展开。$\mathrm{a_{ij}}$ 的代数余子式（cofactor）$\mathrm{C_{ij}}$ 指的是一个 n-1 阶的行列式，这里将 n-1 阶是通过消除第 i 列和第 i 行。当 i+j 是偶数时，符号时正号码；反之，符号是负号。

$$
\begin{vmatrix}
+ & - & + & - & + & - \\
- & + & - & + & - & + \\
+ & - & + & - & + & - \\
- & + & - & + & - & +
\end{vmatrix}
$$

根据代数余子式，我们得到一种新的计算行列式的方法：A 的某一行（列）的元素分别乘以对应的代数余子式，再求和。

$$
\begin{aligned}
\mathrm{\det(A)} &= \mathrm{a_{11}C_{11} + a_{12}C_{12} + \cdots + a_{1n}C_{1n}} \\
&= \mathrm{\sum_{i=1}^{n} a_{1i}C_{1i}} \\
&= \mathrm{\sum_{i=1}^{n} a_{2i}C_{2i}} \\
&= \vdots \\
&= \mathrm{\sum_{i=1}^{n} a_{ni}C_{ni}}
\end{aligned}
$$

如果把代数余子式按位置组合成新的矩阵 C，我们会得到一个等式

$$
\begin{aligned}
\mathrm{A C^{T}} &= \mathrm{\det(A) I} \\
&=
\begin{bmatrix}
\mathrm{a_{11}} & \mathrm{a_{12}} & \cdots & \mathrm{a_{1n}} \\
\mathrm{a_{21}} & \mathrm{a_{22}} & \cdots & \mathrm{a_{2n}} \\
\vdots & \vdots & \vdots & \vdots \\
\mathrm{a_{n1}} & \mathrm{a_{n2}} & \cdots & \mathrm{a_{nn}}
\end{bmatrix}
\begin{bmatrix}
\mathrm{C_{11}} & \mathrm{C_{21}} & \cdots & \mathrm{C_{n1}} \\
\mathrm{C_{12}} & \mathrm{C_{22}} & \cdots & \mathrm{C_{n2}} \\
\vdots & \vdots & \vdots & \vdots \\
\mathrm{C_{1n}} & \mathrm{C_{2n}} & \cdots & \mathrm{C_{nn}}
\end{bmatrix} \\
&=
\begin{bmatrix}
\mathrm{\det(A)} & 0 & \cdots & 0 \\
0 & \mathrm{\det(A)} & \cdots & 0 \\
\vdots & \vdots & \vdots & \vdots \\
0 & 0 & \cdots & \mathrm{\det(A)}
\end{bmatrix} \\
&= \mathrm{\det(A)}
\begin{bmatrix}
1 & 0 & \cdots & 0 \\
0 & 1 & \cdots & 0 \\
\vdots & \vdots & \vdots & \vdots \\
0 & 0 & \cdots & 1
\end{bmatrix}
\end{aligned}
$$

这里需要注意**某一列（行）的元素与其余列（行）的代数余子式的乘积和为 0**。所以，A 的逆矩阵可以通过下式求得

$$
\mathbf{A^{-1} = \frac{1}{\det(A)} C^{T}}
$$

有了逆矩阵计算方法，我们可以直接求解 Ax = b

$$
\mathrm{x = A^{-1} b = \frac{1}{\text{det}(A)} C^{T} b}
$$

这个公式叫做「克莱姆法则」（Cramer's rule）。

### 体积
假设有一个 3 阶的行列式，每一列表示一个向量，可以看作是原点出发的边，因为是三位空间，所以表示的是长宽高。行列式代表的是体积

$$
\mathrm{\det(A) = \text{volume of box}}
$$

如果是二维的，则表示平行四边形的面积

![](./images/la5.png)

C 的坐标是 (3, 1)，D 的坐标是 (1, 2)，将这两个坐标放入方阵，计算行列式即可

$$
\det(A) =
\begin{vmatrix}
\mathrm{a} & \mathrm{b} \\
\mathrm{c} & \mathrm{d} 
\end{vmatrix} =
\mathrm{ad - bc = 6 - 1 = 5}
$$


## 特征值和特征向量
矩阵就像一个函数，作用在 x 上，得到 Ax 表示对 x 做了映射，变换前后的两个向量方向通常不同，而我们关心的则是共线的情况。使得 Ax 与 x 共线的向量称之为特征向量（eigen vector），这里的共线表示同向、反向或者零向量。

$$
\mathbf{Ax = \lambda x}
$$

$\lambda$ 则是特征值（eigen value）。

### 求解
现在考虑如何求解

$$
\begin{matrix}
\mathrm{Ax = \lambda x} \\
\Downarrow \\
\mathrm{(A - \lambda I)x = 0}
\end{matrix}
$$

如果存在 x，那么 $\mathrm{A - \lambda I}$ 一定是奇异的；否则，x 只有零向量的解。

$$
\mathrm{\det(A - \lambda I) = 0}
$$

例如

$$
\mathrm{A} =
\begin{bmatrix}
3 & 1 \\
1 & 3
\end{bmatrix}
$$

$$
\mathrm{\det(A - \lambda I)} = 0 =
\begin{vmatrix}
3 - \lambda & 1 \\
1 & 3 - \lambda
\end{vmatrix} = (3 - \lambda)^{2} - 1
$$

解得

$$
\begin{cases}
\lambda_{1} = 2 \\
\lambda_{2} = 4
\end{cases}
$$

重新代回

$$
\begin{aligned}
& \mathrm{A - \lambda_{1} I} =
\begin{bmatrix}
3 - 2 & 1 \\
1 & 3 - 2
\end{bmatrix} =
\begin{bmatrix}
1 & 1 \\
1 & 1
\end{bmatrix} \\
& \mathrm{A - \lambda_{2} I} =
\begin{bmatrix}
3 - 4 & 1 \\
1 & 3 - 4
\end{bmatrix} =
\begin{bmatrix}
-1 & 1 \\
1 & -1
\end{bmatrix}
\end{aligned}
$$

特征向量

$$
\begin{aligned}
& \mathrm{x_{1}} =
\begin{bmatrix}
1 \\
-1
\end{bmatrix} \\
& \mathrm{x_{2}} =
\begin{bmatrix}
1 \\
1
\end{bmatrix}
\end{aligned}
$$

特征值与行列式的关系

$$
\mathrm{\det(A) = \prod_{i=1}^{n} \lambda_{i}}
$$

### 迹
矩阵 A 对角线上的值的和叫做迹（trace）。

$$
\mathrm{\text{Tr}(A) = \sum_{i=1}^{n} a_{ii} = \sum_{i=1}^{n} \lambda_{i}}
$$

一个上三角矩阵的特征值就是对角线上的值，例如

$$
\begin{bmatrix}
3 & 1 \\
0 & 3
\end{bmatrix}
$$

特征值

$$
\begin{cases}
\lambda_{1} = 3 \\
\lambda_{2} = 3
\end{cases}
$$

特征值相等，对应相同的特征向量

$$
\mathrm{(A - \lambda I)x} =
\begin{bmatrix}
0 & 1 \\
0 & 0
\end{bmatrix} \mathrm{x} =
\begin{bmatrix}
0 \\
0
\end{bmatrix}
$$

$$
\mathrm{x} =
\begin{bmatrix}
1 \\
0
\end{bmatrix}
$$

这时候没有第二个独立的特征向量。

### 对角化
假设 A 有 n 个线性无关的特征向量，并将它们以列向量的形式排列成矩阵 S。

$$
\begin{aligned}
\mathrm{AS} &= \mathrm{A}
\begin{bmatrix}
| & | & \cdots & | \\
\mathrm{x_{1}} & \mathrm{x_{2}} & \cdots & \mathrm{x_{n}} \\
| & | & \cdots & |
\end{bmatrix} =
\begin{bmatrix}
| & | & \cdots & | \\
\mathrm{\lambda_{1}x_{1}} & \mathrm{\lambda_{2}x_{2}} & \cdots & \mathrm{\lambda_{n}x_{n}} \\
| & | & \cdots & |
\end{bmatrix} \\
&=
\begin{bmatrix}
| & | & \cdots & | \\
\mathrm{x_{1}} & \mathrm{x_{2}} & \cdots & \mathrm{x_{n}} \\
| & | & \cdots & |
\end{bmatrix}
\begin{bmatrix}
\lambda_{1} & 0 & \cdots & 0 \\
0 & \lambda_{2} & \cdots & 0 \\
\vdots & \vdots & \vdots & \vdots \\
0 & 0 & \cdots & \lambda_{n}
\end{bmatrix} \\
&= \mathrm{S} \Lambda
\end{aligned}
$$

A 与 S 矩阵相乘，可以看作 A 与 S 的每一列分别相乘，这是乘法的列向量线性组合视角。$\Lambda$ 称之为特征值矩阵（eigenvalue matrix）。

$$
\begin{aligned}
& \mathrm{S^{-1}AS = \Lambda} \\
& \mathrm{A = S \Lambda S^{-1}}
\end{aligned}
$$

总结一下，**如果方阵 A 有 n 个线性无关的特征向量，它就可以被对角化**。矩阵的幂指数对应特征值的幂指数或特征矩阵的幂指数。

$$
\begin{aligned}
& \mathrm{A^{n} x = A^{n-1} Ax = A^{n-1} \lambda x = \lambda A^{n-1}x = \lambda^{n}x} \\
& \mathrm{A^{n} = (S \Lambda S^{-1})^{n} = S \Lambda^{n} S^{-1}}
\end{aligned}
$$

随着 n 趋向于无穷大，$\mathrm{A^{n}}$ 趋向于 0，条件是 A 的所有特征值 $\mathrm{|\lambda_{i}| < 1}$。

**一个矩阵 A 的特征值相互不同（无重复特征值），那么 A 一定有 n 个线性无关的特征向量（可对角化）。**

如果有重复的特征值，可能有 n 个线性无关特征向量。例如 A 是单位矩阵，特征值均为 1，但是可以找到 n 个线性无关特征向量。如果 A 时上三角矩阵且对角元素有重复，那么特征值有重复，特征向量也会重复。这种向量时不能对角化处理的。

### 幂指数
现在有一个递推方程

$$
\mathrm{u_{k+1} = A u_{k}}
$$

给定初始值 $\mathrm{u_{0}}$，那么我们可以求其中任意一项

$$
\mathrm{u_{k} = Au_{k-1} = A^{2} u_{k-2} = \cdots = A^{k} u_{0}}
$$

考虑把 $\mathrm{u_{0}}$ 写成特征向量的一种线性组合

$$
\mathrm{u_{0} = \sum_{i = 1}^{n} c_{i} x_{i}}
$$

那么 $\mathrm{u_{1}}$ 可知

$$
\mathrm{u_{1} = Au_{1} = \sum_{i = 1}^{n} Ac_{i} x_{i} = \sum_{i = 1}^{n} c_{i} \lambda_{i} x_{i}}
$$

同理，$\mathrm{u_{k}}$ 可知

$$
\mathrm{u_{k} = A^{k}u_{0} = \sum_{i = 1}^{n} A^{n}c_{i} x_{i} = \sum_{i = 1}^{n} c_{i} \lambda_{i}^{n} x_{i}}
$$

这样一来，求 A 的幂指数转化成求 A 的特征值的幂指数，简化了计算。

以斐波那契数列为例，0，1，1，2，3，5，8，13... 我们希望求 $\mathrm{F_{100}}$ 并且了解增长的速度有多快（由特征值决定）。其递推方程如下

$$
\mathrm{F_{n + 2} = F_{n + 1} + F_{n}}
$$

构造方程组

$$
\begin{cases}
\mathrm{F_{n + 2} = F_{n + 1} + F_{n}} \\
\mathrm{F_{n + 1} = F_{n + 1}}
\end{cases}
$$

定义

$$
\mathrm{u_{n}} =
\begin{bmatrix}
\mathrm{F_{n + 1}} \\
\mathrm{F_{n}}
\end{bmatrix}
$$

可得方程组

$$
\begin{matrix}
\begin{bmatrix}
\mathrm{F_{n + 2}} \\
\mathrm{F_{n + 1}}
\end{bmatrix} & = &
\begin{bmatrix}
1 & 1 \\
1 & 0
\end{bmatrix} &
\begin{bmatrix}
\mathrm{F_{n + 1}} \\
\mathrm{F_{n}}
\end{bmatrix} \\
\mathrm{u_{n + 1}} & = & \mathrm{A} & \mathrm{u_{n}}
\end{matrix}
$$

A 是一个对称矩阵，有 n 个线性无关的特征向量，其特征值如下

$$
\begin{matrix}
\mathrm{\det(A) = \lambda^{2} - \lambda - 1 = 0} \\
\Downarrow \\
\begin{cases}
\mathrm{\lambda_{1} = \frac{1 + \sqrt{5}}{2} \approx 1.618} \\
\mathrm{\lambda_{2} = \frac{1 - \sqrt{5}}{2} \approx -0.618}
\end{cases}
\end{matrix}
$$

同样可以求出两个特征向量 $\mathrm{x_{1}, x_{2}}$。

$$
\begin{matrix}
\mathrm{(A - \lambda I)x} =
\begin{bmatrix}
1 - \lambda & 1 \\
1 & \lambda
\end{bmatrix}
\begin{bmatrix}
\lambda \\
1
\end{bmatrix} =
\begin{bmatrix}
0 \\
0
\end{bmatrix} \\
\Downarrow \\
\mathrm{x_{1}} =
\begin{bmatrix}
\lambda_{1} \\
1
\end{bmatrix},
\mathrm{x_{2}} =
\begin{bmatrix}
\lambda_{2} \\
1
\end{bmatrix}
\end{matrix}
$$

根据构造的方程组，初始值

$$
\mathrm{u_{0}} =
\begin{bmatrix}
\mathrm{F_{1}} \\
\mathrm{F_{0}}
\end{bmatrix} =
\begin{bmatrix}
0 \\
0
\end{bmatrix} =
\mathrm{c_{1}x_{1} + c_{2}x_{2}}
$$

最后，绝对值大于 1 的特征值将会主导数列增长的速度

$$
\mathrm{F_{100} \approx c_{1}(\frac{1 + \sqrt{5}}{2})^{100}}
$$

### 微分方程
这里有一个常微分方程组

$$
\begin{cases}
\mathrm{\frac{d u_{1}}{dt} = -u_{1} + 2u_{2}} \\
\mathrm{\frac{d u_{2}}{dt} = u_{1} - 2u_{2}}
\end{cases}
$$

初始条件

$$
\mathrm{u(0)} =
\begin{bmatrix}
1 \\
0
\end{bmatrix}
$$

转化成如下形式

$$
\begin{matrix}
\mathrm{\frac{d}{dt}} &
\begin{bmatrix}
\mathrm{u_{1}} \\
\mathrm{u_{2}}
\end{bmatrix} & = &
\begin{bmatrix}
-1 & 2 \\
1 & -2
\end{bmatrix} &
\begin{bmatrix}
\mathrm{u_{1}} \\
\mathrm{u_{2}}
\end{bmatrix} \\
\mathrm{\frac{d}{dt}} & \mathrm{U} & = & \mathrm{A} & \mathrm{U}
\end{matrix}
$$

A 的两个特征值

$$
\begin{cases}
\lambda_{1} = 0 \\
\lambda_{2} = -3
\end{cases}
$$

特征向量

$$
\mathrm{x_{1}} =
\begin{bmatrix}
2 \\
1
\end{bmatrix},
\mathrm{x_{2}} =
\begin{bmatrix}
1 \\
-1
\end{bmatrix}
$$

常微分方程的解

$$
\mathrm{u(t) = c_{1}e^{\lambda_{1}t}x_{1} + c_{2}e^{\lambda_{2}t}x_{2}}
$$

根据初始条件可以求出系数，最终解为

$$
\mathrm{u(t)} = \frac{1}{3}
\begin{bmatrix}
2 \\
1
\end{bmatrix} + \frac{1}{3} \mathrm{e^{-3t}}
\begin{bmatrix}
1 \\
-1
\end{bmatrix}
$$

特征值为 0 产生一个稳态（steady state）。当 t 趋向于正无穷，这个解会达到一个稳态。

$$
\mathrm{U}(\infty) =
\frac{1}{3}
\begin{bmatrix}
2 \\
1
\end{bmatrix}
$$

U(t) 的最终解类似于上一节的幂指数解

$$
\begin{matrix}
\mathrm{u_{k+1} = Au_{k}} \\
\Downarrow \\
\mathrm{u_{k} = c_{1}\lambda_{1}^{k}x_{1} + c_{2}\lambda_{2}^{k}x_{2} + \cdots }
\end{matrix}
$$

特征值决定了在无穷时间下解的的状态

1）稳定（stability）：不论初始值为多少，随着 t 趋向于无穷，u(t) 趋向于 0，这要求 $\mathrm{e^{\lambda t} \rightarrow 0}$，可知，$\lambda < 0$。这里解的形式如果是复数形式，e.g. $\mathrm{e^{(-3+6i)t}}$，虚数部分不影响，因为虚数部分的模长为单位 1。

$$
\mathrm{|e^{(-3+6i)t}| = |e^{-3t}e^{6it}| = |e^{-3t}|}
$$

所以，特征值实数部分要小于 0。

2）稳态（steady state）：一个特征值为 0，剩余的特征值（实数部分）小于 0。

3）发散（blow up）：任意的特征值（实数部分）大于 0。

考虑一个二阶微分方程

$$
\mathrm{y'' + b y' + ky = 0}
$$

类似于斐波那契数列构造方程组，我们令

$$
\mathrm{u} =
\begin{bmatrix}
\mathrm{y'} \\
\mathrm{y}
\end{bmatrix}
$$

重写方程组

$$
\mathrm{u'} =
\begin{bmatrix}
\mathrm{y''} \\
\mathrm{y'}
\end{bmatrix} =
\begin{bmatrix}
\mathrm{-b} & \mathrm{k} \\
1 & 0
\end{bmatrix}
\begin{bmatrix}
\mathrm{y'} \\
\mathrm{y}
\end{bmatrix}
$$

类似地，我们可以处理更高阶的微分。

### 矩阵指数
两个泰勒展开式

$$
\mathrm{e^{x} = 1 + x + \frac{x^{2}}{2} + \cdots \frac{x^{n}}{n!} = \sum_{i=n = 0}^{\infty} \frac{x^{n}}{n!}}
$$

几何级数

$$
\mathrm{\frac{1}{1-x} = \sum_{n=0} x^{n}}
$$

对矩阵同样适应

$$
\mathrm{e^{At} = I + At + \frac{(At)^{2}}{2} + \frac{(At)^{3}}{6} + \cdots + \frac{(At)^{n}}{n!} + \cdots}
$$

$$
\mathrm{(I - At)^{-1} = I + At + (At)^{2} + \cdots + (At)^{n} + \cdots}
$$

如果 A 可对角化，那么

$$
\mathrm{e^{At} = \sum_{n=0}^{\infty} S^{-1} (\Lambda t)^{n} S = S^{-1} e^{\Lambda t} S}
$$


## 复数矩阵
含有复数的矩阵称之为复数矩阵。

### 模长
考虑一个复数向量 z

$$
\mathrm{z} =
\begin{bmatrix}
\mathrm{z_{1}} \\
\mathrm{z_{1}} \\
\vdots \\
\mathrm{z_{n}}
\end{bmatrix} \in \mathrm{C^{n}}
$$

它的模长的平方等于它与自身的内积

$$
\mathrm{|z|^{2} = \bar{z}^{T}z}
$$

$\mathrm{\bar{z}^{T}}$ 表示对 z 进行转置和取共轭，叫做 Hermitian

$$
\mathrm{z^{H} = \bar{z}^{T}}
$$

同样地，如果计算两个复数向量 x 和 y 的内积，需要对其中之一进行 H 化

$$
\mathrm{\bar{x}^{T} y = x^{H} y}
$$

复数对称矩阵需要满足

$$
\mathbf{A^{H} = A}
$$

例如

$$
\begin{bmatrix}
2 & 3 + i \\
3 - i & 5
\end{bmatrix}
$$

特征向量构成特征矩阵 Q 叫做酉矩阵（unitary orthogonal matrix）

$$
\mathrm{\bar{Q}^{T} Q = I = Q^{H} Q}
$$

### FFT
FFT 指的是快速傅立叶变换（Fast Fourier Transform）。傅立叶矩阵由复数正交向量构成，如下

$$
\mathrm{F_{n}} =
\begin{bmatrix}
1 & 1 & 1 & \cdots & 1 \\
1 & \mathrm{w} & \mathrm{w^{2}} & \cdots & \mathrm{w^{n-1}} \\
1 & \mathrm{w^{2}} & \mathrm{w^{4}} & \cdots & \mathrm{w^{2(n-1)}} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
1 & \mathrm{w^{n-1}} & \mathrm{w^{2(n-1)}} & \cdots & \mathrm{w^{(n-1)^{2}}}
\end{bmatrix}
$$

可知，傅立叶矩阵的某一元素的值为

$$
\mathrm{F_{n}(i, j) = w^{ij}}
$$

这里的 i 和 j 的取值从 0 到 n-1

此外，w 是一个特殊值，满足

$$
\mathrm{w^{n} = 1}
$$

可求

$$
\mathrm{w = e^{i\frac{2\pi}{n}} = \cos \frac{2\pi}{n} + i \sin \frac{2\pi}{n}}
$$

w 表示一个单位圆，坐标是复数坐标（横坐标是实部，纵坐标是虚部）。以 4 阶为例

$$
\mathrm{w = e^{i\frac{2\pi}{4}} = e^{\frac{\pi}{2}i} = i}
$$

4 阶傅立叶矩阵如下，任意两列是正交的

$$
\mathrm{F_{4}} =
\begin{bmatrix}
1 & 1 & 1 & 1 \\
1 & \mathrm{i} & \mathrm{i^{2}} & \mathrm{i^{3}} \\
1 & \mathrm{i^{2}} & \mathrm{i^{4}} & \mathrm{i^{6}} \\
1 & \mathrm{i^{3}} & \mathrm{i^{6}} & \mathrm{i^{9}}
\end{bmatrix} =
\begin{bmatrix}
1 & 1 & 1 & 1 \\
1 & \mathrm{i} & -1 & \mathrm{-i} \\
1 & -1 & 1 & -1 \\
1 & \mathrm{-i} & -1 & \mathrm{i}
\end{bmatrix}
$$

FFT 依赖于如下性质

$$
\mathrm{F_{2n}^{2} = F_{n}}
$$

因为

$$
\mathrm{w_{n} = e^{\frac{2\pi}{n}i} = e^{\frac{2\pi}{2n}2i} = w_{2n}^{2}}
$$

矩阵分解如下

$$
\begin{bmatrix}
\mathrm{F_{2n}}
\end{bmatrix} =
\begin{bmatrix}
\mathrm{I} & \mathrm{D} \\
\mathrm{I} & \mathrm{-D}
\end{bmatrix}
\begin{bmatrix}
\mathrm{F_{n}} & 0 \\
0 & \mathrm{F_{n}}
\end{bmatrix} \mathrm{P}
$$

其中 D 是对角矩阵

$$
\mathrm{D} =
\begin{bmatrix}
1 &  &  &  &  \\
& \mathrm{w} & & & \\
& & \mathrm{w^{2}} & & \\\
& & & \ddots & \\
& & & & \mathrm{w^{n-1}}
\end{bmatrix}
$$

P 是置换矩阵，按照偶数行（从 0 开始）排在前面，奇数行排在后面，以一个 4 阶为例

$$
\mathrm{P} =
\begin{bmatrix}
1 & 0 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 1 & 0 & 0 \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

递归地继续分解矩阵，直到 1 阶或 2 阶矩阵。FFT 的核心思想是建立一个复数正交矩阵。


## 正定
### 对称矩阵
矩阵的特殊性体现在特征值和特征向量上。对于实对称矩阵

$$
\mathbf{A^{T} = A}
$$

所有的特征值是实数，特征向量是正交的。

$$
\mathbf{A = S \Lambda S^{-1} = Q \Lambda Q^{-1} = Q \Lambda Q^{T}}
$$

为什么对称矩阵 $\mathrm{A}$ 的特征值是实数？假设其特征值是 $\lambda$，特征向量是 $\mathrm{x}$，有

$$
\mathbf{A x = \lambda x}
$$

取共轭

$$
\mathbf{\bar{A} \bar{x} = \bar{\lambda} \bar{x}}
$$

因为 $\mathrm{A}$ 是对称矩阵，所以有

$$
\mathbf{A \bar{x} = \bar{\lambda} \bar{x}}
$$

对等式两侧转置

$$
\mathbf{\bar{x}^{T} A^{T} = \bar{x}^{T} \bar{\lambda}}
$$

$\mathrm{A}$ 是对称矩阵

$$
\mathbf{\bar{x}^{T} A = \bar{x}^{T} \bar{\lambda}}
$$

同时右乘 $\mathbf{x}$

$$
\mathbf{\bar{x}^{T} A x = \bar{x}^{T} \bar{\lambda} x}
$$

对 $\mathbf{A x = \lambda x}$ 左乘 $\mathbf{\bar{x}^{T}}$

$$
\mathbf{\bar{x}^{T} A x = \bar{x}^{T} \lambda x}
$$

联立上式

$$
\mathbf{\bar{x}^{T} A x = \bar{x}^{T} \lambda x = \bar{x}^{T} \bar{\lambda} x}
$$

因为特征向量的与自己的内积非负，并且特征向量是非零向量，所以

$$
\mathbf{\bar{x}^{T} x > 0}
$$

最终可知特征值是实数。

$$
\lambda = \bar{\lambda}
$$

对复数矩阵，特征值是实数的条件是

$$
\mathbf{A = \bar{A}^{T}}
$$

对实数矩阵，特征值是实数的条件是

$$
\mathbf{A = A^{T}}
$$

对称矩阵的主元符号与特征值的符号相等，也就是说正主元的个数等于正特征值的个数。

### 正定矩阵
正定矩阵（positive definite matrix）是实对称矩阵，所有的特征值是正值，所有的主元是正值，所有的子行列式（sub-determinant）是正值。这里的子行列式指的是从左上角到右下角逐层扩充的行列式。例如有一行列式如下

$$
\begin{vmatrix}
5 & 3 & 4 \\
3 & 2 & 7 \\
4 & 7 & 6
\end{vmatrix}
$$

子行列式如下

$$
\begin{vmatrix}
5
\end{vmatrix},
\begin{vmatrix}
5 & 3 \\
3 & 2
\end{vmatrix},
\begin{vmatrix}
5 & 3 & 4 \\
3 & 2 & 7 \\
4 & 7 & 6
\end{vmatrix}
$$

矩阵 A 是正定矩阵，需要符合下面三个条件之一

1）所有的特征值大于0，即 $\lambda_{i} > 0$

2）所有的主元大于0

3）对任意的非零 x 满足

$$
\mathrm{x^{T} A x > 0}
$$

如果特征值中，有一部分是 0，另一部分是正数，这样的矩阵成为半正定（positive semi-definite）矩阵，例如

$$
\mathrm{A} =
\begin{bmatrix}
2 & 6 \\
6 & 18
\end{bmatrix}
$$

两个特征值

$$
\lambda_{1} = 0, \lambda_{2} = 20
$$

举一个正定矩阵的例子

$$
\mathrm{A} =
\begin{bmatrix}
2 & 6 \\
6 & 19
\end{bmatrix}
$$

按照定义，对于任意的非零 x

$$
\begin{aligned}
\mathrm{x^{T} A x} &=
\begin{bmatrix}
\mathrm{x_{1}} & \mathrm{x_{2}}
\end{bmatrix}
\begin{bmatrix}
2 & 6 \\
6 & 19
\end{bmatrix}
\begin{bmatrix}
\mathrm{x_{1}} \\
\mathrm{x_{2}}
\end{bmatrix} \\
&= \mathrm{2 x_{1}^{2} + 12 x_{1}x_{1} + 19x_{2}^{2}} \\
&= \mathrm{2(x_{1} + 3x_{2})^{2} + x_{2}^{2}} > 0
\end{aligned}
$$

注意到二次型的两个系数正好是矩阵的两个主元

$$
\begin{bmatrix}
2 & 6 \\
6 & 19
\end{bmatrix} =
\begin{bmatrix}
2 & 6 \\
0 & 1
\end{bmatrix}
$$

如果 A 和 B 都是正定矩阵且维度一致，那么 A + B 也是正定的，因为

$$
\mathrm{x^{T} (A + B) x = x^{T}Ax + x^{T}Bx > 0}
$$

正定性串联了最小二乘法，考虑一个矩阵 $\mathrm{A_{m \times n}}$，但是我们可以做一个转化

$$
\mathrm{B = A^{T}A}
$$

我们关心 B 是不是一个正定矩阵或者半正定

$$
\mathrm{x^{T}Bx = x^{T}A^{T}Ax = (Ax)^{T}Ax = |Ax|^{2} \geq 0}
$$

只有当 Ax = 0 时，上式的等号才成立，也就是 A 的零空间只有零向量时，即 A 的秩为 n。


## 相似矩阵
当 A 的列向量线性无关时，A 可以被对角化

$$
\mathbf{S^{-1} A S = \Lambda}
$$

如果存在一个可逆矩阵 M，使得

$$
\mathbf{B = M^{-1} A M}
$$

此时，我们说 A 相似于 B。注意到 A 和 B 的维度时一致的。例如

$$
\mathrm{A} =
\begin{bmatrix}
2 & 1 \\
1 & 2
\end{bmatrix}
$$

一个可逆矩阵

$$
\mathrm{M} =
\begin{bmatrix}
1 & 4 \\
0 & 1
\end{bmatrix}
$$

于是

$$
\mathrm{M^{-1} A M} =
\begin{bmatrix}
1 & -4 \\
0 & 1
\end{bmatrix}
\begin{bmatrix}
2 & 1 \\
1 & 2
\end{bmatrix}
\begin{bmatrix}
1 & 4 \\
0 & 1
\end{bmatrix} =
\begin{bmatrix}
-2 & -15 \\
1 & 6
\end{bmatrix} = \mathrm{B}
$$

**相似矩阵特征值相同，特征矩阵不一定相同**。证明如下

$$
\begin{matrix}
\mathrm{A x = \lambda x} \\
\Downarrow \\
\mathrm{A M M^{-1} x = \lambda  M M^{-1} x} \\
\Downarrow \\
\mathrm{M^{-1} A M M^{-1} x = \lambda M^{-1} M M^{-1} x} \\
\Downarrow \\
\mathrm{B M^{-1} x = \lambda M^{-1} x} \\
\Downarrow \\
\mathrm{B y = \lambda y}
\end{matrix}
$$

因此 A 和 B 具有相同的特征值，特征向量的关系是

$$
\mathrm{y = M^{-1} x}
$$

如果矩阵 A 的有重复特征值，那么特征向量可能是线性相关的，进而 A 不能被对角化。以 2 阶矩阵为例，特征量

$$
\mathrm{\lambda_{1} = \lambda_{2} = 4}
$$

符合这个条件的有两类，第一类是对角矩阵，形如

$$
\mathrm{M^{-1}}
\begin{bmatrix}
4 & 0 \\
0 & 4
\end{bmatrix}
\mathrm{M} =
\begin{bmatrix}
4 & 0 \\
0 & 4
\end{bmatrix}
$$

第二类是若尔当标准型（Jordan form），最接近对角矩阵的最简洁形式

$$
\begin{bmatrix}
4 & 1 \\
0 & 4
\end{bmatrix}
$$

我们可以写出若干矩阵符合上述条件：行列式为 16，迹为 8

$$
\begin{bmatrix}
4 & 1 \\
0 & 4
\end{bmatrix},
\begin{bmatrix}
5 & 1 \\
-1 & 3
\end{bmatrix},
\begin{bmatrix}
4 & 0 \\
17 & 4
\end{bmatrix},\cdots
$$

这些向量都可以找到对应的 M 转化成若尔当标准型。举一个更极端的例子

$$
\begin{bmatrix}
0 & 1 & 7 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 \\
0 & 0 & 0 & 0
\end{bmatrix}
$$

特征值

$$
\mathrm{\lambda_{1} = \lambda_{2} = \lambda_{3} = \lambda_[4] = 0}
$$

因为秩为 2，所以零空间的维度

$$
\mathrm{\dim N(A) = 2}
$$

转化成若尔当标准型，共有两个若尔当块

$$
\left[\begin{array} {ccc:c}
0 & 1 & 0 & 0 \\
0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 \\ \hline
0 & 0 & 0 & 0
\end{array} \right]
$$

每一个若尔当块对角元素是相同的（特征值），每一个特征值上方设置为 1（除第一行），形如

$$
\mathrm{J_{i}} =
\begin{bmatrix}
\lambda_{i} & 1 & & & & \\
& \lambda_{i} & 1 & & & \\
& & \lambda_{i} & \ddots & & \\
& & & \ddots & 1 & \\
& & & & \lambda_{i} & 1 \\
& & & & & \lambda_{i}
\end{bmatrix}
$$

任意的方阵 A 可以转化成若尔当标准型 J。J 将若尔当标准型放到对角线上，并且若尔当块的个数等于 A 的特征向量的个数。

$$
\mathrm{J} =
\begin{bmatrix}
\boxed{\mathrm{J_{1}}} & & & \\
& \boxed{\mathrm{J_{2}}} & & \\
& & \ddots & \\
& & & \boxed{\mathrm{J_{d}}}
\end{bmatrix}
$$

例如

$$
\begin{bmatrix}
0 & 1 & 0 & 0 \\
0 & 0 & 0 & 0 \\
0 & 0 & 0 & 1 \\
0 & 0 & 0 & 0
\end{bmatrix} \Rightarrow
\left[\begin{array} {cc:cc}
0 & 1 & & \\
& 0 & & \\ \hline
& & 0 & 1 \\
& & & 0
\end{array} \right]
$$


## 分解
### LU 分解
假设 A 时可逆的，那么

$$
\mathbf{A^{-1}A = I}
$$

对两端进行转置

$$
\mathbf{(AA^{-1})^{T} = (A^{-1})^{T}A^{T} = I^{T} = I}
$$

我们可以得出一个结论，A 的转置的逆矩阵等价于 A 的逆矩阵的转置

$$
\mathbf{(A^{T})^{-1} = (A^{-1})^{T}}
$$

前面说到消元的本质是行向量的线性变换，对矩阵 A 左乘一个初等变换矩阵得到 U

$$
\begin{matrix}
\begin{bmatrix}
1 & 0 \\
-4 & 1
\end{bmatrix} &
\begin{bmatrix}
2 & 1 \\
8 & 7
\end{bmatrix} & = &
\begin{bmatrix}
2 & 1 \\
0 & 3
\end{bmatrix} \\
\mathbf{E} & \mathbf{A} & = & \mathbf{U}
\end{matrix}
$$

我们将 E 挪到等式的右侧，便可以得到 A 的分解。

$$
\begin{matrix}
\begin{bmatrix}
2 & 1 \\
8 & 7
\end{bmatrix} & = &
\begin{bmatrix}
1 & 0 \\
4 & 1
\end{bmatrix} &
\begin{bmatrix}
2 & 1 \\
0 & 3
\end{bmatrix} & = &
\begin{bmatrix}
1 & 0 \\
4 & 1
\end{bmatrix} &
\begin{bmatrix}
2 & 0 \\
0 & 3
\end{bmatrix} &
\begin{bmatrix}
1 & \frac{1}{2} \\
0 & 1
\end{bmatrix} \\
\mathrm{A} & = & \mathrm{L} & \mathrm{U} & = & \mathrm{L} & \mathrm{D} & \mathrm{U}
\end{matrix}
$$

L 代表下三角（lower triangle），U 代表上三角（upper triangle），D 代表对角（diagonal）。如果我们考虑行交换，那么应该写成 PA = LU。

### SVD
SVD（singular value decomposition）是奇异值分解的简称，对于任意的矩阵 A 可以分解成成两个正交矩阵和一个对角矩阵，表达如下

$$
\mathbf{A = U \Sigma V^{T}}
$$

如果 A 是实对称矩阵时，SVD 退化成如下形式

$$
\mathbf{A = Q \Lambda Q^{T}}
$$

更一般地情况，A 是可对角化的

$$
\mathbf{A = S \Lambda S^{-1}}
$$

对于一个矩阵 $\mathrm{A_{n \times m}}$ 而言，有列空间 $\mathrm{R^{m}}$ 和行空间 $\mathrm{R^{n}}$。SVD 的想法是在行空间中找到一组基变换成列空间的一组基。例如，行空间的一组基为

$$
\mathrm{v_{1}, v_{2}, \cdots, v_{r}}
$$

列空间的一组基为

$$
\mathrm{u_{1}, u_{2}, \cdots, u_{r}}
$$

我们希望 A 经过行向量的基，变成列向量的基

$$
\begin{matrix}
\mathrm{A v_{1} = \sigma_{1} u_{1}} \\
\mathrm{A v_{2} = \sigma_{2} u_{2}} \\
\vdots \\
\mathrm{A v_{r} = \sigma_{r} u_{r}}
\end{matrix}
$$

即为

$$
\begin{aligned}
\mathrm{A}
\begin{bmatrix}
\mathrm{v_{1}} & \mathrm{v_{2}} & \cdots & \mathrm{v_{r}}
\end{bmatrix} &=
\begin{bmatrix}
\mathrm{u_{1} \sigma_{1}} & \mathrm{u_{2} \sigma_{2}} & \cdots & \mathrm{v_{r} \sigma_{r}}
\end{bmatrix} \\
&= \begin{bmatrix}
\mathrm{u_{1}} & \mathrm{u_{2}} & \cdots & \mathrm{u_{r}}
\end{bmatrix}
\begin{bmatrix}
\sigma_{1} & & & \\
& \sigma_{2} & & \\
& & \ddots & \\
& & & \sigma_{r} \\
\end{bmatrix}
\end{aligned}
$$

最终的形式为

$$
\begin{matrix}
\mathrm{A V = U \Sigma} \\
\Downarrow \\
\mathrm{A = U \Sigma V^{-1} = U \Sigma V^{T}}
\end{matrix}
$$

如果我们想消除 U

$$
\begin{aligned}
\mathrm{A^{T} A} &= \mathrm{(U \Sigma V^{T})^{T} U \Sigma V^{T}} \\
&= \mathrm{V \Sigma U^{T} U \Sigma V^{T}} \\
&= \mathrm{V \Sigma^{2} V^{T}}
\end{aligned}
$$

同样地，消除 V

$$
\begin{aligned}
\mathrm{A A^{T}} &= \mathrm{U \Sigma V^{T} (U \Sigma V^{T})^{T}} \\
&= \mathrm{U \Sigma V^{T} V \Sigma U^{T}} \\
&= \mathrm{U \Sigma^{2} U^{T}}
\end{aligned}
$$

因此，如果想求出 U 和 V 只需要计算对应的对称矩阵的特征向量。例如

$$
\mathrm{A} =
\begin{bmatrix}
4 & 4 \\
-3 & 3
\end{bmatrix}
$$

$$
\mathrm{A^{T}A} =
\begin{bmatrix}
4 & -3 \\
4 & 3
\end{bmatrix}
\begin{bmatrix}
4 & 4 \\
-3 & 3
\end{bmatrix} =
\begin{bmatrix}
25 & 7 \\
7 & 25
\end{bmatrix}
$$

$$
\mathrm{A A^{T}} =
\begin{bmatrix}
4 & 4 \\
-3 & 3
\end{bmatrix}
\begin{bmatrix}
4 & -3 \\
4 & 3
\end{bmatrix} =
\begin{bmatrix}
32 & 0 \\
0 & 18
\end{bmatrix}
$$

特征值

$$
\Sigma^{2} =
\begin{bmatrix}
32 & 0 \\
0 & 18
\end{bmatrix}
$$

特征向量（归一化）

$$
\mathrm{U} =
\begin{bmatrix}
1 & 0 \\
0 & 1
\end{bmatrix}
$$

$$
\mathrm{V} = \frac{\sqrt{2}}{2}
\begin{bmatrix}
1 & 1 \\
1 & -1
\end{bmatrix}
$$

最后代回验证

$$
\begin{aligned}
\mathrm{A} &= \mathrm{U \Sigma V^{T}} \\
&=
\begin{bmatrix}
1 & 0 \\
0 & 1
\end{bmatrix}
\begin{bmatrix}
\sqrt{32} & 0 \\
0 & \sqrt{18}
\end{bmatrix}
\frac{\sqrt{2}}{2}
\begin{bmatrix}
1 & 1 \\
1 & -1
\end{bmatrix} \\
&= \begin{bmatrix}
4 & 4 \\
3 & -3
\end{bmatrix} \\
& \neq \begin{bmatrix}
4 & 4 \\
-3 & 3
\end{bmatrix}
\end{aligned}
$$

这里出错了，原因是我们算出来正价矩阵 V 对应的特征值是平方状态

$$
\Sigma^{2} =
\begin{bmatrix}
32 & 0 \\
0 & 18
\end{bmatrix}
$$

我们取根号时默认了为正值。正确的方法是通过下式确定对角矩阵元素的正负号

$$
\mathrm{A V = \Sigma U}
$$

$$
\begin{bmatrix}
4 & 4 \\
-3 & 3
\end{bmatrix}
\frac{\sqrt{2}}{2}
\begin{bmatrix}
1 & 1 \\
1 & -1
\end{bmatrix}
=
\begin{bmatrix}
\sqrt{32} & 0 \\
0 & -\sqrt{18}
\end{bmatrix}
\begin{bmatrix}
1 & 0 \\
0 & 1
\end{bmatrix}
$$

一般情况下，我们会选取对角元素为正值。

```python
from scipy.linalg import svd
import numpy as np

a = [[4, 4,],[-3, 3]]
A = np.array(a)
U, s, Vh = svd(A)
print(U)
print(s)
print(Vh)
'''
[[-1. 0.]
[ 0. 1.]]
[5.65685425 4.24264069]
[[-0.70710678 -0.70710678]
[-0.70710678 0.70710678]]
'''
```


## 应用
### 图
图（Graph）是一种包含节点和边的数据结构

$$
\mathbf{G(V, E)}
$$

例如，考虑一个图有 4 个节点，5 条边

$$
\begin{matrix}
1 & \stackrel{\mathrm{d}}{\rightarrow} & 4 \\
\downarrow{\mathrm{a}} & \stackrel{\mathrm{c}}{\searrow} & \uparrow{\mathrm{e}} \\
2 & \stackrel{\mathrm{b}}{\rightarrow} & 3
\end{matrix}
$$

关联矩阵（Incidence Matrix）是一个矩阵，每一行代表一条边，每一列代表图上的一个节点。

$$
\mathrm{A} =
\begin{bmatrix}
-1 & 1 & 0 & 0 \\
0 & -1 & 1 & 0 \\
-1 & 0 & 1 & 0 \\
-1 & 0 & 0 & 1 \\
0 & 0 & -1 & 1
\end{bmatrix}
$$

边 a、b 和 c 构成了一个环（loop），因此前三行是线性相关的。从列向量的角度，只有 3 列是线性无关的，那么零空间是 1 纬。

$$
\mathrm{Ax} =
\begin{bmatrix}
-1 & 1 & 0 & 0 \\
0 & -1 & 1 & 0 \\
-1 & 0 & 1 & 0 \\
-1 & 0 & 0 & 1 \\
0 & 0 & -1 & 1
\end{bmatrix}
\begin{bmatrix}
\mathrm{x_{1}} \\
\mathrm{x_{2}} \\
\mathrm{x_{3}} \\
\mathrm{x_{4}}
\end{bmatrix} =
\begin{bmatrix}
\mathrm{x_{2} - x_{1}} \\
\mathrm{x_{3} - x_{2}} \\
\mathrm{x_{3} - x_{1}} \\
\mathrm{x_{4} - x_{3}}
\end{bmatrix} = 0
$$

零空间的解

$$
\begin{bmatrix}
1 \\
1 \\
1 \\
1
\end{bmatrix}
$$

如果把 x 看作电势，那么 Ax 就是所有边连接的节点之间的电势差。电势差产生电流

$$
\mathrm{A^{T} y} =
\begin{bmatrix}
-1 & 0 & -1 & -1 & 0 \\
1 & -1 & 0 & 0 & 0 \\
0 & 1 & 1 & 0 & -1 \\
0 & 0 & 0 & 1 & 1
\end{bmatrix}
\begin{bmatrix}
\mathrm{y_{1}} \\
\mathrm{y_{2}} \\
\mathrm{y_{3}} \\
\mathrm{y_{4}} \\
\mathrm{y_{5}} 
\end{bmatrix} = 0
$$

y 表示边上的电流，这就是基尔霍夫电流定理（Kirchoff's current law）。KCL 定理描述了电流在某一节点的输入和输出是相等。


A 的左零空间的维度是

$$
\mathrm{\dim N(A^{T}) = m - r = 5 - 3 = 2}
$$

那么左零空间的解

$$
\begin{bmatrix}
1 \\
1 \\
-1 \\
0 \\
0
\end{bmatrix},
\begin{bmatrix}
0 \\
0 \\
1 \\
-1 \\
1
\end{bmatrix}
$$

这两个解刚好对应图上的两个环。相关性来源于环，如果没有环，那么 A 的各列是线性无关的。没有环的图叫做树（tree）。环数、边数和节点数满足欧拉公式（Euler‘s formula）。

$$
\mathrm{\#nodes - \#edges + \#loops = 1}
$$

### 马尔可夫矩阵
马尔可夫矩阵（Markov Matrix）表示状态转移概率，满足两个性质

1） 所有的元素（entry）必须大于等于 0

2）每一列元素的和为 1

形如

$$
\mathrm{A} =
\begin{bmatrix}
0.1 & 0.01 & 0.3 \\
0.2 & 0.99 & 0.3 \\
0.7 & 0 & 0.4
\end{bmatrix}
$$

性质 2）导致了 A 的特征值其中一个是 1，这样会达到稳态，其他的特征值绝对值均小于 1。A 平移一个单位

$$
\mathbf{A - I} =
\begin{bmatrix}
-0.9 & 0.01 & 0.3 \\
0.2 & -0.01 & 0.3 \\
0.7 & 0 & -0.6
\end{bmatrix}
$$

可知，每一列的和为 0，说明行向量是相关的，那么 A - I 是奇异的

$$
\mathrm{\det(A - I) = 0}
$$

本质上，马尔可夫矩阵符合矩阵幂指数，形如

$$
\begin{aligned}
\mathrm{u_{k}} &= \mathrm{A^{k} u_{0}} \\
&= \mathrm{c_{1} \lambda_{1}^{k} x_{1} + c_{2} \lambda_{2}^{k} x_{2} + \cdots + c_{n} \lambda_{n}^{k} x_{n}} \\
&\approx \mathrm{c_{1} x_{1}}
\end{aligned}
$$

因为只有一个特征值等于 1，所以随着 k 趋向于无穷，解会达到一个稳态。一个人口迁移的例子，加州和麻省两个地方的人口相互迁移

$$
\begin{matrix}
\begin{bmatrix}
\mathrm{u_{cal}} \\
\mathrm{u_{mass}}
\end{bmatrix}_{\mathrm{k+1}} & = &
\begin{bmatrix}
0.9 & 0.2 \\
0.1 & 0.8
\end{bmatrix} &
\begin{bmatrix}
\mathrm{u_{cal}} \\
\mathrm{u_{mass}}
\end{bmatrix}_{\mathrm{k}} \\
\mathbf{u_{k + 1}} & = & \mathbf{A} & \mathbf{u_{k + 1}}
\end{matrix}
$$

初始条件

$$
\begin{bmatrix}
\mathrm{u_{cal}} \\
\mathrm{u_{mass}}
\end{bmatrix} =
\begin{bmatrix}
0 \\
1000
\end{bmatrix}
$$

可求特征值和特征向量

$$
\begin{cases}
\lambda_{1} = 1 \\
\lambda_{2} = 0.7
\end{cases}
$$

$$
\mathrm{x_{1}} =
\begin{bmatrix}
2 \\
1
\end{bmatrix},
\mathrm{x_{2}} =
\begin{bmatrix}
-1 \\
1
\end{bmatrix}
$$

最终解

$$
\mathrm{u_{k} = \frac{1000}{3} 1^{k}}
\begin{bmatrix}
2 \\
1
\end{bmatrix} + \mathrm{\frac{2000}{3} 0.7^{k}}
\begin{bmatrix}
-1 \\
1
\end{bmatrix}
$$

### 傅立叶级数
考虑一组正交基 $\mathrm{q_{1}, q_{2}, \cdots, q_{n}}$，对于任意的向量 V

$$
\mathrm{V = x_{1} q_{1} + x_{2} q_{2} + \cdots + x_{n} q_{n}}
$$

如何知道 $\mathrm{x_{1}}$，可以通过 $\mathrm{V}$ 与 $\mathrm{q_{1}}$ 内积获得

$$
\mathrm{q_{1}^{T} V = x_{1} q_{1}^{T} q_{1} + x_{2} q_{1}^{T} q_{2} + \cdots + x_{n} q_{1}^{T} q_{n} = x_{1}}
$$

对于所有的 x

$$
\begin{matrix}
\begin{bmatrix}
| & | & \cdots & | \\
\mathrm{q_{1}} & \mathrm{q_{2}} & \cdots & \mathrm{q_{n}} \\
| & | & \cdots & |
\end{bmatrix} &
\begin{bmatrix}
\mathrm{x_{1}} \\
\mathrm{x_{2}} \\
\vdots \\
\mathrm{x_{n}}
\end{bmatrix} & = & \mathrm{V} \\
\mathbf{Q} & \mathbf{X} & = & \mathbf{V}
\end{matrix}
$$

因为 Q 是正交矩阵，所以可逆

$$
\mathbf{X = Q^{-1} V = Q^{T} V}
$$

傅立叶级数（Fourier series）：对于一个函数 f(x) 可以表示成一连串的正余弦函数之和。

$$
\begin{aligned}
\mathrm{f(x)} &= \mathrm{a_{0} + a_{1} \cos x + b_{1} \sin x + a_{2} \cos 2x + b_{2} \sin 2x + \cdots} \\
&+ \mathrm{a_{n} \cos nx + b_{n} \sin nx + \cdots}
\end{aligned}
$$

傅立叶级数是无穷维的，但是正余弦是正交的，可以看成一组基。两个函数的内积如下

$$
\mathrm{f^{T} g = \int f(x) g(x) dx}
$$

同样地，傅立叶级数的某一项系数的求解

$$
\begin{aligned}
\int_{0}^{2\pi} \mathrm{f(x) \cos x dx} &= \mathrm{a_{1} \int_{0}^{2\pi} (\cos x)^{2} dx} \\
&= \mathrm{a_{1} \int_{0}^{2\pi} \frac{1 + \cos 2x}{2} dx} \\
&= \mathrm{a}_{1} \pi
\end{aligned}
$$

所以

$$
\mathrm{a_{1} = \frac{1}{\pi} \int_{0}^{2\pi} f(x) \cos x dx}
$$

这里解释一下，为什么除了 $\mathrm{a_{1} \cos x}$，其他项都是 0。利用积化和差公式转化成周期函数

$$
\begin{aligned}
& \cos(\alpha) \sin(\beta) = \frac{\sin(\alpha + \beta) - \sin(\alpha - \beta)}{2} \\
& \sin(\alpha) \cos(\beta) = \frac{\sin(\alpha + \beta) + \sin(\alpha - \beta)}{2} \\
& \cos(\alpha) \cos(\beta) = \frac{\cos(\alpha - \beta) + \cos(\alpha + \beta)}{2} \\
& \sin(\alpha) \sin(\beta) = \frac{\cos(\alpha - \beta) - \cos(\alpha + \beta)}{2}
\end{aligned}
$$

$$
\mathrm{\int_{0}^{2\pi} a_{k} \cos(kx) \cos x = a_{k} \int_{0}^{2\pi} \frac{\cos(kx - x) + \cos(kx + x)}{2} = 0}
$$

$$
\mathrm{\int_{0}^{2\pi} a_{k} \sin(kx) \cos x = b_{k} \int_{0}^{2\pi} \frac{\sin(kx + x) + \sin(kx - x)}{2} = 0}
$$

### 图像有损压缩
一张图片可以看成一个矩阵，我们希望对这张图片进行压损，实际上就是对矩阵换基。一个好的基需要通常是相互正交的，例如傅立叶矩阵或者小波基（wavelets basis）。一组 8 阶的小波基如下

$$
\begin{bmatrix}
1 \\
1 \\
1 \\
1 \\
1 \\
1 \\
1 \\
1
\end{bmatrix},
\begin{bmatrix}
1 \\
1 \\
1 \\
1 \\
-1 \\
-1 \\
-1 \\
-1
\end{bmatrix},
\begin{bmatrix}
1 \\
1 \\
-1 \\
-1 \\
0 \\
0 \\
0 \\
0
\end{bmatrix},
\begin{bmatrix}
0 \\
0 \\
0 \\
0 \\
1 \\
1 \\
1 \\
1
\end{bmatrix},
\begin{bmatrix}
1 \\
-1 \\
0 \\
0 \\
0 \\
0 \\
0 \\
0
\end{bmatrix},
\begin{bmatrix}
0 \\
0 \\
1 \\
-1 \\
0 \\
0 \\
0 \\
0
\end{bmatrix},
\begin{bmatrix}
0 \\
0 \\
0 \\
0 \\
1 \\
-1 \\
0 \\
0
\end{bmatrix},
\begin{bmatrix}
0 \\
0 \\
0 \\
0 \\
0 \\
0 \\
1 \\
-1
\end{bmatrix}
$$

一张图片可以用一组向量来表示 P

$$
\mathrm{P} =
\begin{bmatrix}
\mathrm{P_{1}} \\
\mathrm{P_{2}} \\
\mathrm{P_{3}} \\
\mathrm{P_{4}} \\
\mathrm{P_{5}} \\
\mathrm{P_{6}} \\
\mathrm{P_{7}} \\
\mathrm{P_{8}}
\end{bmatrix}
$$

基于小波基，求出系数

$$
\begin{matrix}
\mathrm{P = W C} \\
\Downarrow \\
\mathrm{C = W^{-1} P}
\end{matrix}
$$

一个好的基可以快速求出基的逆矩阵，所以正交基是合适的，因为

$$
\mathrm{W^{-1} = W^{T}}
$$

求基的系数这一步是无损的。真正发生有损的步骤是根据阈值省略一部分系数。
