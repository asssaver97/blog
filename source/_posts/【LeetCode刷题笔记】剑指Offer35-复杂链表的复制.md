---
title: 【LeetCode刷题笔记】剑指Offer35.复杂链表的复制
date: 2022-01-29 11:51:25
tags: 刷题
categories: LeetCode
---
题目
---
[**剑指Offer 35. 复杂链表的复制**](https://leetcode-cn.com/problems/fu-za-lian-biao-de-fu-zhi-lcof/)

请实现`copyRandomList`函数，复制一个复杂链表。在复杂链表中，每个节点除了有一个`next`指针指向下一个节点，还有一个`random`指针指向链表中的任意节点或者`null`。

示例 1：
```
输入：head = [[7,null],[13,0],[11,4],[10,2],[1,0]]
输出：[[7,null],[13,0],[11,4],[10,2],[1,0]]
```
示例 2：
```
输入：head = [[1,1],[2,1]]
输出：[[1,1],[2,1]]
```
示例 3：
```
输入：head = [[3,null],[3,0],[3,null]]
输出：[[3,null],[3,0],[3,null]]
```
示例 4：
```
输入：head = []
输出：[]
解释：给定的链表为空（空指针），因此返回 null。
```

提示：
* `-10000 <= Node.val <= 10000`
* `Node.random`为空（null）或指向链表中的节点。
* 节点数目不超过`1000`。
<!--more-->

题解一
---
**可以使用哈希表建立原链表与新链表的对照关系！**
循环两次，第一次循环建立创建哈希表和新链表各个节点的值，第二次循环根据哈希表建立各个节点之间的连接。

代码一
---
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/
class Solution {
public:
    Node* copyRandomList(Node* head) {
        Node* cur = head;
        unordered_map<Node*, Node*> dic;
        while (cur != nullptr) {
            Node* copyNode = new Node(cur->val);
            dic[cur] = copyNode;
            cur = cur->next;
        }
        cur = head;
        while (cur != nullptr) {
            dic[cur]->next = dic[cur->next];
            dic[cur]->random = dic[cur->random];
            cur = cur->next;
        }
        return dic[head];
    }
};
```

题解二
---
**拼接+拆分！！**
先按照原节点1->新节点1->原节点2->新节点2->...->原节点N->新节点N拼接，随后拆分即可。

代码二
---
```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* next;
    Node* random;
    
    Node(int _val) {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
*/
class Solution {
public:
    Node* copyRandomList(Node* head) {
        if (head == nullptr) return nullptr;
        Node* cur = head;
        while (cur != nullptr) {
            Node* copyNode = new Node(cur->val);
            copyNode->next = cur->next;
            cur->next = copyNode;
            cur = copyNode->next;
        }
        cur = head;
        while (cur != nullptr) {
            cur->next->random = cur->random == nullptr ? nullptr : cur->random->next;
            cur = cur->next->next;
        }
        Node* newHead = head->next;
        cur = head;
        while (cur != nullptr) {
            Node* temp = cur->next;
            cur->next = temp->next;
            if (temp->next != nullptr)
                temp->next = temp->next->next;
            cur = cur->next;
        }
        return newHead;
    }
};
```
