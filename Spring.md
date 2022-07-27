#### Spring



###### 简介

Spring 是一个开源 的轻量级的应用开发框架，其目的是用于简化企业级应用程序开发，减少侵入；Spring 提供的 IOC 和 AOP 应用，开源将主键的耦合度降至最低，即解耦，便于系统日后的维护和升级

Spring 为系统提供了一个整体的解决方案，开发者开源利用它本身提供的功能外，也可以与第三方框架和技术整合应用，可以自由选择采用哪种技术进行开发

Spring 容器简介：

在 Spring 中，任何的 java 类和 javaBean（简单规范的 java 对象） 都被当成 Bean；这些 Bean 通过容器管理和应用；Spring 容器实现了 IOC 和 AOP 机制，这些机制可以简化 Bean 对象创建和 Bean 对象之间的解耦；Spring 容器有 BeanFactory 和 ApplicationContext 两种类型

Spring 容器的使用：

从本质上讲  BeanFactory 和 ApplicationContext 仅仅只是一个维护 Bean 定义以及相互依赖关系的高级工程接口，通过  BeanFactory 和 ApplicationContext 我们可以访问 Bean 定义

首先在容器配置文件 applicationContext.xml 中添加 Bean 的定义 `<bean id="标识符" class="Bean 类型">` ，然后再创建 BeanFactory 和 ApplicationContext 容器对象后，调用 getBean() 获取 Bean 的实例即可

getBean(标识符)



实例化 Spring 容器：

```java
String conf = "applicationContext.xml";
ApplicationContext ac = new ClassPathXmlApplicationContext(conf);
```



利用 Spring 容器创建 javaBean 对象（Bean 的实例化）

- 用构造器实例化

  1. 配置 applicationContext.xml ，增加 Bean 对象创建声明

     ```java
     <bean id="calendarObj01" class="java.util.GregorianCalendar"/>
     // id :bean的名字，是程序代码中获得 Spring 管理 bean 对象的标识，不能重复
     //  class：指定创建对象的类的全称，Spring 会自动调用GregorianCalendar 类的默认构造器创建 bean 对象实例
     ```

- 使用静态工厂方法实例化

  ```java
  <bean id="calendarObj02" class="java.util.Calendar" factory-method="getInstance"/>
  ```

  factory-method 属性用于指定创建对象的静态工厂方法 getInstance，Spring 会自动调用工厂类 Calendar 静态工厂方法 Calendar 创建 bean 对象实例

- 使用实例工厂方法实例化

  ```java
  <bean id="calendarObj03" class="java.util.GregorianCalendar"/>
  <bean id="dateObj" factory-bean="calendarObj03" factory-method="getTime"/>
  ```

  这里定义了两个 bean，其中一个 bean calendarObj03 是用于创建 dateObj 对象的实例工厂；另外一个 bean 标记中的 id 属性 dateObj 用于定义 bean 名字，是程序代码中获得 Spring 管理 bean 对象的标识，这个名字不能重复，factory-bean 属性用于指定创建对象的工厂对象 calendarObj03 ，前面定义的一个 bean，factory-method 属性用于指定创建对象的工厂方法 getTime，Spring 会自动调用工厂类 Calendar 静态工厂方法 getInstance 创建 bean 对象实例

```java
        // 在容器实例创建号之后，容器中配置的 javaBean 也会被实例化
        String conf = "applicationContext.xml";
        ApplicationContext appContext = new ClassPathXmlApplicationContext(conf);
        System.out.println(appContext);

//        Calendar cal = (Calendar) appContext.getBean("calendarObj01");
        Calendar cal = appContext.getBean("calendarObj01",Calendar.class);
        System.out.println(cal.getTime());
```



###### bean 的命名

在 Spring 容器中，每个 bean 都需要有名字，该名字可以用 <bean>元素的 id 或 name 属性指定；id 比 name 严格，要求具有唯一性，不允许 用 “ / ” 等特殊字符串

bean 的别名：用 alias 指定另一个名字引用

```xml
<alias name="calendarObj01" alias="c"/>
```



###### bean 的作用域

Spring 容器在实例化 Bean 使，可以创建以下作用域 Bean 对象

| 作用域         | 描述                                                         |
| -------------- | ------------------------------------------------------------ |
| singleton      | 在每个 Spring IOC 容器中一个bean 定义对一个对象实例，默认项，单例 |
| prototype      | 一个 bean 定义对应多个对象实例                               |
| request        | 在一次 Http 请求中，一个 bean定义对应一个实例，仅限于  Web 环境 |
| session        | 在一个HttpSession 中，一个bean 定义对应一个实例，仅限于 Web 环境 |
| global session | 在一个全局的 HttpSession中，一个 bean 定义对应一个实例       |

上面的 bean 作用域，可以通过 `<bean>` 定义的 scope 属性定义

```java
<bean id="emp" class="bean.Employees" scope="prototype"/>

public class Employees {
    public Employees() {
        System.out.println("new Employees ... ");
    }
}

@Test
public void testPrototype() {
    ApplicationContext appContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    Employees emp1 = appContext.getBean("emp",Employees.class);
    Employees emp2 = appContext.getBean("emp",Employees.class);
    Employees emp3 = appContext.getBean("emp",Employees.class);
}
    
输出：
new Employees ... 
new Employees ... 
new Employees ...
```



###### Bean 的生命周期回调

指定初始化回调方法（容器创建好实例之后，立马调用 init 方法）

指定销毁回调方法，**仅使用于 singleton 模式**的 bean （容器关闭或者释放实例时调用）

```java
<bean id="emp" class="bean.Employees" init-method="init" destroy-method="destroy"/>
    
public class Employees {
    public Employees() {
        System.out.println("new Employees ... ");
    }

    public void init() {
        System.out.println(" init ... ");
    }

    public void destroy() {
        System.out.println(" destroy ... ");
    }
}

    @Test
    public void testPrototype() {
        ApplicationContext appContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        Employees emp1 = appContext.getBean("emp",Employees.class);
        ((AbstractApplicationContext)appContext).close();
    }

输出：
new Employees ... 
init ... 
destroy ...
```

在顶级的 `<beans>` 元素中的 default-init-method **属性**可以为容器所有的 `<bean>` 指定初始化回调方法；同理 default-destroy-method 属性



###### Bean 延迟实例化

在 ApplicationContext 实现的默认行为就是在启动时将所有 singleton bean 提前进行实例化；如果不想让一个 singleton bean 在 ApplicationContext 初始化时被提前实例化，可以使用 <bean>元素的 lazy-init = "true" 属性改变；一个延迟初始化 bean 将在第一次被用到时实例化（<beans> 有标签 default-lazy-init 可以为容器所有的 bean 延迟实例化特性）



###### 指定 bean 依赖关系

当一个 bean 对另一个 bean 存在依赖关系时，可以利用 <bean> 元素的 depends-on 属性指定；当一个 bean 对多个 bean 存在依赖关系时，depends-on 可以指定多个 bean 名，用逗号隔开

```xml
<bean id="emp" class="bean.Employees" init-method="init" destroy-method="destroy" depends-on="example"/>
<bean id="example" class="bean.ExampleBean1" lazy-init="true"/>
```

emp 依赖于 example，实例化 Employee 会先实例 ExampleBean1，即使ExampleBean1 设置了 `lazy-init="true"`



###### IoC

Inversion of control，控制反转；IOC 是指程序中对象的获取方式发生反转，有最初的 new 方法创建，转变为由 第三方框架创建注入；第三方框架一般是通过配置方式指定注入哪一个具体实现，从而降低了对象之间的耦合度

IOC 按实现方式不同，可以分为依赖注入 DI 和依赖查找两种；Spring 容器采用 DI 方式实现了 IOC 控制，IOC 是Spring 框架的基础和核心

**DI**：Dependency Injection，依赖注入

DI 的基本原理就是将一起工作具有关系的对象，通过构造方法参数或方法参数传入尽力关联，因此容器的工作就是创建 bean 时注入哪些依赖关系（创建对象，初始化对象间的关系）

IOC是一种思想，而 DI 是实现 IOC 的主要技术途径



DI 主要有两种注入方式：setter 注入和构造器注入

- Setter 注入（利用 Spring 实现 bean 属性 setter 方式注入）：

  - 创建对象后立即调用对应的set 方法完成注入

  - 通过调用无参构造器或无参 static 工厂方法实现实例化 bean 之后，调用该 bean 的 setter 方法，即可实现 setter 方式的注入

    ```xml
    <bean id="dataSource" class="bean.JDBCDataSource">
        <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/java220201?characterEncoding=utf8"/>
        <property name="user" value="root"/>
        <property name="pwd" value="hjx20010707"/>
    </bean>
    ```

- 构造器注入（基于构造器的注入时通过调用带参数的构造器来实现的，容器在 bean 被实例化的时候，根据参数类型执行相应的构造器）

  ```xml
  <bean id="userDaoImpl" class="dao.UserDaoImpl">
      <constructor-arg ref="dataSource" index="0"/>
  </bean>
  ```

  利用构造器注入初始化 dataSource 属性，Spring 会自动调用 UserDaoImpl 的有参构造器创建 UserDaoImpl  实例；ref 会引用容器中 id 为相同值的对象

  - index ：当有多个构造器参数时指定构造器参数顺序，从 0 开始



###### AOP



```xml
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
  <version>1.9.9.1</version>
</dependency>
<dependency>
  <groupId>org.aspectj</groupId>
  <artifactId>aspectjrt</artifactId>
  <version>1.9.9.1</version>
</dependency>
```



面向切面编程，AOP 主要用于处理共通逻辑，例如：日志记录、性能统计、安全控制、事物处理、异常处理等，AOP 可以将这些共通逻辑从普通业务逻辑代码中分离出来，这样在日后修改这些逻辑的时候就不会影响普通业务逻辑的代码

利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率



AOP、OOP 在字面上非常类似，但却是面向不同领域的两种设计思想

- OOP (面向对象编程)：这一业务处理过程的实体及其属性和行为进行抽象封装，以获得更加清楚高效的逻辑单元划分
- AOP 则是针对业务处理过程中的切面进行提取，它所面对的是处理过程中的某个步骤或阶段，以获得逻辑过程中各部分之间低耦合的隔离效果
- AOP 需要以 OOP 为前提基础



什么是方面 ？

指封装共同处理的组件，该组件被作用到其他目标组件方法上

什么是目标 ?

指被一个或多个方面所作用的对象

什么是切入点 ？

切入点是用于指定哪些组件和方法使用方面功能，在 Spring 中利用一个表达式指定切入目标

Spring 提供以下常用的切入点目标：

- 方法限定表达式
- 类型限定表达式



什么是通知 ？

通知是用于指定方面组件和目标组件作用的时机，例如：方面功能在目标方法之前或之后执行等时机

Spring 框架提供以下几种类型的通知：

- 前置通知：先执行方面功能再执行目标功能
- 后置通知（after-returning）：先执行目标功能再执行方面功能（目标无异常才执行方面）
- 最终通知（after）：先执行目标功能再执行方面功能（目标有无异常都执行方面）
- 异常通知：先执行目标，抛出后执行方面
- 环绕通知：先方面前置部分，然后执行目标，最后再执行方面后置部分



所有通知都配置的情况下：执行顺序由配置顺序决定



配置方面

```java
public class OperateLogger {
    public void log1() {
        System.out.println("前置通知");
    }
    public void log2() {
        System.out.println("后置通知");
    }
    public void log3() {
        System.out.println("最终通知");
    }
    // 日志记录
    public Object log4(ProceedingJoinPoint p) throws Throwable {
        String classname = p.getTarget().getClass().getName();
        String method = p.getSignature().getName();
        String date = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date());
        String msg = "----> 用户在" + date +",执行了" + classname + "," +method + "()方法";
        System.out.println(msg);
        Object object = p.proceed();
        System.out.println("调用目标主键方法之后");
        return null;
    }
    public void log5(Exception e) {
        StackTraceElement[] elems = e.getStackTrace();
        System.out.println("-->" + elems[0].toString());
    }
}
```

配置切入点：

```xml
<bean id="operateLogger" class="aspect.OperateLogger"/>
<aop:config>
    <aop:aspect ref="operateLogger">
        <aop:before method="log1" pointcut="within(controller.EmpController*) "/>
        <aop:after-returning method="log2" pointcut="within(controller.EmpController*)" />
        <aop:after method="log3" pointcut="within(controller.EmpController*)" />
        <aop:around method="log4" pointcut="within(controller.EmpController*)" />
        <aop:after-throwing method="log5" throwing="e" pointcut="within(controller.EmpController*)" />
    </aop:aspect>
</aop:config>
```



[AOP](https://docs.spring.io/spring-framework/docs/2.5.x/reference/aop.html)

注解方法：

- Aspect：声明方面组件
- Before：声明前置通知
- AfterReturning：后置通知
- After：最终通知
- Around：环绕通知
- AfterThrowing：异常通知









```xml
<!--    启用 @AspectJ 支持-->
    <aop:aspectj-autoproxy/>
```

配置方面和切入点：

```java
@Aspect
@Component
public class OperateLogger {
    @Before("within(controller.EmpController*)")
    public void log1() {
        System.out.println("前置通知");
    }
    @AfterReturning("within(controller.EmpController*)")
    public void log2() {
        System.out.println("后置通知");
    }
    @After("within(controller.EmpController*)")
    public void log3() {
        System.out.println("最终通知");
    }
    // 日志记录
    @Around("within(controller.EmpController*)")
    public Object log4(ProceedingJoinPoint p) throws Throwable {
        String classname = p.getTarget().getClass().getName();
        String method = p.getSignature().getName();
        String date = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date());
        String msg = "----> 用户在" + date +",执行了" + classname + "," +method + "()方法 \n";
        System.out.println(msg);

        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("E:/log.txt",true));
        bos.write(msg.getBytes());
        bos.flush();
        bos.close();

        Object object = p.proceed();
        System.out.println("调用目标主键方法之后");
        return object;
    }
    @AfterThrowing(value = "within(controller.EmpController*)",throwing = "e")
    public void log5(Exception e) {
        StackTraceElement[] elems = e.getStackTrace();
        System.out.println("-->" + elems[0].toString());
    }
}
```





Spring AOP 使用步骤：

1. 创建方面组件
2. 声明方面组件
3. 将方面组件作用到目标组件上

```java
public class OperateLogger {
    public void log1() {
        System.out.println("前置通知");
    }
}
```

```xml
<bean id="operateLogger" class="aspect.OperateLogger"/>
<aop:config>
    <aop:aspect ref="operateLogger">
        <aop:before method="log1" pointcut="within(controller.EmpController*) "/>
    </aop:aspect>
</aop:config>
```

测试方法：

```java
@Test
public void testBefore() {
    ApplicationContext appContext = new ClassPathXmlApplicationContext("applicationContext.xml");
    EmpController empController = appContext.getBean(EmpController.class);
    empController.getEmpDao();
}
```

- within ：用于匹配指定类型内的方法执行
  - `within(controller..*)`：表示 controller 包及其子包下的任何方法执行
  - `within(controller.EmpController*)`：表示 EmpController 这个类下的任何方法执行
  - `within(controller..EmpController+)`：表示 controller 包及其子包下 EmpController 类型及其子类型的任何方法执行
  - `within(@controller..Repository*)`：匹配 controller 包下持有 `@Repository` 注解的任何类型的任何方法





###### 事务

Spring 框架引入的重要因素之一是他全面的事务支持，Spring 框架提供了一致的事务管理方式，给程序带来了：

- 提供简单易用的编程式事务管理 API
- 支持声明式事务管理
- 便于 Spring 整合各种数据访问技术





```java
public String addBatch() {
    Emp e1 = new Emp();
    e1.setEname("张三");
    e1.setJob("坐牢");
    e1.setComm(0);
    empDao.save(e1); // 保存至数据库

    Integer.parseInt("a");

    Emp e2 = new Emp();
    e2.setEname("小明");
    e2.setJob("做数学题");
    e2.setComm(0);
    empDao.save(e2);
}
```

执行上面方法只有张三会被加入数据库中，因此引入事务管理机制是很有必要的



事务使用步骤：

1. 声明事务组件
2. 开启事务注解扫描
3. 使用注解标识类、方法

```xml
<!--    声明事务组件-->
    <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="datasource"/>
    </bean>
<!--    开启事务注解扫描-->
    <tx:annotation-driven transaction-manager="txManager" proxy-target-class="true"/>
```

```java
@Transactional
public class EmpController {
    ...
}
```



1. 事务管理器 txManger 需要根据数据库访问技术不同选择不同的实现，例如：JDBC、Mybatis 技术选用 InternalResourceViewResolver，而 Hibernate 技术则选择 HibernateResourceViewResolver
2. @Transaction 注解标记可以用在类定义上和方法定义上，方法的事务设置将优先于类级别注解的事务设置









###### 自动装配

Spring IoC 容器可以自动装配（autowire）相互协作 bean 之间的关联关系，autowire可以针对单个 bean 进行设置，autowire的方便之处在于减少 xml 的注入配置；在 xml 配置文件中，有在 <bean> 元素中使用 autowire属性指定自动装配规则，一个有五种类型的值

| 属性值             | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| no                 | 禁用自动匹配，默认值                                         |
| byName             | 根据属性名自动匹配，此选项将检查容器并根据名字查找与属性完全一致的 bean，并将其与属性自动装配；setter方式 |
| byType             | 如果容器中存在一个与指定属性类型相同的 bean ，那么将与该属性自动装配；setter方式 |
| constructor        | 与 byType 方式类似，不同之处在于它应用构造函数               |
| default/autodetect | 通过 bean 类来决定是使用 constructor 还是 byType 方式进行自动装配，如果发现默认的构造器，那么使用 byType 的方式 |



###### Spring 参数值注入

- value 元素可以通过字符串指定属性或构造器参数的值；容器将字符串从 java.lang.String 类型转换为实际的属性或参数类型后给 bean 对象注入



1. 注入基本值

2. 注入 Bean 对象

3. 直接集合注入

   ```java
   public class MessageBean {
       private String modelName;
       private int pageSize;
       private String username;
       private String password;
   
       private List<String> list;
       private Set<String> set;
       private Map<String,Object> map;
   //    private Properties properties;
   
       public String execute() {
           System.out.println("modelName :" +modelName);
           System.out.println("pageSize :" + pageSize);
           System.out.println("username :" + username);
           System.out.println("password :" + password);
   
           System.out.println("-------list 信息 -------");
           list.forEach(System.out::println);
           System.out.println("-------set  -------");
           set.forEach(System.out::println);
           System.out.println("-------map 信息 -------");
           Set<String> keys = map.keySet();
           keys.forEach(s -> System.out.println(s + " : " +map.get(s)));
   
           return "success";
       }
       
       ...
           
   }       
   ```

   ```xml
   <bean id="messageBean" class="bean.MessageBean">
       <property name="modelName" value="资源"/>
       <property name="pageSize" value="1"/>
       <property name="username" value="test"/>
       <property name="password" value="test"/>
       <property name="list">
           <list>
               <value>a</value>
               <value>b</value>
               <value>c</value>
           </list>
       </property>
   
       <property name="set">
           <set>
               <value>e</value>
               <value>d</value>
               <value>f</value>
           </set>
       </property>
   
       <property name="map">
           <map>
               <entry key="1" value="1"/>
               <entry key="2" value="2"/>
               <entry key="3" value="3"/>
           </map>
       </property>
   </bean>
   ```

4. 引用方式集合注入

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:util="http://www.springframework.org/schema/util"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/util
           http://www.springframework.org/schema/util/spring-util-4.0.xsd
           ">
   
   
   <bean id="messageBean" class="bean.MessageBean">
           <property name="modelName" value="资源"/>
           <property name="pageSize" value="1"/>
           <property name="username" value="test"/>
           <property name="password" value="test"/>
       
           <property name="list" ref="list"/>
           <property name="set" ref="set"/>
           <property name="map" ref="map"/>
   </bean>
   
   <util:list id="list">
       <value>"1"</value>
       <value>"2"</value>
   </util:list>
   
   <util:set id="set">
       <value>q</value>
       <value>p</value>
   </util:set>
   
   <util:map id="map">
       <entry key="a" value="a"/>
       <entry key="b" value="b"/>
       <entry key="c" value="c"/>
   </util:map>
   ```

   注意头部导入文件

5. 注入 Spring 表达式值

   <img src=".\note_imgs\applicationXml.png" style="zoom:67%;" />

   src 下有文件 db.properties ，内容 `user=root`

6. 注入 null 值或 空字符串

   ```xml
   <property name="password">
       <null/>
   </property>
   ```





###### 基于注解的组件扫描

指定一个包路径，Spring 会走到哪个扫描该包及其子包所有组件类，当发现组件类定义前有特定的组件标记时，就将该组件纳入到 Spring 容器中，等价于原有 xml 配置中的 bean 定义功能；组件扫描可以替代大量 xml 配置的 bean 定义

指定扫描类路径：xml 中配置（容器实例化会自动扫描 javabean 包及其子包下的所有组件类）

- `<context:component-scan base-package="bean"/>`



自动扫描的注解标记

指定扫描类路径后，并不是该路径下所有组件类都扫描到 Spring 容器中，只有在组件类定义前面有以下注解标记时，才会扫描到 Spring 容器中

| 注解标记    | 描述             |
| ----------- | ---------------- |
| @Component  | 通用注解         |
| @Name       | 通用注解         |
| @Repository | 持久化层组件注解 |
| @Service    | 业务层组件注解   |
| @Controller | 控制层组件注解   |

五个注解在功能上没有区别，只是用不同的注解可以区分当前类的作用，增加程序的可读性



- 自动扫描组件的命名
  - 当一个组件在扫描过程中被检测到时，会生成一个默认id 值，默认 id为 小写开头的类名，也可以在注解标记中自定义 id 
  - `@Component("a")`

- 指定组件的作用域
  - 通常受 Spring 管理的组件，默认的作用域是 singleton；如果需要其他的作用域可以使用 `@Scope` 注解，只有在注解中提供作用域名称即可 
  - `@Scope("prototype")`
  
- 初始化和销毁回调
  - `@postConstruct`   `@PreDestroy`

- 指定依赖注入关系
  - 具有依赖关系的 Bean 对象，利用下面任意一种注解都可以实现关系注入
  
  - @Resource
    - 用在字段定义或者 setter 方法前面，默认首先按名称匹配注入，然后再按类型匹配注入；当遇到多个匹配 bean时注入会发生错误，可以显示指定名（`@Resource(name="---")`）
    
  - @Autowired / @Qualifier
  
  - @Inject / @Named（解决匹配引入一个接口多个实现）
  
    ```java
    	public interface DemoService{
    		public void demoTest();
    	}
    
    
    	@Named("demoService_A_impl")
    	public class DemoService_A_impl implements DemoService{
    		@Override
    		public void demoTest(){
    			//here codes
    		}
    	}
    
    
    	@Named("demoService_B_impl")
    	public class DemoService_B_impl implements DemoService{
    		@Override
    		public void demoTest(){
    			//codes here----codes here
    		}
    	}
    
    
    	public class UseDemo{
    
    		@Inject
    		@Named("demoService_B_impl")   //指定注入实现类B
    		private DemoService demoService;
    
    		public void doSomething(){
    			demoService.demoTest();    //在此处调用的是实现类B的实现方法
    		}
    	}
    ```



@Autowired和@Resource的区别

1、@Resource是JDK原生的注解，@Autowired是Spring2.5 引入的注解

2、@Resource有两个属性name和type。Spring将@Resource注解的name属性解析为bean的名字，而type属性则解析为bean的类型。所以如果使用name属性，则使用byName的自动注入策略，而使用type属性时则使用byType自动注入策略。如果既不指定name也不指定type属性，这时将通过反射机制使用byName自动注入策略

@Autowired只根据type进行注入，不会去匹配name。如果涉及到type无法辨别注入对象时，那需要依赖@Qualifier或@Primary注解一起来修饰

共同点：@Resource和@Autowired都可以作为注入属性的修饰，在接口仅有单一实现类时，两个注解的修饰效果相同，可以互相替换，不影响使用



基于 field 的注入可能会带来一些隐含的问题

```java
@Autowired
private User user;

private String company;

public UserDaoImpl(){
    this.company = user.getCompany();
}
```

编译过程不会报错，但是运行之后报NullPointerException

Java 在初始化一个类时，是按照静态变量或静态语句块 –> 实例变量或初始化语句块 –> 构造方法 -> @Autowired 的顺序。所以在执行这个类的构造方法时，user对象尚未被注入，它的值还是 null

- 强制依赖就用构造器方式
- 可选、可变的依赖就用setter注入



###### `@value`

- 普通字符串

  ```java
      @Value("我是个普通字符串")
      private String nornal;
  ```

- 表达式值

  ```java
      @Value("#{demoService.anotherValue}")
      private String anotherValue;
  ```

  表达式的对象必须是通过T()包起来，才能执行

- 其他Bean的属性

  ```java
      @Value("#{demoService.anotherValue}")
      private String anotherValue;
  ```

- 注入文件资源

  ```java
      @Value("classpath:ch2/value/test.txt")
      private Resource testFile;
  ```

- 注入配置属性

  ```java
      @Value("${book.name}")
      private String bookName;
  ```

  在Spring 4中需要用property-placeholder标签把当前要注入的配置注册一下才可以使用（未测试），xml 配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:util="http://www.springframework.org/schema/util"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
  		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
  		http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.3.xsd">
  
      <context:component-scan base-package="dao,entity,service"/>
  <!--    <context:property-placeholder location="classpath:db.properties"/>-->  //另一种方法，可以使用 @Value("#{book.name}") 注入配置属性
  
      <util:properties id="jdbc" location="classpath:db.properties"/>
  ```

  



#### Spring MVC



###### MVC 简介

- M-Model 模型

  模型（Model）的职责是负责业务逻辑，包含两层：业务数据和业务处理逻辑，比如 dao、service 以及实体类都属于模型层

- V-View 视图

  视图（View）的职责是负责显示界面和用户交互（收集用户信息）；属于视图的组件时不包含业务逻辑和控制的 JSP

- C-Controller 控制器

  控制器是模型层 M 和视图层 V 之间的桥梁，用于控制流程；比如：在servlet 项目中的单一控制器 ActionServlet

<img src=".\note_imgs\springMVC.png" style="zoom:67%;" />



什么是 Spring Web MVC ？

 Spring Web MVC 是一个非常重要的功能模块，实现了 MVC 结构，便于简单，开始开发 MVC 结构的 Web 程序，Spring Web MVC 提供的API 封装了 Web 开发中常用的功能，简化了 Web 的过程



 Spring Web MVC 的核心组件

- DispatcherServlet（控制器，请求入口）
- HanderMapping（控制器，请求派发）
- Controller（控制器，请求处理流程）
- ModelAndView（模型，封装业务处理结构和视图）
  - Controller 组件约定的 handleRequest 方法 执行后返回一个 ModelAndView 对象，该对象可封装模型数据和视图名 响应信息
  - 构造器：
    - `ModelAndView(String viewName)`
    - `ModelAndView(String viewName,Map model)` ：viewName 是 jsp 页面的名称，model 的数据存储到 request 的 attribute 中

- ViewResolver（视图，视图显示处理器）
  - 所有 Controller 组件都返回一个 ModelAndView 实例，封装了视图名，Spring 中的视图以名字为标识，视图解析器 ViewResolver 通过名字来解析视图
  - Spring 提供了很多视图解析器：
    - `UrlBasedViewResolver`：将视图名 直接解析成对应的 URL，不需要显示的映射定义，如果你的视图名和视图资源的名字是一致的，就可以使用该解析器，无需映射
    - `InternalResourceViewResolver` ：本质是 UrlBasedViewResolver 的子类，支持 InternalResourceView（对 Servlet 和 jsp 的包装）
    - `XmlViewResolver`：支持用 xml 文件定义具体的响应视图文件



Spring MVC 的 XML 配置文件 spring-mvc.xml 

handlerMapping 的实现类是 Spring 提供的 SimpleUrlHandlerMapping ，SimpleUrlHandlerMapping 的 mapping 属性引用 id 为 urlMappings 的 Properties 集合 ；mapping 是固定属性名，不能改



请求执行流程：

1. 浏览器向 Tomcat7 服务器发起 Web 请求 "hello.form"
2. Tomcat 7 会根据 web.xml 的配置将 " hello.form " 请求交给 Spring 的核心控制器 DispatchServlet 处理
3. DispatchServlet 根据 HandlerMapping 中的 URLMappings 将 "hello.form" 请求交给 helloController
4. helloController 执行 handleRequest 方法，处理请求，并且返回 ModelAndView 对象代表处理结构，ModelAndView 对象包含了目标提示 "hello"
5. Spring 核心控制器收到 ModelAndView 中包含的视图 "hello"
6. Spring 核心控制器利用 ViewResolver 中的前后缀应用 "hello" 到 /WEB-INF/jsp/hello.jsp 文件
7. 执行 hello.jsp 作为响应结果发送到浏览器
8. 浏览器收到 hello.jsp 的执行结果





web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">
    
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value> // classpath:指定类中的 resources 目录 ，不加为webapp
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
        <init-param>
            <param-name>contextConfigLocation</param-name> // 不能随便更改
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>*.form</url-pattern>
    </servlet-mapping>
    
    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>
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
       xsi:schemaLocation="http://www.springframework.org/schema/jee http://www.springframework.org/schema/jee/spring-jee-4.3.xsd
      http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
      http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util-4.3.xsd">

    <context:component-scan base-package="org.springframework.web.servlet"/>

    <bean id="handlerMapping" class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
        <property name="mappings" ref="urlMapping"/>
    </bean>

    <util:properties id="urlMapping">
        <prop key="/hello.form">helloController</prop>
        <prop key="/test.form">helloController</prop>
    </util:properties>

    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/jsp/" />
        <property name="suffix" value=".jsp"/>
    </bean>

    <bean id="helloController" class="controller.HelloController"/>
    <context:component-scan base-package="service"/>
</beans>
```



```java
public class HelloController implements Controller {
    private CityService cityService;

    public CityService getCityService() {
        return cityService;
    }

    @Autowired
    public void setCityService(CityService cityService) {
        this.cityService = cityService;
    }

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse httpServletResponse) throws ServletException, IOException {
        List<City> list = cityService.getCityList();
//        request.setAttribute("list",list);
//        request.getRequestDispatcher("/index.jsp").forward(request,httpServletResponse);
        System.out.println("helloController");
        Map<String,List<City>> map = new HashMap<>(8);
        map.put("list",list);
        return new ModelAndView("index",map);
    }


}
```



###### applicationContext.xml

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
		
		
</beans>
```



###### web.xml

Servlet 4.0 web.xml模板:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
version="4.0"
metadata-complete="true">
</web-app>
```

> metadata-complete="true"这句话不要加删除，会导致无法扫描注解！省时间的@WebServlet("/路径")报错404.

3.0 :

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 　　　　　 xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" 　　　　   version="3.0" >  

</web-app>
```

2.5:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
version="2.5">  

</web-app>
```

2.4:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd" version="2.4">  
</web-app>
```

2.3:

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd">  
<web-app>  
</web-app>
```

5.0 （？？？）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">
```

###### 基于注解配置的 MVC 应用

- @RequestMapping

  - 可以用在类上和方法定义上，标明与哪一个客户请求对应

  - 使用前提：先使该类称为 bean 对象（@Service、... 注解 或 xml 配置）

    - 开启 @RequestMapping 注解映射，需要在 Spring 的 XML 配置文件中定义 RequestMappingHandlerMapping 和 RequestMappingHandlerAdapter 两个组件（需要在 Spring 3.1 之前定义）

      在 Spring 3.2 后可使用如下 xml 配置：`<mvc:annotation-driver/>`

- 接收请求参数值

  - Spring MVC Web 请求提交数据到控制器有下面几种方法

    - HTTPServletRequest

      ```java
      @GetMapping("param.form")
      public String login(HttpServletRequest request) {
          String name = request.getParameter("name");
          String pass = request.getParameter("pass");
          System.out.println(name + " : " + pass);
          return "world";
      }
      ```

      不是String 类型时 需要转类型

    - @RequestParam

      ```java
      @GetMapping("param.form")
      public String login(@RequestParam("name") String name, @RequestParam("pass")String pass) {
          System.out.println(name + " : " + pass);
          return "world";
      }
      ```

      如果参数名与表单 name 值相同，可以省略注解：

      ```java
      @GetMapping("param.form")
      public String login(String name, String pass) {
          System.out.println(name + " : " + pass);
          return "world";
      }
      ```

      参数类型自动转换，但可能出现参数类型转换异常：

      ```java
      @GetMapping("param.form")
      public String login(int pass,@RequestParam("name") int name) {
          System.out.println(name + " : " + pass);
          return "world";
      }
      ```

    - 使用自动机制封装成 Bean 对象

      ```java
      @GetMapping("param.form")
      public String login(User user) {
          System.out.println(user.getName() + " : " + user.getPass());
          return "world";
      }
      ```

      User 类中的属性名因与 form 表单中的 name 值一致

      ```java
      public class User {
          private String name;
          private String pass;
      	
          // getter() / setter()
      }
      
      ```



###### 向页面传值

- HttpServletRequest 和 Session

- ModelAndView

  ```java
  @GetMapping("param.form")
  public ModelAndView login(User user) {
      Map<String,User> map = new HashMap<>(8);
      map.put("user",user); // 相当于  request.setAttribute("user",user)
      return new ModelAndView("world",map); 
  }
  ```

- ModelMap

  ```java
  @GetMapping("param.form")
  public String login(String name, String pass, ModelMap modelMap) {
      User user = new User();
      user.setName(name);
      user.setPass(pass);
      modelMap.addAttribute("user",user);
      return "world";
  }
  ```

  ```java
  @GetMapping("param.form")
  public String login(User user, ModelMap modelMap) {
      modelMap.addAttribute("user",user);
      return "world";
  }
  ```

- @ModelAttribute

  ```java
  @GetMapping("param.form")
  public String login(@ModelAttribute User user) {
      return "world";
  }
  ```

上述方法均是使用 HttpServletRequest 的 setAttribute 传递到 jsp 页面中



###### 重定向视图

SpringMVC 默认采用转发方式定位视图，如果需要重定向方式可以采用下面几种方式：

- 使用 RedirectView
- 使用 redirect:前缀
- 如果 Controller 的请求处理方法返回的是 ModelAndView 对象，可以使用 RedirectView 方法重定向
- 如果 Controller 的请求处理方法返回的是 String 对象，可以使用 "redirect:前缀" 方法重定向

```java
@GetMapping("param.form")
public String login(String name, String pass) {
    System.out.println(name + " : " + pass);
    return "world";
}
// 如果出现 “ 通配符的匹配很全面，但无法找到 ---  ”，将 application.xml 的头部的链接版本号删除 ，如 ---/spring-util-4.3.msd  -->  --/spring-util.msd 

@GetMapping("param.form")
public ModelAndView login(String name, String pass) {
    RedirectView view = new RedirectView("http://baidu.com");
    return new ModelAndView(view);
}
```



###### 乱码

使用 spring 自动注入用户参数值的方式，会产生中文乱码；并且 HttpServletRequest 的 request.setCharacterEncoding("utf-8") 无法生效

在表单提交时，如果遇到中文字符会出现乱码现象，Spring 提供一个 CharacterEncodingFilter 过滤器，可用于解决乱码问题，CharacterEncodingFilter 使用需要注意一些问题：

- 表单数据以 post 方式提交
- 在 web.xml 中配置 CharacterEncodingFilter 过滤器
- 页面编码和过滤器指定编码要一致

```xml
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
```

使用拦截器实现登录检查

Spring 的 HandlerMapping 处理器支持拦截器应用，当需要为某些请求提供特殊功能时，例如对用户进行身份认证

```java
public class Interceptor  implements HandlerInterceptor {
    /**
     * 处理器执行前被调用，方法返回 true 表示会继续调用其他拦截器和处理器，返回 false 表示中断流程，不会执行后续拦截器和处理器
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        ...
        return true;
    }

    /**
     * 处理器执行后，视图处理前调用，此时可以通过 ModelAndView 对象对模型数据进行处理或对视图进行处理
     */
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        ...
    }

    /**
     * 整个请求处理完毕后调用，如性能监控中我们可以在此记录结束时间并输出消耗时间，还可以进行一些资源清理，只有 preHandle 返回 true时才会执行 此方法
     */
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        ...
    }
}
```

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/spring/*"/>
        <mvc:exclude-mapping path="/spring/login.form"/>
        <mvc:exclude-mapping path="/login/*" />
        <bean class="test.Interceptor" />
    </mvc:interceptor>
</mvc:interceptors>
```





###### 异常处理

- 使用 SpringMVC 提供的简单异常处理器 SimpleMappingExceptionResolver

  ```xml
  <!--    定义异常处理页面获取异常信息的变量名为 ex,默认名为 exception-->
      <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver" >
          <property name="exceptionAttribute" value="ex" />
          <property name="exceptionMappings" >
              <props>
  <!--                只匹配运行时异常，子类异常不会匹配-->
                  <prop key="java.lang.RuntimeException">error</prop> 
              </props>
          </property>
      </bean>
  ```

- 实现 HandlerExceptionResolver 接口自定义异常处理

- 使用 @ExceptionHandler 注解实现异常处理

  ```java
  // 将其写入会抛出异常的类中
  @ExceptionHandler
  public String execute(HttpServletRequest request,Exception exception){
      request.setAttribute("ex",exception);
      request.setAttribute("message",exception.getMessage());
      // 可根据异常类型不同返回不同视图名
      return "error";
  }
  // 简单写法，未测试
  @ExceptionHandler
  public String execute(){
      return "error";
  }
  ```



###### 文件上传

在 SpringMVC 中，文件上传功能有即插即用的 CommonsMultipartResolver 解析器组件实现，它支持 Commons FileUpload 和 COS FileUpload 两种上传组件

1. 首先导入依赖


```xml
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
    <version>2.11.0</version>
</dependency>
<dependency>
    <groupId>commons-fileupload</groupId>
    <artifactId>commons-fileupload</artifactId>
    <version>1.4</version>
</dependency>
```

2. 配置文件上传bean

   ```xml
       <!-- SpringMVC上传文件时，需要配置MultipartResolver处理器 -->
       <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
           <property name="defaultEncoding" value="UTF-8" />
       </bean>
   ```

   - CommonsMultipartResolver 解析器组件可以调用 commons-fileupload.jar 的功能，将请求提交的文件信息解析处理

3. 文件上传

   - 必须将表单的method设置为`POST`，并将`enctype`设置为`multipart/form-data`；只有在这样的情况下，浏览器才会把用户选择的文件以二进制数据发送给服务器

jsp文件：

```jsp
<form action="login/test1.form" method="post">
    name:<input name="name">
    pass:<input type="password" name="pass">
    <input type="submit">
</form>
```

controller：

```java
@Controller
public class UploadController {
    @PostMapping("upload.form")
    public String uploadFile(@RequestParam("file") MultipartFile uploadFile, HttpServletRequest request) {
        if (uploadFile.isEmpty()) {
            request.setAttribute("message","空文件");
            return "upload";
        }

        // 项目结构下的路径，可以通过浏览器直接访问,如：C:\Users\chumeng\IdeaProjects\SpringMVC\out\artifacts\SpringMVC_war_exploded\pictures
//        String path = request.getSession().getServletContext().getRealPath("upload");
//        System.out.println(path);
        // request.getContextPath() 站点路径
        
        try {
            String type = uploadFile.getContentType();
            String name = uploadFile.getOriginalFilename();
            byte[] bytes = uploadFile.getBytes();
            // 只有前半数据，测试图片只有上半部分
//            byte[] bytes = Arrays.copyOf(uploadFile.getBytes(),uploadFile.getBytes().length/2);

            System.out.println("文件类型 --> " + type);
            System.out.println("文件名 --> " + name);
            System.out.println("文件大小 --> " + bytes.length);

            // 两种方式都可以上传
            uploadFile.transferTo(new File("E:/"+name) );
//            FileUtils.writeByteArrayToFile(new File("E:/"+name),bytes);

            request.setAttribute("message","success");
        } catch (IOException e) {
            e.printStackTrace();
        }

        return "upload";
    }
}
```

上传多种数据：

jsp 文件：

```jsp
<form method="post" action="upload.form" enctype="multipart/form-data">
    <input type="File" name="file" ><br>
    <input type="File" name="file" ><br>
    <input name="name">
    <input type="password" name="password" />
    <input type="submit" value="上传"/>
</form>
```

```java
@Controller
public class UploadController {
    @PostMapping("upload.form")
    public String uploadFile(@RequestParam("file") List<MultipartFile> uploadFiles, HttpServletRequest request,User user) {
        if (uploadFiles.isEmpty()) {
            request.setAttribute("message","空文件");
            return "upload";
        }

        list<String> list = new Arraylist<>();
        
        try {

            for (MultipartFile uploadFile : uploadFiles) {
                // 执行
                ...
                    
                 //  url 为 项目中的绝对路径
                list.add(url + name);
            }

            // 用来返回上传文件保存的绝对路径，此路径需在项目内
            request.setAttribute("list",list);
            request.setAttribute("message","success");
        } catch (IOException e) {
            e.printStackTrace();
        }

        System.out.println(user.getName());
        System.out.println(user.getPassword());
        
        return "upload";
    }
}
```



限制上传文件大小

```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <property name="defaultEncoding" value="utf-8" />
    <property name="maxUploadSize" value="1024000" />
</bean>
```

```
Failed to complete request: org.springframework.web.multipart.MaxUploadSizeExceededException: Maximum upload size of 1024000 bytes exceeded; nested exception is org.apache.commons.fileupload.FileUploadBase$SizeLimitExceededException: the request was rejected because its size (6905224) exceeds the configured maximum (1024000)
```



简单压缩文件上传到服务器（没什么用）

```java
    @PostMapping("zipUploadFile.form")
    public String zipUploadFile(@RequestParam("file") MultipartFile uploadFile, HttpServletRequest request) throws IOException {
        if (uploadFile.isEmpty()) {
            request.setAttribute("message","空文件");
            return "upload";
        }

        String path = request.getSession().getServletContext().getRealPath("zip");
        String name = uploadFile.getOriginalFilename();
        System.out.println(path);

        File file = new File(path);
        if ( !file.exists()) {
            file.mkdir();
        }

        // 压缩
        InputStream input = uploadFile.getInputStream();
        BufferedInputStream bis = new BufferedInputStream(input);
        File zipFile = new File(path + File.separator + name + ".zip");
        ZipOutputStream zipOut = new ZipOutputStream(new FileOutputStream(zipFile));
        assert name != null;
        zipOut.putNextEntry(new ZipEntry(name));
        zipOut.setComment("test");

        // 设置缓存大小 1M
        byte[] bytes = new byte[1048576];
        int i;
        while ( (i = bis.read(bytes)) != -1) {
            zipOut.write(bytes,0,i);
        }

        input.close();
        zipOut.close();

        request.setAttribute("message","success");
        return "upload";
    }
```
