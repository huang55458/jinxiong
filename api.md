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
   5. 正则
      - `boolean  matches(String regexp)` ：将一个字符串与正则表达式进行匹配，匹配成功返回 `true`
      - `String[]  split(Strinig regexp)` ：将字符串按照正则表达式拆分成字符串数组
      - `String  replaceAll(String regexp,String replacement)` ：将字符串中匹配正则表达式 `regxep` 的字符串替换成 `replacement` 

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
   - `Integer.pareInt()` ：将字符串转为 int 类型

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

#### `Collections`

1. 排序操作
   - `static  void  reverse(List list)` ：反转
   - `static  shuffle(List list)` ：随机排序
   - `static  void  sort(List list)` ：按自然排序的升序排序
   - `static  void  sort(List list,Comparor c)` ：定制排序，由 `Comparator` 控制排序逻辑
   - `static  void  swap(List list,int i,int j)` ：交换两个索引位置的元素
   - `static  void  rotate(List list,int distance)` ：旋转，当 `distance` 为正数时，将 `list` 后 `distance` 个元素整体移到前面；当 `distance` 为负数时，将 `list` 的前 `distance` 个元素整体移到后面
2. 查找、替换操作
   - `static  int  binarySearch(List list,Object key)` ：对 `List` 进行二分查找，返回索引，注意 `List` 必须是有序的
   - `static  int  max(Collection coll)` ：根据元素的自然顺序，返回最大的元素
   - `static  int  min(Collection coll)` 
   - `static  int  max(Collection coll,Comparator c)` ：根据定制排序，返回最大元素，排序规则由 `Comparator` 类控制
   - `static  int  min(Collection coll,Comparator c)` 
   - `void  fill(List list,Object obj)` ：用指定的元素代替指定 `List` 中的所有元素
   - `int  frequency(Collection c,Object o)` ：统计元素出现次数
   - `int  indexOfSubList(List list,List target)` ：统计 `target` 在 `List` 中第一次出现的索引，找不到则返回 `-1` 
   - `int  lastIndexOfSubList(List list,List target)` 
   - `boolean  replaceAll(List list,Object oldVal,Object newVal)` ：用新的元素替换旧的元素
3. 同步控制（不推荐，效率低，需要线程安全的集合类型考虑使用 JUC 包下的并发集合）
   - `synchronizedCollection(Collection<T> c)` ：返回指定 `collection` 支持的同步（线程安全的）`collection`
   - `synchronizedList(List<T> list)` ：返回指定列表支持的同步（线程安全的）`List`
   - `synchronizedMap(Map<K,V> m)` ：返回指定映射支持的同步（线程安全的）`Map`
   - `synchronizedSet(Set<T> s)` ：返回指定映射 `Set` 的同步（线程安全的）`Set`
