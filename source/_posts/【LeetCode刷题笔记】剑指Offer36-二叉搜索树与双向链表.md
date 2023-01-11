---
title: 【LeetCode刷题笔记】剑指Offer36.二叉搜索树与双向链表
date: 2022-01-30 10:20:59
tags: 算法
categories: 刷题笔记
---
题目
---
[**剑指Offer 36. 二叉搜索树与双向链表**](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/)

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的循环双向链表。要求不能创建任何新的节点，只能调整树中节点指针的指向。
<!--more-->

题解
---
**中序遍历的二叉搜索树就是递增序列！！**
创建两个函数，一个是主函数，负责处理特殊情况，另一个是辅函数，用于递归。具体地，先创建head和tail，分别表示序列的头和尾。中序遍历到的首节点，也就是二叉搜索树最左边的节点，即为最小值，将它赋值给head，同理，末节点赋值给tail。cur指针指向当前遍历的节点，pre指针指向上一个遍历的节点，初始值为nullptr。
由于遍历完成后，pre会指向最后一个节点，所以pre=tail。（省去创建tail）
主函数：
1. 如果`head == nullptr`，返回空；
2. 初始化pre和cur；
3. 链接head和tail。

辅函数：
1. 如果`cur == nullptr`，返回；
2. 链接pre和cur，若pre为空，则赋值head=cur。

代码
---
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;

    Node() {}

    Node(int _val) {
        val = _val;
        left = NULL;
        right = NULL;
    }

    Node(int _val, Node* _left, Node* _right) {
        val = _val;
        left = _left;
        right = _right;
    }
};
*/
class Solution {
    Node *head=nullptr, *pre=nullptr;
public:
    Node* treeToDoublyList(Node* root) {
        if (root==nullptr) return nullptr;
        dfs(root);
        head->left=pre;
        pre->right=head;
        return head;
    }

    void dfs(Node* cur) {
        if (cur==nullptr) return;
        dfs(cur->left);
        if (pre == nullptr) head = cur;
        else {
            cur->left=pre;
            pre->right=cur;
        }
        pre = cur;
        dfs(cur->right);
    }
};
```
