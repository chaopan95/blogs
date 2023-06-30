## 简介
优先队列是一种特殊的数据结构，通常以数组的形式，按照当前所有元素的一个特性，e.g. 最大值、最小值等等。


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
