---
title: 【LeetCode刷题笔记】剑指Offer12.矩阵中的路径
date: 2022-01-25 10:11:51
tags: 刷题
categories: LeetCode
---
题目
---
[**剑指Offer 12. 矩阵中的路径**](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

给定一个`m x n`二维字符网格`board`和一个字符串单词`word`。如果`word`存在于网格中，返回`true`；否则，返回`false`。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

示例 1：
```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```
示例 2：
```
输入：board = [["a","b"],["c","d"]], word = "abcd"
输出：false
```
 
提示：
* `1 <= board.length <= 200`
* `1 <= board[i].length <= 200`
* `board`和`word`仅由大小写英文字母组成
<!--more-->

题解
---
**深度优先搜索+剪枝**
* 深度优先搜索(depth-first seach，DFS)在搜索到一个新的节点时，立即对该新节点进行遍历;因此遍历需要用先入后出的栈来实现，也可以通过与栈等价的递归来实现。对于树结构而言，由于总是对新节点调用遍历，因此看起来是向着“深”的方向前进。
* 剪枝顾名思义，就是删去一些不重要的节点，来减小计算或搜索的复杂度。

0. 令i,j表示二维数组board的索引，k表示字符串word的索引，对于已经在路径中的元素，将其置空，即`borad[i][j]='\0'`；
1. 设定初始值：遍历二维数组，并将i、j、k=0传入`dfs()`函数；
2. 回溯：如果`k==word.size()`，即**字符串全部匹配完**，则`return true`；如果`board[i][j]!=word[k]`，即**字符不匹配**，则`return false`；如果`board[i][j]=='\0'`，即**当前位置已经在路径中**，则`return false`；如果**索引溢出**，则`return false`；如果是其他情况，则进入第三步；
3. 递归：进入这一步，代表`board[i][j]==word[k]`，那么令`borad[i][j]='\0'`，`++k`，board向上、下、左、右改变索引，将改变后的board、i、j、k传入`dfs()`函数进行下一次递归。

之后循环2、3步即可。
<!--more-->

代码
---
```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        for (int i = 0; i < board.size(); ++i) {
            for (int j = 0; j < board[0].size(); ++j) {
                if (board[i][j] == word[0] && dfs(board, word, i, j, 0)) return true;
            }
        }
        return false;
    }

private:
    bool dfs(vector<vector<char>>& board, string word, int i, int j, int k) {
        if (k == word.size()) return true;
        if (i < 0 || i == board.size() || j < 0 || j == board[0].size() || board[i][j] != word[k]) return false;
        board[i][j] = '\0';
        bool res = dfs(board, word, i+1, j, k+1) || dfs(board, word, i-1, j, k+1) || dfs(board, word, i, j+1, k+1) || dfs(board, word, i, j-1, k+1);
        board[i][j] = word[k];
        return res;
    }
};
```

**注意：**`dfs()`参数设置要设定为引用，不然每次递归都要新建数组副本会超时。
