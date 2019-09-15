---
title: 精读Servlet源码-javax.servlet.Servlet
date: 2019-07-18 16:41:32
categories:
- 源码
tags:
- Java
- servlet
---

# 精读Servlet源码-javax.servlet.Servlet

servlet是运行在Web服务器上面的Java应用程序，Servlet接口类是servlet最核心的一个类，它定义了所有的servlet应该实现的方法，即servlet的生命周期方法，这些声明周期方法是由Web容器比如tomcat来调用的；同时也定义了获取一个servlet配置参数或者信息的方法。

GenericServlet类实现了Servlet类，定义了一个通用的、协议无关的servlet；而HttpServlet类继承了GenericServlet类，定义了HTTP协议下的servlet，它是抽象类，开发者必须继承HttpServlet并实现所有的抽象方法来创建具体的http servlet应用，它们的依赖关系如图：

![](https://i.loli.net/2019/07/18/5d30365eae30f77247.png)

```java
public interface Servlet {
    public void init(ServletConfig config) throws ServletException;
    
    public ServletConfig getServletConfig();

    public void service(ServletRequest req, ServletResponse res)
	throws ServletException, IOException;

    public String getServletInfo();

    public void destroy();
}
```

## 各个接口方法的说明

### init

`init()`方法是用来指示一个servlet将被加载到web服务器的服务中，这个方法是在servlet初始化完成之后立即调用的，只有`init()`被调用并成功返回后，这个servlet才能接受、处理并响应客户端的请求。

当`inti()`调用过程中发生以下情况时，servlet是不会被加载到服务里面的：

- `init()`抛出`ServletException`异常
- `init()`方法在Web服务器设定的时间内没有返回，即执行超时

一个servlet的`init()`方法只会在初始化时会且只会被调用一次，它接受`ServletConfig`类型的参数，`ServletConfig`包含了servlet的配置和初始化参数，这些参数可以作用域Servlet的整个生命周期，`init()`方法可以使用这些参数完成相关的初始化工作。

### service

只有当`init()`成功调用并将servlet加载进服务中时，`service()`才能被servlet容器调用用来处理客户端的请求。一般servlet应用都是运行在多线程的servlet容器中，可以并行处理多个客户端请求，servlet每当接收到一个请求时都会产生一个新的线程来处理和该请求，因此就会出现多个servlet 同时访问相同的共享资源（如文件、内存资源等）的情况，这时要注意处理好线程同步的问题。

`service() `方法由容器调用，`service()` 方法在适当的时候调用 doGet、doPost、doPut、doDelete 等方法。所以开发者不用对 `service()` 方法做任何动作，只需要根据来自客户端的请求类型来重写 `doGet()` 或 `doPost()` 即可。

它接受两个参数：`ServletRequest`和`ServletResponse`，分别对应客户端的请求和servlet对该请求作出的响应。

### destroy

在servlet被从容器中卸载之前调用，它可以用来清除该servlet占用的资源（例如关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动等）。

`destroy()`方法仅当该servlet所有的service方法都成功返回之后、或者执行时间超时才能调用，而且`destroy()`方法一旦调用完成，这个servlet的`service()`就放就不会被调用了，而且servlet 对象被标记为垃圾回收，会等待JVM回收。

### getServletConfig

该方法返回一个`ServletConfig`对象，这个对象恰好是servlet容器初始化servlet时传入`init()`方法的`ServletConfig`对象，因此在实现Servlet接口的时候要求`init()`方法能够将参数`ServletConfig`对象保存在servlet实例内部，而这个功能已经在Servlet接口实现类`GenericServlet`中实现了：

```java
public ServletConfig getServletConfig() {
	return config;
}

public void init(ServletConfig config) throws ServletException {
    this.config = config;
    this.init();
}
```

### getServletInfo

这是留给开发者自定义的方法，它的作用是用来返回servlet的相关信息比如：作者、版本和版权信息等等；这个方法在Servlet接口实现类`GenericServlet`中默认是返回一个空的字符串，具体的返回值是由实际的开发者决定的，但是有一个约定是：返回值应该是普通文本而不是标记文本（例如HTML、XML等等）

```java
public String getServletInfo() {
	return "";
}
```

## Servlet生命周期架构图

典型的 Servlet 生命周期方案：

- 第一个到达服务器的 HTTP 请求被委派到 Servlet 容器
- Servlet 容器在调用 service() 方法之前加载 Servlet
- 然后 Servlet 容器处理由多个线程产生的多个请求，**每个线程执行一个单一的 Servlet 实例的 service() 方法**

![](https://i.loli.net/2019/07/18/5d305e348bdc550818.jpg)