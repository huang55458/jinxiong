#### Mybatis



- Mybatis 是支持普通 SQL 查询，存储过程和高级映射的优秀持久层框架

- Mybatis 封装了几乎所有的 JDBC 代码和参数的手工设置以及结果集的检索
- Mybatis 使用简单的 XML 或注解做配置和定义映射关系，将 java 的 POJOS 映射成数据库中的记录



###### Mybatis 体系结构

1. 加载配置，配置有两种形式：

   - XML 配置文件
   - java 代码的注解

   Mybatis 将 SQL 的配置信息加载成为一个个的 MappedStatement 对象（包括了传入参数映射配置，执行的 SQL 语句，结果映射配置）

2. SQL 解析

   当 API 接口层接收到调用请求时，会接收到传入 SQL 的 ID 和 传入对象（可以是 Map，JavaBean 或 基本数据类型），Mybatis 会根据 SQL 的ID 找到对应的 MappedStatement 然后根据传入参数对象对 MappedStatement  进行解析，解析后可以得到最终要执行的 SQL 语句和参数

3. SQL 执行

   将最终得到的 SQL 和参数拿到数据库进行执行，得到操作数据库的结果

4. 结果映射

   将操作数据库的结果按照映射的配置进行转换，可以转换成 HashMap，JavaBean 或者基本数据类型，并将最终的结果返回



###### 框架 API

- SqlSessionFactoryBuilder ：该对象负责根据 Mybatis 配置文件 SQLMapConfig.xml 构建 SqlSessionFactory 实例
- SqlSessionFactory ：每一个 Mybatis 的应用程序都以一个 SqlSessionFactory 对象为核心，该对象负责创建 SqlSession 实例
- SqlSession ：该对象包含了所有执行 SQL 操作的方法，用于执行已映射的 SQL 语句



1. 为工程添加 Mybatis 开发包以及数据库驱动包
2. 在 src 下添加 Mybatis 配置文件 SqlMapConfig.xml 
3. 修改 SqlMapConfig.xml ，指定数据库连接参数
4. 利用 Mybatis API 编程，获取 SqlSession 实例



###### XML配置文件（SqlMapConfig.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!-- 加载类路径下的属性文件 -->
    <properties resource="db.properties"/>

    <!-- 设置一个默认的连接环境信息 -->
    <environments default="mysql_developer">
        <!-- 连接环境信息，取一个任意唯一的名字 -->
        <environment id="mysql_developer">
            <!-- mybatis使用jdbc事务管理方式 -->
            <transactionManager type="jdbc"/>
            <!-- mybatis使用连接池方式来获取连接 -->
            <dataSource type="pooled">
                <!-- 配置与数据库交互的4个必要属性 -->
                <property name="driver" value="${mysql.driver}"/>
                <property name="url" value="${mysql.url}"/>
                <property name="username" value="${mysql.username}"/>
                <property name="password" value="${mysql.password}"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="MybatisMappers/Dept.xml"/>
    </mappers>
</configuration>
```

MyBatis 框架的 XML 配置文件包含下面两种类型：

1. SqlMapConfig（1个）：主要配置，用于指定数据库连接参数和框架参数
2. SqlMap.xml（n个）：映射定义文件，用于定义 SQL 语句和映射信息



首先根据数据表编写实体类，然后编写 Mapper 映射文件，将 SQL 语句写在 Mapper 映射文件中，最后利用 SqlSession 对象提供的方法进行操作

接口：

```java
public interface DeptMapper {
    void addDept(Dept dept);
    void updateDept(Dept dept);
    Dept findById(int id);
    void deleteById(int id);
    List<Dept> findAll();
    
    // 一些简单的SQL 语句可以通过下面的方式，可以无需通过 xml 配置
    // @Select("select * from table")
    // List<Dept> findAll();
}
```

Dept.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="MybatisTest.DeptMapper">
    <select id="addDept">
        insert into t_dept(deptno,dname,loc) values(#{deptno},#{dname},#{loc})
    </select>
    <update id="updateDept" parameterType="entity.Dept">
        update t_dept set dname = #{dname},loc = #{loc} where deptno = #{deptno}
    </update>
    <select id="findById" parameterType="int" resultType="entity.Dept">
        select * from t_dept where deptno = #{deptno}
    </select>
    <delete id="deleteById" parameterType="int">
        delete from t_dept where deptno = #{deptno}
    </delete>
    <select id="findAll" resultType="entity.Dept">
        select * from t_dept
    </select>
</mapper>
```

Test 类：

```java
    @Test
    public void testSqlSession() throws IOException {
        String resource = "SqlMapConfig.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        SqlSession sqlSession = sqlSessionFactory.openSession();

        Dept dept = new Dept(4,"aaaaaad","test");

//        sqlSession.insert("MybatisTest.DeptMapper.addDept",dept);

        DeptMapper deptMapper = sqlSession.getMapper(DeptMapper.class);
//        deptMapper.addDept(dept);
//        deptMapper.updateDept(dept);
        System.out.println(deptMapper.findById(4));
        List<Dept> list = deptMapper.findAll();
        list.forEach(System.out::println);

        // 默认不提交
        sqlSession.commit();

        System.out.println(sqlSession);
        sqlSession.close();
    }
```



利用 SqlSession 实现 CRUD 操作，具体步骤：

1. 根据数据表编写实体类（java pojo）
2. 编写 SqlMap.xml 映射文件，定义 SQL 操作和映射信息
3. 获取 SqlSession 对象，执行 CRUD 操作
4. 提交事务（DML 操作）
5. 释放 SqlSession 对象资源



###### 分页查询

在使用 SqlSession 的 selectList 方法时，指定一个 RowBounds 分页器参数，即可查询指定范围的记录

- `RowBounds(offset,limit)`：构造方法
  - offset：指定抓取记录的起始行，从 0 开始
  - limit：指定抓取记录的数量（偏移量）
- `selectList(SQL 的 ID，参数，RowBounds 对象)`

```java
List<Dept> list2 = sqlSession.selectList("findAll",Dept.class,new RowBounds(1,2));
list2.forEach(System.out::println);
```

Mybatis 分页时基于内存分页，原理是查询出所有记录，然后基于 jdbc 的 absolute() 和 next() 方法定位获取部分记录，因此在遇到大量数据的情况下，不推荐使用 Mybatis 自带的分页功能，需要开发者指定分页的 SQL 语句



map

```java
HashMap<String,Object> map = deptMapper.findDept(3);
System.out.println(map.get("dname"));
```

```map
HashMap<String,Object> findDept(int id);
```

```xml
<select id="findDept" resultType="java.util.HashMap" parameterType="int">
    select * from t_dept where deptno = #{deptno};
</select>
```





使用 Mapper 映射器

Mapper 映射器是开发者创建绑定映射语句的接口，映射器接口的示例可以从 SqlSession 中获取；接口中方法的方法名、参数、返回值应与 xml 中的id、parameterType、resultType对应

```java
DeptMapper deptMapper = sqlSession.getMapper(DeptMapper.class);
```





使用 HashMap 查看

```java
List<HashMap<String,Object>> list3 = deptMapper.findAllDept();
for (HashMap<String,Object> m : list3) {
    m.forEach((k,v) -> System.out.println(k + ":" +v));
}
```

接口：

```java
List<HashMap<String,Object>> findAllDept();
```

```xml
<select id="findAllDept" resultType="java.util.HashMap" >
    select * from t_dept;
</select>
```





###### ResultMap 映射定义

在 SqlMap.xml 定义 < select> 操作时，如果查询结果字段名和 java pojo 属性不一致时，需要使用 <resultMap> 元素显示指定映射关系（当实体属性和数据表字段名不一致时）

Test 类：

```java
@Test
public void testResultMap() throws IOException {
    String resource = "SqlMapConfig.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession sqlSession = sqlSessionFactory.openSession();

    DeptMapper deptMapper = sqlSession.getMapper(DeptMapper.class);
    List<Dept1> list = deptMapper.findAll1();
    list.forEach(System.out::println);
}
```

接口：

```java
List<Dept1> findAll1();
```

一个新的实体类：

```java
public class Dept1 {
    private int i;
    private String a;
    private String b;
    ...
        
}
```

web.xml :

```xml
<resultMap id="DeptMappingTest" type="entity.Dept1">
    <result property="i" column="deptno" jdbcType="INTEGER" //需大写javaType="int"/>
    <result property="a" column="dname" jdbcType="VARCHAR" javaType="String"/>
    <result property="b" column="loc" jdbcType="VARCHAR" javaType="String"/>
</resultMap>
<select id="findAll1" resultMap="DeptMappingTest">
    select * from t_dept
</select>
```



#### resultMap

[官网链接](https://mybatis.org/mybatis-3/zh/sqlmap-xml.html#Parameters)



###### 参数

JDBC 要求，如果一个列允许使用 null 值，并且会使用值为 null 的参数，就必须要指定 JDBC 类型（jdbcType）

MyBatis 也支持很多高级的数据类型，**提示，在实际中要像这样不能换行**

```xml
#{middleInitial, mode=OUT, jdbcType=STRUCT, jdbcTypeName=MY_TYPE, resultMap=departmentResultMap}
```

大多时候，你只须简单指定属性名，顶多要为可能为空的列指定 `jdbcType`，其他的事情交给 MyBatis 自己去推断就行

```xml
#{firstName}
#{middleInitial,jdbcType=VARCHAR}
#{lastName}
```



###### 字符串替换

默认情况下，使用 `#{}` 参数语法时，MyBatis 会创建 `PreparedStatement` 参数占位符，并通过占位符安全地设置参数（就像使用 ? 一样）

```java
@Select("select * from user where ${column} = #{value}")
User findByColumn(@Param("column") String column, @Param("value") String value);
```

其中 `${column}` 会被直接替换，而 `#{value}` 会使用 `?` 预处理

效果如下：

```java
@Select("select * from user where id = #{id}")
User findById(@Param("id") long id);

@Select("select * from user where name = #{name}")
User findByName(@Param("name") String name);

@Select("select * from user where email = #{email}")
User findByEmail(@Param("email") String email);
```

用这种方式接受用户的输入，并用作语句参数是不安全的，会导致潜在的 SQL 注入攻击。因此，要么不允许用户输入这些字段，要么自行转义并检验这些参数



###### `resultMap` 元素的概念视图

- constructor ：用于在实例化类时，注入结果到构造方法中
  - `idArg` - ID 参数；标记出作为 ID 的结果可以帮助提高整体性能
  - `arg` - 将被注入到构造方法的一个普通结果
- `id` – 一个 ID 结果；标记出作为 ID 的结果可以帮助提高整体性能
- `result` – 注入到字段或 JavaBean 属性的普通结果
- association ：一个复杂类型的关联；许多结果将包装成这种类型
  - 嵌套结果映射 – 关联可以是 `resultMap` 元素，或是对其它结果映射的引用
- collection ： 一个复杂类型的集合
  - 嵌套结果映射 – 集合可以是 `resultMap` 元素，或是对其它结果映射的引用
- discriminator ：使用结果值来决定使用哪个 resultMap
  - case ：基于某些值的结果映射
    - 嵌套结果映射 – `case` 也是一个结果映射，因此具有相同的结构和元素；或者引用其它的结果映射



###### id & result

*id* 和 *result* 元素都将一个列的值映射到一个简单数据类型（String, int, double, Date 等）的属性或字段。

这两者之间的唯一不同是，*id* 元素对应的属性会被标记为对象的标识符，在比较对象实例时使用。 这样可以提高整体的性能，尤其是进行缓存和嵌套结果映射（也就是连接映射）的时候

两个元素都有一些属性：

| 属性          | 描述                                                         |
| :------------ | :----------------------------------------------------------- |
| `property`    | 映射到列结果的字段或属性。如果 JavaBean 有这个名字的属性（property），会先使用该属性。否则 MyBatis 将会寻找给定名称的字段（field）。 无论是哪一种情形，你都可以使用常见的点式分隔形式进行复杂属性导航。 比如，你可以这样映射一些简单的东西：“username”，或者映射到一些复杂的东西上：“address.street.number”。 |
| `column`      | 数据库中的列名，或者是列的别名。一般情况下，这和传递给 `resultSet.getString(columnName)` 方法的参数一样。 |
| `javaType`    | 一个 Java 类的全限定名，或一个类型别名（关于内置的类型别名，可以参考上面的表格）。 如果你映射到一个 JavaBean，MyBatis 通常可以推断类型。然而，如果你映射到的是 HashMap，那么你应该明确地指定 javaType 来保证行为与期望的相一致。 |
| `jdbcType`    | JDBC 类型，所支持的 JDBC 类型参见这个表格之后的“支持的 JDBC 类型”。 只需要在可能执行插入、更新和删除的且允许空值的列上指定 JDBC 类型。这是 JDBC 的要求而非 MyBatis 的要求。如果你直接面向 JDBC 编程，你需要对可以为空值的列指定这个类型。 |
| `typeHandler` | 我们在前面讨论过默认的类型处理器。使用这个属性，你可以覆盖默认的类型处理器。 这个属性值是一个类型处理器实现类的全限定名，或者是类型别名。 |



```xml
<resultMap id="detailedBlogResultMap" type="Blog">
  <constructor>
    <idArg column="blog_id" javaType="int"/>
  </constructor>
  <result property="title" column="blog_title"/>
  <association property="author" javaType="Author">
    <id property="id" column="author_id"/>
    <result property="username" column="author_username"/>
    <result property="password" column="author_password"/>
    <result property="email" column="author_email"/>
    <result property="bio" column="author_bio"/>
    <result property="favouriteSection" column="author_favourite_section"/>
  </association>
  <collection property="posts" ofType="Post">
    <id property="id" column="post_id"/>
    <result property="subject" column="post_subject"/>
    <association property="author" javaType="Author"/>
    <collection property="comments" ofType="Comment">
      <id property="id" column="comment_id"/>
    </collection>
    <collection property="tags" ofType="Tag" >
      <id property="id" column="tag_id"/>
    </collection>
    <discriminator javaType="int" column="draft">
      <case value="1" resultType="DraftPost"/>
    </discriminator>
  </collection>
</resultMap>
```





###### 类型别名（typeAliases）

| 别名                      | 映射的类型   |
| :------------------------ | :----------- |
| _byte                     | byte         |
| _char (since 3.5.10)      | char         |
| _character (since 3.5.10) | char         |
| _long                     | long         |
| _short                    | short        |
| _int                      | int          |
| _integer                  | int          |
| _double                   | double       |
| _float                    | float        |
| _boolean                  | boolean      |
| string                    | String       |
| byte                      | Byte         |
| char (since 3.5.10)       | Character    |
| character (since 3.5.10)  | Character    |
| long                      | Long         |
| short                     | Short        |
| int                       | Integer      |
| integer                   | Integer      |
| double                    | Double       |
| float                     | Float        |
| boolean                   | Boolean      |
| date                      | Date         |
| decimal                   | Decimal      |
| bigdecimal                | BigDecimal   |
| biginteger                | BigInteger   |
| object                    | Object       |
| date[]                    | Date[]       |
| decimal[]                 | BigDecimal[] |
| bigdecimal[]              | BigDecimal[] |
| biginteger[]              | BigInteger[] |
| object[]                  | Object[]     |
| map                       | Map          |
| hashmap                   | HashMap      |
| list                      | List         |
| arraylist                 | ArrayList    |
| collection                | Collection   |
| iterator                  | Iterator     |



###### Collection

- 集合的嵌套 Select 查询

  ```xml
  <resultMap id="blogResult" type="Blog">
    <collection property="posts" javaType="ArrayList" column="id" ofType="Post" select="selectPostsForBlog"/>
  </resultMap>
  
  <select id="selectBlog" resultMap="blogResult">
    SELECT * FROM BLOG WHERE ID = #{id}
  </select>
  
  <select id="selectPostsForBlog" resultType="Post">
    SELECT * FROM POST WHERE BLOG_ID = #{id}
  </select>
  ```

- 集合的嵌套结果映射

  ```xml
  <resultMap id="blogResult" type="Blog">
    <id property="id" column="blog_id" />
    <result property="title" column="blog_title"/>
    <collection property="posts" ofType="Post">
      <id property="id" column="post_id"/>
      <result property="subject" column="post_subject"/>
      <result property="body" column="post_body"/>
    </collection>
  </resultMap>
  ```

  更详略的、可重用的结果映射

  ```xml
  <resultMap id="blogResult" type="Blog">
    <id property="id" column="blog_id" />
    <result property="title" column="blog_title"/>
    <collection property="posts" ofType="Post" resultMap="blogPostResult" columnPrefix="post_"/>
  </resultMap>
  
  <resultMap id="blogPostResult" type="Post">
    <id property="id" column="id"/>
    <result property="subject" column="subject"/>
    <result property="body" column="body"/>
  </resultMap>
  ```



###### association

- 关联的嵌套 Select 查询

  ```xml
  <resultMap id="blogResult" type="Blog">
    <association property="author" column="author_id" javaType="Author" select="selectAuthor"/>
  </resultMap>
  
  <select id="selectBlog" resultMap="blogResult">
    SELECT * FROM BLOG WHERE ID = #{id}
  </select>
  
  <select id="selectAuthor" resultType="Author">
    SELECT * FROM AUTHOR WHERE ID = #{id}
  </select>
  ```

- 关联的嵌套结果映射

  ```xml
  <resultMap id="blogResult" type="Blog">
    <id property="id" column="blog_id" />
    <result property="title" column="blog_title"/>
    <association property="author" column="blog_author_id" javaType="Author" resultMap="authorResult"/>
  </resultMap>
  
  <resultMap id="authorResult" type="Author">
    <id property="id" column="author_id"/>
    <result property="username" column="author_username"/>
    <result property="password" column="author_password"/>
    <result property="email" column="author_email"/>
    <result property="bio" column="author_bio"/>
  </resultMap>
  ```

  将结果映射作为子元素嵌套在内

  ```xml
  <resultMap id="blogResult" type="Blog">
    <id property="id" column="blog_id" />
    <result property="title" column="blog_title"/>
    <association property="author" javaType="Author">
      <id property="id" column="author_id"/>
      <result property="username" column="author_username"/>
      <result property="password" column="author_password"/>
      <result property="email" column="author_email"/>
      <result property="bio" column="author_bio"/>
    </association>
  </resultMap>
  ```



###### 鉴别器



```xml
<resultMap id="vehicleResult" type="Vehicle">
  <id property="id" column="id" />
  <result property="vin" column="vin"/>
  <result property="year" column="year"/>
  <result property="make" column="make"/>
  <result property="model" column="model"/>
  <result property="color" column="color"/>
  <discriminator javaType="int" column="vehicle_type">
    <case value="1" resultType="carResult">
      <result property="doorCount" column="door_count" />
    </case>
    <case value="2" resultType="truckResult">
      <result property="boxSize" column="box_size" />
      <result property="extendedCab" column="extended_cab" />
    </case>
    <case value="3" resultType="vanResult">
      <result property="powerSlidingDoor" column="power_sliding_door" />
    </case>
    <case value="4" resultType="suvResult">
      <result property="allWheelDrive" column="all_wheel_drive" />
    </case>
  </discriminator>
</resultMap>
```











#### Mybatis + Spring（SSM）



```xml
<dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.10</version>
</dependency>
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis-spring</artifactId>
  <version>2.0.7</version>
</dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.3.21</version>
        </dependency>
```

applicationContext.xml

```xml
<bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
    <property name="username" value="root"/>
    <property name="password" value="hjx20010707"/>
    <property name="url" value="jdbc:mysql://localhost:3306/java220201?characterEncoding=utf8"/>
</bean>
<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="datasource"/>
    <property name="mapperLocations" value="classpath:MybatisMappers/*.xml"/>
</bean>
<bean id="deptMapper" class="org.mybatis.spring.mapper.MapperFactoryBean">
    <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
    <property name="mapperInterface" value="mapper.DeptMapper"/>
</bean>
```

Mybatis-spring.jar 提供下面几个 API

- `SqlSessionFactoryBean` ：为整合应用提供的 SqlSession 对象资源
- `MapperFactoryBean`：根据指定 Mapper 接口生成 Bean 实例
- `MapperScannerConfigurer`：根据指定包批量扫描 Mapper 接口并生成实例



在单独使用 Mybatis 时，所有操作都是围绕 SqlSession 展开的，SqlSession 是通过 SqlSessionFactory 获取的，而 SqlSessionFactory 又是通过 SqlSessionFactoryBuilder 创建生成的

在 Spring 和 Mybatis 整合应用时，同样需要 SqlSession、Mybatis-spring.jar 提供了 SqlSessionFactoryBean 这个组件，作用就是通过原 SqlSessionFactoryBuilder 生成 SqlSessionFactory 对象，为整合应用提供 SqlSession 对象

通过 SqlSessionFactoryBean 还可以定义一些属性来制定 Mybatis 框架的配置信息，在定义 SqlSessionFactoryBean 时，可以使用以下常用属性





- MapperFactoryBean 需要注入以下两个属性
  - SqlSessionFactoryBean 对象，用于提供 SqlSession
  - 要返回的 Mapper 对象的 Mapper 接口
  
- SqlSessionFactoryBean

  - DataSource ：用于指定连连接数据库的数据源（必要属性）
  - MapperLocations：用于指定 Mapper 文件存放的位置
  - configLocation ：用于指定 Mybatis 的配置文件位置，如果指定了该属性，那么会以该配置文件的内容作为配置信息构建对应的 SqlSessionFactoryBuilder，但是后续属性指定的内容会覆盖该配置文件里面指定的对应内容
  - typeAllases ：数组类型，用来指定别名的，指定了这个属性后，Mybatis 会把这个类型的短名称作为这个类型的别名

- MapperScannerConfigurer 

  1. 在使用 MapperFactoryBean 时，有一个 Mapper 就需要定义一个对应的 MapperFactoryBean；当 Mapper 比较少时可以，但遇到大量 Mapper 时就需要使用 Mybatis-spring.jar 提供的 MapperScannerConfiguer 组件，通过这个组件会自动扫描各个 Mapper 接口，并注册对应的 MapperFactoryBean 对象
  2. 在定义 MapperScannerConfigurer 时，只需要指定一个 basePackage即可，basePackage 用于指定 Mapper 接口所在的包，在这个包及其所有子包下面的 Mapper 接口都将被搜索到，并把它们注册为一个个 MapperFactoryBean 对象，多个包之间可以使用逗号或者分号进行分隔

  ```xml
  <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
      <property name="basePackage" value="mapper" />
  </bean>
  ```

  - SqlSessionFactory 属性可以不用指定，会以 Autowired 方式自动注入

  - 所有接口复用一个 SqlSessionFactory 实例

  - 如果指定的包下并不完全是我们定义的 Mapper 接口，此时使用 MapperScannerConfigurer 的另外两个属性可以缩小搜索和注册范围，一个是 annotationClass，另一个是 markerInterface

    - annotationClass ：用于指定一个注解标记，当指定了 annotationClass 时，MapperScannerConfigurer 将只注册使用了注解标记的接口

      ```java
      // 随便定义一个注解
      public @interface MybatisRepository {
          String value() default "";
      }
      ```

      ```xml
      <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
          <property name="basePackage" value="mapper" />
          // 下面语句声明在 Mapper 包中只有被 MybatisRepository 注解修饰的接口才会被加入容器中
          <property name="annotationClass" value="annotation.MybatisRepository"/>
      </bean>
      ```

      

    - markerInterface ：用于指定一个接口，当指定了 markerInterface 时，MapperScannerConfigurer 将只注册继承自 markerInterface 的接口



###### SqlSessionTemplate

上述整合完成后，程序可直接使用 Spring 容器中的 Mapper 接口实例进行编程，此外 Mybatis-Spring.jar 还提供一个 SqlSessionTemplate 组件，也可以将该组件对象注入给程序中 DAO，在 DAO 中利用 SqlSessionTemplate 编程

applicationContext.xml ：

```xml
    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
        <property name="username" value="root"/>
        <property name="password" value="hjx20010707"/>
        <property name="url" value="jdbc:mysql://localhost:3306/java220201?characterEncoding=utf8"/>
    </bean>
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource"/>
        <property name="mapperLocations" value="classpath:MybatisMappers/Dept.xml"/>
    </bean>
<bean class="org.mybatis.spring.SqlSessionTemplate">
    <constructor-arg ref="sqlSessionFactory"/>
</bean>
<context:component-scan base-package="dao"/>
```

写一个接口和实现类：

```java
public interface DeptDao {
    List<Dept> findAll();
}
```

```java
@Repository
public class MybatisDeptDao implements DeptDao{
    private SqlSessionTemplate sqlSessionTemplate;

    public SqlSessionTemplate getSqlSessionTemplate() {
        return sqlSessionTemplate;
    }

    @Autowired
    public void setSqlSessionTemplate(SqlSessionTemplate sqlSessionTemplate) {
        this.sqlSessionTemplate = sqlSessionTemplate;
    }

    @Override
    public List<Dept> findAll() {
        // finaAll 会默认从 Mybatis 的所有 namespace 中查找 id 为 findAll 的查询语句，当有多个时会抛出异常，最好加上 namespace ，如 mapper.DeptMapper.findAll
        return sqlSessionTemplate.selectList("findAll");
    }
}
```

测试：

```java
    @Test
    public void testProperties() {
        ApplicationContext appContext = new ClassPathXmlApplicationContext("static/test.xml");
        MybatisDeptDao mybatisDeptDao = appContext.getBean("mybatisDeptDao",MybatisDeptDao.class);
        List<Dept> list = mybatisDeptDao.findAll();
        list.forEach(System.out::println);
    }
```



###### 

SSM 整合步骤：

1. 创建工程，搭建 SSM 技术环境
   1. 创建一个 Web 工程
   2. 添加 Mybatis 相关技术环境
      - 引入数据库驱动包和 Mybatis 开发包
      - 引入 dbcp 连接池开发包管理 Mybatis 相关参数
      - 引入  Spring ioc、jdbc、tx、webmvc 等支持的开发包
      - 在 src 下添加 applicationContext.xml 配置文件
      - 在 web.xml 中配置 dispatcherServlet 主控制器
      - 引入 Mybatis 和 Spring 整合开发包 Mybatis-Spring.jar
2.  基于 MapperScannerConfigurer 方式整合 Mybatis 的 Mapper 接口
   1. 根据数据库编写实体类
   2. 编写 Mapper 映射文件，在 XML 文件添加 SQL 操作的定义
   3. 编写 Mapper 接口，定义 SQL 操作方法
   4. 在 Spring 配置文件中 定义以下 Bean
      - DateSource
      - SqlSessionFactoryBean
      - MapperScannerConfigurer
3. 编写和配置 SpringMVC 注意组件，如 Controller、HandlerMapping、ViewResolver
   1. 编写 Controller 和请求处理方法
   2. 配置 `<mvc:annotation-driven/>`，支持 @RequestMapping
   3. 配置 Controller 组件
      - 开启组件扫描，将 Controller 扫描到 Spring 容器
      - 需要 DAO 时采用注入的方式使用
      - 在请求处理方法上使用 @RequestMapping 指定对应的请求
   4. 配置 ViewResolver 组件
4. 编写 JSP 视图组件，利用标签和表达式显示模型数据
   - JSP 可以使用 JSTL 标签
   - JSP 可以使用 EL 表达式
   - JSP 可以使用 SpringMVC 的表单标签
5. 测试程序





###### 鉴别器

有时一个单独的数据库查询也许会返回很多不同类型的结果集，例如一个表存储了单选题和多选题，查询时需要返回单选题对象和多选题对象，鉴别器就是用来处理这个情况的，鉴别器元素很像 java中 的 Switch 分支语句
