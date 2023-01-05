---
title: 【C++】右值引用
date: 2022-09-02 17:01:27
tags: 基础知识
categories: C++
---
C++左值和右值
---
在 C++ 或者 C 语言中，一个表达式（可以是字面量、变量、对象、函数的返回值等）根据其使用场景不同，分为左值表达式和右值表达式。确切的说 C++ 中左值和右值的概念是从 C 语言继承过来的。

值得一提的是，左值的英文简写为“lvalue”，右值的英文简写为“rvalue”。很多人认为它们分别是"left value"、"right value" 的缩写，其实不然。lvalue 是“loactor value”的缩写，可意为存储在内存中、有明确存储地址（可寻址）的数据，而 rvalue 译为 "read value"，指的是那些可以提供数据值的数据（不一定可以寻址，例如存储于寄存器中的数据）。

**能取到地址、有名字的就是左值，取不到地址的就是右值。**
<!--more-->
 
左值引用、右值引用
---
C++11以前的引用只能操作左值，无法对右值添加引用，比如：

```cpp
int num = 10;
int &b = num; //正确
int &c = 10; //错误
```

因此被称为**左值引用**。但是可以使用常量左值引用来操作右值，比如：

```cpp
int num = 10;
const int &b = num;
const int &c = 10;
```

但是带来的新问题是无法对右值进行修改。为此C++11引入了一个新标准，称为**右值引用**，用“&&”表示。和声明左值引用一样，右值引用也必须立即进行初始化操作，且只能使用右值进行初始化，比如：

```cpp
int&& a = 1; //实质上就是将不具名(匿名)变量取了个别名
int b = 1;
int && c = b; //编译错误！ 不能将一个左值复制给一个右值引用
class A {
public:
	int a;
};
A getTemp() {
	return A();
}
A && a = getTemp();   //getTemp()的返回值是右值（临时变量）
```
getTemp()返回的右值本来在表达式语句结束后，其生命也就该终结了（因为是临时变量），而通过右值引用，该右值又重获新生，其生命期将与右值引用类型变量a的生命期一样，只要a还活着，该右值临时变量将会一直存活下去。实际上就是给那个临时变量取了个名字。

和常量左值引用不同的是，右值引用还可以对右值进行修改，比如：

```cpp
int && a = 10;
a = 100;
cout << a << endl;
```

移动语义
---
移动语义的引入是指在进行大数据复制的时候，将动态申请的内存空间的所有权直接转让出去，不用进行大量的数据移动，既节省空间又提高效率。

```cpp
#include <iostream>
using namespace std;

class A {
public:
	A() {
		cout << "Default construction." << endl;
	}
	A(const A &a) {
		cout << "Copy construction." << endl;
	}
};

A ReturnRvalue() {
	return A(); //返回一个临时对象
}

void RefTransfer(const A& a) {
	//...
}

void ValTransfer(const A a) {
	//...
}

int main() {
	cout << "pass by value: " << endl;
	ValTransfer(ReturnRvalue()); // 应该调用两次拷贝构造函数
	cout << "pass by reference: " << endl;
	RefTransfer(ReturnRvalue()); //应该只调用一次拷贝构造函数
}
```

在上面的例子中若采用值传递(调用`ValTransfer()`函数)应该需要调用2次拷贝构造函数, 一次是在 `ReturnRvalue()`函数中调用缺省构造函数构造一个临时对象 A, 而返回时则会调用拷贝构造函数生成一个临时对象, 在进行值传递时, 又会讲这个对象拷贝给函数的参数变量 a。而进行引用传递时(调用`RefTransfer()`函数) 则接收一个右值, 即将`ReturnRvalue()`生成的右值临时对象直接变成常量左值引用, 不需要拷贝。

**注: C++中使用拷贝构造函数包括一下三种情况：**

1. **函数调用时, 将对象当做调用的参数，**
2. **函数返回时，返回的对象将从函数栈区复制一份到函数的返回区，**
3. **使用一个对象初始化另一个对象时也会调用拷贝构造函数。**

但实际上如果编译器实现(开启)了返回值优化(RVO/NRVO, RVO, Return Value Optimization 返回值优化，或者NRVO， Named Return Value Optimization), 编译器将`ReturnRvalue()`内部生成的这个对象，与返回的临时对象, 以及函数的形参优化成了一个对象，避免了拷贝, 而不需要调用拷贝构造函数。

RVO的机制允许函数使用父堆栈以避免赋值, 但添加 if-else 语句后编译器无法确定函数将要返回哪一个对象, 此时还是会调用拷贝构造函数。

```cpp
A ReturnRvalue(int n){
	A local_a ，another_a;  
	if （n > 2 ）{    
		return local_a; 
	} else {  
		return another_a ; 
	}
}
int main(){  
	ValTransfer(ReturnRvalue(1)); // 此时将会调用两次构造函数
}
```

要实现移动语义就必须增加两个函数：移动构造函数和移动赋值构造函数。

```cpp
#include <string>
#include <iostream>

class A {
public:
    std::string s;
    A() : s("test") { std::cout << "Default Constructor!\n"; }
    A(const A& a) : s(a.s) { std::cout << "Copy Constructor!\n"; }
    A(A&& a) : s(std::move(a.s)) { std::cout << "Move Constructor!\n"; }
    A& operator=(const A& other) {
        s = other.s;
        std::cout << "Copy Assigned!\n";
        return *this;
    }

    A& operator=(A&& other) {
        s = std::move(other.s);
        std::cout << "Move Assigned\n";
        return *this;
    }
};

A ReturnRValue() { return A(); }

int main()
{
    A a1, a2;
    std::cout << "从右值临时量移动赋值:" << std::endl;
    a1 = ReturnRValue(); // 从右值临时量移动赋值
    std::cout << "从左值移动赋值, 调用移动c操作符:" << std::endl;
    a2 = std::move(a1);  // 从左值移动赋值, 调用移动c操作符
    std::cout << "调用复制构造函数:" << std::endl;
    A a3(a2);            // 调用复制构造函数
    std::cout << "移动前a3的值为:" << std::endl;
    std::cout << a3.s << std::endl;
    std::cout << "调用移动构造函数:" << std::endl;
    A a4(std::move(a3));      // 调用移动构造函数
    std::cout << "移动后a3的值为:" << std::endl;
    std::cout << a3.s << std::endl;
    std::cout << "a4的值为:" << std::endl;
    std::cout << a4.s << std::endl;
}

/* 输出结果为
Default Constructor!
Default Constructor!
从右值临时量移动赋值:
Default Constructor!
Move Assigned
从左值移动赋值, 调用移动c操作符:
Move Assigned
调用复制构造函数:
Copy Constructor!
移动前a3的值为:
test
调用移动构造函数:
Move Constructor!
移动后a3的值为:

a4的值为:
test
*/
```

可以看到，移动构造函数与拷贝构造函数的区别是，拷贝构造的参数是c是常量左值引用，而移动构造的参数是是右值引用。而移动构造函数与拷贝构造不同，它并不是重新分配一块新的空间，将要拷贝的对象复制过来，而是"偷"了过来，**将自己的指针指向别人的资源，然后将别人的指针修改为nullptr**，这一步很重要，如果不将别人的指针修改为空，那么临时对象析构的时候就会释放掉这个资源，"偷"也白偷了。有些左值是局部变量，生命周期也很短，能不能也移动而不是拷贝呢？C++11为了解决这个问题，提供了`std::move()`方法来将左值转换为右值，从而方便应用移动语义。

通用引用（universal references）
---
当右值引用和模板结合的时候，就复杂了。T&&并不一定表示右值引用，它可能是个左值引用又可能是个右值引用。例如：

```cpp
template<typename T>
void f( T&& param){
    
}
f(10);  //10是右值
int x = 10; //
f(x); //x是左值
```

如果上面的函数模板表示的是右值引用的话，肯定是不能传递左值的，但是事实却是可以。这里的&&是一个未定义的引用类型，称为universal references，它必须被初始化，它是左值引用还是右值引用却决于它的初始化，如果它被一个左值初始化，它就是一个左值引用；如果被一个右值初始化，它就是一个右值引用。

**注意：只有当发生自动类型推断时（如函数模板的类型自动推导，或auto关键字），&&才是一个universal references。**

如果`T`被推导为`string&`，就会发生类似`string& &&`的情况，对于这种情况，c++11增加了**引用折叠**的规则，总结如下：

* 所有的右值引用叠加到右值引用上仍然使一个右值引用。
* 所有的其他引用类型之间的叠加都将变成左值引用。

所以`T& &&`其实就被折叠成了个`string &`，是一个左值引用。

完美转发
---
所谓转发，就是通过一个函数将参数继续转交给另一个函数进行处理，原参数可能是右值，可能是左值，如果还能继续保持参数的原有特征，那么它就是完美的。

```cpp
void process(int& i){
    cout << "process(int&):" << i << endl;
}
void process(int&& i){
    cout << "process(int&&):" << i << endl;
}

void myforward(int&& i){
    cout << "myforward(int&&):" << i << endl;
    process(i);
}

int main()
{
    int a = 0;
    process(a); //a被视为左值 process(int&):0
    process(1); //1被视为右值 process(int&&):1
    process(move(a)); //强制将a由左值改为右值 process(int&&):0
    myforward(2);  //右值经过forward函数转交给process函数，却称为了一个左值，
    //原因是该右值有了名字  所以是 process(int&):2
    myforward(move(a));  // 同上，在转发的时候右值变成了左值  process(int&):0
    // forward(a) // 错误用法，右值引用不接受左值
}
```

上面的例子就是不完美转发，而c++中提供了一个std::forward()模板函数解决这个问题。将上面的myforward()函数简单改写一下：

```cpp
void myforward(int&& i){
    cout << "myforward(int&&):" << i << endl;
    process(std::forward<int>(i));
}

myforward(2); // process(int&&):2
```

上面修改过后还是不完美转发，myforward()函数能够将右值转发过去，但是并不能够转发左值，解决办法就是借助universal references通用引用类型和std::forward()模板函数共同实现完美转发：

```cpp
#include <iostream>
#include <cstring>
#include <vector>
using namespace std;

void RunCode(int &&m) {
    cout << "rvalue ref" << endl;
}
void RunCode(int &m) {
    cout << "lvalue ref" << endl;
}
void RunCode(const int &&m) {
    cout << "const rvalue ref" << endl;
}
void RunCode(const int &m) {
    cout << "const lvalue ref" << endl;
}

// 这里利用了universal references，如果写T&,就不支持传入右值，而写T&&，既能支持左值，又能支持右值
template<typename T>
void perfectForward(T && t) {
    RunCode(forward<T> (t));
}

template<typename T>
void notPerfectForward(T && t) {
    RunCode(t);
}

int main()
{
    int a = 0;
    int b = 0;
    const int c = 0;
    const int d = 0;

    notPerfectForward(a); // lvalue ref
    notPerfectForward(move(b)); // lvalue ref
    notPerfectForward(c); // const lvalue ref
    notPerfectForward(move(d)); // const lvalue ref

    cout << endl;
    perfectForward(a); // lvalue ref
    perfectForward(move(b)); // rvalue ref
    perfectForward(c); // const lvalue ref
    perfectForward(move(d)); // const rvalue ref
}
```

上面的代码测试结果表明，在universal references和std::forward的合作下，能够完美的转发这4种类型。

emplace_back减少内存拷贝和移动
---
我们之前使用vector一般都喜欢用`push_back()`，由上文可知容易发生无谓的拷贝，解决办法是为自己的类增加移动拷贝和赋值函数，但其实还有更简单的办法！就是使用`emplace_back()`替换`push_back()`，如下面的例子：

```cpp
#include <iostream>
#include <cstring>
#include <vector>
using namespace std;

class A {
public:
    A(int i){
//        cout << "A()" << endl;
        str = to_string(i);
    }
    ~A(){}
    A(const A& other): str(other.str){
        cout << "A&" << endl;
    }

public:
    string str;
};

int main()
{
    vector<A> vec;
    vec.reserve(10);
    for(int i=0;i<10;i++){
        vec.push_back(A(i)); //调用了10次拷贝构造函数
//        vec.emplace_back(i);  //一次拷贝构造函数都没有调用过
    }
    for(int i=0;i<10;i++)
        cout << vec[i].str << endl;
}
```

可以看到效果是明显的，虽然没有测试时间，但是确实可以减少拷贝。`emplace_back()`可以直接通过构造函数的参数构造对象，但前提是要有对应的构造函数。

对于map和set，可以使用`emplace()`。基本上`emplace_back()`对应`push_back()`, `emplce()`对应`insert()`。

移动语义对`swap()`函数的影响也很大，之前实现swap可能需要三次内存拷贝，而有了移动语义后，就可以实现高性能的交换函数了。

```cpp
template <typename T>
void swap(T& a, T& b)
{
    T tmp(std::move(a));
    a = std::move(b);
    b = std::move(tmp);
}
```

如果T是可移动的，那么整个操作会很高效，如果不可移动，那么就和普通的交换函数是一样的，不会发生什么错误，很安全。

总结
---
* 由两种值类型，左值和右值。
* 有三种引用类型，左值引用、右值引用和通用引用。左值引用只能绑定左值，右值引用只能绑定右值，通用引用由初始化时绑定的值的类型确定。
* 左值和右值是独立于他们的类型的，右值引用可能是左值可能是右值，如果这个右值引用已经被命名了，他就是左值。
* 引用折叠规则：所有的右值引用叠加到右值引用上仍然是一个右值引用，其他引用折叠都为左值引用。当T&&为模板参数时，输入左值，它将变成左值引用，输入右值则变成具名的右值应用。
* 移动语义可以减少无谓的内存拷贝，要想实现移动语义，需要实现移动构造函数和移动赋值函数。
* `std::move()`将一个左值转换成一个右值，强制使用移动拷贝和赋值函数，这个函数本身并没有对这个左值什么特殊操作。
* `std::forward()`和 **universal references** 通用引用共同实现完美转发。
* 用`empalce_back()`替换`push_back()`增加性能。