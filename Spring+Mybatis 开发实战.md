###### 企业级项目环境搭建

1. 导包

   创建 web 项目，导入如下开发包：

   - 数据库驱动，连接池，Mybatis 开发包
   - Spring 开发包
   - Spring 整合 Mybatis 开发包

2. 配置 applicationContext.xml
   1. 配置数据源
   2. 配置 SqlSessionFactory
   3. 配置 Mybatis 注解 （该注解需要自定义）
3. 



web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">  // 5.0是个什么版本
  <display-name>Archetype Created Web Application</display-name>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
        // 好像没有也可以 ？
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>*.form</url-pattern>
    </servlet-mapping>

    <filter>
        <filter-name>encoding</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encoding</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```



applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:jee="http://www.springframework.org/schema/jee"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:c="http://www.springframework.org/schema/c"
       xmlns:cache="http://www.springframework.org/schema/cache"
       xmlns:lang="http://www.springframework.org/schema/lang"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee.xsd
      http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
      http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache.xsd
      http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd
      http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
      http://www.springframework.org/schema/lang http://www.springframework.org/schema/lang/spring-lang.xsd
      http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
      http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">


    <bean id="datasource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.cj.jdbc.Driver" />
        <property name="username" value="root"/>
        <property name="password" value="hjx20010707"/>
        <property name="url" value="jdbc:mysql://localhost:3306/java220201?characterEncoding=utf8"/>
    </bean>
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="datasource"/>
        <property name="mapperLocations" value="classpath:mappers/*.xml"/>
    </bean>
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="---" />
<!--        <property name="annotationClass" value="annotation.MybatisRepository"/>-->
    </bean>

    <context:component-scan base-package="controller"/>

    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp"/>
    </bean>

    <mvc:annotation-driven />

</beans>
```

#### Mybatis 动态 SQL

动态 SQL 是 Mybatis 框架中强大特性之一，在一些组合查询页面，需要根据用户输出的查询条件生成不同的查询 SQL ，在这 JDBC 或其他相似框架中需要在代码中拼接 SQL，经常容易出错，但是在 Mybatis 中可以解决问题

使用动态 SQL 元素与 JSTL 相似，它允许我们在 XML 中构建不同的 SQL 语句，常用的元素如下：

- if、choose：判断

  ```xml
  <!--    根据部门 id 查询员工信息-->
  <select id="findByDept" parameterType="entity.Condition" resultType="entity.Emp">
      select * from t_emp
      <if test="deptno != 0">where deptno = #{deptno}</if>
  </select>
  ```

  deptno 会先从请求参数中获取，没有请求时会从数据库表中查询此字段，拿取 deptno 字段的第一个值

  ```xml
  <!--    根据不同工资 查询员工信息-->
      <select id="findBySalary" parameterType="entity.Condition" resultType="entity.Emp">
          select * from t_emp
          <choose>
              <when test="salary > 3000">
                  where sal > #{salary}
              </when>
              <otherwise>
                  where sal > 1500
              </otherwise>
          </choose>
      </select>
  ```

- where、set、trim：关键字

  - where ：简化查询语句中 where 部分的条件判断，where 元素可以在 <where> 元素所在的位置输出一个 where 关键字，而且还可以将后面条件多余的 and或者 or 关键字去除

  ```xml
  <!--    查询当前部门下，工资大于给定工资的员工-->
      <select id="findByDeptAndSalary" parameterType="entity.Condition" resultType="entity.Emp">
          select * from t_emp
          <where>
              <if test="deptno != 0">
                  and deptno = #{deptno}
              </if>
              <if test="salary != 0">
                  and sal > #{salary}
              </if>
          </where>
      </select>
  ```

  - set ：主要是用于更新操作的时候，主要功能是和 where 元素相似，主要是在 <set> 元素所在的位置输出一个 set 关键字，而且还可以

    ```xml
    update t_emp
        <set>
            <if test="ename != null">
                ename = #{ename},
            </if>
            <if test="job != null">
                job = #{job}
            </if>
        </set>
            where empno = #{empno}
    ```

  - trim：可以在自己包含的内容前加上某些前缀，也可以在其后加上某些后缀，与之对应的属性是 prefix 和 suffix ；可以把包含内容的首部内容过滤，即忽略，也可以把尾部的某些内容过滤，对应的属性是 prefixOverrides 和 suffixOverrides；正因为 trim 有上述功能，我们可以利用 trim 来代替 where 和 set 元素的功能

    ```xml
    <trim prefix="WHERE" prefixOverrides="AND |OR ">
      ...
    </trim>
    ```

    ```xml
    <trim prefix="SET" suffixOverrides=",">
      ...
    </trim>
    ```

- foreach ：循环，可以进行集合的迭代，主要用在构建 in 条件中；foreach 元素允许指定一个集合，声明集合项和索引变量，这些变量可以用在元素体内，也允许指定开放和关闭的字符串，在迭代之间放置分隔符

  ```xml
  select ename,sal,mgr from t_emp where
  <foreach collection="arr" index="index" item="item" open="mgr in (" separator="," close=")" nullable="true">
      #{item}
  </foreach>
  ```
  
  一般传入一个实体类，collection 是实体类中的属性名，属性可以是 集合、数组



###### spring + ajax

Asynchronous JavaScript And XML；Ajax 是一种用来改善用户体验的技术，具有异步处理和刷新部分页面内容的特点

<img src=".\note_imgs\Ajax.png" style="zoom:80%;" />



Ajax 技术在应用时，需要客户端 JavaScript 处理和服务端处理两部分；使用 Ajax 技术时，需要以下工作：

- 编写 JavaScript 程序，发送 Ajax
- 编写服务器处理，返回处理结果（常用 json 格式，也支持xml、html、text）
- 编写 JavaScript 程序，获取服务器结果，刷新部分页面



为了便于接收和处理 Ajax 请求，SpringMVC 提供了 json 响应的支持，可以很方便地将数据自动转成 json 格式字符串给客户端 JavaScript 返回

在 SpringMVC 中，与 json 响应相关的注解为 @ResponseBody

1. 导包

   ```xml
   <dependency>
     <groupId>com.fasterxml.jackson.core</groupId>
     <artifactId>jackson-databind</artifactId>
     <version>2.13.3</version>
   </dependency>
   ```

2. 开启 @ResponseBody

   `<mvc:annotation-driven />`

3. 使用



注解多种用法，该注解可以将以下类型数据做成 json

1. 基本类型 boolean、int、String、等
2. Map 类型数据
3. 集合或数组
4. 实体对象
5. 实体对象集合

```java
@Controller
@RequestMapping("json")
public class JsonController {
    @GetMapping("boolean.form")
    @ResponseBody
    public boolean test1() {
        return true;
    }

    @GetMapping("map.form")
    @ResponseBody
    public HashMap<String, Object> test2() {
        HashMap<String, Object> map = new HashMap<>();
        map.put("name","name");
        map.put("value","value");
        map.put("pass",1234);
        map.put("isTrue",true);
        map.put("num",3.3);
        return map;
    }

    @GetMapping("list.form")
    @ResponseBody
    public List<String> test3() {
        List<String> list = new ArrayList<>();
        list.add("hello");
        list.add("world");
        return list;
    }

    @GetMapping("user.form")
    @ResponseBody
    public User test4() {
        User user = new User();
        user.setId(1);
        user.setName("张三");
        user.setStatus("1234");
        return user;
    }

    @GetMapping("listUser.form")
    @ResponseBody
    public List<User> test5() {
        List<User> list = new ArrayList<>();
        for (int i = 0; i < 5; i++) {
            User user = new User();
            user.setId(1);
            user.setName("张三");
            user.setStatus("1234");
            list.add(user);
        }
        return list;
    }
}
```





###### Mybatis 关联映射

- 主键映射

  在使用 Mybatis 做插入操作时，可以由 Mybatis 负责主键生成，主键字段部分的映射可以分成以下两种情况：

  - 数据支持自动递增，如MySQL、SQLServer

    在设计表时，可以为主键字段指定自动递增，这样在添加操作时，主键值数据库会自动生成，不需要指定；在使用Mybatis 时，有时需要返回 Mysql、SQLServer 数据库自动增长的主键值，可以采用下面的方法定义：

    ```mxl
    <!--    主键映射-->
        <insert id="save" parameterType="entity.Emp" keyProperty="empno" useGeneratedKeys="true">
            insert into t_emp(ename,job,mgr) values(#{ename},#{job},#{mgr})
        </insert>
    ```

    在 <insert> 元素指定了自动递增属性设置后，Mybatis 会在插入操作后将自动生成的主键值给 keyProperty 指定的属性赋值

  - 数据库不支持自动递增，如 Oracle

  

关联映射的作用：

在查询时候经常需要获取两个或者两个以上关联表的数据，通过关联映射可以由一个对象获取相关联对象的数据

Mybatis 的关联映射有以下两种不同的实现形式：

- 嵌套查询：通过执行另外一个 SQL 映射语句来返回关联数据结果（查询两次）

  ```java
  public class Emp {
      private int empno;
      private String ename;
      ......
  
      // 关联属性
      private Dept dept;
      
      // settrt、getter
      ...
  }        
  ```

  ```xml
  <select id="findByIdReorm" parameterType="java.lang.Integer" resultMap="deptMap">
      select *
      from t_dept
      where deptno = #{id}
  </select>
  
  <resultMap id="deptMap" type="entity.Dept">
      <association property="emps" javaType="list" select="findDept" column="deptno"/>
  </resultMap>
  
  <select id="findDept" parameterType="java.lang.Integer" resultType="entity.Emp">
      select *
      from t_emp
      where deptno = #{id}
  </select>
  ```

- 嵌套结果：执行一个表关联查询 SQL，然后将查询结果映射成关联对象（查询一次）

  ```xml
      <select id="findByIdResultOrm" resultMap="deptMapResultOrm">
          select * from t_dept,t_emp where t_dept.deptno = t_emp.deptno and t_dept.deptno = #{id}
      </select>
  
      <resultMap id="deptMapResultOrm" type="entity.Dept">
          <id property="deptno" column="deptno"/>
          <result property="dname" column="dname"/>
          <result property="loc" column="loc"/>
          
          <collection property="emps" ofType="entity.Emp" javaType="arraylist" column="deptno">
              <id property="empno" column="empno"/>
              <result property="ename" column="ename"/>
              <result property="job" column="job"/>
              <result property="mgr" column="mgr"/>
              <result property="hiresdate" column="hiredate" />
              <result property="sal" column="sal"/>
              <result property="deptno" column="deptno"/>
              <result property="comm" column="comm"/>
          </collection>
          
      </resultMap>
  ```

  上面的映射信息，当利用 findByIdResultOrm 查询时，会执行那么定义的关联查询 SQL，然后 Mybatis 负责将结果数据提取，DEPT 字段值封装成 Dept 对象，EMP 字段值封装成 Emp 对象集合，然后将 Emp 集合给 Dept 对象的属性赋值



Collection 

```xml
<collection property="posts" column="id" ofType="Post" select="selectPostsForBlog"/>
```

 “ofType” 属性。这个属性非常重要，它用来将 JavaBean（或字段）属性的类型和集合存储的类型区分开来

在一般情况下，MyBatis 可以推断 javaType 属性，因此并不需要填写



集合映射

当查询某个表的记录信息时，如果关联表有多条相关记录，此时就可以使用集合映射，例如查询某个 Dept 部门对象信息，通过集合映射可以获取该部门中所有的 Emp 对象信息





RESTful

get post put delele patch m

#### git



<img src=".\note_imgs\git.png" style="zoom:67%;" />

<img src=".\note_imgs\git2.png" style="zoom:67%;" />

```
git branch -d newtest		 删除分支
git rebase --abort  		rebase退出(master|REBASE)模式

```

<img src=".\note_imgs\git3.png" style="zoom:80%;" />
