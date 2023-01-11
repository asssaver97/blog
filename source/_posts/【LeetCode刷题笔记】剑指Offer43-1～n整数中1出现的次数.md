---
title: 【LeetCode刷题笔记】剑指Offer43.1～n整数中1出现的次数
date: 2022-02-01 17:49:56
tags: 算法
categories: 刷题笔记
mathjax: true
---
题目
---
[**剑指Offer 43. 1～n整数中1出现的次数**](https://leetcode-cn.com/problems/1nzheng-shu-zhong-1chu-xian-de-ci-shu-lcof/)

输入一个整数`n`，求1～n这n个整数的十进制表示中1出现的次数。

例如，输入12，1～12这些整数中包含1 的数字有1、10、11和12，1一共出现了5次。

示例 1：
```
输入：n = 12
输出：5
```
示例 2：
```
输入：n = 13
输出：6
```

限制：
* 1 <= n < 2^31
<!--more-->

题解
---
**挨个计算每个位数上1出现的次数。**
举例：假设n=1234，我们枚举所有十位数上出现1的可能，分别是0010、0011、0012、...、1217、1218、1219，计算次数时去掉当前位，也就是000～124，一共125种情况。
设计算法时，我们应该考虑如下三种情况：
（仍然是以十位举例）
1. 当前位是0:
> 例：1204
> 0010\0011\0012\...\1118\1119 -> 000~119 = 120
所以计算公式为：高于当前位的数 * 位数
2. 当前位是1:
> 例：1214
> 0010\0011\0012\...\1213\1214 -> 000~124 = 125
所以计算公式为：高于当前位的数 * 位数 + 低于当前位的数 + 1
3. 当前位是2～9:
> 例：1234
> 0010\0011\0012\...\1218\1219 -> 000~129 = 130
所以计算公式为：(高于当前位的数 + 1) * 位数

代码
---
```cpp
class Solution {
public:
    int countDigitOne(int n) {
        int count = 0;
        long int digit = 1;
        while (n >= digit) {
            int i = (n / digit) % 10;
            int high = n / digit /10;
            int low = n % digit;
            if (i == 0) count += high * digit;
            else if (i == 1) count += high * digit + low + 1;
            else count += (high + 1) * digit;
            digit *= 10;
        }
        return count;
    }
};
```
