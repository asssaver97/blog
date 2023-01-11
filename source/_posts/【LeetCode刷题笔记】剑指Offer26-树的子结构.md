---
title: 【LeetCode刷题笔记】剑指Offer26.树的子结构
date: 2022-01-28 10:36:20
tags: 算法
categories: 刷题笔记
---
题目
---
[**剑指Offer 26. 树的子结构**](https://leetcode-cn.com/problems/shu-de-zi-jie-gou-lcof/)

输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)

B是A的子结构， 即 A中有出现和B相同的结构和节点值。

例如:
给定的树 A:
```
     3
    / \
   4   5
  / \
 1   2
```
给定的树 B：
```
   4 
  /
 1
```
返回 `true`，因为`B`与`A`的一个子树拥有相同的结构和节点值。

示例 1：
```
输入：A = [1,2,3], B = [3,1]
输出：false
```
示例 2：
```
输入：A = [3,4,5,1,2], B = [4,1]
输出：true
```

限制：
* `0 <= 节点个数 <= 10000`
<!--more-->

题解
---
**需要两个函数，第一个函数用来遍历A的节点，第二个函数用来判断B是否是A的子树。**
第二个函数需要进行递归操作，判断A和B当前的节点相不相同：如果相同，则继续递归，对比左右字节点；如果不同，则返回false。
**为什么不能只用一个函数囊括遍历和递归？**
因为只有一个函数的话无法判断B的当前节点是不是头节点，就无法确定是继续遍历还是返回false。

代码
---
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSubStructure(TreeNode* A, TreeNode* B) {
        if (A == nullptr || B == nullptr) return false;
        return (recur(A, B) || isSubStructure(A->left, B) || isSubStructure(A->right, B));
    }

    bool recur(TreeNode* A, TreeNode* B) {
        if (B == nullptr) return true;
        if (A == nullptr || A->val!=B->val) return false;
        return recur(A->left, B->left) && recur(A->right, B->right);
    }
};
```
