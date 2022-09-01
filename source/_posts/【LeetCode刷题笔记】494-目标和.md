---
title: 【LeetCode刷题笔记】494.目标和
date: 2022-03-02 11:13:03
tags: 刷题
categories: LeetCode
mathjax: true
---
题目
---
[**494.目标和**](https://leetcode-cn.com/problems/target-sum/)

给你一个整数数组`nums`和一个整数`target`。

向数组中的每个整数前添加`'+'`或`'-'`，然后串联起所有整数，可以构造一个**表达式**：

例如，`nums = [2, 1]`，可以在`2`之前添加`'+'`，在 1 之前添加`'-'`，然后串联起来得到表达式`"+2-1"`。
返回可以通过上述方法构造的、运算结果等于`target`的不同 表达式 的数目。

示例 1：
```
输入：nums = [1,1,1,1,1], target = 3
输出：5
解释：一共有 5 种方法让最终目标和为 3 。
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
```
示例 2：
```
输入：nums = [1], target = 1
输出：1
```

提示：
* `1 <= nums.length <= 20`
* `0 <= nums[i] <= 1000`
* `0 <= sum(nums[i]) <= 1000`
* `-1000 <= target <= 1000`
<!--more-->

题解
---
本题最容易想到的是回溯，即暴力遍历，挨个尝试每个数的加减就好了。
但是会超时。
所以我们尝试动态规划。动态规划比起暴力算法的优点就是它消除了重叠子问题，因此时间复杂度会更低。
首先，我们要进行数学上的公式转换：
令A和B分别表示nums中取正和负的数的子集，那么：
$$\sum A-\sum B=target$$
$$\sum A=traget+\sum B$$
$$\sum A+\sum A=target+\sum B+\sum A$$
$$2*\sum A=target+\sum nums$$
$$\sum A=(target+\sum nums)/2$$
接下来就好办多了，公式右边是个定值，那么设定dp[i][j]为nums的前i个元素有多少个子集A满足和为j，动态规划要素如下：
1. 初始状态：`dp[0][0]=1`（空集也是子集），`dp[0][else]=0`。
2. 终止状态：`dp[n][(target+sum)/2]`
3. 状态转移：如果取当前元素加入子集A，则`dp[i][j]=dp[i-1][j-nums[i]]`；否则，`dp[i][j]=dp[i-1]`。故`dp[i][j]=dp[i-1][j-nums[i]]+dp[i-1][j]`。
4. 转移方向：从上往下，从左往右。

优化：状态压缩

代码
---
```cpp
class Solution {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int n = nums.size(), sum = 0;
        for (int num : nums) sum += num;
        target = target + sum;
        if (target % 2 || target < 0) return 0;
        target = target /2;
        vector<int> dp(target+1, 0);
        dp[0] = 1;
        for (int i = 1; i <= n; ++i) {
            for (int j = target; j >= 0; --j) {
                if (j - nums[i-1] >= 0) {
                    dp[j] = dp[j-nums[i-1]] + dp[j];
                } else {
                    dp[j] = dp[j];
                }
            }
        }
        return dp[target];
    }
};
```
