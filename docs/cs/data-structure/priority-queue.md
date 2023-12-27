## 简介
优先队列（priority queue）是一种按照优先级（call-by-priority）来访问元素的数据结构，通常以数组的形式，这里的优先级可以指最大值、最小值等等。


## 二叉堆
满二叉树（Complete Binary Tree）是一种特殊的自平衡树（AVL），其左子树不短于右子树。逻辑上，满二叉树堆（complete binary heap）等价于满二叉树；物理上，满二叉堆等价于数组。假设一个元素的秩为 i（数组下标），那么其父节点（如果有）的秩为 $\frac{i-1}{2}$，左孩孑节点的秩为 2i+1，右孩子节点的秩为 2i+2。

$$
\begin{matrix}
& & & & & & & & & 0 \\
& & & & & & & \swarrow & & & & \searrow \\
& & & & & & 1 & & & & & & 2 \\
& & & & & \swarrow & & \searrow & & & & & \\
& & & & 3 & & & & 4 & & & &
\end{matrix}
$$

**大顶堆**：顶点之外的所有节点值小于父节点 $H[i] \leq H[parent(i)]$，顶点是整个堆的最大值。**小顶堆**的定义与之相对应。

### 插入
这里以大顶堆为例。堆以数组的形式存储元素，如果我们想插入一个新元素 e，只需要将其放在在队尾。这个时候需要检查堆内结构，如果 e 小于其父节点，无需调整；否则，我们需要交换 e 和其父节点。但这样会引入新的问题：新的父节点有可能依然小于 e。对此，我们继续交换两者的位置，直到满足堆序要求。我们将这一操作成为上滤（percolate up），这里无需每次都交换，只需要找到最后一个位置即可。时间复杂度为 $O(\log n)$。

### 删除
当我们准备删除堆顶元素时，将其与数组的最后一个元素交换位置，然后丢弃。检查此时的根结点元素是否满足堆序要求。如果不满足，与它的较大的那个子节点（如果有）交换位置。持续这一操作，直到满足堆序要求。同样地，我们只需要找到最后一个字节点，交换一次即可。这一操作成为小滤（percolate down），时间复杂度 $O(\log n)$。

### 建堆化
给定一组数据，我们需要对其建堆（heapification）。

#### 自顶向下
对代建堆的数据，逐一插入堆中，采用上滤的方法调整堆内结构。最坏的情况时间复杂度 $O(n \log n)$，

```cpp
void percolateUp(vector<int> &arr, int idx) {
    int n = int(arr.size());
    if (idx < 0 || idx >= n) { return; }
    while (idx > 0) {
        int parIdx = (idx - 1) / 2;
        if (arr[parIdx] > arr[idx]) {
            swap(arr[parIdx], arr[idx]);
            idx = parIdx;
        } else {
            return;
        }
    }
}

void heapify(vector<int> &arr) {
    int n = int(arr.size());
    for (int i = 1; i < n; i++) {
        percolateUp(arr, i);
    }
}
```

#### 自底向上
对满二叉树而言，叶子结点占所有节点的一半，且优先出现在树的左侧。因此，我们只需要对数组内一半的节点采用下滤操作即可建堆。最后的节点位置 $\left \lfloor \frac{n}{2} \right \rfloor - 1$。最坏情况下的时间复杂度 $\sum_{i} height(i) = O(n)$。

```cpp
void percolateDown(vector<int> &arr, int idx) {
    int n = int(arr.size());
    if (idx < 0 || idx >= n) { return; }
    while (idx < n) {
        int leftIdx = idx * 2 + 1, rightIdx = idx * 2 + 2;
        if (leftIdx >= n) { break; }
        if (rightIdx >= n) { rightIdx = leftIdx; }
        if (arr[idx] < min(arr[leftIdx], arr[rightIdx])) { return; }
        if (arr[leftIdx] < arr[rightIdx]) {
            swap(arr[idx], arr[leftIdx]);
            idx = leftIdx;
        } else {
            swap(arr[idx], arr[rightIdx]);
            idx = rightIdx;
        }
    }
}

void heapify(vector<int> &arr) {
    int n = int(arr.size());
    for (int i = n-1; i >= 0; i--) {
        percolateDown(arr, i);
    }
}
```

## 左式堆
左式堆（leftist heap）具备二叉堆的性质之外，还用来高效合并两个堆 A 和 B。如果我们想合并两个堆，一个简单的方法是 A.insert(B.removeMax)，需要 $O(m \log(m+n))$ 的时间复杂度。另一种方法是用 Floid Heapification 的方法，即先 union(A, B) 再 heapify(A+B)，需要 $O(n+m)$ 的时间复杂度。左式堆可以进一步降低时间复杂度 $O(\log n)$。堆内的节点尽可能落在左侧，在右侧合并。

### 零路长度
我们假设所有的外部节点都是空节点，那么定义零路径长度（Null Path Length）

$$
NPL(x) =
\begin{cases}
0, & \text{x is null} \\
1 + min(NPL(lc(x)), NPL(rc(x))), & \text{otherwise}
\end{cases}
$$

以上述的图例而言，0 号节点的 NPL 为 3。

这里，lc(x) 是 x 的左孩子，rc(x) 是 x 的右孩子。实际上，如果我们把上述的 min 改成 max，那么我们会得到一棵树高度的定义。NPL(x) 表达了 x 到外部节点的最短距离。

### 左式堆性质
对任何一个节点，左孩子节点的 NPL 不小于右孩子节点的 NPL，即 $NPL(lc(x)) \geq NPL(rc(x))$

某一节点的 NPL 等于左右孩子节点的 NPL 较小值再加一。$NPL(x) = 1 + NPL(rc(x))$

左式堆的子堆也是一个左式堆。

### 右链
这里定义**右链（Right Chain）**，从某一个节点 x 开始，沿着右孩子节点一路向下直到空节点。右链的最末端节点一定是空节点，并且有最小的NPL。

$$
NPL(r) = \left | rChain(r) \right | = d
$$

一个右链长为 d 的左式堆一定包含了一个高度为 d 的完整二叉树。并且，这个左式堆一定含有 $2^{d+1}-1$ 个节点，其中 $2^{d}-1$ 个内部节点。换言之，如果一个左式堆有 n 个节点，那么

$$
d \leq \left \lfloor log_{2}(n+1) \right \rfloor - 1 = O(logn)
$$

### 合并
因为左式堆打破了常规堆的结构，所以我们用二叉树替代数组来实现左式堆。假设我们有 2 个左式堆 A 和 B（A > B），取 A 的右子堆，递归地与 B 合并，然后将合并后的结果当作 A 的右子堆。如果 A 的左子堆的 NPL 小于新的子堆的 NPL，交换两者。

```cpp
template <typename T>
static BinNodePosi(T) merge(BinNodePosi(T) a, BinNodePosi(T) b) {
	if (!a) return b;
	if (!b) return a;
	if (a->data <  b->data) swap(a, b);
	a->rc = merge(a->rc, b);
	a->rc->parent = a;
	if (!a->lc || a->lc->npl < a->rc->npl) swap(a->lc, a->rc);
	a->npl = a->rc?a->rc->npl+1:1;
	return a;
}
```

### 插入
堆新增一个元素，调整堆的结构。

```cpp
void PQ_LeftHeap<T>::insert (T e) {
	BinNodePosi(T) v = new BinNode<T>(e);
	_root = merge(_root, v);
	_root->parent = NULL;
	_size++;
}
```

### 删除堆顶
同样地，当我们删除堆顶的元素后，需要重新调整堆内的顺序。

```cpp
void PQ_LeftHeap<T>::delMax () {
	BinNodePosi(T) lHeap = _root->lc;
	BinNodePosi(T) rHeap = _root->rc;
	T e = _root->data;
	delete _root;
	_size--;
	_root = merge(lHeap, rHeap);
	if (_root) _root->parent = NULL;
	return e;
}
```
时间复杂度：$O(n \log n)$。


## 题目
### 会议室的个数
给定一个会议时间列表 intervals = [[1, 13], [13, 15], [6, 9]]，intervals[ i ] 表示一个会议的开始结束时间，试问为了避免会议冲突，至少需要准备多少个会议室？

**「分析」**

【优先队列】将 intervals 按照会议开始时间排序，逐个判断当前会议是否与之前的会议有冲突，如果都有冲突，那么会议室加一。利用【优先队列】优化上述算法，因为我们判断一个当前会议是否需要单独开一个会议室，只需要检查，当前会议的开始时间 t1 与之前所有会议的最早结束时间 t2 做对比。如果 t1 仍高于 t2，那么当前会议与之前的都有冲突；反之，我们将当前会议替换最早结束的会议。最后优先队列中存在会议个数就是需要开设的会议室个数。

```cpp
int minMeetingRooms(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());
    priority_queue<int, vector<int>, greater<int>> pq;
    for (vector<int> interval : intervals) {
        if (!pq.empty() && pq.top() <= interval[0]) {
            pq.pop();
        }
        pq.push(interval[1]);
    }
    return (int)pq.size();
}
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(n)$

### 第 K 个最小的素数分数
给定一个有序数组 arr = [1, 2, 3, 5] 和一个整数 k = 3，arr 中的任意两个数组成一个小于 1 分数，求第 k 小的那个分数。

**「分析」**

【优先队列】每一个位置 j (0 < j < n) 的前方有 j 个数，这些数字与 j 组成的分数大小是单调递增的，我们可以借鉴【合并有序链表】的方法，将每一个 j 视作一个单调链表，利用最小堆求得答案。

```cpp
vector<int> kthSmallestPrimeFraction(vector<int>& arr, int k) {
    int n = (int)arr.size();
    vector<int> ans(2, 0);
    if (n == 0) {
        return {};
    }
    typedef pair<int, int> PII;
    
    struct cmp {
        bool operator()(PII &a, PII &b) {
            return a.first * b.second > a.second * b.first;
        }
    };
    
    priority_queue<PII, vector<PII>, cmp> pq;
    
    unordered_map<int, int> idx;
    idx[arr[0]] = 0;
    for (int i = 1; i < n; i++) {
        idx[arr[i]] = i;
        pq.emplace(PII (arr[0], arr[i]));
    }
    while (k--) {
        int a  = pq.top().first, b = pq.top().second;
        pq.pop();
        if (idx[a] < idx[b] - 1) {
            pq.emplace(PII (arr[idx[a] + 1], b));
        }
        ans = vector<int> {a, b};
    }
    return ans;
}
```
时间复杂度：$O(k \log n)$，空间复杂度：$O(n)$，其中 n 是 arr 的长度

### 拆分数组为连续子序列
给你一个按升序排序的整数数组 num（可能包含重复数字），请你将它们分割成一个或多个长度至少为 3 的子序列，其中每个子序列都由连续整数组成。如果可以完成上述分割，则返回 true ；否则，返回 false 。

**「分析」**

【哈希 + 优先队列】每当遇到一个值 num，如果不存在以 (num - 1) 结尾的子序列 [a1, a2, ...]，那么需要新建一个以 num 开头的子序列。因为 num 可能是重复的，e.g. 1 1 2 2 3 3，因此，我们优先将 num 添加到所有子序列 [a1, a2, ...] 中最短的那个。

```cpp
bool isPossible(vector<int>& nums) {
    if (nums.size() < 3) {
        return false;
    }
    typedef priority_queue<int, vector<int>, greater<int>> pq;
    unordered_map<int, pq> M;
    for (int num : nums) {
        if (M.count(num - 1)) {
            int len = M[num - 1].top();
            M[num - 1].pop();
            if (M[num - 1].empty()) {
                M.erase(num - 1);
            }
            M[num].push(len + 1);
        } else {
            M[num].push(1);
        }
    }
    for (auto it = M.begin(); it != M.end(); it++) {
        if (!it->second.empty() && it->second.top() < 3) {
            return false;
        }
    }
    return true;
}
```
时间复杂度：$O(n \log n)$，空间复杂度：$O(n)$

### 有序矩阵中的第 k 个最小数组和
给你一个 m * n 的矩阵 mat，以及一个整数 k ，矩阵中的每一行都以非递减的顺序排列。你可以从每一行中选出 1 个元素形成一个数组。返回所有可能数组中的第 k 个 最小 数组和。例如，矩阵如下，k = 7

$$
\begin{bmatrix}
1 & 10 & 10 \\
1 & 4 & 5 \\
2 & 3 & 6
\end{bmatrix}
$$

**「分析」**

如果每一行选择一个元素，那么一共有 $m^{n}$ 种可能，很难在规定时间内枚举完。注意到题目求的是第k小的组合，在这个例子里，k = 7，mat的前两行组合就有9个方案 s1, s2, ..., s9，只需要将第三行的首位加入到这9个和上，就能知道第7小的值。那么我们可以按照行遍历，每次需要将已经组合的topK，与当前行的元素两两相加，再筛选出topK小的组合。

```cpp
int kthSmallest(vector<vector<int>>& mat, int k) {
    if (mat.empty() || mat[0].empty()) {
        return 0;
    }
    vector<int> nums;
    for (int j = 0; j < mat[0].size() && j < k; j++) {
        nums.emplace_back(mat[0][j]);
    }
    for (int i = 1; i < mat.size(); i++) {
        priority_queue<int, vector<int>, greater<int>> pq;
        for (int num : nums) {
            for (int j = 0; j < mat[i].size(); j++) {
                pq.push(num + mat[i][j]);
            }
        }
        nums.clear();
        for (int j = 0; j < k && !pq.empty(); j++) {
            nums.emplace_back(pq.top());
            pq.pop();
        }
    }
    sort(nums.begin(), nums.end());
    return nums.back();
}
```
时间复杂度 $O(mkn)$，空间复杂度 $O(kn)$。

### 滑动窗口最大值
给你一个整数数组 nums，有一个大小为 k 的滑动窗口从数组的最左侧移动到数组的最右侧。你只可以看到在滑动窗口内的 k 个数字。滑动窗口每次只向右移动一位。返回 滑动窗口中的最大值 。

**「分析」**

「优先队列」

```cpp
/*
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation: 
Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
*/
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    vector<int> ans;
    multiset<int> hash;
    int n = (int)nums.size();
    for (int i = 0; i < k; i++) {
        hash.insert(nums[i]);
    }
    for (int i = k; i < n; i++) {
        ans.emplace_back(*--hash.end());
        hash.erase(hash.find(nums[i-k]));
        hash.insert(nums[i]);
    }
    ans.emplace_back(*--hash.end());
    return ans;
}

vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    vector<int> ans;
    int n = (int)nums.size();
    priority_queue<pair<int, int>> pq;
    for (int i = 0; i < k; i++) {
        pq.emplace(nums[i], i);
    }
    for (int i = k; i <= n; i++) {
        while (pq.top().second + k < i) {
            pq.pop();
        }
        ans.emplace_back(pq.top().first);
        if (i < n) { pq.emplace(nums[i], i); }
    }
    return ans;
}
```
时间复杂度：$O(n \log k)$，空间复杂度：$O(n)$。

### 接雨水
给你一个 m x n 的矩阵，其中的值均为非负整数，代表二维高度图每个单元的高度，请计算图中形状最多能接多少体积的雨水。

**「分析」**

「优先队列」

```cpp
/*
给出如下 3x6 的高度图:
[
  [1,4,3,1,3,2],
  [3,2,1,3,2,4],
  [2,3,3,2,3,1]
]
返回 4 。
*/

int trapRainWater(vector< vector< int>>& heightMap) {
    int nRow = (int)heightMap.size();
    if (nRow == 0) { return 0; }
    int nCol = (int)heightMap[0].size();
    if (nCol < 3) { return 0; }
    typedef pair < int, pair< int, int>> PIII;
    priority_queue < PIII, vector < PIII>, greater< PIII>> heap;
    bool **vis = new bool *[nRow];
    for (int i = 0; i < nRow; i++) {
        vis[i] = new bool [nCol]{};
        heap.push({heightMap[i][0], {i, 0}});
        heap.push({heightMap[i][nCol-1], {i, nCol-1}});
        vis[i][0] = vis[i][nCol-1] = true;
    }
    for (int j = 0; j < nCol; j++) {
        heap.push({heightMap[0][j], {0, j}});
        heap.push({heightMap[nRow-1][j], {nRow-1, j}});
        vis[0][j] = vis[nRow-1][j] = true;
    }
    int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, -1, 0, 1};
    int ans = 0;
    while (!heap.empty()) {
        auto cur = heap.top();
        heap.pop();
        int x = cur.second.first, y = cur.second.second;
        int h = cur.first;
        for (int i = 0; i < 4; i++) {
            int xx = x + dx[i];
            int yy = y + dy[i];
            if (xx >= 0 && xx < nRow && yy >= 0 && yy < nCol &&
                !vis[xx][yy]) {
                vis[xx][yy] = true;
                ans += max(h - heightMap[xx][yy], 0);
                heap.push({max(h, heightMap[xx][yy]), {xx, yy}});
            }
        }
    }
    for (int i = 0; i < nRow; i++) {
        delete []vis[i];
    }
    delete []vis;
    return ans;
}
```
时间复杂度：$O(nm)$，空间复杂度：$O(nm)$。
