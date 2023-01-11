---
title: 【LeetCode刷题笔记】222.完全二叉树的节点个数
date: 2022-03-03 11:17:59
tags: 算法
categories: 刷题笔记
mathjax: true
---
题目
---
[**222.完全二叉树的节点个数**](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

给你一棵 完全二叉树 的根节点 root ，求出该树的节点个数。

完全二叉树 的定义如下：在完全二叉树中，除了最底层节点可能没填满外，其余每层节点数都达到最大值，并且最下面一层的节点都集中在该层最左边的若干位置。若最底层为第 h 层，则该层包含 1~ 2h 个节点。
<!--more-->

题解
---
直接暴力dfs不难，但这肯定不是本题的考核目的，我们需要运用完全二叉树的特性来解此题。

首先，了解一下什么是满二叉树：
满二叉树的每层都是满的，假设它的高（层数）为$h$，那么它的节点数为$2^h-1$。

而对于完全二叉树来说，最底层最少只有一个节点，最大为满二叉树，所以节点树在$2^{h-1}~2^h-1$之间。

我们还需要用到**完全二叉树隐藏特性！！！**
**完全二叉树的左右子树中至少有一颗是满二叉树！**

因此，我们可以这样考虑问题，首先算出左右子树的层数，如果相等，那么直接返回$2^h-1$，否则，递归（每次递归必有一颗满二叉树）。

代码
---
```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int countNodes(TreeNode* root) {
        TreeNode* l = root, * r = root;
        int lc = 0, rc = 0;
        while (l != nullptr) {
            ++lc;
            l = l->left;
        }
        while (r != nullptr) {
            ++rc;
            r = r->right;
        }
        if (lc == rc) return pow(2, lc) -1;
        return 1 + countNodes(root->left) + countNodes(root->right);
    }
};
```
