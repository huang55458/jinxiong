[TOC]



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

###### `SQL Select`的执行顺序

1、from子句组装来自不同数据源的数据； （先join在on）

2、where子句基于指定的条件对记录行进行筛选；

3、group by子句将数据划分为多个分组；

4、使用聚集函数进行计算；

5、使用having子句筛选分组；

6、计算所有的表达式；

7、select 的字段；

8、使用order by对结果集进行排序

SQL 语言不同于其他编程语言的最明显特征是处理代码的顺序。在大多数据库语言中，代码按编码顺序被处理。但在SQL语句中，第一个被处理的子句是 FROM，而不是第一出现的SELECT；SQL查询处理的步骤序号：

(1) FROM <left_table>

(2) <join_type> JOIN <right_table>

(3) ON <join_condition>

(4) WHERE <where_condition>

(5) GROUP BY <group_by_list>

(6) WITH {CUBE | ROLLUP}

(7) HAVING <having_condition>

(8) SELECT

(9) DISTINCT

(9) ORDER BY <order_by_list>

以上每个步骤都会产生一个虚拟表，该虚拟表被用作下一个步骤的输入；这些虚拟表对调用者(客户端应用程序或者外部查询)不可用。只有最后一步生成的表才会会给调用者；如果没有在查询中指定某一个子句，将跳过相应的步骤

###### `having` 用法

- having 子句与 group by 子句一起使用，不一起使用将没有意义，因为使用条件限制
- 使用条件
  - 常数
  - 聚合函数
  - `group by` 指定的列名

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



分组查询：通过关键字 `group  by` 可以将数据划分到不同的组中，实现对记录进行分组查询；在查询时，所查询的列必须包含在分组的类中，目的是使查询的数据没有矛盾；使用分组函数时。select 后面只能跟两种字段（分组字段、聚合函数）

解除只能跟两种字段限制：`SET GLOBAL sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));`

1. 单独使用 `group  by` 查询结果只显示每组一条记录
   - `select gender from students group by gender;` 
2. 使用关键字 `group  by` 和 `group_concat()` 函数查询，可以将每个组中的所有字段都显示出来
   -  `select group_concat(gender) from students group by gender;`
3. 按多个字段进行分组
   -  `select id,group_concat(gender) from students group by id,gender;` 先按照 id 进行分组，然后对剩下的数据再按照 gender 进行分组

分组函数实际上最多的应用适合聚集函数（ `count() , max() , min() , avg() , sum()` ）配合使用 （用于 having 过滤）

- `count()` ：查询记录总数
- `avg()` ：平均值
- `sum()` ：总和



用关键字 limit 限制查询结果的数据

查询数据时，可能会查询出很多的记录，而用于需要的记录可能只是很少的一部分，这样就需要限制查询结果的数量；关键字 limit 可以对查询结果的记录条数进行限定，控制它输出的行数

-  `select * from students order by id limit 3;`

使用关键字 limit 可以从查询结果的中间部分取值

两个参数

- 参数 1 是开始读取的第一条记录的编号（在查询结果中，第一个结果的记录编号是 0 ，而不是 1 ）
- 参数 2 是要查询记录的个数

- `select * from employess limit 5,2;` ：第六行、第七行数据



- `alter table employess modify column id int primary key auto_increment;` ：修改主键



- `length` 查看字段所占字节大小

 `select length(字段名),length(字段名) from 表名;`

- `char_length` 查看字段字符长度

 `select char_length(字段名),char_length(字段名) from 表名;`





1. 连接查询：连接是把不同表的记录连接到一起进行查询，这种连接语句可以以多种高级方法来组合表记录

   - 内连接查询：最普遍的连接类型，它们要求构成连接的每一部分的每个表的匹配，不匹配的行将被排除（内连接最常见例子就是相等连接，也就是连接后的表中的某个字段与每个表中的相同，这种情况下，最后的结果只包含参加连接的表中与指定字段相等的行）

     - ```mysql
       select e.name,e.department_id,d.department_name 
       from employess e,departments d 
       where e.department_id = d.department_id;
       ```

   - 外连接查询：与内连接不同，外连接是指使用 outer join 关键字将两个表连接起来，外连接生成的结果集不仅包含符合连接条件的行数据，而且还包含左表或者右表或者两个表中不符合连接条件的行数据

     - 左外连接：将左表中的所有数据分别与右表中的每条数据进行连接组合，返回的结果除了内连接的数据外，还包括左表中不符合条件的数据，并在右表的相应列中添加 null 值

       - ```mysql
         select e.name,e.department_id,d.department_name  
         from employess e left join departments d  
         on e.department_id = d.department_id; 
         ```

     - 右外连接：......    （`left join -->  right join`）

     - 全外连接：......

   - 复合条件连接查询：在连接查询时，也可以增加其他的限制条件

     - 

2. 子查询：查询语句中可以嵌套多个查询，在外面一层的查询使用里面一层查询产生的结果集；从代码量，逻辑简易度以及时间复杂度来看，子查询更加具备优势；如果内层子查询语句的执行结果为空值，那么外层的 where 子句就始终不会满足条件，这样的查询最后就必然是空值

   - `select * from employess where department_id in (select department_id from departments);` 

   - ```mysql
     select * from employess 
     where salary != (select min(salary) from employess) and salary != (select max(salary) from employess);
     ```

     多层查询：查询语句中可以嵌套多个查询，在外面一层的查询使用里面一层查询产生的结果集；当遇到这样的多层查询时，MySQL 从最内层的查询开始，然后从它开始向上移动到外层查询（主查询），这个过程每个查询产生的结果集都被赋给包围它的父查询，接着这个父查询被执行

     - 子查询中使用（any，all）关键字

       1. any 关键字必须与单行操作符（> , < , ... ）结合使用，并且返回行只要匹配自查的任何一个结果即可（只要满足内层查询语句返回的结果中任意一个，就可以通过该条件执行外层查询语句）

          ```mysql
          select * from (select * from employess where department_id != 80) as test  
          where salary > any(select salary from employess where department_id = 80);
          ```

       2. all 关键字满足所有条件，使用 all 时，只有满足内层查询语句返回的所有结果，才可以执行外层循环

          ```mysql
           select * from employess 
           where salary > all(select salary from employess where department_id = 80);
          ```

       3. exists 关键字：使用exists 关键字，内层查询语句不返回查询的记录，而是返回一个真假值，如果内层查询语句查询到满足条件的记录，就返回一个真值（true），否则将返回一个假值（false）；当返回 true 时，外层查询语句将执行，否则外层不执行；只做判断

          ```mysql
          select * from employess 
          where exists(select * from departments where department_id = 222);
          ```

          ```mysql
          select * from employess 
          where department_id = 111 and exists(select * from departments where department_id = 222);
          ```

       4. 合并查询结果：合并查询结果是将多个 select 语句的查询结果合并在一起，使用关键字 union 和 union all 

          - union ：将所有的查询结果合并在一起，然后去除相同记录

            ```mysql
             select name  from employess 
             union 
             select department_name from departments ;
            ```

          - union all ：简单的合并在一起

            ```mysql
             select name  from employess 
             union all
             select department_name from departments ;
            ```

3. 关联子查询：

   1. 在单行子查询和多行子查询中，内查询和外查询是分开执行的，也就是说：内查询的执行与外查询的执行是没有关系的，外查询仅仅是使用内查询的最终结果

   2. 在一些特殊需求的子查询中，内查询的执行需要借助外查询，而外查询的执行又离不开内查询，这时，内查询和外查询是相互关联，这种子查询称为关联子查询

      ```mysql
      select * from employess e 
      where salary > (select avg(salary) from employess where e.job_id = employess.job_id);
      ```

自连接

在应用系统开发中，用户可能会拥有 “ 自引用式 ” 的外键， “ 自引用式 ” 的外键是指表中的一个列可以是该表中主键的一个外键

```mysql
select emp2.name 上层管理者,emp1.name 下属员工 
from employess emp1 join employess emp2 
on emp1.manager_id = emp2.id;
```

 

- `select * from employess where id not in (23,4,null);` ：结果为 `Empty set`，not in 与 null 不能一起出现

- `select now() from dual;`：`dual` 临时表

- `select concat(name,',',password) 账户 from users;`

- `select datediff('2222-2-2','3333-3-3');` ：`datediff(d1,d2)` ，计算两个日期之间相隔的天数，为 d1 - d2

- `select adddate('2222-2-2',405814);`  ：`adddate(d,n)` ，返回日期 d 加上 n 天后的日期，n 可以为负 （`subdate(d,n)` ：日期 d 减去 n 天）

- `select datediff(sysdate(),hire_date)/365 year,datediff(sysdate(),hire_date) from employess order by year;` ：`hire_date` ：入职日期

- `select * from employess where hire_date > '2020-05-01';` ：日期可以直接和字符串进行比较

- `select name,hire_date from employess where to_days(now()) - to_days(hire_date) > 100;` ：`to_days(d)` ：返回 d 至 0000 年 1 月 1 日 的天数

- `select concat(name,'想',truncate(salary*3,0),'每月') 梦想 from employess;` ：`truncate(num,p)` ：对 num 保留 p 位小数

- 关联子查询

  ```mysql
  select d.* ,(select avg(salary) from employees where department_id = d.department_id ) min_avg_sal
  from departments d
  where department_id = (
  		select department_id 
  		from employees
  		group by department_id 
  		having avg(salary) = (
  			select min(dept_avgsal)
  			from (
  			select avg(salary) dept_avgsal
  			from employees
  			group by department_id
  			) avg_sal
  		)
  	);
  ```

  

###### 约束

- 主键约束：非空且唯一，能够唯一确定表中的记录 `primary key`
- 非空约束：`not null`
- 唯一约束：`unique`

- 外键约束：添加外键约束后，外键所在表为子表，外键关联表为父表，父表中外键字段不能随意删除，修改（添加外键约束时，父表中的关联字段应该非空且唯一）

  - `alter table employess add foreign key (department_id) references departments(department_id);` 修改字段为外键约束

    `alter table employess add constraint name foreign key (department_id) references departments(department_id);`  为外键约束命名（删除外键约束需要），同时也可以定义多个外键约束

  -  `alter table employess drop foreign key name;` 删除外键约束

  - ```mysql
    CREATE TABLE test
    (
    id int NOT NULL,
    P_Id int,
        ...  ,
    PRIMARY KEY (id),
    CONSTRAINT name FOREIGN KEY (P_Id)
    REFERENCES Persons(P_Id)
    )
    ```

  - 外键的作用：

    1. 子表：向子表更新，插入数据时应保证其外键字段的值能在父表中找到，删除没有限制
    2. 父表：父表中的记录，如果已经被子表中的记录关联，则不能删除，关联字段不能更新，插入新数据不受影响

  - 查看是否外键  `show create table employess;` / `desc employess;`



###### 索引

索引是一种将数据库中单列或多列的值进行排序的结果

1. 将某个字段设置为索引或者将多个字段设置索引（联合索引）
2. 将索引的值取出重新进行排序，生成新结构即为 B 树

应用索引可以大幅度提高查询的速度

为什么有索引？

索引类似于书本中的目录，能够将某列或几列数据的特征记录

怎么用？

查询的时候，查询条件中带有索引列



索引分类：

- 单列索引：普通索引和唯一性索引都可以认为是单列索引
- 多列索引：联合索引

主键会自动成为索引列，并且属于唯一性索引；每个表都应设置主键，并设为自动增长

- 查看索引： `show index from employess;`



创建索引

- 创建单列索引

  - 主键自动成为索引

  - 单列包含（普通索引，唯一性索引）

    - 普通索引：对一个普通列（既不是主键，也不是外键并且没有任何约束）定为索引列
    - 唯一性索引（主键是特殊的唯一性索引）：
      - 对一个有唯一性约束列定义为索引
      - 对一个普通列定位为索引是添加唯一性约束

  - ```mysql
    create table test_index (
        id int primary key auto_increment,
        name varchar(20),
        address varchar(20),
        unique index(name)
    );
    
    -- 显示信息
    explain select * from test_index where name = 'test';
    
    -- 创建索引
    create index test_name on test_index(address);
    
    ```

    

- 创建多列索引

  -  数据再插入时就会进行排序，优先按照主键排序、联合索引排序

  - `create index test_name on test_index2(a,b,c);` 

    1. 主键索引会生成主键 B 树
    2. 联合索引会生成联合索引 B 树
    3. 索引本身是将索引列的特征值取出并排序生成的，联合索引 B 树有多个列，优先按照第一个值进行排序，然后再按第二个， ...  ，如果在查询时，联合索引的第一个值没有，则联合索引数的顺序将失去意义，所以此时不会走索引，该现象称为 “ 最左前缀原则 ” 

  - ```mysql
    create table test_index2(
        id int primary key auto_increment,
        a int,
        b int,
        c int,
        e varchar(20)
    );
    
    -- 创建多列索引
    create index test_name on test_index2(a,b,c); 
    ```

    

  







###### blob字段

- `tinyint` ：1 字节，类似 java 中的 byte
- `smallint` ：2 字节
- `mediumint` ：3 字节
- `int` ：4 字节
- `bigint` ：8 字节



- `char` ：固定长度的字符串类型    char(20)，不管放多少内容直接占 20 字符的容量 
- `varchar` ：可变长度的字符串类型    varchar(20)，放 'hello' 只占 5 个字符



- `blob` ：二进制对象，约64KB

- `longblob` ：二进制对象，约4G

- `longtext` ：大文本对象，约4G 字符串类型，固定长度

  

```java
    @Test
    public void testBlob() throws SQLException, IOException {
        FileInputStream fis = new FileInputStream("C:\\Users\\chumeng\\Pictures\\1126563.jpg");
        BufferedReader br = new BufferedReader(new FileReader("C:\\Users\\chumeng\\Desktop\\1.txt"));

        conn = JDBCUtil.openConnection();
        String sql = "insert into testblob(photo,memo) values (?,?)";
        ps = conn.prepareStatement(sql);
        ps.setBinaryStream(1,fis,fis.available());
        ps.setCharacterStream(2,br);
//        ps.setString(2,"dd");

        ps.execute();
        fis.close();
        br.close();

        JDBCUtil.closeConnection(conn);
        JDBCUtil.closePreparedStatement(ps);
    }

    @Test
    public void findBlob() throws SQLException, IOException {
        conn = JDBCUtil.openConnection();
        String sql = "select * from testblob where id = ?";
        ps = conn.prepareStatement(sql);
        // 每次使用时记得更改
        ps.setInt(1,2);
        ResultSet rs = ps.executeQuery();

        if (rs.next()) {
            InputStream is = rs.getBinaryStream("photo");
            byte[] buffer = new byte[1024];
            FileOutputStream fos = new FileOutputStream("C:\\Users\\chumeng\\Desktop\\test.jpg");
            int len;
            while (((len = is.read(buffer)) != -1)) {
                fos.write(buffer,0,len);
            }

            Reader r = rs.getCharacterStream("memo");
            char[] buffer2 = new char[1024];
            FileWriter fw = new FileWriter("C:\\Users\\chumeng\\Desktop\\test.txt");
            int len2;
            while ((len2 = r.read(buffer2)) != -1) {
                fw.write(buffer2,0,len2);
            }
            fw.flush();

            fos.close();
            is.close();

            fw.close();
            r.close();

            JDBCUtil.closeConnection(conn);
            JDBCUtil.closePreparedStatement(ps);
            JDBCUtil.closeResultSet(rs);
        }
    }
```



`datatime` 使用：

```java
    @Test
    public void testTime() throws SQLException {
        conn = JDBCUtil.openConnection();

        String sql = "insert into testblob(current_test_time) values (now())";
        ps = conn.prepareStatement(sql);


        //
//        java.util.Date now = new java.util.Date();
//        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
//        String nowStr = sdf.format(now);
//        ps = conn.prepareStatement("insert into testblob(current_test_time) values (?)");
//        ps.setString(1,nowStr);

        ps.execute();

        JDBCUtil.closeConnection(conn);
        JDBCUtil.closePreparedStatement(ps);
    }
```



###### mysql 数据类型总结

在 mysql 数据库中，每一条数据都有其数据类型



|  数字类型   |              取值范围               | 说明         | 单位   |
| :---------: | :---------------------------------: | ------------ | ------ |
|  `tinyint`  |    有符号 -128~127，无符号 0~255    | 存微小的整数 | 1 字节 |
| `smallint`  | 有符号 -32768~32767，无符号 0~65535 | 存小型的整数 | 2 字节 |
| `mediumint` |                 ...                 | 存中型的整数 | 3 字节 |
|    `int`    |                 ...                 | 存标准的整数 | 4 字节 |
|  `bigint`   |                 ...                 | 存大型的整数 | 8 字节 |

默认是为有符号，用 unsigned 指定为无符号：`create table test(age int unsigned);`



字符串类型

| 普通的文本字符串类型 | 取值范围     |                              |
| :------------------: | ------------ | ---------------------------- |
|      `char(M)`       | 0~255 个字符 | 固定长度为 M 的字符串        |
|     `varchar(M)`     | 0~255 个字符 | 长度可变，其他等同于 char(M) |

可变类型（text、blob、longtext、longblob），它们大小可变，text 类型适合存储长文本，而 blob 适合存储二进制数据，本质上支持任何数据类型，如文本、音视频

text，blob 指定的最大长度（字节数）：65535（约 64 KB）；longtext，longblob 指定的最大长度（字节数）：4 294 967 295 byte（约4G）



日期和时间类型

|    类型    |            |         格式          |
| :--------: | ---------- | :-------------------: |
|    date    | 日期       |     `yyyy-MM-dd`      |
|    time    | 时间       |      `HH:mm:ss`       |
| `datetime` | 日期和时间 | `yyyy-MM-dd HH:mm:ss` |



###### 批处理

mysql 本质是 socket 编程，使用批处理提升效率的本质是提升了输入输出流每次读取数据的数量，减少了和服务器连接的次数

```java
    @Test
    public void testBatch() throws SQLException {
        long time = System.currentTimeMillis();

        conn = JDBCUtil.openConnection();
        String sql = "insert into users(name,password) values(?,?)";
        ps = conn.prepareStatement(sql);
        ps.setString(1,"root");
        ps.setString(2,"root");

        // 取消自动提交
        conn.setAutoCommit(false);
        for (int i = 0;i < 10000;i++) {
            ps.addBatch();
            
            if(i % 1000 == 1) {
                ps.executeBatch();
                // 清空批处理命令列表
                ps.clearBatch();
            }
        }
        // 手动提交
        conn.commit();

        JDBCUtil.closeConnection(conn);
        JDBCUtil.closePreparedStatement(ps);
        System.out.println(System.currentTimeMillis() - time + "ms");
    }
```



###### 事务（transaction）

ACID：

- 原子性（**A**tomicity）：一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样
- 一致性（**C**onsistency）：在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作
- 隔离性（**I**solation，又称独立性）：数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）
- 持久性（**D**urability）：事务处理结束后，对数据的修改就是永久的（写入磁盘），即便系统故障也不会丢失



mysql 对于 CRUD（增删改查）操作默认是自动提交的

直接用 SET 来改变 MySQL 的自动提交模式:

- `SET AUTOCOMMIT = 0` ：禁止自动提交
- `SET AUTOCOMMIT = 1` ：开启自动提交



在 MySQL 命令行的默认设置下，事务都是自动提交的，即执行 SQL 语句后就会马上执行 COMMIT 操作。因此要显式地开启一个事务务须使用命令 BEGIN 或 START TRANSACTION，或者执行命令 `SET AUTOCOMMIT = 0;`，用来禁止使用当前会话的自动提交

- `BEGIN`、`start transaction`  开始一个事务
- `ROLLBACK` 事务回滚（数据定义语言（DDL）不能被回滚（create、alter、...））
- `COMMIT` 事务确认



默认的情况：

- 取消自动提交，多个客户端同时操作数据，无论其他客户端如何操作，当前会话窗口每次读取的数据都一样
- 默认的隔离级别为 “ 可重复读 ”，repeatable read
- 事务是否开启不影响隔离级别，开启事务之后可以保证原子性
- mysql 支持多种存储引擎，默认使用的是 innodb ，它是众多引擎之中唯一支持事务的（`show engines;`）

事务的隔离级别

| 隔离级别         | 脏读 | 不可重复读 | 幻读 |
| ---------------- | ---- | ---------- | ---- |
| read-uncommitted | ✔    | ✔          | ✔    |
| read-committed   | ✖    | ✔          | ✔    |
| repeatable-read  | ✖    | ✖          | ✔    |
| serializable     | ✖    | ✖          | ✖    |







提交操作时将数据的修改写入磁盘中，数据写入磁盘后，`rollback` 是无效的（因此使用事务应先关闭自动提交）

```java
@Test
public void testTransaction() throws SQLException {
    conn = JDBCUtil.openConnection();
    String sql = "insert into users(name,password) values(?,?)";
    ps = conn.prepareStatement(sql);
    ps.setString(1,"root");
    ps.setString(2,"root");

    // 取消自动提交
    conn.setAutoCommit(false);
    for (int i = 0;i < 10;i++) {
        ps.execute();
    }
    conn.rollback();   //有这条语句数据不会添加成功
    // 手动提交
    conn.commit();

    JDBCUtil.closeConnection(conn);
    JDBCUtil.closePreparedStatement(ps);
}
```



-  `select @@transaction_isolation;` ：查看当前会话隔离级别
-  `set session transaction isolation level XX;` ：设置当前会话事务隔离级别
-  `set global transaction isolation level XX;` ：设置全局会话事务隔离级别









###### `JDBC`

<img src=".\note_imgs\jdbc.png" style="zoom:80%;" />

```java
    @Test
    public void testJDBC() throws ClassNotFoundException, SQLException {
        String url = "jdbc:mysql://localhost:3306/java220201?characterEncoding=utf8";
        String user = "root";
        String password = "hjx20010707";
        // 注册驱动程序
        Class.forName("com.mysql.cj.jdbc.Driver");
        Connection conn = DriverManager.getConnection(url,user,password);

//        String sql = "update students set name = 'aaa' where id = '1';";
        // 预编译语句（负责将SQL 通过通道 conn 放入数据库中执行，防止SQL 注入，预编译提升效率）
//        PreparedStatement ps = conn.prepareStatement(sql);
        //执行语句
//        ps.execute();
        
        //查询
        String sql = "select * from employess;";
        PreparedStatement ps = conn.prepareStatement(sql);
        ResultSet rs = ps.executeQuery();
        while (rs.next()) {
            String name = rs.getString("name");
            int salary = rs.getInt("salary");
            System.out.println("name=" + name + ",salary=" + salary);
        }
        
        // 关闭资源
        conn.close();
        ps.close();
    }
```

优化1：

```java
package mysql.jdbc;

import java.sql.*;

/**
 * @author chumeng
 * @date 2022/5/31 14:54
 */
public class JDBCUtil {
    static {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
    public static Connection openConnection() {
        String url = "jdbc:mysql://localhost:3306/java220201?characterEncoding=utf8";
        String user = "root";
        String password = "hjx20010707";
        try {
            return DriverManager.getConnection(url,user,password);
        } catch (SQLException e) {
            e.printStackTrace();
        }
        return null;
    }

    public static void closeConnection(Connection connection) {
        if (connection != null) {
            try {
                connection.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    public static void closePrepareStatement(PreparedStatement prepareStatement) {
        if (prepareStatement != null) {
            try {
                prepareStatement.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    public static void closeResultSet(ResultSet rs) {
        if (rs != null) {
            try {
                rs.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

```java
@Test
public void testUtil() {

    Connection conn = JDBCUtil.openConnection();
    PreparedStatement ps = null;
    try {
        ps = conn.prepareStatement("update students set name = 'bbb' where id = '1';");
        ps.execute();
    } catch (SQLException e) {
        e.printStackTrace();
    } finally {
        JDBCUtil.closeConnection(conn);
        JDBCUtil.closePrepareStatement(ps);
    }
    
}
```

优化2：

```java
package mysql.jdbc;

import java.sql.*;

/**
 * @author chumeng
 * @date 2022/5/31 14:21
 */
public class MysqlConnect {
    String url;
    String user;
    String password;

    Connection conn;
    PreparedStatement prepareStatement;
    ResultSet rs;
    public MysqlConnect() {
        url = "jdbc:mysql://localhost:3306/java220201?characterEncoding=utf8";
        user = "root";
        password = "hjx20010707";
    }
    public MysqlConnect(String url, String user, String password) {
        this.url = url;
        this.user = user;
        this.password = password;
    }

    public void connect() {
        try {
            // 注册驱动程序
            Class.forName("com.mysql.cj.jdbc.Driver");
            conn = DriverManager.getConnection(url,user,password);
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }
    }

    public ResultSet getResultSet(String sql) {
        try {
            prepareStatement = conn.prepareStatement(sql);
            rs = prepareStatement.executeQuery();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        close();
        return rs;
    }

    public void operate(String sql) {
        try {
            prepareStatement = conn.prepareStatement(sql);
            prepareStatement.execute();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        close();
    }

    /**
     * 流的关闭
     */
    public void close() {
        try {
            if (conn != null) {
                conn.close();
            }
            if (prepareStatement != null) {
                prepareStatement.close();
            }
            if (rs != null) {
                rs.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

```java
@Test
public void testMysqlConnect() {
    MysqlConnect mysqlConnect = new MysqlConnect();
    mysqlConnect.connect();
    mysqlConnect.operate("update students set name = 'aaa' where id = '1';");
    mysqlConnect.close();
}
```

###### SQL注入

```java
        String user = " 1' or 1=1 -- ";
        String password = "test";

        ResultSet resultSet = null;
        try {
            //获取连接
            conn = JDBCUtil.openConnection();
            //3.创建sql语句
            String sql = "select * from users where name = '" + user + "' and password = '" + password + "';"  ;
            //4.预编译语句(负责将sql通过通道conn放入数据库中执行.防止sql注入,预编译提升效率)
            Statement stat = conn.createStatement();
            //5.执行语句
            resultSet =  stat.executeQuery(sql);
            if(resultSet.next()){
                System.out.println("qq");
            }
            System.out.println("over");
            
            ......
```

解决方法

```java
            String sql = "select * from users where name = ? and password = ?;";
            PreparedStatement stat = conn.prepareStatement(sql);

            // 4.5 传入参数
            stat.setString(1, user);
            stat.setString(2,password);
            //5.执行语句
            resultSet =  stat.executeQuery();
```

1. PreparedStatement 防止 sql 注入的本质是改变注入参数的方式
2. PreparedStatement 能够做好参数和sql 语句分离，如果 SQL 不变则只会编译一次，而 Statement 则每次都认为数据时全新的
