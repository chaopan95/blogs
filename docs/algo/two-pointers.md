## 相关题目
??? note "[「Leetcode 3. 无重复字符的最长子串」]()"

  ```cpp
  /*
  给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。

  示例 1:
  输入: s = "abcabcbb"
  输出: 3 
  解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
  */
  int lengthOfLongestSubstring(string s) {
      int n = int(s.length());
      if (n == 0) { return 0; }
      int i = 0, j = 0, maxLen = 0;
      unordered_set<char> hash;
      while (j < n) {
          if (hash.count(s[j])) { hash.erase(s[i++]); }
          else {
              hash.insert(s[j++]);
              maxLen = max(maxLen, j - i);
          }
      }
      return maxLen;
  }
  ```

??? note "[「Leetcode 15. 三数之和」]()"

  ```cpp
  /*
  给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素
  a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的
  三元组。

  注意：答案中不可以包含重复的三元组。
  */
  vector<vector<int>> threeSum(vector<int>& nums) {
      int n = int(nums.size());
      vector<vector<int>> ans;
      if (n < 3) { return ans; }
      sort(nums.begin(), nums.end());
      for (int i = 0; i < n - 2; i++) {
          int j = i + 1, k = n - 1;
          while (j < k) {
              int sum = nums[i] + nums[j] + nums[k];
              if (sum < 0) { j++; }
              else if (sum > 0) { k--; }
              else {
                  while (i < k && nums[i] == nums[i+1]) { i++; }
                  while (j < k && nums[j] == nums[j+1]) { j++; }
                  while (j < k && nums[k] == nums[k-1]) { k--; }
                  ans.push_back({nums[i], nums[j], nums[k]});
                  j++;
                  k--;
              }
          }
      }
      return ans;
  }
  ```

??? note "[「Leetcode 16. 最接近的三数之和」]()"

  ```cpp
  /*
  给定一个包括 n 个整数的数组 nums 和 一个目标值 target。找出 nums 中的三
  个整数，使得它们的和与 target 最接近。返回这三个数的和。假定每组输入只存在
  唯一答案。
  */
  int threeSumClosest(vector<int>& nums, int target) {
      int n = int(nums.size()), res = 0, diff = (1ll << 31) - 1;
      if (n < 3) { return 0; }
      sort(nums.begin(), nums.end());
      for (int i = 0; i <= n-3; i++) {
          int j = i + 1, k = n - 1;
          while (j < k) {
              int sum = nums[i] + nums[j] + nums[k];
              if (abs(sum - target) < diff)
              {
                  diff = abs(sum - target);
                  res = sum;
              }
              if (sum < target) { j++; }
              else if (sum > target) { k--; }
              else { return target; }
          }
      }
      return res;
  }
  ```
