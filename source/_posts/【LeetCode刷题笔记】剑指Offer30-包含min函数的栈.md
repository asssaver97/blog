---
title: 【LeetCode刷题笔记】剑指Offer30.包含min函数的栈
date: 2022-01-28 12:18:36
tags: 算法
categories: 刷题笔记
---
题目
---
[**剑指Offer 30. 包含min函数的栈**](https://leetcode-cn.com/problems/bao-han-minhan-shu-de-zhan-lcof/)

定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的`min`函数在该栈中，调用`min`、`push`及`pop`的时间复杂度都是`O(1)`。

示例:
```
MinStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.min();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.min();   --> 返回 -2.
```

提示：
* 各函数的调用总次数不超过`20000`次
<!--more-->

题解
---
**如果只是维护一个min变量，不能确定`pop()`以后是否会改变，因此得想办法将min与每次push的数结合，组成pair！**

代码
---
```cpp
class MinStack {
public:
    /** initialize your data structure here. */
    int minN = INT_MAX;
    stack<pair<int, int>> res;
    MinStack() {

    }
    
    void push(int x) {
        if (x < minN) minN = x;
        res.push(make_pair(x, minN));
    }
    
    void pop() {
        res.pop();
        if (!res.empty()) minN = res.top().second;
        else minN = INT_MAX;
    }
    
    int top() {
        return res.top().first;
    }
    
    int min() {
        return minN;
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->min();
 */
```
