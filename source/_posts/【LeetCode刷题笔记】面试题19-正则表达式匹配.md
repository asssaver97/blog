---
title: 【LeetCode刷题笔记】面试题19.正则表达式匹配
date: 2022-01-31 10:50:57
tags: 刷题
categories: LeetCode
mathjax: true
---
题目
---
[**面试题19. 正则表达式匹配**](https://leetcode-cn.com/problems/zheng-ze-biao-da-shi-pi-pei-lcof/)

请实现一个函数用来匹配包含'. '和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（含0次）。在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但与"aa.a"和"ab*a"均不匹配。

示例 1:
```
输入:
s = "aa"
p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。
```
示例 2:
```
输入:
s = "aa"
p = "a*"
输出: true
解释: 因为 '*' 代表可以匹配零个或多个前面的那一个元素, 在这里前面的元素就是 'a'。因此，字符串 "aa" 可被视为 'a' 重复了一次。
```
示例 3:
```
输入:
s = "ab"
p = ".*"
输出: true
解释: ".*" 表示可匹配零个或多个（'*'）任意字符（'.'）。
```
示例 4:
```
输入:
s = "aab"
p = "c*a*b"
输出: true
解释: 因为 '*' 表示零个或多个，这里 'c' 为 0 个, 'a' 被重复一次。因此可以匹配字符串 "aab"。
```
示例 5:
```
输入:
s = "mississippi"
p = "mis*is*p*."
输出: false
```
* `s`可能为空，且只包含从`a-z`的小写字母。
* `p`可能为空，且只包含从`a-z`的小写字母以及字符`.`和`*`，无连续的`'*'`。
<!--more-->

题解
---
**动态规划！**
我们用dp[i][j]记录s的前i个字符和p的前j个字符是否匹配，p的字符有三种情况：
1. 当p的第j个字符为字母时，转移规则如下：
$$dp[i][j]=
\begin{cases}
dp[i-1][j-1],& p[j]\neq s[i] \\
false,& p[j]=s[i]
\end{cases}
$$
2. 当p的第j个字符为'·'时，转移规则如下：
$$dp[i][j]=dp[i-1][j-1]$$
3. 当p的第j个字符为'*'时，转移规则如下：
$$dp[i][j]=
\begin{cases}
dp[i-1][j] or dp[i][j-2],& p[j-1]=s[i] or p[j-1]='·' \\
dp[i][j-2],& p[j-1]\neq s[i]
\end{cases}
$$
**注意！**需要先处理dp第一行，因为a*与空字符串匹配。

代码
---
```cpp
class Solution {
public:
    bool isMatch(string s, string p) {
        int m = s.size(), n = p.size();
        vector<vector<bool>> dp(m + 1, vector<bool>(n + 1, false));
        dp[0][0] = true;
        for (int i = 2; i < n + 1; i+=2) 
            dp[0][i] = dp[0][i-2] & (p[i-1] == '*');
        for (int i = 0; i < m; ++i) {
            for (int j = 0; j < n; ++ j) {
                if (p[j] == '.') {
                    dp[i+1][j+1] = dp[i][j];
                } else if (p[j] == '*') {
                    if (p[j-1] == '.' || p[j-1] == s[i]) {
                        dp[i+1][j+1] = dp[i+1][j-1] | dp[i][j+1];
                    } else {
                        dp[i+1][j+1] = dp[i+1][j-1];
                    }
                } else {
                    if (p[j] == s[i]) {
                        dp[i+1][j+1] = dp[i][j];
                    } else {
                        dp[i+1][j+1] = false;
                    }
                }
            }
        }
        return dp[m][n];
    }
};
```
