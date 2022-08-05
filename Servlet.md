#### Servlet



<img src=".\note_imgs\两层cs.png" style="zoom:67%;" />

两层 cs 架构优缺点：

- 特点：

  1. 数据库作为 Server，使用数据库特定的编程语言（存储过程 / 存储函数）编写业务逻辑
  2. 客户端提供操作界面和少量的业务逻辑处理（验证）

- 缺点：

  1. 移植性差（更换数据库需要重新编程）

  2. 不适合大型应用（客户端需要与数据库之间建立持续的连接）

     

<img src=".\note_imgs\三层cs1.png" style="zoom:67%;" />

<img src=".\note_imgs\三层cs2.png" style="zoom:67%;" />

三层 cs 架构优缺点：

- 特点：
  1. 数据库只负责数据的管理
  2. 应用服务器提供所有的业务逻辑的处理
  3. 客户端只负责提供操作界面
- 优点：
  1. 移植性好，适合大型应用
- 缺点：
  1. 客户端需要单独安装，开发复杂（需要自定义协议，需要编写客户端和服务器端的通信模块）



<img src=".\note_imgs\bs架构.png" style="zoom:67%;" />

bs 架构特点：

- 数据库只负责数据的管理
- Web 服务器负责业务逻辑的处理
- 浏览器负责提供操作界面

优点：

- 不需要单独安装客户端
- 开发相对于 cs 简单，客户端和服务器（如 tomcat）的通信模块都是使用标准的 HTTP 协议进行通信





- 什么是 Servlet ？

  sun（Oracle）公司制定的一种用来扩展 web 服务器的组件规范

  组件规范：在软件开发行业，符合一定规范，实现部分功能并且需要部署到容器中才能运行的软件模块

  容器：符合一定规范，提供组件运行环境的一个程序

- Servlet 有什么用？

  在 Web 服务器中编写业务逻辑

  

1. 开发登入界面
2. 创建一个 Servlet （控制器）
   - 3.0 版本之前的 Servlet 需要在 Web.xml 中手动配置访问路径
   - 3.0 版本之后无需配置，借助 @WebServlet 注解完成



###### 使用 idea 配置使用 tomcat

1. 编辑配置文件添加并配置 Tomcat Server

<img src=".\note_imgs\tomcat1.png" style="zoom:80%;" />

2. 从项目结构配置添加依赖

<img src=".\note_imgs\tomcat2.png" style="zoom: 80%;" />

Maven 中使用的依赖

```
<dependency>
  <groupId>jakarta.servlet.jsp</groupId>
  <artifactId>jakarta.servlet.jsp-api</artifactId>
  <version>3.1.0</version>
</dependency>
<dependency>
  <groupId>jakarta.servlet</groupId>
  <artifactId>jakarta.servlet-api</artifactId>
  <version>6.0.0</version>
</dependency>
```



###### 获取请求参数

- `String  getParameter(name)` ：获取提交的 Name-Value 数值
- `String[]  getParameterValues(name)` ：获取提交的 Name-Values 数值，当获取参数相同的多个参数值时使用该方法，多用于获取提交表单中的复选框的值；没有返回 null

form 表单中不同的提交方式（get 和 post）会将请求提交到不同的方法

为什么要区分请求方式 ？

请求方式是客户端对话服务器时的意向说明，是区分请求种类的关键；不同的请求方式不仅仅在数据传输时会有所不同，在表单提交及服务器处理时都会采用不同的方式，而区分不同种类的请求方式也会使得浏览器采用不同的缓冲方式处理后续请求，从而提升响应的速度

- get 请求方式

  1. 当需要向服务器请求指定的资源时使用的方式
  2. 它不应该用于一些会造成副作用的操作中（在网络应用中用它来提交请求是一种常见的错误用法）
  3. 什么情况下浏览器会发送 get 请求
     - 在地址栏输入一个地址，然后回车
     - 点击超链接
     - 表单默认提交方式
     - 重定向的第二次请求

  get 请求方式的特点：

  - 会将请求数据添加到请求资源路径的后面，所有只能提交少量的数据给 web 服务器（约 4 kb）
  - 请求参数显示在浏览器地址栏上，不安全

- post 请求方式

  - 向服务器提交需要处理的数据，这些数据写在请求内容中，可以导致新资源的产生和已有资源的更新
  - 什么情况浏览器会发送 post 请求
    1. 设置表单 method 属性为 post
  - post 请求方式的特点：
    - 请求参数添加到实体内容中，可以提交大量数据，理论上没有限制
    - 不会将请求参数显示在浏览器地址栏中，相对安全



###### HttpServletRequest

`HttpServletRequest`封装了一个HTTP请求，它实际上是从`ServletRequest`继承而来。最早设计Servlet时，设计者希望Servlet不仅能处理HTTP，也能处理类似SMTP等其他协议，因此，单独抽出了`ServletRequest`接口，但实际上除了HTTP外，并没有其他协议会用Servlet处理，所以这是一个过度设计

- getMethod()：返回请求方法，例如，`"GET"`，`"POST"`；
- getRequestURI()：返回请求路径，但不包括请求参数，例如，`"/hello"`；
- getQueryString()：返回请求参数，例如，`"name=Bob&a=1&b=2"`；
- getParameter(name)：返回请求参数，GET请求从URL读取参数，POST请求从Body中读取参数；
- getContentType()：获取请求Body的类型，例如，`"application/x-www-form-urlencoded"`；
- getContextPath()：获取当前Webapp挂载的路径，对于ROOT来说，总是返回空字符串`""`；
- getCookies()：返回请求携带的所有Cookie；
- getHeader(name)：获取指定的Header，对Header名称不区分大小写；
- getHeaderNames()：返回所有Header名称；
- getInputStream()：如果该请求带有HTTP Body，该方法将打开一个输入流用于读取Body；
- getReader()：和getInputStream()类似，但打开的是Reader；
- getRemoteAddr()：返回客户端的IP地址；
- getScheme()：返回协议类型，例如，`"http"`，`"https"`；

###### HttpServletResponse

`HttpServletResponse`封装了一个HTTP响应。由于HTTP响应必须先发送Header，再发送Body，所以，操作`HttpServletResponse`对象时，必须先调用设置Header的方法，最后调用发送Body的方法。

常用的设置Header的方法有：

- setStatus(sc)：设置响应代码，默认是`200`；
- setContentType(type)：设置Body的类型，例如，`"text/html"`；
- setCharacterEncoding(charset)：设置字符编码，例如，`"UTF-8"`；
- setHeader(name, value)：设置一个Header的值；
- addCookie(cookie)：给响应添加一个Cookie；
- addHeader(name, value)：给响应添加一个Header，因为HTTP协议允许有多个相同的Header；

写入响应时，需要通过`getOutputStream()`获取写入流，或者通过`getWriter()`获取字符流，二者只能获取其中一个。

写入响应前，无需设置`setContentLength()`，因为底层服务器会根据写入的字节数自动设置，如果写入的数据量很小，实际上会先写入缓冲区，如果写入的数据量很大，服务器会自动采用Chunked编码让浏览器能识别数据结束符而不需要设置Content-Length头。

但是，写入完毕后调用`flush()`却是必须的，因为大部分Web服务器都基于HTTP/1.1协议，会复用TCP连接。如果没有调用`flush()`，将导致缓冲区的内容无法及时发送到客户端。此外，写入完毕后千万不要调用`close()`，原因同样是因为会复用TCP连接，如果关闭写入流，将关闭TCP连接，使得Web服务器无法复用此TCP连接。

 **写入完毕后对输出流调用flush()而不是close()方法**




###### 乱码

为什么表单提交中文会出现乱码 ？

当表单提交时，浏览器会以中文参数进行编码（会使用打开表单所在的页面时的字符集进行编码）

通常网页使用 utf-8 进行编码与解码，而Servlet 使用 iso-8859-1 进行编码与解码

```java
 req.setCharacterEncoding("utf-8");    				// 解码格式
 resp.setContentType("text/html;charset=utf-8");	// 编码格式
```

如果需要转码

```java
String name =new String(request.getParameter("name").getBytes("ISO8859-1"),"UTF-8");
```



###### Web.xml 文件

```xml
  <servlet>
    <servlet-name>HelloForm</servlet-name>
    <servlet-class>com.runoob.test.HelloForm</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>HelloForm</servlet-name>
    <url-pattern>/TomcatTest/HelloForm</url-pattern>
  </servlet-mapping>
  

    <welcome-file-list>
    <welcome-file></welcome-file>
  </welcome-file-list>  //设置首页
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--1.注册servlet-->
    <servlet>
        <servlet-name>SpringMVC</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--通过初始化参数指定SpringMVC配置文件的位置，进行关联-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml</param-value>
        </init-param>
        <!-- 启动顺序，数字越小，启动越早 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <!--所有请求都会被springmvc拦截 -->
    <servlet-mapping>
        <servlet-name>SpringMVC</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```

需要保持 servlet-name 一致形成对应；url-pattern 的值相当于 @WebServlet 注解中 url-patterns 的值；servlet-class 为运行此服务的类

/*会把所有请求拦截包括.jsp，/不会拦截jsp（未测试）

###### WEB-INF

<img src=".\note_imgs\WEB-INF.png" style="zoom:80%;" />

- `WEB-INF`是一个特殊目录，Web Server会阻止浏览器对`WEB-INF`目录下任何资源的访问，这样就防止用户通过`/user.jsp`路径直接访问到JSP页面





###### Servlet 运行过程

1. 浏览器依据 IP 建立与容器的连接
2. 浏览器请求数据打包
3. 容器解析请求数据包，封装对象
4. 容器依据路径找到 Servlet 创建对象
5. 容器调用 Servlet 对象的 service 方法
6. 容器响应打包发送给浏览器
7. 浏览器取出结果，生成页面



###### 转发与重定向

什么是重定向 ？

服务器向浏览器发送一个 302 状态以及一个 Location 消息头（该消息头的值是一个地址，称为重定向地址），浏览器接收后会立即向重定向地址发出请求

使用响应对象的 api 方法即可实现重定向的过程

```java
resp.sendRedirect("search");
```

<img src=".\note_imgs\redirect.png" style="zoom:80%;" />

该段代码使得响应数据包中数据发生如下变化：

<img src="E:\github\note_imgs\redirect02.png" style="zoom:80%;" />

重定向的特点：

1. 整个过程中浏览器发送了两次请求
2. 浏览器的第二次请求为 get 请求方式
3. 重定向的地址可以是任意的地址
4. 重定向之后，浏览器地址栏中的地址会发生改变
5. 重定向过程中涉及到的 web 组件并不会共享同一个 request 和 response



Web 服务器对请求地址的处理过程

在浏览器地址栏输入 http://ip:port/appName/xxx.html

1. 浏览器依据 ip，port 建立与 tomcat 控制器之间的连接，然后将请求资源路径 appName/xxx.html 发送给 Tomcat
2. Tomcat 依据应用名找到应用所在的文件，Tomcat 会默认请求的是一个 Servlet ，然后查找了 Web.xml 文件中所有的 Servlet 配置 URL-pattern，查找对应的 Servlet



什么是转发 ？

一个 Web 组件（Servlet / jsp）将未完成的处理通过容器转发给另外一个 Web 组件继续完成

常见情况：一个 Servlet 获得数据之后，将这些数据转发给一个 jsp ，由这个 jsp 来展现这些数据



如何实现转发 ？

1. 绑定数据到 request 对象

   `request.setAttribute(String name,Object obj)` 

   - 读取绑定值

     `Object request.getAttribute(String name)` ：如果绑定名对应的值不存在，则返回 null

2. 获得转发器

   ​     `RequestDispatcher rd = request.getRequestDispatcher(String path);` ：path：转发目的地，即将未完成的处理继续下去的另一个组件，比如一个 jsp 文件

3. 转发

   `rd.forward(request,response);`

   通常写为：     `request.getRequestDispatcher("login_success.jsp").forward(request, response);`

小结：

1. 可以携带数据；转发所涉及的各个 web 组件会共享同一个 request 和 response 对象
2. 地址栏地址不改变；原因转发的过程是发生服务器的内部的，浏览器并不知道
3. 浏览器只发送一次请求
4. 不可以跨服务器；转发所涉及的各个 web 组件 必须是同一个应用内部的某个地址

转发和重定向的区别

- 重定向是浏览器发送请求并收到响应以后再次向一个新地址发送请求，转发是服务器收到请求后未完成响应转到一个新的地址
- 重定向中有两个请求对象，不共享数据；转发只产生一次请求对象且在组件间共享数据
- 重定向后地址栏地址发生改变，而转发则不会
- 重定向的新地址可以是任意地址，转发到的新地址必须是同一个应用内的某个地址





###### 匹配 Servlet 规则

匹配 Servlet 规则 -- 精确匹配

通过将请求资源路径中的具体资源名称与 Web.xml 文件中 “ url-pattern ” 进行对比，严格匹配相等后找到对应资源并执行

尽管应用中 list.html 这个具体的页面，也会去执行该 url-pattern 为 list.html 的 Servlet，而不是返回 list.html 页面

匹配 Servlet 规则 -- 通配符匹配

- `* `：0 ~ 多个字符
  - `url-patterns="/*"` ：匹配任意路径

- 优先匹配精确匹配

- 映射到`/`的`IndexServlet`比较特殊，它实际上会接收所有未匹配的路径，不相当于`/*`



匹配 Servlet 规则 -- 后缀匹配

- `* ----  ` ：不能使用斜杆开头，使用 “ * ” 开头的多个任意多个字符
  - `*.do` ：匹配所有以 `.do` 结尾

ps：优先级：精确匹配 > 通配符匹配 > 后缀匹配



无匹配的 Servlet 的处理

如果精确匹配、通配符匹配、后缀匹配都没有成功时，容器会查找相应的文件；查找到对应的文件则返回；找不到则返回 404

**会最优先加载 root 目录下的 index 文件**

- 404 错误：服务器执行完客户端的请求后，返回给客户端的一个执行的状态编码

  - 产生的原因：Web 服务器根据请求地址找不到对应的资源
    1. 地址写错（拼写不正确，字母大小写，输入法不对）
    2. Web.xml 文件中的两个 <servlet-name> 不一致
    3. 工程没有部署
    4. Web 应用程序部署结果没有遵守 Servlet 规范

  <img src=".\note_imgs\web_xml.png" style="zoom:80%;" />

- 405 

  - 产生的原因：Web 服务器找不到 Service 方法处理请求
    1. service 方法名称写错
    2. service 方法参数类型与标准不一致
  - 解决方法：
    1. 检查 service() 是否存在
    2. 检查 service() 的签名（方法名、参数、返回值、异常类型）

- 500

  - 产生原因：程序在运行过程中错误
    - Servlet 类没有继承 HttpServlet 或实现 Servlet 接口
    - Web.xml 文件中的 <servlet-class> 写错 （包名、类名写错）
  - 解决办法：检查代码是否运行错误



###### Servlet 的生命周期

什么是 Servlet 的生命周期？

容器（Web 服务器）如何创建 Servlet 对象，如何为 Servlet 对象分配资源，如何调用 Servlet 对象的方法来处理请求，以及如何销毁 Servlet 对象的整个过程

生命周期的四个阶段

1. 实例化（容器调用 Servlet 的构造器创建一个Servlet 对象）

   - 什么时候实例化

     - 情形1：开始容器里面没有 Servlet 对象，收到请求后创建 Servlet 对象

     - 情形2：容器启动之后就立即创建相应的实例（用 `loadOnStartup = 1` 设置）

       ```java
       @WebServlet(urlPatterns = "/lifecycle",loadOnStartup = 1)
       ```

2. 初始化

   - 什么时候初始化

     - 容器在创建好 Servlet 对象之后，会立即调用该对象的 init 方法

     - 一般情况下，我们不用写 init 方法，genericServlet 已经提供 init 方法的实现（将容器传递过来的 ServletConfig 对象保存下来，并且，提供了 getServletConfig 方法来获得 ServletConfig 对象；该对象是每一个 Servlet 程序创建时，就创建一个对应的 ServletConfig 对象）

       - ServletConfig 的作用：
         1. 获取初始化参数 init-param 中参数名对应的参数值
         2. 获取 ServletContext 上下文对象
         3. 可以获取 Servlet 程序的别名 <Servlet-name> 的值
     
       ```java
       ServletConfig sc = getServletConfig();
       String name = sc.getInitParameter("address");
       String value = sc.getInitParameter("address");
       System.out.println(name + " : " + value);
       ```
     
     - init 方法只会执行一次

3. 就绪

   - 容器收到请求之后调用 Servlet 对象的 service() 来处理请求

4. 销毁

   - 容器依据自身的算法删除 Servlet 对象，删除前会调用 destroy() 方法

   - 只会执行一次

   - 可以 重写 destroy() 来实现自己的处理逻辑

   - 应用程序卸载时一定会调用 destroy() 

<img src=".\note_imgs\servletLifecycle.png" style="zoom:80%;" />



###### GenericServlet

<img src=".\note_imgs\ServletRelation.png" style="zoom:80%;" />

- genericServlet 抽象类实现了 Servlet 接口的部分方法（init() 和 destroy() ）

- HttpServlet 抽象类 继承了GenericServlet ，实现了 service() ，init() 在 service() 调用之前调用，容器会先创建 ServletConfig 对象，然后把 ServletConfig 对象传给 init() 

- init() 执行完之后，ServletConfig 对象会消失，所以 this.config = config 可以保存对象的引用



###### Servlet 上下文

容器启动之后，会为每一个 Web 应用创建一个 唯一的一个符合 Servlet 接口要求的对象，该对象就是 Servlet 上下文，特点：

- 唯一性（一个 Web 应用对应一个 ServletContext）
- 一直存在（只要容器不关闭并且应用没有停止，Servlet 上下文一直存在）

如何获取？

1. 通过 GenericServlet 提供的 getServletContext()
2. 通过 ServletConfig 对象提供的 getServletContext()
3. 通过 HttpSession 提供的 getServletContext()
4. 通过 FilterConfig 提供的 getServletContext()



记录访问记录

```java
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletContext context =  this.getServletContext();
        if (context.getAttribute("count") == null) {
            context.setAttribute("count",1);
        } else {
            int num = (int) context.getAttribute("count");
            context.setAttribute("count",++num);
        }
        System.out.println("访问人数：" + context.getAttribute("count"));
    }
```



x 1    @PostMapping("zipUploadFile.form")2    public String zipUploadFile(@RequestParam("file") MultipartFile uploadFile, HttpServletRequest request) throws IOException {3        if (uploadFile.isEmpty()) {4            request.setAttribute("message","空文件");5            return "upload";6        }7​8        String path = request.getSession().getServletContext().getRealPath("zip");9        String name = uploadFile.getOriginalFilename();10        System.out.println(path);11​12        File file = new File(path);13        if ( !file.exists()) {14            file.mkdir();15        }16​17        // 压缩18        InputStream input = uploadFile.getInputStream();19        BufferedInputStream bis = new BufferedInputStream(input);20        File zipFile = new File(path + File.separator + name + ".zip");21        ZipOutputStream zipOut = new ZipOutputStream(new FileOutputStream(zipFile));22        assert name != null;23        zipOut.putNextEntry(new ZipEntry(name));24        zipOut.setComment("test");25​26        // 设置缓存大小 1M27        byte[] bytes = new byte[1048576];28        int i;29        while ( (i = bis.read(bytes)) != -1) {30            zipOut.write(bytes,0,i);31        }32​33        input.close();34        zipOut.close();35​36        request.setAttribute("message","success");37        return "upload";38    }java

测试生命周期

```java
@WebServlet(urlPatterns = "/lifecycle",initParams = {@WebInitParam(name = "address",value = "南昌")})
public class LifeCycleServlet  extends HttpServlet {
    public LifeCycleServlet() {
        System.out.println("Constructor is running ... ");
    }

    @Override
    public void init(ServletConfig config) throws ServletException {
        // 一定要调用
        super.init(config);
        System.out.println(" init is running .......");
    }

    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        System.out.println("service is running ... ");
        ServletConfig sc = getServletConfig();
        String name = sc.getInitParameter("address");
        String value = sc.getInitParameter("address");
        System.out.println(name + " : " + value);
    }

    @Override
    public void destroy() {
        super.destroy();
        System.out.println("destroy is running ... ");
    }


}
```



###### @WebServlet 注解

| 属性名         | 类型            | 标签                                                         | 描述                                                         | 是否必需 |
| -------------- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- |
| name           | String          | xxxxxxxxxx38 1    @PostMapping("zipUploadFile.form")2    public String zipUploadFile(@RequestParam("file") MultipartFile uploadFile, HttpServletRequest request) throws IOException {3        if (uploadFile.isEmpty()) {4            request.setAttribute("message","空文件");5            return "upload";6        }7​8        String path = request.getSession().getServletContext().getRealPath("zip");9        String name = uploadFile.getOriginalFilename();10        System.out.println(path);11​12        File file = new File(path);13        if ( !file.exists()) {14            file.mkdir();15        }16​17        // 压缩18        InputStream input = uploadFile.getInputStream();19        BufferedInputStream bis = new BufferedInputStream(input);20        File zipFile = new File(path + File.separator + name + ".zip");21        ZipOutputStream zipOut = new ZipOutputStream(new FileOutputStream(zipFile));22        assert name != null;23        zipOut.putNextEntry(new ZipEntry(name));24        zipOut.setComment("test");25​26        // 设置缓存大小 1M27        byte[] bytes = new byte[1048576];28        int i;29        while ( (i = bis.read(bytes)) != -1) {30            zipOut.write(bytes,0,i);31        }32​33        input.close();34        zipOut.close();35​36        request.setAttribute("message","success");37        return "upload";38    }java | 指定 Servlet 的 name 属性。 如果没有显式指定，则取值为该 Servlet 的完全限定名，即包名+类名。 | 否       |
| value          | String[ ]       | <url-pattern>                                                | 该属性等价于 urlPatterns 属性，两者不能同时指定。 如果同时指定，通常是忽略 value 的取值。 | 是       |
| urlPatterns    | String[ ]       | <url-pattern>                                                | 指定一组 Servlet 的 URL 匹配模式。                           | 是       |
| loadOnStartup  | int             | <load-on-startup>                                            | 指定 Servlet 的加载顺序。                                    | 否       |
| initParams     | WebInitParam[ ] | <init-param>                                                 | 指定一组 Servlet 初始化参数。                                | 否       |
| asyncSupported | boolean         | <async-supported>                                            | 声明 Servlet 是否支持异步操作模式。                          | 否       |
| description    | String          | <description>                                                | 指定该 Servlet 的描述信息。                                  | 否       |
| displayName    | String          | <display-name>                                               | 指定该 Servlet 的显示名。                                    | 否       |

```java
@WebServlet(asyncSupported = true, name = "myServlet", description = "name描述", loadOnStartup = 1, urlPatterns = {
        "/MyServlet", "/*" }, initParams = {
                @WebInitParam(name = "编程帮", value = "www.biancheng.net", description = "init参数1"),
                @WebInitParam(name = "京东", value = "www.jd.com", description = "init参数2") })
```

- @WebServlet 注解配置 Servlet
  - 优点：@WebServlet 直接在 Servlet 类中使用，代码量少，配置简单。每个类只关注自身业务逻辑，与其他 Servlet 类互不干扰，适合多人同时开发
  - 缺点：Servlet 较多时，每个 Servlet 的配置分布在各自的类中，不便于查找和修改

- web.xml 配置文件配置 Servlet
  - 优点：集中管理 Servlet 的配置，便于查找和修改
  - 缺点：代码较繁琐，可读性不强，不易于理解



###### 线程安全

为什么会有线程安全问题？

容器收到请求后，会启动一个线程来进行相应的处理；默认情况下，容器会为每个 Servlet 创建一个实例

如果同时又有多个请求访问同一个 Servlet ，则肯定会有多个线程去访问这个Servlet 的实例，如果这些线程要修改 Servlet 实例的某个属性就有可能发生线程安全问题

归纳：多线程（多个请求）同时访问一个 Servlet （单例）



使用：`synchronized(this){  }`

```java
    private int count = 0;
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        synchronized (this) {
            count++;
            try {
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + count);
        }
    }
```

Apache-jmeter 压力测试

<img src=".\note_imgs\jmeter.png" style="zoom:67%;" />

<img src=".\note_imgs\jmeter2.png" alt="jmeter2" style="zoom:67%;" />

<img src=".\note_imgs\jmeter3.png" alt="jmeter3" style="zoom:67%;" />

<img src=".\note_imgs\jmeter4.png" alt="jmeter4" style="zoom:67%;" />

start -> 模拟多用户访问



###### Servlet 找不到 mysql 驱动

项目结构中进行如下设置（结果图）

<img src=".\note_imgs\servlet_connect1.png" style="zoom:80%;" />



#### JSP

Servlet 技术产出以后，在使用时最麻烦的是使用大量的 out.println 语句输出页面，这样的形式在系统变更，维护，预览效果时都不能方便快捷的完成任务，于是推出 JSP 这种技术用来将 Servlet 中负责显示的语句抽取出来

什么是 JSP ？

Sun 公司制定的一种服务器端动态页面技术的组件规范；Jsp 是一个以 .jsp 为后缀的文件，在该文件中，主要为 HTML 和 少量的 Java 代码，JSP 文件会被容器转换为一个 Servlet 类，然后执行

注释：

```jsp
<%--test--%> jsp 特有注释，里面的 java 代码会被忽略
<!--test--> html注释，注释中的内容如果包含 java 代码，这些代码会被执行
<%
  // test
    
    /*
    test
     */

    /**
     * test
     */
%>
```

jsp 页面中的 HTML 代码像编写 HTML 页面一样编写即可；转译成 Servlet 时的规则：成为 Servlet 中 service() 的 out.write 语句



###### 使用

jsp 页面中的 java 代码

1. jsp 表达式

   - 语法规则：<%=...%>

   - 合法内容：变量、变量加运算符组成的表达式，有返回值的方法

   - 转译成Servlet 时规则：在 service() 中用 out.print 语句输出该变、表达式、方法的值

     ```jsp
     <p>1+1=<%=1+1%></p>
     转换成：
     out.write("<p>1+1=");
     out.print(1+1);
     out.write("</p>p>");
     ```

2. jsp 小脚本

   -  语法规则：`<% ... %>`
   - 合法内容：能够写在方法里的 java 代码片段都可以作为小脚本；转译成 Servlet 时的规则：原封不动的成为 Servlet 类的 service() 里的一段代码

3. jsp 声明

   - 语法规则：`<%! ... %>` 
   - 合法内容：成员属性或成员方法的声明
   - 转译成 Servlet 时的规则：成为 jsp 页面转译成的 Servlet 类中的成员变量或成员方法

编写位置：页面中任意位置

作用：控制页面中可变内容的产生



1. jsp 本质就是 servlet

   因为 jsp 其转换成的对应的 java 类继承了HttpjspBase ，而 HttpJspBase 又是 HttpServlet 的子类

2. jsp 中内置 9 个对象，已经实例化，直接拿过来使用即可

   ```java
       final jakarta.servlet.jsp.PageContext pageContext;
       jakarta.servlet.http.HttpSession session = null;
       final jakarta.servlet.ServletContext application;
       final jakarta.servlet.ServletConfig config;
       jakarta.servlet.jsp.JspWriter out = null;
       final java.lang.Object page = this;
       jakarta.servlet.jsp.JspWriter _jspx_out = null;
       jakarta.servlet.jsp.PageContext _jspx_page_context = null;
   ```

   文件路径在项目输出日志找，如：`C:\Users\chumeng\AppData\Local\JetBrains\IntelliJIdea2021.3\tomcat\a40a9cb2-3192-495e-9c79-a634865c760b` 



###### 三大指令

- 语法规则：`<%@指令名  属性=值%>`

- 常用指令

  - page：用于导包、设置页面属性

    ```jsp
    <%@ page import="java.util.List" %>
    ```

    ```jsp
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <%@ page pageEncoding="utf-8"%> 设置容器读取该文件的解码
    ```

  - include：在 jsp 页面转换成 Servlet 时，能够将其他文件包含进来，可以包含 jsp 文件也可以是静态 HTML 文件；通过该语句能方便的在每个 jsp 页面中包含导航栏、版权声明、logo ...

    - 语法：`<%@ include file="url"%>`

    Ps：include 指令引入 HTML 文件时出现乱码原因

    缺少了 html 指定从 jsp 翻译为 java 文件时规定的编码；`<%@ include file="url"%>` 方式包含文件时，tomcat 默认采用 ISO-8859-1 来读取被包含的文件，如果包含的文件中有中文就会乱码，所以需要在被引入的文件中指定其 pageEncoding，告知 tomcat 其解码方式

  - taglib：

    ```xml
    <dependency>
      <groupId>org.glassfish.web</groupId>
      <artifactId>jakarta.servlet.jsp.jstl</artifactId>
      <version>2.0.0</version>
    </dependency>
    ```

- 作用：控制 jsp 在转译成 Servlet 类时 生成的内容



jsp 中的各种编码

- pageEncoding 是 jsp 文件的本身的编码，是指定 web 容器将 jsp 编译成 java 文件时采用解码的方式（相对 tomcat）

- contextType 的 charset 设置的编码是指服务器响应给客服端的内容编码（相对浏览器）

- html 中 charset 指定的编码格式作用是告诉客户端展示这个html 文件时使用 utf-8 编码 （相对浏览器）

<img src=".\note_imgs\jsp_Encoding.png" style="zoom:80%;" />



###### 九大内置对象

容器自动创建，在 jsp 文件中可以直接使用的对象；作用：jsp 预先创建的这些对象可以简化对 HTTP 请求、响应信息的访问

<img src=".\note_imgs\jsp_Object.png" style="zoom:80%;" />

|             |                     |                      |
| ----------- | ------------------- | -------------------- |
| request     | HttpServletRequest  | 请求信息             |
| response    | HttpServletResponse | 响应信息             |
| out         | JSPWriter           | 输出的数据流         |
| session     | HttpSession         | 会话                 |
| application | ServletContext      | 全局的上下文对象     |
| pageContext | PageContext         | JSP 页面的上下文对象 |
| page        | Object              | JSP 页面本身         |
| config      | ServletConfig       | Servlet 配置对象     |
| exception   | Throwable           | 捕获网页异常         |

###### 四大域对象

作用域从小到大：

- page（当前页面）：page域指的是pageContext(前后端分离模式下基本不用了）
- request（一次请求）：request域指的是HttpServletContext
  - web 应用程序的 A 组件转发到 B 组件，request 携带的数据只能使用一次
- session（一次会话，默认为浏览器一开一关）：session域指的是HttpSession
- application（整个项目运行期间）：application域指的是application 、ServletContext

之所以他们是域对象，原因是他们都内置了map集合，都有setAttribute和getAttribute方法



###### 异常处理

编程式异常处理

使用转发跳转到指定页面进行提示说明

```java
try{

} catch (Exeception e) {
	request.getRequestDispatcher("url").forward(req,resp);
}
```

容器中声明式处理：

1. 将异常抛给容器，但底层的错误提示不要返回给用户
   - 注意：异常只允许抛 service 指定的异常，不能超出指定范围
   - `throw  new  ServletExeception(e)；`

2. 在 web.xml 文件中配置错误处理页面节点

   ```jsp
     <error-page>
       <exception-type>jakarta.servlet.ServletException</exception-type>
       <location>/url</location>
     </error-page>
   ```

   ```java
           try {
               int i = 1/0;
           } catch (Exception e) {
   //            String str = e.toString();
   //            req.setAttribute("err",str);
   //            req.getRequestDispatcher("url").forward(req,resp);
               // 将异常抛给容器，在 web.xml 中添加声明，变成声明式处理异常
               throw new ServletException(e);
           }
   ```



###### 路径问题

- 链接地址：`<a href="url"></a>`
- 表单提交：`<form action="url"></form>`
- 重定向：`resp.sendRedirect("url")`
- 转发：`req.getRequestDispatcher("url")`



- 相对路径：从当前文件出发到达目标文件所经过的路径叫做相对路径
- 绝对路径：以 “ / ” 开头，不以当前文件的位置作为起始，而是以一个固定位置（项目结构中的webapp下）作为起始到达目标文件所经过的路径 



- `String request.getContextPath()` ：获取应用的实际部署名称



###### MVC

<img src=".\note_imgs\MVC.png" style="zoom:80%;" />



###### 状态管理- Cookie

为什么需要状态管理 ？

web 应用程序使用 http 协议通信，而Http 协议是 “ 无状态 ” 协议，即服务器一旦响应完客户的请求之后，就断开连接，而同一个客户下一次请求将重新建立网络连接；服务器应用程序有时需要判断是否为同一个客户发出的请求，比如客户端的多次选购商品，因此，有必要跟踪同一个客户发出的一系列请求

什么是状态管理？

将客户端（浏览器）与服务器之间多次交互当做一个整体来看（一次请求，一次响应），并且将多次交互所涉及的数据即状态保存下来；状态指的是数据；管理指的是多次交互对数据的修改

状态管理的两种常见模式

- 客户端状态管理技术：将状态保存在客户端，代表性的是 cookie 技术
- 服务器状态管理技术：将状态保存在服务器，代表性的是 session 技术

**Cookie**

浏览器向 web 服务器发送请求是：服务器会将少量的数据以 set_Cookie 消息头的方式发送给浏览器，浏览器将这些数据保存下来；当浏览器在访问服务器时，会将这些数据以 cookie 消息头的方式发送给服务器

```java
        Cookie cookie1 = new Cookie("test","root");
        resp.addCookie(cookie1); // 将 Cookie 添加到响应体中返回给浏览器
```

第一次和第二次访问页面时 cookie 的变化 

<img src=".\note_imgs\cookie.png" style="zoom:80%;" />

<img src=".\note_imgs\cookie2.png" style="zoom:80%;" />

第二次对页面进行访问，会在请求数据包中会自动发送存储的Cookie 到服务器端，由于请求的 Servlet 中会执行 resp.addCookie 这样的语句，所以在响应数据包中还是会以 setCookie 的头信息发回添加到 resp 中的信息

关掉浏览器，刚刚保存在浏览器内存中的 cookie 会随着浏览器的关闭自动销毁



- `Cookie[]  req.getCookies()` ：获取客户端发送的所有 Cookie 对象
- `String  cookie.getName()` 
- `String  cookie.getValue()`

```java
        req.setCharacterEncoding("utf-8");
        resp.setCharacterEncoding("utf-8");
        resp.setContentType("test/html");

        Cookie cookie1 = new Cookie("user","root");
        Cookie cookie2 = new Cookie("pass","root");

        resp.addCookie(cookie1);
        resp.addCookie(cookie2);

        Cookie[] cookies = req.getCookies();
        System.out.println(cookies.length);
        System.out.println(req.getContextPath());
        for (Cookie cookie : cookies) {
            System.out.println(cookie.getName()+cookie.getValue());
        }
```



修改 cookie

1. 获取客户端发送的所有 cookie
2. 根据 name 找到要修改的 cookie（可以设置多个 name 值，所有值会以 Set-Cookie 发送给浏览器，浏览器发送给服务端的 cookie 值不会重复，后设置的 value 值会覆盖前面的）
3. 调用 cookie 的 setValue(String value) 修改 cookie 的值
4. 将修改的 cookie 加入到 response 发送回客户端（同名 cookie 到达客户端后会覆盖客户端中的同名 cookie，等同于修改）

```java
        for (Cookie cookie : cookies) {
            if ("user".equals(cookie.getName()) ) {
                cookie.setValue("newValue");
                resp.addCookie(cookie);
            }
        }
```



cookie 的流程

第一次访问 addCookie 时，在响应头信息中使用 Set-Cookie 将 cookie 传送到客户端，浏览器在内存中保存了 cookie 信息，接下来访问 updateCookie 时，在请求头信息中，使用 cookie 将内存中的 cookie 信息发送到了服务器端，当服务器端执行到 response.addCookie(cookie) 时，就有一堆新数据的 cookie 通过响应的 Set-Cookie 发送回了客户端，于是同名 cookie 覆盖类原有信息，实现修改功能



Cookie  的生存时间

默认情况下，浏览器会将 cookie 保存在内存中，只要浏览器不关闭，cookie 就一直存在；如果希望关闭浏览器后 cookie 仍然存在，可以通过设置过期时间来实现

`void  cookie.setMaxage(int seconds)`：

- seconds > 0 ：保存在硬盘上的时间
- seconds = 0 ：立即删除
  - 删除 cookie ，在修改 cookie 的生存时间为 0 后，随着 response 发送回客户端，替换原有 cookie ，因 segment 周期到了即将该 cookie 删除
- seconds < 0 ：默认，保存在浏览器内存中（缺省值，浏览器会将 cookie 保存在内存中，浏览器关闭，cookie  消失）

```java
Cookie cookie1 = new Cookie("name","root");
Cookie cookie2 = new Cookie("pass","root");

cookie1.setMaxAge(180);

resp.addCookie(cookie1); // 时间一过浏览器将此 cookie 删除，即使浏览器未关闭
resp.addCookie(cookie2); // 关闭浏览器这个cookie 将被删除
```

查看 cookie 方法：（网页控制台：`alert(document.cookie)`）



Cookie 中文问题

response.addCookie(cookie) 如果c 中包含中文 ：IllegalArgumentException 

解决方案：

- 使用某种特定的编码格式将中文转换成符合要求的数据，使用时按照特定的编码格式将其解码还原为中文

- cookie只能保存合法的 ASCII 字符，如果要保存中文，需要将中文转换成合法的 ASCII 字符，即编码
- 编码后的 cookie 为了看到实际的中文，需要还原后再显示



编码：

```java
// 先将中文进行 utf-8 编码，在将编码后的字节数组转换为字符串  （错的）
        String str = URLEncoder.encode("南昌", StandardCharsets.UTF_8);
        System.out.println(str);  // %E5%8D%97%E6%98%8C

        Cookie cookie = new Cookie("pass",str);
        resp.addCookie(cookie);
```

解码：

```java
Cookie[] cookies = req.getCookies();
if (cookies != null) {
    for (Cookie cookie : cookies) {
        System.out.println(cookie.getName() + ":" + URLDecoder.decode(cookie.getValue(), StandardCharsets.UTF_8));
    }
}
```

原理：cookie 使用 ASCII 编码，ASCII 码不支持中文，因此 cookie 值写入中文将直接报错；此时利用 URLEnCoder.encode() 将中文编码成一串全英文字符串（如 %E5%8D%97%E6%98%8C ），此临时字符串将可以写入 cookie，在浏览器接收到的 cookie 也是此临时字符串，此时可利用 URLDecoder.decode() 将临时字符串反编码为正确的字符串



cookie 的路径问题

浏览器在访问服务器上的某个地址时，会比较 cookie 的路径与该路径是否匹配，只有匹配的 cookie 才会发送给服务器；cookie 的默认路径等于添加这个 cookie 的 web 组件的路径

`http://localhost://a/b/c.jsp  -->  /a/b/`

发送 cookie 的条件：要访问的地址必须是 cookie 的路径或者是其子路径时，浏览器才会发送 cookie

`void cookie.setPath(String url)`

结论：只有访问的当前路径与 cookie 路径一致或是其子目录时，浏览器才会发送 cookie；需要修改 cookie 路径时 使用 setPath() 



Cookie 的限制

cookie 可以被用户禁止

cookie 会将状态保存在浏览器端，不安全，对于敏感数据，需要加密后再使用 cookie 来保存

cookie 只能保存少量的数据，约 4 kb

cookie 的个数是有限制的

cookie 只能保存字符串



###### session

什么是 session ？

浏览器访问 web 服务器时，服务器会为每一个浏览器在服务器端的内存中分配空间，单独创建一个 Session 对象，该对象有一个 id 属性，其值唯一，一般称之为 SessionId，并且服务器会将这个 SessionId （使用cookie 的方式）发送给服务器，浏览器再次访问服务器时会将 JSessionId 发送给服务器，服务器可以依据 JSessionId 找到对应的Session 对象

<img src=".\note_imgs\session流程.png" style="zoom: 67%;" />



如何获得 session ？

xxxxxxxxxx46 1<table style="border-collapse: collapse">2    <tr><th>id</th><th>username</th><th>age</th></tr>3    <tr th:each="user : ${users}">4        <td th:text="${user.id}"></td>5        <td th:text="${user.username}"></td>6        <td th:text="${user.age}"></td>7    </tr>8</table>9<hr>10<table style="border-collapse: collapse">11    <tr><th>id</th><th>username</th><th>age</th>12        <th>index</th><th>count</th><th>size</th>13        <th>even</th><th>odd</th><th>first</th><th>last</th></tr>14    <tr th:each="user,var : ${users}">15        <td th:text="${user.id}"></td>16        <td th:text="${user.username}"></td>17        <td th:text="${user.age}"></td>18        <td th:text="${var.index}"></td>19        <td th:text="${var.count}"></td>20        <td th:text="${var.size}"></td>21        <td th:text="${var.even}"></td>22        <td th:text="${var.odd}"></td>23        <td th:text="${var.first}"></td>24        <td th:text="${var.last}"></td>25    </tr>26</table>27<hr>28<table style="border-collapse: collapse">29    <tr><th>address</th></tr>30    <tr th:each="user : ${users}">31        <td th:text="${user}"></td>32    </tr>33</table>34<hr>35<table style="border-collapse: collapse">36    <tr><th>id</th><th>username</th><th>age</th></tr>37    <tr th:each="user : ${users}">38        <td th:each="entry:${user}" th:text="${user.id}"></td>39        <td th:each="entry:${user}" th:text="${user.username}"></td>40        <td th:each="entry:${user}" th:text="${user.age}"></td>41    </tr>42</table>43<hr>44request:<span th:text="${#request.getAttribute('request')}"></span><br>45session:<span th:text="${session.session}"></span><br>46application:<span th:text="${application.servletContext}"></span>html

HttpSession 是一个接口，后面返回的是符合接口规范的对象；当 flag 为 true 时：先查看请求中有没有 SessionId ，如果没有 SessionId，服务器创建一个 Session 对象；如果有 SessionId，依据 SessionId 查找对应的 Session 对象，找到则返回，找不到则创建一个新的 Session 对象，所以flag 为 true时，一定能得到一个 Session 对象；当 flag 为 false时，没有 SessionId 及有 SessionId 但没有找到 Session 对象，均返回 null，找到则返回

```java
// 获取 Session 对象
HttpSession session = req.getSession();
System.out.println(session.getId());
Object object = session.getAttribute("count");
if (object == null) {
    session.setAttribute("count",1);
} else {
    int num = (int)object;
    session.setAttribute("count",++num);
}
System.out.println((int)session.getAttribute("count"));
```

sessionId 跟随一次会话，存储在客户端浏览器的内存中一个 JsessionId 的 cookie 里面第一次创建后，只要不关闭浏览器，那么每一次的请求 SessionId 都会被自动发送到服务器端；重启浏览器后，sessionId 会被销毁，重新创建新的数值

- `void  session.removeAttribute(String name)` 

- `void  session.invalidate()` ：使该 session 失效



```java
HttpSession session = req.getSession();

// 设置 session 生命周期
String sessionId = session.getId();
Cookie cookie = new Cookie("JSESSIONID",sessionId);
cookie.setMaxAge(140);
resp.addCookie(cookie);


String uri = req.getRequestURI();
String action = uri.substring(uri.lastIndexOf("/") +1 );

if ("login".equals(action)) {
    String name = req.getParameter("name");
    String password = req.getParameter("password");
    if ("root".equals(name) && "root".equals(password)) {
        session.setAttribute("name",name);
        resp.sendRedirect("/WebappTest_war_exploded/testSession/index.jsp");
    }
} else {
    req.setAttribute("msg","错误");
    req.getRequestDispatcher("/WebappTest_war_exploded/testSession/login.jsp").forward(req,resp);
}
```

设置 session 生命周期

由于 session 对象是通过 sessionId 查找，而 session 存在于 cookie 中，而 cookie 生命周期默认为一次会话，所以浏览器关闭，session 也就不能用了，所以可以通过设置 cookie 的生命周期从而改变 session 的生命周期

由于 cookie 是浏览器自动生成的，里面包含了 JSESSIONID ，通过创建 cookie 对象包含 JSESSION，并且设置生命周期，通过替换的方式实现了修改



session 验证

用户访问需要保护的资源时，可以使用 session 验证的方式来保证安全性

1. 先使用 session.setAttribute() 绑定数据
2. 使用 session.getAttribute() 方式来读取绑定值
3. 如果没有则跳转回登入界面



什么是 session 超时

web 服务器会将空闲时间过程的 session 对象删除掉，以节省服务器内存资源（一般 30 分钟）

修改session 缺省时间限制的两种方式

```xml
<session-config>
  <session-timeout>10</session-timeout>
</session-config>
```

```
session.setMaxInactiveInterval(1);  // 秒数
```



###### 浏览器禁用cookie

服务器在默认情况下，会使用 cookie 的方式将 sessionId 发送给浏览器，如果用户禁止 cookie ，则sessionId 不会被浏览器保存，此时服务器可以使用如 URL 重写这样的方式来发送 sessionId

URL重写：改写原本的访问地址，在URl 后面使用参数的方式携带上 sessionId

浏览器在访问服务器上的某个地址时，不再使用原来的那个地址，而是使用经过改写的地址

```java
resp.sendRedirect(resp.encodeRedirectURL("index.jsp")); 或
resp.sendRedirect("index.jsp;jsessionid="+session.getId());
```

- 如果是重定向，使用 `response.encodeRedirectURL(String url)` 
- 如果是连接地址和表单提交，使用 `response.encodeURL(String url)` 生成重写后的 url



###### session 的优缺点

- 优点：
  - 安全（将状态保存在服务器端）
  - session 能够保存的数据类型更丰富，cookie 只能保存字符串
  - session 能够保存更多的数据，cookie 约 4 kb
- 缺点：
  - session 将状态保存在服务器端，战用服务器端的内存，如果用户量过大，会严重影响服务器的性能



###### 验证码

为了防止机器人的破坏性操作，可以使用验证码技术来防止恶意发送数据；验证码本质上是一张动态生成的图片；图片的内容会随着程序的运行而随机生成

使用 java 绘图技术，动态绘制一张图片，由 Servlet 完成后，通过输出流将其输出到客户端，浏览器通过 img 标签的 src 属性访问该 Servlet 以获得该动态图片

```java
public class GenerateValidateCode extends HttpServlet {
    @Override
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {    
	// 创建空白图片
    BufferedImage image = new BufferedImage(100,30,BufferedImage.TYPE_INT_RGB);
    // 获取图片画笔
    Random r = new Random();
    Graphics g = image.getGraphics();
    // 设置画笔颜色
    g.setColor(new Color(r.nextInt(255),r.nextInt(255),r.nextInt(255)));
    // 绘制矩形的背景
    g.fillRect(0,0,100,30);
    // 调用自定义方法，获取长度为 5 的字母数字组合的字符串
    String number = getNumber(5);
    // 设置画笔大小
    g.setColor(new Color(0,0,0));
    g.setFont(new Font(null,Font.BOLD,24));
    // 绘制字符串
    g.drawString(number,5,25);
    // 绘制干扰线
    for (int i = 0; i < 8; i++) {
        g.setColor(new Color(r.nextInt(255),r.nextInt(255),r.nextInt(255)));
        g.drawLine(r.nextInt(100),r.nextInt(30),r.nextInt(100),r.nextInt(30));
    }
    // 返回
    resp.setContentType("image/jpeg");
    ImageIO.write(image,"jpeg",resp.getOutputStream());
}
private String getNumber(int num) {
    String str = "qwertyuiopasdfghjklzxcvbnm";
    StringBuilder number = new StringBuilder();
    Random r = new Random();
    for(int i=0;i<num;i++) {
        number.append(str.charAt(r.nextInt(str.length())));
    }
    return number.toString();
}
```

验证码的验证流程

请求带有验证码的页面时：

1. 通过 img 标签的 src 属性获取验证码图片
2. 服务器端生成随机字符，并绘制
3. 服务器端将生成的随机字符串绑定到 session 中
4. 提交表单即填写验证码内容时
5. 处理程序将 session 中绑定的正确的验证码字符串取出来
6. 获得表单提交时填写的验证码内容
7. 比较两者，根据结果做出判断



###### 过滤器

是 Servlet2.3 规范中定义的一种小型的，可插入的 web 组件，用来拦截 Servlet 容器的请求和响应过程，以便查看，提取或以某种方式操作正在客户机和服务器之间交换的数据；过滤器通常是封装了一些功能的 web 组件，这些功能很重要，但对于处理客户端请求或发送响应来说不是决定性的；典型的应用包括记录请求和响应的数据，管理会话属性等

如何编写过滤器 ？

1. 编写一个 java，实现 Filter 接口
2. 在 doFilter 方法中实现拦截处理逻辑
3. 把过滤器添加到 web 程序中
4. 把 过滤器 和 web 应用一起打包部署

示例：过滤敏感词汇

在评论内容到达处理 Servlet 之前，由过滤器拦截后先进行内容的过滤，如果包含敏感词汇则直接返回给客户端响应提示，不包含则将请求传递给下一个过滤器或 Servlet

```xml
<filter>
  <filter-name>CommentFilter</filter-name>
  <filter-class>CommentFilter</filter-class>
    
  <init-param>
      <param-name>name</param-name>
      <param-value>egg</param-value>
   </init-param>
    
</filter>
<filter-mapping>
  <filter-name>CommentFilter</filter-name>
  <url-pattern>/commend</url-pattern>
</filter-mapping>
```

也可以使用 `@webFilter` 注解，不过这种方式多个过滤器时无法确定顺序；web.xml 中的过滤器执行顺序默认为由上至下的配置顺序

```java
/**容器启动之后创建过滤器实例，然后调用 init 方法，只会调用一次
 * 容器会将已经创建好的 FilterConfig 对象作为参数传入可以从该参数获取初始化的配置信息
 **/
@Override
public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
    HttpServletRequest request = (HttpServletRequest) servletRequest;
    HttpServletResponse response = (HttpServletResponse) servletResponse;
    request.setCharacterEncoding("utf-8");
    response.setContentType("text/html;charset=utf-8");

    String comment = request.getParameter("comment");

    if (comment.contains("a")) {
        System.out.println(comment + " ---> a 禁止输入");
    } else {
        filterChain.doFilter(request,response);
    }
}
```

filterChain 和 request 转发的区别：

1. filterChain 会将控制权交给下一个过滤器，如果没有则交给下一个 Servlet
2. request 转发会直接请求交给下一个 Servlet

```java
//容器启动之后，创建过滤器实例；然后调用 init方法，只会调用一次；容器会将已经创建好的 FilterConfig 对象作为参数传入；可以从该参数中获取初始化的配置信息

private String name;
@Override
public void init(FilterConfig filterConfig) throws ServletException {
    name = filterConfig.getInitParameter("name");
    System.out.println(name);
}
```

过滤器的优点：

1. 实现代码的 “ 可插拔性 ” ，即增加或减少某种功能模块，不会影响程序的正常执行
2. 可以将多个相同处理逻辑的模块集中写在过滤器里面，方便代码的维护



#### EL 和 JSTL

EL 表达式

EL（Expression language）表达式是一套简单的计算规则，用于给 jsp 标签的属性赋值，也可以直接用来输出；表达式也可以脱离标签单独使用

为什么需要 EL 表达式 和 jsp 标签 ？

jsp 中嵌套的大量 java 代码增加页面内容的复杂度，使得页面不够简洁，不方便代码的维护；为此 Sun 公司制定了 jsp 标签来代替 java 代码

Apache 组织开发的一套标签库被 Sun 公司整合后，称为标准标签库（JSP STANDARD TAG LIBRARY），配合 EL 表达式以减轻 jsp 文件的复杂度，方便维护 jsp 文件的目的



EL 表达式的作用：

- 访问 Bean 的属性

  - 通常情况下的 javaBean 指的是一个公共的类，含有一个空参的构造方法，一些属性以及访问这些属性所提供的 get/set 方法，方法名与属性名需要符合一定的规范

  - 使用 EL 表达式访问 Bean 属性：

    - `${对象名.属性名}`
    - `${对象名["属性名"]}`

  - ```jsp
    <%@ page isELIgnored="false" %> //EL表达式未被正常解析时配置
    
    <%
        Course course = new Course();
        course.setCourseId(1);
        course.setCourseName("math");
    
        User user = new User();
        user.setName("jerry");
        user.setAge(19);
        user.setCourse(course);
        user.setInterset(new String[]{"a","b"});
        request.setAttribute("user",user);
    %>
        年龄：<%=((User)request.getAttribute("user")).getAge()%>
        姓名：${user.name}
        课程名：${user.course.courseName}
        ${user.age}
    	${user.interset[1]}  // 数组
    ```

    执行过程：容器会一次从 四大域 中查找名为 “ user ” 的对象，找到后调用 getName() 等方法，然后输出，等价于：

    ```jsp
    <%
        User u = (User)request.getAttribute("user");
        out.println(u.getName());
    %>
    ```

- 输出简单的运算结果

  -  EL 表达式可以做一些简单的计算，可将结果直接输出或给 jsp 标签的属性赋值

  - 算数运算：+（只能求和、不能够连接字符串）、-、*、/、%；逻辑运算、关系运算 ......

  - empty：用来判断一个字符串是否为空，或者一个集合是否为空；以下四种情况为 true ：

    - 空字符串
    - 空的集合
    - 值为 null
    - 找不到对应的值

    ```jsp
    ${1+1}
    ${"100" + "100"}  // 200
    
    ${1 > 2}
    ${user.name == "jerry"}  // 比较的是内容
    
    ${1 > 0 && 2 < 3}
                     
    ${empty user.name}  // 不存在的属性会抛出异常
    ${empty ""}
    ```

- 获取请求参数值

  - `${param.username}` 等价于 `request.getParameter("username")`
  - `${paramValues.username}` 等价于 `request.getParameterValues("username")`

  ```jsp
  名字：${param.name}
  爱好：${paramValues.interset[0]}
  
  访问网址示例：
  http://localhost:8080/WebappTest_war_exploded/el01.jsp?name=a&interset=1&interset=2
  ```



1. 简化了 jsp 页面的代码
2. 相对 java 小脚本：如果 request 中没有 user 对象，java 小脚本会报 500 错误，而 EL 表达式不会；如果没有为属性赋值，java 小脚本会打印 null，而 EL 表达式不会；如果是绑定名写错，java 小脚本会报 500 空指针异常，而 EL 表达式不会



指定对象的查找范围

在编写 EL 表达式时是可以指定查找对应绑定名对象的范围，`${sessionScope.user.name}`，一旦指定查找范围，那么在该范围没有找到指定的绑定对象时，不会再去其他的区域中查找



**JSTL**

如何使用？

1. 将 JSTL 标签对应的 jar 包添加至项目环境目录中
2. 使用 taglib 指令导入要使用的 jsp 标签

Tomcat 10.* （详见 [jstl 的 jar 包处理](https://stackoverflow.com/questions/4928271/how-to-install-jstl-the-absolute-uri-http-java-sun-com-jstl-core-cannot-be-r/4928309#4928309)）

```xml
<dependency>
    <groupId>org.glassfish.web</groupId>
    <artifactId>jakarta.servlet.jsp.jstl</artifactId>
    <version>2.0.0</version>
</dependency>
```



```
结论：Tomcat 10 使用 JSTL2.0.0 无问题
    Tomcat 9 使用 JSTL 1.2.6 出错，可将 c.tld 直接丢至 web-inf 下运行（已解决，tomcat配置文件设置了跳过扫描所有 ）
```





核心标签 - if 标签

语法：`<c:if test="" var="" scope=""></c:if>`

- 当 test 属性值为 true时，执行标签体的内容，test 属性可以使用 EL 表达式赋值
- var 属性：指定一个绑定名称
- scope 属性：指定绑定的范围（page，request、session、application）
- 注意：var 和 scope 要配合使用



choose 标签

语法：

```
<c:choose>
	<c:when test=""></c:when>
	...
	<c:otherwise test=""></c:otherwise>
</c:choose>
```

when 表示一个处理分支，当 test 属性为 true 时会执行该分支，可以出现 1 次或多次

otherwise 表示例外，可以出现 0 次或一次



```jsp
<%
    Employees emp = new Employees();
    emp.setName("jerry");
    emp.setGender("m");
    request.setAttribute("emp",emp);
%>
    姓名：${emp.name}
    性别：<c:if test="${emp.gender == 'm'}" var="a" scope="request">男</c:if>
<%--        <c:if test="${emp.gender != 'm'}">女</c:if>--%>
        <c:if test="${!a}">女</c:if>

    <c:choose>
        <c:when test="${emp.gender == 'm'}">男</c:when>
        <c:otherwise>女</c:otherwise>
    </c:choose>
```



forEach 标签

用来遍历集合或数组，语法：

```
<c:forEach var="" items="">
...
</c:forEach>
```

var属性：指定一个绑定名称，容器每次从集合中取一个对象，然后绑定到 pageContext 对象上

items 属性：指定要遍历的集合，一般使用 EL 表达式来复制



varStatus 属性：指定一个绑定名称，绑定值时一个由容器创建的对象，该对象封装了当前迭代的状态

index ：返回正在被迭代的对象的下标，下标从 0 开始

count ：返回是第几次迭代，从 1 开始



```jsp
<c:forEach items="${list}" var="emps">
    姓名：${emps.name}
    性别：${emps.gender}<br>
</c:forEach>


<c:forEach items="${list}" var="emps" varStatus="s">
    姓名：${emps.name}
    性别：${emps.gender}
    count: ${s.count}
    index: ${s.index}<br>
</c:forEach>

<c:forEach items="${list}" var="emps" varStatus="s">
    <p class=s${s.index%2 + 1}>
        姓名：${emps.name}
        性别：${emps.gender}
        count: ${s.count}
        index: ${s.index}<br>
    </p>
</c:forEach>
```



自定义标签

1. java类继承 SimpleTagSupport 类
2. 在 doTag 方法中添加处理逻辑
3. 配置标签说明文件

自定义标签具备两个属性，一个用于设定要输出的内容，一个用于设定输出的次数，在自定义标签类中定义两个属性接收页面中设定的数据，获取页面对象中的输出对象，输出内容

```java
public class HelloTag extends SimpleTagSupport {
    private String str;
    private int count;

    @Override
    public void doTag() throws JspException, IOException {
        PageContext context = (PageContext) getJspContext();
        JspWriter writer = context.getOut();
        for (int i = 0; i < count; i++) {
            writer.println(str + "<br>");
        }
    }

    public String getStr() {
        return str;
    }

    public void setStr(String str) {
        this.str = str;
    }

    public int getCount() {
        return count;
    }

    public void setCount(int count) {
        this.count = count;
    }
}
```

tag.tld：（放在 WEB-INF 中）

```xml
<?xml version="1.0" encoding="UTF-8" ?>

<taglib xmlns="http://java.sun.com/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-jsptaglibrary_2_1.xsd"
        version="2.1">

    <tlib-version>1.2</tlib-version>
    <short-name>c</short-name>
    <uri>http://b.chumeng.buzz/mytag</uri>
    <tag>
        <description>
            根据次数打印文本
        </description>
        <name>pt</name>
        <tag-class>tag.HelloTag</tag-class>
        <body-content>empty</body-content>
        <attribute>
            <description>
                none
            </description>
            <name>str</name>
            <required>false</required>
            <rtexprvalue>false</rtexprvalue>
        </attribute>
        <attribute>
            <description>
                none
            </description>
            <name>count</name>
            <required>false</required>
            <rtexprvalue>false</rtexprvalue>
        </attribute>
    </tag>
</taglib>
```

mytag.jsp：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://b.chumeng.buzz/mytag" %>
<%@ page isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
<c:pt count="10" str="Hello, world!"/>
</body>
</html>
```



#### Maven

Maven就是是专门为 Java 项目打造的管理和构建工具，它的主要功能有：

- 提供了一套标准化的项目结构；
- 提供了一套标准化的构建流程（编译，测试，打包，发布……）；
- 提供了一套依赖管理机制

###### Maven项目结构

<img src=".\note_imgs\Maven结构.png" style="zoom: 80%;" />

项目的根目录`a-maven-project`是项目名，它有一个项目描述文件`pom.xml`，存放Java源码的目录是`src/main/java`，存放资源文件的目录是`src/main/resources`，存放测试源码的目录是`src/test/java`，存放测试资源的目录是`src/test/resources`，最后，所有编译、打包生成的文件都放在`target`目录里。这些就是一个Maven项目的标准目录结构

###### 唯一ID

对于某个依赖，Maven只需要3个变量即可唯一确定某个jar包：

- groupId：属于组织的名称，类似 Java 的包名；
- artifactId：该jar包自身的名称，类似 Java 的类名；
- version：该jar包的版本



###### 依赖关系

Maven定义了几种依赖关系，分别是`compile`、`test`、`runtime`和`provided`：

| scope    | 说明                                          | 示例            |
| :------- | :-------------------------------------------- | :-------------- |
| compile  | 编译时需要用到该jar包（默认）                 | commons-logging |
| test     | 编译Test时需要用到该jar包                     | junit           |
| runtime  | 编译时不需要，但运行时需要用到                | mysql           |
| provided | 编译时需要用到，但运行时由JDK或某个服务器提供 | servlet-api     |

其中，默认的`compile`是最常用的，Maven会把这种类型的依赖直接放入classpath

```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.48</version>
    <scope>runtime</scope>
</dependency>
```



###### 搜索第三方组件

如果我们要引用一个第三方组件，比如`okhttp`，如何确切地获得它的`groupId`、`artifactId`和`version` ？方法是通过 [search.maven.org](https://search.maven.org/) 搜索关键字，找到对应的组件后，直接复制



###### 构建流程

Maven的生命周期由一系列阶段（phase）构成，以内置的生命周期`default`为例，它包含以下phase：

```
- validate
- initialize
- generate-sources
- process-sources
- generate-resources
- process-resources
- compile
- process-classes
- generate-test-sources
- process-test-sources
- generate-test-resources
- process-test-resources
- test-compile
- process-test-classes
- test
- prepare-package
- package
- pre-integration-test
- integration-test
- post-integration-test
- verify
- install
- deploy
```

如果我们运行`mvn package`，Maven就会执行`default`生命周期，它会从开始一直运行到`package`这个phase为止：

```
- validate
- ...
- package
```

如果我们运行`mvn compile`，Maven也会执行`default`生命周期，但这次它只会运行到`compile`，即以下几个phase：

```
- validate
- ...
- compile
```

Maven另一个常用的生命周期是`clean`，它会执行3个phase：

```
- pre-clean
- clean （注意这个clean不是lifecycle而是phase）
- post-clean
```

所以，我们使用`mvn`这个命令时，后面的参数是phase，Maven自动根据生命周期运行到指定的phase



在实际开发过程中，经常使用的命令有：

- `mvn clean`：清理所有生成的class和jar；

- `mvn clean compile`：先清理，再执行到`compile`；

- `mvn clean test`：先清理，再执行到`test`，因为执行`test`前必须执行`compile`，所以这里不必指定`compile`；

- `mvn clean package`：先清理，再执行到`package`。

大多数phase在执行过程中，因为我们通常没有在`pom.xml`中配置相关的设置，所以这些phase什么事情都不做。

经常用到的phase其实只有几个：

- clean：清理
- compile：编译
- test：运行测试
- package：打包