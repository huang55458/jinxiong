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

##### 基本 `IO`

我们编写的程序除了自身会定义一些数据信息外，经常还会引用外界的数据，或是将自身的数据发送到外界，比如：我们编写的程序想要读取一个文本文件，又或是我们想要将程序中的某些数据写入到一个文件中，这时我们就要使用输入与输出

- 输入：一个从外界进入到程序的方向，通常我们需要 “ 读取 ” 外界的数据时，使用输入，所以输入是用于**读取数据**的
- 输出：一个从程序发送到外界的方向，通常我们需要 “ 写出 ” 数据到外界时，使用输出，所以输出是用来**写出数据**的

按照流是否直接与特定的地方（如磁盘、内存、设备等）相连，分为节点流和处理流两类；处理流的构造方法总是要带一个其他的流对象做参数，一个流对象经过其他流的多次包装，称为流的连接，

- 节点流：也称低级流，可以从或向一个特定的地方（节点）读取数据
- 处理流：也称高级流或过滤流，是对一个已经存在的流的连接和封装，通过所封装的流的功能调用实现数据读写

**`IS` 和 `OS` 常用方法**

`Inputstream` 是所有字节输入流的父类，其定义了基础的读取方法：

- `int  read()` ：读取一个字节，以 `int` 形式返回，为 `int` 值的 “ 低 8 位 ” ，若返回 -1，则表示 `EOF`（`end of file`）
- `int  read(byte[] b)` ：尝试最多读取给定数组的 `length` 个字节并存入数组，返回值为实际读取到的字节量

`OutputStream` 是所有字节输出流的父类，其定义了基础的写出方法：

- `void  write(int b)` ：写出一个字节，写出的是给定的 `int` 的 “ 低 8 位 ”
- `void  write(byte[] b)` ：将给定的字节数组中的所有字节全部写出

**文件流**

`FileOutputStream` 是文件的字节输出流，使用该流可以以字节为单位将数据写入文件；若指定的文件已经包含内容，那么当使用 `FileOutputStream` 对其写入数据时，会将该文件中原有的数据全部清除

1. 构造方法

   - `FileOutputStream(File file)` ：创建一个向指定 `File` 对象表示的文件中写出数据的文件输出流

   - `FileOutputStream(String filename)` ：创建一个向具有指定名称的文件中写出数据的文件输出流

     上面两种构造方法创建的对象对文件进行写操作时会覆盖文件中的原有数据，若想在文件的原有数据之后追加新数据则需要使用以下构造方法：

   - `FileOutputStream(File file,boolean append)` ：创建一个向指定 `File` 对象表示的文件中写出数据的文件输出流，`append` 为 `true` 时，写入数据将在文件末尾追加

   - `FileOutputStream(String filename,boolean append)` ：创建一个向具有指定名称的文件中写出数据的文件输出流，`append` 为 `true` 时，写入数据将在文件末尾追加

2. 常用方法
   - `int  read()` ：从输入流中读取一个字节，若返回 -1则表示文件结尾（`EOF`）
   - `int  read(byte[] b)` ：从此输入流中将最多 `b.length` 个字节的数据读入到字节数组 `b` 中

```java
FileOutputStream fos = new FileOutputStream("Hello.txt",true);
fos.write("Hello".getBytes());
```

`FileInputStream` 是文件的字节输入流，使用该流可以以字节为单位将数据读取文件

1. 构造方法
   - `FileInputStream (File file)` ：创建一个向指定 `File` 对象表示的文件中写出数据的文件输入流
   - `FileOutputStream(String filename)` ：创建一个向具有指定名称的文件中写出数据的文件输入流
2. 常用方法
   - `void  write(int d)` ：将指定字节输出流，这里只写给定 `int` 的 “ 低 8 位 ”
   - `void  write(byte[] b)` ：将 `b.length` 个字节从指定 `byte` 数组写出此文件输入流中
   - `void  write(byte[] b,int offset,int len)` ：将指定 `byte` 数组中从偏移量 `offset` 开始的 `len` 个字节写入此文件输入流

