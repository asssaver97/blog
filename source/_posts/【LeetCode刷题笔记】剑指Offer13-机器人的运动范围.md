---
title: 【LeetCode刷题笔记】剑指Offer13.机器人的运动范围
date: 2022-01-25 12:18:06
tags: 算法
categories: 刷题笔记
---
题目
---
[**剑指Offer 13. 机器人的运动范围**](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

地上有一个m行n列的方格，从坐标`[0,0]`到坐标`[m-1,n-1]`。一个机器人从坐标`[0, 0]`的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格`[35, 37]`，因为3+5+3+7=18。但它不能进入方格`[35, 38]`，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

示例 1：
```
输入：m = 2, n = 3, k = 1
输出：3
```
示例 2：
```
输入：m = 3, n = 1, k = 0
输出：1
```

提示：
* `1 <= n,m <= 100`
* `0 <= k <= 20`
<!--more-->

题解
---
**dfs+剪枝，用set记录节点，避免重复**
剪枝条件：1.当前节点已经经历过了 2.索引溢出 3.当前`i/10 + i%10+ j/10 + j%10 > k`，则不用再向左或向右遍历

代码
---
```cpp
class Solution {
    set<pair<int, int>> visited;
public:
    int movingCount(int m, int n, int k) {
        dfs(0, 0, m, n, k);
        set<pair<int, int>> temp = visited;
        return visited.size();
    }

private:
    void dfs(int i, int j, int m, int n, int k) {
        if (visited.find(make_pair(i, j)) != visited.end() || i < 0 || j < 0 || i == m || j == n || i/10 + i%10+ j/10 + j%10 > k)
            return;
        visited.insert(make_pair(i, j));
        dfs(i+1, j, m, n, k);
        dfs(i, j+1, m, n, k);
    }
};
```
