#### 使用Tomcat10导致的DispatcherServlet不可用问题

Apache为了将Tomcat10与之前的版本区分，不再使用javax，而是使用jakarta；在配置前端控制器时它导入的包依然是javax.*；解决办法：把Tomcat降级，当然官网也提供了迁移工具

在 使用Tomcat 9  `failed to parse class file - probably due to a new Java class file version that isn't supported yet` 有这个异常，再次运行时错误消失，期间只确定更改了编译字节码版本 1.7 -> 1.8

经测试，在设置-java编译器-目标字节码版本设置为 15 以下，可正常运行



#### JSTL

```
 无法在web.xml或使用此应用程序部署的jar文件中解析绝对uri：[http://java.sun.com/jsp/jstl/core]
```

tomcat 10  --- >  

```xml
<dependency>
  <groupId>org.glassfish.web</groupId>
  <artifactId>jakarta.servlet.jsp.jstl</artifactId>
  <version>2.0.0</version>
</dependency>
```

tomcat 9  --- >  1.2.6

问题仍未解决时：检查下tomcat的配置文件`catalina.properties`下面的`tomcat.util.scan.StandardJarScanFilter.jarsToSkip=\`是否修改为了`tomcat.util.scan.StandardJarScanFilter.jarsToSkip=*.jar`；如果是，则改回原来的配置即可。

因为改了这项后，将不会扫描一些必要jar包了



在 js 中提交 form表单时，不能通过如下方式设置 请求参数，只能是 input name 值；`@RequestParam(value = "answer",required = false)` 可以设置不是必须需要的参数

```js
let exam_answer = document.getElementById("exam_answer");
exam_answer.action = "saveAnswer.form?id=${param.exam_num}&answer=" + str
exam_answer.submit();
```





js 由上往下执行，变量不能定义在下方（当时使用的是 let 声明），如果是 var ，存在 变量提升，不会报错

> var在全局作用域声明的变量有一种行为会挂载在window对象上，它会创建一个新的全局变量作为全局对象的属性，这种行为说不定会覆盖到window对象上的某个属性，而`let const`声明的变量则不会有这一行为

```js
var value1 = "张三"
let value2 = "李四"
const value3 = "王五"
console.log(window.value1) // 张三
console.log(window.value2) // undefined
console.log(window.value3) // undefined
```





无法访问 js css 文件时，被 spring dispatchServlet 拦截，解决：applicationContext.xml 和 web.xml

```xml
<mvc:resources mapping="/layui/**" location="/layui/"/>
```

```xml
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.html</url-pattern>
</servlet-mapping>
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.css</url-pattern>
</servlet-mapping>
<servlet-mapping>
    <servlet-name>default</servlet-name>
    <url-pattern>*.js</url-pattern>
</servlet-mapping>
```





#### 对象 date 属性返回 json

```java
@JsonFormat(pattern = "yyyy-MM-dd hh:mm:ss")
private Date time;
```
