---
title: 【C++】STL容器总结
date: 2022-01-19 13:34:04
tags: C++
---
STL简介
---
1. STL（Standard Template Library），即标准模板库，是一个高效的C++程序库，包含了诸多常用的基本数据结构和基本算法。为广大C++程序员们提供了一个可扩展的应用框架，高度体现了软件的可复用性。
2. 从逻辑层次来看，在STL中体现了泛型化程序设计的思想（generic programming）。在这种思想里，大部分基本算法被抽象，被泛化，独立于与之对应的数据结构，用于以相同或相近的方式处理各种不同情形。
3. 从实现层次看，整个STL是以一种类型参数化（type parameterized）的方式实现的，基于模板（template）。
<!--more-->
STL有六大组件，但主要包含容器、迭代器和算法三个部分。
* **容器（Containers）**：用来管理某类对象的集合。每一种容器都有其优点和缺点，所以为了应付程序中的不同需求，STL 准备了七种基本容器类型。
* **迭代器（Iterators）**：用来在一个对象集合的元素上进行遍历动作。这个对象集合或许是个容器，或许是容器的一部分。每一种容器都提供了自己的迭代器，而这些迭代器了解该种容器的内部结构。
* **算法（Algorithms）**：用来处理对象集合中的元素，比如 Sort，Search，Copy，Erase 那些元素。通过迭代器的协助，我们只需撰写一次算法，就可以将它应用于任意容器之上，这是因为所有容器的迭代器都提供一致的接口。
**STL 的基本观念就是将数据和操作分离。数据由容器进行管理，操作则由算法进行，而迭代器在两者之间充当粘合剂，**使任何算法都可以和任何容器交互运作。

容器（Containers）
---
容器用来管理某类对象。为了应付程序中的不同需求，STL 准备了两类共七种基本容器类型：
* 序列式容器（Sequence containers），此为可序群集，其中每个元素均有固定位置—取决于插入时机和地点，和元素值无关。如果你以追加方式对一个群集插入六个元素，它们的排列次序将和插入次序一致。**STL提供了三个序列式容器：向量（vector）、双端队列（deque）、列表（list），**此外你也可以把 string 和 array 当做一种序列式容器。
* 关联式容器（Associative containers），此为已序群集，元素位置取决于特定的排序准则以及元素值，和插入次序无关。如果你将六个元素置入这样的群集中，它们的位置取决于元素值，和插入次序无关。**STL提供了四个关联式容器：集合（set）、多重集合（multiset）、映射（map）和多重映射（multimap）。**
示意图如下：
![](/images/STL容器示意图.png)
### vector（向量）
vector（向量）: 是一种序列式容器，事实上和数组差不多，但它比数组更优越。一般来说数组不能动态拓展，因此在程序运行的时候不是浪费内存，就是造成越界。而 vector 正好弥补了这个缺陷，当内存空间不够时，需要重新申请一块足够大的内存并进行内存的拷贝。
#### 特点
* 拥有一段连续的内存空间，因此它能非常好的支持随机访问，即`[]`操作符和`.at()`，随机访问快。（优点）
* 当向其头部或中间插入或删除元素时，为了保持原本的相对次序，插入或删除点之后的所有元素都必须移动，所以插入或删除的效率比较低。（缺点）
* 在后面插入删除元素最快，此时一般不需要移动内存。（优点）
总结：相当于可拓展的数组（动态数组），随机访问快，在头部和中间插入或删除效率低，但在尾部插入或删除效率高。
#### 适用场景
适用于对象简单，变化较小，并且频繁随机访问的场景。
#### 迭代器
* 开始指针：`vec.begin();`
* 末尾指针：`vec.end();` // 指向最后一个元素的下一个位置
* 指向常量的开始指针：`vec.cbegin();` // 意思就是不能通过这个指针来修改所指的内容，但还是可以通过其他方式修改的，而且指针也是可以移动的。
* 指向常量的末尾指针：`vec.cend();`
```cpp
#include <iostream>
#include <vector>

using namespace std;

int main(int argc, char* argv[])
{
    // Create and populate the vector
	vector<int> vecTemp;
	for (int i = 0; i<6; i++)
	{
		vecTemp.push_back(i);
	}

    // Display contents of vector
	cout <<"Original deque: ";    
	vector<int>::iterator it;
	for (it = vecTemp.begin(); it!=vecTemp.end(); it++)
	{
		cout <<*it <<" ";
	}

	return 0;
}

/*
输出结果：
Original deque: 0 1 2 3 4 5
*/
```
#### 初始化
使用之前必须加相应容器的头文件：
```cpp
#include <vector> // vector属于std命名域的，因此需要通过命名限定，例如using std::vector;
```
定义的实现代码如下：
```cpp
vector<int> a; // 定义一个int类型的向量a
vector<int> a(10); // 定义一个int类型的向量a，并设置初始大小为10
vector<int> a(10, 1); // 定义一个int类型的向量a，并设置初始大小为10且初始值都为1
vector<int> b(a); // 定义并用向量a初始化向量b
vector<int> b(a.begin(), a.begin()+3); // 将a向量中从第0个到第2个(共3个)作为向量b的初始值
```
除此之外，还可以直接使用数组来初始化向量：
```cpp
int n[] = {1, 2, 3, 4, 5} ;
// 将数组n的前5个元素作为向量a的初值
// 说明：当然不包括arr[4]元素，末尾指针都是指结束元素的下一个元素，
// 这个主要是为了和vec.end()指针统一。
vector<int> a(n, n+5) ;              
vector<int> a(&n[1], &n[4]) ;        // 将n[1]、n[2]、n[3]作为向量a的初值
```
#### 函数
* 容器容量：`vec.capacity();` // 指在不分配更多内存的情况下，容器可以保存的最多元素个数，即预分配的内存空间，与 size() 不同
* 容器大小：`vec.size();` // 指它实际包含的元素个数
* 容器最大容量：`vec.max_size();`
* 更改容器大小：`vec.resize();`
* 容器判空：`vec.empty();`
* 末尾添加元素：`vec.push_back(const T& x);`
* 任意位置插入一个元素：`vec.insert(iterator it, const T& x);`
* 任意位置插入 n 个相同元素：`vec.insert(iterator it, int n, const T& x);`
* 插入另一个向量的 [forst,last] 间的数据：`vec.insert(iterator it, iterator first, iterator last);`
* 末尾删除元素：`vec.pop_back();`
* 任意位置删除一个元素：`vec.erase(iterator it);`
* 删除 [first,last] 之间的元素：`vec.erase(iterator first, iterator last);`
* 清空所有元素：`vec.clear();`
* 下标访问：`vec[1];` // 并不会检查是否越界
* at 方法访问：`vec.at(1);` // 以上两者的区别就是 at 会检查是否越界，是则抛出 out of range 异常
* 访问第一个元素：`vec.front();`
* 访问最后一个元素：`vec.back();`
* 返回一个指针：`int* p = vec.data();` // 可行的原因在于 vector 在内存中就是一个连续存储的数组，所以可以返回一个指针指向这个数组。这是是 C++11 的特性。
* 多个元素赋值：`vec.assign(int nSize, const T& x);` // 类似于初始化时用数组进行赋值
```cpp
vector<int> vec;
vec.assign(3, 1); // vec:[1 1 1]
```
* 交换两个同类型容器的元素：`swap(vector&);`
```cpp
vec1.swap(vec2); // 交换vec1和vec2的元素
```
#### 算法
* 遍历元素
```cpp
vector<int>::iterator it;
for (it = vec.begin(); it != vec.end(); it++)
    cout << *it << endl;
// 或者
for (int i = 0; i < vec.size(); i++) {
    cout << vec.at(i) << endl;
}
```
* 元素翻转
```cpp
#include <algorithm>
reverse(vec.begin(), vec.end());
```
* 元素排序
```cpp
#include <algorithm>
sort(vec.begin(), vec.end()); // 采用的是从小到大的排序

// 如果想从大到小排序，可以采用先排序后反转的方式，也可以采用下面方法:
// 自定义从大到小的比较器，用来改变排序方式
bool Comp(const int& a, const int& b) 
{
    return a > b;
}

sort(vec.begin(), vec.end(), Comp);
```
#### 例子
以下例子针对整型定义了一个 vector，插入 6 个元素，然后打印所有元素：
```cpp
#include <iostream>
#include <vector>

using namespace std;

int main(int argc, char* argv[])
{       
        vector<int> vecTemp;
        
        for (int i = 0; i<6; i++)
                vecTemp.push_back(i);

        for (int i = 0; i<vecTemp.size(); i++)
                cout << vecTemp[i] <<" "; // 输出：0 1 2 3 4 5

        return 0;
}
```

### deque（双端队列）
deque（double-ended queue）是双向开口的连续内存空间（动态将多个连续空间通过指针数组接合在一起），随时可以增加一段新的空间。deque的最大任务就是在这些分段的连续空间上，维护其整体连续的假象，并提供随机存取的接口。
#### 特点
* 一旦要在 deque 的头部和尾部增加新空间，便配置一段定量连续空间，串在整个 deque 的头部或尾部，因此不论在头部或尾部插入元素都十分迅速。 (优点）
* 在中间部分安插元素则比较费时，因为必须移动其它元素。（缺点）
* deque 是 list 和 vector 的折中方案。兼有 list 的优点，也有 vector 随机访问效率高的优点。
总结：支持随机访问，但效率没有 vector 高，在头部和尾部插入或删除效率高，但在中间插入或删除效率低。
#### 适用场景
适用于既要频繁随机访问，又要关心两端数据的插入与删除的场景。
#### 迭代器
* 开始迭代器指针：`deq.begin();`
* 末尾迭代器指针：`deq.end(); // 指向最后一个元素的下一个位置`
* 指向常量的开始迭代器指针：`deq.cbegin(); // 意思就是不能通过这个指针来修改所指的内容，但还是可以通过其他方式修改的，而且指针也是可以移动的。`
* 指向常量的末尾迭代器指针：`deq.cend();`
* 反向迭代器指针，指向最后一个元素：`deq.rbegin();`
* 反向迭代器指针，指向第一个元素的前一个元素：`deq.rend();`
```cpp
#include <iostream>
#include <deque>

using namespace std；

int main(int argc, char* argv[])
{
	// Create and populate the deque
	deque<int> dequeTemp;
	for (int i = 0; i<6; i++)
	{
		dequeTemp.push_back(i);
	}

	// Display contents of deque
	cout <<"Original deque: ";
	deque<int>::iterator it;
	for (it = dequeTemp.begin(); it != dequeTemp.end(); it++)
	{
		cout <<*it <<" ";
	}
	cout <<endl;

	return 0;
}
/*
输出结果：
Original deque: 0 1 2 3 4 5
*/
```
#### 初始化
使用之前必须加相应容器的头文件：
```cpp
#include <deque> // deque属于std命名域的，因此需要通过命名限定，例如using std::deque;
```
定义的实现代码如下：
```cpp
deque<int> a; // 定义一个int类型的双端队列a
deque<int> a(10); // 定义一个int类型的双端队列a，并设置初始大小为10
deque<int> a(10, 1); // 定义一个int类型的双端队列a，并设置初始大小为10且初始值都为1
deque<int> b(a); // 定义并用双端队列a初始化双端队列b
deque<int> b(a.begin(), a.begin()+3); // 将双端队列a中从第0个到第2个(共3个)作为双端队列b的初始值
```
除此之外，还可以直接使用数组来初始化向量：
```cpp
int n[] = { 1, 2, 3, 4, 5 };
// 将数组n的前5个元素作为双端队列a的初值
// 说明：当然不包括arr[4]元素，末尾指针都是指结束元素的下一个元素，
// 这个主要是为了和deque.end()指针统一。
deque<int> a(n, n + 5); 
deque<int> a(&n[1], &n[4]); // 将n[1]、n[2]、n[3]作为双端队列a的初值
```
#### 函数
* 容器大小：`deq.size();`
* 容器最大容量：`deq.max_size();`
* 更改容器大小：`deq.resize();`
* 容器判空：`deq.empty();`
* 减少容器大小到满足元素所占存储空间的大小：`deq.shrink_to_fit();`
* 头部添加元素：`deq.push_front(const T& x);`
* 末尾添加元素：`deq.push_back(const T& x);`
* 任意位置插入一个元素：`deq.insert(iterator it, const T& x);`
* 任意位置插入 n 个相同元素：`deq.insert(iterator it, int n, const T& x);`
* 插入另一个向量的 [forst,last] 间的数据：`deq.insert(iterator it, iterator first, iterator last);`
* 头部删除元素：`deq.pop_front();`
* 末尾删除元素：`deq.pop_back();`
* 任意位置删除一个元素：`deq.erase(iterator it);`
* 删除 [first,last] 之间的元素：`deq.erase(iterator first, iterator last);`
* 清空所有元素：`deq.clear();`
* 下标访问：`deq[1]; // 并不会检查是否越界`
* at 方法访问：`deq.at(1); // 以上两者的区别就是 at 会检查是否越界，是则抛出 out of range 异常`
* 访问第一个元素：`deq.front();`
* 访问最后一个元素：`deq.back();`
* 多个元素赋值：`deq.assign(int nSize, const T& x); // 类似于初始化时用数组进行赋值`
* 交换两个同类型容器的元素：`swap(deque&);`
#### 算法
* 遍历元素
```cpp
deque<int>::iterator it;
for (it = deq.begin(); it != deq.end(); it++)
    cout << *it << endl;
// 或者
for (int i = 0; i < deq.size(); i++) {
    cout << deq.at(i) << endl;
}
```
* 元素翻转
```cpp
#include <algorithm>
reverse(deq.begin(), deq.end());
```
* 元素排序
```cpp
#include <algorithm>
sort(deq.begin(), deq.end()); // 采用的是从小到大的排序

// 如果想从大到小排序，可以采用先排序后反转的方式，也可以采用下面方法:
// 自定义从大到小的比较器，用来改变排序方式
bool Comp(const int& a, const int& b) {
    return a > b;
}

sort(deq.begin(), deq.end(), Comp);
```
#### 例子
以下例子声明了一个浮点类型的 deque，并在容器尾部插入 6 个元素，最后打印出所有元素：
```cpp
#include <iostream>
#include <deque>

using namespace std;

int main(int argc, char* argv[])
{
        deque<float> dequeTemp;

        for (int i = 0; i<6; i++)
                dequeTemp.push_back(i);

        for (int i = 0; i<dequeTemp.size(); i++)
                cout << dequeTemp[i] << " "; // 输出：0 1 2 3 4 5

        return 0;
}
```
#### 总结
可以看到，deque 与 vector 的用法基本一致，除了以下几处不同：
* deque 没有 capacity() 函数，而 vector 有；
* deque 有 push_front() 和 pop_front() 函数，而 vector 没有；
* deque 没有 data() 函数，而 vector 有。

### list（列表）
List 由双向链表（doubly linked list）实现而成，元素存放在堆中，每个元素都是放在一块内存中。没有空间预留习惯，所以每分配一个元素都会从内存中分配，每删除一个元素都会释放它占用的内存。
#### 特点
* 内存空间可以是不连续的，通过指针来进行数据的访问，这个特点使得它的随机存取变得非常没有效率，因此它没有提供 [] 操作符的重载。（缺点）
* 由于链表的特点，在任意位置的插入和删除效率都较高。（优点）
* 只支持首尾两个元素的直接存取，想获取其他元素（访问时间一样），则需要遍历链表。（缺点）
总结：不支持随机访问，在任意位置的插入和删除效率都较高。
#### 适用场景
适用于经常进行插入和删除操作并且不经常随机访问的场景。
#### 迭代器
* 开始迭代器指针：`lst.begin();`
* 末尾迭代器指针：`lst.end(); // 指向最后一个元素的下一个位置`
* 指向常量的开始迭代器指针：`lst.cbegin(); // 意思就是不能通过这个指针来修改所指的内容，但还是可以通过其他方式修改的，而且指针也是可以移动的。`
* 指向常量的末尾迭代器指针：`lst.cend();`
* 反向迭代器指针，指向最后一个元素：`lst.rbegin();`
* 反向迭代器指针，指向第一个元素的前一个元素：`lst.rend();`
```cpp
#include <iostream>
#include <list>

using namespace std;

int main(int argc, char* argv[])
{
	// Create and populate the list
	list<int> listTemp;
	for (int i = 0; i<6; i++)
	{
		listTemp.push_back(i);
	}

	// Display contents of list
	cout << "Original list: ";
	list<int>::iterator it;
	for (it = listTemp.begin(); it != listTemp.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;

	// Insert five 9 into the list
	list<int>::iterator itStart = listTemp.begin();
	listTemp.insert(itStart,5,9);

	// Display the result
	cout << "Result of list: ";
	for (it = listTemp.begin(); it != listTemp.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;

	return 0;
}
/*
输出结果：
Original list: 0 1 2 3 4 5
Result of list: 9 9 9 9 9 0 1 2 3 4 5
*/
```
#### 初始化
* 使用之前必须加相应容器的头文件：
```cpp
#include <list> // list属于std命名域的，因此需要通过命名限定，例如using std::list;
```
* 定义的代码如下：
```cpp
list<int> a; // 定义一个int类型的列表a
list<int> a(10); // 定义一个int类型的列表a，并设置初始大小为10
list<int> a(10, 1); // 定义一个int类型的列表a，并设置初始大小为10且初始值都为1
list<int> b(a); // 定义并用列表a初始化列表b
deque<int> b(a.begin(), ++a.end()); // 将列表a中的第1个元素作为列表b的初始值
```
* 除此之外，还可以直接使用数组来初始化向量：
```cpp
int n[] = { 1, 2, 3, 4, 5 };
list<int> a(n, n + 5); // 将数组n的前5个元素作为列表a的初值
```
#### 函数
* 容器大小：`lst.size();`
* 容器最大容量：`lst.max_size();`
* 更改容器大小：`lst.resize();`
* 容器判空：`lst.empty();`
* 头部添加元素：`lst.push_front(const T& x);`
* 末尾添加元素：`lst.push_back(const T& x);`
* 任意位置插入一个元素：`lst.insert(iterator it, const T& x);`
```cpp
lst.insert(lst.begin(), 2); // 在第一个元素前插入一个2
```
* 任意位置插入 n 个相同元素：`lst.insert(iterator it, int n, const T& x);`
```cpp
lst.insert(lst.begin(), 3, 9); // 在第一个元素前插入3个9
```
* 插入另一个向量的 [forst,last] 间的数据：`lst.insert(iterator it, iterator first, iterator last);`
```cpp
lst.insert(lst.begin(), lst2.begin(), ++lst2.begin());
```
* 头部删除元素：`lst.pop_front();`
* 末尾删除元素：`lst.pop_back();`
* 任意位置删除一个元素：`lst.erase(iterator it);`
* 删除 [first,last] 之间的元素：`lst.erase(iterator first, iterator last);`
* 清空所有元素：`lst.clear();`
* 访问第一个元素：`lst.front();`
* 访问最后一个元素：`lst.back();`
* 多个元素赋值：`lst.assign(int nSize, const T& x); // 类似于初始化时用数组进行赋值`
* 交换两个同类型容器的元素：`swap(list&, list&); 或 lst.swap(list&);`
* 合并两个列表的元素（默认升序排列）：`lst.merge();`
* 在任意位置拼接入另一个list：`lst.splice(iterator it, list&);`
* 删除容器中相邻的重复元素：`lst.unique();`
#### 算法
* 遍历元素
```cpp
list<int>::iterator it;
for (it = lst.begin(); it != lst.end(); it++)
    cout << *it << endl;
```
* 元素翻转
```cpp
#include <algorithm>
reverse(lst.begin(), lst.end());
```
* 元素排序
```cpp
#include <algorithm>
sort(lst.begin(), lst.end()); // 采用的是从小到大的排序

// 如果想从大到小排序，可以采用先排序后反转的方式，也可以采用下面方法:
// 自定义从大到小的比较器，用来改变排序方式
bool Comp(const int& a, const int& b) 
{
    return a > b;
}

sort(lst.begin(), lst.end(), Comp);
```
#### 例子
以下例子产生一个空 list，准备放置字符，然后将 'a' 至 'z' 的所有字符插入其中，利用循环每次打印并移除集合的第一个元素，从而打印出所有元素：
```cpp
#include <iostream>
#include <list>

using namespace std;

int main(int argc, char* argv[])
{
	list<char> listTemp;

	for (char c = 'a'; c <= 'z'; ++c)
		listTemp.push_back(c);

	while (!listTemp.empty())
	{
		cout <<listTemp.front() << " ";
		listTemp.pop_front();
	}

	return 0;
}
```
#### 总结
可以看到，list 与 vector、deque 的用法基本一致，除了以下几处不同：
* list 为双向迭代器，故不支持it+=i；
* list 不支持下标访问和at方法访问。

### set（集合）
set（集合）顾名思义，就是数学上的集合——每个元素最多只出现一次，并且 set 中的元素已经从小到大排好序。
#### 特点
* 使用红黑树实现，其内部元素依据其值自动排序，每个元素值只能出现一次，不允许重复。
* 每次插入值的时候，都需要调整红黑树，效率有一定影响。（缺点）
* map 和 set 的插入或删除效率比用其他序列容器高，因为对于关联容器来说，不需要做内存拷贝和内存移动。（优点）
总结：由红黑树实现，其内部元素依据其值自动排序，每个元素值只能出现一次，不允许重复，且插入和删除效率比用其他序列容器高。
#### 适用场景
适用于经常查找一个元素是否在某集合中且需要排序的场景。
#### 迭代
* 开始迭代器指针：`st.begin();`
* 末尾迭代器指针：`st.end(); // 指向最后一个元素的下一个位置`
* 指向常量的开始迭代器指针：`st.cbegin(); // 意思就是不能通过这个指针来修改所指的内容，但还是可以通过其他方式修改的，而且指针也是可以移动的。`
* 指向常量的末尾迭代器指针：`lst.cend();`
* 反向迭代器指针，指向最后一个元素：`st.rbegin();`
* 反向迭代器指针，指向第一个元素的前一个元素：`st.rend();`
* 返回最后一个 key<=keyElem 元素的迭代器：`st.lower_bound(keyElem);`
* 返回第一个 key>keyElem 元素的迭代器：`st.upper_bound(keyElem);`
* 返回容器中 key 与 keyElem 相等的上下限的两个迭代器，这两个迭代器被放在对组（pair）中：`st.equal_range(keyElem);`
```cpp
#include <iostream>
#include <set>

using namespace std;

int main(int argc, char* argv[])
{
	// Create and populate the set
	set<char> setTemp;
	for (int i = 0; i<6; i++)
	{
		setTemp.insert('F'-i);
	}

	// Display contents of set
	cout <<"Original set: ";
	set<char>::iterator it;
	for (it = setTemp.begin(); it != setTemp.end(); it++)
	{
		cout <<*it <<" ";
	}
	cout <<endl;

	return 0;
}
/*
输出结果：
Original set: A B C D E F
*/
```
#### 初始化
* 使用之前必须加相应容器的头文件：
```cpp
#include <set> // set属于std命名域的，因此需要通过命名限定，例如using std::set;
```
* 定义的代码如下：
```cpp
set<int> a; // 定义一个int类型的集合a
// set<int> a(10); // error，未定义这种构造函数
// set<int> a(10, 1); // error，未定义这种构造函数
set<int> b(a); // 定义并用集合a初始化集合b
set<int> b(a.begin(), a.end()); // 将集合a中的所有元素作为集合b的初始值
```
* 除此之外，还可以直接使用数组来初始化向量：
```cpp
int n[] = { 1, 2, 3, 4, 5 };
list<int> a(n, n + 5); // 将数组n的前5个元素作为集合a的初值
```
#### 函数
* 容器大小：`st.size();`
* 容器最大容量：`st.max_size();`
* 容器判空：`st.empty();`
* 查找键 key 的元素个数：`st.count(key);`
* 在容器中插入元素：`st.insert(const T& x);`
* 任意位置插入一个元素：`st.insert(iterator it, const T& x);`
* 删除容器中值为 elem 的元素：`st.erase(const T& elem);`
* 删除it迭代器所指的元素：`st.erase(iterator it);`
* 删除区间 [first,last] 之间的所有元素：`st.erase(iterator first, iterator last);`
* 清空所有元素：`st.clear();`
* 查找键 key 是否存在,若存在，返回该键的元素的迭代器；若不存在，返回`set.end()`：`st.find(key);`
* 交换两个同类型容器的元素：`swap(set&, set&);`或`lst.swap(set&);`
#### 算法
* 遍历元素
```cpp
set<int>::iterator it;
for (it = st.begin(); it != st.end(); it++)
    cout << *it << endl;
```
#### 例子
下面的例子演示 set（集合）的两个特点：
```cpp
#include <iostream>
#include <set>

using namespace std;

int main(int argc, char* argv[])
{
	set<int> setTemp;

	setTemp.insert(3);
	setTemp.insert(1);
	setTemp.insert(2);
	setTemp.insert(1);

	set<int>::iterator it;
	for (it = setTemp.begin(); it != setTemp.end(); it++)
	{
		cout << *it << " ";
	}

	return 0;
}
```
输出结果：1 2 3。一共插入了 4 个数，但是集合中只有 3 个数并且是有序的，可见之前说过的 set 集合的两个特点，有序和不重复。
当 set 集合中的元素为结构体时，该结构体必须实现运算符 ‘<’ 的重载：
```cpp
#include <iostream>
#include <set>
#include <string>

using namespace std;

struct People
{
	string name;
	int age;

	bool operator <(const People p) const
	{
		return age < p.age;
	}
};

int main(int argc, char* argv[])
{
	set<People> setTemp;

	setTemp.insert({"张三",14});
	setTemp.insert({ "李四", 16 });
	setTemp.insert({ "隔壁老王", 10 });

	set<People>::iterator it;
	for (it = setTemp.begin(); it != setTemp.end(); it++)
	{
		printf("姓名：%s 年龄：%d\n", (*it).name.c_str(), (*it).age);
	}

	return 0;
}

/*
输出结果
姓名：王二麻子 年龄：10
姓名：张三 年龄：14
姓名：李四 年龄：16 
*/ 
```
可以看到结果是按照年龄由小到大的顺序排列。另外 string 要使用c_str()转换一下，否则打印出的是乱码。
另外 Multiset 和 set 相同，只不过它允许重复元素，也就是说 multiset 可包括多个数值相同的元素。这里不再做过多介绍。
#### 总结
可以看到，set 与序列式容器的用法有以下几处不同：
* set 不支持 resize() 函数；
* set 容器不提供下标操作符。为了通过键从 set 中获取元素，可使用 find 运算；
* set 只能使用insert的两种重载函数插入，不支持 push_back() 和 push_front() 函数；
* set 不支持 STL 里的 reverse 和 sort 算法；
* set 能不通过迭代器，只通过元素值来删除该元素；
* set 只支持默认构造函数和拷贝构造函数，没有其它重载的构造函数。

### map（字典）
map 由红黑树实现，其元素都是 “键值/实值” 所形成的一个对组（key/value pairs)。
map 主要用于资料一对一映射的情况，map 内部自建一颗红黑树，这颗树具有对数据自动排序的功能，所以在 map 内部所有的数据都是有序的。比如一个班级中，每个学生的学号跟他的姓名就存在着一对一映射的关系。
#### 特点
* 每个元素都有一个键，且只能出现一次，不允许重复。
* 根据 key 值快速查找记录，查找的复杂度基本是 O(logN)，如果有 1000 个记录，二分查找最多查找 10次(1024)。（优点）
* 每次插入值的时候，都需要调整红黑树，效率有一定影响。（缺点）
* 增加和删除节点对迭代器的影响很小，除了那个操作节点，对其他的节点都没有什么影响。（优点）
* 对于迭代器来说，可以修改实值，而不能修改 key。
总结：元素为键值对，key 和 value 可以是任意你需要的类型，每个元素都有一个键，且只能出现一次，不允许重复，根据 key 快速查找记录。
#### 适用场景
适用于需要存储一个数据字典，并要求方便地根据key找value的场景。
#### 迭代器
* 开始迭代器指针：`mp.begin();`
* 末尾迭代器指针：`mp.end(); // 指向最后一个元素的下一个位置`
* 指向常量的开始迭代器指针：`mp.cbegin(); // 意思就是不能通过这个指针来修改所指的内容，但还是可以通过其他方式修改的，而且指针也是可以移动的。`
* 指向常量的末尾迭代器指针：`mp.cend();`
* 反向迭代器指针，指向最后一个元素：`mp.rbegin();`
* 反向迭代器指针，指向第一个元素的前一个元素：`mp.rend();`
* 返回最后一个 key<=keyElem 元素的迭代器：`mp.lower_bound(keyElem);`
* 返回第一个 key>keyElem 元素的迭代器：`mp.upper_bound(keyElem);`
* 返回容器中 key 与 keyElem 相等的上下限的两个迭代器，这两个迭代器被放在对组（pair）中：`mp.equal_range(keyElem);`
```cpp
#include <iostream>
#include <map>

using namespace std;

typedef map<int, char> MyMap;

int main(int argc, char* argv[])
{
	// Create and populate the map
	MyMap mapTemp;
	for (int i = 0; i<6; i++)
	{
		mapTemp[i] = ('F'-i);
	}

	// Display contents of map
	cout <<"Original map: " <<endl;
	MyMap::iterator it;
	for (it = mapTemp.begin(); it != mapTemp.end(); it++)
	{
		cout << (*it).first << " --> ";
		cout << (*it).second << std::endl;
	}
	cout <<endl;

	return 0;
}
/*
输出结果：
Original map:
0 --> F
1 --> E
2 --> D
3 --> C
4 --> B
5 --> A
*/
```
#### 初始化
使用之前必须加相应容器的头文件：
```cpp
#include <map> // map属于std命名域的，因此需要通过命名限定，例如using std::map;
```
定义的代码如下：
```cpp
map<int, string> a; // 定义一个int类型的映射a
// map<int, string> a(10); // error，未定义这种构造函数
// map<int, string> a(10, 1); // error，未定义这种构造函数
map<int, string> b(a); // 定义并用映射a初始化映射b
// map<int, string> b(a.begin(), a.end());  // error，未定义这种构造函数
```
#### 函数
* 容器大小：`mp.size();`
* 容器最大容量：`mp.max_size();`
* 容器判空：`mp.empty();`
* 查找键 key 的元素个数：`mp.count(key);`
* 在容器中插入元素：`mp.insert(const T& x);`
* 任意位置插入一个元素：`mp.insert(iterator it, const T& x);`
* 删除键值为 keyValue 的元素：`mp.erase(const T& keyValue);`
* 删除迭代器所指的元素：`mp.erase(iterator it);`
* 删除区间[first,last]之间的所有元素：`mp.erase(iterator first, iterator last);`
* 清空所有元素：`mp.clear();`
* 查找键 key 是否存在，若存在，返回该键的元素的迭代器；若不存在，返回`map.end()`：`mp.find(key);`
* 交换两个同类型容器的元素：`swap(map&, map&);`或`mp.swap(map&);`
#### 算法
* 遍历元素
```cpp
map<int>::iterator it;
for (it = mp.begin(); it != mp.end(); it++)
    cout << it->second << endl;
```
#### 例子
```cpp
#include "stdafx.h"
#include <iostream>
#include <map>
#include <string>

using namespace std;

int main(int argc, char* argv[])
{
	map<int, string> mapTemp;

	mapTemp.insert({ 5,"张三" });
	mapTemp.insert({ 3, "李四"});
	mapTemp.insert({ 4, "隔壁老王" });

	map<int, string>::iterator it;
	for (it = mapTemp.begin(); it != mapTemp.end(); it++)
	{
		printf("学号：%d 姓名：%s\n", (*it).first, (*it).second.c_str());
	}

	return 0;
}

/*
输出结果：
学号：3 姓名：李四
学号：4 姓名：隔壁老王
学号：5 姓名：张三
*/
```
multimap 和 map 相同，但允许重复元素，也就是说 multimap 可包含多个键值（key）相同的元素。这里不再做过多介绍。
#### 总结
可以看到，map 与set的用法基本一致，只有以下一处不同：
* map 可以像数组那样插入元素，而 set 不行。

### string（字符串）
特殊容器。
#### 迭代器
```cpp
#define  _SCL_SECURE_NO_WARNINGS
 
#include <iostream>
#include <string>	//要与c语言里面的#include <string.h>区分
using namespace std;
 
int main(){
 
	string str("abcdefg");
	string::iterator ite;
 
	ite = str.begin();
	for (size_t i = 0; i < str.size(); i++){
		cout << *ite;
		ite++;
	}
	ite = str.begin();
	cout << endl;
	for (size_t i = 0; i < str.size(); i++){
		cout << ite[i];
	}
	cout << endl;
	ite = str.begin();
	for (; ite != str.end(); ite++){
		cout << *ite;
	}
 
	cout << endl;
 
	str.append(10, 'a');
	//cout << ite[16] << endl; 迭代器失效
 
	system("pause");
	return 0;
}
```
#### 初始化
使用之前必须加上相应容器的头文件：
```cpp
#include <string>
```
定义的实现代码如下：
```cpp
string s1 = "wty";
string s2(s1);
string s3 = s2;
string s4("wangtianyang");
string s5(10, 'c');
```
#### 函数
* 字符串长度：`str.length()`
* 字符串长度：`str.size() \\ 同上`
* at方法访问：`str.at(1)`
* 更改容器大小：`str.resize(length + 10, 'a'); //重新分配空间，将多余的十个空间分配a`
* 清空容器：`str.clear()`
* 判空：`str.empty()`
* 末尾添加元素：`str.append(" hell0")`

总结
---
### 各容器的特点总结
* vector 支持随机访问，在头部和中间插入或删除效率低，但在尾部插入或删除效率高。
* 支持随机访问，但效率没有 vector 高，在头部和尾部插入或删除效率高，但在中间插入或删除效率低。
* list 不支持随机访问，在任意位置的插入和删除效率都较高。
* set 由红黑树实现，其内部元素依据其值自动排序，每个元素值只能出现一次，不允许重复，且插入和删除效率比用其他序列容器高。
* map 的元素为键值对，key 和 value 可以是任意你需要的类型，每个元素都有一个键，且只能出现一次，不允许重复，根据 key 快速查找记录。
在实际使用过程中，到底选择这几种容器中的哪一个，应该根据遵循以下原则：
1. 如果需要高效的随机访问，不在乎插入和删除的效率，使用 vector。
2. 如果需要随机访问，并且关心两端数据的插入和删除效率，使用 deque。
3. 如果需要大量的插入和删除元素，不关心随机访问的效率，使用 list。
4. 如果经常查找一个元素是否在某集合中且需要排序，唯一存在的情况使用 set，不唯一存在的情况使用 multiset。
5. 如果打算存储数据字典，并且要求方便地根据 key 找到 value，一对一的情况使用 map，一对多的情况使用 multimap。
### 各容器的时间复杂度分析
* vector 在头部和中间位置插入和删除的时间复杂度为 O(N)，在尾部插入和删除的时间复杂度为 O(1)，查找的时间复杂度为 O(1)；
* deque 在中间位置插入和删除的时间复杂度为 O(N)，在头部和尾部插入和删除的时间复杂度为 O(1)，查找的时间复杂度为 O(1)；
* list 在任意位置插入和删除的时间复杂度都为 O(1)，查找的时间复杂度为 O(N)；
* set 和 map 都是通过红黑树实现，因此插入、删除和查找操作的时间复杂度都是 O(log N)。
### 各容器的共性
各容器一般来说都有下列函数：默认构造函数、复制构造函数、析构函数、empty()、max_size()、size()、operator=、operator<、operator<=、operator>、operator>=、operator==、operator!=、swap()。
顺序容器和关联容器都共有下列函数：
* `begin()` ：返回容器第一个元素的迭代器指针；
* `end()`：返回容器最后一个元素后面一位的迭代器指针；
* `rbegin()`：返回一个逆向迭代器指针，指向容器最后一个元素；
* `rend()`：返回一个逆向迭代器指针，指向容器首个元素前面一位；
* `clear()`：删除容器中的所有的元素；
* `erase(it)`：删除迭代器指针it处元素。
