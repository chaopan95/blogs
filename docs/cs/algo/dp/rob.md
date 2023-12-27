## 成排
给定一个非负整数数组表示房屋内的金额，一个小偷可以偷取若干房屋内的所有金钱，但是偷窃的房屋不能相邻，求可以获得最大金额数目。

**「分析」**

设 dp[ i ][ 0 ] 表示第 i 个房屋没有被盗窃，dp[ i ][ 1 ] 为第 i 个房屋被盗窃

$$
\begin{aligned}
& \text{dp[ i ][ 0 ]} = \max(\text{dp[i - 1][ 0 ], dp[i - 1][ 1 ]}) \\
& \text{dp[ i ][ 1 ]} = \text{dp[i - 1][ 0 ] + A[ i ]}
\end{aligned}
$$

```cpp
int dp0 = 0, dp1 = nums[0];
for (int i = 1; i < n; i++) {
    int _dp0 = max(dp0, dp1);
    dp1 = dp0 + nums[i];
    dp0 = _dp0;
}
return max(dp0, dp1);
```
时间复杂度 $O(n)$，空间复杂度 $O(1)$

## 成环
同样一个非负数组表示房屋内的金额，但是数组的首尾是相连的，求此时的最大金额

**「分析」**

数组成环的状态下，首尾元素至少有一个不会被盗窃，那么我们可以将分成两种情况：（1）不选择首元素，（2）不选择尾元素。由此，将一个成环问题转换成两个成排问题，各自的解法如上。

```cpp
int dp0 = 0, dp1 = nums[0];
for (int i = 1; i < n - 1; i++) {
    int _dp0 = max(dp0, dp1);
    int _dp1 = dp0 + nums[i];
    dp0 = _dp0;
    dp1 = _dp1;
}
int ans = max(dp0, dp1);
dp0 = 0;
dp1 = nums[1];
for (int i = 2; i < n; i++) {
    int _dp0 = max(dp0, dp1);
    int _dp1 = dp0 + nums[i];
    dp0 = _dp0;
    dp1 = _dp1;
}
ans = max(ans, max(dp0, dp1));
return ans;
```
时间复杂度 $O(n)$，空间复杂度 $O(1)$

## 成树
这次的房屋排列成一颗二叉树，相邻的房屋不能同时被盗窃，求最大金额。

$$
\begin{matrix}
& & & & & 3 \\
& & & & & \wedge \\
& & & 4 & & & & & 5 \\
& & & \wedge & & & & & \wedge \\
& & 1 & & 3 & & & 2 & & 1
\end{matrix}
$$

最大金额是 10。

**「分析」**

构成一颗二叉树的情况下，当前节点只与左右孩子相关联（自底向上遍历），设 f[ root ] 为当前节点不盗窃， t[ root ] 为当前节点被盗窃，其状态转移方程如下

$$
\begin{aligned}
& \text{t[ root ] = } \max(\text{f[ root} \rightarrow \text{right ], f[ root} \rightarrow \text{left ]}) + \text{root} \rightarrow \text{val} \\
& 
\begin{aligned}
\text{f[ root ] = } & \max(\text{f[ root } \rightarrow \text{ left ], t[ root } \rightarrow \text{ left ]}) \\
& + \max(\text{f[ root } \rightarrow \text{ right ], t[ root } \rightarrow \text{ right ]})
\end{aligned}
\end{aligned}
$$

```cpp
unordered_map<TreeNode*, int> t, f;
int rob(TreeNode* root) {
    if (root == nullptr) { return 0; }
    rob(root->left);
    rob(root->right);
    t[root] = f[root->left] + f[root->right] + root->val;
    f[root] = max(f[root->left], t[root->left]) +
              max(f[root->right], t[root->right]);
    return max(t[root], f[root]);
}
```
时间复杂度 $O(n)$，空间复杂度 $O(n)$