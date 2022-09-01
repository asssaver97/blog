---
title: 【LeetCode刷题笔记】剑指OfferII101.分割等和子集
date: 2022-03-01 11:20:12
tags: 刷题
categories: LeetCode
---
题目
---
[**剑指 Offer II 101. 分割等和子集**](https://leetcode-cn.com/problems/NUPfPr/)

给定一个非空的正整数数组`nums`，请判断能否将这些数字分成元素和相等的两部分。

示例 1：
```
输入：nums = [1,5,11,5]
输出：true
解释：nums 可以分割成 [1, 5, 5] 和 [11] 。
```
示例 2：
```
输入：nums = [1,2,3,5]
输出：false
解释：nums 不可以分为和相等的两部分
```
<!--more-->

题解
---
化成0-1背包问题！
先计算数组的总和，则数组的元素如果能达到总和的一半，则返回true。
dp[i][j]表示前i个元素能不能正好合成数字j。动态规划四要素：
1. 初始状态：`dp[0][0]=true; while(j!=0) dp[0][j]=false; while(i!=0) dp[i][0]=false;` 很好理解，只有空数组和零可以互相匹配，否则空数组满足不了任何数，零也匹配不了任何非空正整数数组。
2. 终止状态：`dp[n][sum/2]`
3. 状态转移：只有两个选择，当前元素`i`要不要加入组合，如果加入，则`dp[i][j]=dp[i-1][j-nums[i]]`，否则，`dp[i][j]=dp[i-1][j]`。取**或**。
4. 转移方向：从上往下，从左往右

**优化：状态压缩**
可以看出，`dp[i][j]`都是通过上一行`dp[i-1][...]`转移过来的，这意味着可以进行压缩。
压缩时需要注意，应该从右往左遍历，否则会覆盖。

代码
---
```cpp
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int n = nums.size(), sum = 0;
        for (int num : nums) {
            sum += num;
        }
        if (sum % 2) return false;
        sum /= 2;
        vector<bool> dp(sum+1, false);
        dp[0] = true;
        for (int i = 1; i <=n; ++i) {
            for (int j = sum; j > 0; --j) {
                if (nums[i-1] <= j) dp[j] = dp[j] | dp[j-nums[i-1]];
            }
        }
        return dp[sum];
    }
};
```
