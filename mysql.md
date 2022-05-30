#### 安装

###### 基本命令操作

`sc （service console）` ：`sc start mysql`、`...`

`net` ：`net satrt mysql`、`...`

`gpedit.msc` ：组策略

`services.msc` ：服务

一些基本使用命令：

`select nows();` 、 `select current_time;`

- `show databases;`

- `create database java220201;`
- `use java220201;`

<img src="E:\github\note_imgs\SQL_01.png" style="zoom:80%;" />

#### 使用

SQL 语言：用来操作数据库中的数据的

mysql 数据库里面可以创建很多数据库：众多数据库对象（表）的集合

mysql 数据库常用命令（SQL 语言操作数据的常用命令 + mysql 数据库提供的命令）

- `create  database  数据库名;` ：创建数据库
- `drop  database  数据库名;` ：删除数据库
- `use  数据库名;` ：使用指定的数据库
- `show  databases;` ：显示所有的数据库
- `create  table  表名(字段名 属性,字段名 属性,...);` ：创建表
- `show  tables;` ：显示数据库中的所有的表
- `desc/describe  表名;` ：查看表结构
- `alter  table  表名  rename  to  new_tableName;` ：重命名表名
- `alter  table  表名  add  column  列名  属性;` ：增加列
- `insert  into  表名(字段1,字段2,...)  values(value1,value2,...);` ：添加表数据
- `insert  into  表名  values(value1,value2,...);` ：添加表数据
- `update  表名  set salary = 3200 where gender = '女';` ：修改数据
- `select database(),user(),now(),version();`  ：查看当前数据库、用户、时间、数据库版本

关于乱码

1. `show variables like '%char%';` ：`char` 类型的系统变量
   - `set   character_set_database  =  utf8mb4;` 
   - `set   character_set_server  =  utf8mb4;` ：设置字符集
2. 如果上述不能成功则单独修改表和表字段字符集
   - `alter  table  表名  charset = utf8mb4;`
   - `alter  table  表名  modify  字段名  varchar(20)  character  set  utf8  collate  utf8_general_ci;`

- `alter database java220201 character set utf8mb4;` 

###### 查询数据表命令

- `select  *  from  表名;` ：全字段 + 全表扫描
- `select  字段1,字段2,...  from  表名;` ：投影查询
  - `select  id i,name n  from  表名;` ：别名
- `select  字段1,字段2,...  from  表名  where  条件;`
  - `< , > , <= , >= , != , <>(相当于不等于) , is null , ...` ：能使用的运算符
    - `select * from students where name is null;`
    - `select * from students where name is not null;` 
    - `select * from students where name = '';` 
  -  `in , not in , like , not like , between ... and , not  between ... and , regexp` ：关键字
    - `select * from students where id in (2,3,4);` ：判断某个字段的值是否在指定的集合中
    - `select * from students where id between 3 and 6;` 等同于  `select * from students where id >= 3 and id <= 6;` 
    - `select * from students where name like '%云%';` ：模糊查询
      -  `%` ：表示任意 0 个或多个字符，可匹配任意类型和长度的字符；有些情况下若是中文，使用两个百分号（%%）表示
      -  `_` ：表示任意单个字符，匹配单个任意字符；常用来限制表达式的字符长度语句
      -  `[]` ：表示括号内所列字符中的一个（类似正则表达式），指定一个字符、字符串或范围，要求所匹配对象为它们中的任一个
      -  `[^]` ：表示不在括号所列之内的单个字符
      -  查询内容包含通配符时,由于通配符的缘故，导致我们查询特殊字符 `"%"、"_"、"["` 的语句无法正常实现，而把特殊字符用 `"[ ]"` 括起便可正常查询
      -  使用：
         - `'%a'` ：以 a 结尾的数据
         - `'%a%'` ：含有 a 的数据
         - `_a_` ：三位且中间字母是 a 的数据
         - `'a_'` ：两位且开头是 a 的数据
  - `distinct , and , or` ：特殊关键字
    - `and` ：可以用来联合多个条件进行查询，只有同时满足所有查询条件的记录会被查询出来
    - `or` ：也可以用来联合多个条件进行查询，只有满足查询条件中的一个，那么此记录就会被查新出来
    - `distinct` ：去除查询结果中重复记录
      -  `select distinct gender from students;`
- `select  字段1,字段2,...  from  表名  where  条件  order by  字段名  desc/asc(默认、升序);` 
  - `select * from students where age = 18 order by id desc;` 
  - `select * from students where age = 18 order by id desc,name;` ：先根据 id 进行降序排序，id 相同时，再根据 name 升序排序



分组查询：通过关键字 `group  by` 可以将数据划分到不同的组中，实现对记录进行分组查询；在查询时，所查询的列必须包含在分组的类中，目的是使查询的数据没有矛盾

不完全兼容旧版时：`SET GLOBAL sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));`

1. 单独使用 `group  by` 查询结果只显示每组一条记录
   - `select gender from students group by gender;` 
2. 使用关键字 `group  by` 和 `group_concat()` 函数查询，可以将每个组中的所有字段都显示出来
   -  `select group_concat(gender) from students group by gender;`
3. 按多个字段进行分组
   -  `select id,group_concat(gender) from students group by id,gender;` 先按照 id 进行分组，然后对剩下的数据再按照 gender 进行分组

分组函数实际上最多的应用适合聚集函数（ `count() , max() , min() , avg() , sum()` ）配合使用

- `count()` ：查询记录总数
- `avg()` ：平均值
- `sum()` ：总和



用关键字 limit 限制查询结果的数据

查询数据时，可能会查询出很多的记录，而用于需要的记录可能只是很少的一部分，这样就需要限制查询结果的数量；关键字 limit 可以对查询结果的记录条数进行限定，控制它输出的行数

-  `select * from students order by id limit 3;`
