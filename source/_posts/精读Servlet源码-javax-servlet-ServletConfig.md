---
title: 精读Servlet源码-javax.servlet.ServletConfig
date: 2019-07-19 09:43:08
categories:
- 源码
tags:
- Java
- servlet
---

# 精读Servlet源码-javax.servlet.ServletConfig

> 参考：https://www.cnblogs.com/smyhvae/p/4140877.html

`ServletConfig`的实例是servlet容器在初始化一个servlet时传递到`init()`方法的对象，它携带了一个servlet的相关配置以及初始化参数。

```java
package javax.servlet;

import java.util.Enumeration;

/**
 * A servlet configuration object used by a servlet container
 * to pass information to a servlet during initialization. 
 */
 public interface ServletConfig {

    public String getServletName();

    public ServletContext getServletContext();

    public String getInitParameter(String name);

    public Enumeration<String> getInitParameterNames();

}

```

## 各个接口方法的说明

### getServletName

返回servlet实例的名称，它可能是在web服务器中由服务器管理者提供的，如果servlet没有被注册到服务中，那么它将返回servlet实例的类名。

### getServletContext

`getServletContext`返回`ServletContext`实例的引用，WEB容器在启动时，它会为**每个WEB应用程序都创建一个对应的ServletContext对象，它代表当前web应用**，并且保存在`ServletConfig`实例中并传递给对应的servlet，通过它可以允许调用者与servlet容器进行交互。

### getInitParameter

初始化参数在`ServletConfig`中是以键值对方式存储的，`getInitParameter()`可以通过给定的参数名字获取对应的参数值，键和值都是以字符串的形式存储；如果对应的参数名字不存在，则返回null。

### getInitParameterNames

获取所有的初始化参数，以Enumeration类型返回；如果没有，则返回一个空的Enumeration。Enumeration类似于迭代器，Enumeration接口中定义了一些方法，通过这些方法可以枚举（一次获得一个）对象集合中的元素。

## 追根溯源：ServletConfig vs ServletContext

### ServletConfig

从更本质上来看，`ServletConfig`实际上代表的是当前Servlet在web.xml中的配置信息，在Servlet的配置文件中，可以使用\<servlet>标签下的一个或多个\<init-param>标签为servlet配置一些初始化参数。\<init-param>标签是**为某一个单独的servlet加配置信息，这种配置信息在其他的Servlet中是无法访问到的**。

当servlet配置了初始化参数后，web容器在创建servlet实例对象时，会自动将这些初始化参数封装到ServletConfig对象中，并在调用servlet的init方法时，将ServletConfig对象传递给servlet。进而，程序员通过ServletConfig对象就可以得到当前servlet的初始化参数信息。

这样做的好处是：如果将数据库信息、编码方式等配置信息放在web.xml中，如果以后数据库的用户名、密码改变了，则**直接很方便地修改web.xml就行了，避免了直接修改源代码的麻烦**。

### ServletContext

WEB容器在启动时，它会为**每个WEB应用程序都创建一个对应的ServletContext对象，它代表当前web应用**。

ServletConfig对象中维护了ServletContext对象的引用，它是由servlet容器提供的，开发人员在编写servlet时，可以通过ServletConfig.getServletContext方法获得ServletContext对象。

由于一个WEB应用中的**所有Servlet共享同一个ServletContext对象**，因此Servlet对象之间可以通过ServletContext对象来实现通讯。ServletContext对象通常也被称之为**context域对象**。

<u>*对ServletContext更深入的研究请移步：精读Servlet源码-javax.servlet.ServletContext*</u>

## ServletConfig实例

### 编写Servlet

```java
package servlet;

import javax.servlet.ServletConfig;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;

public class ServletConfigDemo extends HttpServlet {
    @Override
    public void init() throws ServletException {
        super.init();
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        ServletConfig servletConfig = this.getServletConfig();
        PrintWriter out = resp.getWriter();
        resp.setContentType("text/plain");

        String servletName = servletConfig.getServletName();
        out.println("Servlet name: " + servletName);

        String username = servletConfig.getInitParameter("username");
        String password = servletConfig.getInitParameter("password");
        out.println("=================servletConfig.getInitParameter================");
        out.println("username: " + username);
        out.println("password: " + password);

        out.println("=================servletConfig.getInitParameterNames================");
        Enumeration<String> params = servletConfig.getInitParameterNames();
        while (params.hasMoreElements()) {
            String name = params.nextElement();
            out.println(name + ": " + servletConfig.getInitParameter(name));
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
```

### 配置web.xml

```xml
<servlet>
    <servlet-name>ServletConfigDemo-xxxx</servlet-name>
    <servlet-class>servlet.ServletConfigDemo</servlet-class>
    <init-param>
        <param-name>username</param-name>
        <param-value>steve_chph</param-value>
    </init-param>
    <init-param>
        <param-name>password</param-name>
        <param-value>mypassword123456</param-value>
    </init-param>
    <init-param>
        <param-name>name1</param-name>
        <param-value>value1</param-value>
	</init-param>
	<init-param>
        <param-name>name2</param-name>
        <param-value>value2</param-value>
	</init-param>
</servlet>

<servlet-mapping>
    <servlet-name>ServletConfigDemo-xxxx</servlet-name>
    <url-pattern>/ServletConfigDemo</url-pattern>
</servlet-mapping>

```

### 输出结果

![](https://i.loli.net/2019/07/19/5d312f57b095347163.png)