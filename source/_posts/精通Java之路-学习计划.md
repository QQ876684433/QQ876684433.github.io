---
title: 精通Java之路-学习计划
date: 2019-07-16 09:22:52
categories:
- 笔记
tags:
- Java
---

# 精通Java之路-学习计划

## 玩好JDK

> 转载信息
>
> 作者：老刘
>
> 链接：https://www.zhihu.com/question/328775413/answer/741689212
>
> 来源：知乎著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### Java运行时环境

学习资料：《深入理解Java虚拟机 周志明 第二版》、《Java虚拟机规范 Java SE8版》

目标：对Java虚拟机有深入的理解，能够动手实现Java虚拟机核心功能

方式：阅读书籍并撰写阅读笔记，完成实现Java虚拟机的开源项目

> 所以，对于JVM，没什么可说的，就是往死里学，往死里研究，能有多深就多深！

### Java的基础类库

学习资料：jdk源码、各源码类技术博客、Oracle官方Java tutorial、《Java编程思想》

#### 第一级别：精读源码

```java
java.io
java.lang
java.util
// 除了Exception和Error这一类的
```

目标：深入理解jdk源码的设计原则、编程思想，包括里面使用到的数据结构、算法等知识，精通相关API的使用，能够实现上述类库的核心功能

方式：阅读源码并撰写源码阅读笔记，完成类库核心功能的开源项目

#### **第二级别：深刻理解**

```java
java.lang.reflect
java.net
javax.net.*
java.nio.*
java.util.concurrent.*
```

目标：熟练掌握类库的使用，对类库的设计有较为深入的理解，包括类库设计背后的计算机基础知识（操作系统、网络等），并对核心功能的源码有深入的研究

方式：研究类库的使用方法及常见并撰写学习笔记

> 举个例子，反射你要了解清楚的话，你是不是要搞明白JVM的类加载机制？网络IO要搞清楚的话，你是不是要清楚TCP/IP和HTTP、HTTPS？包括并发包，如果你要搞清楚的话，是不是要了解并发的相关知识？因此，这四个包要彻底搞清楚，还是需要花费一定时间和精力的。但是，请相信我，这绝对是值得的，甚至可以说，这四个包用的够不够叼，基本决定了一个Java程序员所处的档次。
> 

#### **第三级别：会用即可**

```java
java.lang.annotation
javax.annotation.*
java.lang.ref
java.math
java.rmi.*
javax.rmi.*
java.security.*
javax.security.*
java.sql
javax.sql.*
javax.transaction.*
java.text
javax.xml.*
org.w3c.dom.*
org.xml.sax.*
javax.crypto.*
javax.imageio.*
javax.jws.*
java.util.jar
java.util.logging
java.util.prefs
java.util.regex
java.util.zip
```

目标：了解上述类库的API及其应用场景

方式：学习常见API的功能并撰写学习笔记

*ps：这些类库都是有特定的应用场景的，可以留到实际开发中遇到时再作深入研究*

> 就像sql包和transaction包，就是操作数据库时用到的。而xml、dom和sax这些，都是操作xml时用到的。其它的包也都是类似的，有使用注解时用的，有远程方法调用时用的，也有涉及到加密时用到的等等

#### **四级别：直接无视**

~~能走到这一步，应该来说已经达到了熟悉/熟练使用Java的境界了，但是离精通Java还差得远~~

### Java的开发工具

> 如果你有兴趣的话，也可以自己去JDK的bin目录下找找，看有没有什么更好玩的工具

```java
steve@ubuntu-17-10:~$ ls /usr/lib/jvm/jdk1.8.0_201/bin/
appletviewer  javafxpackager  jdb      jrunscript    pack200      unpack200
ControlPanel  javah           jdeps    jsadebugd     policytool   wsgen
extcheck      javap           jhat     jstack        rmic         wsimport
idlj          javapackager    jinfo    jstat         rmid         xjc
jar           java-rmi.cgi    jjs      jstatd        rmiregistry
jarsigner     javaws          jmap     jvisualvm     schemagen
java          jcmd            jmc      keytool       serialver
javac         jconsole        jmc.ini  native2ascii  servertool
javadoc       jcontrol        jps      orbd          tnameserv
```

