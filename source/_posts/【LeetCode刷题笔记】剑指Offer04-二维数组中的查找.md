---
title: 【LeetCode刷题笔记】剑指Offer04.二维数组中的查找
date: 2022-01-24 15:53:08
tags: 算法
categories: 刷题笔记
---
题目
---
[**剑指Offer 04. 二维数组中的查找**](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)

在一个`n * m`的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个高效的函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

示例:
```
现有矩阵 matrix 如下：

[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
给定 target = 5，返回 true。

给定 target = 20，返回 false。
```

限制：
* `0 <= n <= 1000`
* `0 <= m <= 1000`
<!--more-->

题解
---
**选对查找的起点很重要！！！**
因为数组从左往右递增、从上往下递增的特性，每当访问一个元素，我们可以排除数组中的部分元素。
**我们选定右上角作为起点，**有以下三种情况：
* 如果当前元素等于目标值，返回`true`；
* 如果当前元素大于目标值，那么它下面的元素都大于目标值，索引往左移一位；
* 如果当前元素小于目标值，那么它左边的元素都小于目标值，索引往下移一位。

代码
---
```cpp
class Solution {
public:
    bool findNumberIn2DArray(vector<vector<int>>& matrix, int target) {
        if (!matrix.size()) return 0;
        int n = matrix.size(), m = matrix[0].size();
        int i = 0, j = m - 1;
        while (i >= 0 && j >= 0 && i < n && j < m) {
            if (matrix[i][j] == target) return true;
            if (matrix[i][j] < target) i++;
            else j--;
        }
        return false;
    }
};
```
