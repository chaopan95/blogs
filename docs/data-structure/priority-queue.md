## 简介
优先队列是一种特殊的数据结构，通常以数组的形式，按照当前所有元素的一个特性，e.g. 最大值、最小值等等。

## 会议室的个数
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
