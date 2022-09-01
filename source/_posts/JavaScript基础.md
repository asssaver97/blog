---
title: JavaScript基础
date: 2022-01-12 17:57:06
categories: JavaScript
tags: 基础知识
---
> **写在前面**
> + JavaScript 对大小敏感
> + 语句结尾分号可加可不加
> + JavaScript 不需要空格，一般为了代码可读性会向脚本添加空格，但JS解释执行时会忽略多余的空格
> + 字符串可以用单引号或双引号
> + 不同数据类型可以直接相加
> + 回车符是`<br>`
> + 获取对象长度是a.length
<!--more-->
---
## 变量
JavaScript 使用关键字 var 来定义变量， 使用等号来为变量赋值：
```js
var num = 10;					// 数字
var a = b + c;					// 表达式
var x = true;					// 布尔
var name = "vincent";				// 字符串
var nums = [1, 2, 3];				// 数组
var ceo = {firstName:"Steve", lastName:"Jobs"};	// 对象
```
也可以使用关键词"new"来声明类型：
```js
var carname=new String;
var x=      new Number;
var y=      new Boolean;
var cars=   new Array;
var person= new Object;
```
### undefined 和 null
undefined表示这个变量不含有值，可以通过将变量的值设为null来清空变量。
```js
var a;	// undefined
a = 10;
a = null;
```
---
## 函数
JavaScript 语句可以写在函数内，函数可以重复引用：
```js
function myFunction(a, b) {
    return a * b;                                // 返回 a 乘以 b 的结果
}
```
---
## 对象
对象由花括号分隔。在括号内部，对象的属性以名称和值对的形式 (name : value) 来定义。属性由逗号分隔：
```js
var person={
firstname:"John",
lastname:"Doe",
fullName:function(){}	// 创建对象方法
};
```
对象属性有两种寻址方式：
```js
var name = person.lastname;
var name = person["lastname"];
```
### 对象方法
对象的方法定义了一个函数，并作为对象的属性存储。
对象方法通过添加 () 调用 (作为一个函数)，注意调用对象方法和调用对象属性的区别：
```js
name = person.fullName();	// 调用对象方法
name = person.fullName;		// 调用对象属性
```
---
## 运算符
### 比较运算符
|运算符|描述|
|:----:|:----:|
|==|等于|
|===|绝对等于（值和类型都相等）|
|!=|不等于|
|!==|不绝对等于（值和类型有一个不相等，或两个都不相等）|
|>|大于|
|>=|大于等于|
|<|小于|
|<=|小于等于|
### 逻辑运算符
|运算符|描述|
|:----:|:----:|
|&&|与|
|\|\||或|
|\!|非|
### 条件运算符
```js
variablename=(condition)?value1:value2
```
---
## 条件语句
在 JavaScript 中，我们可使用以下条件语句：
* **if 语句** - 只有当指定条件为 true 时，使用该语句来执行代码
* **if...else 语句** - 当条件为 true 时执行代码，当条件为 false 时执行其他代码
* **if...else if....else 语句**- 使用该语句来选择多个代码块之一来执行
* **switch 语句** - 使用该语句来选择多个代码块之一来执行
### if 语句
```js
if (condition)
{
    \\ 当条件为 true 时执行的代码
}
```
### if...else 语句
```js
if (condition)
{
    \\ 当条件为 true 时执行的代码
}
else
{
    \\ 当条件不为 true 时执行的代码
}
```
### if...else if....else 语句
```js
if (condition1)
{
    \\ 当条件 1 为 true 时执行的代码
}
else if (condition2)
{
    \\ 当条件 2 为 true 时执行的代码
}
else
{
    \\当条件 1 和 条件 2 都不为 true 时执行的代码
}
```
### switch 语句
首先设置表达式 n（通常是一个变量）。随后表达式的值会与结构中的每个 case 的值做比较。如果存在匹配，则与该 case 关联的代码块会被执行。
**注意：**请使用 break 来阻止代码自动地向下一个 case 运行。
使用 default 关键词来规定匹配不存在时做的事情：
```js
switch(n)
{
    case 1:
        \\ 执行代码块 1
        break;
    case 2:
        \\ 执行代码块 2
        break;
    default:
        \\ 与 case 1 和 case 2 不同时执行的代码
}
```
---
## 循环语句
JavaScript 支持不同类型的循环：
* **for** - 循环代码块一定的次数
* **for/in** - 循环遍历对象的属性
* **while** - 当指定的条件为 true 时循环指定的代码块
* **do/while** - 同样当指定的条件为 true 时循环指定的代码块
### for 循环
```js
for (语句 1; 语句 2; 语句 3)
{
    // 被执行的代码块
}
// 语句 1 循环开始前执行
// 语句 2 定义循环运行的条件
// 语句 3 在每一次循环后执行
```
### for/in 循环
```js
var txt = "";
var person={fname:"Bill",lname:"Gates",age:56}; 
for (x in person)  // x 为属性名
{
    txt=txt + person[x];
}
```
### while 循环
```js
while (条件)
{
   //  需要执行的代码
}
```
### do/while 循环
```js
do
{
    \\ 需要执行的代码
}
while (条件);
```
### break 和 continue
* break跳出循环
* continue继续下次循环
---
## 类型
### typeof 操作符
typeof 操作符用来查看 JavaScript 变量的数据类型
```js
typeof "John"                 // 返回 string
typeof 3.14                   // 返回 number
typeof NaN                    // 返回 number
typeof false                  // 返回 boolean
typeof [1,2,3,4]              // 返回 object
typeof {name:'John', age:34}  // 返回 object
typeof new Date()             // 返回 object
typeof function () {}         // 返回 function
typeof myCar                  // 返回 undefined (如果 myCar 没有声明)
typeof null                   // 返回 object
```
### constructor 属性
constructor 属性返回所有 JavaScript 变量的构造函数
```js
"John".constructor                 // 返回函数 String()  { [native code] }
(3.14).constructor                 // 返回函数 Number()  { [native code] }
false.constructor                  // 返回函数 Boolean() { [native code] }
[1,2,3,4].constructor              // 返回函数 Array()   { [native code] }
{name:'John', age:34}.constructor  // 返回函数 Object()  { [native code] }
new Date().constructor             // 返回函数 Date()    { [native code] }
function () {}.constructor         // 返回函数 Function(){ [native code] }
```
### 类型转换
|方法|描述|实例|
|:---:|:---:|:---:|
|String()|数字、布尔值、日期转字符串|String(x)、String(true)、String(new Date())|
|().toString|数字、布尔值、日期转字符串|x.toString()|
|Number()|字符串、布尔值、日期转数字|Number("3.14")、Number(true)、Number(new Date())|
