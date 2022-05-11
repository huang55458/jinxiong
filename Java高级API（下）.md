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
