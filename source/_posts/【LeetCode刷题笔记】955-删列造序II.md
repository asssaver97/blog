---
title: 【LeetCode刷题笔记】955.删列造序II
date: 2022-04-18 12:35:46
tags: 刷题
categories: LeetCode
---
题目
---
[**955. 删列造序 II**](https://leetcode-cn.com/problems/delete-columns-to-make-sorted-ii/)

给定由 n 个字符串组成的数组 strs，其中每个字符串长度相等。

选取一个删除索引序列，对于 strs 中的每个字符串，删除对应每个索引处的字符。

比如，有 strs = ["abcdef", "uvwxyz"]，删除索引序列 {0, 2, 3}，删除后 strs 为["bef", "vyz"]。

假设，我们选择了一组删除索引 answer，那么在执行删除操作之后，最终得到的数组的元素是按 字典序（strs[0] <= strs[1] <= strs[2] ... <= strs[n - 1]）排列的，然后请你返回 answer.length 的最小可能值。

示例 1：

```cpp
输入：strs = ["ca","bb","ac"]
输出：1
解释： 
删除第一列后，strs = ["a", "b", "c"]。
现在 strs 中元素是按字典排列的 (即，strs[0] <= strs[1] <= strs[2])。
我们至少需要进行 1 次删除，因为最初 strs 不是按字典序排列的，所以答案是 1。
```

示例 2：

```cpp
输入：strs = ["xc","yb","za"]
输出：0
解释：
strs 的列已经是按字典序排列了，所以我们不需要删除任何东西。
注意 strs 的行不需要按字典序排列。
也就是说，strs[0][0] <= strs[0][1] <= ... 不一定成立。
```

示例 3：

```cpp
输入：strs = ["zyx","wvu","tsr"]
输出：3
解释：
我们必须删掉每一列。
```

提示：

* n == strs.length
* 1 <= n <= 100
* 1 <= strs[i].length <= 100
* strs[i] 由小写英文字母组成

<!--more-->
题解
---
**贪心贪心贪心。注意审题，["a", "a", "a"] 也是符合题意的。**

我们把每个字符串当作一行，所有字符串的同一索引当作一列。则**原题可以转换为删除最少的列，再从贪心算法的角度出发，转换成保留最多的列。**

如果当前列需要判断，则分为下面三种情况：

* 如果`strs[i][j] < strs[i-1][j]`，此列删除；
* 如果`strs[i][j] >= strs[i-1][j]`，此列保留。

那么如何知道当前列需不需要判断呢？当然要从前面几列出发：

* 如果`strs[i][j] < strs[i-1][j]`或者`strs[i][j] > strs[i-1][j]`，后面的列肯定不用判断；
* 如果`strs[i][j] == strs[i-1][j]`，那么还需要在后面的列判断。

所以我们还需要建立一个数组来判断当前列是否需要判断。

代码
---
```cpp
class Solution {
public:
    int minDeletionSize(vector<string>& strs) {
        int m = strs.size(), n = strs[0].size();
        vector<bool> pre(m, true);
        vector<bool> cur;
        int res = 0;
        for (int i = 0; i < n; ++i) {
            cur = pre;
            int j = 1;
            for (; j < m; ++j) {
                if (cur[j]) {
                    if (strs[j][i] < strs[j-1][i]) {
                        ++res;
                        break;
                    } else if (strs[j][i] > strs[j-1][i]) {
                        cur[j] = false;
                    }
                }
            }
            if (j==m) pre = cur;
        }
        return res;
    }
};
```