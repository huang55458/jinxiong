#### `String`

1. 构造方法：
   - `public  String()` ：创建 `String` 对象
   - `public  String(String original)` ：把字符串转成字符串
   - `public  String(char[] value)` ：把字符数组转成字符串
   - `public  String(char[] value,int index,int count)` ：把字符数组的一部分转成字符串
   - `public  String(byte[] )` ：把字符串转成字符串
1. 常用功能：
   1. 判断功能
      - `boolean  equals(Object obj)` ：比较字符串的内容是否相同，严格区分大小写
      - `boolean  equalsIgnoreCase(String str)` ：比较字符串的内容是否相同，不考虑大小写
      - `boolean  contains(String str)` ：判断是否包含指定的字符串
      - `boolean  startsWith(String str)` ：判断是否以指定的字符串开头
      - `boolean  endsWith(String str)` ：判断是否以指定的字符串结尾
      - `boolean  isEmpty()` ：判断字符串的内容是否为空
   2. 获取功能
      - `int  length()` ：返回字符串的长度
      - `char  charAt(int index)` ：返回字符串指定位置的字符
      - `int  indexOf(int char)` ：返回指定字符在字符串中第一次出现的位置    `97 = 'a'`
      - `int  indexOf(String str)` ：返回指定字符串在字符串中第一次出现的位置
      - `int  indexOf(int char,int fromindex)` ：返回指定字符从指定位置开始在字符串中第一次出现的位置
      - `int  indexOf(String str,int fromindex)` ：返回指定字符串从指定位置开始在字符串中第一次出现的位置
      - `String  substring(int start)` ：返回从指定位置开始到末尾的字串
      - `String  substring(int start,int end)` ：返回从指定位置开始到指定位置结束的字串
   3. 转换功能
      - `byte[]  getBytes()` ：把字符串转换为字节数组
      - `char[]  toCharArray()` ：把字符串转换为字符数组
      - `static  String  valueOf(char[] chs)` ：把字符数组转换成字符串
      - `static  String  valueOf(int i)` ：把 int 类型的数据转换成字符串 （该方法能把任意类型转换成字符串）
      - `String  toLowerCase()` ：把字符串转小写
      - `String  toUpperCase()` ：把字符串转大写
      - `String  concat(String str)` ：字符串的连接
   4. 其他功能
      - 替换功能
        - `String  replace(char old,char new)` 
        - `String  replace(String old,String new)`
      - 去除字符串两边空格
        - `String  trim()`
      - 按字典顺序比较两个字符串
        - `int  compareTo(String str)`
        - `int compareToIgnoreCase(String str)`

#### `StringBuffer`

1. 构造方法：
   - `StringBuffer  str = new  StringBuffer();`
   - `StringBuffer  str = new  StringBuffer(97);`
   - `StringBuffer  str = new  StringBuffer("hello");`
2. 成员方法：
   - 添加功能
     - `public  StringBuffer  append(String str)` ：追加字符串
     - `public  StringBuffer  insert(int offset,String str)` ：在指定位置插入字符串
   - 删除功能
     - `public  StringBuffer  deleteCharAt(int index)` ：删除指定位置字符串
     - `public  StringBuffer  delete(int start,int end)` ：删除从指定开始位置到结束位置的字符串
   - 替换功能
     - `public  StringBuffer  replace(int start,int end,String str)` ：以指定字符串替代从指定开始位置到结束位置的字符串
   - 反转功能
     - `public  StringBuffer  reverse()` ：反转
   - 截取功能
     - `public  String  substring(int start)` ：从指定索引到末尾的字符串
     - `public  String  substring(int start,int end)` ：从指定索引开始到指定索引结束的字符串

#### `Arrays`

1. 成员方法
   - `public  static  String  toString()` ：将任意类型数组转换成字符串
   - `public  static  void  sort()` ：排序
   - `public  static  int  binarySearch(int[] arr,int key)` ：二分查找

#### `Integer`

1. 成员方法
   - Integer.pareInt() ：将字符串转为 int 类型

#### `Character`

1. 成员方法
   - `public  boolean  isUpperCase(char char)` ：判断字符是否为大写字母
   - `public  boolean  isLowerCase(char char)` ：判断字符是否为小写字母
   - `public  boolean  isDigit(char char)` ：判断字符是否为数字字符
   - `public  char  toUpperCase(char char)` ：把字符转为大写
   - `public  char  toLowerCase(char char)` ：把字符转为小写

#### `Math`

1. 常见功能
   - `public  static  int  max(int a,int b)`：最大值
   - `public  static  double  randow()` ：返回一个 [0,1) 的 double 类型值
   - `public  static  double  pow(double a,double b)` ：a 的 b 次方
   - `public  static  int  abs(int a)` ：绝对值
   - `public  static  double  ceil(double a)` ：向上取整
   - `public  static  double  floor(double a)` ：向下取整
   - `public  static  int  round(double a)` ：四舍五入
   - `public  static  double  sqrt(double a)` ：平方根

#### `Random`

1. 成员方法
   - `public  int nextInt()` ：int 范围内的数据
   
   - `public  int  nextInt(int n)` ：[0,n) 范围内的数据
   
     > 产生 1-100 之间的随机数
     >
     > `Random  r  =  new  Random();`
     >
     > `int  number = r.nextInt(100) + 1;`

#### `System`

1. 成员方法
   - `public  static  void  gc()` ：运行垃圾收集器
   - `public  static  void  exit(int status)` ：终止当前运行的 Java 虚拟机
   - `public  static  long  currentTimeMillis()` ：以毫秒为单位返回当前时间
   - `public  static  void  arraycopy(Object src,int srcPos,Object dest,int destPos,int length)` ：将指定源数组中的数组从指定位置开始复制到目标数组的指定位置
