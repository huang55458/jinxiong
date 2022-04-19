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
  String str2 = str.substring(10);	// co
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

