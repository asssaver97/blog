---
title: 【LeetCode刷题笔记】95.不同的二叉搜索树
date: 2022-02-20 21:43:08
tags: 算法
categories: 刷题笔记
---
题目
---
[**95.不同程度的二叉搜索树**](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

给你一个整数`n`，请你生成并返回所有由`n`个节点组成且节点值从`1`到`n`互不相同的不同**二叉搜索树**。可以按**任意顺序**返回答案。

示例 1：
```
输入：n = 3
输出：[[1,null,2,null,3],[1,null,3,2],[2,1,3],[3,1,null,null,2],[3,2,null,1]]
```
示例 2：
```
输入：n = 1
输出：[[1]]
```

提示：
* `1 <= n <= 8`
<!--more-->

题解
---
对于二叉搜索树，就应该想到用他的性质解题：左子树 < 根节点 < 右子树。
比如对于递增区间[start, end]，若当前节点为i，则左子树范围是[start, i-1]，右子树范围是[i+1, end]。对于本题，我们也只需要输入start和end，并在start和end之间遍历当前节点并往下递归就好了。
递归结束条件：start > end，说明当前子树为空。

**注意：**当子数为空时，应该返回{nullptr}，而非{}，后者会导致输出始终为空。

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
    vector<TreeNode*> generateTrees(int n) {
        return generateTrees(1, n);
    }

    vector<TreeNode*> generateTrees(int start, int end) {
        if (start > end) return {nullptr};
        vector<TreeNode*> res;
        for (int i = start; i <= end; ++i) {
            vector<TreeNode*> left = generateTrees(start, i-1);
            vector<TreeNode*> right = generateTrees(i+1, end);
            for (int j = 0; j < left.size(); ++j) {
                for (int k = 0; k < right.size(); ++ k) {
                    TreeNode *cur = new TreeNode(i, left[j], right[k]);
                    res.push_back(cur);
                }
            }
        }
        return res;
    }
};
```

