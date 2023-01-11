---
title: 【读书笔记】《Java核心技术 卷I》
date: 2022-12-22 09:57:27
tags: Java
categories: 读书笔记
---
[Java 在线 API 文档](http://docs.oracle.com/javase/9/docs/api)

Java的基本程序设计结构
---
### 数据类型
#### char 类型
> p32

char 类型原本用于表示单个字符，**不过现在不一样了**。如今，有些 Unicode 字符可以用一个 char 值描述，而另外一些 Unicode 字符则需要两个 char 值。
<!--more-->

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

*块（block）*是指由若干条 Java 语句组成的语句，并用一对大括号括起来（**也就是说，大括号可以单独成对使用，不必搭配语句**）。块确定了变量的作用域。一个块可以*嵌套*在另一个块中，但**不能在嵌套的两个块中声明同名的变量**。

#### 多重选择：switch 语句
> p72

```java
switch (choice)
{
	case 1:
		...
		break;
	case 2:
		...
		break;
	...
	default:
		...
		break;
}
```

case 标签可以是：

* 类型为 char、byte、short 或 int 的常量表达式；
* 枚举常量；
* 从 Java 7 开始，case 标签还可以是字符串字面量。

在 switch 语句中使用枚举常量时，不必在每个标签中指明枚举名，可以由 switch 的表达式值推导出，例如：

```java
enum Size {SMALL, MEDIUM, LARGE, EXTRA_LARGE};
Size sz = Size.MEDIUM;

switch (sz)
{
	case SMALL:  // 不用写成 Size.SMALL
		...
	...
}
```

#### 终端控制流程的语句
> p74

Java 中没有 `goto`，但是新增了一种**带标签的** `break` 语句来代替 `goto`，例：

```java
read_data:
while (...)
{
	for (...)
	{
		...
		if (...)
			break read_data;
	}
}
```

**NOTE：**事实上，可以将标签应用到任何语句，甚至可以将其应用到 if 语句或者块语句上，但不建议这么使用。

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
	...
}
```
或者用 java 9 中提供的新方法：
```java
public Employee(String n, double s, int year, int month, int day)
{
	name = Objects.requireNonNullElse(n, "unknown");
	...
}
```
* “严格型”方法则是干脆拒绝 null 参数：
```java
public Employee(String n, double s, int year, int month, int day)
{
	Objects.requireNonNull(n, "The name cannot be null!");
	name = n;
	...
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
public static final ...
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

var a = new Employee(...);
var b = new Employee(...);
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

关键词 `this` 除了指示一个方法的隐式参数以外，还有另一个作用：如果**构造器的第一个语句**形如 `this(...)`，那么这个构造器将调用**同一个类**的另一个构造器。例：

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
	...
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
	...
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
	...
}
```

final 类中的所有方法自动地成为 final 方法，但是也可以手动声明正常类中的某个特定方法为 final，这样可以阻止子类覆盖。

```java
public class Employee
{
	...
	public final String getName()
	{
		return name;
	}
	...
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
	...
}
```

#### 抽象类
> p169

可以使用 `abstract` 关键词将方法声明为抽象方法，抽象方法不用提供具体实现。包含一个或多个抽象方法的类本身**必须**被声明为抽象类。例：

```java
public abstract class Person
{
	...
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