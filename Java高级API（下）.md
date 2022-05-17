##### `RandomAccessFile`

`Java` 提供了一个可以对文件随机访问的操作，访问包括读和写操作，该类名为 `RandomAccessFile`，该类的读写是基于指针的操作

`RandomAccessFile` 在对文件进行随机访问操作时有两个模式，分别是只读模式（只读取文件数据）和读写模式（对文件数据进行读写）

- 模式：在创建 `RandomAccessFile` 时，其提供的构造方法要求我们传入访问模式

  - `RandomAccessFile(File file,String mode)` 

  - `RandomAccessFile(String filename,String mode)` 

  - 第一个参数是需要访问的文件，第二个参数是访问模式：“ `r` ”：读；“ `w` ”：写

    ```java
    File file = new File("pathname");
    RandomAccessFile raf = new RandomAccessFile(file,"rw");
    ```

- 字节数据读写操作
  - `void  write(int b)` ：根据当前指针所在的位置处写入一个字节，是将参数 `b` 的 “ 低 8 位 ” 写入
  
  - `void  wirite(byte[] b)` ：该方法会根据当前指针所在位置处连续写出给定数组中的字节
  
    ```java
    File file = new File("pathname");
    RandomAccessFile raf = new RandomAccessFile(file, "rw");
    byte[] bytes = "初梦".getBytes();
    raf.write(bytes);
    raf.close();
    ```
  
    > 默认使用 `ISO-8859-1` 格式编码，系统使用 `gb2312` 编码格式进行解码
  
  - `void  wirte(byte[] b,int offset,int len)` ：根据当前指针所在位置处连续写出给定数组中的部分字节，这个部分是从数组的 `offset` 处开始，连续 `len` 个字节
  
  - `int  read()` ：该方法会从文件中读取一个 `byte` 填充到 `int` 的低 8 位（高 24 位为 0 ），返回值范围 `0 ~ 255`；如果返回 `-1` 表示读取到了文件的末尾，每次读取后自动移动文件指针，准备下次读取
  
  - `int  read(byte[] b)` ：该方法会从指针位置处尝试最多读取给定数组的总长度的字节量，并从给定的字节数第一个位置开始，将读取到的字节顺序存放至数组中，返回值为实际读取到的字节量
  
    ```java
    File file = new File("Hello.txt");
    RandomAccessFile raf = new RandomAccessFile(file,"r");
    byte[] bytes = new byte[1024];
    int len = raf.read(bytes);
    System.out.println(len);
    System.out.println(new String(bytes));
    ```
  
  - `void  close()` ：在对文件访问的操作全部结束后，要调用 `close()`  方法来释放与其有关联的所有系统资源

- 文件指针操作

  - `long  getFilePointer()` ：该方法用于获取当前 `RandomAccessFile` 的指针位置；`RandomAccessFile` 的读写操作都是基于指针的，也就是说总是在指针当前所指向的位置进行读写操作

  - `int  skipBytes(int n)` ：该方法可以跳过一些较少数量的字节，这可能有任意数量的条件引起，在跳过 `n` 个字节之前已到达文件的末尾只是其中一种可能；此方法不抛出 `EOFException`，返回跳过的实际字节数，如果 `n` 为负数，则不跳过任何字节

    ```java
    File file = new File("Hello.txt");
    RandomAccessFile raf = new RandomAccessFile(file,"rw");
    System.out.println(raf.getFilePointer()); // 指针开始位置，0 
    raf.skipBytes(4);
    System.out.println(raf.getFilePointer());  // 4
    byte[] bytes = new byte[1024];
    raf.read(bytes);						// 从第 5 个字节开始读取
    System.out.println(new String(bytes));
    System.out.println(raf.getFilePointer());    	// 文件总字节数或 1024
    raf.seek(0);							// 将指针移到 0
    System.out.println(raf.getFilePointer());	// 0
    raf.close();
    ```

  - `void  seek(long pos)` ：将指针移到指定位置

#### 基本 `IO`

我们编写的程序除了自身会定义一些数据信息外，经常还会引用外界的数据，或是将自身的数据发送到外界，比如：我们编写的程序想要读取一个文本文件，又或是我们想要将程序中的某些数据写入到一个文件中，这时我们就要使用输入与输出

- 输入：一个从外界进入到程序的方向，通常我们需要 “ 读取 ” 外界的数据时，使用输入，所以输入是用于**读取数据**的
- 输出：一个从程序发送到外界的方向，通常我们需要 “ 写出 ” 数据到外界时，使用输出，所以输出是用来**写出数据**的

按照流是否直接与特定的地方（如磁盘、内存、设备等）相连，分为节点流和处理流两类；处理流的构造方法总是要带一个其他的流对象做参数，一个流对象经过其他流的多次包装，称为流的连接，

- 节点流：也称低级流，可以从或向一个特定的地方（节点）读取数据
- 处理流：也称高级流或过滤流，是对一个已经存在的流的连接和封装，通过所封装的流的功能调用实现数据读写

###### **`IS` 和 `OS` 常用方法**

`Inputstream` 是所有字节输入流的父类，其定义了基础的读取方法：

- `int  read()` ：读取一个字节，以 `int` 形式返回，为 `int` 值的 “ 低 8 位 ” ，若返回 -1，则表示 `EOF`（`end of file`）
- `int  read(byte[] b)` ：尝试最多读取给定数组的 `length` 个字节并存入数组，返回值为实际读取到的字节量

`OutputStream` 是所有字节输出流的父类，其定义了基础的写出方法：

- `void  write(int b)` ：写出一个字节，写出的是给定的 `int` 的 “ 低 8 位 ”
- `void  write(byte[] b)` ：将给定的字节数组中的所有字节全部写出

###### **文件流**

`FileOutputStream` 是文件的字节输出流，使用该流可以以字节为单位将数据写入文件；若指定的文件已经包含内容，那么当使用 `FileOutputStream` 对其写入数据时，会将该文件中原有的数据全部清除

1. 构造方法

   - `FileOutputStream(File file)` ：创建一个向指定 `File` 对象表示的文件中写出数据的文件输出流

   - `FileOutputStream(String filename)` ：创建一个向具有指定名称的文件中写出数据的文件输出流

     上面两种构造方法创建的对象对文件进行写操作时会覆盖文件中的原有数据，若想在文件的原有数据之后追加新数据则需要使用以下构造方法：

   - `FileOutputStream(File file,boolean append)` ：创建一个向指定 `File` 对象表示的文件中写出数据的文件输出流，`append` 为 `true` 时，写入数据将在文件末尾追加

   - `FileOutputStream(String filename,boolean append)` ：创建一个向具有指定名称的文件中写出数据的文件输出流，`append` 为 `true` 时，写入数据将在文件末尾追加

2. 常用方法
   - `void  write(int d)` ：将指定字节输出流，这里只写给定 `int` 的 “ 低 8 位 ”
   - `void  write(byte[] b)` ：将 `b.length` 个字节从指定 `byte` 数组写出此文件输入流中
   - `void  write(byte[] b,int offset,int len)` ：将指定 `byte` 数组中从偏移量 `offset` 开始的 `len` 个字节写入此文件输入流

```java
FileOutputStream fos = new FileOutputStream("Hello.txt",true);
fos.write("Hello".getBytes());
```

`FileInputStream` 是文件的字节输入流，使用该流可以以字节为单位将数据读取文件

1. 构造方法
   - `FileInputStream (File file)` ：创建一个向指定 `File` 对象表示的文件中写出数据的文件输入流
   - `FileOutputStream(String filename)` ：创建一个向具有指定名称的文件中写出数据的文件输入流
2. 常用方法
   - `int  read()` ：从输入流中读取一个字节，若返回 -1则表示文件结尾（`EOF`）
   - `int  read(byte[] b)` ：从此输入流中将最多 `b.length` 个字节的数据读入到字节数组 `b` 中

`RandomAccessFile` 和 `FileInputStream` 、`FileOutputStream` 的区别

- 相同点：
  - 二者在进行读写操作时使用的方法 `write(int d)` / `write(byte[] b)` / `read()` / `read(byte[] b)` 都是相同的原理，都是参考自 `InputStream` / `OutputStream`
- 不同点：
  - `RandomAccessFile` 基于指针的随机访问，通过改变指针可以任意访问文件中的内容
  - `FileInputStream` 、`FileOutputStream` 没有指针的概念，通过 “ 覆盖 ” 和 “ 追加 ” 两种模式完成读写

###### **缓冲流**（`BOS` 与 `BIS`）

`BOS` 基本工作原理：在向硬件设备做写出操作时，增大写出次数无疑会降低写出次数，为此我们可以使用缓冲输出流来一次性批量写出若干数据减少写出次数来提高写出次数

`BufferedOutputStream` 缓冲输出流，内部维护着一个缓冲区，每当我们向该流写出数据时，都会先将数据存入缓冲区，当缓冲区已满时，缓冲区会将数据一次性全部写出

```java
FileOutputStream fos = new FileOutputStream("Hello.txt");
BufferedOutputStream bos = new BufferedOutputStream(fos);
bos.write("Hello".getBytes());
// 程序关闭流之前，缓冲输出流会将缓冲区内容一次性写出
bos.close();
```

使用缓冲输出流可以提高写出的效率，但是这也存在一个问题，就是写出数据缺乏及时性，有时我们需要在执行玩某些操作后，就希望将这些数据确实写出，而非在缓冲区保存直到缓冲区满后才写出，这时我们可以使用缓冲流的一个方法 `flush`

- `void  flush()` ：清空缓冲区，将缓冲区中的数据强制写出

<img src=".\note_imgs\BufferedOutputStream.png" style="zoom: 67%;" />

`BIS` 基本工作原理：在读取数据时若以字节为单位读取数据，会导致读取次数过于频繁从而大大的降低读取效率，为此我们可以通过提高一次读取的字节数量减少读写次数来提高读取的效率

`BufferedInputStream` 是缓冲字节输入流，其内部维护着一个缓冲区（字节数组），使用该流在读取一个字节时该流会尽可能多的一次性读取若干个字节并存入缓冲区，然后逐一的将字节返回，直到缓冲区中的数据被全部读取完毕，会再次读取若干字节从而反复，这样就减少了读取的次数，从而提高了读取效率

`BIS` 是一个处理流，该流为我们提供了缓冲功能

```java
FileInputStream fis = new FileInputStream("Hello.txt");
BufferedInputStream bis = new BufferedInputStream(fis);
int d;
// 缓冲读入，并非一个字节一个字节从文件中读取
while ((d = bis.read()) != -1){
    System.out.print(d + " ");
}
bis.close();
```

缓冲区的文件复制

```java
FileInputStream fis = new FileInputStream("Hello.txt");
FileOutputStream fos = new FileOutputStream("hello_copy.txt");
BufferedInputStream bis = new BufferedInputStream(fis);
BufferedOutputStream bos = new BufferedOutputStream(fos);

byte[] bytes = new byte[1024];
int d;
// 缓冲读入，并非一个字节一个字节从文件中读取
while ((d = bis.read(bytes)) != -1){
    bos.write(bytes,0,d);
    bos.flush();
}
bis.close();
bos.close();
fis.close();
fos.close();

fis.delete(); // FileInputStream 在封装 File 对象之后，会将该对象锁起来，所以该流关闭之前，File 对象无法删除，需要先将流关闭才能操作
```

###### 文件乱码问题

<img src=".\note_imgs\编码和解码.png" style="zoom:67%;" />

个人理解记录：对于各种字符，如数字、字母、特殊字符、中文、其他语言等，用其写成的文本文件要保存在硬盘上，需要使用编码；假如有一个 `TXT` 文件由 `A` 编码格式编码保存在硬盘上，当这个文件被发送给另一个地方，那么也应使用 `A` 编码打开，否则就会发生乱码；当然也不一定全部乱码，大部分的编码对于数字、字母都是一样的 `8` 位二进制保存；每个字节由 `8` 位二进制组成，`0000 0000 ~ 1111 1111` 转换成十进制为 `0 ~ 255` 之间的数；简单编码方式理解：对于同一个字：“ 初 ”，有A 编码方式写成：`-27 -120 -99` ，B 编码方式写成：`53 190`，当出现一个不存在的编码时以乱码显示；在 `idea` 测试，每个字节的 `int` 范围为 `-128 ~ 127`

###### 序列化

对象序列化概念：对象是存在于内存中，有时我们需要将对象保存到硬盘上，又有时我们需要将对象传输到另一台计算机上等这样的操作，这时我们需要将对象转换为一个字节序列，而这个过程称为对象序列化；相反，我们由这样一个字节序列需要将其转换为对应的对象，这个过程就称为对象的反序列化

使用 `OOS` 实现对象序列化

`ObjectOutputStream` 是用来对对象进行序列化的输出流，其实现对象序列化的方法为：

- `void  writeObject(Object obj)` ：将给定的对象转换为一个字节序列后输出

`Serializable` 接口

`ObjectOutputStream` 在对对象进行序列化时有一个要求，就是需要序列化的对象所属的类必须实现 `Serializable` 接口

- 实现了该接口不需要重写任何方法，其只是作为可序列化的标志；若没有实现该接口，会引发 `java.io.NotSerializableException`

- 通过实现该接口的类需要提供一个常量 `serialVersionUID` ，表明该类的版本；若不声明，在对象序列化时也会根据当前类的各个方面计算该类的默认 `serialVersionUID`，但不同的平台编译器实现有所不同，所以若想跨平台，都应该显示的声明版本号
- 如果声明的类型的对象序列化存到硬盘上面，之后随着需求的变化更改了类的属性（增加、减少或改名），那么当反序列化时，就会出现 `InvalidClassException`，这样就会造成不兼容性的问题；但当 `serialVersionUID` 相同时，它就会将不一样的 `field` 以 `type` 的预设值（默认值）反序列化，可避开不兼容的问题

序列化：将数据结构或对象装换成二进制字节流的过程

```java
Emp emp = new Emp("1",2,'d',30);
FileOutputStream fos = new FileOutputStream("Emp.obj");
ObjectOutputStream oos = new ObjectOutputStream(fos);
oos.writeObject(emp);
// 高级流关闭的时候会将其中包含的流一并关闭
oos.close();


private static final long serialVersionUID = 1;
```

反序列化：将在序列化过程中所生成的二进制字节流装换成数据结构或者对象的过程

```java
FileInputStream fis = new FileInputStream("Emp.obj");
ObjectInputStream ois = new ObjectInputStream(fis);
Emp emp = (Emp) ois.readObject();
ois.close();
```

序列化的主要目的是通过网络传输对象或说是将对象存储到文件系统、内存、数据库中

<img src=".\note_imgs\序列化.png" style="zoom: 80%;" />

`transient` 关键字

对象在序列化后得到的字节序列往往比较大，有时我们在对一个对象进行序列化时可以忽略某些不必要的属性，从而对序列化后得到的字节序列 “ 瘦身 ” ；被该关键字修饰的属性在序列化时其值将被忽略

- `transient` 只能修饰变量，不能修饰类和方法
- `transient` 修饰的变量，在反序列化后变量值将会被置成类型的默认值，例如：如果是 `int` 类型，那么反序列化后的结果就是 `0`
- `static` 变量因为不属于任何对象（`Object`），所有无论有没有 `transient` 关键字修饰，均不会被序列化

###### 字符流

字符流是以字符（`char`）为单位读写数据的，一次处理一个 `Unicode`；字符流的底层仍然是基本的字节流

- `Reader` 是字符输入流的父类
- `Writer` 是字符输出流的父类

<img src=".\note_imgs\字符流.png" style="zoom:50%;" />

常用方法：

- `Reader` 常用方法：
  - `int  read()` ：读取一个字符，返回的 `int` 值 “ 低 `16` 位 ” 为有效
  - `int  read(char[] chs)` ：从该流中读取一个字符数组的 `length` 个字符并存入该数组，返回值为实际读取到的字符量
- `Writer` 常用方法：
  - `void  write(int c)` ：写出一个字符，写出给定 `int` 值的 “ 低 `16` 位 ” 为表示的字符
  - `void  write(char[] chs)` ：将给定字符数组中所有字符写出
  - `void  write(String str)` ：将给定的字符串写出
  - `void  write(char[] chs,int offset,int len)` ：将给定的字符数组中从 `offset` 处开始连续 `len` 个字符写出

字符转换流

- `InputStreamReader` ：字符输入流
  - 使用该流可以设置字符集，并按照指定的字符集从流中按照该编码将字节数据转换为字符并读取
  - 构造方法：
    - `InputStreamReader(InputStream in)` ：根据系统默认字符集创建 `ISR`
    - `InputStreamReader(InputStream in,Charset cs)` ：基于给定的字节输入流以及字符编码创建 `ISR`
- `OutputStreamWriter` ：字符输出流
  - 使用该流可以设置字符集，并按照指定的字符集将字符转换为对应字节通过该流写出
  - 使用时最好指定字符集编码，否则使用平台（如 `idea` ）默认字符集编码
  - 构造方法：
    - `OutputStreamWriter(OutputStream out)` ：根据系统默认字符集创建 `OSR`
    - `OutputStreamWriter(OutputStream out,Charset cs)` ：基于给定的字节输出流以及字符编码创建 `OSR`

写出：

```java
FileOutputStream fos = new FileOutputStream("Hello.txt");
OutputStreamWriter osw = new OutputStreamWriter(fos, StandardCharsets.UTF_8);
osw.write("write hello");
osw.close();


FileWriter fw = new FileWriter("Hello.txt",StandardCharsets.UTF_8,true);
fw.write(" hello");
fw.close();
```

读取：

```java
FileInputStream fis = new FileInputStream("Hello.txt");
InputStreamReader isr = new InputStreamReader(fis,StandardCharsets.UTF_8);
int d;
while ((d = isr.read()) != -1) {
    System.out.print((char)d);;
}


FileReader fd = new FileReader("Hello.txt",StandardCharsets.UTF_8);
int d;
while ((d = fd.read()) != -1) {
    System.out.print((char)d);;
}
```

**既然有了字节流，为什么还要有字符流 ？**

问题本质：不管是文件读写还是网络发送接收，信息的最小存储单元都是字节，那为什么 `I/O` 流操作要分为字节操作和字符操作呢 ？

回答：字符流是由 `Java` 虚拟机将字节转换得到的，问题就出在这个过程还算是非常耗时，并且，如果我们不知道编码类型就很容易出现乱码问题；所有，`I/O` 流就干脆提供了一个直接操作字符的接口，方便我们平时对字符进行操作，如果音频文件、图片等媒体文件用字节流比较好，如果涉及到字符的话使用字符流比较好

###### 文件数据的 `IO` 操作

PrintWriter ：具有自动换行刷新的缓冲字符输出流

- 构造方法

  - `PrintWriter(File file)` 

  - `PrintWriter(String filename)` 

  - `PrintWriter(OutputStream out)` 

  - `PrintWriter(OutputStream out,boolean autoFlush)` 

  - `PrintWriter(Writer writer)` 

  - `PrintWriter(Writer writer,boolean autoFlush)`

    其中 `autoFlush` 参数表示 `PrintWriter` 是否具有自动行刷新

`PrintWriter` 提供了丰富的重载 `print` 和 `println` 方法，其中 `println` 方法在于输出目标数据后自动输出一个系统支持的换行符，若该流是具有自动行刷新的，那么每当通过 `println` 方法写出的内容都会被实际写出，而不是进行缓存

- 常用方法：
  - `void  print(int i)` ：打印整数
  - `void  print(char[] chs)` ：打印字符数组
  - `void  print(String str)` ：打印字符串
  - `......`   包括 `8` 种基本类型

###### `BufferedReader`

`BufferedReader` 是缓冲字符输入流，其内部提供缓冲区，可以提高读取效率

- 构造方法
  - `BufferedReader(Reader reader)`

`BufferedReader` 提供了一个可以便于读取一行字符串的方法

- `String  reanline()` ：该方法连续读取一行字符串，直到换行符，返回读取到的字符串；如果到达文件末尾没有可读内容将返回 `null`

```java
FileWriter writer = new FileWriter("Hello.txt");
PrintWriter pw = new PrintWriter(writer,true);
pw.println("Hello \n World");
pw.println("Hello");
pw.close();

FileReader fr = new FileReader("Hello.txt");
BufferedReader br = new BufferedReader(fr);
String str;
while ((str = br.readLine()) != null) {
    System.out.println(str);
}
br.close();
```

###### `IO` 流分类

- 按照流的流向分，可以分为输入流和输出流
- 按照操作单元划分，可以划分为字节流和字符流
- 按照流的角色划分为节点流和处理流

`Java IO` 流共涉及 `40` 多个类，这些类看上去很杂乱，但实际上很有规则，而且彼此之间存在非常紧密的联系，`Java IO` 流的 `40` 多个类都是从如下 `4` 个抽象类基类中派生出来的

- `InputStream` / `Reader` ：所有的输入流的基类，前者是字节输入流，后者是字符输入流
- `OutputStream` / `Writer` ：所有输出流的基类，前者是字节输出流，后者是字符输出流

按操作方式分类结构图：

<img src=".\note_imgs\IO-操作方式分类.png" style="zoom: 67%;" />

按操作对象分类结构图：

<img src="E:\github\note_imgs\IO-操作对象分类.png" style="zoom:67%;" />

###### 获取键盘输入的两种方法

1. 通过 `Scanner`

   ```java
   Scanner input = new Scanner(System.in);
   String s  = input.nextLine();
   input.close();
   ```

2. 通过 `BufferedReader`

   ```java
   BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
   String s = input.readLine();
   ```

#### 异常处理

在 `Java` 语言出现之前，传统的异常处理方式多采用返回值来表示程序出现的异常情况，这种方式虽然为程序员所熟悉，但却有多个坏处：

1. 首先，一个 `API` 可以返回任意的返回值，而这些返回值本身并不能解释该返回值是否代表一个异常情况发生了和该异常的具体情况，需要调用 `API` 的程序自己判断并解释返回值的含义
2. 其次，并没有一种机制来保证异常情况一定会得到处理，调用程序可以简单的忽略该返回值，需要调用 `API` 的程序员记住去检测返回值并处理异常情况这种方式还使程序变得冗长，尤其是当进行 `IO` 操作等容易出现异常的处理时，代码的很大部分用于处理异常情况的 `switch` 分支，程序代码的可读性变得很差

异常处理机制

当程序中抛出一个异常后，程序从程序中断导致异常的代码跳出，`Java` 虚拟机检测寻找和 `try` 关键字匹配的处理该异常的 `catch`，如果找到，将控制权交到 `catch` 块中的代码，然后继续往下执行程序；`try` 块中发生异常的代码不会被重新执行，如果没有找到处理该异常的 `catch` 块，在所有的 `finally` 块代码被执行和当前线程的所属的 `ThreadGroup` 的 `uncaughtException()` 被调用后，遇到异常的当前线程被中止

异常的捕获和处理（`Throwable` 、`Error` 和 `Exception`）

`Java` 异常结构中定义有 `Throwable` 类，`Error` 和 `Exception` 是其派生的两个子类，其中 `Exception` 表示由于网络故障、文件损坏、设备错误、用户输入非法等情况导致的异常，而 `Error` 表示 `Java` 运行时环境出现异常

`try-catch`

- `try{...}` 语句指定了一段代码，该段代码就是一次捕获并处理异常的范围
- 在执行过程中，该段代码可能会产生并抛出一种或几种类型的异常对象，它后面的 `catch` 语句分别对这些异常做了相应的处理
- 如果没有异常发生，所有的 `catch` 代码段都被略过不执行
- 在 `catch` 语句块中是对异常进行处理的代码
- 在 `catch` 中声明的异常对象封装了异常事件发生的信息，在 `catch` 语句块中可以使用这个对象的一些方法获取这些信息

多个 `catch`

每个 `try` 语句块可以伴随一个或多个 `catch` 语句，用于处理可能产生的不同类型的异常；`catch` 捕获的异常类型由上至下的捕获异常类型的顺序应该是子类到父类；一个 `try` 语句块如果有多个 `catch` 块，最多执行其中一个

<img src=".\note_imgs\catch.png" style="zoom:50%;" />

`finally` 的作用

- `finally` 语句为异常处理提供一个统一的出口，使得在控制流程转到程序其他部分之前，能够对程序的状态统一管理
- 无论 `try` 所指定的程序块中是否抛出异常，`finally` 所指定的代码都要执行
- 通常在 `finally` 语句中可以进行资源的释放工作，如关闭打开的文件、删除临时文件等
- `finally` 后面也能够写代码，在 `finally` 执行完之后运行

```java
try {
    throw new RuntimeException("RuntimeException");
} catch (Exception e) {
    System.out.println(e.getMessage());  // RuntimeException
} finally {
    System.out.println("Finally");	 // Finally
}
```

###### `throw` 和 `throws` 关键字

- `throw` ：当程序发生错误而无法处理的时候，会抛出对应的异常对象，除此之外，在某些时刻，我们可能想要自行抛出异常，例如在异常处理结束后，再将异常抛出，让下一层异常处理块捕获，若想要自行抛出异常，我们可以使用 " `throw` " 关键字，并生成指定的异常对象后抛出
- `throws` ：在程序中会声明很多方法，这些方法中很可能会因为某些错误而引发异常，但我们不希望直接在这个方法中处理这些异常，而希望调用这个方法的方法来处理，这时候我们可以使用 ” `throws` “ 关键字来声明这个方法将可能抛出异常；抛出非受检异常调用方法可以不处理，作为声明

重写方法时的 `throws` ：

如果使用继承时，在父类别的某个方法上宣告了 `throws` 某些异常，而在子类别中重新定义该方法时，我们可以：

- 不处理异常（重新定义时不设定 `throws` ）

- 可仅 `throws` 父类别中声明的部分异常

- 可 `throws` 父类别方法中抛出异常的子类异常

  但是不可以：

- `throws` 额外的异常
- `throws` 父类方法中抛出异常的父类异常

以上针对受检异常，可以随意抛出运行时异常（`RuntimeException` 及其子类）

`Java` 异常可以分为检测异常，非检测异常

- 检测异常：可检测异常编译器验证，对于声明抛出异常的任何方法，编译器将强制处理或声明规则，不捕捉这个异常，编译器就通不过，不允许编译
- 非检测异常：非检测异常不遵循处理或者声明规则，在产生此类异常时，不一定非要采取任何适当操作，编译器不会检查是否已经解决了这样一个异常

`RuntimeException` 类属于非检测异常，因为 `JVM` 操作引起的运行时异常随时可能发生，此类异常一般是由特定的操作引起，但这些操作在 `Java` 应用程序中会频繁出现，因此它们不受编译器检查与处理或声明规则的限制

###### `Throwable` 类的常用方法

- `void  printStackTrace()` ：输出错误信息，用来跟踪异常事件发生时执行的堆栈内容（打印 `Throwable` 对象封装的异常信息）
- `String  getMessage()` ：返回异常发生时的简要描述
- `String  toString()`：返回异常发生时的详细信息
- `String  getLocalizedMessage()` ：返回异常对象的本地化信息；使用 Throwable 的子类覆盖这个方法，可以生成本地化信息，如果子类没有覆盖该方法，则该方法返回的信息与 `getMessage()` 返回的结果相同
- `Throwable  getCause()` ：获取该异常出现的原因；很多时候，当一个异常导致异常而被抛出的时候，`Java` 库和开放源代码会将一种异常包装成另一种异常，这时，日志记录和打印根异常就变得非常重要；`Java` 异常类提供 `getCause()` 来检索导致异常的原因，这些可以对异常根层次的原因提供更多的信息，该 `Java` 实际对代码的调试或故障排除有很大的帮助

###### 异常

<img src="E:\github\note_imgs\Throwable.png" style="zoom: 80%;" />

**`Exception` 和 `Error` 的区别**

在 `Java` 中，所有的异常都有一个共同的祖先：`java.lang` 包中的 `Throwable` 类，`Throwable` 类有两个重要的子类：

- `Exception` ：程序本身可以处理的异常，可以通过 `catch` 来进行捕获；`Exception` 又可以分为 `Checked Exception`（受检查异常，必须处理）和 `Unchecked Exception`（不受检查异常，可以不处理）
- `Error` ：`Error` 属于程序无法处理的错误，~~我们没办法通过 `catch` 来进行捕获~~ 不建议通过 `catch` 捕获；例如 `Java` 虚拟机运行错误（`Virtual MachineError`）、虚拟机内存不够错误（`OutOfMemoryError`）、类定义错误（`NoClassDefFoundError`）等，这些异常发生时，`Java`虚拟机（`JVM`）一般会选择线程终止

`Checked Exception` 和 `Unchecked Exception` 的区别

- `Checked Exception` 即受检查异常，`Java` 代码在编译过程中，如果受检查异常没有被 `catch` 或者 `throws` 关键字处理的话，就没办法通过编译

  除了 `RuntimeException` 及其子类以外，其他的 `Exception` 类及其子类都属于受检查异常；常见的受检查异常有：`IO` 相关的异常、`ClassNotFoundException`、`SQLException` ...

- `Unchecked Exception` 即不受检查异常，`Java` 代码在编译过程中，我们即使不处理不受检查异常以可以正常通过编译

  `RuntimeException` 及其子类都统称为非受检查异常，常见的有：

  - `NullPointerException`（空指针错误）
  - `IllegalArgumentException`（参数错误比如方法入参类型错误）
  - `NumberFormatException`（字符串转换为数字格式错误，`IllegalArgumentException`的子类）
  - `ArrayIndexOutOfBoundsException`（数组越界错误）
  - `ClassCastException`（类型转换错误）
  - `ArithmeticException`（算术错误）
  - `SecurityException` （安全错误比如权限不够）
  - `UnsupportedOperationException`（不支持的操作错误比如重复创建同一用户）
  - `...` 

###### `try-catch-finally`

- `try` 块 ：用于捕获异常；其后可接零个或多个 `catch` 块，如果没有 `catch` 块，则必须跟一个 `finally` 块
- `catch` 块 ：用于处理 `try` 捕获到的异常
- `finally` 块 ：无论是否捕获或处理异常，`finally` 块里的语句都会被执行；当在 `try` 块或 `catch` 块中遇到 `return` 语句时，`finally` 语句块将在方法返回之前被执行

**注意：不要在 `finally` 语句块中使用 `return` ！ **当 `try` 语句和 `finally` 语句中都有 `return` 语句时，`try` 语句块中的 `return` 语句会被忽略，这是因为 `try` 语句中的 `return` 返回值会先被暂存一个本地变量中，当执行到 `finally` 语句中的 `return` 之后，这个本地变量的值就变为了 `finally` 语句中的 `return` 返回值

```java
public static void main(String[] args) {
    System.out.println(f(2));   // 输出 0
}

public static int f(int value) {
    try {
        return value * value;
    } finally {
        if (value == 2) {
            return 0;
        }
    }
}
```

###### `finally` 中的代码一定会执行吗 ？

不一定的，在某些情况下，`finally` 中的代码不会被执行；就比如说 `finally` 之前虚拟机被终止运行的话，`finally` 中的代码就不会被执行

```java
try {
	throw new RuntimeException();
} catch (Exception e) {
    System.out.println(e.getMessage());
    System.exit(1);
} finally {
    System.out.println("hello")  // 不会输出
}
```

另外，在以下 2 种特殊情况下，`finally` 块的代码也不会被执行

1. 程序所在的线程死亡
2. 关闭 `CPU`

###### 如何使用 `try-with-resources` ?

1. 适用范围（资源的定义）：任何实现 `java.lang.AutoCloseable` 或者 `java.io.Closeable` 的对象
2. 关闭资源和 finally 块的执行顺序：在 `try-with-resources` 语句中，任何 `catch` 或 `finally` 块在声明的资源关闭后运行

《Effective Java》中明确指出：

> 面对必须要关闭的资源，我们总是应该优先使用 `try-with-resources` 而不是 `try-finally` ；随之产生的代码更短简，更清晰，产生的异常对我们也更有用；`try-with-resources` 语句让我们更容易编写必须关闭的资源的代码，若采用 `try-finally` 则几乎做不到这点

`Java` 中类似于 `InputStream`、`OutputStream`、`Scanner`、`PrintWriter` 等的资源都需要我们调用 `close()` 来手动关闭，一般情况下我们都是通过 `try-catch-finally` 语句来实现这个需求，如下：

```java
//读取文本文件的内容
Scanner scanner = null;
try {
    scanner = new Scanner(new File("D:\\read.txt"));
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
} finally {
    if (scanner != null) {
        scanner.close();
    }
}
```

使用 Java 7 之后的 `try-with-resources` 语句改造上面的代码：

```java
try (Scanner scanner = new Scanner(new File("test.txt"))) {
    while (scanner.hasNext()) {
        System.out.println(scanner.nextLine());
    }
} catch (FileNotFoundException e) {
    e.printStackTrace();
}
```

当多个资源需要关闭的时候，使用 `try-with-resources` 实现起来也非常简单，如果还用`try-catch-finally` 可能会带来很多问题

通过使用分号分隔，可以在 `try-with-resources` 块中声明多个资源

```java
try (BufferedInputStream bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
     BufferedOutputStream bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")))) {
    int b;
    while ((b = bin.read()) != -1) {
        bout.write(b);
    }
}
catch (IOException e) {
    e.printStackTrace();
}
```

###### 使用异常需要注意的地方

- 不要把异常定义为静态变量，因为这样会导致异常栈信息错乱；每次手动抛出异常，我们都需要手动 `new` 一个异常对象抛出
- 抛出的异常信息一定要有意义
- 建议抛出更加具体的异常，比如字符串转换为数字格式错误的时候应该抛出 `NumberFormatException` 而不是其父类 `IllegalArgumentException`
- 使用日志打印异常之后就不要再抛出异常了（两者不要同时存在一段代码逻辑中）
- `...` 

#### 多线程

什么是进程？

进程是操作系统中运行的一个任务；进程（`process`）是一块包含了某些资源的内存区域，操作系统利用进程把它的工作划分为一些功能单元；进程中所包含的一个或多个执行单元称为线程（`thread`），进程还拥有一个私有的虚拟地址空间，该空间仅能被它所包含的线程访问；线程只能归属于一个进程并且它只能访问该进程所拥有的资源，当操作系统创建一个进程后，该进程会自动申请一个名为主线程或首要线程的线程

什么是线程？

一个线程是进程的一个顺序执行流；同类的多个线程共享一块内存空间和一组系统资源，线程本身有一个供程序执行时的堆栈；线程在切换时负荷小，因此，线程被称为轻负荷进程，一个进程中可以包含多个线程

线程与进程的区别？

- 一个进程至少有一个线程

- 线程的划分尺度小于进程，使得多线程程序的并发性高，另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大的提高了程序的运行效率

- 线程在执行过程中与进程的区别在于每个独立的线程有一个程序运行的入口，顺序执行序列和程序的出口；但是线程不能够独立运行，不许依存在应用程序中，由应用程序提供多个线程的执行控制

- 从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行，但操作系统并没有将多个线程看做多个独立的应用来实现进程的调度和管理以及资源分配

###### 创建线程

`Thread` 类是线程类，其每一个实例表示一个可以并发运行的线程，我们可以通过继承该类并重写 `run()` 来定义一个具体的线程，其中重写 `run()` 的目的是定义该线程要执行的逻辑；启动线程是调用线程的 `start()` 而非直接调用 `run()` 方法，`start()` 会将当前线程纳入线程调度，使当前线程可以开始并发运行；当线程获取时间片段后自动开始执行 `run()` 中的逻辑


```java
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println(i);
        }
    }
}


public class ThreadTest {
    public static void main(String[] args) {
        // 此时的线程处于 new 状态，处于该状态的线程无法获取 CPU 分配的时间片资源，不会运行
        Thread thread1 = new MyThread();
        Thread thread2 = new MyThread();
        // 将 new 状态变为可执行状态
        thread1.start();
        thread2.start();
    }
}
```

使用 `Runnable` 创建并启动线程

实现 `Runnable` 接口并重写 `run` 方法来定义线程体，然后再创建线程的时候将 `Runnable` 的实例传入并启动线程；这样做的好处在于可以将线程与线程要执行的任务分离开，减少耦合；同时 `Java` 是单继承的，定义一个类实现 `Runnable` 接口这样的做法可以更好的去实现其他父类或接口，因为接口可以多继承

```java
public class RunnableTest implements Runnable{
    public static void main(String[] args) {
        Runnable r = new RunnableTest();
        Thread t = new Thread(r);
        t.start();
//        new Thread(new RunnableTest()).start();
    }

    @Override
    public void run() {
        System.out.println("hello");
    }
}
```

通常我们可以通过匿名内部类的方式创建线程，使用该方式可以简化编写代码的复杂度，当一个线程仅需要一个实例时我们可以通过这种方式来创建

线程通常用于在一个程序中需要同时完成多个任务的情况，我们可以将每个任务单行为一个线程，使它们得以一同工作

**并发**

多个线程 “ 同时 ” 运行只是我们感官上的一种表现，事实上线程是并发运行的，`OS` 将时间划分为很多时间片段（时间片），尽可能均匀分配给每一个线程，获取时间片段的线程被 `CPU` 运行，而其他线程全部等待，所以微观上是走走停停的，宏观上是都在运行，这种现象叫并发，但不是绝对意义上的 “ 同时发生 ” 

<img src=".\note_imgs\ThreadStatus.png" style="zoom:80%;" />

###### **线程操作 `API`**

- `static  Thread  currentThread()` ：用于获取运行当前代码片段的线程

  ```java
  public static void testCurrent() {
      System.out.println("testCurrent" + Thread.currentThread());
  }
  
  public static void main(String[] args){
      System.out.println("main" + Thread.currentThread());
      testCurrent();
      new Thread(){
          @Override
          public void run() {
              System.out.println("new Thread" + Thread.currentThread());
              testCurrent();
          }
      }.start();
  }
  ```

- `long  getId()` ：返回该线程的标识符

- `String  getName()` ：返回该线程的名称

- `int  getPriority()` ：返回线程的优先级

- `Thread.state  getState()` ：获取线程的状态

- `boolean  isAlive()` ：测试线程是否处于活动状态

- `boolean  isDaemon()` ：测试线程是否为守护线程

- `boolean  isInterrupted()` ：测试线程是否已经中断

  ```java
  Thread thread = new Thread("hello");
  thread.setPriority(Thread.MAX_PRIORITY);
  System.out.println(thread.getPriority()); 
  System.out.println(thread.getName());	// hellp
  System.out.println(thread.getId());		// 16
  ```

###### 线程的优先级

线程的切换是由线程调用控制的，我们无法通过代码来进行干涉，但是我们可以通过提高线程的优先级来最大程度的改善线程获取时间片的记录

线程的优先级被划分 `10` 级，值分别是 `1 - 10` ，其中 `1` 最低，`10` 最高，线程提供 `3` 个常量来表示最低、最高、默认的优先级以及一个方法设置线程的优先级：

- `Thread.MIN_PRIORITY` ：`1`
- `Thread.MAX_PRIORITY` ：`10`
- `Thread.NORM_PRIORITY` ：`5`
- `void  setPriority()` ：设置线程的优先级

###### 守护线程

守护线程与普通线程在表现上没有什么区别，我们只需要通过 `Thread` 提供的方法来设定即可；守护线程的特点是：**当线程中只剩下守护线程时，所有守护线程强制终止**；垃圾回收机制（`GC`）就是运行在一个守护线程上

- `void  setDaemon(boolean on)` ：当参数为 `true` 时，该线程为守护线程

  ```java
      public static void main(String[] args) throws InterruptedException {
          // 每 0.1 秒输出一个 hello
          Thread t = new Thread(){
              @Override
              public void run() {
                  while (true) {
                      try {
                          sleep(100);
                      } catch (InterruptedException e) {
                          e.printStackTrace();
                      }
                      System.out.println("hello");
                  }
              }
          };
          // 设置线程 t 为守护线程，如果不设置程序在 5 秒后不会停止，线程 t 会继续运行
          t.setDaemon(true);
          t.start();
          Thread.currentThread().sleep(5000);
          System.out.println("over");
      }
  ```

`sleep()` ： `Thread` 的静态方法 `sleep()` 用于使当前线程进入阻塞状态

- `static  void  sleep(long ms)` ：该方法会使当前线程进入阻塞状态指定毫秒，当阻塞指定毫秒后，当前线程会重新进入 `Runable` 状态，等待分配时间片（阻塞状态下，`OS` 不再分配时间片资源）；该方法会抛出 `InterruptedException`，使用该方法需要捕获这个异常

  关于 `InterruptedException` ：假如有个方法调用线程 `t` ，并使它阻塞 5 秒，在这 5 秒内，有另一个方法调用线程 `t` ，运行 `t.interrupt()` ，这时线程 t 以抛出异常  `InterruptedException` 来让自己提前结束阻塞

- `TimeUnit.SECONDS.sleep(1);` ：阻塞 1 秒

  ```java
  public static void main(String[] args) {
      Thread t = new Thread(){
          @Override
          public void run() {
              System.out.println("start");
              try {
                  TimeUnit.SECONDS.sleep(10000);
              } catch (InterruptedException e) {
                  System.out.println("被中断了");
              }
              System.out.println("end");
          }
      };
      t.start();
      t.interrupt();    
  }
  
  输出：
  	start
  	被中断了
  	end
  ```

###### `yield()`、`join()`

- `static  void  yield()` ：该方法用于使当前线程主动让出当次 `CPU` 时间片回到 `Runable` 状态，等待分配时间片
- `void  join()` ：该方法用于等待当前线程结束，会抛出 `InterruptedException`

```java
public static void main(String[] args) {
    Thread t1 = new Thread(){
        @Override
        public void run() {
            for (int i = 0; i < 10; i++) {
                System.out.println("下载" + i + "%");
                try {
                    sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println("下载完成");
        }
    };
    Thread t2 = new Thread(){
        @Override
        public void run() {
            try {
                t1.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("打开图片");
        }
    };
    t1.start();
    t2.start();
}
```

###### 线程同步

多个线程并发读写同一个临界资源时会发生 “ 线程并发安全问题 ”

常见的临界资源：

- 多线程共享实例变量
- 多线程静态公共变量

若想解决多线程安全问题，需要将异步操作变为同步操作

- 异步操作：多线程并发的操作
- 同步操作：有先后顺序的操作

`synchronized` 关键字是 `Java` 中的同步锁

**锁机制**

`Java` 提供了一种内置的锁机制来支持原子性（指这段代码必须执行完之后才能让其他线程进行访问）

同步代码块（`Synchronized` 关键字），同步代码包含两部分：一个作为锁的对象的引用，一个作为由这个锁保护的代码块

```java
synchronized(同步监视器--锁对象引用) {
	// ...
}
```

若方法所有代码都需要同步也可以给方法直接加锁；每个 `Java` 对象都可以用作一个实现同步的锁，线程进入同步代码块之前会自动获得锁，并且在退出同步代码块时自动释放锁，而且无论是通过正常途径退出还是通过抛出异常退出都一样，获得内置锁唯一途径就是进入由这个锁保护的同步代码块或者方法

```java
public class SynchronizedTest extends Thread{
    public SynchronizesTest(String name) {
        super(name);
    }

    static long startTime;
    static final Object OBJ = new Object();

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName()+ "  " + (System.currentTimeMillis() - startTime));
        
        synchronized (SynchronizesTest.OBJ) {
            System.out.println(Thread.currentThread().getName()+ " 锁住 " + (System.currentTimeMillis() - startTime));
            try {
                sleep(5000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName()+ " 释放 " + (System.currentTimeMillis() - startTime));
        }
    }

    public static void main(String[] args) {
        startTime = System.currentTimeMillis();
        Thread t1 = new SynchronizesTest("t1");
        Thread t2 = new SynchronizesTest("t2");
        t1.start();
        t2.start();
    }
}
```

选择合适的锁对象：

使用 `synchronized` 需要对一个对象上锁以保证线程同步，那么这个锁对象应该注意：

- 多个需要同步的线程在访问该同步块时，看到的应该是同一个锁对象引用，否则达不到同步效果
- 通常我们会使用 `this` 来作为锁对象

选择合适的锁范围：

在使用同步块时，应当尽量在允许的情况下减少同步范围，以提高并发的效率

方法锁和静态方法锁

- 方法锁：对象锁（实例锁）；`synchronized` 是对类的当前实例（当前对象）进行加锁，防止其他线程同时访问该类的该实例的所有 `synchronized` 块，每一个对象都有一个锁，且是唯一的
  - 指的是 “ 类的当前实例 ” ，类的两个不同实例就没有这种约束了

- 静态方法锁：当我们对一个静态方法加锁，如：

  ```java
  public  synchronized  static  void  fun(){}
  ```

  那么该方法锁的对象是类对象，每一个类都有唯一的一个类对象，获取类对象的方式：`类名.class` 

  - `static synchronized` 又称为类锁 / 全局锁
  - 该锁针对的是类，无论实例出多少个对象，那么线程依然共享该锁；`static synchronized`  是限制多个线程中该类的所有实例同时访问该类所对应的代码块
  - 锁住的只是 `static synchronized` 块，`synchronized` 块锁不住，而不加锁的方法更加锁不住

静态方法与非静态方法同时声明了 `synchronized` ，它们之间是非互斥关系的，原因在于：静态方法锁的是类对象，非静态方法锁的是当前方法所属对象

```java
public class SynchronizedTwoTest {
    public static synchronized void test () {
        System.out.println(Thread.currentThread() + "start");
        try {
            sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println(Thread.currentThread() + "end");
    }
    public static void main(String[] args) {
        new Thread(){
            @Override
            public void run() {
                SynchronizedTwoTest.test();
            }
        }.start();
        new Thread(){
            @Override
            public void run() {
                SynchronizedTwoTest.test();
            }
        }.start();
    }
}
```

###### `wait()` 、`notify()`

多线程之间需要协调工作，例如：浏览器的一个显示图片的 `displayThread` 想要执行显示图片的任务，必须等待下载线程 `downloadThread` 将该图片下载完成，如果图片还没有下载完，`displayThread` 可以暂停，当 `downloadThread` 完成了任务后，在通知 `displayThread` 继续执行；就是说：如果条件不满足，则等待，当条件满足时，等待该条件的线程将被唤醒

在 `Java` 中，这个机制的实现依赖于 `wait() / notify()`，等待机制与锁是密切关联的

`notify()` 和 `notifyAll()`

`notify()` 和 `notifyAll()` 都是用来唤醒调用 wait() 进入等待锁资源队列的线程

- `notify()` ：唤醒正在等待此对象监视器的单个线程，如果有多个线程在等待，则选中其中一个随机唤醒（由调度器决定），唤醒的线程享有公平竞争资源的权利
- `notifyAll()`：唤醒正在等待此对象监视器的所有线程，唤醒的所有线程公平竞争资源

`wait()`

`wait()` 是 `Object` 类的实例方法，调用 `wait()` 会使得当前线程进入等待状态，只有获取到锁才能调用 `wait()` （在同步块中使用），未获得锁时调用 `wait()` 会抛出异常，等待状态会释放执行 `wait()` 的锁资源（仅限于执行 `wait()` 的锁，其他锁资源并不会释放）

`wait()` 可以设置等待时间，到达时间自动唤醒而不需要 `notify()` 、`notifyAll()`；调用 `wait()` 需要捕获 `InterruptedExcepyion`（避免数据期间设置中断标志）

`wait()` 使用在同步锁保护的同步代码块或者方法内；线程如果调用 `wait()` 方法失去锁资源，而后被其他线程唤醒，如果此时线程没有获取对应的锁资源，即使唤醒也依旧不会执行

实例：

```java
public class WaitTest {
    public static void main(String[] args) {
        Object obj1 = new Object();
        Object obj2 = new Object();
        long start = System.currentTimeMillis();
        Runnable r1 = new Runnable() {
            @Override
            public void run() {
                System.out.println("r1 start " + (System.currentTimeMillis() - start));
                synchronized (obj1) {
                    System.out.println("r1 getLock 1 " + (System.currentTimeMillis() - start));
                    synchronized (obj2) {
                        System.out.println("r1 getLock 2 " + (System.currentTimeMillis() - start));
                        try {
                            sleep(2000);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                        obj1.notifyAll(); // 将所有等待 obj1 的线程唤醒
                        System.out.println("r1 开始等待 " + (System.currentTimeMillis() - start));
                        try {
                            obj1.wait();
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }
                        System.out.println("r1 等待结束 " + (System.currentTimeMillis() - start));
                        obj1.notifyAll();
                    }
                    System.out.println("r1 已释放 lock 2 " + (System.currentTimeMillis() - start));
                }
                System.out.println("r1 已释放 lock 1 " + (System.currentTimeMillis() - start));
            }
        };
        Runnable r2 = new Runnable(){
            @Override
            public void run() {
                System.out.println("r2 start " + (System.currentTimeMillis() - start));
                synchronized (obj1) {
                    System.out.println("r2 get lock 1" + (System.currentTimeMillis() - start));
                    System.out.println("r2 sleep 2s");
                    try {
                        sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    obj1.notifyAll();
                    System.out.println("r2 开始等待" +  (System.currentTimeMillis() - start));
                    try {
                        obj1.wait(10);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println("r2 等待结束"+  (System.currentTimeMillis() - start));
                    obj1.notifyAll();
                }
            }
        };
        Runnable r3 = new Runnable() {
            @Override
            public void run() {
                System.out.println("r3 start " + (System.currentTimeMillis() - start));
                try {
                    sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                synchronized (obj2) {
                    System.out.println("r3 get lock 2" + +  (System.currentTimeMillis() - start));
                }
                System.out.println("r3 已释放 lock3" +   (System.currentTimeMillis() - start));
            }
        };
        new Thread(r1).start();
        new Thread(r2).start();
        new Thread(r3).start();
    }
}
```

输出：

```
r2 start 9
r1 start 9
r3 start 9
r2 get lock 114
r2 sleep 2s
r3 get lock 22015
r2 开始等待2015
r3 已释放 lock32015
r1 getLock 1 2015
r1 getLock 2 2015
r1 开始等待 4028
r2 等待结束4028
r1 等待结束 4028
r1 已释放 lock 2 4028
r1 已释放 lock 1 4028
```

###### `volatile`

`Java` 提供了一种稍弱的同步机制，即使用 `volatile` 修饰变量，用来确保变量的更新操作实时通知到其他线程

```java
public class VolatileTest {
    // volatile 修饰使得线程每次获取时间片运行的时候都先去主内存中重新获取
    private volatile static int found = 0;
    
    public static void change() {
        VolatileTest.found = 1;
    }
    public static void main(String[] args) {
        Runnable r1 = new Runnable() {
            @Override
            public void run() {
                System.out.println("好累");
                // 第一次的时候取了 found，后面一直用的都是线程副本中的 found
                while (found == 0) {

                }
                System.out.println("睡觉");
            }
        };
        Runnable r2 = new Runnable() {
            @Override
            public void run() {
                System.out.println("还有一小时");
                try {
                    sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("change");
                change();
                System.out.println("跳过一小时");
            }
        };
        new Thread(r1).start();
        new Thread(r2).start();
    }
}
```

代码的解析：

<img src="E:\github\note_imgs\volatile.png" style="zoom: 50%;" />

- `found` 变量是多线程之间共享的，每次修改都会同步带主内存中
- 每个线程运行的时候都只会读取一次
- 加了 `volatile` 每次运行的时候都会从主内存读取一次

`volatile` 和 `synchronized` 的区别

1. `volatile` 是轻量级，只能修饰变量；`synchronized` 是重量级的，还可以修饰方法
2. `volatile` 只能保证数据的可见性，不能用了同步，因为多个线程并发访问 `volatile` 修饰的变量不会发生阻塞；`synchronized` 即可以保证可见性，同时可以保证原子性，因为只有获取了锁的线程才能进入临界区，从而保证临界区所有的语句都全部执行，多个线程争夺 `synchronized` 锁对象时，会出现线程阻塞，所以在适当的时候使用 `volatile` 可以提高线程执行的效率

公平锁与非公平锁

- 非公平锁：每个线程获取锁的机会是不平等的，常见的非公平锁：`synchronized`、`ReentrantLock(false)`
- 公平锁：每个线程获取锁的机会是平等的，常见的公平锁对象：`ReentrantLock(true)`

```java
public class SyncTest {
    public static void main(String[] args) {
        Task task = new Task();
        Thread t1 = new Thread(task,"线程一");
        Thread t2 = new Thread(task,"线程二");
        Thread t3 = new Thread(task,"线程三");

        t1.start();
        t2.start();
        t3.start();
    }
}
class Task implements Runnable {
    @Override
    public void run() {
        while (true) {
            synchronized (this) {
                System.out.println(Thread.currentThread().getName());
            }
        }
    }
}
```

使用 `ReentrantLock(true)` 重写类 `Task`

```java
class Task implements Runnable {
    Lock lock = new ReentrantLock(true);
    @Override
    public void run() {
        while (true) {
            lock.lock();
            try {
                System.out.println(Thread.currentThread().getName());
            } finally {
                lock.unlock();
            }
        }
    }
}
```

此时输出：

```
线程三
线程二
线程一
线程三
线程二
线程一
...
```

###### 读写锁

独占锁（写锁）/ 共享锁（读锁）

- 独占锁：指该锁一次只能被一个线程锁持有，对于 `ReentrantLock` 和 `Synchronized` 而言都是独占锁
- 共享锁：指该锁可以被多个线程持有

为什么会有读锁和写锁？

原理我们使用 `ReentrantLock` 和 `Synchronized` 创建锁的时候，均是独占锁，也就是说一次只能一个线程访问，但是如果我们的业务有一个读写分离场景，读的时候可以让多人同时进行，这样的话原先的独占锁并发性就显得比较差了，而我们读业务并不会造成数据不一致问题，所以应该建议多人可以一起读

```java
public class CacheTest {
        private volatile Map<String,Object> map = new HashMap<String,Object>();
        public void put(String key, Object value) {
            System.out.println(Thread.currentThread().getName() + " 正在写入 ");
            try {
                sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + " 写入完成");
        }
        public Object get(String key) {
            System.out.println(Thread.currentThread().getName() + " 开始读取");
            try {
                sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " 读取完毕");
            return map.get(key);
        }
}
class ReaderWriterLock {
    public static void main(String[] args) {
        CacheTest ct = new CacheTest();
        // 模拟多线程同时进行读写
        for (int i = 1;i < 5;i++) {
            // 当存在大量线程的情况下，启动线程的时间很短，启动了不代表线程执行完结束了
            // 因此线程本身生命周期还没有结束，而 for 循环定义的临时变量生命周期可能结束了
            // 当线程里面使用这个变量的时候再来访问就会出错
            int finalI = i;
            // 这样 final 修饰更好，相当于加了锁，用起来更加安全，本质防止多核 cpu 共同操作该线程锁引发的不安全性
            new Thread() {
                @Override
                public void run() {
                    ct.put(finalI + "",finalI + "");
                }
            }.start();
        }
        for (int i = 0; i <5;i++) {
            int finalI = i;
            new Thread() {
                @Override
                public void run() {
                    ct.get(finalI +"");
                }
            }.start();
        }
    }
}
```

输出：

```
Thread-1 正在写入 
Thread-3 正在写入 
Thread-2 正在写入 
Thread-0 正在写入 
Thread-5 开始读取
Thread-8 开始读取
Thread-4 开始读取
Thread-7 开始读取
Thread-6 开始读取
Thread-1 写入完成
Thread-3 写入完成
Thread-7 读取完毕
Thread-8 读取完毕
Thread-5 读取完毕
Thread-4 读取完毕
Thread-0 写入完成
Thread-6 读取完毕
Thread-2 写入完成
```

从得到的结果来看，写入的时候，写操作可以被其他线程打断，这就造成了写操作还没完，读操作又开始了，数据量一旦变大很有可能造成读取 null 值的现象

解决方案：为上述代码添加读写锁（`ReentrantReadWriteLock`）

- `ReentrantReadWriteLock lock = new ReentrantReadWriteLock();` ：创建读写锁
- `lock.writeLock().lock();` ：创建一个写锁
- `lock.writeLock().unlock();` ：释放写锁
- `lock.readLock().lock();` ：创建一个读锁
- `lock.readLock().unlock();` ：释放锁写

读写锁：多个线程同时读一个资源类是没有问题的，所以为了满足并发量，读取共享资源应该同时进行；但是如果一个线程想要去写共享资源，就不应该再有其他线程可以对该资源进行读或写

```java
public class CacheTest {
        private volatile Map<String,Object> map = new HashMap<String,Object>();
        ReentrantReadWriteLock rwLock = new ReentrantReadWriteLock();
        public void put(String key, Object value) {
            rwLock.writeLock().lock();
            try {
                System.out.println(Thread.currentThread().getName() + " 正在写入 ");
                sleep(500);
                map.put(key, value);
                System.out.println(Thread.currentThread().getName() + " 写入完成");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                rwLock.writeLock().unlock();
            }
        }
        public Object get(String key) {
            rwLock.readLock().lock();
            try {
                System.out.println(Thread.currentThread().getName() + " 开始读取");
                sleep(500);
                System.out.println(Thread.currentThread().getName() + " 读取完毕");
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                rwLock.readLock().unlock();
            }
            return map.get(key);
        }
}
```

输出：

```
Thread-2 正在写入 
Thread-2 写入完成
Thread-3 正在写入 
Thread-3 写入完成
Thread-1 正在写入 
Thread-1 写入完成
Thread-0 正在写入 
Thread-0 写入完成
Thread-6 开始读取
Thread-8 开始读取
Thread-4 开始读取
Thread-5 开始读取
Thread-7 开始读取
Thread-6 读取完毕
Thread-8 读取完毕
Thread-7 读取完毕
Thread-5 读取完毕
Thread-4 读取完毕
```

从运行结构我们看出，写入操作是一个一个的线程进行执行的，并且中间不会被打断，而读操作的时候是 5 个线程同时进行的，然后并发读取

线程进入读锁的前提条件：

1. 没有进入线程的写锁
2. 没有写请求或有写请求，但调用线程和持有锁的线程是同一个

线程进入写锁的前提条件：

1. 没有其他线程的读锁
2. 没有其他线程的写锁

#### 高并发



#### `TCP`通信

`Socket` 简介

`socket` 通常称作 “ 套接字 ” ，用于描述 `IP` 地址和端口，是一个通信链句柄；在 `Internet` 上的主机一般运行了多个服务软件，同时提供几种服务，每种服务都打开了一个 `socket`，并绑定了一个端口上，不同的端口对应不同的服务

应用程序通常通过 “ 套接字 ” 向网络发送请求，或者应答网络请求，`Socket` 和 `ServerSocket` 类位于 `java.net` 包中，`ServerSocket` 用于服务端，`Socket` 是建立网络连接时使用的，在连接成功时，应用程序两端都会产生一个 `Socket` 示例，操作这个示例，完成所需要的会话

`java.net.Socket` 为套接字类，其提供了很多方法：

- `int  getLocalPort()` ：获取本地使用的端口号
- `InetAddress  getLocalAddress()` ：获取套接字绑定的本地地址
- 使用 `InetAddress` 表示本机地址
  - `String  getCanonicalHostName()` ：获取此 `IP` 地址的完全限定域名
  - `String  getHostAddress()` ：返回 `IP` 地址字符串（以文本表现形式）

- 通过 Socket 获取远端的地址以及端口号
  - `int  getPost()` ：获取远端使用的端口号
  - `InetAddress  getIntAddress()` ：获取套接字绑定的远端地址

<img src=".\note_imgs\tcp01.png" style="zoom: 67%;" />
