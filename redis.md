

- ```
  SCAN cursor [MATCH pattern] [COUNT count]
  ```

  第一次使用 cursor 设为 0，SCAN 返回一个包含两个元素的数组， 第一个元素是用于进行下一次迭代的新游标， 而第二个元素则是一个数组， 这个数组中包含了所有被迭代的元素。如果新游标返回 0 表示迭代已结束



- 过期时间为 1 分钟，1分钟后该键会自动删除

  ```
  EXPIRE runooobkey 60
  ```

- ```
  GETRANGE KEY_NAME start end   	截取字符串
  STRLEN mykey		获取字符串的长度, 不存在的 key 长度为 0
  incr key 增
  decr key 减
  ```

  Hash

  ```
  HSET myhash field1 "foo"
  HGET myhash field1
  HEXISTS myhash field1
  HKEYS myhash  : 哈希表中所有域（field）列表,当 key 不存在时，返回一个空列表
  HMGET myhash field1 field2 nofield : 显示 value 值
  HSETNX myhash field1 "bar" ：如果 fileld1 值存在，则不会覆盖
  HVALS myhash ： 显示所有 value
  HSCAN key cursor [MATCH pattern] [COUNT count] ：
  	pattern ： 匹配的模式 ("run*")
  	count ： 默认 10
  ```




#### 安装



安装过程：

1. 下载解压
2. make 编译
3. make install 将命令移至 /usr/local



- `daemonize:yes` : redis采用的是单进程多线程的模式。当redis.conf中选项daemonize设置成yes时，代表开启守护进程模式。在该模式下，redis会在后台运行，并将进程pid号写入至redis.conf选项pidfile设置的文件中，此时redis将一直运行，除非手动kill该进程

- `Redis-benchmark`  官方自带的性能测试工具

redis 非关系型数据库

数据之间没有关系，以独立的形式存在



###### 配置

```
CONFIG GET *   ：使用 * 号获取所有配置项
CONFIG GET loglevel		：获取一个配置
CONFIG SET loglevel "notice"   	：	更改一个配置
```

[具体配置信息](https://www.runoob.com/redis/redis-conf.html)

- `databases 16`  设置数据库的数量，默认数据库为0，可以使用SELECT 命令在连接上指定数据库id







###### springboot + Lettuce



- ```
  "\"test\""
  ```

  String 类型需要使用如上格式，否则java 不能读取











#### 数据类型



###### String

字符串，Redis 最基本的类型，一个类型 key 对应一个 value；String 是二进制安全的，指String 可以包含任何数据类型，比如图片，音视频或者序列化对象，一个 redis 字符串 value 最多可以是 512 M

- keys pattern ：查看 key
- exists key ：判断 key 是否存在
- move key db ：将当前库 中的key 剪切至其他库
- expire key ：为key 设置过期时间，单位 秒，数据过期将从数据库中删除
- ttl key ：查看 key 还有多久过期（-1 ：永不过期，-2：已过期）
- type key ：查看 key 的类型
- del key ：删除 key
- append  key value：向已经存在的 key 追加内容
- strlen key ：查看 value 长度
- incr key ：增加 value 值
- decr key ：value - 1
- incrby key increment ：给 value 加上 给定的值
- decrby key decrement ：
- getrange key start end ：截取指定长度的字符串（getrange key 0 -1 ：获取全部）
- setrange key offset value ：从指定位置覆盖值
- setex key seconds value ：设置过期时间，expire 是设置已经存在的key
- setnx key value ：如果 key 已存在则不会覆盖
- mget key key key ：取出多个值
- mset key value key value ：一次设置多个值





###### Hash

哈希，是一个键值对集合；Redis hash 是一个 String 类型的 field 和 value 的映射表，hash 特殊适合存储对象，类似于 java 中的 Map<Strting,Map>

- hset key k1 v1 k2 v2 ：
- hget key k1 ：



###### List

列表，简单的字符串列表，按照插入顺序排序，可以添加一个元素到列表的头部或尾部，底层是一个链表

- lpush key value value ：插入数据，先进后出
- rpush key value value ：插入数据，先进先出
- lrange key start stop ：查看（lrange key 0 -1：查看key所有值）
- lpop key count：从栈顶取出
- rpop key count：从栈底取出
- lindex key index：按照索引下标获得元素（从上到下），下标从 0 开始
- rpoplpush  list1 list2：将list 的最后一个元素剪切至list2第一个元素



###### Set

集合，String类型 的无序、不可重复集合，是通过 HashTable(线程安全的 HashMap) 实现的

- sadd key e e e ：
- scard ：获取集合里面的元素个数



###### Zset

有序集合，和 set 一样是 String类型元素的集合，不允许重复的成员，不同的是每个元素都会关联一个 double 类型的分数（score），redis 正是通过分数时集合中的成员进行从小到大的排序，zset 的成员是唯一的，但是分数是可以重复的

- zadd key ---- ：
- zrange key 0 -1 ：







#### 主从复制

主机数据更新后根据配置和策略，自动同步到备机的 

- 读写分离
- 容灾恢复



配从不配主（只配从机，不配置主机）

- info replication
- slaveof ip port ：做小弟
- flushdb ：清空key



主机状态：

```sh
127.0.0.1:6380> info replication
# Replication
role:master
connected_slaves:2
slave0:ip=127.0.0.1,port=6381,state=online,offset=10373,lag=1
slave1:ip=127.0.0.1,port=6382,state=online,offset=10373,lag=1
```

- 如果主机宕机了，从机默认情况下是原地待命，不会立即选出主机
- 主机再次恢复，依旧维持之前的从属关系



哨兵模式：主机宕机，从机接手

1. 修改哨兵配置文件 sentinel.conf端口号
2. `sentinel monitor host6380 127.0.0.1 6380 1`
3. `redis-sentinel ./sentinel.conf`
4. 6380客户端 shutdown 关闭服务
5. 输出 `switch-master mymaster 127.0.0.1 6380 127.0.0.1 6382` 
6. 6382 主机，80、81从机
