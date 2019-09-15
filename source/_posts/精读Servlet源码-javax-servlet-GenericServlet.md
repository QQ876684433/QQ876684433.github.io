---
title: 精读Servlet源码-javax.servlet.GenericServlet
date: 2019-07-18 19:57:19
categories:
- 源码
tags:
- Java
- servlet
---

# 精读Servlet源码-javax.servlet.GenericServlet

`GenericServlet`定义了通用的、协议独立的servlet，HTTP协议版的servlet即`HttpServlet`直接继承了`GenericServlet`；`GenericServlet`实现了`Servlet`和`ServletConfig`接口，它提供了`Servlet`接口生命周期方法的简单实现版本（`init`和`destroy`），同时对`ServletConfig`的接口方法作进一步的封装。

`ServletConfig`作为`GenericServlet`的私有属性保存，它是在servlet在初始化时`init()`方法中传递进来的，这样就能在servlet的各个生命周期方法中以及servlet容器中访问到servlet配置及初始化参数。

```java
public abstract class GenericServlet 
    implements Servlet, ServletConfig, java.io.Serializable
{
    private static final String LSTRING_FILE = "javax.servlet.LocalStrings";
    private static ResourceBundle lStrings =
        ResourceBundle.getBundle(LSTRING_FILE);

    private transient ServletConfig config;

    ... ...
}
```

## 各个方法的说明

### 构造函数

因为servlet的初始化工作是由`init()`完成的，所以在构造函数里什么也不做。

```java
public GenericServlet() { }
```

### Servlet接口方法

#### init

`GenericServlet`除了实现`Servlet`接口的`init()`方法外，还提供了一个无参的`init()`方法：

- `void init(ServletConfig config)` 由servlet容器调用，它将`ServletConfig`实例传递进来，`init()`的工作是将`ServletConfig`保存起来以备后用，然后再调用无参的`init()`来完成开发者自定义的初始化过程
- `void init()` 提供给开发者重写`init()`的便利方式，开发者可以根据需要在`init()`中完成servlet的初始化工作

理论上，在开发servlet时重写任何一个`init()`都是可行的，区别在于如果重写的是带参数的`init()`，需要手动调用`super(config);`以将参数`ServletConfig`保存在servlet内部；如果重写的是无参的`init()`，那么就没有必要手动调用父类中对应的方法，因为servlet容器始终调用的是带参数的`init()`，带参数的`init()`随后又会调用无参的`init()`，如果开发者重写了无参的`init()`，那么也能完成自定义的servlet初始化工作。

一般来说推荐重写无参的`init()`，这样可以避免由于忘记调用`super(config);`而带来的不必要的麻烦。

```java
public void init(ServletConfig config) throws ServletException {
    this.config = config;
    this.init();
}

public void init() throws ServletException {

}
```

#### service

抽象方法，`GenericServlet`没有提供任何实现，意味着任何一个具体子类必须实现`service()`方法以完成相应的业务逻辑。

```java
public abstract void service(ServletRequest req, ServletResponse res)throws ServletException, IOException;
```

#### destroy

实现了一个空方法，由servlet容器调用，开发者可以根据需要重写该方法来完成servlet卸载前的相关资源清理工作。

```java
public void destroy() {}
```

#### getServletInfo

这是留给开发者自定义的方法，它的作用是用来返回servlet的相关信息比如：作者、版本和版权信息等等；`GenericServlet`中默认是返回一个空的字符串，因为具体的返回值是由实际的开发者决定的，但是有一个约定是：返回值应该是普通文本而不是标记文本（例如HTML、XML等等）。

```java
public String getServletInfo() {return "";   }
```

#### getServletConfig

返回servlet保存的`ServletConfig`引用。

```java
public ServletConfig getServletConfig() {return config;   }
```

### ServletConfig接口方法

这些接口方法实际上只是对`ServletConfig`的接口方法作了进一步的封装，实际上的工作是由servlet容器传递到`init()`方法的`ServletConfig`实例完成的。

```java
public String getInitParameter(String name) {
    ServletConfig sc = getServletConfig();
    if (sc == null) {
        throw new IllegalStateException(
        lStrings.getString("err.servlet_config_not_initialized"));
    }

    return sc.getInitParameter(name);
}

public Enumeration<String> getInitParameterNames() {
    ServletConfig sc = getServletConfig();
    if (sc == null) {
        throw new IllegalStateException(
        lStrings.getString("err.servlet_config_not_initialized"));
    }

    return sc.getInitParameterNames();
} 

public ServletContext getServletContext() {
    ServletConfig sc = getServletConfig();
    if (sc == null) {
        throw new IllegalStateException(
        lStrings.getString("err.servlet_config_not_initialized"));
    }

    return sc.getServletContext();
}

public String getServletName() {
    ServletConfig sc = getServletConfig();
    if (sc == null) {
        throw new IllegalStateException(
        lStrings.getString("err.servlet_config_not_initialized"));
    }

    return sc.getServletName();
}
```

### 扩展方法

#### log

这个方法是对`getServletContext().log(...)`方法的进一步封装，用来向`ServletContext`输出servlet运行时的日志信息：

- `void log(String msg)` 正常运行的日志信息，以对应的servlet名称作为日志信息前缀
- `void log(String message, Throwable t)` 运行时异常的日志信息，信息内容还包含了异常的调用栈追踪信息，以对应的servlet名称作为日志信息前缀

```java
public void log(String msg) {
    getServletContext().log(getServletName() + ": "+ msg);
}

public void log(String message, Throwable t) {
    getServletContext().log(getServletName() + ": " + message, t);
}
```

