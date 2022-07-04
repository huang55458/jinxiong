[TOC]



### `API` 

##### `API`文档

JDK 中包含大量的 API 类库，所谓 API（Application Program Interface,应用程序编程接口）就是一些已写好，可供直接调用的功能（在Java 语言中，这些功能以类的形式封装）

JDK API 包含的类库功能强大，经常使用的有：字符串操作、集合操作、文件操作、输入输出操作、网络操作、多线程等

JDK 包结构：为了便于使用和维护，JDK类库按照包结构划分，不同功能的类划分在不同的包中

| 包            | 功能                                                         |
| :------------ | :----------------------------------------------------------- |
| java.lang     | java程序的基础包，如字符串、多线程等，该包中的类使用的频率非常高，不需要 import ，可以直接使用 |
| java.util     | 常用工具类，如集合、随机数产生器、日历、时钟等               |
| java.io       | 文件操作，输入/输出操作                                      |
| java.net      | 网络操作                                                     |
| java.math     | 数学运算相关操作                                             |
| java.security | 安全相关操作                                                 |
| java.sql      | 数据库访问                                                   |
| java.text     | 处理文件、日期、数字、信息的格式                             |

文档注释规范

以 /** 开始，以 */ 结束；加在类和方法的开头，用于说明作者、时间、版本、要实现功能的详细描述等信息；通过 javadoc 工具，可以轻松的将此注解转换为 HTML 文档说明；学习者和程序员主要通过文档了解 API 的功能

文档注释不同于普通的注释（ // ...  或 /* ... ... */ ）,普通注释写在程序之中，用于程序员进行代码维护和交流，无法通过工具生成文档，而文档注释（ /** ... ... */ ）写在类和方法的开头，专门用于生成供 API 使用者进行参考的文档资料

**文档注释的生成**

<img src=".\note_imgs\javadoc1.png" style="zoom: 67%;" />

<img src=".\note_imgs\javadoc2.png" style="zoom:67%;" />

其他命令行实参： `-encoding UTF-8 -charset UTF-8 -author -version -windowtitle "文档HTML页面标签的标题"`

##### `String` 及其常用 `API`

`String` 是不可变对象，`java.lang.String` 使用 `final` 修饰，不能被继承；字符串底层封装了字符串数组及针对字符数组的操作算法； 字符串一旦创建，对象永远无法改变，但字符串引用可以重新赋值；`java` 字符串在内存中采用 `Unicode` 编码，每一个字符都为两个字节，字符串本质是转换成 `char[]`，`char` 字符使用 `Unicode` 编码格式占据两个字节

**`String` 常量池**

java 为了提高性能，静态字符串（字面量、常量、常量连接的结果）在常量池中创建，并尽量使用同一个对象，重用静态字符串；对于重复出现的字符串直接量，JVM 会首先在常量池中查找，如果存在即返回该对象

```java
String a = "1234";
String b = "12" + "34";
System.out.println(a == b);	// true
```

会直接在常量池中将 a 的引用赋给 b ，此时 a 和 b 都指向常量池中的同一个对象

`==` ：既可以用于基本数据类型，也可以用于引用数据类型；基本类型比较的是值，引用类型比较的是内存地址

使用 `indexOf` 实现检索，`indexOf` 方法用于实现字符串中检索另外一个字符串，`String` 提供几个重载的 `indexOf` 方法：

- `int  indexOf(String str)` ：在字符串中检索 str ，返回其第一次出现的位置，如果找不到则返回 -1
- `int  indexOf(String str,int fromIndex)` ：从字符串的 `fromIndex` 位置开始检索
- `int  lastIndexOf(String str)` ：str 在字符串中多次出现时，返回最后一次出现的位置
- `int  lastIndexOf(String str,int fromIndex)` ：从指定位置开始往前找，返回第一次出现的位置

```java
String str = "I can because i think can";
int index = str.indexOf("can");
System.out.println(index);	// 2
index = str.lastIndexOf("can");
System.out.println(index);	// 24
index = str.IndexOf("can",6);
System.out.println(index);	// 24
index = str.IndexOf("Can");
System.out.println(index);	// -1
```

- `String  substring(int beginIndex)` ：返回字符串中从下标 `beginIndex`（包括）开始到字符串结尾的子字符串

  ```java
  String str = "www.baidu.com";
  String str1 = str.substring(4,9);	// baidu
  String str2 = str.substring(10);	// com
  ```

- `void  trim()` ：去掉一个字符串的前导和后继空字符

- `char  charAt(int index)` ：返回字符串指定位置的字符，参数 index 表示指定的位置

- `void  startsWith(String str)` ：检测一个字符串是否以指定字符串开头

- `void  endsWith(String str)` ：检测一个字符串是否以指定字符串结尾

- `void  toLowerCase()` ：转大写

- `static  String  valueOf()` ：将其他类型转为字符串

**`toString` 和 `valueOf` 的区别**

`toString` 是属于对象的方法，`valueOf` 是属于类的方法；使用 `toString` 需要先构建要转换成字符串的对象，而使用 `valueOf` 则直接使用 `String` 点出即可

##### `StringBuilder` 及其 `API`

`StringBuilder` 封装可变字符串，对象创建后可以通过调用方法改变其封装的字符序列

1. 构造方法
   - `public  StringBuilder()`;
   - `public  StringBuilder(String str)`;
2. 常用方法
   - `StringBuilder  append(String str)` ：追加字符串
   - `StringBuilder  insert(int detOffset,Strting str)` ：插入字符串
   - `StringBuilder  delete(int start,int end)` ：删除字符串
   - `StringBuilder  replace(int start,int end,String str)` ：替换字符串
   - `StringBuilder  reverse()` ：反转

这些方法的返回语句均为 `return this` ，改变封装的字符串序列后又返回了该对象的引用，因此可以这样书写：

```java
StringBuilder str = new StringBuilder("abc");
str.append("d").insert(4,"e").replace(1,3,"mn");
System.out.println(str);	// amnd
System.out.println(str.hashCode() == str.append("i").hashCode());	// true , StringBuilder对象改变前后地址不变
```

`StringBuilder` 总结

`StringBuilder` 是可变字符串，字符串的内容计算，建议采用 `StringBuilder` 实现，这样性能会好一些

java 的字符串连接过程是利用 `StringBuilder` 实现的

```java
String str1 = "a";
String Str2 = str1 + "b";
// 等同于
String str2 = new StringBuilder(Str1).append("b").toString();
```

`StringBuilder` 和 `StringBuffer`

- `StringBuffer` 是线程安全的，同步处理，性能稍慢
- `StringBuilder` 是非线程安全的，并发处理，性能稍快

##### 正则表达式

实际开发中，经常需要对字符串数据进行一些复杂的匹配、查找、替换等操作，通过正则表达式可以方便的实现字符串的复杂操作；正则表达式是一串特定字符（`String` 数据类型），组成一个”规则字符串“，这个”规则字符串“是描述文本规则的工具，正则表达式就是记录文本规则的代码

**字符集合**

| 正则表达式     | 说明                                 |
| -------------- | ------------------------------------ |
| `[abc]`        | a,b,c中任意一个字符                  |
| `[^abc]`       | 除了 a,b,c 以外的任意一个字符        |
| `[a-z]`        | 表示 a-z 中的任意一个字符            |
| `[a-zA-Z0-9]`  | 表示 a-z,A-z,0-9 中任意一个字符      |
| `[a-z&&[^bc]]` | a-z 中除了 b 和 c 以外的任意一个字符 |

**预定义字符集**

| 正则表达式 | 说明                                           |
| ---------- | ---------------------------------------------- |
| .          | 任意一个字符（除了 \r\n），全部匹配可用 [\s\S] |
| \d         | 任意一个数字字符，相当于 [0-9]                 |
| \D         | 非数字字符                                     |
| \w         | 单词字符（包括下划线），相当于 [a-zA-Z0-9_]    |
| \W         | 非单词字符                                     |
| \s         | 空白字符，相当于 [\f\r\n\t\v]                  |
| \S         | 非空白字符                                     |

**数量词**

| 正则表达式 | 说明                  |
| ---------- | --------------------- |
| x ?        | 表示 0 个或 1 个 x    |
| x *        | 表示 0 个或任意多个 x |
| x +        | 表示 1 个或任意多个 x |
| x {n}      | 表示 n 个 x           |
| x {n,}     | 表示 n 个到任意多个 x |
| x {n,m}    | 表示 n 个到 m 个 x    |

边界匹配：“ ^ ” 与 “ $ ” 分别代表字符串的开始与结束

分组 " ( ) "：可以将一系列正则表达式看做一个”整体“，分组是可以使用 "|"表示"或"的关系

> **`\\`** :
>
> 在其他语言中，`\\`  表示：**我想要在正则表达式中插入一个普通的（字面上的）反斜杠，请不要给它任何特殊的意义**
>
> 在 `Java` 中，`\\` 表示：**我要插入一个正则表达式的反斜线，所以其后的字符具有特殊的意义**
>
> 所以，在其他的语言中，一个反斜杠 `\` 就足以具有转义的意义，而在 `Java` 中正则表达式中则需要两个反斜杠才能被解析为其他语言中的转义作用。也可以简单的理解在 Java 的正则表达式中，两个 `\\` 代表其他语言中的一个 `\` ，这也就是为什么表示一位数字的正则表达式是 `\\d` ，而表示一个普通的反斜杠是 `\\` 

> 根据 `Java Language Specification` 的要求，`Java` 源代码的字符串中的反斜线被解释为 `Unicode` 转义或其他字符转义。因此必须在字符串字面值中使用两个反斜线，表示正则表达式受到保护，不被 `Java` 字节码编译器解释。例如，当解释为正则表达式时，字符串字面值 `\b` 与单个退格字符匹配，而 `\\b` 与单词边界匹配。字符串字面值 `\(hello\)` 是非法的，将导致编译时错误；要与字符串 `(hello)` 匹配，必须使用字符串字面值 `\\(hello\\)` 。在 `Java` 正则表达式中要表达一个 `\` 要写成 `\\\\` 

`String` 正则 `API` 

- `boolean  matches(String regexp)` ：将一个字符串与正则表达式进行匹配，匹配成功返回 `true`

- `String[]  split(Strinig regexp)` ：将字符串按照特定正则表达式拆分成字符串数组

  ```java
  String str = "1+1=2";	// 将数字提取出来
  String[] arr = str.split("\\D+");
  System.out.println(Arrays.toString(arr));	// [1,1,2]
  ```

- `String  replaceAll(String regexp,String replacement)` ：将字符串中匹配正则表达式 `regxep` 的字符串替换成 `replacement`

##### `Object`

`Object` 类是一个特殊的类，是所有类的父类，它主要提供了以下 11 个方法：

```java
public  final  native  Class<?>  getClass() 	// 用于返回当前运行时对象的 Class 对象
public  native  int  hashCode()		// 返回对象的哈希码，主要使用在哈希表中，比如 JDK 中的 HashMap
public  boolean  equals()	// 用于比较 2 个对象的内存地址是否相等，String 类对该方法进行了重写以用于比较字符串的值是否相等
protected  native  Object  clone()  throws CloneNotSupportedException  // 创建并返回当前对象的一份拷贝
public  String  toString()  // 返回类的名字的实例的哈希码的 16 进制的字符串，建议 Object 所有的子类都重写这个方法
public  final  native  void  notify() 	// 唤醒一个在此对象监视器上等待的线程（监视器相当于就是锁的概念），如果有多个线程在等待只会任意唤醒一个
public  final  native  void  notifyAll()  // 唤醒在此对象监视器上等待的所有线程
public  final  native  void  wait(long timeout) throws InterruptedException   //  暂停线程的执行    注意：sleep 方法没有释放锁，而 wait 方法释放了锁，timeout 是等待时间
public  final  void  wait(long timeout,int nanos) throws InterruptedException  // nanos 参数表示额外的时间（以毫秒为单位，范围是 0 - 999999），所以超过的时间还需要加上 nanos 毫秒
public  final  void  wait() throws InterruptedException  // 该方法会一直等待，没有超过时间这个概念
protected  void  finalize() throws THrowable  // 实例被垃圾回收器回收的时候触发的操作
```

在 `Java` 类继承结构中，`java.lang.Object` 类位于顶端；如果定义一个 `Java` 类时没有使用 `extends` 关键字声明其父类，则其父类默认为 `java.lang.Object` ；`Object` 类型的引用可以指向任何类型的对象

**`toString()` 方法**

用于返回对象值得字符串表示：类的全称@散列码，建议重写

```java
public String toString(){
    return "Cell{" + "row=" + this.row + ",col=" + this.col + "}"; // 返回当前类的属性
}
```

`Java` 语言中很多地方会默认调用对象的 `toString` 方法，如：`字符串 + 对象`  、`System.out.println(对象) ；`，`toString`方法是非常有用的调试工具；`JDK` 中的标准库中，许多类都定义了 `toString` 方法，方便用户获得有关对象状态的必要信息，如 `String` 重写 `toString` 方法就是将自身返回：`public String toString(){ return this; }`

`equals` 方法

检测一个对象是否等于另一个对象，在 `Object` 类中，这个方法判断两个对象是否具有相同的引用来判断是否为相同的对象；在实际中，一般需要重写该方法，通过比较对象的成员属性，使该方法更具有意义（没有重写的 `equals` 方法等同于 `==` ）

```java
public boolean equals(Object obj){
    if(obj == null){
        return false;
    }
    if(obj == this){
        return true;
    }
    if(obj instanceof Cell){
        Cell cell = (Cell)obj;	// 如果传入的是 Cell 类的子类，就将其向上转型为 Cell 
        return cell.row == this.row && cell.col == this.col;
    } else {
        return false;
    }
}
```

###### `==` 和 `equals()` 的区别

`==` 对于基本类型和引用类型的作用效果是不同的：

- 对于基本数据类型来说：`==` 比较的是值

- 对于引用数据类型来说，`==` 比较的是对象的内存地址

  > 因为 `Java` 只有值传递，所以，对于 `==` 来说，不管是比较基本数据类型，还是引用数据类型的变量，其本质比较的都是值，只是引用类型变量存的值是对象的地址

`equals()` 不能用于判断基本数据类型的变量，只能用来判断两个对象是否相等；`equals()` 存在于 `Object` 类中，而 `Object` 类是所有类的直接或间接父类，因此所有的类都有 `equals()` 

Object 类中的 equals() ：

```java
public boolean equals(Object obj) {
     return (this == obj);
}
```

`equals()` 存在两种使用情况：

- 类没有重写 `equals()` ：通过 `equals()` 比较该类的两个对象时，等价于通过 ” `==` “ 比较这两个对象，使用的默认是 `Object` 类中的 `equals()`

- 类重写了 `equals()` ：一般重写 `equals()` 来比较两个对象中的属性是否相等，若它们的属性相等，则返回 `true`（即认为这两个对象相等）

  > `String` 中的 `equals()` 是被重写过的，比较的是对象的值

###### `hashCode()` 的作用

`hashCode()` 的作用是获取哈希码（`int` 整数），也称散列码；这个哈希码的作用是确定该对象在哈希表中的索引位置

`hashCode()` 定义在 `JDK` 的 `Object` 类中，这就意味着 `Java` 中任何类中都包含有 `hashCode()` 函数；另外需要注意的是：`Object` 的 `hashCode()` 是本地方法，也就是用 `C` 语言或 `C++` 实现的，该方法通常用来将对象的内存地址转换成整数后返回

```java
public  native  int  hashCode();
```

散列表存储的是键值对（`key-value`），它的特点是：能根据 “ 键 ” 快速的检索出对应的 “ 值 ” ，这就利用到了散列码（可以快速找到所需要的对象）

1. 为什么要有 `hashCode` ?

   我们以 “ `HashSet` 如何检查重复 ” 为例来说明为什么要有  `hashCode` ：

   当把对象加入 `HashSet` 时，`HashSet` 会先计算对象的 `hashCode` 值来判断对象加入的位置，同时也会与其他已经加入的对象的 `hashCode` 值做比较，如果没有相符的 `hashCode`，`HashSet` 会假设对象没有重复出现；但如果发现有相同 `hashCode` 值的对象，这时会调用 `equals()` 来检查 `hashCode` 相等的对象是否真的相同；如果两者相同，`HashSet` 就不会让其加入操作成功；如果不同的话，就会重新散列到其他位置；这样就大大减少了 `equals` 的次数，相应就大大提高了执行速度

2. `hashCode()` 和 `equals()` 都是用于比较两个对象是否相等，那为什么 `JDK` 还要提供这两个方法呢？

   这是因为在一些容器中（比如 `HashMap`、`HashSet`）中，有了 `hashCode()` 之后，判断元素是否在对应的容器中的效率会更高（参考添加元素进 HashSet 的过程）；如果 `HashSet` 在对比的时候，同样的 `hashCode` 有多个对象，它会继续使用 `equals()` 来判断是否真的相同，也就是说：`hashCode` 帮助我们大大缩小了查找成本

3. 那为什么不只提供 `hashCode()` ？

   因为两个对象的 `hashCode` 值相等并不代表两个对象就相等

4. 为什么两个对象有相同的 `hashCode` 值，它们也不一定相等 ？

   因为 `hashCode()` 所使用的哈希算法也许刚好会让多个对象传回相同的哈希值；越糟糕的哈希算法越容易碰撞，但这也与数据值域分布的特性有关（所谓哈希碰撞也就是指的是不同的对象得到相同的 `hashCode`）

   总结：

   - 如果两个对象的 `hashCode` 值相等，那这两个对象不一定相等（哈希碰撞）
   - 如果两个对象的 `hashCode` 值相等并且 `equals()` 也返回 `true`，我们才认为这两个对象相等
   - 如果两个对象的 `hashCode` 值不相等，我们就可以直接认为这两个对象不相等

5. 为什么重写 `equals()` 时必须重写 `hashCode()` 方法？

   因为两个相等的对象的 `hashCode` 值必须相等，也就是说如果 `equals()` 判断两个对象是相等的，那这两个对象的 `hashCode` 值也要相等

   如果重写 `equals()` 时没有重写 `hashCode()` 的话就可能会导致 `equals()` 判断是相等的两个对象，`hashCode` 值却不相等

   总结：

   - `equals()` 判断两个对象是相等的，那这两个对象的 `hashCode` 值也要相等
   - 两个对象有相同的 `hashCode` 值，它们也不一定相等（哈希碰撞）



##### 包装类

在基本类型转换的范畴内，有一种特殊的转换，需要将 `int` 这样的基本数据类型转换为对象；所有基本类型都有一个与之对应的类，即包装类；包装类是不可变类，在构造了包装类对象后，不允许更改包装在其中的值（因为值被 `final` 修饰）；包装类被 `final` 修饰，不能定义它们的子类

| 基本类型  | 包装类                | 父类               |
| --------- | --------------------- | ------------------ |
| `int`     | `java.lang.Integer`   | `java.lang.Number` |
| `long`    | `java.lang.Long`      | `java.lang.Number` |
| `double`  | `java.lang.Double`    | `java.lang.Number` |
| `byte`    | `java.lang.Byte`      | `java.lang.Number` |
| `short`   | `java.lang.Short`     | `java.lang.Number` |
| `float`   | `java.lang.Float`     | `java.lang.Number` |
| `char`    | `java.lang.Character` | `java.lang.Object` |
| `boolean` | `java.lang.Boolean`   | `java.lang.Object` |

`Number` 及其主要方法

抽象类 `Number` 是 `Byte` 、`Short` 、`Integer` 、`Long` 、`Float` 、`Double` 的父类；抽象类 `Number` 中定义了 `intVaule` 、`doubleVaule` ... 等六个方法，包装类中都有重写这些方法，因此每个包装类中都有这六个方法

- `double  doubleValue()` ：以 `double` 形式返回指定的数值

- `int  intValue()` ：以 `int` 形式返回指定的数组
- `...`

一个基本类型可以转换成任意包装类型，但可能会造成精度损失和溢出

```java
double c = 1.2;
Double s = Double.valueOf(c);
Integer v = Integer.valueOf((int)c); // 需要强转

Number m = 1.1;
Number n = 3;
int intvalue = m.intVaule();
double doublevalue = n.doubleValue();
System.out.println(m.getClass().getName()); // java.lang.Double  输出 n 对象所属的类型
```

**自动装箱和自动拆箱**

从 `Java5.0` 版本以后加入了 `autoboxing` 功能，自定 "拆箱" 和 "装箱" 是依靠 `JDK5` 的编译器在编译时的 "预处理" 工作；装箱和拆箱是 “ 编译器 ” 认可的，而不是虚拟机，编译器在生成类的字节码时插入必要的方法调用

```java
Integer a = 1;	// 自动装箱
Integer b = 1;	// 自动装箱
Integer c = a + b;	// 先拆箱再装箱
double d = c;	// 自动拆箱
```

在进行自动装箱和拆箱的过程中，Integer 的变量自动调用 intValue，Double 类型自动调用 doubleValue，以此类推

```java
Integer a = 100;     -->    Integer a = Integer.valueOf(a);
int b = a;           -->    int b = a.intValue();
```

基本类型到包装类的转换：遵守基本类型之间的转换关系

```java
Integer.valueOf(2.1);  		  // ✘
Integer.valueOf((int)2.1);    // ✔
Double.valueOf(2);     		  // ✔
```

包装类到基本类型的转换：包装类可以转换成 6 种基本类型，但需要显示的调用，否则也需要遵守基本类型之间的转换关系

```java
Integer a = 1;
int b = a;
double c = a;        -->    double c = a.intValue();

Double m = 1.0;
double k = m;
int n = m;       	// ✘
int n = (int)m;  	// ✘
int n = (int)m.doubleValueOf();  // ✔
int n = m.intValue();	// ✔
```

`Integer` 常用功能

该类提供了多个方法，能在 int 类型和 String 类型之间互相转换，还提供一些常量

- `static  int  MAX_VALUE` ：2 的 31 次方 - 1，表示 `int` 类型的最大值
- `static  int  MIN_VALUE` ：- 2 的 31 次方，表示 `int` 类型的最小值
- `static  int  parseInt(String str)` ：将字符串转换为 `int` 类型，如果 `str` 不是一个数值类型字符串，将会发生 `NumberFormatException` ：数据格式转换异常

```java
String str = "123";
int i = Integer.parseInt(str);
Integer.parseInt("1.2"); 	// ✘ 
```

`Double` 常用功能

1. 构造方法
   - `Double(double value)`
   - `Double(String str)`
2. 常用方法
   - `double  doubleValue()` ：返回此 `Double` 对象的 `double` 值
   - `static  double  parseDouble(String str)` ：返回一个double 值

```java
Double num = new Double(1.2);
Double num = new Double("1.2");
Double num = Double.valueOf(1.2);	// 三种方式是一样的

String str = "1.2";
double value = Double.parseDouble(str);
```

### 日期操作

##### `Date` 及其常用 `API`

`Java` 中的时间使用标准类库的 `Date` 类表示，使用距离一个固定时间点毫秒数（可正可负，long 类型）表达一个特定的时间点；固定的时间点称为纪元（`epoch`），是 UTC 时间 1970 年 1 月 1 日 00:00:00 ；`UTC`（`Universal Time Coordinate` 世界调整时间）与 `GMT` （`Greenwich Mean Time` 格林威治时间）一样，是一种具有实际目的的科学标准时间

`Date` 类：`java.util.Date` 类封装日期及时间信息；`Date` 类大多数用于时间分量计算的方法已经被 `Calendar` 取代

- `long  getTime()` ：获取距离纪元至今所经历的毫秒数
- `void  setTime(Long time)` ：将传入的距离纪元至今所经历的毫秒数转换为对应的时间赋给 `date` 对象

```java
Date date = new Date();
System.out.println(date);   // 获取系统当前的日期及其时间信息
long time = date.getTime();  // 获取距离纪元至今所经历的毫秒数
```

`SimpleDateFormat` ：`java.text.SimpleDateFormat` 是一个以与语言环境有关的方式来格式化和解析日期的具体类，它允许进行格式（日期 --> 文本）、解析（文本 --> 日期）和规范化

1. 构造方法

   - `SimpleDateFormat()`
   - `SimpleDateFormat(String pattern)` ：用给定的模式和默认语言环境的日期格式符号构造 `SimpleDateFormat`

2. 常用方法

   - `final  String  format(Date date)` ：`Date --> String` 

     ```java
     Date date = new Date();
     SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
     String str = sdf.format(date); // 2022-04-24 22:24:22
     ```

   - `Date  parse(String source)  throws  ParseException` ： `String  --> Date` 

     ```java
     SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
     String str = "2022-2-23";
     try {
         Date date = sdf.parse(str);
     } catch (ParseException e) {
         System.out.println("格式错误");
     }
     ```

日期模式匹配字符

| 字符 | 含义             | 示例                                      |
| ---- | ---------------- | ----------------------------------------- |
| `y`  | 年               | `yyyy`                                    |
| `M`  | 月               | `MM`                                      |
| `d`  | 日               | `dd`                                      |
| `H`  | 小时（24小时制） |                                           |
| `h`  | 小时（12小时制） | `hh(a)`    a 为标识，自动判断为上午或下午 |
| `m`  | 分钟             |                                           |
| `s`  | 秒               | `HH:mm:ss`                                |

**`Calendar`**

`java.util.Calendar` 类用于封装日历信息，其主要作用在于其方法可以对时间分量进行运算；`Calendar` 是抽象类，其具体子类针对不同国家的日历系统，其中应用最广泛的是 `GregorianCalendar`（格里高利历，阳历），对应世界上绝大数国家、地区使用的标准日历系统

- `static  Calendar  getInstance()` ：返回一个 `Calendar` 对象，其日历字段由当前日期和时间初始化

- `void  set()` ：设置月份时，设置的实际上是下一个月；如：想设置日期为 3 月 2 日，月份应当设置为 2（`set(Calendar.MONTH,2)`），此时用 `get` 方法得到的月份也将为 2 ，但用 `getTime` 方法将得到正确的月份 3

  ```java
  Calendar c = Calendar.getInstance();
  System.out.println(c.getClass().getName());	// java.util.GregorianCalendar
  System.out.println(c.getTime());  // 返回 Date 对象，相当于 new Date()
  
  GregorianCalendar g = new GregorianCalendar(2000,2,2);	 // GregorianCalendar 的初始化
  ```

设置日期和时间分量

```java
Calendar c = Calendar.getInstance();
c.set(Calendar.YEAR,2000);
c.set(Calendar.MONTH,4); // 实际上是 5 月，月份从 0 开始

SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
System.out.ptintln(sdf.format(c.getTime()));  // 没有被设置的默认为系统当前时间
```

- `void  add(int intValues,int mount)` ：为给定的时间分量的值加上给定的值，若给定的值为负数则是减去给定的值

  ```java
  Calendar c = Calendar.getInstance();
  c.add(Calendar.YEAR，1);  	// 当前年份加一
  ```

获取日期和时间分量

- `final  static  int  YEAR` ：指示年份的字段数字（值为 1 ）
- `final  static  int  MONTH` ：指示月份的字段数字（2）
- `final  static  int  DATE` ：指示一个月中的第几天  （3）
- `final  static  int  DAY_OF_WEEK` ：指示一个星期的第几天，星期日为 1 （7）
- `int  get(int intValues)` ：传入 `Calendar` 类里的常量，如：`Calendar.YEAR`（和直接传入 1 是一样的）；用此方法传入月份将返回实际月份小 1 的数

- `int  getActualMaximum(int intValues)` ：传入 `Calendar` 类的时间值（如：`Calendar.DATE`），返回给定日历字段的最大值

  ```java
  Calendar c = Calendar.getInstance();
  c.set(Calendar.YEAR,2014);
  for(int month = Calendar.JANUARY; month <= Calendar.DECEMBER; month++){
      c.set(Calendar.MONTH,month);           
      System.out.print(c.getActualMaximum(Calendar.DATE) + " ");  // 输出2014年每个月最大值
  }
  ```

`setTime` 与 `getTime`

- `Date  getTime()` ：使用 `Date` 描述 `Calendar` 表示的日期并返回

- `void  setTime(Date date)` ：使 `Calendar` 表示 `Date` 所描述的日期

  ```java
  Calendar c = Calendar.getInstance();
  Date date = new Date(1600800456332L);
  c.setTime(date);
  ```

### 集合（`Collection`）

##### `List` 和 `Set`

在实际开发中，需要将使用的对象存储于特定数据结构的容器中，`JDK` 提供了这样的容器 ---- 集合（`Collection`）；`Collection` 是一个接口，定义了集合相关的操作方法，其有两个子接口 `List`、`Set`

- `List` ：可重复集，`Set` ：不可重复集
- 不可以放基本数据类型
- 元素是否重复，取决于元素的 `equals()` 比较的结果

集合持有对象的引用：集合中存储的都是引用类型元素，并且集合只保存每个元素对象的引用，而并非将元素对象本身存入集合

<img src=".\note_imgs\Collection内存.png" style="zoom: 50%;" />

```java
Collection<Cell> cells = new ArrayList<Cell>();  //  Cell 为自定义类
cells.add(new Cell(1,3));
Cell cell = new Cell(33,2);
cells.add(cell);
System.out.println(cells);

cell.drop();
System.out.println(cells);  // 输出数据发生改变（多个引用指向同一个对象）
```

- `boolean  add(E e)` ：该方法将给定的元素添加进集合，若添加成功则返回 `true` ，否则返回 `false` 

- `boolean  contains(Object object)` ：该方法用于判断给定的元素是否包含在集合中，若包含则返回 `true`，否则返回 `false`；集合在判断元素是否包含在集合中是根据每个元素的 `equals()` 方法进行比较后的结果，通常有必要重写 `equals()` 保证 `contains()` 方法的合理结果

  ```java
  Collection<String> c = new ArrayList<>();
  c.add("a");
  String str = "a";
  System.out.println(c.contains(str));  // true
  ```

- `int  size()` ：返回当前集合中的元素总数

- `void  clear()` ：清空当前集合

- `boolean  isEmpty()` ：判断当前集合是否不包含任何元素

  ```java
  Collection<String> c = new HashSet<>();
  c.add("a");
  c.add("b");
  c.add("a");
  System.out.println(c.isEmpty);  // false
  System.out.println(c);  // [a,b]
  System.out.println(c.size());  // 2
  c.clear();
  System.out.println(c);  // []
  ```

- `boolean  addAll(Collection<?extends E> c)` ：该方法需要传入一个集合，并将该集合中的所有元素添加到当前集合中

- `boolean  containsAll(Collection<?> c)` ：判断当前集合是否包含给定集合中的所有元素，若包含则返回 `true` 

  ```java
  Collection<String> c1 = new ArrayList<>();
  c1.add("a");
  c1.add("b");
  Collection<String> c2 = new ArrayList<>();
  c2.addAll(c1);
  System.out.println(c2);   // [a,b]
  Collection<String> c3 = new ArrayList<>();
  c3.add("a");
  System.out.println(c1.containsAll(c3));    // true
  ```

##### **`Iterator`**

迭代器用于遍历集合元素，获取迭代器可以使用 `Collection` 定义的方法（`Iterator iterator()`）；迭代器 `Iterator` 是一个接口，集合在重写 `Collection` 的 `iterator()` 方法时利用内部类提供迭代器的实现；`Iterator` 提供了统一的遍历集合的方式，其提供了用于遍历集合的两个方法：

- `boolean  hasNext()` ：判断集合是否还有元素可以遍历

- `E  next()` ：返回迭代的下一个元素

  ```java
  Collection<String> c1 = new HashSet<>();
  c1.add("a");
  c1.add("b");
  
  Iterator<String> it = c1.iterator();
  while(it.hasNext()){
      String str = it.next();
      System.out.println(str);
  }            
  ```

- `void  remove()` ：在使用迭代器遍历集合时，不能通过集合的 `remove()` 删除元素，否则会抛出并发更改异常，我们可以通过迭代器自身提供的 `remove()` 来删除通过 `next()` 迭代出的元素；迭代器删除方法是在源集合中删除元素；需要注意的是：在调用 `remove()` 前必须通过迭代器的 `next()` 迭代过元素，那么删除的就是这个元素，并且需要再次使用 `next()` 后才可再次调用 `remove()`

  ```java
  Collection<String> c1 = new HashSet<>();
  c1.add("a");
  c1.add("b");
          
  Iterator<String> it = c1.iterator();
  while(it.hasNext()){
       String str = it.next();
       if(str.startsWith("a")){
            it.remove();
        }
  }       
  ```

**增强型 for 循环**

Java 5.0 之后推出了一个新的特性，增强 for 循环，也称为新循环，该循环不通用于传统循环的工作，其只用于遍历集合或数组；新循环并非新的语法，而是在编译过程中，编译器会将新循环转为迭代器模式，所以新循环本质上是迭代器

```java
for (元素类型 e : 集合或数组) {
    循环体
}

Collection<String> c1 = new HashSet<>();
c1.add("a");

for (String str : c1) {
    System.out.println(str);
}
```

**泛型机制**

泛型是 `JavaSE 5.0` 引入的特性，泛型的本质是参数化类型，在类、接口和方法 的定义过程中，所操作的数据类型被传入的参数指定；`Java` 泛型机制广泛的应用在集合框架中，所有的集合类型都带有泛型参数，这样在创建集合时可以指定放入集合中元素的类型，`Java` 编译器可以据此进行类型检查，这样可以减少代码在运行时出现错误的可能性

`ArrayList<E>` 类的定义中，`<E>` 中的 `E` 为泛型参数，在创建对象时可以将类型作为参数传递，此时类中定义的所有 `E` 将被替换为传入的参数

##### 集合操作（线性表）

`ArrayList` 和 `LinkedList` ：`List` 接口是 `Collection` 的子接口，用于定义线性表数据结构，可以将 `List` 理解为存放对象的数组，只不过其元素个数可以动态的增加或减少；`List` 接口的两个常见实现类 `ArrayList` 和 `LinkedList` ，分别用动态数组和链表的方式实现了 `List` 接口；可以认为 `ArrayList` 和 `LinkedList` 的方法在逻辑上完全一样，只是在性能上有一定差别，`ArrayList` 更适合用于随机访问而 `LinkedList` 更适合用于插入和删除，在性能要求不是特别高的情况下可以忽略这个差别；小结：

- `List`：定义线性表的数据结构，`ArrayList` 和 `LinkedList` 都是线性表结构（线性：有顺序，一个接一个）
- `ArrayList` 和 `LinkedList`  方法相同，对于调用者而言没有区别，但是底层逻辑不同，导致性能不同

`ArrayList` 和 `LinkedList` 的底层逻辑（底层结构）

- `ArrayList` ：动态数组结构

- `LinkedList` ：链表结构

  上述都是线性结构，都可以理解为：存放对象的数组，只不过可以动态改变

**常用方法：**

`List` 除了继承 `Collection` 定义的方法外，还根据其线性表的数据结构定义了一系列的方法，其中最常用的就是基于下标的 `get` 和 `set` 方法：

- `E  get(int index)` ：获取集合中指定下标对应的元素，下标从 `0` 开始

- `E  set(int index,E element)` ：将给定的元素存入给定位置，并将原位置的元素返回

  ```java
  List<String> c1 = new ArrayList<>();
  c1.add("a");
  c1.add("b");
  c1.set(0,c1.set(1,c1.get(0)));	// 交换 a，b 在集合中的位置
  ```

- `void  add(int index,E element)` ：将给定的元素插入到指定的位置，原位置及后续元素都顺序向后移动
- `E  remove(int index)` ：删除给定位置的元素，并将被删除的元素返回

- `List<E>  subList(int fromIndex,int toIndex)` ：用于获取子 `List` ，截取子 `List` 的首尾下标（前包括，后不包括）；需要注意的是，`subList` 获取的 `List` 与原 `List` 占有相同的存储空间，对子 `List` 的操作会影响原 `List` 

  ```java
  List<Integer> c1 = new ArrayList<>();
  for (int i = 0; i < 4; i++){
      c1.add(i);
  }					// c1 : [0,1,2,3]
  List<Integer> subList = c1.subList(1,3);  // sublist : [1,2]
  subList.clear();    // c1 : [0,3]
  ```


##### `List` 与数组之间的转换

1. **`List` 转换为数组**：`List` 的 `toArray()` 用于将集合转换成数组，该方法是在 `Collection` 中定义的，因此所有的集合都具备这个功能

   - `Object[]  toArray()` ：将集合中的所有元素放入 `Object` 数组中返回，需要用 `Object` 数组接收

   - `<T>T[]  toArray(T[] a)` ：这个方法较常用，可以传入一个指定的类型的数组，该数组的元素类型应与集合的元素类型一致，返回值是转换后的数组，该数组会保存集合中的所有元素；传入的数组可以指定长度，如果指定的长度小于集合元素的长度，则转换后的数组长度为集合元素的长度，不会为指定长度；如果指定的长度大于集合的元素个数，则转换后的数组长度与指定的长度相同，并且使用 `null` 填充数组

     ```java
     List<String> list = new ArrayList<>();
     list.add("a");
     list.add("b");
     list.add("c");
     String[] strarr1 = list.toArray(new String[]{});	// [a,b,c]
     String[] strarr2 = list.toArray(new String[1]);		// [a,b,c]
     String[] strarr3 = list.toArray(new String[4]);		// [a,b,c,null]
     ```

2. 数组转换为 `List` ：`Arrays` 类中提供有个静态方法 `asList()` ，使用该方法可以将一个数组转换成对应的 `List` 集合

   - `static  <T>List<T>  asList<T...a>` ：返回的 `List` 的集合类型由传入的数组的元素类型决定；要注意的是：返回的集合不能对其增删元素，否则会抛出异常，并且对集合的元素进行修改会影响数组对应的元素

     ```java
     String[] strarr = {"a","c"};
     List<String> list = Arrays.asList(strarr);	  // [a,c]
     
     System.out.println(list.getClass().getName());	  // java.util.Arrays$ArrayList
     list.add("m");    // UnsupportedOperationException
     ```

##### `List` 排序

1. `Collections.sort()` 实现排序：`Collections` 是集合的工具类，它提供了很多方便我们操作集合的方法，其中就有用于集合排序的 `sort()` 

   - `static  void  sort(List<T> list)` ：对给定的集合元素进行自然排序

     ```java
     List<Integer> list = new ArrayList<>();
     Random r = new Random();
     for (int i = 0; i < 4; i++){
         list.add(r.nextInt(10));	// 随机生成 4 个 0-9 的整数放入集合 list 中
     }
     Collections.sort(list);		// 对集合 list 从小到大进行排序
     ```

2. `Comparable` ：`Collections` 的 `sort()` 是对集合元素进行自然排序，那么两个元素对象之间就一定要有大小之分，这个大小之分如何界定？实际上，在使用 `Collections` 的 `sort()` 排序的集合元素必须是 `Comparable` 接口的实现类，该接口表示其子类是可比较的，因为实现该接口必须重写抽象方法：

   - `int  compareTo(T t)` ：若当前对象小于给定对象，那么返回值应为小于 `0` 的整数；若当前对象大于给定对象，那么返回值应为大于 `0` 的整数；若当前对象等于给定对象，那么返回值应为等于 `0` 

     ```java
      // 使自定义类 Cell 实现接口 Comparable ，通过 Cell 中的 row 成员变量的大小进行排序
     public class Cell implements Comparable<Cell>{
         @Override
         public int compareTo(Cell cell) {
             return this.row - cell.row;
         }
     }
     List<Cell> list = new ArrayList<>();
     list.add(new Cell(1,1));
     list.add(new Cell(2,2));
     Collections.sort(list);
     ```

3. `Comparator` ：一旦 Java 类实现了 `Comparable` 接口，其比较逻辑就已经确定，如果希望在排序的操作中临时指定比较规则，可以采用 `Comparator` 接口回调的方式

   - `int  compare(T o1,T o2)` ：若 o1 > o2 则返回值应大于 0；若 o1 < o2 则返回值应大于 0；若 o1 = o2 则返回值应等于 0

     ```java
     List<Cell> list = new ArrayList<>();
     list.add(new Cell(1,1));
     list.add(new Cell(2,2));
     
     // Collections.sort(list,new Comparator<Cell>(){});
     list.sort(new Comparator<Cell>() {
         @Override
         public int compare(Cell o1, Cell o2) {
             return o2.col - o1.row;
         }
     });
     ```

##### 队列和栈

队列（`Queue`）是常用的数据结构，可以将队列看成特殊的线性表，队列限制了对线性表的访问方式：只能从线性表的一端添加（`offer`）元素，从另一端取出（`poll`）元素；队列遵循先进先出（`FIFO：First input First Output`）的原则；`JKD` 中提供了 `Queue` 接口，同时使得 `LinkedList` 实现了该接口（选择 `LinkedList` 实现 `Queue` 的原因在于 Queue 经常需要进行添加和删除操作，而 `LinkedList` 在这方面效率将高）

- `boolean  offer(E e)` ：将一个对象添加至队尾，如果添加成功则返回 true

- `E  poll()` ：从队首删除并返回一个元素

- `E  peek()` ：返回队首元素（并不删除）

  ```java
  Queue<String> queue = new LinkedList<>();
  queue.offer("a");
  queue.offer("b");	// offer: [a,b]
  
  while (queue.size() > 0){
      System.out.print(queue.poll() + " ");
  }
  
  System.out.println(queue);   // []
  ```

`Deque` 是 `Queue` 的子接口，定义了所谓的 ” 双端队列 “ 即从队列的两端分别可以入队（`offer`）和出队（`poll`），`LinkedList` 实现了该接口

如果将 `Deque` 限制为只能从一端入队和出队，则可实现 “ 栈 ”（`Stack`）的数据结构；对于栈而言，入栈称之为 `push`，出栈称之为 `pop`；栈遵循先进后出（`FIFO：First Input Last Output`）的原则

`Java` 集合没有单独的 `Stack` 接口，因为有个遗留类名字就叫 `Stack`，出于兼容性考虑，没有创建 `Stack` 接口，但能用 `Deque` 接口 “ 模拟 ” 一个 `Stack`；当我们把 `Deque` 作为栈使用时，注意只调用 `push()` 、`pop()` 、`peek()` ，不要调用 `addFirst()` 、`removeFirst()` 、`peekFirst()` ......

```java
Deque<String> stack = new LinkedList<>();
stack.push("a");
stack.push("b");
stack.push("c");	// stack: [c,b,a]

while(stack.size() > 0){
    System.out.print(stack.pop() + " ");    //  c b a
}
System.out.println();
System.out.println(stack);    // []
```

##### `Map` 接口

`Map` 接口定义的集合又称为查询表，用于存储所谓 “ `Key-Value` ” 映射对，`Key` 可以看成是 `Value` 的索引，作为 `Key` 的对象在集合中不可以重复；根据内部数据结构的不同，`Map` 接口有多种实现类，其中常用的有内部为 `hash` 表实现的 `HashMap` 和内部为排序二叉树实现的 `TreeMap`

- `V  put(K key,V value)` ：将 `Key-Value` 对存入 `Map`，如果在集合中已经包含该 `Key`，则将操作变为替换该 `Key` 所对应的 `Value`，返回值为 `Key` 原来所对应的 `Value` (如果集合中没有包含该 `Key`则返回 `null` )

- `V  get(Object key)` ：返回参数 `key` 所对应的 `Value` 对象，如果不存在则返回 `null`

- `boolean  containsKey(Object key)` ：若 `Map` 中包含给定的 `key` 则返回 `true`，否则返回 `false`

  ```java
  Map<Integer,Integer> map = new HashMap<>();
  map.put(1,10);
  map.put(2,20);
  System.out.println(map.get(2));    // 20
  System.out.println(map.containsKey(2));	  // true
  ```

`hashcode` 方法 ：返回该对象所在内存地址的整数形式

- 重写 `hashCode()` 需要注意两点：
  1. 与 `equals()` 的一致性，即 `equals` 比较返回 `true` 的两个对象其 `hashCode()` 的返回值应该相同
  2. `hashCode()` 返回的数值应该符合 `hash` 算法的要求；如果有很多对象的 `hashCode` 方法返回值都相同，则会大大降低 `hash` 表的效率，一般情况下可以使用 `IDE`（如 `Eclipse`、`idea`）提供的工具自动生成 `hashCode()`
- 查看重写的 `hashCode()` :
  1. 是否根据对象的属性值进行重写
  2. `hashCode()` 中用到的属性是否也是 `equals()` 比较时用到的属性

Map 中的一些静态属性：

- `capacity`：容量，`hash` 表里的 `bucket`（桶）的数量，也就是散列数组大小
- `initial  capacity`：初始容量，创建 hash 表时，初始 `bucket` 的数量，默认构建容量是 16
- `size`：大小，当前散列表中存储数据的数量
- `loadfactor`：加载因子，默认值是 0.75（也就是 75%），当向散列表增加数据时，如果 `size / capacity` 的值大于 `loadfactor` 则发生扩容并且重新散列（`rehash`）

性能优化：加载因子较小时，散列查询性能会提高，同时也浪费散列桶空间容量，0.75 是性能和空间相对平衡的结果，在创建散列表时指定合理容量，减少 `rehash` 提高性能

##### `Map` 的遍历

- `Set<K>  keySet()` ：遍历所有的 `key` ，该方法会将当前 `Map` 中的所有 `key` 存入一个 `Set` 集合后返回

  ```java
  Set<Character> set = map.keySet();
  for (char c : set) {
      System.out.println(c + ":" + map.get(c));
  }
  ```

- `Set<Entry<K,V>>  entrySet()` ：遍历所有的键值对的方法，该方法会将当前 `Map` 中的每一组 `key-value` 对封装为一个 Entry 对象并存入一个 `Set` 集合后返回

  ```java
  Set<Map.Entry<Character,Integer>> entry = map.entrySet();
  for (Map.Entry<Character,Integer> e : entry){
      System.out.println(e);
      System.out.println(e.getKey() + "");
  }
  ```

有序的 `Map`：`LinkedHashMap` 实现有序 `Map`；使用 `Map` 接口的哈希表和链表实现，具有可预知的迭代顺序，此实现与 `HashMap` 的不同之处在于：`LinkedHashMap` 堆维护这一个双向循环列表，此链表定义了迭代顺序，该迭代顺序

通常就是存放元素的顺序；需要注意的是：如果在 `Map` 中重新存入已有的 `key`，那么 `key` 的位置不会变化，只是 `value` 替换

### 文件操作

`java.io.File` 用于表示文件（目录），也就是说程序员可以通过 `File` 类在程序中操作硬盘中的文件和目录；`File` 类只用于表示文件（目录）的信息（名称、大小等），不能对文件的信息进行访问

构造方法：

- `public  File(String name)` ：通过将给定路径名字符串转换成抽象路径名来创建一个新 `File` 实例；抽象路径应尽量使用相对路径，并且目录的层级分离不要直接写 " `/` " 或 " `\` "，应使用 `File.separator` 这个常量表示，以避免不同系统带来的差异

- `public  File(File parent,String child)` ：根据 `parent` 抽象路径名和 `child` 路径名字符串创建一个新的 `File` 实例

  ```java
  File parent = new File("demo");
  File file = new File(parent,"hello.txt");
  ```

##### 常用方法

- `boolean  isFile()` ：判断当前 `File` 对象所表示的是否为一个文件

- `long  length()` ：返回当前对象所表示的文件所占用的字节量

- `boolean  exists()` ：若该 File 表示的文件或目录存在则返回 `true`，否则返回 `false`

- `boolean  createNewFile()` ：如果指定的文件不存在并且成功创建，则返回 `true`；如果指定的文件已经存在，则返回 `false`

- `boolean  delete()` ：当成功删除文件或目录时，返回 `true`，否则返回 `false`；需要注意的是：若此 `File` 对象所表示的是一个目录时，在删除时需要保证此为空目录才可以成功删除（目录中不能含有任何子项）

- `boolean  isDirectory()` ：当 `File` 对象表示的是一个目录则返回 `true`；否则返回 `false`

- `boolean  mkdir()` ：当成功创建目录时，返回 `true`；否则返回 `false`

- `boolean  mkdirs()` ：用于创建此抽象路径名指定的目录，包括所有必须但不存在的父目录；注意：此操作失败时也可能创建一部分必须的父目录

  ```java
  File file = new File("dir" + File.separator + "dir2");
  System.out.println(file.mkdirs());
  ```

- `File[]  listFiles()` ：返回值为抽象路径名数组，这些路径名表示此抽象路径名表示的目录中的文件和目录；如果目录为空，那么数组也将为空；如果抽象路径名不表示一个目录或者发生 `I/O` 错误，则返回 `null`

  ```java
  File file = new File("dir");
  File[] fs = file.listFiles();
  System.out.println(Arrays.toString(fs));
  ```

##### `FileFilter` 接口

用于抽象路径名的过滤器；此接口的实例可传递给 `File` 类的 `listFiles()`；用于返回满足该过滤器要求的子项

- `File[]  listFiles(FileFilter filter)` ：

  ```java
  File[] fs = file.listFiles(new FileFilter() {
      @Override
      public boolean accept(File pathname) {
          return pathname.getName().endsWith(".txt");
      }
  });
  ```
