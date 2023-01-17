---
title: 【读书笔记】《Java核心技术 卷I》
date: 2022-12-22 09:57:27
tags: Java
categories: 读书笔记
---
[Java 在线 API 文档](http://docs.oracle.com/javase/9/docs/api)

## Java 程序设计概述

### Java “白皮书”的关键术语

* 简单性
* 面向对象
* 分布式
* 健壮性
* 安全性
* 体系结构中立
* 可以执行
* 解释型
* 高性能
* 多线程
* 动态性

## Java 程序设计环境

### 使用命令行工具

```shell
javac Welcome.java
java Welcome
```

`javac` 程序是一个 Java 编译器，它将文件 Welcome.java 编译成 Welcome.class。`java` 程序启动 Java 虚拟机，虚拟机执行编译器编译到类文件中的字节码。

<!--more-->

Java 的基本程序设计结构
---

### 数据类型
#### char 类型
> p32

char 类型原本用于表示单个字符，**不过现在不一样了**。如今，有些 Unicode 字符可以用一个 char 值描述，而另外一些 Unicode 字符则需要两个 char 值。

### 变量与常量
#### 枚举类型
> p37

```java
enum Size {SMALL, MEDIUM, LARGE, EXTRA_LARGE};
Size sz = Size.MEDIUM;
```

### 运算符
#### 位运算符
> p43

位运算符包括：

```java
&  // 与
|  // 或
^  // 异或
~  // 非
>>  // 左移并用符号位填充高位
>>>  // 左移并用 0 填充高位
<<  // 右移， 并不存在 <<< 运算符
```

例：

```java
int fourthBitFromRight = (n & 0b1000) / 0b1000; 
// 当二进制表示的 n 从右往左数第 4 位为 1 时，fourthBitFromRight 为 1，否则为 0。
// 上句代码等同于：
int fourthBitFromRight = (n & (1 << 3)) >> 3;
```

> 注：应用在 bool 值上时，`&` 和 `|` 运算符也会得到一个 bool 值。这些运算符与 `&&` 和 `||` 运算符很类似，不过 `&` 和 `|` 运算符不采用“短路”的方式来求值，也就是说，**得到计算结果之前两个操作数都需要计算**。

#### 括号与运算符优先级
> p44

因为 `+=` 是右结合运算符（详细运算符优先级见本书 p44），所以表达式

```java
a += b += c
```

等价于

```java
a += (b += c)
```

也就是将 `b += c` 的结果（加上 c 之后的 b）加到 a 上。

### 字符串
> p44

Java 没有内置的字符串类型，而是在标准 Java 类库中提供了一个预定类 String。String 类的常用方法见 p49～p51。

#### 子串
> p45

```java
String a = "Hello";
String b = a.substring(0, 3);  // b is "Hel"
```

#### 拼接
> p45

Java 允许 `+` 号拼接两个字符串。此外，在 Java 11 中，还提供了一个 `repeat` 方法：

```java
String repeated = "Java".repeat(3);  // repeated is "JavaJavaJava"
```

#### 不可变字符串
> p46

**Java 中的 String 类对象是不可变的，**也就是说，不能修改字符串中的单个字符。不过可以修改字符串*变量*，让它指向另外一个字符串。

从中可以看出，与 C++ 不同的是，Java 中的字符串并不是字符数组 `char[]`，而是 `char*` 指针。

#### 检测字符串是否相等
> p47

**一定不要使用 `==` 运算符来检测两个字符串是否相等！**`==` 运算符比较的只是两个字符串的地址。

**C++ NOTE：**C++ 中重载了 string 类的 `==` 运算符，而 Java 没有。事实上，Java设计者只重载了字符串的 `+` 运算符，但没有重载**其他任何**运算符，且不允许 Java 程序员自己重载运算符。

检测两个字符串是否相等可以用 `equals` 方法：

```java
s.equals(t)
```

其中 s 和 t 可以是字符串*变量*，也可以是字符串*字面量*。如果要比较两个字符串而不区分大小写，可以用 `equalsIgnoreCase` 方法：

```java
s.equalsIgnoreCase(t)
```

#### 空串与 Null 串
> p48

空串与 Null 串并不相同：

1. 空串是指长度为 0 的字符串，仍是一个 Java 对象；
2. Null 串表示目前没有任何对象与该变量关联。

#### 码点与代码单元
> p48

从 上一小章节 [char 类型](#char 类型) 可以看到，char 数据类型是一个采用 UTF-16 编码表示 Unicode *码点*的*代码单元*。最常用的 Unicode 字符使用一个代码单元就可以表示，而辅助字符则需要一对代码单元来表示。

`str.length()` 方法只会返回字符串 `str` 的代码单元数量，而想要获取字符串的实际长度，即码点的数量，需要使用 `str.codePointCount(0, str.length())`。

同样的，`str.charAt(i)` 只会返回位置 i 处的代码单元，而想要得到第 i 个码点，应该使用 `str.codePointAt(i)`。

#### 构建字符串
> p54

StringBuilder 类可以避免拼接字符串时的时间与空间浪费：

```java
String ch = "abc";
String str = "def";

StringBuilder builder = new StringBuilder();
builder.append(ch);
builder.append(str);

// 构建完成后调用 toString 方法，将可以得到一个 String 对象
String completedString = builder.toString();
```

其他 StringBuilder 的常用方法见 p54。

### 输入与输出
#### 读取输入
> p55

输出：

```java
System.out.println()
```

输入：

```java
Scanner in = new Scanner(System.in)l
String name = in.nexLline();  // 读取一行，以换行作分隔符
String firstName = in.next();  // 读取一个单词，以空白符作分隔符
int age = in.nextInt();  // 读取一个整数
double height = in.nextDouble();  // 读取一个浮点数
```

如果想要不可见的输入，比如密码，可以使用 Console 类：

```java
Console cons = System.console();
String username = cons.readLine("User name: ");
char[] passwd = cons.readPassword("Password: ");  // 为了安全起见，返回的密码应该被存放在一个字符数组中，而不是字符串中，这样在处理完密码以后马上用一个填充值来覆盖数组元素
```

Console 类没有读取单个单词或者数值的方法。

其他输入输出方法见 p56-p57。

#### 格式化输出
> p57

```java
double x = 10000.0 / 3.0;
System.out.print(x);  // 3333.3333333333335

System.out.printf("%8.2f", x);  // 输出8 个字符，精度为小数点后两位：3333.33（多余的字符位用前导空格填充）
System.out.printf("%,.2f", x);  // 增加分组分隔符，精度为小数点后两位：3,333.33
```

`printf` 可以使用多个标志，可用于 `printf` 的标志符有：

|标志|目的|示例|
|:---|:---|:---|
|+|打印符号位|+3333.33|
|空格|在整数之前添加空格| \| 3333.33\||
|0|数字前面补0|003333.33|
|-|左对齐|\|3333.33 \||
|(|将负数括在括号内|(3333.33)|
|,|添加分组分隔符|3,333.33|
|#（对于 f 格式）|包含小数点|3,333.|
|#（对于 x 或 o 格式）|添加前缀 0x 或 0|0xcafe|
|$|指定要格式化的参数索引|-|
|<|格式化前面说明的数值|-|

`printf` 可以接收多个参数：

```java
System.out.prinf("Hello, %s. Next year, you'll be %d.", name, age);
```

每一个以 `%` 字符开始的*格式说明符*都用相应的参数替换，可用于 `printf` 的转换符如下表：

|转换符|类型|示例|
|:---:|:---|:---|
|d|十进制整数|159|
|x|十六进制整数|9f|
|o|八进制整数|237|
|f|定点浮点数|15.9|
|e|指数浮点数|1.59e+01|
|g|通用浮点数（e 和 f 中较短的一个）|-|
|a|十六进制浮点数|0x1.fccdp3|
|s|字符串|Hello|
|c|字符|H|
|b|布尔|true|
|h|散列码|42628b2|
|%|百分号|%|
|n|与平台有关的行分隔符|-|

可以使用静态的 `String.format` 方法创建一个格式化的字符串，而不打印输出：

```java
String str = String.format("Hello, %s. Next year, you'll be %d.", name, age);
```

日期与时间的格式化选项见 p59。

#### 文件输入与输出
> p61

读取文件：
```java
Scaner in = new Scanner(Path.of("myfiled.txt"), StandardCharsets.UTF_8);
```

**NOTE：**如果文件名中包含反斜杠符号，就要在每一个反斜杠之前再加一个反斜杠转义：`“c:\\mydirectory\\myfile.txt”`

写入文件：

```java
PrintWriter out = new PrintWriter("myfile.txt", StandardCharsets.UTF_8);
```

如果文件不存在，创建该文件。可以像输出到 `System.out` 一样使用 `print`、`println` 以及 `printf`。

### 控制流程
#### 块作用域
> p63

*块*（block）是指由若干条 Java 语句组成的语句，并用一对大括号括起来（**也就是说，大括号可以单独成对使用，不必搭配语句**）。块确定了变量的作用域。一个块可以*嵌套*在另一个块中，但**不能在嵌套的两个块中声明同名的变量**。

#### 多重选择：switch 语句
> p72

```java
switch (choice)
{
	case 1:
		. . .
		break;
	case 2:
		. . .
		break;
	. . .
	default:
		. . .
		break;
}
```

`case` 标签可以是：

* 类型为 char、byte、short 或 int 的常量表达式；
* 枚举常量；
* 从 Java 7 开始，`case` 标签还可以是字符串字面量。

在 `switch` 语句中使用枚举常量时，不必在每个标签中指明枚举名，可以由 `switch` 的表达式值推导出，例如：

```java
enum Size {SMALL, MEDIUM, LARGE, EXTRA_LARGE};
Size sz = Size.MEDIUM;

switch (sz)
{
	case SMALL:  // 不用写成 Size.SMALL
		. . .
	. . .
}
```

#### 终端控制流程的语句
> p74

Java 中没有 `goto`，但是新增了一种**带标签的** `break` 语句来代替 `goto`，例：

```java
read_data:
while (. . .)
{
	for (. . .)
	{
		. . .
		if (. . .)
			break read_data;
	}
}
```

**NOTE：**事实上，可以将标签应用到任何语句，甚至可以将其应用到 `if` 语句或者块语句上，但不建议这么使用。

### 大数
> p76

`java.math` 包中有两个大数类可以处理包含任意长数字序列的数值：

* `BigInteger` 类实现任意精度的整数运算；
* `BigDecimall` 类实现任意精度的浮点数运算。

可以使用静态的 `valueOf` 方法将普通的数值转化为大数：

```java
BigInteger a = BigInteger.valueOf(100);
```

**不能使用 `+` 或 `*` 等算术运算符来处理大数**，而需要使用大数类中的 `add` 和 `multipy` 方法：

```java
BigInteger  c = a.add(b);  // c = a + b
BigInteger d = c.multiply(b.add(BigInteger.valueOf(2)));  // d = c * (b + 2)
```

更多大数类的方法见 p78。

### 数组
#### 声明数组
> p79

声明数组：

```java
int[] a;
```

声明并初始化一个可以存储 100 个整数的数组：

```java
int[] a = new int[100];
```

**一旦创建了数组，就不能改变它的长度！**

声明并同时提供初始值：

```java
int[] a = {2, 3, 5, 7, 11, 13};
```

最后一个值后面允许有逗号！这样方便随时添加。

声明*匿名数组*：

```java
new int[] {17, 19, 21, 22, 30}
```

可以用它来初始化一个数组。

#### for each 循环
> p81

```java
for (int element : a)
	System.out.println(element);
```

#### 数组拷贝
> p82

浅拷贝（两个变量引用同一个数组）：
```java
int[] b = a;
```

深拷贝：

```java
int[] b = Arrays.copyOf(a, n);
```

第二个参数是新数组的长度，如果

* 大于原数组长度，那么：
  * 如果数组元素是数值型，用 0 填充；
  * 如果数组元素是布尔型，用 false 填充；
* 小于原数组长度，只拷贝前 n 个值。

#### 命令行参数
> p83

如果在命令行中输入：

```shell
java MyProgress -g cruel world
```

那么对于 `String[] args`:

```
args[0]: "-g"
args[1]: "cruel"
args[2]: "world"
```

#### 数组排序
> p83

```java
Arrays.sort(a);
```

该方法使用的是快排。

#### 多维数组
> p86

```java
double[][] a;
```

对象与类
---
### 用户自定义类
* **文件名必须与 public 类的名字相匹配，在一个文件中，只能有一个公共类。**
* 构造器总是结合 `new` 运算符来调用。**不能对一个已经存在的对象调用构造器来达到重新设置实例字段的目的。**

#### 用 var 声明局部变量
> p109

从 Java 10 开始，可以用 `var` 关键字来声明**局部变量**，而无须指定类型。`var` 关键字只能用于方法中的*局部*变量！

#### 使用 null 引用
> p109

如果对 null 值应用一个方法，会产生一个 NullPointerException 异常。为了防止这个错误，我们应该避免对象中的字段为 null。对此有两种解决方法：

* “宽容型”方法是把 null 参数转化为一个适当的非 null 值，比如
```java
public Employee(String n, double s, int year, int month, int day)
{
	if (n == null) name = "unknown"; else name = n;
	. . .
}
```
或者用 java 9 中提供的新方法：
```java
public Employee(String n, double s, int year, int month, int day)
{
	name = Objects.requireNonNullElse(n, "unknown");
	. . .
}
```
* “严格型”方法则是干脆拒绝 null 参数：
```java
public Employee(String n, double s, int year, int month, int day)
{
	Objects.requireNonNull(n, "The name cannot be null!");
	name = n;
	. . .
}
```

#### 隐式参数与显式参数
> p110

每个方法都有两个参数：

* 第一个参数是*隐式*（implict）参数，是出现在方法前该类型的对象，也即**调用该方法的对象**。我们可以用关键字 `this` 指示隐式参数：
```java
public void raiseSalary(double byPercent)
{
	double raise = this.salary * byPercent / 100;
	this.salary += raise;
}
```
* 第二个参数是*显式*（explicit）参数，位于方法名后面括号中。

#### 基于类的访问权限
> p113

**一个方法可以访问所属累的所有对象的私有数据。**比如 Employee 类的方法可以访问**任何** Employee 类型对象的**私有**字段。

#### final 实例字段
> p114

可以将实例字段定义为 `final`，这样的字段必须在构造对象时初始化。

### 静态字段与静态方法
#### 静态字段
> p115

如果将一个字段定义为 `static`，那么**每个类只有一个该字段，也即所有该类的对象共享同一个静态字段**，而对于非静态的实例字段，每个对象都有自己的一个副本。例：

```java
public class EmployeeTest {
    public static void main(String[] args) {
        Employee employee1 = new Employee();
        Employee employee2 = new Employee();

        employee1.getId(); // 1
        employee2.getId();  // 2
    }
}

public class Employee {
    private int id;
    private static int nextId = 1;

    public Employee()
    {
        this.id = nextId;
        ++nextId;
    }

    public void getId()
    {
        System.out.println(this.id);
    }
}
```

静态字段 `nextId` 属于类，而不属于任何单独的对象。

#### 静态常量
> p116

由于每个类对象都可以修改公共字段，所以最好不要有公共字段，但是公共常量就不会有问题：

```java
public static final . . .
```

#### 静态方法
> p116

静态方法是不在对象上执行的方法。比如：`Math.pow(x, a)`，它并不使用任何 Math 对象。换句话说，**静态方法没有隐式参数。**

**静态方法不能访问非静态字段**，因为它不能在对象上执行，但是可以访问静态字段。

### 方法参数
> p121

* *按值调用*（call by value）表示方法接收的是调用者提供的值；
* *按引用调用*（call by reference）表示方法接收的是调用者提供的变量地址。

**Java 中只有按值调用！！！**也就是说，方法得到的是所有参数值的一个副本，它不能修改传递给它的任何参数变量的内容。比如以下方法是**不起作用的**：

```java
public static void tripleValue(double x)
{
	x = 3 * x;
}
```

**但是！！！**Java 有两种类型的方法参数：

* 基本数据类型
* 对象引用

如果将对象作为参数，那么就可以修改它的字段值！**因为它是引用，指向同一个对象。**比如：

```java
pubilc static void tripleSalary(Employee x)
{
	x.raiseSalary(200);
}
```

比较搞的是，**对象引用仍然是按值传递的**，比如：

```java
pubilc static void swap(Employee x, Employee y)
{
	Employee tmp = x;
	x = y;
	y = tmp;
}

var a = new Employee(. . .);
var b = new Employee(. . .);
swap(a, b)l
```

如果是按引用调用，那么上述方法应该是能交换 a 和 b 的，可是事实上，它只交换了 a 和 b 的副本 x 和 y，而 a 和 b 仍是原本的对象。

### 对象构造
#### 无参数的构造器
> p127

**仅当类没有任何其他构造器的时候，编译器才会提供一个默认的无参数构造器。**

#### 参数名
> p129

对于构造器参数，有两种命名风格：

* 有些程序员喜欢在每个参数前面加上一个前缀“a"
```java
public Employee(String aName, double aSalary)
{
	name = aName;
	salary = aSalary;
}
```
* 利用 Java 的特点——参数变量会遮蔽同名的实例字段，所以我们也可以这么写：
```java
public Employee(String name, double salary)
{
	this.name = name;
	this.salary = salary;
}
```

**C++ NOTE：**在 C++ 中，经常用下划线或某个固定的字母（一般选用 m 或 x）作为**实例字段**的前缀，Java程序员一般不这么做。

#### 调用另一个构造器
> p129

关键词 `this` 除了指示一个方法的隐式参数以外，还有另一个作用：如果**构造器的第一个语句**形如 `this(. . .)`，那么这个构造器将调用**同一个类**的另一个构造器。例：

```java
public Employee(double s)
{
	this("Employee #" + nextId, s);  // 调用 Employee(String, double)
	nextId++;
}
```

#### 初始化块
> p130

Java 除了能在构造器中设置值和在声明中赋值以外，还有第三种初始化数据字段的方法，称为*初始化块*（initialization block）。例：

```java
class Employee
{
	private static int nextId;
	
	private int id;
	
	{
		id = nextId;
		nextId++;
	}
}
```

初始化块的执行在构造器主体之前。可以用关键字 `static` 来修饰一个静态的初始化块：

```java
static
{
	var generator = new Random();
	nextId = generator.nextInt(10000);
}
```

静态字段只会在类**第一次加载**的时候初始化一次。

#### 对象析构与 finalize 方法
> p134

由于 Java 有自动的垃圾回收机制，不需要人工回收内存，所以 **Java 不支持析构器**，也不要使用 `finalize` 方法来完成清理！

### 包
#### 包名
> p134

* 使用包的主要原因是确保类名的唯一性。
* 为了确保包名的绝对唯一性，要用一个因特网域名以**逆序**的形式作为包名，然后对于不同的工程使用不同的子包。
* 从编译器的角度来看，嵌套的包之间没有任何关系，例如 java.util 和 java.util.jar 包毫无关系，**每一个包都是独立的类集合。**

#### 类的导入
> p135

一个类可以使用所属包中的所有类，以及其他包中的*公共类*（public class）。我们可以采用两种方式访问另一个包中的公共类：

1. 使用*完全限定名*（fully qualified name），即包名后面跟着类名。例如：
```java
java.time.LocalDate today = java.time.LocalDate.now();
```
2. 使用 `import`：
```java
import java.time.*;

LocalDate today = LocalDate.now();
```
还可以导入一个包中的特定类：
```java
import java.time.LocalDate;
```

**NOTE：**只能使用星号 `*` 导入一个包，而不能使用 `import java.*` 或 `import java.*.*` 导入以 java 为前缀的所有包。

#### 静态导入
> p136

在 `import` 后添加 `static` 关键字可以导入静态方法和静态字段，这样在使用静态方法和静态字段时不必加类名前缀。例：

```java
import static java.lang.System.*;

public class HelloWorld
{
	public static void main(String args[])
	{
		out.println("test");  // i.e., System.out
		exit(0);  // i.e., System.exit
	}
}
```

#### 在包中增加类
> p137

要想将类放入包中，就必须将包的名字放在源文件的开头，即放在定义这个包中各个类的代码之前。例：

```java
package com.horstmann.corejava;

public class Employee
{
	. . .
}
```

如果没有在源文件中放置 `package` 语句，那么这个源文件中的类就属于*无名包*（unnamed package）。

**将源文件放到与完整包名匹配的子目录。**例如，com.horstmann.corejava 包中的所有源文件应该放置在子目录 com/horstmann/corejava中。

编译：

```shell
javac com/horstmann/corejava/Employee.java
```

运行：

```shell
java com.horstmann.corejava.Employee
```

#### 包访问
> p139

* `public` 访问修饰符：可以由任意类使用；
* `private` 访问修饰符：只能由定义它们的类使用；
* 不指定访问修饰符（默认）：可以被同一个包中的所有方法使用。

### 文档注释
#### 类注释
> p149

类注释必须放在 `import` 语句之后，类定义之前。例：

```java
/**
 * Here is
 * some
 * comments.
 */
public class Card
{
	. . .
}
```

#### 方法注释
> p149

每一个方法注释必须放在所描述的方法之前，并可以使用下面的标记：

* `@param variable description`

  这个标记将给当前方法的参数部分添加一个条目，这个描述可以占据多行，并且可以使用 HTML 标记。一个方法的所有 `@param` 标记必须放在一起。
  
* `@return description`

  这个标记将给当前方法添加返回部分。这个描述可以跨多行，并且可以使用 HTML 标记。
  
* `@throws class description`

  这个标记将添加一个注释，表示这个方法有可能抛出异常。
  

例：

```java
/**
 * Raises the salary of an employee.
 * @param byPercent the percentage by which to raise the salary (e.g., 10 means 10%)
 * @return the amount of the raise
 */
public double raiseSalary(double byPercent)
{
	double raise = salary * byPercent / 100;
	salary += raise;
	return raise;
}
```

#### 字段注释
> p150

只需要对公共字段（通常指静态常量）建立文档。例如：

```java
/**
 * The "Hearts" card suit
 */
public static final int HEARTS = 1;
```

#### 通用注释
> p150

* `@author name`
* `@version text`

### 类设计技巧
> p152

1. 一定保证数据私有。
2. 一定要对数据进行初始化。
3. 不要在类中使用过多的基本类型，可以用一个新类来替换。
4. 不是所有的字段都需要单独的字段访问器和字段更改器。
5. 分解有过多职责的类。
6. 类名和方法名要能够体现它们的职责。
7. 优先使用不可变的类。

继承
---
### 类、超类和子类
EmployeeTest.java

```java
public class EmployeeTest {
    public static void main(String[] args) {
        Manager boss = new Manager("Carl Cracker", 80000, 1987, 12, 15);
        boss.setBonus(5000);

        Employee[] staff = new Employee[3];

        staff[0] = boss;
        staff[1] = new Employee("Harry Hacker", 50000, 1989, 10, 1);
        staff[2] = new Employee("Tommy Tester", 40000, 1990, 3, 15);

        for (Employee e : staff)
        {
            System.out.println("name=" + e.getName() + ",salary=" + e.getSalary());
        }
    }
}
```

Employee.java

```java
import java.time.*;

public class Employee {
    private String name;
    private double salary;
    private LocalDate hireDay;

    public Employee(String name, double salary, int year, int month, int day)
    {
        this.name = name;
        this.salary = salary;
        this.hireDay = LocalDate.of(year, month, day);
    }

    public String getName()
    {
        return name;
    }

    public double getSalary()
    {
        return salary;
    }

    public LocalDate getHireDay()
    {
        return hireDay;
    }

    public void raiseSalary(double byPercent)
    {
        double raise = salary * byPercent / 100;
        salary += raise;
    }
}
```

Manager.java

```java
public class Manager extends Employee
{
    private double bonus;

    public Manager(String name, double salary, int year, int month, int day)
    {
        super(name, salary, year, month, day);
        bonus = 0;
    }

    @Override
    public double getSalary()
    {
        return bonus + super.getSalary();
    }

    public void setBonus(double bonus)
    {
        this.bonus = bonus;
    }
}
```

#### 定义子类
> p156

```java
public class Manager extends Employee
{
	added methods and fields
}
```

**C++ NOTE：**在 Java 中，所有的继承都是公共继承，而没有 C++ 中的私有继承和保护继承。

将 Employee 类称为*超类*（superclass）、*基类*（baseclass）或*父类*（parent class）；Manager 类称为*子类*（subclass）、*派生类*（derived class）或*孩子类*（child class）。

#### 覆盖方法
> p157

**子类不能直接访问超类中的私有字段！**因此需要使用超类中提供的公共方法来访问字段，用关键字 `super` 来达成此目的。例：`super.getSalary()`，可以在子类中调用父类的 `getSalary()` 方法。

#### 子类构造器
> p158

```java
public Manager(String name, double salary, int year, int month, int day)
{
	super(name, salary, year, month, day);
	bonus = 0;
}
```

如果子类的构造器没有显式地调用超类的构造器，那么将自动调用超类的无参数构造器。如果超类没有无参数构造器，并且在子类的构造器中又没有显式地调用超类的其他构造器，Java 编译器就会报告一个错误。

一个**对象变量**可以指示多种**实际类型**的现象称为*多态*（polymorphism）。在运行时能自动选择适当的方法，称为*动态绑定*（dynamic binding）。

**C++ NOTE：**在 C++ 中，如果希望实现动态绑定，需要将成员函数声明为 `virtual`。在 Java 中，动态绑定是默认的行为，如果**不**希望让一个方法是虚拟的，可以将它标记为 `final`。

#### 继承层次
> p162

Java 不支持多重继承，但是有接口。

#### 多态
> p162

有一个简单规则可以用来判断是否应该将数据设计为继承关系，那就是“is-a”规则，即子类的每个对象都是超类的对象，比如每个经理都是员工。“is-a”规则的另一种表述是*替换原则*（substitution principle），意思是**程序中任何出现超类对象的地方都可以使用子类对象替换！**

```java
boss.setBonus(5000);  // OK!

staff[0].setBonus(5000);  // ERROR!
Manager m = staff[0];  // ERROR!
```

编译器只将 `staff[0]` 看成是一个 Employee 对象！

#### 阻止继承：final 类和方法
> p165

```java
public final class Executive extends Manager
{
	. . .
}
```

final 类中的所有方法自动地成为 final 方法，但是也可以手动声明正常类中的某个特定方法为 final，这样可以阻止子类覆盖。

```java
public class Employee
{
	. . .
	public final String getName()
	{
		return name;
	}
	. . .
}
```

如果将一个类声明为 final，只有其中的房啊自动成为 final，而**不包括字段**。

#### 强制类型转换
> p166

```java
double x = 3.405;
int nx = (int) x;  // x is 3

Manager boss = (Manager) staff[0];
```

如果试图在继承链上进行向下的强制类型转换，并且“谎报”对象包含的内容，就会产生 ClassCastException 异常，比如：

```java
Manager boss = (Manager) staff[1];  // ERROR!
```

当然如果两个类型本身就风马牛不相及，类型转换也不会成功：

```java
String c = (String) staff[1];  // ERROR!
```

因此，**在进行强制转换之前，应该先使用 `instanceof` 操作符查看能否转换成功。**例：

```java
if (staff[1] instanceof Manager)
{
	boss = (Manager) staff[1];
	. . .
}
```

#### 抽象类
> p169

可以使用 `abstract` 关键词将方法声明为抽象方法，抽象方法不用提供具体实现。包含一个或多个抽象方法的类本身**必须**被声明为抽象类。例：

```java
public abstract class Person
{
	. . .
	public abstract String getDescription();
}
```

即使不含抽象方法，也可以讲类声明为抽象类。**抽象类不能实例化！**

#### 受保护访问
> p173

* private：仅对本类可见
* public：对外部完全可见
* protected：
  * 对本包和所有子类可见
  * 若子类与基类不在同一包中，那么在子类中，子类实例可以访问其从基类继承而来的protected方法，而不能访问基类实例的protected方法
* 默认/不使用修饰符：对本包可见

### Object：所有类的超类
#### Object 类型的变量
> p174

在 Java 中，只有*基本类型*（primitive type）不是对象，即，数值、字符和布尔类型的值不是对象。**其他所有**都是对象，都扩展自 Object 类。

所有的数组类型，不管是对象数组还是基本类型的数字都扩展了 Object 类。

```java
Object obj;

Employee[] staff = new Employee[10]'
obj = staff;  // OK!

obj = new int[10];  // OK!
```

**C++ NOTE：**在 C++ 中没有所有类的根类，不过，每个指针都可以转换为 `void*` 指针。

#### equals 方法
> p175

Obejct 类中的 `equals` 方法用于检测一个对象是否等于另外一个对象。Object 类中实现的 `equals` 方法将确定两个对象**引用**是否相等：如果两个对象引用相等，这两个对象肯定就相等。不过也可以重写，比如：

```java
public class Employee
{
	. . .
	public boolen equals(Object otherObject)
	{
		// 检查是不是一个 Object
		if (this == otherObject) return true;
		
		// 如果显参为空，返回 false
		if (otherObject == null) return false;
		
		// 如果类型不匹配，返回 flase
		if (getClass() != otherObject.getClass()) return false;
		
		Employee other = (Employee) otherObject;
		
		return name.equals(other.name) 
		    && salary == other.salary 
		    && hireDay.equals(other.hireDay); 
	}
}
```

为了防备 name 或 hireDay可能为 null 的情况，需要使用 `Objects.equals` 方法。如果两个参数都为 null，`Objects.equals(a, b)` 将返回 true；如果其中一个参数为 null，则返回 false；如果两个参数都不为 null，则调用 `a.equals(b)`。因此 `Employee.equals` 方法的最后一句语句要改写为：

```java
return Objects.equals(name, other.name) 
    && salary == other.salary 
    && Objects.equals(hireDay, other.hireDay); 
```

在子类中定义 `equals` 方法时，首先调用超类的 `equals`。如果检测失败，对象就不可能相等；否则比较子类中的实例字段。

```java
public class Manager extends Employee
{
	. . .
	public boolen equals(Object otherObject)
	{
		if (!super.equals(otherObject)) return false;
		Manager other = (Manager) otherObject;
		return bonus == other.bonus;
	}
}
```

#### 相等测试与继承
> p176

Java 语言规范要求 `equals` 方法具有以下特性：

1. *自反性*：对于任何非空引用 x，`x.equals(x)` 应该返回 true；
2. *对称性*：对于任何引用 x 和 y，当且仅当 `x.equals(y)` 返回 true 时，`y.equals(x)` 也返回 true；
3. *传递性*：对于任何引用 x、y 和 z，如果 `x.equals(y)` 返回 true 且 `y.equals(z)` 也返回 true时，`x.equals(z)` 应该返回 true；
4. *一致性*：如果 x 和 y 引用的对象没有发生变化，反复调用 `x.equals(y)` 应该返回同样的结果；
5. 对于任意非空引用 x，`x.equals(null)` 应该返回 false。

但是当隐式参数和显式参数是超类与子类的关系时，`equals` 方法应该如何处理呢？许多程序员选择 `instanceof` 进行检测：

```java
if (!(otherObject instanceof Employee)) return false;
```

但是这显然违反了对称性规则。但是如果用 `getClass` 检测，又违反了替换原则。本书中给出了一个完美编写 `equals` 方法的建议：

1. 显式参数命名为 otherObject，稍后需要将它强制转换为另一个名为 other 的变量。
2. 检测 this 和 otherObject 是否相等：
  ```java
  if (this == otherObject) return true;
  ```
  这条语句只是一个优化，因为检查身份要比逐个比较字段开销小。
3. 检测 otherObject 是否为 null，如果为 null，返回 false：
  ```java
  if (otherObject == null) return false;
  ```
4. 比较 this 和 otherObject 的类。如果 `equals` 的语义可以在子类中改变，就使用 `getClass` 检测：
  ```java
  if (getClass() != otherObject.getClass()) return false;
  ```
  如果**所有**子类都有相同的相等性语义，就使用 `instanceof` 检测：
  ```java
  if (!(otherObject instanceof ClassName)) return false;
  ```
5. 将 otherObject 强制转换为相应类类型的变量：
  ```java
  ClassName other = (ClassName) otherObject;
  ```
6. 现在根据相等性概念的要求来比较字段，使用 `==` 比较基本类型字段，使用 `Objects.equals` 比较对象字段。如果所有字段都匹配，就返回 true；否则返回 false。
  ```java
  return field1 == other.field1
      && Objects.equals(field2, other.field2)
      && . . .;
  ```
  如果在子类中重新定义 `equals`，就要在其中包含一个 `super.equals(other)` 调用。

#### hashCode 方法
> p179

*散列码*（hash code）是由对象导出的一个整型值，`hashCode` 方法应该返回一个整数（可以为负）。

如果两个对象相同，那他们的散列码也应该相同。因此，**如果重写了 `equals` 方法，那么也应该重写 `hashCode` 方法。**例如，下面是 Employee 类的 hashCode 方法：

```java
public class Employee
{
	public int hashCode()
	{
		return 7 * name.hashCode()
		      + 11 * new Double(salary).hashCode()
		      + 13 * hireDay.hashCode();
	}
	. . .
}
```

以上代码可以分两步优化：

1. 使用 null 安全的方法 `Objects.hashCode`。如果其参数为 null，这个方法会返回 0，否则返回对参数调用 `hashCode` 的结果；
2. 使用静态方法 `Double.hashCode` 来避免创建 Double 对象。

如下：

```java
public int hashCode()
{
	return 7 * Objects.hashCode(name)
	      + 11 * Double.hashCode(salary)
	      + 13 * Objects.hashCode(hireDay);
}
```

上面的代码可以进一步优化：调用 `Objects.hash` 并提供所有这些参数，该方法会对各个参数调用 `Objects.hashCode`，并组合这些散列值。如下：

```java
public int hashCode()
{
	return Objects.hash(name, salary, hireDay);
}
```

`equals` 与 `hashCode` 的定义必须相容：如果 `x.equals(y)` 返回 true，那么 `x.hashCode()` 和 `y.hashCode()` 就必须返回相同的值。例如，如果定义 `Employee.equals` 比较的是员工的 ID，那么 `hashCode` 方法就需要散列 ID，而不是员工的姓名或存储地址。

#### toString 方法
> p181

绝大多数（不是全部）的 `toString` 方法都遵循这样的格式：类的名字，随后是一对方括号括起来的字段值。比如，Employee 类中的 `toString` 方法的实现为：

```java
public String toString()
{
	return getClass.getName()
	      + ",salary=" + salary
	      + ",hireDay=" + hireDay
	      + "]";
}
```

子类应该调用 `super.toString()`，例如：

```java
public class Manager extends Employee
{
	. . .
	public String toString()
	{
		return super.toString()
		      + "[bonus=" + bonus
		      + "]";
	}
}
```

`toString` 完备的理由：

* 只要对象与一个字符串通过操作符 `+` 链接，Java 编译器就会自动调用 `toString` 方法来获得这个对象的字符串描述；
* 如果 x 是一个任意对象，并调用 `System.out.println(x);`，`println` 方法会自动调用 `x.toString()`；
* `toString` 方法是一种非常有用的调试工具，可以搭配 `Logger.global.info` 方法来输入日志。

### 泛型数组列表
> p186

在 C/C++ 中，必须在编译时就确定整个数组的大小，而 Java 允许运行时确定数组的大小：

```java
int n = . . .;
var staff = new Employee[n];
```

但这并没有完全解决运行时动态改变数组大小的问题，解决该问题的正确方式是使用 ArrayList 类。ArrayList 类类似于数组，但在添加或删除元素时，它能够自动调整数组容量，而不需要为此编写任何代码。

ArrayList 是一个有*类型参数*（type parameter）的*泛型类*（generic class）。为了指定列表保存的元素对象的类型，需要用一对见括号将类名括起来追加到 `ArrayList` 后面，例如，`ArrayList<Employee>`。

#### 声明数组列表
> p187

声明并构造数组列表：

```java
// 三种方式
ArrayList<Employee> staff = new ArrayList<Employee>();  // original form

var staff = new ArrayList<Employee>();  // in Java 10
ArrayList<Employee> staff = new ArrayList<>();  // OK!
```

添加元素到数组列表：

```java
staff.add(new Employee("Harry Hacker", . . .));
```

数组列表内部管理着一个对象引用数组，当这个内部的对象引用数组满了，数组列表就会自动创建一个更大的数组，并将所有的对象从较小的数组中拷贝到更大的数组中去。我们也可以为数组列表预先分配好**可能的**容量，这样就可以减少开销很大的重新分配空间。例：

```java
// 两种方式
staff.ensureCapacity(100);
ArrayList<Employee> staff = new ArrayList<100>;
```

`size` 方法可以返回数组列表中包含的实际的元素个数。例：

```java
staff.size();
```

一旦确定不会再新增元素，我们可以用 `trimToSize` 方法来回收数组列表中多余的未使用的空间。

#### 访问数组列表元素
> p189

* 访问元素
  ```java
  staff.get(i);  \\ i 是索引
  ```
* 替换**已经存在的元素**
  ```java
  staff.set(i, harry);
  ```
  **注意！**i 必须小于数组列表的实际元素个数，否则应该用 `add`。
* 插入元素
  ```java
  staff.add(i, vincent);
  ```
  位置 i 及之后的所有元素都要向后移动一个位置。
* 删除元素
  ```java
  Employee e = staff.remove(i);
  ```
  位置 i 之后的所有元素都要向前移动一个位置。

#### 类型化与原始数组列表的兼容性
> p191

假设有一个**没有使用**类型参数的遗留代码，如下所示：

```java
public class EmployeeDB
{
	public void update(ArrayList list) { . . . }
	public ArrayList find(String query) { . . . }
}
```

可以将一个类型化的数组列表传递给 `update` 方法，而不需要进行任何强制类型转换，如下所示：

```java
ArrayList<Employee> staff = . . .;
employeeDB.update(staff);
```

相反，将一个原始未类型化的 ArrayList 赋给一个类型化 ArrayList 会得到一个**警告**（不是报错！），如下所示：

```java
ArrayList<Employee> result = employeeDB.find(query);  // yields warning
```

使用强制类型转换并**不能**避免出现警告，如下所示：

```java
ArrayList<Employee> result = (ArrayList<Employee>) employeeDB.find(query);  
// yields another warning
```

对于这个问题，我们做不了什么，只能在确保警告不太严重的情况下忽略它。可以用 `@SuppressWarnings("unchecked")` 注解来标记接受强制类型转换的变量，如下所示：

```java
@SuppressWarnings("unchecked") ArrayList<Employee> result = (ArrayList<Employee>) employeeDB.find(query);
```

### 对象包装器与自动装箱
> p192

所有的基本类型都有一个与之对应的类，这些类称为*包装器*（wrapper）：

1. Integer
2. Long
3. Float
4. Double
5. Short
6. Byte
7. Character
8. Boolean

前 6 个类派生于公共的超类 Number。

包装器类是不可变的，即一旦构造了包装器，就不允许更改包装在其中的值。同时，包装器类还是 `final` 的，因此**不能派生它们的子类**。

数组列表尖括号中的类型参数不允许事基本类型，也就是说必须写成包装器类，例如：

```java
var list = new ArrayList<int>();  // ERROR!!!
var list = new ArrayList<Integer>();  // OK!
```

**NOTE：**由于每个值分别包装在对象中，所以 `ArrayList<Integer>` 的效率远远低于 `int[]` 数组。因此，只有当程序员操作的方便性比执行效率更重要的时候，才会考虑对**较小**的集合使用这种结构。

`list.add(3);` 会自动转换成 `list.add(Integer.valueOf(3));`，这种特性叫作*自动装箱*（auto-boxing）；相反地，`int n = list.get(i);` 也会自动转换为 `int n = list.get(i).intValue();`，这种特性叫作*自动拆箱*（auto-unboxing）。**自动装箱和自动拆箱也适用于算术表达式。**

**`==` 运算符应用于包装器对象时，检测的是对象的内存位置，而非值！！！**

自动装箱补充说明：

1. 由于包装器类引用可以为 null，所以自动装箱有可能会抛出一个 NullPointerException 异常，例如：
  ```java
  Integer n = null;
  System.out.println(2 * n);  // throws NullPointerException
  ```
2. 如果在一个**算术表达式**或**条件表达式**中混合使用 Integer 和 Double 类型，Integer 值就会拆箱，提升为 Double，再装箱为 Double，例如：
  ```java
  Integer a = 1;
  Double b = 100.0;
  System.out.println(a+b);  // 101.0
  System.out.println(true ? a : b);  // 1.0
  ```
3. 装箱和拆箱是*编译器*要做的工作，而不是虚拟机。

要想将字符串转换为整型，可以使用如下语句：

```java
int x = Integer.parseInt(s);  // 静态方法，与 Integer 对象无关
```

### 参数数量可变的方法
> p195

在显参中使用 `...` 可以使方法接受任意数量的对象。例如：

```java
/** 
  *计算若干个数值中的最大值（数值个数可变）
  */
public static double max(double... values)
{
	double largest = Double.NEGATIVE_INFINITY;
	for (double v : values) largest = v > largest ? v : largest;
	return largest;
}
```

### 枚举类
> p196

```java
public enum SIze{SMALL, MEDIUM, LARGE, EXTRA_LARGE};
```

上面是一个常见的声明并定义枚举的例子，实际上这个声明定义的类型是一个类，它刚好有 4 个实例，**不可能**构造新的对象，因此可以直接用 `==` 来比较两个枚举类型的值。

可以为枚举类增加构造器、方法和字段。枚举类的构造器**总是且默认是**私有的，因此可以省略 `private` 修饰符，但不能声明为 `public` 或 `protected`，会出现语法错误。

所有枚举类都是 Enum 类的子类，因此也继承了这个类的许多方法，比如：

* `toString` 方法。例如 `Size.SMALL.toString()` 将返回字符串 "SMALL"。
* `toString` 的逆方法 `valueOf`，`valueOf` 方法是静态方法。例如 `Size s = Enum.valueOf(Size.class, "SMALL");` 将 s 设置成 Size.SMALL。
* 每个枚举类型都有一个静态的 `values` 方法，它将返回一个包含全部枚举值的数组。例如 `Size[] values = Size.values();` 返回包含元素 Size.SMALL、Size.MEDIUM、Size.LARGE 和 Size.EXTRA_LARGE 的数组。
* `ordinal` 方法返回 enum 声明中枚举常量的位置，从 0 开始计数。例如 `Size.MEDIUM.ordinal()` 将返回 1。

### 反射
> p198

能够分析类能力的程序称为*反射*（reflective），反射机制可以用来：

* 在运行时分析类的能力。
* 在运行时检查对象。
* 实现泛型数组操作代码。
* 利用 Method 对象。

#### Class 类
> p199

在程序运行期间，Java 运行时系统始终为所有对象维护一个*运行时类型标识*，这个信息会跟踪每个对象所属的类。可以使用一个 Class 类来访问这些信息，Object 类中的 `getClass()` 方法将会返回一个 Class 类型的实例。例如：

```java
Employee e;
. . .
Class cl = e.getClass();
```

就像 Employee 对象描述一个特定员工的属性一样，Class 对象会描述一个特定类的属性。例如，`getName` 方法会返回类的名字，如果类在一个包里，包的名字也作为类名的一部分。例如：

```java
var generate = new Random();
Class cl = generate.getClass();
String name = cl.getName();  // name is "java.util.Random"
```

`getName` 的你方法是 `forName`，它是静态方法，并且返回类名对应的 Class 对象。例如：

```java
String name = "java.util.Random";
Class cl = Class.forName(name);
```

如果输入的不是类名或接口名，那么 `forName` 方法将抛出一个*检查型异常*（checked exception），因此使用这个方法时应该提供一个*异常处理器*（exception handler）。

也可以直接用 `T.class` 来获得 Class 类对象，例如：

```java
Class cl1 = Random.class;  // if import java.util.*;
Class cl2 = int.class;
Class cl3 = Double[].class;
```

虚拟机为每个类型管理一个唯一的 Class 对象，因此可以利用 `==` 运算符实现两个类对象的比较。

#### 声明异常入门
> p201

如果一个方法包含一条可能抛出检查型异常的语句，则在方法名上增加一个 `throws` 子句。例如：

```java
public static void doSomethingWithClass(String name)
	throws ReflectiveOperationException
{
	Class cl = Class.forName(name);  // might throw exception
	. . .
}
```

#### 资源
> p202

类通常有一些关联的数据文件，例如：

* 图像和声音文件；
* 包含消息字符串和按钮标签的文本文件。

在 Java 中，这些关联的文件被称为*资源*（resource）。Class 类提供了查找资源文件的方法，步骤如下：

1. 获得拥有资源的类的 Class 对象；
  ```java
  Class cl = ResourceTest.class;
  ```
2. 使用 `getResource` 方法获得描述资源文件的 URL，例如：
  ```java
  URL url = cl.getResource("about.gif");
  var icon = new ImageIcon(url);
  ```
3. 使用 `getResourceAsStream` 方法得到一个输入流来读取文件中的数据，例如：
  ```java
  InputStream stream = cl.getResourceAsStream("data/about.txt");
  var about = new String(stream.readAllBytes(), "UTF-8");
  ```

#### 利用反射分析类的能力
> p203

在 java.lang.reflect 包中有三个类 Field、Method 和 Constructor 分别用于描述类的字段、方法和构造器。

* 这三个类都有一个 `getName` 方法，返回字段、方法和构造器的名称。
* Field 类有一个 `getType` 方法，返回一个描述字段类型的 Class 对象。
* Method 和 Constructor 类有报告参数类型的方法，Method 类还有一个报告返回类型的方法。
* 这三个类都有一个 `getModifiers` 方法，返回一个整数，用不同的 0/1 位描述所使用的修饰符，如 `public` 和 `static`。可以使用 java.lang.reflect 类中的 `isPublic`、`isPrivate` 或 `isFinal` 等静态方法判断方法或构造器 是 `public`、`private` 还是 `final`。另外还可以利用 `Modifier.toString` 方法将修饰符打印出来。

Class 类中的 `getFields`、`getMethods` 和 `getConstructors` 方法将分别返回这个类支持的**公共**字段、方法和构造器的**数组**，其中包括超类的**公共**成员。

Class 类中的 `getDeclareFields`、`getDeclareMethods` 和 `getDeclareConstructors` 方法将分别返回类中声明的全部字段、方法和构造器的数组，其中包括私有成员、包成员和受保护成员，但**不包括超类的成员**。

### 继承的设计技巧
> p219

1. 将公共操作和字段放在超类中。
2. 不要使用受保护的字段。
3. 除非两个类是“is-a”关系，否则不要使用继承。
4. 除非所有继承的方法都有意义，否则不要使用继承。
5. 在覆盖方法时，不要改变预期的行为。
6. 使用多态，而不要使用类型信息。
7. 不要滥用反射。

接口、lambda 表达式与内部类
---
### 接口
#### 接口的概念
> p222

*接口*（interface）用来描述类应该做**什么**，而不指定它们具体应该**如何**做。一个类可以*实现*（implement）一个或多个接口。例如：

```java
public interface Comparable
{
	int compareTo(Object other);
}
```

这说明，任何实现 Comparable 接口的类都需要包含 `compareTo` 方法，这个方法有一个 Object 参数，并且返回一个整数。可以进一步将 Comparable 提升为泛型接口：

```java
public interface Comparable<T>
{
	int compareTo(T other);
}
```

为了让类实现一个接口，通常需要完成两个步骤：

1. 使用关键字 `implements` 将类声明为实现给定的接口。
2. 对接口中的所有方法提供定义。

例如：

```java
  class Employee implements Comparable<Employee>
  {
  	public int compareTo(Employee other)
  	{
  		return Double.compare(salary, other.salary);
  	}
  }
```

接口中的所有方法自动是 `public`，因此无需提供关键字。但是在实现接口时，必须把方法声明为 `public`。

#### 接口的属性
> p228

**不能用 `new` 运算符实例化一个接口**，但是可以声明接口的变量，接口变量必须引用实现了这个借口的类的对象。例如：

```java
x = new Comparable(. . .);  // ERROR!
Comparable x;  // OK
x = new Employee(. . .);  // OK provided Employee implements Comparable
```

接口可以通过 `extends` 字段进行扩展，与类的继承一样。例如：

```java
public interface Powered extends Moveable { . . . }
```

**接口中不能包含实例字段**，但是可以包含常量。而且与接口中方法都自动被设置为 `public` 一样，接口中的字段也自动被设置为 `public static final`。例如：

```java
public interface Powered extends Moveable 
{
	double SPEED_LIMIT = 95;  // a public static final constant
}
```

可以用 `instanceof` 检查一个对象是否实现了某个特定的接口。例如：

```java
if (anObject instanceof Comparable) { . . . }
```

可以实现多个接口，用逗号分开，例如：

```java
class Employee implements Cloneable, Comparable { . . . }
```

#### 接口与抽象类
> p229

为什么要用接口代替抽象类？

因为 Java 不支持*多重继承*（multiple inheritance），多重继承会让语言变得非常复杂（如果 C++），或者效率会降低（如同 Eiffel）。而接口可以提供多重继承的大多数好处，同时还能避免多重继承的复杂性和低效性。

**C++ NOTE：**C++ 具有多重继承特性，随之带来了一些复杂的特性，如虚基类、控制规则和横向指针类型转换，等等。

#### 静态和私有方法
> p230

从 Java 8 开始，允许在接口中增加静态方法，虽然这有违接口作为抽象规范的初衷。

#### 默认方法
> p230

可以用 `default` 修饰符来为接口方法提供一个*默认*实现，接口的具体实现不用实现默认方法。默认方法也可以调用其他方法，例如：

```java
public interface Collection
{
	int size();  // an abstract method
	default boolean isEmpty() { return size() == 0; }
}
```

这样实现 Collection 的程序员就不用再操心实现 `isEmpty` 方法了。

默认方法的一个重要用法是*接口演化*（interface evolution），比如要为一个接口添加新方法的同时不影响到它的具体实现类，那么就可以用到默认方法。

#### 解决默认方法冲突
> p231

默认方法的两种冲突情形：

1. 一个类同时继承了一个超类并实现了一个接口，其中超类提供了一个具体方法，且接口提供了一个同名同参数类型的默认方法。例如：
  ```java
  interface Person
  {
  	default String getName()
  	{
  		return "wty";
  	};
  }
  
  public class Named 
  {
  	public String getName()
  	{
  		return "vincent";
  	}
  }
  
  class Student extends Named implements Person { . . . }
  ```
  * 遵从**超类优先**：如果超类提供了一个具体方法，同名且有相同参数类型的默认方法会被忽略。
2. 一个类实现了两个接口，两个接口都提供了一个同名同参数类型的方法，且**至少有一个**是默认方法。例如：
  ```java
   interface Person
  {
  	default String getName()
  	{
  		return "wty";
  	};
  }
  
   interface Named
  {
  	String getName();
  }
  
  class Student implements Person, Named { . . . }
  ```
  * Java 编译器并不会从中选择一个，而是报告一个错误并交给程序员来解决这个*二义性*问题。我们只需要在 Student 类中提供一个 `getName` 方法即可，类中的方法会同时覆盖两个接口中的方法，例如：
    ```java
    class Student implements Person, Named
    {
    	public String getName() 
    	{
    		return Person.super.getName();
    	}
    }
    ```

#### 接口与回调
> p233

定时器：

```java
var listener = new TimePrinter();
Timer t = new TImer(1000, listener);
t.start();
```

Timer 构造器的第一个参数是一个时间间隔（单位是毫秒），即经过多长时间通知一次；第二个参数是监听器**对象**。

#### Comparator 接口
> p235

`Arrays.sort` 方法可以传入一个数组和一个*比较器*（comparator）作为参数，比较器是实现了 Comparator 接口的实例。例如：

```java
class LengthComparatorTest
{
	public static void main(String[] args)
	{
		String[] friends = {"Peter", "Paul", "Mary"};
		Arrays.sort(friends, new LengthComparator());
	}
}

class LengthComparatpr implements Comparator<String>
{
	public int compare(String first, String second)
	{
		return first.length() - second.length();
	}
}
```

#### 对象克隆
> p236

在对象内重写 `clone` 方法，该方法用于深拷贝。例：

```java
public Employee implements Cloneable
{
	private String name;
	private double salary;
	private Date hireDay;
	
	. . .
	
	public Employee clone() throws CloneNotSupportedException
	{
		// call Object.clone()
		Employee cloned = (Employee) super.clone();
		
		// clone mutable fields
		cloned.hireDay = (Day) hireDay.clone();
		
		return cloned;
	}
	
	. . .
}
```

如果在一个对象上调用 `clone`，但这个对象的类并没有实现 Cloneable 接口，Object 类的 `clone` 方法就会抛出一个 `CloneNotSupportException`。

### lambda 表达式
#### 为什么引入 lambda 表达式
> p242

lambda 表达式让 Java 向函数式编程迈进。

lambda 表达式是独立于类和方法外的第三种形式。（个人理解）

#### lambda 表达式的语法
> p243

lambda 表达式形式：参数，箭头（->）以及一个表达式，如果一个表达式写不下，可以用一个代码块 `{}`。例如：

```java
(String first, String second) -> first.length() - second.length();  // 检查一个字符串是否比另一个字符串短
```

即使 lambda 表达式没有参数，仍然要提供空括号，就像无参数方法一样。例如：

```java
() -> 
{ 
	for (int i = 100; i >= 0; --i) 
		System.out.println(i);
}
```

如果可以推导出一个 lambda 表达式的参数类型，则可以忽略其类型。例如：

```java
Comparator<String> comp = (first, second) -> first.length() - second.length(); 
```

如果方法只有一个参数，而且这个参数的类型可以推导得出，那么甚至可以省略小括号。例如：

```java
ActionListener listener = event ->
	System.out.println("This time is "
		+ Instant.ofEpochMilli(event.getWhen()));
		// instead of (event) -> . . . or (ActionEvent event) -> . . .
```

无需指定 lambda 表达式的范围类型，lambda 表达式的范围类型总是会由上下文推导得出。

#### 函数式接口

> p245

对于只有一个抽象方法的接口，需要这种接口的对象时，就可以提供一个 lambda 表达式。这种接口称为*函数式接口*（functional interface）。例如  `Arrays.sort` 方法的第二个参数需要一个 Comparator 实例，Comparator 就是只有一个方法的接口，所以可以提供一个 lambda 表达式。

#### 方法引用

> p247

```java
var timer = new javax.swing.Timer(1000, event -> System.out.println(event));
```

可以直接把 `println` 方法传递到 Timer 构造器：

```java
var timer = new javax.swing.Timer(System.out::println);
```

表达式 `System.out::println` 是一个*方法引用*（method reference），它指示编译器生成一个函数式接口的实例，覆盖这个接口的抽象方法来调用给定的方法。

方法引用要用 `::` 运算符分隔方法名与对象或类名。主要有 3 种情况：

1. `object::instanceMethod`：方法引用等价于向方法传递参数的 lambda 表达式。例如 `System.out::println` 中，**`System.out` 是一个对象**，所以方法表达式等价于 `x -> System.out.println(x)`。
2. `Class::instanceMethod`：第一个参数会成为方法的隐式参数。例如 `String::compareToIgnoreCase` 等同于 `(x, y) -> x.compareToIgnoreCase(y)`。
3. `Class::staticMethod`：所有参数都传递到静态方法。例如 `Math::pow` 等价于 `(x, y) -> Math.pow(x, y)`。

**只有当 lambda 表达式的体只调用了一个方法而不做其他操作时，才能把 lambda 表达式重写为方法引用。**例如 `s -> s.length() == 0` 不止有方法调用，还有一个比较，所以这里不能使用方法引用。

可以在方法引用中使用 `this` 或 `super` 参数，即：`this/super::instanceMethod`。

#### 构造器引用

> p250

构造器引用类似于方法引用，只不过方法名为 `new`。例如，`Person::new` 是 Person 构造器的一个引用。

#### 变量作用域

> p250

Lambda 表达式可以*捕获*（capture）外围方法或类中的变量，并在表达式或代码块中使用，但是有几点需要注意：

1. lambda 表达式捕获的值必须是明确定义的；
2. lambda 表达式只能引用变量，不会改变变量的值；
3. lambda 表达式引用的变量必须是*事实最终变量*（effectively final），即这个变量值不会改变；
4. lambda 表达式**与嵌套块有相同的作用域**，所以不能声明一个与局部变量同名的参数。

### 内部类

*内部类*（inner class）是定义在另一个类中类。

* 内部类可以对同一个包中的其他类**隐藏**。
* 内部类方法可以访问定义这个类的作用域中的数据，包括原本私有的数据。

#### 内部类的特殊语法规则

> p259

* 表达式 `OuterClass.this` 表示外围类引用。
* 表达式 `outerObject.new` 可以实例化一个内部类的对象。
* 表达式 `OuterClass.InnerClass` 可以在外围类的作用域之外引用内部类。

#### 局部内部类

> p262

可以在**一个方法中局部**地定义一个类。声明局部类时不能有访问说明符（即 `public` 或 `private`）。局部类对**外部世界完全隐藏**，甚至定义它的方法所在的类中的其他代码也不能访问它。

#### 由外部方法访问变量

> p263

局部类不仅可以访问外部类的字段，还可以访问局部变量，不过那些局部变量必须是事实最终变量，即它们一旦赋值绝不会改变。

#### 匿名内部类

> p264

在使用局部内部类是，如果只想创建这个类的一个对象，甚至不需要为类指定名字。这样的类被称为*匿名内部类*（anonymous inner class）。一般地，语法如下：

```java
var objectName = new Supertype(construction paramaters)
										 {
										     inner class methods and data  
										 }
```

其中，SuperType 可以是接口，也可以是一个类。SuperType 如果是接口，那么匿名内部类就需要实现这个接口，如果是一个类，那么匿名内部类就要扩展这个类。

由于构造器的名字必须与类名相同，而匿名内部类没有类名，所以匿名内部类不能有构造器，但可以提供一个对象初始化块。

#### 静态内部类

> p267

如果内部类不需要访问外围类对象，就应该使用静态内部类，即将内部类声明为 `static`。

* 与常规内部类不同，静态内部类可以有静态字段和方法。
* 在接口中声明的内部类自动是 `public static` 的。
* 静态方法中的局部内部类必须是静态内部类。

### 代理

利用*代理*（proxy）可以在运行时创建实现了一组给定接口的新类。

#### 何时使用代理

> p273

代理类可以在运行时创建一个实现你所有指定接口的类，具体包含以下方法：

* 指定接口的全部方法；
* Object 类中的全部方法，例如，`toString`，`equals` 等。

必须提供一个*调用处理器*（invocation handler），即一个实现了 InvocationHandler 接口的类的对象，该接口只有一个方法：

```java
Object invoke(Object proxy, Method method, Object[] args)
```

#### 创建代理对象

> p273

要想创建一个代理对象，需要使用 Proxy 类的 `newProxyInstance` 方法。这个方法有三个参数：

* 一个*类加载器*（class loader）；
* 一个 Class 对象数组，每个元素对应需要实现的各个接口；
* 一个调用处理器。

接着要定义处理器，这取决于我们要用代理干什么。代理主要解决三件事：

* 将方法调用路由到远程服务器；
* 在运行的程序中将用户界面事件与动作关联起来；
* 为了调试，跟踪方法调用。

## 异常、断言和日志

### 处理错误

* 用户输入错误
* 设备错误
* 物理限制
* 代码错误

#### 异常分类

> p280

在 Java，所有异常对象都是派生于 Throwable 类的一个类实例。如果 Java 中内置的异常类不能满足需求，用户还可以创建自己的异常类。异常对象可以分为两类：

* Error 类：描述了 Java 运行时系统的内部错误和资源耗尽错误，这种情况除了通知用户并尽力妥善终止程序外干不了别的。
* Exception 类：
  * RuntimeException 类：由编程错误导致的异常。
    * 错误的强制类型转换。
    * 数组访问越界。
    * 访问 null 指针。
  * 其他异常：程序本身没有问题，但由于像 I/O 错误这类问题导致的异常。
    * 试图超越文件末尾继续读取数据。
    * 试图打开一个不存在的文件。
    * 试图根据给定的字符串查找 Class 对象，而这个字符串表示的类并不存在。

Java 语言规范将所有派生于 Error 类或 RuntimeException 类的所有异常都称为*非检查型*（unchecked）异常，所有其他的异常都称为*检查型*（checked）异常。

#### 声明检查型异常

>p282

方法不仅需要告诉编译器将要返回什么值，**还要告诉编译器有可能发生什么错误**。（像不像 GO 语言的思想？）如果一个方法有可能抛出多个检查型异常类型，那么就必须在方法的首部列出所有的异常类，每个异常类之间用逗号隔开。例如：

```java
class MyAnimation
{
  . . .
    public Image loadImage(String s) throws FileNotFoundException, EOFException
  	{
    	. . .
  	}
}
```

**NOTE：**

* 不需要声明 Java 的内部错误，即从 Error 类继承的异常。任何程序代码都有可能抛出那些异常，而且我们对此也完全无法控制。
* 不应该声明从 RuntimeException 类继承的那些非检查型异常。这些运行时错误完全在我们的控制之中，应该多花时间修正这些错误，而不是声明。

#### 如何抛出异常

> p284

如果一个已有的异常类能够满足你的要求，那么在这种情况下：

1. 找到一个合适的异常类。
2. 创建这个类的一个对象。
3. 抛出这个对象。

#### 创建异常类

> p285

当遇到任何标准异常类都无法描述清楚的问题时，我们可以定义一个派生于 Exception 的类，或者派生于 Exception 的某个子类（如 IOException）的类。

常规做法是，自定义的这个类应该包含两个构造器，一个是默认的构造器，另一个是包含详细描述信息的构造器（超类 Throwable 的 `toString` 方法会返回一个字符串，其中包含这个详细信息，这在调试中会非常有用）。例如：

```java
class FileFormatException extends IOException
{
  public FileFormatException() {}
  public FileFormatException(String gripe)
  {
    super(gripe);
  }
}
```

### 捕获异常

#### 捕获异常

> p286

要捕获一个异常，需要设置 `try/catch` 语句块：

```java
try
{
  // code
  // more code
  // more code
}
catch (ExceptionType e)
{
  // handler for this type
}
```

* 如果 `try` 语句块中的任何代码抛出了 `catch` 子句中指定的一个异常类，那么
  1. 程序将跳过 `try` 语句块的其余代码。
  2. 程序将执行 `catch` 子句中的处理器代码。
* 如果 `try` 语句块中的代码没有抛出任何异常，那么程序将跳过 `catch` 子句。
* 如果方法中的任何代码抛出了 `catch` 子句中没有声明的一个异常类型，那么这个方法就会立刻退出。

**QUESTION：**如果选择抛出异常还是捕获异常？

> 一般经验是，要捕获那些你知道如何处理的异常，而继续传播（即抛出）那些你不知道怎么处理的异常。但是有个例外：如果编写一个方法覆盖超类的方法，而这个超类没有抛出异常，你就必须捕获你的方法代码中出现的每一个检查型异常。

#### 捕获多个异常

> p288

为每个异常类型使用一个单独的 `catch` 子句，如果捕获后的动作一样，可以在同一个 `catch` 子句中可以捕获多个异常类型。例如：

```java
try
{
  // code that might throw exceptions
}
catch (FileNotFoundException | UnknownHostException e)
{
  // emergency action for missing files and unknown hosts
}
catch (IOException)
{
  // emergency action for all other I/O problems
}
```

异常对象可能包含有关异常性质的信息。使用 `e.getMessage()` 可以得到详细的错误消息，或者使用 `e.getClass().getName()` 得到异常对象的实际类型。

#### 再次抛出异常与异常链

> p289

可以在`catch` 子句中抛出一个异常。

#### finally 子句

> p290

不管是否有异常被捕获，`finally`子句中的代码都会被执行。

```java
try
{
  // code
  // more code
  // more code
}
catch (ExceptionType e)
{
  // handler for this type
}
finally
{
  // clean resources
}
```

**NOTE：**不要把改变控制流的语句（return，throw，break，continue）放在 `finally` 子句中！

#### try-with-Resources 语句

> p292

Try-with-resorces 语句（带资源的 `try` 语句）的最简形式为：

```java
try (Resource res = . . .)
{
  // work with res
}
```

`try` 块退出时，会自动调用 `res.close()`，还可以指定多个资源。例如：

```java
try (var in = new Scanner(new FileInputStream("words.txt"), StandardCharsets.UTF_8);
    var out = new PrinterWriter("out.txt", StandardCharsets.UTF_8))
{
  while (in.hasNext())
    System.out.println(in.next());
}
```

不管这个块如何退出，都会自动调用 `in.close()` 和 `out.close()`，就好像用了 `finally` 块一样。

### 使用异常的技巧

> p297

1. **异常处理不能代替简单的测试。**捕获异常花费的时间远超过简单的测试，因此我们必须遵循：只在异常情况下使用异常。
2. **不要过分地细化异常。**很多程序员习惯将每一条语句都分装在一个独立的 `try` 语句块中，这会导致代码量急剧膨胀，因此我们有必要将**整个任务**包在一个 `try` 语句块中。
3. **充分利用异常层次结构。**
   * 不要只抛出 RuntimeException 异常。应该寻找一个合适的子类或创建自己的异常类。
   * 不要只捕获 Throwable 异常，否则会使你的代码更难读、更难维护。
   * 充分考虑检查型异常和非检查型异常的区别。
   * 如果可以，应该将一种异常转化为另一种更加合适的异常。
4. **不要压制异常。**
5. **在检测错误时，苛刻比放任更好。**不要用返回一个虚拟值来代替抛出一个异常。
6. **不要羞于传递异常。**

### 使用断言

#### 断言的概念

> p300

断言机制允许在测试期间向代码中插入一些检查，而在生产代码中会自动删除这些检查。

Java 引入了关键字 `assert`，这个关键字有两种形式

1. ```java
   assert condition;
   ```

2. ```java
   assert condition : expression;
   ```

这两个语句都会计算条件，如果结果为 false，则会抛出一个 AssertionError 异常。在第二个语句中，表达式将传入 AssertionError 对象的构造器，并转换为一个消息字符串。AssertionError 对象并不存储具体的表达式值，因此无法在之后得到这个表达式值。

例如，要想断言 x 是一个非负数，只需要使用下面这条语句

```java
assert x >= 0;
```

如果还想将 x 的实际值传递给 AssertionError 对象，以便日后显示，那么就要写成

```java
assert x >= 0 : x;
```

**C++ NOTE：**C 语言中的 `assert` 宏将断言中的条件转换成一个字符串，当断言失败时，就会打印这个字符串，例如，若 `assert(x >= 0);` 失败，那么将打印出失败条件 “x >= 0”。但是在 Java 中，条件并不会自动地成为错误报告的一部分，如果希望看到这个条件，就必须将它以字符串的形式传递给 AssertionError 对象：`assert x >= 0 : "x >= 0"`。

#### 启用和禁用断言

> p301

启用或禁用断言是*类加载器*（class loader）的功能。在默认情况下，断言是禁用的。可以在运行程序时用 `-enableassertions` 或 `-ea` 选项启用断言：

```shell
java -enableassertions MyApp
```

也可以在某个类或整个包中启用断言，例如：

```shell
java -ea:MyClass -ea:com.mycompany.mylib MyApp
```

也可以用选项 `-disableassertions` 或 `-da` 在某个特定类和包中禁用断言：

```shell
java -ea:. . . -da:MyClass MyApp
```

### 日志

> P304

新手程序员都很熟悉在有问题的代码中插入一些 `System.out.println` 方法来帮助观察程序的这种操作，但是一旦解决问题以后就要将这些 `print` 语句删除，接下来又出现问题就需要再插入几个 `print` 语句，这种反反复复的删改语句的行为很是麻烦。Java 中的日志 API就是为了解决这个问题的，它有如下优点：

* 可以轻松取消全部日志记录，或者仅仅取消某个级别以下的日志，同样的如果想要再次打开日志开关也很容易。
* 可以很简单地禁止日志记录。
* 日志记录可以被定向到不同的处理器，如控制台显示、写至文件，等等。
* 日志记录器和处理器都可以对记录进行过滤。
* 日志记录可以采用不同的方式格式化，例如纯文本或 XML。
* 应用程序可以使用多个日志记录器，它们使用与包名类似的有层次结构的名字，例如，com.mycompany.myapp。
* 日志系统的配置由配置文件控制。

#### 基本日志

> p305

要生成简单的日志记录，可以使用*全局日志记录器*（global logger）并调用其 `info` 方法：

```java
Logger.getGlobal().info("some infos")
```

如果在合适的地方（如 `main` 的最前面）调用

```java
Logger.getGlobal().setLevel(Level.OFF);
```

将会取消所有日志。

#### 高级日志

> p305

如果不想所有的日志都记录到一个全局日志记录器中，你可以定义自己的日志记录器，具体地，调用 `getLogger` 方法创建或获取日志记录器：

```java
private static final Logger mylogger = Logger.getLogger("com.mycompany.myapp");
```

日志记录器的层次性比包名的**更强**，包与父包之间没有语义关系，但是日志记录器的父与子之间将共享某些属性。例如，如果对日志记录器“com.mycompany”设置了日志级别，它的子日志记录器也会继承这个级别。

通常有以下 7 个日志级别：

1. SEVERE
2. WARNING
3. INFO
4. CONFIG
5. FINE
6. FINER
7. FINEST

在默认情况下，只记录前 3 个级别。也可以设置一个不同的级别，例如：

```java
logger.setLevel(Level.FINE);  // 现在，FINE 以及所有更高级别的日志都会被记录
logger.setLevel(Level.ALL);  // 开启所有级别的日志记录
logger.setLevel(Level.OFF);  // 关闭所有级别的日志记录
```

所有级别都可以分开记录日志，例如：

```java
logger.warning(message);
logger.fine(message);
```

或者用 `log` 方法记录并指定级别，例如：

```java
logger.log(Level.FINE, message);
```

## 泛型程序设计

### 为什么要使用泛型程序设计

*泛型程序设计*（generic programming）意味着编写的代码可以对多种不同类型的对象重用。

#### 类型参数的好处

> p327

*类型参数*（type parameter）用来指示元素的类型。

不用类型参数时：

```java
ArrayList files = new ArrayList();
. . .
String filename = (String) files.get(0);
```

* 当获取一个值时必须进行强制类型转换。
* 没有错误检查。

使用类型参数时：

```java
var files = new ArrayList<String>();
```

* 更易读。
* 更安全。

### 定义简单泛型类

> p328

*泛型类*（generic class）就是有一个或多个类型变量的类。例如：

```java
public class Pair<T>
{
  public T first;
  public T second;
  
  public Pair()
  {
    first = null;
    second = null;
  }
  
  public Pair(T first, T second)
  {
    this.first = first;
    this.second = second;
  }
  
  public T getFirst() { return first; }
  public T getSecond() { return second; }
  
  public void setFirst(T newValue) { first = newValue; }
  public void setSecond(T newValue) { second = newValue; }
}
```

也可以引入多个类型变量，例如：

```java
public class Pair<T, U> { . . . }
```

**NOTE：**Java 库使用变量 E 表示集合的元素类型，K 和 V 分别表示表的键和值。T（必要时还可以用相邻的字母 U 和 S）表示“任意元素”。

可以用具体的类型替换类型变量来*实例化*（instantiate）泛型类型，例如：`Pair<String>`

### 泛型方法

> p330

还可以顶一个带有类型参数的方法。例如：

```java
class ArrayAlg
{
  public static <T> T getMiddle(T... a)
  {
    return a[a.length / 2];
  }
}
```

类型变量 `<T>` 放在修饰符（这里是 `public static`）的后面，并放在返回类型的前面。泛型方法可以在普通类中定义，也可以在泛型类中定义。

当调用一个泛型方法时，可以把具体类型包围在尖括号中，放在方法名前面。例如：

```java
String middle = ArrayAlg.<String>getMiddle("John", "Q.", "Public");
```

### 类型变量的限定

> p331

可以对类型变量 `T` 设置一个*限定*（bound）：

```java
<T extends BoundingType>
```

表示 `T` 应该是*限定类型*（bounding type）的*子类型*（subtype）。T 和 限定类型可以是类，也可以是接口。选择关键字 `extends` 是因为它更接近子类型的概念，并且 Java 的设计者也不打算在语言中再添加一个新的关键字。

一个类型变量或通配符可以有多个限定，限定类型之间用 `&` 隔开，类型变量之间用 `,` 隔开。例如：

```java
<T extends Cpmparable & Serializable>
```

**最多**有一个类型可以是类，而且它必须是限定列表中的**第一个**限定。

### 泛型代码和虚拟机

虚拟机没有泛型类型对象——所有对象都属于普通类，所有编译器会“擦除”类型参数。

#### 类型擦除

> p333

无论何时定义一个泛型类型，都会自动提供一个相应的*原始类型*（raw type），类型变量会被*擦除*（erased），并替换为**第一个**限定类型，如果没有限定类型，则替换为 Object。

**C++ NOTE：**Java 泛型与 C++ 模板在这点上有很大的区别。C++ 会为每个模板的实例化产生不同的类型，这一现象被称为“模板代码膨胀”。Java 则不存在这个问题。

### 限制与局限性

* 不能用基本类型实例化类型参数。
* 运行时类型查询只适用于原始类型。
* 不能创建参数化类型的数组。
* Varargs 警告。
* 不能实例化类型参数。
* 不能构造泛型参数。
* 泛型类的静态上下文类型变量无效。
* 不能抛出或捕获泛型类的实例。
* 可以取消对检查型异常的检查。
* 注意擦除后的冲突。

#### 不能用基本类型实例化类型参数

> p338

不能用基本类型代替类型参数，例如，没有 `Pair<double>`，只有 `Pair<Double>`。原因就是类型擦除。

#### 运行时类型查询只适用于原始类型

> p338

所有的类型查询（`instanceof`、`getClass` 和强制类型转换）只产生原始类型。例如：

```java
if (a instanceof Pair<String>)  // ERROR! 仅仅是测试 a 是否是任意类型的一个 Pair
  
if (a instanceof Pair<T>)  // ERROR!! 同上
  
Pair<String> p == (Pair<String>) a;  // warning--can only tesy that a is a Pair

Pair<String> stringPair = . . .;
Pair<Employee> employeePair = . . .;
if (stringPair.getClass() == employeePair.getClass())  // always true
```

总结：

* 对泛型类使用 `instanceof` 时，编译器会报错。
* 对泛型类使用强制类型转换时，会得到一个警告。
* 对泛型类使用 `getClass` 时，始终返回原始类型，例如 `Pair.class`。

#### 不能创建参数化类型的数组

> p338

不能实例化参数化类型的数组，例如：

```java
var table = new Pair<String>[10];  // ERROR!
```

原因仍在类型擦除，擦除之后 table 的类型是 pair[]，会导致即使存放了不同参数类型的 Pair 实例也会被允许，因此干脆直接禁止创建参数化类型的数组。

可以用 `ArrayList<Pair<String>>` 创建。

#### Varargs 警告

> p339

考虑一个向参数个数可变的方法传递一个泛型类型的实例，例如：

```java
public static <T> void addAll(Collection<T> coll, T... ts)
{
  for (T t : ts) coll.add(t);
}
```

实际上参数 ts 是一个数组，包含提供的所有实参，这也意味着如果要调用该方法，Java 虚拟机就必须建立一个 `Pair<String>` 数组，这就违反了上一章的规则。但是在这种情况下，Java 的规则有所放松，你只会得到一个警告，而不是报错。

可以采用两种方法来抑制这个警告：

* 为所有包含 `addAll` 调用的方法增加注解 `@SuppressWarnings("unchecked")`
* 在 Java 7 中，可以用 `@SafeVarargs` 直接注解 `addAll` 方法。但是要注意的是 **`@SafeVarargs` 只能用于声明为 `static`、`final` 或（Java 9 中）`private` 的构造器和方法**，所有其他的方法都可能被覆盖，导致这个注解没什么意义。

#### 不能实例化类型变量

> p340

不能在类似 `new T(. . .)` 的表达式中使用类型变量。例如，下面的 `Pair<T>` 构造器就是非法的：

```java
public Pair() { first = new T(); second = new T(); }  // ERROR!
```

类型擦除将 T 变成 Object，而你肯定不希望调用 `new Object()`。

#### 泛型类的静态上下文中类型变量无效

> p342

不能在静态字段或方法中引用静态变量。

### 泛型类型的继承规则

> p346

无论 `S` 和 `T` 是什么关系，`Pair<S>` 与 `Pair<T>` 都**没有**任何关系。例如 `Employee` 是` Manager` 的超类，但是 `Pair<Manager>` 和 `Pair<Employee>` 之间没有任何关系。因此也不能类型转换。

泛型类可以扩展或实现其他的泛型类。例如，`ArrayList<T>` 实现了 `List<T>` 接口，那么一个 `ArrayList<Manager>` 可以转换为一个 `List<Manager>`。

### 通配符类型

#### 通配符概念

> p348

在通配符类型中，允许类型参数发生变化。例如，

```java
Pair<? extends Employee>
```

表示任何泛型 `Pair` 类型，它的类型参数是 `Employee` 的子类，如 `Pair<Manager>`，但不能是 `Pair<String>`。

**NOTE：**占位符 `T` 代表一个明确的类型，在我们需要知道实际类型的地方，我们需要能够推算出它。相反，通配符 `?` 表示任何类型，我永远不需要也不能知道这个类型是什么。你可以使用 `extends` 和 `super`  来限制通配符，但是没有办法得到实际的类型。

对于子类型限定来说，由于不能确定对象的具体类型，所以**无法写入**。

#### 通配符的超类型限定

> p349

通配符限定与类型变量限定十分相似，但是通配符限定有一个额外的能力，就是可以指定一个*超类型限定*（supertype bound），如下所示：

```java
? super Manager
```

这一通配符限制为 `Manager` 的所有超类型。

通俗来讲，**带有超类型限定的通配符允许你写入一个泛型对象（只写），而带有子类型限定的通配符允许你读区一个泛型对象（只读）。**

#### 通配符捕获

> p352

通配符不是类型变量，因此不能在编写代码时使用 `?` 作为一种类型。例如以下代码是错误的：

```java
public static void swap(Pair<?> p)
{
  ? t = p.getFirst();  // ERROR!!
  p.setFirst(p.getSecond());
  p.setSecond(t);
}
```

我们可以通过类型参数 `T` 来*捕获通配符*。例如：

```java
public static void swap(Pair<?> p)
{
  swapHelper(p);
}

public static void swapHelper(Pair<T> p)
{
  T t = p.getFirst(); 
  p.setFirst(p.getSecond());
  p.setSecond(t);
}
```

### 反射和泛型

#### 虚拟机中的泛型类型信息

> p356

例：

```java
public static <T extends Comparable<? super T>> T min(T[] a)
```

擦除以后得到：

```java
public static Comparable min(Comparable[] a)
```

为了表述泛型类型声明，可以使用 java.lang.reflect 包中的接口 Type。这个借口包含以下子类型：

* Class 类，描述具体类型。
* TypeVariable 接口，描述类型变量。例如 `T extends Comparable<? super T>`。
* WildcardType 接口，描述通配符。例如 `? super T`。
* ParameterizedType 接口，描述泛型类或接口类型。例如 `Comparable<? super T>`。
* GenericArratType 接口，描述泛型数组。例如 `T[]`。

## 集合

### Java集合框架

#### 集合接口与实现分离

> p365

与现代的数据结构类库的常见做法一样，Java 集合类库也将*接口*（interface）与*实现*（implementation）分离。

在研究 API 文档时，会发现另外一组名字以 Abstract 开头的类，例如，`AbstractQueue`。这些类是为类库实现者设计的，如果想要实现自己的队列类，会发现扩展 `AbstractQueue` 类要比实现 `Queue` 接口中的所有方法轻松得多。

#### Collection 接口

> p368

在 Java 类库中，集合类的基本接口是 `Collection` 接口。这个接口有两个基本方法：

```java
public interface Collection<E>
{
  boolean add(E element);  // 添加元素，如果改变了集合，就返回 true，否则返回 false。
  //例如，向 set 中添加一个元素，而这个元素已经存在，就返回 false。
  Iterator<E> iterator();  // 迭代
  . . .
}
```

#### 迭代器

> p368

`Iterator` 接口包含四个方法：

```java
public interface Iterator<E>
{
  E next();
  boolean hasNext();
  void remove();
  default void forEachRemaining(Consumer<? super E> action);  // Consumer 是常用函数式接口
}
```

`forEachRemaining` 可以不用循环而遍历任何集合，只需要提供一个 lambda 表达式，将对迭代器的每一个元素调用这个 lambda 表达式。

```java
iterator.forEachRemaining(element -> do something with element);
```

**C++ NOTE：**在 C++ 标准模板库中，迭代器是根据数组索引建模的，可以通过数组索引 i 查找数组元素，也可以通过调用 `i++` 将数组索引向前移动。但是 Java 迭代器的查找操作与位置变更更紧密耦合，**迭代器查找一个元素的唯一方法是调用 `next`**。因此可以认为 Java 迭代器**位于两个元素之间**。

`Iterator` 接口的 `remove` 方法将会删除上次调用 `next` 方法时返回的元素，且 `next` 方法和 `remove` 方法调用之间存在依赖性，如果调用 `remove` 之前不调用 `next` 将是**不合法的**。

```java
Iterator<String> it = c.iterator();
it.next();
it.remove;
it.next();  // must do it!
it.remove();
it.remove();  // ERROR!
```

#### 泛型使用方法

> p371

`Collection` 接口声明了很多有用的方法，所有的实现类都必须提供这些方法，下面列举了其中一部分：

```java
int size();
boolean isEmpty();
boolean contains(Object obj);
boolean containsAll(Collection<?> c);
boolean equals(Object other);
boolean addAll(Collection<? extends E> from);
boolean remove(Object obj);
boolean removeAll(Collection<?> c);
void clear();
```

如果实现 `Collection` 接口的每一个类都要提供那么多的例行方法，这将是一件很烦人的事情，为了能够让实现者更容易地实现这个接口，Java 类库提供了一个类 `AbstractCollection`，它保持基础方法 `size` 和 `iterator` 仍为抽象方法，但是为实现者实现了其他例行方法。

### 集合框架中的接口

> p373

Java 集合框架为不同类型的集合定义了大量接口，如下图所示：

![](/Users/asssaver/github/blog/source/images/20230117/1.jpeg)

集合有两个基本接口：`Collection` 和 `Map`。`Map` 中的插入和读取方法为：

```java
V put(K key, V value);
V get(K key);
```

`List` 是一个*有序集合*（ordered collection），元素会增加到容器中的特定位置。可以采用两种方式访问元素：

* 使用迭代器访问。
* 使用一个整数索引来访问。

后者也被称为*随机访问*（random access），代表着可以按任意顺序访问元素，而迭代器必须顺序地访问元素。`List` 接口定义了多个用于随机访问的方法：

```java
void add(int index, E element);
void remove(int index);
E get(int index);
E set(int index, E element);
```

`ListIterator` 接口是 `Iterator` 的一个子接口。它定义了一个方法用于在迭代器位置前面增加一个元素：

```java
void add(E element);
```

`Set` 接口等同于 `Collection` 接口，但是其方法的行为有更严谨的定义。之所以要在 `Collection` 接口外建立一个单独的接口 `Set`，是因为从概念上讲并不是所有集合都是集，建立一个 `Set` 接口可以允许程序员编写只接受集的方法。

### 具体集合

| 集合类型        | 描述                                         |
| --------------- | :------------------------------------------- |
| ArrayList       | 可以动态增长和缩减的一个索引序列             |
| LinkedList      | 可以在任何位置高效插入和删除的一个有序序列   |
| ArrayDeque      | 实现为循环数组的一个双端队列                 |
| HashSet         | 没有重复元素的一个无序集                     |
| TreeSet         | 一个有序集                                   |
| EnumSet         | 一个包含枚举类型值的集                       |
| LinkedHashSet   | 一个可以记住元素插入次序的集                 |
| PriorityQueue   | 允许高效删除最小元素的一个集合               |
| HashMap         | 存储键/值关联的一个数据结构                  |
| TreeMap         | 键有序的一个映射                             |
| EnumMap         | 键属于枚举类型的一个映射                     |
| LinkedHashMap   | 可以记住键/值项添加次序的一个映射            |
| WeakHashMap     | 值不会在别处使用时就可以被垃圾回收的一个映射 |
| IdentityHashMap | 用 `==` 而不是用 `equals` 比较键的一个映射   |

![](/Users/asssaver/github/blog/source/images/20230117/2.jpeg)

#### 链表

> p375

在 Java 程序设计语言中，所有链表实际上都是*双向链表*（doubly linked）。

`LinkedList.add` 方法将对象添加到链表的尾部。如果要将元素添加到链表的中间，就需要用到迭代器。集合类库提供了一个子接口 `ListIterator`，其中包含 `add` 方法：

```java
interface ListIterator<E> extends Iterator<E>
{
  void add(E element);
  . . .
}
```

与 `Collection.add` 不同，这个方法不返回 boolean 类型的值，因为它假定 `add` 操作总是会改变链表。

另外，`ListIterator` 接口有两个方法，可以用来反向遍历链表：

```java
E previous();  // 对应 next()
boolean hasPrevious();  // 对应 hasNext()
```

**NOTE：**如果调用了 `previous`，`remove` 就会删除后边的元素。

`set` 方法用一个新元素替换调用 `next` 或 `previous` 方法返回的元素。

链表在并发时容易出现问题，例如：一个迭代器指向一个元素前面的位置，而另一个迭代器刚刚删除了这个元素，那么前一个迭代器现在就是无效的，而且不能再使用了。链表迭代器可以检测到这种修改，并抛出 ConcurrentModificationException 异常。

有一种简单的方法可以检测到并发修改。集合可以跟踪更改操作的次数，没个迭代器都会为他负责的更改操作维护一个单独的更改操作数，在没个迭代器方法的开始处，迭代器会检查它自己的更改操作数是否与集合的更改操作数相等，如果不一致，就抛出一个 ConcurrentModificationException 异常。

`LinkedListed` 提供了一个用来随机访问某个特定元素的 `get` 方法，但我们不应该使用它，因为它效率**极低**。除非链表很小，这时候不需要担心 `get` 的开销，但是数据量小的时候不如使用数组或 `ArrayList`。

#### 数组列表

> p384

`Vector` 是**同步**的，可以安全地从两个线程访问一个 `Vector` 对象，但是如果只有一个线程，代码就会在同步操作上浪费大量时间。因此建议在不需要同步时使用 `ArratList`，而不要使用 `Vector`。

#### 散列集

> 385

链表和数组允许你根据意愿指定元素的次序，但是如果想要查看某个指定的元素又不记得它的位置，就需要访问所有元素。*散列表*（hash table）可以用于快速查找对象，它为每个对象计算一个整数，称为*散列码*（hash code）。

**在 Java 中，散列表用链表数组实现。**每个列表被称为*桶*（bucket）。如果想要将元素插入桶，但桶已经被填充，这种现象被称为*散列冲突*（hash collision）。这时需要将新对象与桶中的所有对象进行比较，查看这个对象是否已经存在。如果散列表太满，就需要*再散列*（rehashed）。*装填因子*（load factor）可以确定何时对散列表进行再散列。例如，装填因子为 0.75，说明表中已经填满了 75% 以上，就会自动再散列，新表的桶数是原来的两倍。

![](/Users/asssaver/github/blog/source/images/20230117/3.jpeg)

#### 树集

> p388

树集是一个*有序集合*（sorted collection），值将自动排序。树集的底层实现是*红黑树*（red-black tree），关于红黑树的详细介绍见《算法导论》。

#### 优先队列

> p392

*优先队列*（priority queue）没有对所有元素进行抛许，只是让最小（或最大）的元素移动到顶端。底层实现是*堆*（heap）。

### 映射

#### 基本映射操作

> p394

Java 类库为映射提供了两个通用的实现：`HashMap` 和 `TreeMap`。

* 散列映射对**键**进行散列，散列和比较函数**只应用于键**，与键关联的值不进行散列或比较。
* 树映射根据键的顺序将元素组织为一个搜索树。

散列映射更快，如果不需要按照有序的顺序访问键，最好选择散列映射。

使用 `put` 和 `get` 来存放和读取元素，如果映射中没有存储给定的键，`get` 将返回 `null`。或者用 `getOrDefault` 方法来返回默认值。例如：

```java
Map<String, Integer> scores = . . .;
int score = scores.getOrDefault(id, 0);
```

`remove` 方法删除给定键对应的元素，`size` 方法返回映射中的元素数。

要迭代处理映射的键和值，最容易的方法是使用 `forEach` 方法，可以提供一个接受键和值的 lambda 表达式。例如：

```java
scores.forEach((k, v) ->
              System.out.println("key=" + k + ",value=" + v))
```

#### 更新映射条目

> p397

考虑使用映射统计一个单词在文件中出现的频率，对于第一次出现的单词，为了防止 `get` 方法返回 `null`，有三种补救方法：

* 使用 `getOrDefault` 方法。

  ```java
  counts.put(word, counts.getOrDefault(word, 0) + 1);
  ```

* 使用 `putIfAbsent` 方法。只有当键原先不存在或映射为 `null` 时才会放入一个值。

  ```java
  counts.putIfAbsent(word, 0);
  counts.put(word, counts.get(word) + 1);
  ```

* 使用 `merge` 方法。如果键不存在，就将 word 与 1 关联，否则使用 `Integer::sum` 函数组合原值和 1。

  ```java
  counts.merge(word, 1, Integer::sum);
  ```

#### 映射视图

> p398

有三种视图：

* 键集。返回键集的方法为：`Set<K> keySet()`。`keySet` 返回的不是 `HashSet` 或 `TreeSet`，而是实现了 `Set` 接口的另外某个类的对象。
* 值集合（不是一个集）。返回值集合的方法为：`Collection<V> values()`。
* 键/值对集。返回键/值对集的方法为：`Set<Map.Entry<K, V>> entrySet()`。

#### 弱散列映射

> p399

`WeakHashMap` 可以从长期存活的映射表中删除那些无用的值。`WeakHashMap` 使用*弱引用*（weak reference）保存键，`WeakReference` 对象将包含另一个对象的引用，在这里就是一个散列表键。如果某个对象已经没有其他人引用了，垃圾回收器会将其回收，而如果某个对象**只由 `WeakReference` 引用**，垃圾回收器也会将其回收，同时将引用这个对象的弱引用放入一个队列。`WeakHashMap` 将周期性地检查队列，并删除相关联的映射条目。

#### 链接散列表与映射

> p400

`LinkedHashSet` 和 `LinkedHashMap` 类会记住插入元素项的顺序。以双向链表实现。

#### 枚举集与映射

> p401

`EnumSet` 是一个枚举类型元素集的高效实现。由于枚举类型只有有限个实例，所以 `EnumSet` 内部用位序列实现，如果对应的值在集中，则相应的位被置为1。

`EnumSet` 类没有公共的构造器，要用静态工厂方法构造这个集。例如：

```java
enum Weekday { MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY };
EnumSet<Weekday> always = EnumSet.allOf(Weekday.class);
EnumSet<Weekday> never = EnmuSet.noneOf(Weekday.class);
EnumSet<Weekday> workday = EnumSet.range(Weekday.MONDAY, Weekday.FRIDAY);
EnumSet<Weekday> mwf = EnumSet.of(Weekday.MONDAY, Weekday.WEDNESDAY, Weekday.FRIDAY);
```

可以使用 `Set` 接口的常用方法来修改 `EnumSet`。

#### 标识散列映射

> p402

类 `IdentityHashMap` 有特殊的用途。在这个类中，键的散列值不使用 `hashCode` 函数计算的，而是用 `System.identityHashCode` 计算的，这是根据对象的内存地址计算散列码时所使用的方法。

### 视图与包装器

可以使用*视图*（view）获得所有实现了 `Collection` 接口或 `Map` 接口的对象。例如，使用映射类 `keySet` 方法返回一个实现了 `Set` 接口的类对象，并用这个类的方法操纵原映射，这种集合称为视图。

#### 小集合

> p404

Java 9 引入了一些静态方法，可以生成给定元素的集、列表或映射。例如：

```java
List<String> names = List.of("Peter", "Paul", "Mary");
Set<Integer> numbers = Set.of(2, 3, 5);
Map<String, Integer> scores = Map.of("Peter", 2, "Paul", 3, "Mary", 5);
```

元素、键或值**不能**为 `null`。

这些集合对象是**不可修改的**。如果试图改变它们的内容，会导致一个 UnsupportedOperationException 异常。如果需要一个可更改的集合，可以把这个不可修改的集合传递到构造器。例如：

```java
var names = new ArrayList<>(List.of("Peter", "Paul", "Mary"));
```

`Collections.nCopies(n, anObject)` 静态方法会返回一个实现了 `List` 接口的**不可变**的对象，里面的元素 anObject 重复 n 次。

#### 子范围

> p405

可以为集合建立*子范围*（subrange）视图。例如：

```java
List<Employee> group2 = staff.subList(10, 20);  // 取出第10～19个元素，即左边闭区间右边开区间。
```

**对子范围的任何操作都会反映到整个列表。**例如：`group2.clear();` 不仅会清空 group2，还会将元素自动从 staff 列表中删除。

对于有序集和映射，可以使用**排序顺序**而不是元素位置来建立子范围：

```java
SortedSet<E> subSet(E from, E to);
SortedSet<E> headSet(E to);
SortedSet<E> tailSet(E from);

SortedSet<E> subMap(K from, K to);
SortedSet<E> headMap(K to);
SortedSet<E> tailMap(K from);
```

Java 6 还引入了 `NavigableSet` 接口来指定是否包含边界：

```java
NavigableSet<E> subSet(E from, boolean fromInclusive, E to, boolean toInclusive);
NavigableSet<E> headSet(E to, boolean toInclusive);
NavigableSet<E> tailSet(E from, boolean fromInclusive);
```

#### 不可修改的视图

> p405

可以使用下面 8 个方法来生成集合的*不可修改视图*（unmodifiable view）：

```java
Collections.unmodifiableCollection
Collections.unmodifiableList
Collections.unmodifiableSet
Collections.unmodifiableSortedSet
Collections.unmodifiableNavigableSet
Collections.unmodifiableMap
Collections.unmodifiableSortedMap
Collections.unmodifiableNavigableMap
```

#### 同步视图

> p406

例如，`Collections` 类的静态 `synchronizedMap` 方法可以将任何一个映射转化成有同步访问方法的 `Map`：

```java
var map = Collections.synchronizedMap(new HashMap<String, Employee>());
```

#### 检查型视图

> p407

检查型视图用来对泛型类型可能出现的问题提供调试支持，比如检查插入的对象类型是否正确。例如：

```java
var strings = new ArrayList<String>();
List<String> safeStrings = Collections.checkedList(strings, String.class);
```

**NOTE：**检查型视图只能完成虚拟机可以完成的运行时检查。例如，对于 `ArrayList<Pair<String>>`，由于虚拟机有一个原始 `Pair` 类，所以无法阻止插入 `Pair<Date>`。

### 算法

#### 批操作

> p417

```java
coll1.removeAll(coll2);  // 从coll1中删除所有在coll2中出现的元素
coll1.retainAll(coll2);  // 从coll1中删除所有未在coll2中出现的元素
```

#### 集合与数组的转换

> p418

```java
String[] values = . . .;
var staff = new HashSet<>(List.of(values));  // 数组转集合
String[] values1 = staff.toArray(new String[0]);  // 集合转数组
```

### 遗留的集合

#### 属性映射

> p421

*属性映射*（property map）是一个特殊类型的映射结构，它有下面三个特性：

* 键与值都是字符串。
* 这个映射可以很容易地保存到文件以及从文件加载。
* 有一个二级表存放默认值。

实现属性映射的 Java 类为 `Properties`。

#### 栈

> p424

`Stack` 类扩展了 `Vector` 类，所以它不仅有 `push`、`pop` 和 `peek` 的栈操作方法，还有并非栈操作的 `insert` 和 `remove` 方法，可以在任何地方插入和删除值。

## 图形用户界面程序设计

### 显示窗体

在 Java 中，顶层窗口（就是没有包含在其他窗口中的窗口）被称为*窗体*（frame）。

#### 创建窗体

> p431

```java
package simpleframe;

import java.awt.*;
import javax.swing.*;  // 包名javax表示这是一个Java扩展包

public class SimpleFrameTest
{
    public static void main(String[] args)
    {
        EventQueue.invokeLater(() ->
        {
            var frame = new SimpleFrame();
            frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
            frame.setVisible(true);  // 需要手动显示窗体
        });
    }
}

/**
 * JFrame中默认的窗体大小是0*0像素，我们在这里定义为800*600像素，
 * 这是SimpleFrame和JFrame的唯一区别。
 */
class SimpleFrame extends JFrame  
{
    private static final int DEFAULT_WIDTH = 800;
    private static final int DEFAULT_HEIGHT = 600;

    public SimpleFrame()
    {
        setSize(DEFAULT_WIDTH, DEFAULT_HEIGHT);
    }
}
```

在每个 Swing 程序中，有两个技术问题需要强调。

首先，所有的 Swing 组件必须由*事件分派线程*（event dispatch thread）配置，这是控制线程，它将鼠标点击和按键等事件传递给用户接口组件。

```java
EventQueue.invokeLater(() ->
                       {
                         . . .
                       });
```

其次，定义用户关闭这个窗体时的相应动作。对于上述程序而言，我们只是让程序简单地退出。

```java
frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
```

在包含多个窗体的程序中，不能在用户关闭其中的一个窗体时就让程序退出。在默认情况下，用户关闭窗体时只是将窗体隐藏起来，而程序没有终止。

#### 窗体属性

> p433

* `setLocation` 方法和 `setBounds` 方法用于设置窗体的位置。
* `setIconImage` 方法用于告诉窗口系统在标题栏、任务切换窗口等位置显示那个图标。
* `setTitle` 方法用于改变标题栏的文字。
* `setResizable` 利用一个 boolean 值确定是否允许用户改变窗体的大小。

### 在组件中显示信息



## Swing 用户界面组件

## 并发
