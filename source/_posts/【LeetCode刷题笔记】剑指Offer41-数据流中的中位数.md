---
title: 【LeetCode刷题笔记】剑指Offer41.数据流中的中位数
date: 2022-01-31 14:25:42
tags: 算法
categories: 刷题笔记
mathjax: true
---
题目
---
[**剑指Offer 41. 数据流中的中位数**](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)

如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。

例如，

[2,3,4] 的中位数是 3

[2,3] 的中位数是 (2 + 3) / 2 = 2.5

设计一个支持以下两种操作的数据结构：
* void addNum(int num) - 从数据流中添加一个整数到数据结构中。
* double findMedian() - 返回目前所有元素的中位数。

示例 1：
```
输入：
["MedianFinder","addNum","addNum","findMedian","addNum","findMedian"]
[[],[1],[2],[],[3],[]]
输出：[null,null,null,1.50000,null,2.00000]
```
示例 2：
```
输入：
["MedianFinder","addNum","findMedian","addNum","findMedian"]
[[],[2],[],[3],[]]
输出：[null,null,2.00000,null,2.50000]
```

限制：
* 最多会对 addNum、findMedian 进行 50000 次调用。
<!--more-->

题解
---
**本题是想考察堆的运用，而不是简单排序**
堆的定义：堆是一个完全二叉树，每个节点与其子节点位置相对。父节点总是大于或等于子节点，这种情况下被叫作大顶堆，或者父节点总是小于或等于子节点，这种情况下叫作小顶堆。注意，给定父节点的子节点不一定按顺序排列。
make_heap和priority_queue的区别：（两者都是堆）
1. priority_queue可以自动保持元素的顺序，但缺点是只能访问第一个元素，且不能打乱priority_queue的有序状态；
2. make_heap可以访问堆中的任意元素，而不限于最大（或最小）的元素，因为元素被存储在一个容器中，就像是我们自己的vector。这也提供了偶然破坏元素顺序的可能，但是总可以调用`make_heap()`来还原堆。
3. make_heap可以在任何提供随机访问迭代器的序列容器中创建堆。这些序列容器包括普通数组、string 对象、自定义容器。这意味着无论什么时候需要，都可以用这些序列容器的元素创建堆，必要时，可以反复创建。甚至还可以为元素的子集创建堆。

对于本题，我们要维护一个大顶堆和一个小顶堆，大顶堆保存小于等于中位数的元素，小顶堆保存大于等于中位数的元素，那么中位数即为两个堆顶元素的组合，具体地：当数据流中有偶数个数时，中位数为 (大顶堆第一个元素的值 + 小顶堆第一个元素的值)/2；当数据流中有奇数个数时，中位数为大顶堆第一个元素的值。
我们令m表示大顶堆的大小，n表示小顶堆的大小，
$$
m=
\begin{cases}
n, &length为偶数 \\
n+1, &length为奇数
\end{cases}
$$
每次添加一个新的数时：当m==n时，应该先将新元素添加到小顶堆，再把小顶堆顶部的值加到大顶堆；当m==n+1时，先将新元素添加到大顶堆，再把大顶堆顶部的值加到小顶堆。

代码
---
```cpp
class MedianFinder {
public:
    /** initialize your data structure here. */
    priority_queue<int, vector<int>, less<int>> left; // 大顶堆
    priority_queue<int, vector<int>, greater<int>> right; // 小顶堆
    MedianFinder() {
    }
    
    void addNum(int num) {
        if (left.size() == right.size()) {
            right.push(num);
            left.push(right.top());
            right.pop();
        } else {
            left.push(num);
            right.push(left.top());
            left.pop();
        }
    }
    
    double findMedian() {
        if (left.size() == right.size()) return (left.top() + right.top()) / 2.0;
        else return (double)left.top();
    }
};

/**
 * Your MedianFinder object will be instantiated and called as such:
 * MedianFinder* obj = new MedianFinder();
 * obj->addNum(num);
 * double param_2 = obj->findMedian();
 */
```
