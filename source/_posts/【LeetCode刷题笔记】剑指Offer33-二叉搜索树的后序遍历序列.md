---
title: 【LeetCode刷题笔记】剑指Offer33.二叉搜索树的后序遍历序列
date: 2022-01-28 19:26:51
tags: 刷题
categories: LeetCode
---
题目
---
[**剑指Offer 33. 二叉搜索树的后序遍历序列**](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/)

输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回`true`，否则返回`false`。假设输入的数组的任意两个数字都互不相同。

参考以下这颗二叉搜索树：
```
     5
    / \
   2   6
  / \
 1   3
```
示例 1：
```
输入: [1,6,3,2,5]
输出: false
```
示例 2：
```
输入: [1,3,2,6,5]
输出: true
``` 

提示：
* 数组长度 <= 1000
<!--more-->

题解
---
**二叉搜索树的定义：**左子树中所有节点的值 < 根节点的值 < 右子树中所有节点的值。
**后序遍历：**左子树->右子树->根节点
根据定义，只要所有左子树和右子树都满足二叉搜索树，则该树为二叉搜索树，所以我们应该想到使用**递归&分治**判断所有子树的正确性。
令[l, r]表示当前树的范围，用m来划分左右子树，**m为第一个大于根节点的节点的索引。**具体地：
1. 遍历[l, r]，找到第一个大于tree[r]的节点tree[m]，将[l, m-1]、[m, r-1]分别作为下一次递归的参数；
2. 步骤1已经保证了左子树都小于根节点，所以我们只需要再遍历判断右子树是不是都大于根节点，如果不是则返回false；
3. 如果l >= r，表明当前树小于等于一个节点，则返回true。

代码
---
```cpp
class Solution {
public:
    bool verifyPostorder(vector<int>& postorder) {
        return recur(postorder, 0, postorder.size()-1);
    }

    bool recur(vector<int>& postorder, int l, int r) {
        if (l >= r) return true;
        int m = l;
        for (; m < r; ++m){
            if (postorder[m] > postorder[r]) break;
        }
        for (int i = m+1; i<r; ++i) {
            if (postorder[i] < postorder[r]) return false;
        }
        return recur(postorder, l, m-1) && recur(postorder, m, r-1);
    }
};
```
