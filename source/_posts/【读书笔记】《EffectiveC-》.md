---
title: 【读书笔记】《EffectiveC++》
date: 2022-03-24 14:58:27
tags: 读书笔记
categories: C++
---
导读
---
### 声明式和定义式
1. 变量的定义：变量的定义用于为变量分配存储空间，还可以为变量指定初始值。在一个程序中，变量有且仅有一个定义。
2. 变量的声明：用于向程序表明变量的类型和名字。程序中变量可以声明多次，但只能定义一次。
3. 两者联系与区别：
    * 定义也是声明，因为当定义变量时我们也向程序表明了它的类型和名字；
    * 但声明不是定义，可以通过使用extern关键字声明变量而不定义它。不定义变量的声明包括对象名、对象类型和对象类型前的关键字extern；
<!--more-->

条款01：视C++为一个语言联邦
---
C++是一个多重范型编程语言，一个同时支持**过程形式**、**面向对象形式**、**函数形式**、**泛型形式**、**元编程形式**的语言。多重特性之间的适用范围会让人糊涂，因此我们可以将其看成一个由多个**次语言**组成的联邦而非单一语言。

C++主要的次语言有四个：

* **C。**区块、语句、预处理器、内置数据类型、数组、指针等统统来自于C，但是C也有其局限：没有模板、没有异常、没有重载……
* **Object-Oriented C++。**这部分也就是**C with Classes**（C++原名）所诉求的：classes（包括构造函数和析构函数），封装、继承、多态、virtual函数（动态绑定）……
* **Template C++。**这是C++的泛型编程部分。它带来了崭新的编程范型（programming paradigm），也就是所谓的**template metaprogramming**（TMP，模板元编程）。
* **STL。**包含容器、迭代器、算法。

条款02：尽量以`const`，`enum`，`inline` 替换 `#define`
---
### const
当我们以常量替换`#define`时，有两种特殊情况需要注意。

第一是定义**常量指针**。由于常量定义通常被放在头文件内（一边被不同的源码含入），因此有必要讲**指针**（而不是指针所指之物）声明为const。例如要在头文件内定义一个常量的`char*`字符串时，必须要写`const`两次：

```cpp
const char* const authorName = "Scott Meyers";
```

第二个需要注意的是class专属常量。为了将常量的作用域（scope）限制于class内，你必须要让它成为class的一个成员（member）；而为确保此常量至多只有一份实体，你必须让它成为一个static成员：

```cpp
class GamePlayer {
private:
    static const in NumTurns = 5; //常量声明式
    int scores[NumTurns]; //使用该常量
    ...
};
```

### enum
枚举类型(enumeration)是 C++ 中的一种派生数据类型，它是由用户定义的若干枚举常量的集合。

定义格式：

```cpp
enum <类型名> {<美剧常量表>}
```

**枚举常量**代表该枚举类型的变量可能取的值，编译系统为每个枚举常量指定一个整数值，默认状态下，这个整数就是所列举元素的序号，序号从0开始。 **也可以在定义枚举类型时为部分或全部枚举常量指定整数值，在指定值之前的枚举常量仍按默认方式取值，而指定值之后的枚举常量按依次加1的原则取值。 **各枚举常量的值可以重复。例如：

```cpp
enum fruit_set {apple, orange, banana=1, peach, grape}
//枚举常量apple=0,orange=1, banana=1,peach=2,grape=3。

enum week {Sun=7, Mon=1, Tue, Wed, Thu, Fri, Sat};
//枚举常量Sun,Mon,Tue,Wed,Thu,Fri,Sat的值分别为7、1、2、3、4、5、6。
```

选择enum而非const的理由：有时候你不想别人取到你常量的地址时，或者不想有一个指针或者引用指向enum时，因为取const的地址是合法的，而取#define和enum的地址就不合法。

### inline
宏。没什么好说的。

条款03：尽可能使用const
---
### 指针常量和常量指针
常量指针：`const int * a;` 或 `int const * a;`

指针常量：`int * const a;`

### 迭代器
声明迭代器为 const 就像声明指针为const一样（即声明一个 T* const 指针），如果希望迭代器所指的东西不可被改动（即声明一个 const T* 指针），你需要的是 const_iterator：

```cpp
const std::vector<int>::iterator iter = vec.begin(); //等于 T* const
std::vector<int>::const_Iterator cIter = vec.begin(); //等于 const T*
```

### 函数返回值
作用：比如避免`a*b=c`这样的错误。

```cpp
class Rational { ... };
const Rational operator* (const Rational& lhs, const Rational& rhs); //第一个const确保返回值不被修改
```

### 成员函数
将const实施于成员函数的目的，是为了确认该成员函数可作用于const对象身上。

```cpp
class TextBlock {
public:
    ...
    // operator[] 作用于const对象上
    const char& operator[] (std::size_t position) const {
        return text[position];
    }
    // operator[] 作用于非const对象上
    char& operator[] (std::size_t position) {
        return text[position];
    }
}
```
如果想要修改指定成员变量，可以用`mutable`修饰。

条款04：确定对象被使用前已先被初始化
---
对于内置类型，初始化很简单，比如`int a = 0;` 对于内置类型以外的类，初始化责任落在构造函数上：确保每一个构造函数都将对象的每一个成员初始化。

对于成员变量需要注意的是：**构造函数内初始化并不是真正的初始化！！！而只是赋值！**比如：

```cpp
class Cperson
{
private:
    int m_age;
    float m_height;
    char* m_name;
public:
    Cperson(int age,float height,const char* name)
    {
        m_age=age;
        m_height=height;
        int len=strlen(name);
        m_name=new char[len+1];//创建内存
        strcpy(m_name,name);
    }
}
```

真正的初始化应该发生在这些成员的 default 构造函数被自动调用之时，比进入当前对象的构造函数更早，也就是**成员初值列**：

```cpp
//m_name为指针类型，需要自己申请空间
Cperson::Cperson(int age,float height,const char* name):m_age(age),m_height(height)
{
        int len=strlen(name);
         m_name=new char[len+1];//创建内存
        strcpy(m_name,name);
}
```

**注意点！！！**

* 如果成员变量是`const`或**references**，它们就一定需要初值，而不能被赋值，所以一定要使用成员初值列。
* class的成员变量总是以其声明次序被初始化，即使它们在成员初值列中以不同的次序出现，也不会改变。

### 不同编译单元内定义之 non-local static 对象的初始化顺序
概念：

* static对象：所谓static对象，其寿命从被构造出来知道程序结束为止，因此，堆（stack）栈（heap）里的对象都被排除。static对象包括全局对象、定义于namespace作用域内的对象、在classes内、在函数内、以及在文件作用域内被声明为`static`的对象。函数内的static对象称为local static对象（因为它们对函数而言是loacl），其他static对象被称为non-local static对象。程序结束时static对象会自动销毁，也就是它们的析构函数会在`main()`结束时被自动调用。
* 编译单元：所谓编译单元（translation unit）是指产出单一目标文件的那些源码。基本上它是单一源码文件加上其所含入的头文件（`# include` files）。

C++对于定义于不同编译单元内的 **non-local static** 对象的初始化顺序没有明确定义，这就导致了当一个 **non-local static** 对象用到了另一个 **non-local static** 对象时，如果第二个 **non-local static** 对象还没有初始化，就会造成错误。

解决办法：把 **non-local static** 对象放进函数里，使其变成 **local static** 对象，只要返回一个 **reference** 指向该  **local static** 对象就好了。因为C++保证，函数内的 **local static** 对象会在该函数被调用期间首次遇到该对象定义式时被初始化。

详见P30-32。

条款05：了解C++默默编写并调用哪些函数
---
C++会为一个空类自动声明一个 ***copy*** 构造函数，一个 ***copy assignment*** 操作符和一个析构函数，此外，如果你没有声明任何构造函数，编译器也会为你声明一个 ***default*** 构造函数。

注意，编译器产出的析构函数是 **non-virtual**。

对于内含 **reference** 成员或者 **const** 成员的类来说，必须自己定义 ***copy assignment*** 操作符。

条款06：若不想使用编译器自动生成的函数，就该明确拒绝
---
为了不使用编译器自动提供的函数，（比如 ***copy*** 构造函数和 ***copy assignment*** 操作符，）一般有两种方法：

1. 我们可以将对应的成员函数声明为 **private** 并不提供实现。
2. 我们如上设计一个基类，再继承他。

条款07：为多态基类声明 virtual 析构函数
---
如果基类的析构函数为 **non-virtual**，那么会造成“局部销毁”现象，即派生类内的成员变量很可能没被销毁，而基类的成员变量则被全部销毁。

对如上问题的解决办法：给 base class 的析构函数设为 **virtual**。

**注：**任何 class，只要带有 **virtual** 函数，都应该配有一个 **virtual** 析构函数。而如果 class 内不含 **virtual** 函数，那么就意味着它不打算做一个基类，这时候将它的析构函数声明为 **virtual** 就是一个坏主意。（**因为占的内存大小会增加。**）

小tips：当你需要基类为抽象类时，你可以将析构函数声明为纯虚函数。但是必须为该析构函数提供定义。

条款08：别让异常逃离析构函数
---
不应该让析构函数吐出异常，否则当容器或array里存了此类，C++会同时收到两个及以上的异常，这时程序必须立即结束执行，否则会导致不明确行为。

一般的，有两个解决办法：

* 如果析构函数抛出异常就立刻结束程序，通常通过调用`std::abort()`完成；
* 忽略异常。

但是，有一个更好的办法：在类内提供一个普通函数来执行 close 操作。

条款09：绝不在构造和析构过程中调用 virtual 函数
---
确保你的构造函数和析构函数（即在对象创建和销毁期间）都没有调用 **virtual** 函数，而且它们调用的所有函数也都服从统一约束。

因为**基类对虚函数的调用从不下降至派生类。**

解决方法：我们可以换种思路，既然不能让虚函数从基类**向下**调用，那么我们可以让派生类将必要的构造信息**向上**传递至基类的构造函数。（详细见 P51）

条款10：令 operator= 返回一个 *reference to* *this
---
```cpp
class Wiget {
public:
    ...
    Wiget& operate=(const Wiget& rhs) {
        ...
        return *this;
    }
}
```

此条款适用于所有赋值相关运算，例如`+=`等，**但不具有强制性**。

条款11：在 operator= 中处理“自我赋值”
---
