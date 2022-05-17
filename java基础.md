### Java基础

##### JDK 、JRE 、JVM

- JVM：java虚拟机，保证java语言跨平台

- xxxxxxxxxx7 1FileInputStream fis = new FileInputStream("Hello.txt");2FileOutputStream fos = new FileOutputStream(new File("hello_copy.txt"));3byte[] bytes = new byte[1024];4int num;5while ((num = fis.read(bytes)) > 0){6    fos.write(bytes,0,num);7}java

- JDK：java开发环境。包括JRE和开发工具（javac，java）

##### 常见命名方式

-   包：其实就是文件夹，用于解决相同类名的问题
  - 全部小写         单极：com      多级：cn.itcast
  
-   类或者接口
    - 一个单词：首字母大写      Student、Person、Teacher
    - 多个单词：每个首字母大写      HelloWorld、MyName、NameDemo
    
-   方法或者变量
    - 一个单词：全部小写     name、age、show()
    - 多个单词：从第二个单词开始，每个单词首字母大写       myName、showAllStudentNames()
    
-   常量
    - 一个单词：全部大写     AGE
    
    - 多个单词：每个单词都大写，用 _  连接     STUDENT_MAX_AGE
    

**小结**

1. 自己起变量名时，一般都是纯字母
2. 常量多个单词时，用 `_`
3. 内部类完整名称，用 `$` 和数字（匿名内部类）


##### Java语言关键字  

<img src=".\note_imgs\关键字.png" alt="关键字" style="zoom: 67%;" />

##### 基本数据类型

<img src=".\note_imgs\基本数据类型.png" alt="基本数据类型" style="zoom: 67%;" />

**注意事项：**

1. 整数默认是int类型，浮点数默认是double类型（运算时要注意隐式转换）

2. ​        定义long类型数据时，加  L 

   ​		定义float类型数据时，加  F 

##### 类型转换

- ​	btye，short，char  - - >  int  - - >  long  - - >  float  - - >  double

- 1. 小数强转整数会直接舍弃小数
  2. double强转float会四舍五入
- byte 、short 、char 三种类型参与运算时，先一律转换为int类型再进行运算
- 扩展赋值运算符自带强转

```java
short a,b;
a = a + b;  //错误 a+b结果为int类型
a += b;
```

- 关于long为什么可以转到float ：

  数据范围：

  ​				long：2^63

  ​				float：3.4*10^38

  ```
  		3.4*10^38  >  3.4*8^38  =  3.4*2^3^38 == 3.4*2^114  >  2^63
  ```
  
##### 运算符

-   " / " : 整数相除，结果是整数。想要得到小数，可以除以1.0
- " % " : %的结果的符号和前面的那个数一致
- "  && 、||  " ：具有短路效果

##### 数组

数组：

1.  是一种最基本的数据结构

2.  数组的长度在创建后是不可改变的

- 一维数组


  ​		数组的初始化：

     	1. 动态初始化    只给长度，不给元素
     	
     	  ​				int [] arr = new  int[3];
     	
     	2 .静态初始化   不给长度，给元素
     	
     	  ​				int[]  arr  =  new  arr[]{1,2,3,4};
     	
     	  ​				简化版：int[]  arr  =  {1,2,3,4};

 **一些简单的使用数组的方法**

  1. ​	得到完整数组： `String  str  =  Arrays.toString( arr ) ;` 
  2. ​    复制数组：
     -   `int[]  arr2  =  Arrays.copyOf( arr , 4 ) ;`    
     -   `System.arraycopy( arr , 0 , arr2 , 0 , 4 ) ;` 

3. 排序    `Arrays.sort( arr ) ;`

- 二维数组

  ​		 `int[][]  arr  =  new  arr[2][4];`

​				`int[][]  arr  =  {{1,2,3},{4,5,6},{7,8,9}};`

```java
        for(int[] a : arr){
            for (int b : a){
            	System.out.println(b);
            }
        }
```

**引用类型数组**

```java
Cell[]  cell = new Cell[4];	//Cell为自定义类
```

<img src=".\note_imgs\数组内存.png" style="zoom:67%;" />

​	`new  Cell[4]` 实际上是在堆中分配了4个空间用于存放4个Cell类型的引用，并不是分配了4个Cell类型的对象，需要通过  `cell[0] = new  Cell();` 将每个引用指向具体的对象

​	引用类型数组的默认初始值为 `null`

##### continue、break、return的区别

- continue：指跳出当前的这一次循环，继续下一次循环
- break：指跳出整个循环体，继续执行循环下面的语句
- return：用于跳出所在方法，结束该方法的运行
  
  - `return ；` 直接使用return结束方法执行，用于没有放回值的函数的方法
  
  - `return  value；` return一个特定值，用于有返回值函数的方法

##### public  static  void  main( String[]  args)

JVM 会寻找特定的方法签名开始运行，该方法为  `public static void main(String[] args)` ， args 是字符串数组类型的参数， `String[] args` 也可以写成 `Strting args[]` 、`String...args` 

参数类型（字符串）是固定的，但 `args` 可以更改为任何内容

- `public` ：访问修饰符，表示任何类都可以访问此方法（如果其他类可以访问此类）
- `static` ：标识与类相关的关键字，表示无需创建类实例即可对其访问
- `void` ：方法的返回类型，表示没有返回值
- `main` ：方法名，JVM 以此方法名作为具有特定签名的应用的起点
- `String[] args` ：`main` 方法的参数，参数名可以更改

##### 可变长参数

在定义方法时，在最后一个形参后加上三个点 `...` ，就表示该形参可以接收多个参数值，多个参数值被当成数组传入

```java
public static void method(String... args){
    //......
}
```

- 可变参数只能作为函数的最后一个参数，但其前面可以有也可以没有任何其他参数
- 由于可变参数必须是最后一个参数，所以一个函数最多只能有一个可变参数
- 遇到方法重载会优先匹配固定参数的方法，因为固定参数的方法匹配度更高

##### 执行顺序

Java 类中的不同块及其执行顺序：

1. 静态块
2. 初始化块（匿名块）
3. 构造器

```java
public class A {
    {
        System.out.println("Anonymous Block");
    }
    static {
        System.out.println("Static Block");
    }
    public A() {
        System.out.println("Constructor of Class");
    }
    public static void main(String[] args) {
        A a = new A();
        System.out.println("--------------------");
        A a1 = new A();
    }
}
```

上面程序的输出结果：

```
Static Block
Anonymous Block
Constructor of Class
--------------------
Anonymous Block
Constructor of Class
```

static 块仅在加载类时调用一次，但是实例化一个类的对象时，匿名块和构造器就会被调用，初始化块首先执行，然后构造器

##### 栈 、堆的简单理解

- 功能不同

  - 栈内存用来存储局部变量和方法调用

  - 堆内存用来存储Java中的对象。无论是成员变量、局部变量、还是类变量，它们指向的对象都存储在堆内存中

- 共享性不同
  - 栈内存是线程私有的
  - 堆内存是所有线程共有的
- 异常错误不同
  - 栈空间不足：java.lang.StackOverFlowError
  - 堆空间不足：java.lang.OutOfMemoryError
- 空间大小
  - 栈的空间大小远远小于堆的

**常见误区**

1. Java中的基本数据类型一定存储在栈中吗？

   ​	不一定，栈内存用来存储局部变量和方法调用。如果该局部变量是基本数据类型如  `int  a  =  1 ;` 那么直接将该值存储在栈中。

   ​		如果该局部变量是一个对象，如  `int[]  arr = new  int[]{1,2,3} ;`

   ​	那么将引用存储在栈中而对象 {1，2，3} 存储在堆内

2. 栈的速度比堆快吗？

​			一定情况下栈的速度是比堆快的，但快的不明显，毕竟都是RAM。所以这算不上栈和堆的一大区别

> [链接](https://iamjohnnyzhuang.github.io/java/2016/07/12/Java%E5%A0%86%E5%92%8C%E6%A0%88%E7%9C%8B%E8%BF%99%E7%AF%87%E5%B0%B1%E5%A4%9F.html)

**拓展**

​	内存分为四个区：

- stack  segment ：存放函数参数和局部变量
- heap  segment ：存放对象
- data  segment ：静态变量
- code  segment：类中的方法

##### String不可变

​		String对象被设计成不可改变的对象，即String对象所包含的字面值发生改变时，会开辟一个新的地址，创建一个新的对象，原地址不变

```java
String a = "hello";
String b = a;	
a = "world";	// 此时b的值依然为"hello"
```

1. 变量a存储的是字符串的引用

2. 不可变指的是堆中的字符串数据不可变

3. 对于变量a，可以随意赋值，但是原来的字符串不变；这样其他引用这个字符串的地方，不会受到影响，线程也安全

   **创建字符串**

   java的字符串池属于JVM专门给指定的特殊内存区域，用来存储**字符串字面量**

   - 字面量 ：`String  a = "hello" ;` 

   - new ： `String  b = new String("world") ;` 

   **两种方式的区别**
   
   1. 字面量的方式，首先会在常量池中找该字符串。如果存在，则将变量指向这个地址，如果不存在，则在方法区创建一个存放字面值 "hello" 的地址
   
   2. 使用new创建String变量时，在堆中创建一个存放 "world" 的对象，使变量b指向堆中的对象
   
  ```java
   String a = "hello";
   String b = "hello";
   String c = new String("hello");
   String d = new String("hello");
  ```

​		分析：

1. ​      在创建 a 时，字符串常量池中没有存储 "hello" 的地址，就在常量池中创建一个存      放"hello" 的地址，使变量 a 指向该地址。创建 b 的时候，字符串常量池已存在存  放"hello" 的地址，将 b 变量指向该地址，此时 a 与 b 指向同一内存地址，所以 此时 a == b 为 true
2. ​     使用new关键字创建对象时，每一次都会在堆中创建一个新的对象，所以 c 和 d 指向对象的地址不同，c == d 为 false
3. ​     a 与 c 指向的地址不同，所以 a == c 为false



### 数组的排序

​	所谓排序，是指将数组元素按照从小到大或者从大到小的顺序重新排列对于元素较多的数组，排序算法的优劣至关重要

​	一般情况下，通过排序过程中数组元素的交换次数来衡量排序算法的优劣

##### 数组冒泡排序算法

冒泡排序：比较相邻的元素，如果违反最后的规则，则交换

​		可以简化理解为：第一次找到所有元素中最大的放在最后一个位置上，不在变动

​		第二次找到剩余所有元素中最大的放在倒数第二个位置上，不在变动。以此类推，直到排序完成

```java
int[] arr = new arr[10];
for(int i=10; i<arr.length; i++){
    arr[i] = (int)(Math.random()*100);
}
for(int i=0; i<arr.length-1 ; i++){
    for(int j=0; j<arr.length-1-i; j++){
        if(arr[j] > arr[j+1]){
            int temp = arr[j+1];
            arr[j+1] = arr[i];
            arr[i] = temp;
        }
    }
}
```

