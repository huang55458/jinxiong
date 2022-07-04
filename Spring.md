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

  创建对象后立即调用对应的set 方法完成注入

- 

