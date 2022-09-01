---
title: 【LeetCode刷题笔记】剑指Offer20.表示数值的字符串
date: 2022-01-26 11:09:43
tags: 刷题
categories: LeetCode
mathjax: true
---
题目
---
[**剑指Offer 20. 表示数值的字符串**](https://leetcode-cn.com/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/)

请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。

数值（按顺序）可以分成以下几个部分：
1. 若干空格
2. 一个**小数**或者**整数**
3. （可选）一个`'e'`或`'E'`，后面跟着一个**整数**
4. 若干空格

小数（按顺序）可以分成以下几个部分：
1. （可选）一个符号字符（'+' 或 '-'）
2. 下述格式之一：
    1. 至少一位数字，后面跟着一个点 '.'
    2. 至少一位数字，后面跟着一个点 '.' ，后面再跟着至少一位数字
    3. 一个点 '.' ，后面跟着至少一位数字

整数（按顺序）可以分成以下几个部分：
1. （可选）一个符号字符（'+' 或 '-'）
2. 至少一位数字

部分**数值**列举如下：
* `["+100", "5e2", "-123", "3.1416", "-1E-16", "0123"]`

部分**非数值**列举如下：
* `["12e", "1a3.14", "1.2.3", "+-5", "12e+5.4"]`

示例 1：
```
输入：s = "0"
输出：true
```
示例 2：
```
输入：s = "e"
输出：false
```
示例 3：
```
输入：s = "."
输出：false
```
示例 4：
```
输入：s = "    .1  "
输出：true
```

提示：
* `1 <= s.length <= 20`
* `s`仅含英文字母（大写和小写），数字（0-9），加号`'+'`，减号`'-'`，空格`' '`或者点`'.'`。
<!--more-->

题解
---
**对于任何“给定字符串s，判断是否满足条件p”的命题都可以用确定有限状态自动机求解。**

确定有限状态自动机${\displaystyle {\mathcal {A}}}$是由
* 一个非空有限的状态集合${\displaystyle Q}$ 
* 一个输入字母表${\displaystyle \Sigma }$ （非空有限的字符集合）
* 一个转移函数${\displaystyle \delta :Q\times \Sigma \rightarrow Q}$ （例如：${\displaystyle \delta \left(q,\sigma \right)=p,\left(p,q\in Q,\sigma \in \Sigma \right)}$）
* 一个开始状态${\displaystyle s\in Q}$ 
* 一个接受状态的集合${\displaystyle F\subseteq Q}$ 

所组成的5-元组。因此一个DFA可以写成这样的形式：${\displaystyle {\mathcal {A}}=\left(Q,\Sigma ,\delta ,s,F\right)}$。

首先确定**状态集合**：
1. 起始空格
2. 符号位
3. 整数部分
4. 左边无整数的小数点 
5. 左边有整数的小数点
6. 小数部分
7. 幂符号
8. 指数部分的符号位
9. 指数部分的整数
10. 结尾空格

其中，1为**开始状态**，[3, 5, 6, 9, 10]为**接受状态的集合**，**输入字母表**为字符串中的字符，**转移函数**如下：
![](/images/jianzhi_20_fig1.png)

代码
---
```cpp
class Solution {
public:
    // 状态集合
    enum State {
        STATE_INITIAL,
        STATE_SIGN_INT,
        STATE_INTEGER,
        STATE_POINT_WITH_INTEGER,
        STATE_POINT_WITHOUT_INTEGER,
        STATE_FRACTION,
        STATE_EXP,
        STATE_SIGN_EXP,
        STATE_INTEGER_EXP,
        STATE_END
    };
    
    // 接受状态集合
    set<State> endState{
        STATE_INTEGER,
        STATE_POINT_WITH_INTEGER,
        STATE_FRACTION,
        STATE_INTEGER_EXP,
        STATE_END
    };

    // 输入字母表
    enum CharType {
        CHAR_SPACE,
        CHAR_INT,
        CHAR_SIGN,
        CHAR_EXP,
        CHAR_POINT,
        CHAR_WRONG
    };

    // 将字符转换为字母表
    CharType toCharType(char c) {
        if (c == ' ') return CHAR_SPACE;
        if (c >= '0' && c <= '9') return CHAR_INT;
        if (c == '+' || c == '-') return CHAR_SIGN;
        if (c == 'e' || c == 'E') return CHAR_EXP;
        if (c == '.') return CHAR_POINT;
        return CHAR_WRONG;
    }    

    // 状态转移函数
    unordered_map<State, unordered_map<CharType, State>> transfer{
        {STATE_INITIAL, {
            {CHAR_SPACE, STATE_INITIAL},
            {CHAR_SIGN, STATE_SIGN_INT},
            {CHAR_POINT, STATE_POINT_WITHOUT_INTEGER},
            {CHAR_INT, STATE_INTEGER}
            }
        },
        {STATE_SIGN_INT, {
            {CHAR_INT, STATE_INTEGER},
            {CHAR_POINT, STATE_POINT_WITHOUT_INTEGER}
            }
        },
        {STATE_INTEGER, {
            {CHAR_POINT, STATE_POINT_WITH_INTEGER},
            {CHAR_SPACE, STATE_END},
            {CHAR_EXP, STATE_EXP},
            {CHAR_INT, STATE_INTEGER}
            }
        },
        {STATE_POINT_WITH_INTEGER, {
            {CHAR_SPACE, STATE_END},
            {CHAR_INT, STATE_FRACTION},
            {CHAR_EXP, STATE_EXP}
            }
        },
        {STATE_POINT_WITHOUT_INTEGER, {
            {CHAR_INT, STATE_FRACTION}
            }
        },
        {STATE_FRACTION, {
            {CHAR_INT, STATE_FRACTION},
            {CHAR_SPACE, STATE_END},
            {CHAR_EXP, STATE_EXP}
            }
        },
        {STATE_EXP, {
            {CHAR_SIGN, STATE_SIGN_EXP},
            {CHAR_INT, STATE_INTEGER_EXP}
            }
        },
        {STATE_SIGN_EXP, {
            {CHAR_INT, STATE_INTEGER_EXP}
            }
        },
        {STATE_INTEGER_EXP, {
            {CHAR_INT, STATE_INTEGER_EXP},
            {CHAR_SPACE, STATE_END}
            }
        },
        {STATE_END, {
            {CHAR_SPACE, STATE_END}
            }
        }
    };

    bool isNumber(string s) {
        State cur = STATE_INITIAL;
        for (char c : s) {
            CharType ct = toCharType(c);
            if (transfer[cur].find(ct) == transfer[cur].end()) return false;
            cur = transfer[cur][ct];
        }
        return endState.find(cur) != endState.end();
    }
};
```
