---
title: 【LeetCode刷题笔记】剑指Offer07.重建二叉树
date: 2022-01-24 17:02:55
tags: 算法
categories: 刷题笔记
---
题目
---
[**剑指Offer 07. 重建二叉树**](https://leetcode-cn.com/problems/zhong-jian-er-cha-shu-lcof/)

输入某二叉树的前序遍历和中序遍历的结果，请构建该二叉树并返回其根节点。

假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

示例 1:
```
Input: preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]
Output: [3,9,20,null,null,15,7]
```
示例 2:
```
Input: preorder = [-1], inorder = [-1]
Output: [-1]
```

限制：
* 0 <= 节点个数 <= 5000
<!--more-->

题解
---
> * 前序遍历：根结点 ---> 左子树 ---> 右子树
> * 中序遍历：左子树---> 根结点 ---> 右子树
> * 后序遍历：左子树 ---> 右子树 ---> 根结点
> * 层次遍历：只需按层次遍历即可

**找到前序遍历数组`preorder`和中序遍历数组`inorder`的对应关系，就可以迭代。**
易见`preorder[0]`代表着树顶，那么`inorder`中对应的`preorder[0]`的左边所有元素都是左子树，右边的所有元素都是右子树。
记`inorder`中`preorder[0]`对应的左边有 N 个元素，那么`inorder[1]`~`inorder[N]`之间的元素也是左子树，`inorder[N]`之后的元素为右子树。
将对应的子序列作为新的参数进行迭代，即可得到完整二叉树。

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
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (!preorder.size()) return NULL;
        int head = preorder[0];
        TreeNode* x = new TreeNode(head);
        int count = 0;
        for (int num : inorder) {
            if (num == head) break;
            ++count;
        }
        vector<int> new_left_preorder(preorder.begin()+1, preorder.begin()+count+1);
        vector<int> new_right_preorder(preorder.begin()+count+1, preorder.end());
        vector<int> new_left_inorder(inorder.begin(), inorder.begin()+count);
        vector<int> new_right_inorder(inorder.begin()+count+1, inorder.end());
        x -> left = buildTree(new_left_preorder, new_left_inorder);
        x -> right = buildTree(new_right_preorder, new_right_inorder);
        return x;
    }
};
```
