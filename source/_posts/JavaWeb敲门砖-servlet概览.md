---
title: JavaWeb敲门砖-servlet概览
date: 2019-07-18 15:50:49
categories:
- 后端
tags:
- Java
- servlet
---

# JavaWeb敲门砖-Servlet概览

## Servlet生命周期

> 来源：https://www.runoob.com/servlet/servlet-life-cycle.html

Servlet 生命周期可被定义为从创建直到毁灭的整个过程。以下是 Servlet 遵循的过程：

- Servlet 通过调用 **init ()** 方法进行初始化。
- Servlet 调用 **service()** 方法来处理客户端的请求。
- Servlet 通过调用 **destroy()** 方法终止（结束）。
- 最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的。

## Servlet实例

### 编写Servlet类

开发web应用时，通常是继承HttpServlet类来编写自己的Servlet应用，用以处理客户端的HTTP请求。

```java
package servlet;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;

public class HelloWorld extends HttpServlet {
    private String message;

    @Override
    public void init() throws ServletException {
        message = "Hello World!";
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html");
        PrintWriter pw = resp.getWriter();
        pw.println("<h1>"+message+"</h1>");
    }

    @Override
    public void destroy() {
        System.out.println("end of servlet");
    }
}
```

### 编译Servlet

如果直接javac编译`.java`文件，会出现依赖问题，因为servlet默认不是标准jdk自带的：

```java
import javax.servlet.ServletException;
                    ^
HelloWorld.java:4: 错误: 程序包javax.servlet.http不存在
import javax.servlet.http.HttpServlet;
                         ^
HelloWorld.java:5: 错误: 程序包javax.servlet.http不存在
import javax.servlet.http.HttpServletRequest;
                         ^
HelloWorld.java:6: 错误: 程序包javax.servlet.http不存在
import javax.servlet.http.HttpServletResponse;
                         ^
......
```

为了方便解决依赖问题，建议使用maven来编译整个项目，然后再在`target/classes`下面找到需要的class文件：

![](https://i.loli.net/2019/07/18/5d3079b72175b79338.png)

### 部署Servlet

进入在tomcat目录，在`webapps/`下面建立项目结构

```java
webapps
└── serv
    ├── index.html*
    └── WEB-INF
        ├── classes
        │   └── HelloWorld.class
        └── web.xml
```

其中index.html是可选的，在这里我们只是为了测试我们的servlet，因此可以不用建立这个文件。然后将我们之前编译好的HelloWorld.class复制到classes下，然后编写web.xml文件：

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<web-app xmlns="http://java.sun.com/xml/ns/j2ee"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
    version="2.4">

  <display-name>Welcome to my first jsp </display-name>

  <description>
     Welcome to my jsp
  </description>
  
  <servlet> 
      <servlet-name>Hello</servlet-name> 
      <servlet-class>HelloWorld</servlet-class> 
  </servlet>
  
  <servlet-mapping>   
      <servlet-name>Hello</servlet-name>
      <url-pattern>/HelloWorld</url-pattern>  
  </servlet-mapping>
  
</web-app>
```

保存后启动tomcat服务器`bin/startup.sh`，访问"http://localhost:8080/serv/HelloWorld"就可以了。。。

。。。。。。

wtf？为什么报错了？

![](https://i.loli.net/2019/07/18/5d307bbf775da27042.png)

别急，往下看

### 注意事项

如果你细心的话可以发现，我们的HelloWorld类是在`servlet`包下面的，但是我们的classes结构和web.xml描述是这样的：

```java
WEB-INF
    ├── classes
    │   └── HelloWorld.class // 看这里
    └── web.xml
```

```java
<servlet> 
    <servlet-name>Hello</servlet-name> 
    <servlet-class>HelloWorld</servlet-class> // 看这里
</servlet>

<servlet-mapping>   
    <servlet-name>Hello</servlet-name>
    <url-pattern>/HelloWorld</url-pattern>  
</servlet-mapping>
```

但是实际上HelloWorld这个类的完整类名是`servlet.HelloWorld`，很显然跟我们的配置不匹配，所以我们需要修改：

```java
webapps/serv/WEB-INF/
├── classes
│   └── servlet // 看这里
│       └── HelloWorld.class // 看这里
└── web.xml
```

```java
<servlet> 
    <servlet-name>Hello</servlet-name> 
    <servlet-class>servlet.HelloWorld</servlet-class> // 看这里
</servlet>

<servlet-mapping>   
    <servlet-name>Hello</servlet-name>
    <url-pattern>/HelloWorld</url-pattern>  
</servlet-mapping>
```

保存后重启tomcat，然后再次访问页面：

![](https://i.loli.net/2019/07/18/5d307d724192099566.png)

这很舒服了！

### servlet配置

为了让Servlet能响应用户请求，还必须将Servlet配置在web应用中，配置Servlet需要修改web.xml文件。从Servlet3.0开始，配置Servlet有两种方式：

- 在Servlet类中使用@WebServlet Annotation进行配置。
- 在web.xml文件中进行配置。

这里面我们用的是web.xml配置的方式。这里只介绍一下最核心的部分，更详细的内容可以参考：https://blog.csdn.net/believejava/article/details/43229361

- \<servlet>定义一个servlet，它必须含有\<servlet-name>和\<servlet-class>
  - \<servlet-name>：用来定义servlet的名称，该名称**在整个应用中必须是惟一的**
  - \<servlet-class>：用来指定servlet的完全限定的名称，例如我们定义的servlet全限定名为`servlet.HelloWorld`
- \<servlet-mapping>用来定义servlet与请求url的匹配规则，它含有\<servlet-name>和\<url-pattern>
  - \<servlet-name>：Servlet的名字，唯一性和一致性，与\<servlet>元素中声明的名字一致
  - \<url-pattern>：指定相对于Servlet的URL的路径。该路径**相对于web应用程序上下文的根路径**，例如我们上面定义了应用的根路径是`/serv`，那么最后我们访问该servlet的url就是：`http://localhost:8080/serv/<url-pattern>`。\<servlet-mapping>将URL模式映射到某个Servlet，即该Servlet处理的URL