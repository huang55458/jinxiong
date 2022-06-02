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



分组查询：通过关键字 `group  by` 可以将数据划分到不同的组中，实现对记录进行分组查询；在查询时，所查询的列必须包含在分组的类中，目的是使查询的数据没有矛盾；使用分组函数时。select 后面只能跟两种字段（分组字段、聚合函数）

解除只能跟两种字段限制：`SET GLOBAL sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));`

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

 



`select * from employess where id not in (23,4,null);` ：结果为 `Empty set`，not in 与 null 不能一起出现



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
