## 简介
单调队列是一种特殊的队列，特点是队列内的元素相对有序。

## 和至少为 K 的最短子数组
给定一个数组 nums = [2, -1, 2] 和一个整数 k = 3，求子数组的和大于等于 k 的最小长度是多少？

**「分析」**

【前缀和 + 单调队列】假设子数组的头索引为 i，尾索引是 j，那么对于固定的 j，我们希望在满足 presum[ j ] - presum[ i ] >= k 时的最大 i，于是，我们发现，i 和 j 是有序的，并且 presum[ i ] 和 presum[ j ] 也是有序的。因此，我们希望借助一种数据结构存储 i。需要满足：1）给定一个 j，当确定 i 满足连续和的要求时，将 i 移除，此时对于大于 j 的索引 k，满足 presum[ k ] - presum[ i ] >= k 时，k - i < j - i 一定成立；2）将 j 加入到这个数据结构中，需要保证有序。这样的结构是单调队列

```cpp
int shortestSubarray(vector<int>& nums, int k) {
    int n = (int)nums.size(), ans = n + 1;
    vector<long> presum(n + 1, 0);
    for (int i = 0; i < n; i++) {
        presum[i + 1] = presum[i] + nums[i];
    }
    deque<int> dq;
    for (int i = 0; i < presum.size(); i++) {
        while (!dq.empty() && presum[i] - presum[dq.front()] >= k) {
            ans = min(ans, i - dq.front());
            dq.pop_front();
        }
        while (!dq.empty() && presum[i] < presum[dq.back()]) {
            dq.pop_back();
        }
        dq.push_back(i);
    }
    return ans == n + 1 ? -1 : ans;
}
```
时间复杂度：$O(n)$，空间复杂度：$O(n)$
