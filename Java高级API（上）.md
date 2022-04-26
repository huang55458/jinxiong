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

`StringBuilder` 是可变字符串，字符串的内容计算，建议采用 StringBuilder 实现，这样性能会好一些

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
public  native  int  hashcode()		// 返回对象的哈希码，主要使用在哈希表中，比如 JDK 中的 HashMap
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

`Date` 类：`java.util.Date` 类封装日期及时间信息；Date 类大多数用于时间分量计算的方法已经被 `Calendar` 取代

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
  c.set(Calendar.YEAR，1);  	// 当前年份加一
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

在实际开发中，需要将使用的对象存储与特定数据结构的容器中，`JDK` 提供了这样的容器 ---- 集合（`Collection`）；`Collection` 是一个接口，定义了集合相关的操作方法，其有两个子接口 `List`、`Set`

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

- `boolean  contains(Object object)` ：该方法用于判断给定的元素是否包含在集合中，若包含则返回 `true`，否则返回 `false`；集合在判断元素是否包含在集合中是根据每个元素的 equals() 方法进行比较后的结果，通常有必要重写 `equals()` 保证 `contains()` 方法的合理结果

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

  
