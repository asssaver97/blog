---
title: 【LeetCode刷题笔记】146.LRU缓存
date: 2022-02-19 11:54:24
tags: 算法
categories: 刷题笔记
---
题目
---
[**146.LRU缓存**](https://leetcode-cn.com/problems/lru-cache/)

请你设计并实现一个满足**LRU (最近最少使用) 缓存**约束的数据结构。
实现`LRUCache`类：
* `LRUCache(int capacity)`以**正整数**作为容量`capacity`初始化LRU 缓存
* `int get(int key)`如果关键字`key`存在于缓存中，则返回关键字的值，否则返回`-1`。
* `void put(int key, int value)`如果关键字`key`已经存在，则变更其数据值`value`；如果不存在，则向缓存中插入该组`key-value`。如果插入操作导致关键字数量超过`capacity`，则应该**逐出**最久未使用的关键字。
函数 get 和 put 必须以 O(1) 的平均时间复杂度运行。

示例：
```
输入
["LRUCache", "put", "put", "get", "put", "get", "put", "get", "get", "get"]
[[2], [1, 1], [2, 2], [1], [3, 3], [2], [4, 4], [1], [3], [4]]
输出
[null, null, null, 1, null, -1, null, -1, 3, 4]

解释
LRUCache lRUCache = new LRUCache(2);
lRUCache.put(1, 1); // 缓存是 {1=1}
lRUCache.put(2, 2); // 缓存是 {1=1, 2=2}
lRUCache.get(1);    // 返回 1
lRUCache.put(3, 3); // 该操作会使得关键字 2 作废，缓存是 {1=1, 3=3}
lRUCache.get(2);    // 返回 -1 (未找到)
lRUCache.put(4, 4); // 该操作会使得关键字 1 作废，缓存是 {4=4, 3=3}
lRUCache.get(1);    // 返回 -1 (未找到)
lRUCache.get(3);    // 返回 3
lRUCache.get(4);    // 返回 4
```

提示：
* `1 <= capacity <= 3000`
* `0 <= key <= 10000`
* `0 <= value <= 105`
* 最多调用 2 * 105 次`get`和`put`
<!--more-->

题解
---
**双向链表+map+虚拟头尾节点**
知识点：
1. LRU，即最近最少使用，当内存满了的时候，自动删除最久没有访问的数据
2. list也是双向链表！但是搜索元素时需要遍历，时间复杂度太高，所以本题选择自定义双向链表
3. 虚拟头尾可以省去很多边界条件的处理

注意事项：
1. 调换节点顺序时，要注意处理好前后节点新的指向关系，不要遗漏
2. 构建双向链表结构的时候，需要注意在构造函数内，将next和pre初始化为nullptr，否则在调用`node->next`时会出错
3. 注意边界条件，比如将最后一个节点调换到最后一个时（原地tp），如果按照寻常逻辑处理，会导致节点的next指针指向自己（这地方我卡了一下午！！！）

代码
---
```cpp
struct LinkedNode {
    int key;
    int value;
    LinkedNode *next;
    LinkedNode *prev;
    LinkedNode(int k, int v) {
        key=k;
        value=v;
        next=nullptr;
        prev=nullptr;
    }
    LinkedNode() {
        key=-1;
        value=-1;
        next=nullptr;
        prev=nullptr;
    }
};

class LRUCache {
private:
    LinkedNode *head;
    LinkedNode *tail;
    int cap;
    int size;
    unordered_map<int, LinkedNode*> des;

    void toTail(int key) {
        LinkedNode *cur = des[key];
        if (cur->next == tail) return;
        tail->prev->next = cur;
        cur->prev->next = cur->next;
        cur->next->prev = cur->prev;
        cur->prev = tail->prev;
        tail->prev = cur;
        cur->next = tail;
    }
public:
    LRUCache(int capacity) {
        head=new LinkedNode();
        tail=new LinkedNode();
        head->next=tail;
        tail->prev=head;
        cap=capacity;
        size=0;
    }
    
    int get(int key) {
        if (des.find(key)!=des.end()) {
            toTail(key);
            return des[key]->value;
        }
        return -1;
    }
    
    void put(int key, int value) {
        if (des.find(key)!=des.end()) {
            toTail(key);
            des[key]->value=value;
            return;
        }
        LinkedNode *cur = new LinkedNode(key, value);
        tail->prev->next = cur;
        cur->prev = tail->prev;
        tail->prev = cur;
        cur->next = tail;
        des.insert({key, cur});
        ++size;
        if (size > cap) {
            head = head->next;
            des.erase(head->key);
            --size;
        }
    }

};

/**
 * Your LRUCache object will be instantiated and called as such:
 * LRUCache* obj = new LRUCache(capacity);
 * int param_1 = obj->get(key);
 * obj->put(key,value);
 */
```
