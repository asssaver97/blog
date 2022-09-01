---
title: 【LeetCode刷题笔记】1792.最大平均通过率
date: 2022-04-05 23:29:01
tags: 刷题
categories: LeetCode
mathjax: true
---
题目
---
[**1792. 最大平均通过率**](https://leetcode-cn.com/problems/maximum-average-pass-ratio/)

一所学校里有一些班级，每个班级里有一些学生，现在每个班都会进行一场期末考试。给你一个二维数组 classes ，其中 classes[i] = [passi, totali] ，表示你提前知道了第 i 个班级总共有 totali 个学生，其中只有 passi 个学生可以通过考试。

给你一个整数 extraStudents ，表示额外有 extraStudents 个聪明的学生，他们 一定 能通过任何班级的期末考。你需要给这 extraStudents 个学生每人都安排一个班级，使得 所有 班级的 平均 通过率 最大 。

一个班级的 通过率 等于这个班级通过考试的学生人数除以这个班级的总人数。平均通过率 是所有班级的通过率之和除以班级数目。

请你返回在安排这 extraStudents 个学生去对应班级后的 最大 平均通过率。与标准答案误差范围在 10-5 以内的结果都会视为正确结果。
<!--more-->

示例 1：

```
输入：classes = [[1,2],[3,5],[2,2]], extraStudents = 2
输出：0.78333
解释：你可以将额外的两个学生都安排到第一个班级，平均通过率为 (3/4 + 3/5 + 2/2) / 3 = 0.78333 。
```
示例 2：

```
输入：classes = [[2,4],[3,9],[4,5],[2,10]], extraStudents = 4
输出：0.53485
```

提示：

* 1 <= classes.length <= 105
* classes[i].length == 2
* 1 <= passi <= totali <= 105
* 1 <= extraStudents <= 105

题解
---
**优先队列！！！**

显然，将优秀学生安排到哪一个班级是根据通过率增加量的多少来排序的，也就是

$$\frac{x+1}{y+1} - \frac{x}{y}$$

其中，x、y 分别是班级能通过考试的人数和班级总人数。

那么，我们可以用 priority_queue 来维护一个最大堆。小细节是，可以用**匿名函数**来优化代码效率。

代码
---
```cpp
class Solution {
public:
    double maxAverageRatio(vector<vector<int>>& classes, int extraStudents) {
        priority_queue<tuple<double, int, int>> q;

        auto diff = [](int x, int y) {
            double res = (double)(x+1)/(y+1) - (double)x/y;
            return res;
        };
        
        double res = 0;
        for (vector<int>& c : classes) {
            int x = c[0], y = c[1];
            double d = diff(x, y);
            res += (double) x/y;
            q.emplace(d, x, y);
        }

        while (extraStudents > 0) {
            --extraStudents;
            auto [d, x, y] = q.top();
            res += d;
            d = diff(x+1, y+1);
            q.pop();
            q.emplace(d, x+1, y+1);
        }

        return res/classes.size();
    }
};
```