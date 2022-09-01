---
title: 【LeetCode刷题笔记】516.最长回文子序列
date: 2022-02-27 11:39:41
tags: 刷题
categories: LeetCode
---
题目
---
[**516.最长回文子序列**](https://leetcode-cn.com/problems/longest-palindromic-subsequence/)

给你一个字符串`s`，找出其中最长的回文子序列，并返回该序列的长度。

子序列定义为：不改变剩余字符顺序的情况下，删除某些字符或者不删除任何字符形成的一个序列。

示例 1：
```
输入：s = "bbbab"
输出：4
解释：一个可能的最长回文子序列为 "bbbb" 。
```
示例 2：
```
输入：s = "cbbd"
输出：2
解释：一个可能的最长回文子序列为 "bb" 。
```

提示：
* `1 <= s.length <= 1000`
* `s`仅由小写英文字母组成
<!--more-->

题解
---
对于最长子序列的问题，肯定考虑用动态规划。
动态规划主要分为一维dp数组和二维dp数组，对于二维dp数组，又要分两种情况：
1. 当涉及到两个字符串/数组时，dp[i][j]代表arr1[0...i]和arr2[0...j]；
2. 当只涉及一个字符串/数组时，dp[i][j]代表arr[i...j]

**本题使用二维dp数组。**

动态规划需要考虑下面四点：
1. 初始状态，即最简单的情况（base case）
> 对于本题，base case是对角线，即`i==j`，此时回文子序列长度必是1。
2. 终止状态，即返回值
> 对于本题，显然要返回str[0...n]的情况，即dp[0][n]
3. 状态转移
> 对于本题，当`str[i]==str[j]`时，显然`dp[i][j]=dp[i+1][j-1]`；而当`str[i]!=str[j]`时，说明这两个字符不可能同时出现在回文子序列中，**经观察**可以得出，`dp[i][j]=max(dp[i+1][j], dp[i][j-1])`。
4. 转移方向
> 对于本题，由于i不可能小于j，所以我们可以**从下往上，从左往右**遍历。

代码
---
```cpp
class Solution {
public:
    int longestPalindromeSubseq(string s) {
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n, 0));
        for (int i = 0; i < n; ++i) {
            dp[i][i] = 1;
        }
        for (int i = n-2; i >=0; --i) {
            for (int j = i+1; j < n; ++j) {
                if (s[i] == s[j]) {
                    dp[i][j] = dp[i+1][j-1] + 2;
                } else {
                    dp[i][j] = max(dp[i+1][j], dp[i][j-1]);
                }
            }
        }
        return dp[0][n-1];
    }
};
```
