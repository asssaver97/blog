---
title: 【C++】String转数组（split方法构建）
date: 2022-03-03 12:30:52
tags: C++
---
思路是istringstream配合getline使用。

istringstream是C++的输入输出控制类，它的构造函数原型如下：
```cpp
istringstream::istringstream(string str);
```
它的作用是从string对象str中读取字符。

getline是C++标准库函数，它的原型如下：
```cpp
istream& getline (istream &is, string &str, char delim);
```
其中，istream &is 表示一个输入流，譬如cin，或者istringstream；
string &str表示把从输入流读入的字符串存放在这个字符串中（可以自己随便命名，str什么的都可以）；
char delim表示遇到这个字符停止读入，在不设置的情况下系统默认该字符为'\n'，也就是回车换行符（遇到回车停止读入）。
<!--more-->

split函数代码
---
```cpp
#include <string>
#include <vector>
#include <iostream>
#include <sstream>

using namespace std;

vector<string> split(const string& s, char delim) {
    istringstream iss(s);
    vector<string> strs;
    string temp;
    while (getline(iss, temp, delim)) {
        if (temp == "") continue; // 排除空字符串
        strs.push_back(temp);
    }
    return strs;
}
```
