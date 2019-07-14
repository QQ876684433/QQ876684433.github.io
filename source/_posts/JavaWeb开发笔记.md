---
title: JavaWeb开发笔记
date: 2019-07-14 23:27:20
categories:
- 笔记
tags:
- Java
- JavaWeb
- Spring
- SpringMVC
- Mybatis
- HTML
- CSS
- javascript
- jQuery
- 面试
---

# JavaWeb开发学习笔记



## Java SE

### Java基础（一）

#### Java正则表达式

（1）参考博客：<https://segmentfault.com/a/1190000009162306>

（2）对于`*?`这个符号，

> 如果 `?` 是限定符 `*` 或 `+` 或 `?` 或 `{}` 后面的第一个字符，那么表示**非贪婪模式**（尽可能少的匹配字符），而不是默认的**贪婪模式**

意思是，正则表达式匹配时从左往右扫描，当已扫描的字符串片段已经符合正则规则，则将其提取出来，即使再往后扫描一个字符依然符合这个规则，用代码解释如下：

```java
private static void test2() {
        String str = "Hello###, world!";
        String pattern1 = "([#]{2,}?)";
        String pattern2 = "([#]{2,})";
        System.out.println(str.replaceAll(pattern1, "&"));
        System.out.println(str.replaceAll(pattern2, "%"));
}
```

输出结果是

```java
Hello&#, world!
Hello%, world!
```

可以看出，在{}后面使用了?后，正则的匹配是**非贪婪模式**，扫描到`##`时，就已经满足pattern1的规则，故不再扫描到第三个#，而pattern2使用**贪婪模式**，因此匹配的是`###`

（3）分组和反向引用

> 小括号 `()` 可以达到对正则表达式进行分组的效果。

> 在以正则表达式替换字符串的语法中，是通过 `$` 来引用分组的反向引用，`$0` 是匹配完整模式的字符串（注意在 JavaScript 中是用 `$&` 表示）；`$1` 是第一个分组的反向引用；`$2` 是第二个分组的反向引用，以此类推。

> `String pattern = "(\\w)(\\s+)([.,])";`使用了 `[.]` 来匹配普通字符 `.` 而不需要使用 `[\\.]`。因为正则对于 `[]` 中的 `.`，会自动处理为 `[\.]`，即普通字符 `.` 进行匹配。

> 在小括号 `()` 内的模式开头加入 `?:`，那么表示这个模式仅分组，但不创建反向引用。

> 在小括号中使用 `?<name>` 将小括号中匹配的内容保存为一个名字为 name 的副本。

（4）Matcher、Pattern类的基本用法：

```java
String text = "Hello Regex!";
Pattern pattern = Pattern.compile("\\w+");
// Java 中忽略大小写，有两种写法：
// Pattern pattern = Pattern.compile("\\w+", Pattern.CASE_INSENSITIVE);
// Pattern pattern = Pattern.compile("(?i)\\w+"); // 推荐写法
Matcher matcher = pattern.matcher(text);
// 遍例所有匹配的序列
while (matcher.find()) {
    System.out.print("Start index: " + matcher.start());
    System.out.print(" End index: " + matcher.end() + " ");
    System.out.println(matcher.group());
}
```

- `Pattern.compile(正则表达式)`返回Pattern对象，Pattern类的构造函数是私有的，不能通过new来创建
- `pattern.matcher(字符串)`使用已经解析出来的pattern对象取匹配要处理的字符串，返回Matcher对象
- 关于Matcher类和Pattern类，参考：<https://blog.csdn.net/yin380697242/article/details/52049999>

> Pattern类
> 常用方法及介绍
>
> `Pattern complie(String regex)` 
> 由于Pattern的构造函数是私有的,不可以直接创建,所以通过静态方法compile(String regex)方法来创建,将给定的正则表达式编译并赋予给Pattern类
>
> `String pattern()` 返回正则表达式的字符串形式,其实就是返回Pattern.complile(String regex)的regex参数
>
> `Pattern.matcher(CharSequence input)` 对指定输入的字符串创建一个Matcher对象
>
> `Pattern.quote(String s)` 返回给定的字符串的字面量
>
> `matches()`方法编译给定的正则表达式并且对输入的字串以该正则表达式为模开展匹配,该方法适合于该正则表达式只会使用一次的情况,也就是只进行一次匹配工作,因为这种情况下并不需要生成一个Matcher实例

> Matcher类 常用方法及介绍
>
> `boolean matches()` 最常用方法:尝试对整个目标字符展开匹配检测,也就是只有整个目标字符串完全匹配时才返回真值
>
> `boolean lookingAt()` 对前面的字符串进行匹配,只有匹配到的字符串在最前面才会返回true
>
> `boolean find()` 对字符串进行匹配,匹配到的字符串可以在任何位置
>
> `int start()` 返回当前匹配到的字符串在原目标字符串中的位置
>
> `int end()` 返回当前匹配的字符串的最后一个字符在原目标字符串中的索引位置
>
> `String group()` 返回匹配到的子字符串

- 对同一个Matcher对象，如果上一次find()方法调用成功，那么它会记录下上次match的start和end，当再一次执行find时，Matcher会在end后面继续匹配，而不是从头开始
- Java正则源码解析参考：<https://blog.csdn.net/li20081006/article/details/21999779>



#### Java基本运算符

（1）&和&&的异同

> **电路问题：**
>
> 对于：&   -- >  不管怎样，都会执行"&"符号左右两边的程序
>
> 对于：&& -- >  只有当符号"&&"左边程序为真(true)后，才会执行符号"&&"右边的程序。
>
> **运算规则：**
>
> 对于：&  -- >  只要左右两边有一个为false，则为false；只有全部都为true的时候，结果为true
>
> 对于：&& -- > 只要符号左边为false，则结果为false；当左边为true，同时右边也为true，则结果为true
>
> ——来源：<https://www.cnblogs.com/hongten/p/hongten_java_yu.html>

```java
private static void operatorTest(String str) {
        if (str != null && !str.equals(" ")) System.out.println("str != null && !str.equals(\"\")");
        if (str != null & !str.equals(" ")) System.out.println("str != null & !str.equals(\"\")");
}
/**
* 第二个判断条件会抛出异常
*/
```

&可以是按位与运算和逻辑与运算，&&只能是逻辑与运算

（2）|和||的异同

类似于（1）中的&和&&，||和|都是表示“或”，区别是||只要满足第一个条件，后面的条件就不再判断，而|要对所有的条件进行判断



#### 基本数据类型及其包装类

（1）参考博客：<https://alexyyek.github.io/2014/12/29/wrapperClass/>

（2）自动装箱和自动拆箱

简单一点说，装箱就是自动将基本数据类型转换为包装器类型；拆箱就是自动将包装器类型转换为基本数据类型。

```java
Integer i = 10; //装箱
int index = i;  //拆箱
```

注意：

```java
Integer test = null;
int f = test.intValue();
```

这种写法会抛出空指针异常，编译不通过，因为将null赋值给Integer，这个时候不会进行自动装箱，也就没有创建Integer实例

（2）实现机制：在装箱的时候自动调用的是Integer的`valueOf(int)`方法。而在拆箱的时候自动调用的是Integer的`intValue()`方法。对其他的类型也类似，**装箱过程是通过调用包装器的valueOf方法实现的，而拆箱过程是通过调用包装器的xxxValue方法实现的。（xxx代表对应的基本数据类型）**

（3）关于null、void和Void的一些比较

> void不是函数，是方法的修饰符，void的意思是该方法没有返回值，意思就是方法只会运行方法中的语句，但是不返回任何东西。 java.lang.Void是一种类型。例如给Void引用赋值null。通过Void类的源代码可以看到，Void类型不可以继承与实例化。

- <https://blog.csdn.net/f641385712/article/details/80409211>
- <https://blog.csdn.net/claram/article/details/52053798>

摘录一些比较重要的知识如下：

- 如果使用了带有null值的引用类型变量，instanceof操作将会返回false

  ```java
  // 输出是false
  BoxingAndUnboxingTest test = null;
  System.out.println(test instanceof BoxingAndUnboxingTest);
  ```

- 可以使用或者!=操作来比较null值，但是不能使用其他算法或者逻辑操作，例如小于或者大于

  ```java
  Integer i = null;
  Integer j = null;
  System.out.println(i == j);
  System.out.println(i > j);
  ```

  输出是

  ```java
  true
  Exception in thread "main" java.lang.NullPointerException
  	at tk.chph.demo.box.BoxingAndUnboxingTest.test5(BoxingAndUnboxingTest.java:16)
  	at tk.chph.demo.box.BoxingAndUnboxingTest.main(BoxingAndUnboxingTest.java:9)
  
  ```

- class关键字用法

  ```java
  System.out.println(Void.class); //class java.lang.Void
  System.out.println(void.class); //void
  //类似于下面的
  System.out.println(Integer.class); //class java.lang.Integer
  System.out.println(int.class); //int
  
  ```

- Void类的用法：

  - 用于反射中判断某方法的返回值是否为void

    ```java
    for(Method method : Test.class.getMethods()) {
    	//判断返回值类型 用到了Void类
    	if(method.getReturnType().equals(Void.TYPE)) {
    		System.out.println(method.getName());
    	}
    }
    
    ```

    因为equals方法中不能传递关键字void，其中Void.TYPE源代码是`public static final Class<Void> TYPE = (Class<Void>) Class.getPrimitiveClass("void");`

  - 使用泛型时函数并不需要返回结果或某个对象不需要值时候这是可以使用java.lang.Void类型表示

  - ==操作符

    ```Java
    class AutoUnboxingTest {
        public static void main(String[] args) {
            Integer a = new Integer(3);
            Integer b = 3;                  // 将3自动装箱成Integer类型
            int c = 3;
            System.out.println(a == b);     // false 两个引用没有引用同一对象
            System.out.println(a == c);     // true a自动拆箱成int类型再和c比较
        }
    }
    
    ```

#### 字符串编码

（1）参考博客：<https://my.oschina.net/mojiewhy/blog/177473>

（2）主要有`getBytes(Charset charset)`和`String(byte bytes[], String charsetName)`两个方法：

> `getBytes(Charset charset)`：Encodes this {@code String} into a sequence of bytes using the charset, storing the result into a new byte array.
>
> `String(byte bytes[], String charsetName)`：Constructs a new {@code String} by decoding the specified array of bytes using the specified {@linkplain java.nio.charset.Charset charset}.

- 字符串在java中统一用unicode表示( 即utf-16 LE)

> Case mapping is based on the Unicode Standard version specified by the {@link java.lang.Character Character} class.

- 对于`String gbkStr = "你好哦!"; `这个方法：

  如果源码文件是GBK编码, 操作系统（windows）默认的环境编码为GBK，那么编译时,  JVM将 按照GBK编码将字节数组解析成字符，然后将字符转换为unicode格式的字节数组，**作为内部存储**。

  当打印这个字符串时，JVM 根据操作系统本地的语言环境，将unicode转换为GBK，然后操作系统将GBK格式的内容显示出来。

- `str.getBytes(Charset charset)`是按照给定的编码方式charset，将str内部存储的字节数据（实际上String类的内部实现里是维护一个私有的byte数组的）转换成一个新的byte数组并返回，这个时候新的byte数组的编码方式就是charset了

- `new String(bytes, charset)`是使用charset这个编码来对字节数组bytes进行解码，得到新的字符串，这个字符串内部依然会维护一个byte数组，编码方式就是charset指定的

- **getBytes 和 new String() 来进行编码转换**，核心工作都是在getbytes这个方法内部实现了，即将原编码方式的byte数组转换成给定的新的编码方式的byte数组，这时候实际上就已经完成了编码转换，new String只是按照新的编码方式将byte数组转出字符串对象而已，便于进行输出

- **如何正确的将GBK转UTF-8 ? （实际上是unicode转UTF-8)**

  ```java
  // 利用getBytes将unicode字符串转成UTF-8格式的字节数组
  byte[] utf8Bytes = gbkStr.getBytes("UTF-8"); 
  
  // 然后用utf-8 对这个字节数组解码成新的字符串
  String utf8Str = new String(utf8Bytes, "UTF-8");
  
  // 简化后就是:
  unicodeToUtf8 (String s) {
  	return new String( s.getBytes("utf-8") , "utf-8");
  }
  
  // UTF-8 转GBK原理也是一样
  return new String( s.getBytes("GBK") , "GBK");
  ```

- String类默认的编码是UTF-8（参考：<https://blog.csdn.net/sugar_rainbow/article/details/76945323>）

  ```java
  String str = "数据结构与算法";
  byte[] bytes = str.getBytes("utf8");
  for (byte b : str.getBytes()) {
  	System.out.print(b);
  }
  System.out.println();
  for (byte b : bytes) {
  	System.out.print(b);
  }
  System.out.println();
  System.out.println(new String(bytes, "unicode"));
  ```

  输出结果是

  ```
  -26-107-80-26-115-82-25-69-109-26-98-124-28-72-114-25-82-105-26-77-107
  -26-107-80-26-115-82-25-69-109-26-98-124-28-72-114-25-82-105-26-77-107
  냦趮鏦麄軧꺗�
  ```

  可见，`getBytes("utf8")`和`getBytes()`返回的byte数组是一样的，而且最后一句可以看到，utf-8和Unicode是不一样的

- 不同的编码在Java中所占用的字节数：<https://www.cnblogs.com/Qian123/p/5799835.html>

#### String 、StringBuffer和StringBuilder

（1）参考：<https://blog.csdn.net/weixin_41101173/article/details/79677982>

|                            String                            |                         StringBuffer                         |  StringBuilder   |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :--------------: |
| String的值是不可变的，这就导致每次对String的操作都会生成新的String对象，不仅效率低下，而且浪费大量优先的内存空间 | 是可变类和线程安全的字符串操作类，任何对它指向的字符串的操作都不会产生新的对象。每个StringBuffer对象都有一定的**缓冲区容量**，当字符串大小没有超过容量时，不会分配新的容量，当字符串大小超过容量时，会自动增加容量 | 可变类，速度更快 |
|                            不可变                            |                             可变                             |       可变       |
|                                                              |                           线程安全                           |    线程不安全    |
|                                                              |                       多线程操作字符串                       | 单线程操作字符串 |



### Java基础（二）

#### 数组（Array）和列表（ArrayList）的区别

（1）参考：<https://blog.csdn.net/m0_37683661/article/details/78778483>

|          | Array                                                        | ArrayList                                                    |
| -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 空间大小 | 空间大小是固定的，空间不够时也不能再次申请，所以需要事前确定合适的空间大小 | 动态增长，如果空间不够，它会创建一个空间比原空间大一倍的新数组，然后将所有元素复制到新数组中，接着抛弃旧数组。而且，每次添加新的元素的时候都会检查内部数组的空间是否足够 |
| 存储内容 | Array数组可以包含基本类型和对象类型；Array数组在存放的时候一定是**同种类型的元素** | ArrayList却**只能包含对象类型**；ArrayList可以存储不同类型的对象，因为ArrayList可以存储Object |
| 方法     |                                                              | 添加全部addAll()、删除全部removeAll()、返回迭代器iterator()等 |
| 应用场景 | 如果想要保存一些在整个程序运行期间都会存在而且不变的数据，我们可以将它们放进一个全局数组里 | 如果需要频繁对数组进行增删改查操作，使用ArrayList会很方便；但是如果需要对元素进行频繁的移动或删除，或者是处理的是超大量的数据，使用ArrayList的效率很低，而使用Array进行这样的动作很麻烦，那么，我们可以考虑选择LinkedList |

（2）值得注意的是，Array中的包装类和基本类型可以混用（本质上是自动装箱和自动拆箱）

```java
Integer[] arr = new Integer[3];
arr[0] = 1;
arr[2] = Integer.valueOf("2");
arr[1] = null;
for (Integer i : arr) System.out.println(i);

```

for循环使用的是Integer，所以null可以正常输出，如果改成int，那么null就会报错，因为没法自动拆箱成基本类型

#### 值传递和引用传递

（1）首先，Java内的传递使用的都是**值传递**，这里面有两层意思：

- 对于基本数据类型，传递的是变量的值

- 对于对象的引用传递，传递的只是对象引用的值（即只是传递引用指向的对象的地址，而不是对象本身，也不是**对象的引用的引用**）

- 参考博客：https://blog.csdn.net/javazejian/article/details/51192130

  ```java
  public class CallByValue {
  	private static User user=null;
  	private static User stu=null;
  	
  	/**
  	 * 交换两个对象
  	 * @param x
  	 * @param y
  	 */
  	public static void swap(User x,User y){
  		User temp =x;
  		x=y;
  		y=temp;
  	}
  	
  	
  	public static void main(String[] args) {
  		user = new User("user",26);
  		stu = new User("stu",18);
  		System.out.println("调用前user的值："+user.toString());
  		System.out.println("调用前stu的值："+stu.toString());
  		swap(user,stu);
  		System.out.println("调用后user的值："+user.toString());
  		System.out.println("调用后stu的值："+stu.toString());
  	}
  }
  
  ```

  执行结果如下：

  ```
  调用前user的值：User [name=user, age=26]
  
  调用前stu的值：User [name=stu, age=18]
  
  调用后user的值：User [name=user, age=26]
  
  调用后stu的值：User [name=stu, age=18]
  
  ```

  可以这么理解：`Object obj = new Object();`变量obj的**值**是新的对象`new Objext()`的地址，obj在方法调用时`call(Object obj1)`传递是值传递，*就是把obj的值（新对象的地址）拷贝到了obj1中*，这时候obj1持有的引用是`new Object()`，而不是obj，所以obj所做的任何修改只是对对象的修改，而对obj不会产生任何影响，只是副作用是obj指向的对象自身属性可能被（obj1）修改了

####  Java8新特性

##### Lambda表达式

（1）对于只有一个抽象方法（可以是**重载**的方法）的接口称为**函数式接口**，函数式接口与lambda兼容；实际上Java中的lambda表达式能做的也就是转换为函数式接口而已

（2）方法引用：使用`::`操作符来分隔对象或类与方法

- object::instanceMethod（等价于(..)->object.instanceMethod(..)）
- Class::staticMethod（等价于(..)->Class.staticMethod(..)）
- **Class::instanceMethod**

特别注意的是第三个方法，它的参数列表中第一个参数会成为方法的目标（即调用该方法的对象），例如：`String::compareToIngnoreCase`等同于`(x, y)->x.compareToIgnoreCase(y)`

（3）当有多个同名的重载方法时，编译器会自动选择最符合的那个版本

（4）lambda表达式中可以有**this**和**super**关键字

（5）`Class::new`可以调用一个构造器，具体是哪个构造器会根据上下文进行推导

（6）lambda表达式的三个部分：

- 一个代码块
- 参数
- 自由变量的值，指的是非参数而且不是在代码块中定义的变量

例如：

```java
public static void repeatMessage(String text, int delay){
	ActionListener listener = event -> {
        //注意text变量
		System.out.println(text);
		Toolkit.getDefaultToolkit().beep();
	};
    new Timer(delay, listener).start();
}

```

这里的text就是自由变量；由于repeatMessage和listener方法不是同步调用的，有可能出现repeatMessage调用结束后，listener才调用，而这时候text这个变量很可能已经被回收了，因此listener要正常运行，必须捕获自由变量的值

（7）**闭包**

（8）lambda表达式中只能引用值不会改变的变量，而且lambda表达式中捕获的变量必须实际上是最终变量（final或者像String这种不会改变的变量）

（9）lambda表达式与嵌套块有相同的作用域，因此lambda表达式中声明一个与局部变量同名的参数或者局部变量是不合法的

```java
// 报错：Variable ‘event’ is already defined in the scope
public static void repeatMessage(String text, int delay){
    int event = 1;
    ActionListener listener = event -> {
        System.out.println(text);
        Toolkit.getDefaultToolkit().beep();
	};
}

// 报错：Variable ‘i’ is already defined in the scope
public static void repeatMessage(String text, int delay){
    int i = 1;
    ActionListener listener = event -> {
        int i;
        System.out.println(text);
        Toolkit.getDefaultToolkit().beep();
    };
}

```

（10）lambda表达式中的this关键字是指创建这个lambda表达式的方法的this参数：

```java
package tk.chph.demo.lambda;

interface Listener {
    void test();
    default void method1(){
        System.out.println("Listener");
    }
}

public class LambdaTest {
    public static void main(String[] args) {
        new LambdaTest().run();
    }

    public void method1() {
        System.out.println("Lambda Test");
    }

    public void run() {
        Listener listener = () -> {
            // 这里的this关键字指的都是LambdaTest这个类的实例的引用，而不是listener的引用
            this.method1();
            method1();
        };
        listener.test();
    }
}

```

输出结果是：

```java
Lambda Test
Lambda Test

```



#### 各个jdk版本的新特性

（1）



#### 线程

（1）Java中实现一个线程的方法有：

- 继承Thread类

- 实现Runnable接口

  事实上，Thread类也实现了Runnable接口，并且自动获得了run方法

（2）为什么通过实现Runnable接口来实现线程的方法好？

- 因为Java中类是单继承的，如果使用继承Thread类的方式来实现线程，那么他就不能再继承其他的类了
- 从面向对象的角度来看，继承一个类的目的更多是在原有类的基础上提供新的特征或者功能；如果我们只是想使用run()方法来使得我们的代码能够和主线程并行运行，实现Runnable接口是个更好的选择

（3）Java线程的生命周期

[![Thread in Java.png](https://i.loli.net/2019/05/20/5ce1fb9627bff98011.png)](https://i.loli.net/2019/05/20/5ce1fb9627bff98011.png)

- 当我们创建实现了Runnable接口或者继承了Thread类的实例后，我们只是创建了一个线程对象（处在图中的**NEW状态**），只有当我们调用start()方法后，这个线程才会启动（这时是在**RUNNABLE状态**），在start方法内部会调用run方法
- 处于RUNNABLE状态的线程还不一定在执行，需要等待线程调度器分配CPU资源去运行线程，也就是说，线程的**run方法是由JVM调用的**
- run()方法和start方法的区别
  - when program calls start() method a **new Thread** is created and code inside run() method is executed in new Thread
  - if you call run()method directly **no new Thread is created** and code inside run() will execute on **current Thread**
  - Another **difference between start vs run in Java thread** is that you **can not call start() method twice** on thread object. once started, second call of start() will throw `IllegalStateException` in Java while you can call run() method twice
- 小知识点
  - 线程执行完毕的顺序和线程start的顺序并不一致，这取决于jvm的线程调度器
  - 当线程中的run方法运行结束后，线程进入**DEAD状态**，这时候再调用start方法，线程也无法再次启动

（4）线程与进程的区别

- 线程是进程的子集，一个进程可以有很多线程，每条线程并行执行不同的任务
- 不同的进程使用不同的内存空间，而所有的线程共享一片相同的内存空间（**堆内存**）
- 每个线程都拥有单独的**栈内存**用来存储本地数据

（5）Java内存模型(JMM)的Happens-before规则

![](https://i.loli.net/2019/05/20/5ce207dc2cb5916216.png)

- 程序顺序规则：一个线程中的每个操作，happens-before于该线程中的任意后续操作。

- 监视器锁规则：对一个锁的解锁，happens-before于随后对这个锁的加锁。

  volatile变量规则：对一个volatile域的写，happens-before于任意后续对这个volatile域的读。

- 传递性：如果A happens-before B，且B happens-before C，那么A happens-before C。

- start()规则：如果线程A执行操作ThreadB.start()（启动线程B），那么A线程的ThreadB.start()操作happens-before于线程B中的任意操作。

- join()规则：如果线程A执行操作ThreadB.join()并成功返回，那么线程B中的任意操作happens-before于线程A从ThreadB.join()操作成功返回。

- 程序中断规则：对线程interrupted()方法的调用先行于被中断线程的代码检测到中断时间的发生。

- 对象finalize规则：一个对象的初始化完成（构造函数执行结束）先行于发生它的finalize()方法的开始。

（6）volatile变量

> 参考：<https://javarevisited.blogspot.com/2011/06/volatile-keyword-java-example-tutorial.html>

![volatile variable in Java.png](https://i.loli.net/2019/05/20/5ce20ac393e1935315.png)

- Java中的**volatile**关键字是用来指示Java编译器和线程不要缓存这个变量的值，始终都从主存中读取变量的值

  > Anyway,  the volatile keyword in Java is used as an indicator to Java compiler and Thread that do not cache value of this variable and always read it from main memory

- 当我们需要使得对某个变量的读或者写操作是原子性的（例如对int或者boolean变量的读或者写），那么我们可以将其声明为volatile

  > So if you want to share any variable in which read and write operation is atomic by implementation e.g. read and write in an int or a boolean variable then  you can declare them as volatile variable

- volatile关键字只能用在变量上，不能用在类或者方法上

  > The Java volatile keyword cannot be used with method or class and it can only be used with a variable

- volatile关键字可以用在多线程环境的单例模式中

  ```java
  public class Singleton{
  private static volatile Singleton _instance; //volatile variable 
  
  public static Singleton getInstance(){
  
     if(_instance == null){
              synchronized(Singleton.class){
                if(_instance == null)
                _instance = new Singleton();
              }
  
     }
     return _instance;
  
  }
  
  ```

  如果不将\_instance声明为volatile，其中一个线程在创建了_instance后就失去了CPU资源，这时候其他线程的看到的\_instance变量依然是null的，这时候其他线程就有可能再次创建\_instance对象，这样Singleton类的实例就有可能不是单例模式的了

（7）如何停止一个线程

- JDK 1.0本来有一些像stop(), suspend() 和 resume()的控制方法但是由于潜在的死锁威胁因此在后续的JDK版本中他们被弃用

- 当run方法执行结束或者跑出异常时，线程会自动结束（<u>*简单的说，如果线程运行过程中抛出的异常没有被捕获，该线程将会停止执行*</u>）

- 可以通过结合volatile设置标记位来手动中断线程的执行

  - 代码示例

    ```java
    private class Runner extends Thread{
        volatile boolean bExit = false;
      
        public void exit(boolean bExit){
            this.bExit = bExit;
        }
      
        @Override
        public void run(){
            while(!bExit){
                System.out.println("Thread is running");
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException ex) {
                        Logger.getLogger(ThreadTester.class.getName()).log(Level.SEVERE, null, ex);
                    }
            }
        }
    }
    
    ```

    当我们调用exit()方法将bExit设置为true时，run方法就会在新一轮迭代开始时检测到，然后终止循环

  - 要注意，每个线程都有自己的本地内存，bExit变量也一样，因此要使得bExit值的改变能够立即被其他线程看到，需要将bExit设置为volatile变量

（8）线程之间的消息传递

- 生产者消费者模式：where One thread produce and put something on shared bucket, and then tell other thread that there is an item for your interest in shared object, consumer thread than pick than item and do his job

- 如果不使用wait()和notify()/notifyAll()方式，消费者线程就必须得周期性地轮询bucket（一个共享对象），即使bucket的状态没有发生变化

- **消费者在检查条件的时候，要使用while循环而不是if判断**

  主要是为了避免虚假唤醒的情况，这样线程在休眠前和唤醒后都会进行条件的检查；因为有可能有多个线程在条件改变时同时被唤醒，其中的一些线程由于没有得到即使的响应，被其他唤醒的线程将条件改变了，那些没有及时响应的线程在获得CPU资源后，如果使用的是if检查添加，那么它就不会再去检查条件而是直接对共享对象进行操作，导致出错；如果使用的是while检查条件，那么它被唤醒之后，无论如何都回再去检查条件，如果条件没有满足，它会再次进入休眠状态而不是取操作共享对象

  > While if you call notifyAll method, all threads waiting on that lock will be woken up, but again all woken thread will fight for lock before executing remaining code and that's why wait is called on loop because if multiple threads are woken up, the thread which will get lock will first execute and it may reset waiting for condition, which will force subsequent threads to [wait](http://javarevisited.blogspot.sg/2011/12/difference-between-wait-sleep-yield.html).

（9）notify()和notifyAll()的区别

> The main difference between notify and notifyAll is that notify method will only notify one [Thread](http://javarevisited.blogspot.sg/2011/02/how-to-implement-thread-in-java.html) and notifyAll method will notify all Threads  which are waiting on that monitor or lock

- notify()唤醒的线程是哪一个是无法保证的，这由线程调度器来决定

- 当我们的程序中唤醒休眠中的线程，最终只会有一个线程会获得资源对共享对象进行操作的时候，我们应该使用notify()而不是notifyAll()，而且这样还能够节省CPU资源

  > You can use notify over notifyAll if all thread are waiting for the same condition and only one Thread at a time can benefit from condition becoming true. In this case, notify is optimized call over notifyAll because waking up all of them because we know that only one thread will benefit and all other will wait again, so calling notifyAll method is just waste of CPU cycles

（10）为什么wait、notify和notifyAll是在Object类中定义的而不是Thread类

> 参考：<https://javarevisited.blogspot.com/2012/02/why-wait-notify-and-notifyall-is.html>

（11）Thread.interrupted和isInterrupted的区别

> *interrupted()* 和 *isInterrupted()*的主要区别是**<u>*前者会将中断状态清除而后者不会*</u>**。Java多线程的中断机制是用内部标识来实现的，调用*Thread.interrupt()*来中断一个线程就会设置中断标识为true。当中断线程调用[静态方法](https://link.juejin.im/?target=http%3A%2F%2Fjava67.blogspot.com%2F2012%2F11%2Fwhat-is-static-class-variable-method.html)*Thread.interrupted()*来检查中断状态时，中断状态会被清零。而非静态方法isInterrupted()用来查询其它线程的中断状态且不会改变中断状态标识。简单的说就是任何抛出InterruptedException异常的方法都会将中断状态清零。无论如何，一个线程的中断状态有有可能被其它线程调用中断来改变。

（12）如果wait和nofity不在同步块中调用，代码就会抛出IllegalMonitorStateException异常



### Java面向对象

#### 接口

（1）接口中的所有方法自动地属于public ，因此, 在接口中声明方法时 , 不必提供关键字
public；但是在实现接口时，必须把方法声明为public，否则编译器认为该方法的访问属性是包可见性

```java
interface InterfaceA {
    void method1();
}

public class ImplA implements InterfaceA {
    // 这里会报错
    // Error:(4, 10) java: tk.chph.demo.interface_.ImplA中的method1()无法实现tk.chph.demo.interface_.InterfaceA中的method1()正在尝试分配更低的访问权限; 以前为public
    void method1() {
        
    }
}

```

（2）接口可以定义常量，但是不能有实例域；在jdk8之前，接口中的方法也不能含有实现

（3）可以使用`instanceof`检查一个类的实例是否实现了特定的接口

```Java
new ImplA() instanceof InterfaceA;

```

（4）接口中不能包含**实例域**或者**静态方法（1.8之前）**，但是可以包含**常量**；接口中的方法自动的设置为`public`，而接口中的域也自动被设置为`public static final`

（5）使用接口不使用抽象类的原因：Java中的类只能扩展一个抽象类或者超类，而可以实现多个接口；多重继承会使得语言本身变得复杂，同时效率低下

（6）jdk1.8之后interface中允许有静态方法，即static修饰的类方法，但是必须为其提供实现体；同时interface中也允许提供以default修饰的成员方法的默认实现，且默认方法可以调用任何其他的方法

```java
public interface InterfaceA {
    void method1();

    static void method2(){
        
    }
    
    default void method3(){
        method1();
        method2();
    }
}

```

（7）默认方法冲突：如果一个接口中的一个默认方法和另一个超类或者接口中定义的方法签名相同，这是就产生了默认方法冲突问题，解决方法如下：

- **超类优先**：如果超类提供了一个具体方法（如果是抽象的方法，那么实现类要么是实现该方法，要么是让该方法依旧抽象，从这一点来看，超接口的默认方法依然是被忽略的），且签名和默认方法相同，那么具体的实现是选择超类中的实现，忽略超接口的所有同签名方法不论是否是默认的
- （ps：超类优先原则可以保证jdk8之前的代码兼容性，jdk8之后的接口提供签名相同的默认方法并不会造成冲突）
- （pss：默认方法重新定义Object类中的方法无效，因为所有的类都继承自Object，如果为toString或者equals这样的Object类方法定义默认方法，由于类优先原则，Object.toString或者Objects.equals方法会覆盖该默认方法）
- **接口冲突**：如果是超接口提供了一个同签名的默认方法，那么实现类必须覆盖该默认方法以解决冲突

```java
package tk.chph.demo.interface_;

public class ImplA extends AbstractA implements InterfaceA, InterfaceB {

    @Override
    public void method4() {

    }

    public static void main(String[] args) {
        new ImplA().method5();
    }
}

abstract class AbstractA {
    public abstract void method4();

    public void method5(){
        System.out.println("AbstractA");
    }
}


interface InterfaceA {
    void method4();

    default void method5(){
        System.out.println("InterfaceA");
    }
}


interface InterfaceB {
    void method4();

    default void method5(){
        System.out.println("InterfaceB");
    }
}

```

输出为：AbstractA

如果将AbstractA这个抽象类去掉，那么输出结果是：

```java
Error:(3, 8) java: 类 tk.chph.demo.interface_.ImplA从类型 tk.chph.demo.interface_.InterfaceA 和 tk.chph.demo.interface_.InterfaceB 中继承了method5() 的不相关默认值

```

必须在ImplA中覆盖method5方法后才能编译通过

```java
package tk.chph.demo.interface_;

public class ImplA implements InterfaceA, InterfaceB {

    @Override
    public void method4() {

    }

    @Override
    public void method5() {
        System.out.println("ImplA");
    }

    public static void main(String[] args) {
        new ImplA().method5();
    }
}

```

输出为：ImplA

当然也可以在ImplA中选择超接口的其中一个method5实现：

```java
@Override
public void method5() {
	InterfaceA.super.method5();
}

```

这里的例子是两个超接口都的冲突默认方法都提供了实现，然而实际上是，InterfaceA和InterfaceB只要至少一个提供了默认方法，那个实现类都必须覆盖该默认方法了

#### Comparable 和 Comparator接口

（1）参考博客：<https://www.cnblogs.com/skywang12345/p/3324788.html>

（2）若一个类实现了Comparable接口，就意味着“**该类支持排序**”。  “实现Comparable接口的类的对象的List列表(或数组)”，可以通过 Collections.sort（或 Arrays.sort）进行排序。

```java
package java.lang;
import java.util.*;

// 通过 x.compareTo(y) 来“比较x和y的大小”。若返回“负数”，意味着“x比y小”；返回“零”，意味着“x等于y”；返回“正数”，意味着“x大于y”。
public interface Comparable<T> {
    public int compareTo(T o);
}

```

（3）Comparator 是比较器接口，通过实现Comparator构建一个比较器，可以用来对某个没有实现Comparable接口的类进行排序

> 也就是说，我们可以通过“**实现Comparator类来新建一个比较器**”，然后通过该比较器对类进行排序。

```java
package java.util;

// “比较o1和o2的大小”。返回“负数”，意味着“o1比o2小”；返回“零”，意味着“o1等于o2”；返回“正数”，意味着“o1大于o2”
public interface Comparator<T> {

    int compare(T o1, T o2);

    boolean equals(Object obj);
}

```

若一个类要实现Comparator接口：它一定要实现compareTo(T o1, T o2) 函数，但可以不实现 equals(Object obj) 函数。理由还是“类优先原则”，所有类都继承了Object的equals方法，因此超接口中的equals不用去实现也可以编译通过

（4）Comparable相当于“内部比较器”，而Comparator相当于“外部比较器”。

#### Cloneable接口和clone()方法

（1）参考博客：<https://www.cnblogs.com/xrq730/p/4858937.html>

（2）Cloneable接口

- 实现Cloneable接口（该接口是一个标记接口，内部没有任何方法），以指示Object的clone()方法**可以合法地对该类实例进行按字段复制**
- 如果在**没有实现Cloneable接口的实例上调用Object的clone()方法，则会导致抛出CloneNotSupporteddException**
- 实现此接口的类应该**使用公共方法重写Object的clone()方法**，Object的clone()方法在定义时是protected的，即只能在java.lang包下的类和子类能够调用clone()方法，所以在重写clone方法时要将修饰符改成public，以便重写的clone方法能够被其他类调用

（3）clone方法应该满足以下条件：

- **x.clone() != x为true**
- **x.clone().getClass() == x.getClass()为true**
- x.clone().equals(x)一般情况下为true，但这并不是必须要满足的要求

（4）**clone方法不会调用类的构造函数**

（5）**浅克隆和深克隆**

​		浅克隆（shallow clone）和深克隆（deep clone）反映的是，当对象中还有对象的时候，那么：

- 浅克隆，即很表层的克隆，如果我们要克隆对象，只克隆它自身以及它所包含的所有**对象的引用地址**
- 深克隆，克隆除自身对象以外的所有对象，包括自身所包含的**所有对象实例**

（6）其实Object的clone()方法，提供的是一种**浅克隆的机制**，如果想要实现对对象的深克隆，在不引入第三方jar包的情况下，可以使用两种办法：

- 先对对象进行序列化，紧接着马上反序列化出
- 先调用super.clone()方法克隆出一个新对象来，然后在子类的clone()方法中手动给克隆出来的非基本数据类型（引用类型）赋值，比如ArrayList的clone()方法

#### 内部类

（1）内部类的特点：

- 内部类方法可以访问该类定义所在的作用域中的数据，包括私有的数据
- 可以对同一个包中的其他类隐藏起来
- 当要定义一个回调函数且不想编写大量代码时，可以使用**匿名内部类**

（2）内部类可以访问自己定义的成员，也可以访问创建它的外围类对象的成员（包括私有的）

```Java 
package tk.chph.demo.inner;

public class InnerClassTest {
    private int a;
    
    public class Inner{
        private void method(){
        	// 访问外围类的成员变量a
            System.out.println(a);
        }
    }
}

// 反编译class文件后是这样的
public class InnerClassTest {
    private int a;

    public InnerClassTest() {
    }

    public class Inner {
        public Inner() {
        }

        private void method() {
            // 注意这里
            System.out.println(InnerClassTest.this.a);
        }
    }
}

```

实际上内部类的对象中有一个**隐式的引用**指向了创建它的外围类的对象；实际上编译器修改了所有内部类的构造函数，添加了一个外围类引用的参数，如下：

```java
// 为了便于说明，将外围对象的引用定义为outer
public Inner(InnerClassTest test) {
	this.outer = test;
}

```

当InnerClassTest创建Inner类时，会这样调用构造函数：`Inner inner = new Inner(this);`将自身的引用传递到内部类对象中

（3）只有内部类可以是**私有类**, 而常规类只可以具有包可见性 , 或公有可见性

（4）内部类对外围类的引用正式语法是：`OuterClass.this`

（5）外围类对象调用内部类的构造器的正式语法是：`outerObject.new InnerClass(construction parameters)`

（6）在外围类的作用域外，可以这样引用其内部类：`OuterClass.InnerClass`

（7）内部类声明的所有静态域都必须是final，原因是我们希望每一个静态域都只有一个实例，但是对每一个外部对象，它都会被分配一个单独的内部类实例（就是说不同外部对象的内部类实例都是不一样的对象），如果内部类静态域不是final，那么就可能出现不是唯一的情况

```java
private class Inner{
	// 报错：Inner classes cannot have static declarations
	private static int b = 10;
	private void method(){
		System.out.println(a);
	}
    
    // 报错：Inner classes cannot have static declarations
    private static void test(){
            
    }
}

```

（8）外围类可以访问内部类的任何成员，包括私有的

（9）局部内部类：定义在一个方法内的类

- 据不累不能用public或private访问修饰符声明，因为它的作用域被限制在声明这个局部内部类的块中
- 对外部世界，局部内部类可以完全隐藏起来

```java
package tk.chph.demo.inner;

public class InnerClassTest {
    {
        class InnerClass{}
    }

    static {
        class InnerClass{}
    }

    void test1(){
        class InnerClass{}
    }

    static void test2(){
        class InnerClass{}
    }
}

```

（10）局部内部类可以访问其所在块的作用域内的局部变量

```java
void test1(int a){
    class InnerClass{
        private void test(){
            System.out.println(a);
        }
    }
    new InnerClass().test();
}

```

和lambda的捕获一样，由于可能出现test1方法执行结束后局部变量消失，而内部类还未执行的情况，内部类会在外部块释放局部变量之前，将局部变量进行备份，实际上的实现是这样的：

<u>*编译器会修改局部内部类的成员和构造函数，开始时先检测局部内部类对局部变量的访问，为每个局部变量建立相应的数据域，然后将局部变量拷贝到构造器中，以便这些局部内部类的数据域初始化为局部变量的副本*</u>

而且，引用的局部变量必须是final的或者是实际上不变的对象：

```java
void test1(int a){
    class InnerClass{
        private void test(){
        	// 报错：Variable 'a' is accessed from within inner class, needs to be final or effectively final
            a = 10;
            System.out.println(a);
        }
    }
    new InnerClass().test();
}

```

因此上面划线部分补充一下，内部类建立的数据域实际上是final的，对它初始化后不能够再进行修改，所以出现`a=10`时会报错，即使是在内部类外面修改也不行：

```java
void test1(int a) {
	// 这里修改
    a = 10;
    class InnerClass {
        private void test() {
        	// 报错：Variable 'a' is accessed from within inner class, needs to be final or effectively final
        	System.out.println(a);
        }
    }
    new InnerClass().test();
}

```

总之，只要局部变量被局部内部类引用了，它就必须是final或者实际上的final了：

```java
void test1(final int a) {
    final int b = 90;
    class InnerClass {
        private void test() {
            System.out.println(b);
            System.out.println(a);
        }
    }
    new InnerClass().test();
}

```

（11）如果一个类只被创建一个对象，那就不必命名了，这种类称为**匿名内部类**

- 如果构造参数的括号后面跟一个大括号，那么正在定义的是一个匿名内部类
- 由于匿名内部类没有类名，自然就没有构造函数，取而代之的是将构造器参数传递给超类的构造器

```java
package tk.chph.demo.inner;

abstract public class AbstractClass {
    protected int i;

    AbstractClass(int i) {
        this.i = i;
    }

    abstract public void test(String name);

    public static void main(String[] args) {
        AbstractClass c = new AbstractClass(10) {
            @Override
            public void test(String name) {
                System.out.println(this.i + ": " + name);
            }
        };
        c.test("steve");
    }
}
```

由于AbstractClass是抽象类，他不能直接被实例化，new出来的实际上是AbstractClass的一个子类（实现类），这个子类没有类名，是个匿名内部类，没有自己的构造函数，要将构造参数传递给超类的构造器

- 匿名内部类（子类）进行`getClass() != other.getClass()`测试时会失败：

```java
AbstractClass c = new AbstractClass(10) {
    @Override
    public void test(String name) {
    	System.out.println(this.i + ": " + name);
    }
};
AbstractClass abstractClass = new AbstractClass(10) {
    @Override
    public void test(String name) {
    	System.out.println(this.i + ": " + name);
    }
};
System.out.println(c.getClass() != abstractClass.getClass());
StringBuilder sb1 = new StringBuilder();
StringBuilder sb2 = new StringBuilder();
System.out.println(sb1.getClass() != sb2.getClass());
```

输出为：

```java
true	//注意这个
false

```

（12）静态内部类

- 只有内部类可以声明为static，静态内部类除了没有外围类的引用外，其他功能和所有内部类相同

- 静态内部类的初始化实在静态方法中完成的

- 静态内部类可以有静态域和方法

- 声明在接口中的内部类自动成为static和public类

  ```java
  package tk.chph.demo.inner;
  
  interface InterfaceA {
      class InnerClass{
          void test(){
              System.out.println("InnerClass in InterfaceA");
          }
      }
  }
  
  public class OuterClassA {
      static class InnerClassA{
          // 静态内部类拥有静态域
          public static final String name = "InnerClassA";
  
          // 静态内部类拥有静态方法，普通的内部类不能拥有静态方法，自然就不能有main方法
          public static void main(String[] args) {
              // 这里直接通过接口名引用接口中定义的内部类
              InterfaceA.InnerClass innerClass = new InterfaceA.InnerClass();
          }
      }
  }
  
  
  ```

- 由于方法内部不能用static修饰变量，因此无法在方法内部声明一个静态局部内部类



### 泛型

#### 泛型方法

（1）可以通过在修饰符（例如public static）后面、返回类型前面添加类型变量\<T>来声明一个泛型方法：

```java
package tk.chph.demo.generic;

public class GenericTest {
    <T> void test1(T t){
        System.out.println(t);
    }
}

```

（2）泛型方法的调用是在方法名前面加上<类型变量>：

```java
public static void main(String[] args) {
	new GenericTest().<String>test1("string");
    // It's also ok like this
    // new GenericTest().test1("string");
}

```

当然调用时的类型变量是可以省略的，编译器可以通过上下文推断出来

#### 类型变量的限定

（1）可以使用`<T extends SuperClass/SuperInterface>`的语法来对类型变量T做限定，意思是T是绑定类型的子类型（subtype），这里的extends关键字不是继承的意思，因为绑定类型是接口也是可以的

```java
package tk.chph.demo.generic;

interface InterfaceA{
    
}

public class GenericTest {
	
    <T extends InterfaceA> void test1(T t){
        System.out.println(t);
    }

    public static void main(String[] args) {
    	// 这时候就会报错：
    	// Error:(13, 26) java: 无法将类 tk.chph.demo.generic.GenericTest中的方法 test1应用到给定类型;
        //  需要: T
        //  找到: java.lang.String
        //  原因: 显式类型参数java.lang.String不符合声明的范围tk.chph.demo.generic.InterfaceA
        new GenericTest().<String>test1("string");
    }
}

```

（2）一个类型变量或通配符可以有多个限定，限定类型用&分隔，逗号用来分隔类型变量

```java
<T extends InterfaceA & Comparable, U extends InterfaceA & Cloneable> 
void test2(T t, U u) {
    System.out.println(t);
    System.out.println(u);
}

```

（3）根据Java的继承和实现接口原则，只能够单继承多实现，而且继承在实现前面，所以如果使用类作为限定，它必须是限定列表的第一个

#### 泛型和虚拟机：类型擦除

（1）有关 Java 泛型转换的事实 :

- 虚拟机中没有泛型 , 只有普通的类和方法 
- 所有的类型参数都用它们的限定类型替换 
- 桥方法被合成来保持多态 
- 为保持类型安全性, 必要时插人强制类型转换



### Javascript

#### 对象的访问方法

对象Person的**字面量创建**方法

```JavaScript
var person = {
  name : {
  	first : 'Bob',
  	last : 'Smith'
  },
  age : 32,
  gender : 'male',
  interests : ['music', 'skiing'],
  bio : function() {
    alert(this.name[0] + ' ' + this.name[1] + ' is ' + this.age + ' years old. He likes ' + this.interests[0] + ' and ' + this.interests[1] + '.');
  },
  greeting: function() {
    alert('Hi! I\'m ' + this.name[0] + '.');
  }
};

```

##### 点表示法

```JavaScript
person.age
person.interests[1]
person.bio()

```

##### 括号表示法

```JavaScript
person['age']
person['name']['first']

```

在这个意义上，使用了**关联了值的名字**，而不是**索引**去选择元素，因此对象有时被称之为关联数组(associative array)——对象做了**字符串到值的映射**，而数组做的是**数字到值的映射**。

##### 创建对象成员

```JavaScript
person['eyes'] = 'hazel'	// 括号表示法
person.farewell = function() { alert("Bye everybody!") }	// 点表示法

// 测试新成员
person['eyes']
person.farewell()

```

此外，使用括号表示法可以动态设置成员的名字，而点表示法不可以（点表示法只能接受字面量的成员的名字，不接受变量作为名字）

```js
var myDataName = nameInput.value
var myDataValue = nameValue.value
person[myDataName] = myDataValue

```

这里的nameInput里的值完全可以在运行脚本的时候，通过\<input/>标签让用户动态输入

#### 构建函数和对象

##### 构建函数

JavaScript 用一种称为**构建函数**的特殊函数来定义对象和它们的特征

```js
function Person(name) {
  this.name = name;
  this.greeting = function() {
    alert('Hi! I\'m ' + this.name + '.');
  };
}

```

这个构建函数是 JavaScript 版本的类。它只定义了对象的属性和方法，除了没有明确创建一个对象和返回任何值和之外，它有了函数所拥有的全部功能

```js
var person = new Person('Bob');

```

这样使用new，通过构建函数创建了类Person的一个实例（*<u>实际的方法，比如greeting()仍然是定义在类里面， 而不是在对象里面</u>*）

##### 创建对象的其他方法

使用`Object()`构造函数来创建一个新对象。一般对象都有构造函数，它创建了一个空的对象

```js
var person = new Object();

```

这样创建了一个空的对象person，然后, 可以根据需要, 使用点或括号表示法向此对象添加属性和方法

```js
person.name = 'Chris';
person['age'] = 38;
person.greeting = function() {
  alert('Hi! I\'m ' + this.name + '.');
}

```

可以将对象文本传递给Object() 构造函数作为参数， 以便用属性/方法填充它

```js
var person = new Object({
  name : 'Chris',
  age : 38,
  greeting : function() {
    alert('Hi! I\'m ' + this.name + '.');
  }
});

```

##### 使用create()方法

JavaScript有个内嵌的方法`Object.create()`, 它允许您基于现有对象创建新的对象

```js
var newPerson = Object.create(person);

```

newPerson和person具有相同的属性和方法，即create方法创建了原对象的一个副本

#### JavaScript原型链与继承

##### 参考资料

- <https://juejin.im/post/58f94c9bb123db411953691b>
- <https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes>

##### 确定原型和实例的关系

```js
function Father(){
	this.property = true;
}
Father.prototype.getFatherValue = function(){
	return this.property;
}
function Son(){
	this.sonProperty = false;
}
//继承 Father
Son.prototype = new Father();//Son.prototype被重写,导致Son.prototype.constructor也一同被重写
Son.prototype.getSonVaule = function(){
	return this.sonProperty;
}
var instance = new Son();

```

（1）instanceof操作符来测试实例(instance)与原型链中出现过的构造函数，结果就会返回true

```JavaScript
alert(instance instanceof Object);//true
alert(instance instanceof Father);//true
alert(instance instanceof Son);//true

```

（2）使用 **isPrototypeOf()** 方法, 同样只要是原型链中出现过的原型，isPrototypeOf() 方法就会返回true

```js
alert(Object.prototype.isPrototypeOf(instance));//true
alert(Father.prototype.isPrototypeOf(instance));//true
alert(Son.prototype.isPrototypeOf(instance));//true

```

##### 借用构造函数

即在子类型构造函数的内部调用超类型构造函数，也叫作经典继承

```js
function Father(){
	this.colors = ["red","blue","green"];
}
function Son(){
	Father.call(this);//继承了Father,且向父类型传递参数
}
var instance1 = new Son();
instance1.colors.push("black");
console.log(instance1.colors);//"red,blue,green,black"

var instance2 = new Son();
console.log(instance2.colors);//"red,blue,green" 可见引用类型值是独立的

```

这样就保证了原型链中的引用类型的值是独立的，不会被所有实例共享

这里本质上是Son类通过this来调用Father构造函数中的方法实现，用来构造Son实例，但是Son类、实例和Father类之间**不具有任何继承关系**，因此Father类中定义的方法对Son是不可见的，因为无法通过原型链到达Father类

##### 组合继承

使用原型链实现对原型属性和方法的继承，通过借用构造函数来实现对实例属性的继承，这样,既通过在原型上定义方法实现了函数复用,又能保证每个实例都有它自己的属性

```js
function Father(name){
	this.name = name;
	this.colors = ["red","blue","green"];
}
Father.prototype.sayName = function(){
	alert(this.name);
};
function Son(name,age){
	Father.call(this,name);//继承实例属性，第一次调用Father()
	this.age = age;
}
Son.prototype = new Father();//继承父类方法,第二次调用Father()
Son.prototype.sayAge = function(){
	alert(this.age);
}

```

`Father.call(this, name)`借用构造函数继承了Father类中的属性，如上一小节所说，这样继承的属性在Son类的不同实例中是独立的；`Son.prototype = new Father()`修改了Son类的原型，这样Son类的实例就能够通过原型链找到Father类中定义的方法

##### 原型继承

在object()函数内部, 先创建一个临时性的构造函数, 然后将传入的对象作为这个构造函数的原型,最后返回了这个临时类型的一个新实例

```js
function object(o){
	function F(){}	// 创建一个临时性的构造函数
	F.prototype = o;	// 将传入的对象作为这个构造函数的原型
	return new F();	// 返回这个临时类型的一个新实例
}

```

实际上创建的新对象是传入的原对象o的一个浅复制，因为新的对象的原型被设置为了原对象o，所以新对象也就拥有了原对象的引用类型属性，即新对象与原对象的引用类型属性是共享的

前面提到的Object.create()就是原型继承的规范版本：

> **object.create()** 接收两个参数:
>
> - 一个用作新对象原型的对象
> - (可选的)一个为新对象定义额外属性的对象

只有一个参数时，就是上面实现的object方法，如果接受第二个参数，它会将原来的同名属性覆盖：

```js
var person = {
	name : "Van"
};
var anotherPerson = Object.create(person, {
	name : {
		value : "Louis"
	}
});
alert(anotherPerson.name);//"Louis"

```

##### 寄生式继承

类似工厂模式，在原型继承的基础上，对新创建的对象增加了一些功能，然后再返回

```js
function createAnother(original){
	var clone = object(original);//通过调用object函数创建一个新对象
	clone.sayHi = function(){//以某种方式来增强这个对象
		alert("hi");
	};
	return clone;//返回这个对象
}

```

##### 寄生组合式继承*(不大明白)

为了减小组合继承调用两次父类构造函数的开销提出的

```js
function extend(subClass,superClass){
	var prototype = object(superClass.prototype);//创建对象
	prototype.constructor = subClass;//增强对象
	subClass.prototype = prototype;//指定对象
}

```

<u>*首先通过原型继承创建一个父类的实例，然后通过寄生式继承，为这个实例增加子类的构造函数，最后为子类指定原型为刚刚创建的父类的实例 ？*</u>

##### new运算符

new运算符实际上做的工作是：

```js
var obj  = {};	// 创建了一个空对象obj
obj.__proto__ = F.prototype;	// 将这个空对象的__proto__成员指向了F函数对象prototype成员对象
F.call(obj);	// 将F函数对象的this指针替换成obj，然后再调用F函数

```

> new 操作符调用构造函数的时候，函数内部实际上发生以下变化：
>
> 1、创建一个空对象，并且 this 变量引用该对象，同时还继承了该函数的原型。
>
> 2、属性和方法被加入到 this 引用的对象中。
>
> 3、新创建的对象由 this 所引用，并且最后隐式的返回 this.

##### 属性查找

（1）当查找某个对象的属性时，JavaScript会沿着原型链一直寻找，直到Object.prototype，如果没有找到的话则会返回undefined

（2）如果想避免原型链查找，可以通过`hasOwnProperty`方法，它不会遍历原型链，只会在对象内部查找

（3）**isPrototypeOf**功能与instanceof相反，它是用来判断该方法所属的对象是不是参数的原型对象，是则返回true，否则返回false

（4）`this instanceof arguments.callee` 的值如果为 true 表示是作为构造函数被调用的,如果为 false 则表示是作为普通函数被调用

```js
function f(){
  if(this instanceof arguments.callee)
    console.log('此处作为构造函数被调用');
  else
    console.log('此处作为普通函数被调用');
}
f();//此处作为普通函数被调用
new f();//此处作为构造函数被调用

```

（5）**typeof** 则用以获取一个变量或者表达式的类型，如number，boolean，string，function（函数），object（NULL，数组，对象），undefined

##### 一般的继承实现方式

```js
function Person(first, last, age, gender, interests) {
        this.name = {
            first,
            last
        };
        this.age = age;
        this.gender = gender;
        this.interests = interests;
    }

    Person.prototype.greeting = function () {
        alert('Hi! I\'m ' + this.name.first + '.');
    };

    function Teacher(first, last, age, gender, interests, subject) {
        Person.call(this, first, last, age, gender, interests);

        this.subject = subject;
    }

    Teacher.prototype = Object.create(Person.prototype);

	// 到这里的时候，Teacher.prototype.contructor === Person，所以下面这一步很有必要，否则就就破坏继承结构

    Teacher.prototype.constructor = Teacher;

```

> 每一个函数对象（`Function`）都有一个`prototype`属性，并且*只有*函数对象有`prototype`属性，因为`prototype`本身就是定义在`Function`对象下的属性。当我们输入类似`var person1=new Person(...)`来构造对象时，JavaScript实际上参考的是`Person.prototype`指向的对象来生成`person1`。另一方面，`Person()`函数是`Person.prototype`的构造函数，也就是说`Person===Person.prototype.constructor`（不信的话可以试试）。
>
> 在定义新的构造函数`Teacher`时，我们通过`function.call`来调用父类的构造函数，但是这样无法自动指定`Teacher.prototype`的值，这样`Teacher.prototype`就只能包含在构造函数里构造的属性，而没有方法。因此我们利用`Object.create()`方法将`Person.prototype`作为`Teacher.prototype`的原型对象，并改变其构造器指向，使之与`Teacher`关联。
>
> *任何*您想要被继承的方法都应该定义在构造函数的`prototype`对象里，并且*永远*使用父类的`prototype`来创造子类的`prototype`，这样才不会打乱类继承结构。

##### 三种对象成员

> 1. 那些定义在构造器函数中的、用于给予对象实例的。这些都很容易发现 - 在您自己的代码中，它们是构造函数中使用`this.x = x`类型的行；在内置的浏览器代码中，它们是可用于对象实例的成员（通常通过使用`new`关键字调用构造函数来创建，例如`var myInstance = new myConstructor()`）。
> 2. 那些直接在构造函数上定义、仅在构造函数上可用的。这些通常仅在内置的浏览器对象中可用，并通过被直接链接到构造函数而不是实例来识别。 例如`Object.keys()`。
> 3. 那些在构造函数原型上定义、由所有实例和对象类继承的。这些包括在构造函数的原型属性上定义的任何成员，如`myConstructor.prototype.x()`。

#### Promise

##### Promise用法

> 参考：
>
> （1）<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises>
>
> （2）<http://es6.ruanyifeng.com/#docs/promise>

##### Promise实现原理

> 参考：<https://juejin.im/post/5b83cb5ae51d4538cc3ec354>

#### Fetch API

> 参考：<https://developer.mozilla.org/zh-CN/docs/Web/API/Fetch_API/Using_Fetch>

##### fetch与jQuery.ajax() 不同

（1）当接收到一个代表错误的 HTTP 状态码时，从 `fetch()`返回的 Promise **不会被标记为 reject，** 即使该 HTTP 响应的状态码是 404 或 500。相反，它会将 Promise 状态标记为 resolve （但是会将 resolve 的返回值的 `ok` 属性设置为 false ），仅当网络故障时或请求被阻止时，才会标记为 reject

（2）默认情况下，`fetch` **不会从服务端发送或接收任何 cookies**, 如果站点依赖于用户 session，则会导致未经认证的请求（要发送 cookies，必须设置 [credentials](https://developer.mozilla.org/zh-CN/docs/Web/API/GlobalFetch/fetch#参数) 选项）

##### 进行fetch请求

（1）fetch的返回值是一个包含响应结果的Promise（一个Response对象，并非JSON）；为了获取JSON内容，需要使用json()方法

（2）fetch的第一个参数是请求的url，第二个参数是init，它的内容可以如下

```js
fetch(url, {
    body: JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
        'user-agent': 'Mozilla/4.0 MDN Example',
        'content-type': 'application/json'
    },
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
})
.then(response => response.json())
.then(data => console.log(data)) // JSON from `response.json()` call
.catch(error => console.error(error))

```

- 通过body设置请求实体，如果要传输json实体，则要在headers里面设置`'content-type': 'application/json'`，而且请求方式要是`method: 'POST'`
- fetch返回的Promise对象通过`then(response => response.json())`转成json之后，便于后续的处理

如果要上传文件，可以通过FormData()来实现

```js
var formData = new FormData();
var fileField = document.querySelector("input[type='file']");

formData.append('username', 'abc123');
formData.append('avatar', fileField.files[0]);

fetch('https://example.com/profile/avatar', {
  method: 'PUT',
  body: formData
})
.then(response => response.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));

```

这里的body不需要转成json；FormData对象实际上就是一个键值对列表

多文件上传可以使用`<input type="file" mutiple/>`来配合FormData

```js
var formData = new FormData();
var photos = document.querySelector("input[type='file'][multiple]");

formData.append('title', 'My Vegas Vacation');
formData.append('photos', photos.files);

fetch('https://example.com/posts', {
  method: 'POST',
  body: formData
})
.then(response => response.json())
.then(response => console.log('Success:', JSON.stringify(response)))
.catch(error => console.error('Error:', error));

```

##### 检测请求是否成功

由于返回resolved的Promise不一定是请求成功的，因此首先要判断Response.ok，再决定接下来的处理

```js
fetch('flowers.jpg').then(function(response) {
  if(response.ok) {
    // 如果response.ok是false的，实际上是取不到response.blob()的
    return response.blob();
  }
  throw new Error('Network response was not ok.');
}).then(function(myBlob) { 
  var objectURL = URL.createObjectURL(myBlob); 
  myImage.src = objectURL; 
}).catch(function(error) {
  console.log('There has been a problem with your fetch operation: ', error.message);
});

```

##### 自定义请求对象

fetch还可以直接将第一、第二个参数封装成Request对象后再传递进去

```js
// 请求头对象
var myHeaders = new Headers();

var myInit = { method: 'GET',
               headers: myHeaders,
               mode: 'cors',
               cache: 'default' };

var myRequest = new Request('flowers.jpg', myInit);

fetch(myRequest).then(function(response) {
  return response.blob();
}).then(function(myBlob) {
  var objectURL = URL.createObjectURL(myBlob);
  myImage.src = objectURL;
});

```

Request()和fetch()接受的参数是一样的；由于Request()和Resonse Body被设计成了**流的形式**，不能被两次请求和读取，但可以通过拷贝再次使用request和response

```js
var anotherRequest = new Request(myRequest,myInit);

```

最佳实践是在使用响应的Promise之前检查 content type 是否正确

```js
fetch(myRequest).then(function(response) {
  // 判断响应头的contentType是不是json，是的话再将其转为json进行后续处理
  if(response.headers.get("content-type") === "application/json") {
    return response.json().then(function(json) {
      // process your JSON further
    });
  } else {
    console.log("Oops, we haven't got JSON!");
  }
});

```

##### Response对象

Response是fetch()处理完Promise后返回的，常见的属性有：

- **Response.status** — 整数(默认值为200) 为response的状态码
- **Response.statusText** — 字符串(默认值为"OK"),该值与HTTP状态码消息对应
- **Response.ok** — 该属性是来检查response的状态是否在200-299(包括200,299)这个范围内.该属性返回一个布尔值

##### Body实体

请求和响应都可以包含Body，常见的类型有：ArrayBuffer、ArrayBufferView、Blob/File、String、URLSearchParams和FormData

可以通过Body类的**arrayBuffer()、blob()、json()、text()和formData()**方法来获取请求和响应的实体数据

当我们设置了Body实体但是没有在headers中设置contentType时，request和response会进行自动设置

##### Polyfill

在不支持Fetch的浏览器中可以使用Fetch Polyfill



#### 浏览器事件循环机制

##### 事件循环机制

> 参考：<https://juejin.im/post/5afbc62151882542af04112d>

事件循环可以简单描述为：

（1）函数入栈，当Stack中执行到异步任务的时候，就将他丢给WebAPIs,接着执行同步任务,直到Stack为空;

（2）在此期间WebAPIs完成这个事件，把回调函数放入CallbackQueue中等待;

（3）当执行栈为空时，Event Loop把Callback Queue中的一个任务放入Stack中,回到第1步。

其中第（2）步是由浏览器的线程去完成，不关js引擎的事情

![](https://user-gold-cdn.xitu.io/2018/5/16/163677dd81721ac8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

> - Event Loop是由javascript宿主环境（像浏览器）来实现的;
> - WebAPIs是由C++实现的浏览器创建的线程，处理诸如DOM事件、http请求、定时器等异步事件;
> - JavaScript 的并发模型基于"事件循环";
> - Callback Queue(Event Queue 或者 Message Queue) 任务队列,存放异步任务的回调函数

例子：

```js
var start=new Date();
setTimeout(function cb(){
    console.log("时间间隔：",new Date()-start+'ms');
},500);
while(new Date()-start<1000){};

```

执行过程：

1. main(Script) 函数入栈,start变量开始初始化
2. setTimeout入栈,出栈,丢给WebAPIs,开始定时500ms;
3. while循环入栈,开始阻塞1000ms;
4. 500ms过后,WebAPIs把cb()放入任务队列,此时while循环还在栈中,cb()等待;
5. 又过了500ms,while循环执行完毕从栈中弹出,main()弹出,此时栈为空,Event Loop,cb()进入栈,log()进栈,输出'时间间隔：1003ms',出栈,cb()出栈

##### 宏任务和微任务

（1）**宏任务macro-task(Task)**：一个event loop有一个或者多个task队列，task任务源有：

- **setTimeout**
- setInterval
- setImmediate
- I/O
- UI rendering

（2）**微任务micro-task(Job)**：一个 event loop里只有一个microtask 队列，任务源有：

- process.nextTick
- **promises**
- Object.observe
- MutationObserver

（3）例子：

```js
Promise.resolve().then(function promise1 () {
       console.log('promise1');
    })
setTimeout(function setTimeout1 (){
    console.log('setTimeout1')
    Promise.resolve().then(function  promise2 () {
       console.log('promise2');
    })
}, 0)

setTimeout(function setTimeout2 (){
   console.log('setTimeout2')
}, 0)

```

执行过程（脚本代码本身是一个task，会被列入task队列）：

**循环1：**

【task队列：script ；microtask队列：】

1、从task队列中取出script任务，推入栈中执行。

2、promise1列为microtask，setTimeout1列为task，setTimeout2列为task。

【task队列：setTimeout1 setTimeout2；microtask队列：promise1】

3、script任务执行完毕，执行microtask checkpoint，取出microtask队列的promise1执行。

**循环2：**

【task队列：setTimeout1 setTimeout2；microtask队列：】 

4、从task队列中取出setTimeout1，推入栈中执行，将promise2列为microtask。

- 【task队列：setTimeout2；microtask队列：promise2】

5、执行microtask checkpoint，取出microtask队列的promise2执行。

**循环3：**

- 【task队列：setTimeout2；microtask队列：】

6、从task队列中取出setTimeout2，推入栈中执行。

7.setTimeout2任务执行完毕，执行microtask checkpoint。

- 【task队列：；microtask队列：】

*8、Update the rendering（更新渲染）

<u>*最关键的不同：**每次event loop循环执行栈完成后，会继续执行完相应的microtask任务**，最后每轮事件执行的最后，浏览器会去决定是否更新视图渲染（Update the rendering，也就是说可能不是每轮事件循环都去更新视图，只在有必要的时候才更新视图）*</u>

#### async/await

> 参考：<https://www.jianshu.com/p/ffa5cbe9ab29>

#### JavaScript闭包（Closure）

> 参考：<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Closures>

（1）JavaScript中的函数会形成*闭包*。 闭包是由函数以及创建该函数的词法环境组合而成。**<u>这个环境包含了这个闭包创建时所能访问的所有局部变量</u>**

（2）在循环中创建闭包的常见错误：

```js
<p id="help">Helpful notes will appear here</p>
<p>E-mail: <input type="text" id="email" name="email"></p>
<p>Name: <input type="text" id="name" name="name"></p>
<p>Age: <input type="text" id="age" name="age"></p>

function showHelp(help) {
  document.getElementById('help').innerHTML = help;
}

function setupHelp() {
  var helpText = [
      {'id': 'email', 'help': 'Your e-mail address'},
      {'id': 'name', 'help': 'Your full name'},
      {'id': 'age', 'help': 'Your age (you must be over 16)'}
    ];

  for (var i = 0; i < helpText.length; i++) {
    var item = helpText[i];
    document.getElementById(item.id).onfocus = function() {
      showHelp(item.help);
    }
  }
}

setupHelp();
```

- 无论焦点在哪个`input`上，显示的都是关于年龄的信息，原因是赋值给onfocus的闭包是由他们的**函数定义**以及**setupHelp作用域捕获的环境**组成的，三个闭包在循环中被创建，但他们共享了同一个词法作用域，在这个作用域中存在一个变量item。当onfocus的回调执行时，`item.help`的值被决定。由于循环在事件触发之前早已执行完毕，变量对象`item`（被三个闭包所共享）已经指向了`helpText`的最后一项；
- 简单来说就是，三个闭包是共享词法作用域，而不是各自拥有不同的词法作用域，在setupHelp执行完毕时，他们共享的item已经指向了`helpText`最后一项

（3）解决方法

- 使用更多的闭包

  ```js
  ...
  
  function makeHelpCallback(help) {
    return function() {
      showHelp(help);
    };
  }
  
  function setupHelp() {
    ...
  
    for (var i = 0; i < helpText.length; i++) {
      var item = helpText[i];
      document.getElementById(item.id).onfocus = makeHelpCallback(item.help);
    }
  }
  
  ...
  ```

  这样赋值给onfocus的三个闭包不再共享词法作用域，而是各自拥有`makeHelpCallback(help)`中的不同词法作用域

- 使用匿名闭包

  ```js
  ...
  
  for (var i = 0; i < helpText.length; i++) {
      (function() {
         var item = helpText[i];
         document.getElementById(item.id).onfocus = function() {
           showHelp(item.help);
         }
      })(); // 马上把当前循环项的item与事件回调相关联起来
    }
  
  ...
  
  ```

  这样每次循环中赋值给onfocus的闭包拥有的此法环境就限制在了匿名函数内部

- 使用let而不是var关键字

  ```js
  ...
  
  for (var i = 0; i < helpText.length; i++) {
      let item = helpText[i];
      document.getElementById(item.id).onfocus = function() {
      	showHelp(item.help);
      }
  }
  
  ...
  
  ```

  *关于let关键字，查看 1.5.12 节*

（4）使用闭包的注意点

- **滥用闭包，会造成内存泄漏**：由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
- **会改变父函数内部变量的值**：如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

#### this、 call、bind和apply

> 参考：<https://juejin.im/post/59bfe84351882531b730bac2>

##### 关于this

this 的指向并不是在创建的时候就可以确定的，在 es5 中，**this永远指向最后调用它的那个对象**（例子见参考链接）

##### 改变this的指向

- 可以通过call、bind和apply改变this的指向
- 使用箭头函数
- 在函数内部使用`_this = this`临时保存定义函数的对象的this调用

##### 箭头函数

**箭头函数的 this 始终指向函数定义时的 this，而非执行时。**使用箭头函数需要记着这句话：“箭头函数中没有 this 绑定，必须通过查找作用域链来决定其值，如果箭头函数被非箭头函数包含，则 **this 绑定的是最近一层非箭头函数的 this**，否则，this 为 undefined”

##### apply、call、bind 区别

- apply() 方法调用一个函数, 其具有一个指定的this值，以及作为一个数组（或类似数组的对象）提供的参数

  ```js
  fun.apply(thisArg, [argsArray])
  
  ```

  - thisArg：在 fun 函数运行时指定的 this 值。需要注意的是，指定的 this 值并不一定是该函数执行时真正的 this 值，如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动指向全局对象（浏览器中就是window对象），同时值为原始值（数字，字符串，布尔值）的 this 会指向该原始值的自动包装对象
  - argsArray：一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 fun 函数。如果该参数的值为null 或 undefined，则表示不需要传入任何参数。

- apply 和 call 的区别是 call 方法接受的是若干个参数列表，而 apply 接收的是一个包含多个参数的数组

  ```js
  fun.call(thisArg[, arg1[, arg2[, ...]]])
  
  ```

- bind()方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列；即**bind 是创建一个新的函数，我们必须要手动去调用**

  ```js
  var a ={
      name : "Cherry",
      fn : function (a,b) {
      	console.log( a + b)
      }
  }
  
  var b = a.fn;
  b.bind(a,1,2)()  
  
  ```

##### 再补充一点

```js
// 1
var a = {
    name:'steve',
    b : function(){
    	setTimeout(()=>console.log(this.name),100)
    }
}
a.b()	//steve

// 2
var a = {
    name:'steve',
    b : function(){
        setTimeout(function(){console.log(this.name)},100)
    }
}
a.b()	//windowsName

// 3
var a = {
    name:'steve',
    b : function(){
        console.log(this.name)
    }
}
a.b()	//steve

```

- （1）中使用的是箭头函数，由于函数闭包的作用，箭头函数里面的this.name是定义时的a对象的name属性，因此是steve，即使这个箭头函数是延迟执行的
- 如果没有使用箭头函数，由于setTimeout里面回调函数延迟执行的缘故，this指向的实际上是window对象，因此打印了windowsName
- 如果没有延迟执行，那么this.name指向的是最后调用的对象，在（3）中是使用a.b()来调用的，因此最后调用的对象是a，所以结果是steve

#### 函数调用

函数调用在js中一共有四种，如下

##### 作为一个函数调用

```js
var name = "windowsName";
function a() {
    var name = "Cherry";
    console.log(this.name);          // windowsName
    console.log("inner:" + this);    // inner: Window
}
a();
console.log("outer:" + this)         // outer: Window

```

这样定义的函数，不属于任何一个对象，就是一个函数，这样的情况在 JavaScript 的在浏览器中的非严格模式默认是属于全局对象 window 的，在严格模式，就是 undefined

##### 函数作为（对象的）方法调用

> 在js中，函数是一个对象，它的内部有自己的属性和方法

```js
var name = "windowsName";
var a = {
    name: "Cherry",
    fn : function () {
        console.log(this.name);      // Cherry
    }
}
a.fn();

```

在这里，fn最后调用的对象是a，所以this.name指向的是a的name属性

***note**：

```js
var name = "windowsName";
var a = {
    name : "Cherry",

    func1: function () {
        console.log(this.name)     
    },

    func2: function () {
        setTimeout(  function () {
            this.func1()
        },100);
    }
};
a.func2()     // this.func1 is not a function

```

这个例子中，由于func2中调用了setTimeout方法，这个方法里面的回调函数不是立即执行的，因此当回调函数执行的时候，它的this指向已经不是对象a了，而是window对象，因此this.func1()函数不存在

##### （作为构造函数调用

```js
// 构造函数:
function myFunction(arg1, arg2) {
    this.firstName = arg1;
    this.lastName  = arg2;
}

// This    creates a new object
var a = new myFunction("Li","Cherry");
a.lastName;   

```

作为构造函数调用后，创建了对象，自然里面的this也是指向创建的对象的

##### 使用apply、call和bind调用

*这个就不多解释了*

#### Currying柯里化

> 参考：<https://juejin.im/post/5af13664f265da0ba266efcf>

（1）Currying的作用之一是 可以实现参数的复用，例如有一个多参数函数，当传递了第一个参数之后，得到一个柯里化后的函数用来接受剩下的参数，这样这个函数在调用时，第一个参数就已经是固定的，即被复用

（2）基于Currying原理的一个函数库lodash，参考：<https://www.jianshu.com/p/d46abfa4ddc9>

#### let、var和const

> 参考：<https://www.jianshu.com/p/4e9cd99ecbf5>

（1）ES6才添加的let和const

（2）js中没有块级作用域，因此**在JS函数中的var声明，其作用域是函数体的全部**

```js
for(var i=0;i<10;i++){
	var a = 'a';
}
console.log(a);

```

（3）在循环中容易出现变量过度共享

```js
for (var i = 0; i < 3; i++) {
    setTimeout(function () {
    	console.log(i)
    }, 1000);
}

```

原因是setTimeout中的回调函数是延迟调用的，可以肯定的是必然晚于循环的执行完毕，而当循环调用完毕时，var i其实已经是3了，因此打印的是3 3 3而不是0 1 2

（4）将var换成let可以解决以上两个bug（**note：必须声明 'use strict' 后才能使用let声明变量，否则浏览器并不能显示结果**）

- let声明的变量拥有块级作用域

- let声明的全局变量不是全局对象的属性

- **形如for (let x...)的循环在每次迭代时都为x创建新的绑定。**

  如果一个for (let...)循环执行多次并且循环保持了一个闭包，那么**每个闭包将捕捉一个循环变量的不同值作为副本**，而不是所有闭包都捕捉循环变量的同一个值

- 用let重定义变量会抛出一个语法错误（SyntaxError）

  ```js
  let a = 'a';
  let a = 'b';	// 报错
  
  ```

（5）let和var都支持**解构**赋值

（6）const 就是用来定义常量的：**只声明不赋值**、**重复声明常量**和**修改常量的值**都是不允许的

（7）如果在函数体内部声明变量时**没有使用var、const或者let，那么声明的变量实际上是一个全局变量**，函数体外部也能访问

### CSS

#### flex布局

> 参考：<https://juejin.im/post/58e3a5a0a0bb9f0069fc16bb>

![](https://lc-gold-cdn.xitu.io/0dd26d8e99257ff36443.png?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

1.6.2 







## 数据结构与算法

### 数据结构

#### 队列

（1）实现循环队列：

```java
package tk.chph.demo.ds_alg;

class MyCircularQueue {
    private int[] data;
    private int front, end;

    /**
     * Initialize your data structure here. Set the size of the queue to be k.
     */
    public MyCircularQueue(int k) {
        this.data = new int[k + 1];
        front = 0;
        end = 0;
    }

    /**
     * Insert an element into the circular queue. Return true if the operation is successful.
     */
    public boolean enQueue(int value) {
        if (isFull()) return false;
        data[end++] = value;
        end = end % data.length;
        return true;
    }

    /**
     * Delete an element from the circular queue. Return true if the operation is successful.
     */
    public boolean deQueue() {
        if (isEmpty()) return false;
        front = (++front) % data.length;
        return true;
    }

    /**
     * Get the front item from the queue.
     */
    public int Front() {
        if (isEmpty()) return -1;
        return data[front];
    }

    /**
     * Get the last item from the queue.
     */
    public int Rear() {
        if (isEmpty()) return -1;
        return data[(end - 1+data.length)%data.length];
    }

    /**
     * Checks whether the circular queue is empty or not.
     */
    public boolean isEmpty() {
        return front == end;
    }

    /**
     * Checks whether the circular queue is full or not.
     */
    public boolean isFull() {
        return (end - (front - data.length) +1) % (data.length) == 0;
    }
}

```

这个实现方法：以长度为k的数组实现，data数组要长度为k+1，原因是要使得`front==end`能够作为isEmpty的条件，isFull判断条件是：`(end - (front - data.length) +1) % (data.length) == 0`，enQueue和deQueue时要注意end和front越界，同时由于end指向的元素是空的，因此Rear时要注意end-1可能会出现<1的情况

（2）**添加顺序**和**处理顺序**相同时，可以考虑使用队列来解决



## SSM+Redis框架

### Java动态代理技术和设计模式

#### JDK动态代理

（1）建立动态代理的步骤：

- 代理对象和真实对象建立代理关系
- 实现代理对象的逻辑方法

（2）在 JDK 动态代理中，要实现**代理逻辑类**必须去实现 `java.lang.reflect.InvocationHandler
`接口，它里面定义了 一个 invoke 方法，并提供接口数组用于下挂代理对象

```java
interface HelloWorld {
    public void sayHelloWorld();
}

class HelloWorldImpl implements HelloWorld {
    public void sayHelloWorld() {
        System.out.println("Hello World!");
    }
}

class JdkProxyExample implements InvocationHandler {
    // 真实对象
    private Object target = null;

    // 建立代理对象和真是对象的关系，并返回代理对象
    Object bind(Object target) {
        this.target = target;
        return Proxy.newProxyInstance(target.getClass().getClassLoader(),
                target.getClass().getInterfaces(), this);
    }

    // 代理方法的逻辑
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("进入代理逻辑方法");
        System.out.println("调度真实对象前的服务");
        Object obj = method.invoke(target, args);
        System.out.println("调度真实对象后的服务");
        return obj;
    }
}

public class ProxyTest {
    public static void main(String[] args) {
        JdkProxyExample jdk = new JdkProxyExample();
        // 进行代理，并获得代理对象，这时候外部只能直接访问代理对象，不能直接访问真实对象
        HelloWorld proxy = (HelloWorld) jdk.bind(new HelloWorldImpl());
        proxy.sayHelloWorld();
    }
}

```

测试方法中返回的HelloWorld对象已经是一个代理对象，`proxy.sayHelloWorld();`的调用会进入invoke方法中

（3）JDK动态代理必须提供接口才能使用，比如例子中的HelloWorld接口；`Proxy.newProxyInstance(target.getClass().getClassLoader(),
                target.getClass().getInterfaces(), this);`中的`target.getClass().getInterfaces()`就是获取真是对象实现的接口，并将真实对象挂靠在接口下，`HelloWorld proxy = (HelloWorld) jdk.bind(new HelloWorldImpl());`也得通过接口来引用代理对象

#### CGLIB动态代理

（1） CGLIB 动态代理的优势在于不需要提供接口，只要一个非抽象类就
能实现动态代理 

（2）代码示例

```java
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodProxy;
...

// 真实对象
class HelloWorldImpl implements HelloWorld {
    public void sayHelloWorld() {
        System.out.println("Hello World!");
    }
}

// 代理对象
class Interceptor implements MethodInterceptor {
    public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        System.err.println("调用真实对象前");
        Object result = methodProxy.invokeSuper(proxy, args);
        System.err.println("调用真实对象后");
        return result;
    }
}

// 通过Enhancer建立代理对象与真实对象之间的关系
public class CglibProxyExample {
    public Object getProxy(Class cls){
        Enhancer enhancer = new Enhancer();
        enhancer.setSuperclass(cls);
        enhancer.setCallback(new Interceptor());
        return enhancer.create();
    }

    public static void main(String[] args) {
        CglibProxyExample cglibProxyExample = new CglibProxyExample();
        HelloWorldImpl helloWorld = (HelloWorldImpl) cglibProxyExample.getProxy(HelloWorldImpl.class);
        helloWorld.sayHelloWorld();
    }
}

```

- 代理类要**实现MethodInterceptor接口**，并在invoke方法中实现代理逻辑方法
- 注意，通过反射调用真实对象的方法时是invokeSuper方法，如`Object result = methodProxy.invokeSuper(proxy, args);`，因为Enhancer建立代理对象与真实对象的关系时是将超类设为真实类，setCallback将代理类设为我们定义的Interceptor

#### 拦截器

（1）代码实例

- 设计者提供的

  ```java
  public class InterceptorJdkProxy implements InvocationHandler {
      private Object target;
      private String interceptorClass;
  
      private InterceptorJdkProxy(Object target, String interceptorClass) {
          this.target = target;
          this.interceptorClass = interceptorClass;
      }
  
      public static Object bind(Object target, String interceptorClass) {
          return Proxy.newProxyInstance(target.getClass().getClassLoader(),
                  target.getClass().getInterfaces(), new InterceptorJdkProxy(target, interceptorClass));
      }
  
      @Override
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
          if (interceptorClass == null) {
              return method.invoke(target, args);
          }
          Object result = null;
          Interceptor interceptor = (Interceptor) Class.forName(interceptorClass).newInstance();
          // 调用前置方法
          if (interceptor.before(proxy, target, method, args)) {
              // 前置方法返回true，反射原对象方法
              result = method.invoke(target, args);
          } else {
              // 否则执行around方法
              interceptor.around(proxy, target, method, args);
          }
          // 调用后置方法
          interceptor.after(proxy, target, method, args);
          return result;
      }
  }
  
  ```

  ```java
  public interface Interceptor {
      boolean before(Object proxy, Object target, Method method, Object[] args);
      void after(Object proxy, Object target, Method method, Object[] args);
      void around(Object proxy, Object target, Method method, Object[] args);
  }
  
  ```

- 开发者提供的

  ```java
  public class MyInterceptor implements Interceptor {
      @Override
      public boolean before(Object proxy, Object target, Method method, Object[] args) {
          System.out.println("反射方法前逻辑");
          return false;
      }
  
      @Override
      public void after(Object proxy, Object target, Method method, Object[] args) {
          System.out.println("反射方法后逻辑");
      }
  
      @Override
      public void around(Object proxy, Object target, Method method, Object[] args) {
          System.out.println("取代了被代理对象的方法");
      }
  }
  
  ```

  ```java
  public class InterceptorTest {
      public static void main(String[] args) {
          HelloWorld proxy = (HelloWorld) InterceptorJdkProxy.bind(new HelloWorldImpl(), "tk.chph.demo.proxy.interceptor.MyInterceptor");
          proxy.sayHelloWorld();
      }
  }
  
  ```

这里使用的是JDK动态代理，对开发者来说，只需要实现设计者提供的拦截器接口Interceptor，然后通过bind方法将拦截器（代理对象）和自己的业务对象（真实对象）建立代理关系就可以了；而设计动态代理的代码全部由设计者来实现，开发者无需关心

另外拦截器实例化是被延迟到了代理方法（invoke）调用的时候才进行的，在此之前，代理对象只是保存了拦截器的全限定名

#### 责任链模式

（1）说白了就是：当一个对象在一条链上被多个拦截器拦截处理（拦截器也可以选择不拦截处理它）时，
我们把这样的设计模式称为责任链模式

（2）责任链模式和拦截器的一点不同在于，要注意责任链模式上每个拦截器的before、after方法的执行顺序；责任链模式实质上是对代理对象进行代理，这样层层叠加：

> proxy3代理proxy2，proxy2代理proxy1，proxy1代理真实对象

这样在调用的时候，对proxy3来说，proxy2是它的真实对象，因此执行顺序是：proxy3.before->proxy2->proxy3.after，而proxy2的真实对象是proxy1，因此执行顺序变成了：proxy3.before->proxy2.before->proxy1->proxy2.after->proxy3.after，以此类推

#### 观察者模式（发布订阅模式）

（1）观察者模式定义了 一种**一对多**
的依赖关系，让多个观察者对象同时监视着被观察者的状态，当被观察者的状态发生变化
时 ， 会通知所有观察者 ， 并让其自动更新自己 

（2）代码实例

- 被观察者，要继承Observable类

  ```java
  public class ProductList extends Observable {
      private List<String> productList=null;
      private static ProductList instance;
      private ProductList(){}
  
      public static ProductList getInstance(){
          if (instance==null){
              instance = new ProductList();
              instance.productList = new ArrayList<>();
          }
          return instance;
      }
  
      public void addProduct(String newProduct){
          productList.add(newProduct);
          System.out.println("产品列表新增了产品："+newProduct);
          // =====注意这里=====
          this.setChanged();
          this.notifyObservers(newProduct);
      }
  }
  
  ```

  这里要注意的是，被观察者状态改变后，要调用setChanged将changed变量设为true，否则后面的notifyObservers方法没有效果；同时，notifyObservers方法调用完成后，changed为会被重新设置为false，等待下一次状态更新

- 观察者，要实现Observer接口及其接口里的update方法，因为被观察者调用notifyObservers方法，实际上是调用所有观察者的update方法

  ```java
  class JingDongObserver implements Observer {
      @Override
      public void update(Observable o, Object product) {
          String newProduct = (String) product;
          System.err.println("发送新产品【"+newProduct+"】到京东商城");
      }
  }
  
  class TaoBaoObserver implements Observer {
      @Override
      public void update(Observable o, Object product) {
          String newProduct = (String) product;
          System.err.println("发送新产品【"+newProduct+"】到淘宝商城");
      }
  }
  
  ```

- 测试类

  ```java
  public class ObserveTest {
      public static void main(String[] args) {
          ProductList productList = ProductList.getInstance();
          productList.addObserver(new TaoBaoObserver());
          productList.addObserver(new JingDongObserver());
          productList.addProduct("MacBook Pro");
      }
  }
  
  ```

  

#### Builder模式

（1）很多时候由于对象的创建比较复杂，这时候就可以使用Builder模式，通过提供配置类来帮助创建复杂对象

（2）Builder 模式是一种**分步构建对象**的模式 



### Mybatis框架

#### Mybatis的核心组件

（1）mybatis核心组件由四部分组成

- **SqlSessionFactoryBuilder（构造器）**：通过配置或者代码来生成SqlSessionFacotry对象，采用分步构建的**Builder模式**
- **SqlSessionFactory（工厂接口）**：依靠它来生成SqlSession，使用**工厂模式**
- **SqlSession（会话）**： 一个既可以发送 SQL 执行返回结果，也可以获取 Mapper 的接口。在现有的技术中， 一般我们会让其在业务逻辑代码中“消失”，而使用的是
  MyBatis 提供的 SQLMapper 接口编程技术，它能提高代码的可读性和可维护性 
- **SQL Mapper（映射器）**：MyBatis 新设计存在的组件，它由一个 **Java 接口和 XML**
  **文件（或注解）**构成，需要给出对应的 SQL 和映射规则。它负责发送 SQL 去执行，
  并返回结果 

*Note：无论是映射器还是 SqlSession 都可以发送 SQL 到数据库执行

（2）SqlSessionFactory （工厂接口）

- Mybatis提供构造器SqlSessionFactoryBuilder以及配置类org.apache.ibatis.session.Configuration来生产SqlSessionFactory对象

- org.apache.ibatis.session.Configuration对象既可以通过读取XML文件来生成，也可以直接通过Java代码来定义，它是整个mybatis的上下文

- SqlSessionFactory是一个接口，它的实现类有SqlSessionManager和DefaultSqlSessionFactory；一般而言都是使用DefaultSqlSessionFactory，而SqlSessionManager用在多线程里，它的实现依靠DefaultSqlSessionFactory

- SqlSessionFactory唯一的作用是生产Mybatis的核心接口对象SqlSession，因此它一般来说是单例模式

- 使用XML文件描述SqlSessionFactory的配置文件就是mybatis-config.xml，然后就可以创建SqlSessionFactory对象了

  ```java
  InputStream inputStream = Resource.getResourceAsStream("mybatis-config.xml");
  SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
  
  ```

- 采用XML配置的形式好处是：信息在配置文件中，便于后期维护和修改；同时不需要重新编译代码

（3）SqlSession

- 有两个实现类：DefaultSqlSession（用于单线程）和SqlSessionManager（用于多线程）

- SqlSession类似一个JDBC中的一个Connection，管理者一个连接中的资源启用

  - 获取Mapper接口

    ```java
    RoleMapper roleMapper = sqlSession.getMapper(RoleMapper.class);
    
    ```

  - 发送SQL给数据库

  - 控制数据库事务（提交和回滚）

- 使用SqlsessionFactory创建SqlSession的方法

  ```java
  SqlSession sqlSession = sqlSessionFactory.openSession();
  
  ```

- SqlSession只是一个门面接口，底层真正在工作的是Executor

（4）映射器

- 它由一个接口和对应的XML（或者注解）组成
- 映射器的主要作用就是将 SQL 查询到的**结果映射为一个 POJO**，或者**将 POJO 的数据**
  **插入到数据库**中 ， 并定义一些关于缓存等的重要内容
- Mybatis使用动态代理技术，根据XML或者注解的描述为接口创建一个代理对象去处理相关的逻辑
- 同时使用注解和XML创建映射器时，XML的方式会覆盖注解的定义

（5）发送SQL语句到数据库

- SqlSession方式

  ```java
  Role role = (Role) sqlSession.selectOne("com.learn.ssm.chapter3.mapper.RoleMapper.getRole", 11);
  
  ```

- Mapper接口方式

  ```java
  RoleMapper roleMapper = sqlSession.getMapper(RoleMapper.class);
  Role role = roleMapper.getRole(1L);
  
  ```

（6）生命周期

- SqlSessionFactoryBuilder：它的作用是创建SqlSessionFactory，创建完后就失去了作用，可以被JVM回收了
- SqlSessionFactory：作用相当于一个数据库连接池，一旦创建了 SqlSessionFactory ， 就要长期保存它，直至
  不再使用 MyBatis 应用，所以可以认为 SqlSessionFactory 的生命周期就等同于 MyBatis 的
  应用周期；同时一般要求SqlSessionFactory是一个单例，在应用中被共享
- SqlSession：SqlSession 就相当于一个数据库
  连接（ Connection 对象），它应该存活在一个业务请求中，处理完整个请求
  后，应该关闭这条连接，让它归还给 SqlSessionFactory ， 否则数据库资源就很快被耗费精光，系统就会瘫痪
- Mapper：它由 SqlSession 所创建，所以它的最大生命周期至多和 SqlSession
  保持一致；但由于SqlSession关闭后，它的数据库连接资源也会消失，
  所以它的生命周期应该小于等于 SqlSession 的生命周期 ；Mapper 代表的是一个请求中业务处理的一个步骤，所以它应该在一个请求中，一旦处理完了相关的业务，就应该随着SqlSession的关闭而废弃它

#### MyBatis 的解析和运行原理

#### Spring-Mybatis整合

（1）配置数据源

- 简单数据源SimpleDriverDataSource，没有提供数据库连接池功能
  - 数据库驱动driver
  - 数据库连接url
  - 用户名username
  - 密码password
- 第三方数据库连接池数据源DBCP（dbcp.BasicDataSource），提供了数据库连接池功能
  - 数据库驱动driver
  - 数据库连接url
  - 用户名username
  - 密码password
  - 数据库连接池配置：maxActive、maxIdle、maxWait等
- 从web服务器获取数据源JNDI（jndi.JndiObjectFactoryBean）
  - web服务器配置的数据源名称 jndiName

（2）配置SqlSessionFactoryBean

- 数据源datasource
- mybatis配置文件configLocation

（3）配置MapperScannerConfigurer

- 扫描路径basePackages
- SqlSessionFactoryBeanName
- 映射器的注解annotationClass



### Spring框架

#### Spring IoC

（1）控制反转是一种通过描述（在 Java
中可以是 XML 或者注解）并通过第三方去产生或获取特定对象的方式。在 Spring 中实现控制反转的是 IoC 容器，其实现方法是依赖注入 （Dependency Injection, DI ）

（2）Spring IoC容器主要是基于BeanFactory 和 ApplicationContext 两个接口，其中ApplicationContext 是 BeanFactory 的子接口之一，在绝大部分的工作场景下 ， 都会使用 ApplicationContext 作为
Spring IoC 容器 

（3）Spring IoC的初始化包括**Bean的初始化**和**依赖注入**两个步骤

- Bean初始化
  - Resource 定位，这步是 Spring IoC 容器根据开发者的配置，进行资源定位，在 Spring
    的开发中，通过 XML 或者注解都是十分常见的方式
  - BeanDefinition 的载入，这个过程就是 Spring 根据开发者的配置获取对应的 POJO,
    用以生成对应实例的过程
  - BeanDefinition 的注册 ，这个步骤就相当于把之前通过 BeanDefinition 载入的 POJO
    往 Spring IoC 容器中注册，这样就可以使得开发和测试人员都可以通过描述从中得到 Spring
    IoC 容器的 Bean 了
- 依赖注入：在完成依赖注入之前的Bean还不能够使用，对于依赖注入，spring中默认的lazy-init（是否初始化Bean）是false，即 Spring IoC 默认会自动初始化 Bean；如果将其设置为 true，那么只有当我们使用 Spring IoC 容器的 getBean 方法获取它时，它才
  会进行初始化， 完成依赖注入。

（4）Spring Bean的生命周期

- （占个坑orz）

#### Spring Bean的装配

（1）依赖注入的三种方式

- 构造器注入
- setter注入
- <u>*接口注入*</u>？

（2）将Bean装配进Spring IoC的配置方式（以**注解为主，XML为辅**）

- 在XML中显式配置
- 在Java的接口和类中实现配置
- 隐式Bean的发现机制和自动装配原则

（3）三种装配Bean方式的优先级

- 基于约定优于配置的原则，优先选择隐式Bean的发现机制和自动装配原则
- 在没法使用自动配置原则情况下，优先使用在Java的接口和类中实现配置，这样可以避免XML配置泛滥；同时，当一个父类有多个子类时，如果想通过IoC容器初始化父类，容器将无法决定使用哪个子类去初始化，这时候可以通过Java注解的方式去指定一个子类
- 最后选用XML方式，因为有时候使用第三方库时，没法取修改这些类库代码，这时候就可以通过XML方式去配置，将第三方类库的Bean装配到Spring IoC容器中

（4）XML装配Bean

```xml
<beans>
    <bean id="role2" class="com.ssm.chapter9.pojo.Role" >
        <property name="id" value="1"/>
        <property name="roleName" value="高级工程师"/>
        <property name="note" value="重要人员"/>
	</bean>
</beans>

```

- id是spring找到这个Bean的编号，如果没有指定，则spring会自动生成“全限定名#{num}”格式的编号
- class是这个Bean的类的全限定名
- property是定义类的属性，name是属性名，value是属性值，另外属性的值如果是引用类型，可以通过ref=“{source.id}”来引用定义的其他bean

当property是集合时，可以通过如下方式来定义：

```xml
<!-- 列表 -->
<property name="list">
    <list>
    	<value>value-list-1</value>
        <value>value-list-2</value>
        <value>value-list-3</value>
    </list>
</property>

<!-- 映射 -->
<property name="map">
    <map>
    	<entry key="key1" value="value-key-1"/>
        <entry key="key2" value="value-key-2"/>
        <entry key="key3" value="value-key-3"/>
    </map>
</property>

<!-- Property -->
<property name="properties">
    <props>
    	<prop key="prop1">value-prop-1</prop>
        <prop key="prop2">value-prop-2</prop>
        <prop key="prop3">value-prop-3</prop>
    </props>
</property>

<!-- 集合 -->
<property name="set">
    <set>
    	<value>value-set-1</value>
        <value>value-set-2</value>
        <value>value-set-3</value>
    </set>
</property>

<!-- 数组 -->
<property name="array">
    <array>
    	<value>value-array-1</value>
        <value>value-array-2</value>
        <value>value-array-3</value>
    </array>
</property>
```

如果list、map、set和array这些集合的元素是一个类而不是基本数据类型，那么可以将\<value/>替换为\<ref bean="xxx"/>（map中\<entry/>的key替换为key-ref、value替换为value-ref）来引用别处定义好的bean，这样就完成了类集合的Bean定义

（5）Spring中，Spring IoC容器发现Bean的方式有两中：

- 组件扫描：通过定义资源的方式 ， 让 Spring IoC 容器扫描对应 的包 ，从而把 Bean
  装配进来
- 自动装配：通过注解定义，使得一些依赖关系可以通过注解完成

常见的注解如下：

- **@Component**

```java
@Component(value = "role")
public class Role{
	@Value("1")
	private Long id;
	@Value("role_name_1")
	private String roleName;
	@Value("role_note_1")
	private String note;
	
	// setters and getters
}

```

其中@Component表示Spring IoC会将这个类扫描成Bean实例，value后面的值是Bean在Spring中的id编号；如果不知道value，Spring会自动生成编号

@Value代表的是值的注入

- **@ComponentScan**

```java
@ComponentScan
public class PojoConfig{
}

```

只有@Component还不行，还得设置@ComponentScan告诉Spring要扫描的包路径，这里没有设置值，则默认是扫描PojoConfig所在包下的类，所以上面的Role要和PojoConfig放在一起才能被Spring扫描到

@ComponentScan有两个配置项：basePackages，接受一个Java包数组，Spring会根据配置来扫描对于的包和这些包下面的**子包**；basePackagesClasses，它接受一个类的数组，Spring会根据这些类所在的包，为**包和子包**进行扫描装配对应配置的
Bean 

要注意的是：

- 如果定义了多个@ComponentScan，每个@ComponentScan都会对扫描到的Bean生成一个实例，这就**可能出现很多重复的实例**
- 但是对于同一个@ComponentScan里面的basePackages和basePackagesClasses，两者如果有出现扫描重复的Bean，也**只会生成一个Bean实例**

```java
ApplicationContext context =
new AnnotationConfigApplicationContext(PojoConfig.class);
Role role= context.getBean(Role.class);

```

接下来只需要将配置类PojoConfig添加到context中，就能通过context来获取Spring IoC扫描到的Bean实例了

- **自动装配@Autowired**

Spring在生成所有Bean后，如果发现有这个注解，就会**根据类型**在Bean容器中查找对应的Bean，将其注入进来，这就是依赖注入

如果IoC容器寻找失败，默认情况下会抛出异常，如果有些字段的实例可有可无，可以设置@Autowired(require = false)，这样IoC在没有找到对应的Bean实例的时候就不会抛出异常了

根据类型注入的问题在于：当Java中的一个接口（或者抽象类）有多个实现类的时候，这样就会造成
**通过类型（ by type ）获取 Bean 的不唯一 ， 从而导致 Spring IoC 类似于按类型的方法无法获**
**得唯一的实例化类**，这就是自动装配的歧义性。解决歧义性的方法是@Primary和@Qualifier：

- ＠Primary 注解告诉 Spring IoC 容器，如果存在某个类型的多个子类型，无法判
  断注入哪个的时候，优先将注解有@Primary 的实例注入

- @Qualifier 注解是告诉Spring IoC容器根据Bean的名称而不是类型来进行注入，由于Bean的id的唯一性，这样就消除了歧义性

- 注意，**@Primary是注解到Bean**上的，**@Quality是注解到Bean的引用变量**上的

  ```java
  @Component("roleService3")
  @Primary
  public class RoleServiceimpl implements RoleService{
      ...
  }
  
  @Autowired
  @Qualifier("roleService3")
  private RoleService roleService = null ;
  
  ```

- **@Qualifier和@Autowired这两个注解可以注解到参数变量上**

- @Component只能注解到类上，不能注解到方法上；可以通过注解@Bean注解到方法上（但不能注解到类上），将方法的返回值作为Spring的Bean存放到IoC容器中

  @Bean有四个配置项

  - name ： 是一个字符串数组，允许配置多个 BeanName 
  - autowire：标志是否是一个引用的 Bean 对象，默认值是 Autowire.NO 
  - initMethod ： 自定义初始化方法
  - destroyMethod ： 自定义销毁方法。

- ＠ImportResource

  如果使用XML和注解混用的方式配置Bean的时候，可以在配置类上面使用＠ImportResource来引入XML定义的Bean，这样就能将XML定义的Bean扫描进IoC容器

  ```java
  @ImportResource({"classpath:spring-dataSource.xml", "...", ...})//接受一个数组
  public class ApplicationConfig{
  }
  
  ```

- @Import

  如果配置分散到多个配置类中，那么就可以在主配置类上使用@Import来引入其他的配置类

  ```java
  @ComponentScan
  @Import({ApplicationConfig2.class, ApplicationConfig3.class, ...})//接受一个数组
  public class ApplicationConfig {
  }
  
  ```

（6）使用Profile

- @Profile注解主要是配置Spring Bean的作用环境的（dev环境、test环境还是prod环境等）

- @Profile注解在Bean上

  ```java
  @Bean(name = "devDataSource")
  @Profile("dev")
  public DataSource getDevDataSource(){
  	...
  }
  
  @Bean(name = "testDataSource")
  @Profile("test")
  public DataSource getDevDataSource(){
  	...
  }
  
  ```

- 激活Profile

  - 在使用 Spring MVC 的情况下可以配置Web上下文参数 ，或者 DispatchServlet 参数
  - 作为 JNDI 条目
  - 配置环境变量
  - 配置 JVM 启动参数
  - **在集成测试环境中使用＠ActiveProfiles** 

（9）加载属性（properties）文件

- 使用注解@PropertySource来加载属性文件，它有四个配置项：

  - name ： 字符串，配置这次属性配置的名称
  - value ： 字符串数组，可以配置多个属性文件
  - ignoreResourceNotFound : boolean 值，默认为false，其含义为如果找不到对应的属
    性文件是否进行忽略处理，由于默认值为 false，所以在默认的情况下找不到对应的
    配置文件会抛出异常 
  - encoding：编码，默认为””

  ```java
  @Configuration
  @PropertySource(value={"classpath:database-config.properties"},
  ignoreResourceNotFound=true)
  public class ApplicationConfig {
  }
  
  ```

- 使用配置文件的属性

  加载属性文件后，就可以通过`context.getEnvironment().getProperty("xxx")`来获得属性文件中的属性值，xxx是属性名

  ```java
  String url = context.getEnvironment().getProperty("jdbc.database.url");
  
  ```

- 属性文件解析类PropertySourcesPlaceholderConfigurer

  ```java
  @Configuration
  @PropertySource(value={"classpath:database-config.properties"},
  ignoreResourceNotFound=true)
  public class ApplicationConfig{
  	@Bean
  	public PropertySourcesPlaceholderConfigurer propertySourcesPlaceholderConfigurer(){
  		return new PropertySourcesPlaceholderConfigurer();
  	}
  }
  
  ```

  这样就可以直接使用@Value来引用配置的属性和占位符了

  ```java
  @Component
  public clss DataSourceBean{
  	@Value("${jdbc.datasource.driver}")
  	private String driver = null;
  	
  	@Value("${jdbc.datasource.url}")
  	private String driver = url;
  	
  	...
  }
  
  ```

（10）条件化装配Bean

- @Conditional可以配置一个或者多个类，这些类都得实现接口Condition

  ```java
  @Bean(name = "dataSource")
  @Conditional({DataSourceCondition.class, ...})
  public DataSource getDataSource(){
      ...
  }
  
  ```

  ```java
  public class DataSourceCondition implements Condition{
  	@Override
  	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata){
  		Environment env = context.getEnvironment();
  		return env.containsProperty("jdbc.database.driver") && ...;
  	}
  }
  
  ```

  可以通过context获得Spring的运行环境，当matches方法返回值为true时则装配Bean，返回false则不会创建对应Bean

  这样的作用是，如果属性配置文件的属性没有配置完整的话，可能会导致对应的Bean初始化失败，配置了@Conditional之后，可以自定义在属性配置完整后才初始化Bean

（11）Bean的作用域

- 默认情况下，Spring IoC容器会为配置的Bean生成一个实例，而不是多个

- Spring 提供了 4 种作用域，它会根据情况来决定是否生成新的对象：

  - 单例（ singleton ）：它是默认的选项，在整个应用中 ， Spring 只为其生成一个 Bean
    的实例
  - 原型（ prototype ） ： 当每次注入，或者通过 Spring IoC 容器获取 Bean 时， Spring 都
    会为它创建一个新的实例
  - 会话（ session ） ： 在 **Web 应用**中使用，就是在会话过程中 Spring 只创建一个实例 
  - 请求（ request ） ： 在 **Web 应用**中使用的，就是在一次请求中 Spring 会创建一个实例，
    但是不同的请求会创建不同的实例（和会话不同的是，一次会话中可能会有多次请求）

- 使用@Scope注解在Bean上面来配置Bean的作用域

  ```java
  @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
  
  ```

  

#### Spring AOP

（1）AOP的流程![](https://i.loli.net/2019/05/21/5ce351725257d97470.png)

（2）常见术语

- 切面（Aspect）：整个工作环境，想当一个拦截器
- 通知（Advice）：通知是切面开启后，切面的方法。它根据在代理对象真实方法调用前、后的顺序和逻
  辑区分
- 引入（Introduction）：引入允许我们**在现有的类里添加自定义的类和方法**
- 切点（Pointcut）：动态代理中，被切面拦截的方法就是一个切点
- 连接点（join point）：连接点是一个判断条件，由它可以**指定哪些是切点**。对于指定的切点， Spring 会生成
  代理对象去**使用对应的切面对其拦截**，否则就不会拦截它
- 织入（Weaving）：生成代理对象的过程，Spring中使用JDK动态代理技术来织入

（3） SpringAOP 是一种基于方法拦截的 AOP，换句话说 **Spring 只能支持方法拦截的 AOP**

（4）Spring 中有 4 种方式去实现 AOP 的拦截功能：

- 使用 ProxyFactoryBean 和对应的接口实现 AOP 
- 使用 XML 配置 AOP 
- **使用＠AspectJ 注解驱动切面（最常用）** 
- 使用 AspectJ 注入切面

（5）使用＠AspectJ 注解驱动切面

- 创建切面

  Spring中只需要使用@Aspect注解一个类，那么Spring IoC容器就会认为这是一个切面

- 添加通知（各个通知的调用顺序如下图）

  ![](https://i.loli.net/2019/05/21/5ce3564e073da46274.png)

- 连接点：连接点是添加在通知注解里的

  ```java
  @Before("execution(*com.ssm.chapter11.aop.service.impl.RoleService.printRole(...))")
  public void before(){
  	...
  }
  
  ```

  @Before里面的正则表达式就是连接点

  - execution ： 代表执行方法的时候会触发
  - *： 代表任意返回类型的方法
  - com.ssm.chapter11.aop .service.impl.RoleServicelmpl：代表类的全限定名
  - **printRole ： 被拦截方法名称**
  - (...) ： 任意的参数

- 切点：如果每个通知里面都重复写一样的连接点会很麻烦，这时候可以使用切点@Pointcut来解决

  ```java
  @Pointcut("execution(*com.ssm.chapter11.aop.service.impl.RoleService.printRole(...))")
  public void print(){
  	// 可以是空的
  }
  
  @Before("print()")
  public void before(){
  	...
  }
  
  @After("print()")
  public void after(){
  	...
  }
  
  ```

  这样print()就变成了这个切面的切点了，在**通知**中只需要将**切点方法**作为**连接点**就行了，避免重复书写冗长的正则表达式

- 启用AspectJ自动代理

  ```java
  @Configuration
  @EnableAspectJAutoProxy
  @ComponentScan
  public class AppConfig{
  	@Bean
  	public RoleAspect getRoleAspect(){
  		return new RoleAspect();
  	}
  }
  
  ```

  **@EnableAspectJAutoProxy**代表着启用 AspectJ 框架的 自动代理，这个时候 Spring 才会生成动
  态代理对象，进而可以使用 AOP；然后将我们定义的RoleAspect这个切面通过@Bean初始化到IoC容器中，这样就可以拦截printRole方法了

- 环绕通知

  ```java
  @Around("print()")
  public void around(ProceedingJoinPoint jp){
  	...	// 语句1
  	jp.proceed();
  	...	// 语句2
  }
  
  ```

  环绕通知Spring会提供一个参数ProceedingJoinPoint用来反射调用被代理对象的原有方法，这样实际上环绕通知可以在jp.proceed()前后实现@Before和@After一样的功能了;

  假如也实现了@Before和@After通知的话，Spring调用的顺序是：

  ​	**语句1->@Before->print()->语句2->@After->@AtfterReturning/@AfterThrowing**

- 织入

  织入是生成代理对象的过程，在上述的代码中，切点方法所在的类都是拥有接口的类，
  而事实上即使没有接口 ， Spring 也能提供 AOP 的功能

  - 当类的实现存在接口的
    时候， Spring 将提供 JDK 动态代理，从而织入各个通知
  - 当类不存在接口的时候没有办法使用 JDK 动态代理，
    Spring 会采用 CGLIB 来生成代理对象

- 给通知传递参数

  使用args()函数来给各类通知传递切点方法的参数

  ```java
  @Before("execution(*com.ssm.chapter11.aop.service.impl.RoleService.printRole(...)) && args(role, sort)")
  public void before(Role role, int sort){
  	...
  }
  
  ```

  注意这里，传给通知的参数是和传递给通知方法printRole(role, sort)一样的，这样就能够在前置方法中对传递的参数进行检查，不合法的话可以拦截下来，从而不调用拦截方法

- 引入

  可以通过定义一个接口，在生成动态代理对象时将被拦截对象挂靠在这个接口上，就可以为被拦截对象添加新的功能了

  ```java
  interface RoleVerifier{
  	boolean verify(Role role);
  }
  
  class RoleVerifierImpl implements RoleVerifier{
  	@Override 
  	public boolean verify(Role role){
  		return role != null;
  	}
  }
  
  // 然后在切面类中添加如下代码
  @DeclareParents(value = "com.ssm.chapter11.aop.service.impl.RoleServiceImpl+", defaultImpl = RoleVerifierImpl.class)
  public RoleVerifier roleVerifier;
  
  ```

  @DeclareParents配置项：

  - value="com.ssm.chapter11.aop.service.impl.RoleServiceImpl+"： 表示对 RoleServicelmpl
    类进行增强，也就是在 RoleServicelmpl 中引入一个新的接口
  - defaultlmpl：代表其默认的实现类，这里是 RoleVerifierlmpl 

  实现的原理其实是在生成代理对象时，

  ```java
  return Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj.getClass().getInterfaces(), this)
  
  ```

  `obj.getClass().getInterfaces()`是用来给被代理对象添加挂靠接口的，只要生成代理对象时将它挂靠在我们声明的RoleVerifier接口上面，这样我们就能够通过强制类型装换将被代理对象转化为RoleVerifier，进而调用我们引入的方法（CGLIB动态代理也一样，Enhancer也有个提供挂靠接口的方法）

  ```java
  RoleService roleService = ctx.getBean(RoleService.class );
  RoleVerifier roleVerifier = (RoleVerifier) roleService;
  // 调用引入的方法
  if(roleVerifier.verify(role)){
  	...
  }
  
  ```

- 一个方法有多个切面时，这些切面的调用顺序是根据切面在配置类中的配置顺序来调用的；也可以通过注解**@Order**来<u>*在切面声明时*</u>给这些切面设置优先级（优先级一样时，依旧是按照配置顺序来调用的）；这其实就是一种**责任链模式**

  ```java
  @Aspect
  @Order(1)
  public class Aspect1{
  	...
  }
  
  ```

  当然也可以通过**实现Ordered接口**，在**gerOrder()方法**中返回优先级也可以实现和@Order注解一样的效果

  ```java
  @Aspect
  public class Aspect1 implements Ordered{
  	@Override
  	public int getOrder() {
  		return 1;
  	}
  }
  
  ```

  ![](https://i.loli.net/2019/05/21/5ce3a97351a4e69123.png)

  

#### Spring数据库事务管理

（1） MyBatis 框架用得最多的事务管理器是 **DataSourceTransactionManager**，配置事务管理器要提供数据源

```xml
<bean id＝"transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource"/>
</bean>

```

（2）数据库资源的产生和释放如果没有委托给数据库管理器，那么就由 jdbcTemplate 管理 ，但是此时已经委托给了事务管理器，所以
jdbcTemplate 的**数据库资源和事务**己经由事务管理器处理了

（3）在配置类中使用注解＠EnableTransactionManagement
后 ， 在 Spring 上下文中使用事务注解＠Transactional, Spring 就会知道使用这个数据库事务管理器管理事务了

（4）用 Java 配置的方式来实现 Spring 数据库事务，需要在配置类中实现接口
**TransactionManagementConfigurer 的 annotationDrivenTransactionManager 方法** 。 Spring 会把
annotationDrivenTransactionManager 方法返回的事务管理器作为程序中的事务管理器

```java
// 实现接口方法 ， 使得返回数据库事务管理器
@Override
@Bean(name="transactionManager")
public PlatformTransactionManager annotationDrivenTransactionManager(){
	DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
	// initDataSource()是配置类中配置的数据源Bean
	transactionManager.setDataSource(initDataSource());
	return transactionManager;
}

```

（5）声明式事务（一种约定式事务）

- 如果使用的是声明式事务，那么当你的业务方法不发生异常（或者发生异常 ， 但该异常也
  被配置信息允许提交事务）时， Spring 就会让事务管理器提交事务 
- 发生异常（并且该
  异常不被你的配置信息所允许提交事务）时，则让事务管理器回滚事务。

（6）＠Transactional 

![](https://i.loli.net/2019/05/26/5cea055fa7d5578783.png)

![](https://i.loli.net/2019/05/26/5cea05d2bac5676266.png)

（7）数据库事务

- ACID特性
  - 原子性：一个事务中的操作要么全部成功，要么全部失败，不可能停滞在中间的某个状态
  - 一致性：事务可以修改数据库封装的状态，但是无论并发的事务有多少，数据库封装的状态都是一致的
  - 隔离性：不同事务之间的隔离程度
  - 持久性：事务一旦执行成功，那么该事务对数据库所做的修改就是永久保存的，不会被回滚
- 丢失更新
  - 第一类丢失更新：一个事务提交、一个事务回滚导致的提交事务的更新丢失（当前主流数据库已经克服了此类更新）
  - 第二类丢失更新：由于无法探知其他事务中的操作，导致两个或多个事务都提交后的更新丢失（即一个事务将之前事务的更新给覆盖掉）
- 隔离级别
  - **脏读**：允许一个事务去读取其他事务中已经修改但尚未提交的数据（如果被脏读的事务回滚了，这属于第一类更新丢失的情况，但是主流数据库已经克服了此类更新丢失，这样回滚的事务不会覆盖成功提交的事务，出现的问题就是相当于回滚的事务不起作用，成功提交的事务使用了脏读的数据进行数据库更新导致数据库最终的状态是保存了错误的数据）
  - **读/写提交**：一个事务只能读或者写另一个事务已经提交的数据（这样在一个事务成功提交、一个事务回滚的情况下，成功提交的事务没有脏读，回滚的事务由于第一类更新丢失被数据库的设计克服了，不会覆盖成功提交的事务；但是会出现的一个情况是，如果另一个事务B没有回滚，而是在另一个事务成功提交前也成功提交了，会导致最后提交的事务A出现**不可重复读**的现象，即 A在B提交前和B提交后读取的数据不一致）
  - **可重复读**：可重复读是针对**同一条记录**而言的，即不同事务对**同一条记录**的读/写按照一个序列化进行操作，不会产生交叉现象，保证了**同一条数据**的一致性（这个级别的隔离会导致**幻读现象**，幻读是针对多条记录而言的，即事务A在查询数据库多条记录，同时事务B往数据库添加或者删除了一条记录并成功提交后，事务A在提交时会幻读多出/少掉一条）
  - **序列化**：让SQL按照序列化的顺序进行读写操作，即所有事务对数据库的读写操作都是按照顺序执行的，不会产生交叉，这样就可以克服幻读现象了

![](https://i.loli.net/2019/05/26/5cea37aba16ad75008.png)

（8）隔离级别和传播行为的选择

- 选择隔离级别的出发点在于两点 ： **性能和数据一致性**，从脏读到序列化，系统性能直线下降，例如序列化，直接就抑制了并发，导致大量的请求被挂起

- @Transactional通过Isolation设置隔离级别，通过propagation设置传播行为

  - ＠Transactional 隔离级别的默认值为 Isolation .DEFAULT，其含义
    是默认的，随数据库默认值的变化而变化

- 传播行为是指**方法之间**的调用事务策略的问题，**一个方法调度另外一个方法时，可以对事务的特性进行传播配置**；即当一个事务中是由多个数据库操作完成的（不同的方法），而这些操作之间是相互独立的，那么其中一个操作失败时，没必要回滚整个事务，而只需要将失败的那个操作回滚就可以了，因为这个回滚操作对其他的独立的操作不会造成数据不一致性的影响

  ![](https://i.loli.net/2019/05/26/5cea3aebe3b5d83195.png)

（9）@Transactional 的自调用失效问题

- 注解＠Transaction 的底层实现是 SpringAOP 技术，而 SpringAOP 技术使用 的是动态代
  理 。 这就意味着对于**静态（ static ）方法和非 public 方法 ， 注解＠Transactional 是失效的** 

- 由于＠Transactional 的实现原理是
  AOP ，而 AOP 的实现原理是动态代理 ，如果是自己调用自己的过
  程，并不存在代理对象的调用，这样就不会产生 AOP 去为我们设置＠Transactional
  配置的参数，这样就出现了自调用注解失效的 问题；解决的方法时，**不直接进行自调用，而是通过从容器中获取代理对象来间接进行自调用，这样就能够启用AOP技术了**

  ```java
  // 从容器中获取 RoleService 对象 ，实际是一个代理对象
  RoleService service = ctx.getBean(RoleService.class);
  ```

（10）使用@Transactional时的一些注意点

- 如果前后调用带有@Transactional的方法，那么这两次调用不在同一个事务中
- 不要长时间占用事务，比如在事务中长时间处理和数据库事务无关的东西，这样会严重浪费数据库资源
- 在@Transactional方法中捕获到异常时一定要再将其抛出，让Spring的事务管理器去处理；否则如果没有抛出，事务管理器就会任务代码运行正常，不会对出错的事务进行回滚



### SpringMVC框架

#### SpringMVC的组件和流程

![](https://i.loli.net/2019/05/21/5ce3adec9efd490462.png)

![](https://images2017.cnblogs.com/blog/764313/201708/764313-20170801160342599-1885837375.png)

（1）Servlet在初始化时，SpringMVC会根据配置获取配置信息，从而得到**统一资源标识符URI**和**处理器Handler**之间的**映射关系（HandlerMapping）**；同时为了更加灵活和增强功能，SpringMVC还给处理器加入了**拦截器Interceptor**，可以在处理器执行前后加入自己的功能，这样就构成了一个**处理器执行链（HandlerExecutionChain）**（拦截器+处理器）

（2）当一个请求过来时，DispatchServlet首先通过请求和实现解析好的HandlerMapping，找到对应的处理器Handler，然后就准备开始执行拦截器和处理器

（3）由于运行处理器需要有一个对应的环境，它可以通过请求类型找到**处理器适配器HandlerAdapter**来运行对应的处理器及其拦截器，然后处理器将**模型和视图ModalAndView**返回给DispatchServlet

（4）如果是逻辑视图，那么DispatchServlet就会将它传递给**视图解析器ViewResolver**解析它，将模型渲染到视图中去；如果不是逻辑视图，则不会进行处理，直接通过视图渲染数据模型

（5）最后将渲染好的视图返回给客户端

![](https://i.loli.net/2019/05/21/5ce3b52f06a5095490.png)

#### SpringMVC的初始化

（1）初始化 Spring IoC 容器上下文

```java
public class ContextLoaderListener extends ContextLoader implements ServletContextListener {
    public void contextInitialized(ServletContextEvent event) {
        // 初始化Spring IoC容器，使用的是满足ApplicationContext接口的Spring Web IoC容器
        this.initWebApplicationContext(event.getServletContext());
    }

    public void contextDestroyed(ServletContextEvent event) {
        // 关闭Web IoC容器
        this.closeWebApplicationContext(event.getServletContext());
        // 清除相关参数
		ContextCleanupListener.cleanupAttributes(event.getServletContext());
    }
}

```

- DispatcherServlet 初始化前完成 Spring IoC 容器的初始化
- 在结束期完成对Spring IoC 容器的销毁

（2）初始化映射请求上下文

（略orz，SSM框架书籍P379页）

（3）使用注解配置方式初始化

- 继承一个名字比较长的类 AbstractAnnotationConfigDispatcherServletlnitializer，然后实现它所定义的方法

  ```java
  public class MyWebAppinitializer extends AbstractAnnotationConfig
  DispatcherServletinitializer {
      // Spring IoC 容器配置
      @Override
      protected Class<?>[] getRootConfigClasses() {
          // 可以返回 Spring 的 Java 配置文件数组
          return new Class<?> [] {} ;
      }
      
      //DispatcherServlet 的 URI 映射关系配置
      @Override
      protected Class<?>[] getServletConfigClasses() {
          // 可以返回 Spring 的 Java 配置文件数组
          return new Class<?>[] { WebConfig.class };
      }
      
      // DispatcherServlet 拦截内容
      @Override
      protected String[] getServletMappings () {
      	return new String[]{"*.do"}；
      }
  }
  
  ```

  只要实现了 WebApplicationlnitializer 接口的 onStartup 方法，
  Spring MVC 就会把类当作一个初始化器加载进来；上面代码继承的是
  AbstractAnnotationConfigDispatcherServletlnitializer，它们的关系如下：

  ![](https://i.loli.net/2019/05/21/5ce3c0c8bb9db12310.png)

  这就是只要继承 AbstractAnnotationConfigDispatcherServletlnitializer 类就完成了
  DispatcherServlet 映射关系和 Spring IoC 容器的初始化工作的原因。

- MyWebApplnitializer 配置类

  - getRootConfigClasses 获取 **Spring IoC 容器的 Java 配置类**，用以装载各类 Spring Bean 

    getRootConfigClasses 方法返回为空，就不加载自定义的 Bean 到 Spring IoC 容器
    中

  - getServletConfigClasses 获取各类 Spring MVC 的 **URI 和控制器的配置关系类**，用以生成 **Web 请求的上下文**

    getServletConfigClasses 加载了 WebConfig，则它就是一个 URI和控制器的映射关系
    类

  - getServletMappings 定义 **DispatcherServlet 拦截的请求**

- WebConfig配置类

  ```java
  @Configuration
  //定义扫描的包，加载控制器
  @ComponentScan ("com.*")
  //启用 Spring Web MVC
  @EnableWebMvc
  public class WebConfig {
      /***
      * 创建视图解析器
      * @return 视图解析器
      */
      @Bean(name=""viewResolver")
      public ViewResolver initViewResolver () {
          InternalResourceViewResolver viewResolver = new InternalResourceViewResolver();
          viewResolver.setPrefix("/WEB-INF/jsp/") ;
          viewResolver.setSuffix (".jsp") ;
          return viewResolver ;
      }
  }
  
  ```

  和 Spring IoC 使用 Java 的配置也是一样的， 只是多 了一个注解＠EnableWebMvc,
  它代表启动 Spring MVC 框架的配置

#### SpringMVC开发流程

（1）@RequestMapping

＠RequestMapping 可以配置在类或者方法之上，它的作用是指定 URI 和哪个类（或者方法）作为一个处理请求的处理器； Spring MVC 还定义了处理器的
拦截器 ， 当启动 Spring MVC 的 时候 ， Spring MVC 就会去解析＠Controller 中的
@RequestMapping 的配置 ， 再结合所配置的拦截器，这样它就会组成多个拦截器和一个控
制器的形式 ， 存放到一个 HandlerMapping 中去

如果没有配置@RequestMapping中的method属性，那么它会响应所有类型请求

（2）控制器开发

- 获取请求参数

  - @RequestParam 获取请求参数，默认情况下不为空，设置required为false实可以为空；为空时，可以通过设置defaultValue来设置默认值
  - Spring MVC 还提供了注解＠SessionAtrribute 去从 Session 中获取对应的
    数据

- 处理业务逻辑

- 绑定模型和视图

  - Spring MVC 会默认使用 JstlView 进行渲染，也就是
    它将查询出来的**模型绑定到 JSTL** （JSP 标准标签库）模型中

  - 使用JSON视图MappingJackson2JsonView（非逻辑视图，井不需要
    视图解析器进行解析）可以将模型转化成JSON字符串返回到前端，一般用于Ajax请求

    ```java
    ModelAndView mv = new ModelAndView();
    mv.setObject("role", role);
    mv.setView(new MappingJackson2JsonView());
    
    ```

    这样SpringMVC就会将对象role转成json字符串；但是也可以使用@ResponseBody使得返回结果变成JSON

#### 深入SpringMVC组件开发

（1）控制器接收各类请求参数

- 接受普通参数

  - 如果请求的参数名和控制器的参数名一致，则无需任何注解都能够正确接收；这种方式允许参数为空（基本数据类型除外，因为基本数据类型没有null这种值）
  - 在没有任何注解的情况下，SpringMVC有映射POJO的能力，直接将请求参数与POJO内部的属性对应来生成POJO参数实例

- 使用＠RequestParam 注解获取参数

  - 如果参数被＠RequestParam 注解，那么**默认的情况下该参数不能为空**，如果为
    空则系统会抛出异常。如果希望允许它为空，那么要修改它的配置项 required 为 false
  - ＠RequestParam("xxx")中的xxx与请求的参数名对应

- 使用 ＠RequestMapping 和 ＠PathVariable 两个注解共同协作传递URL参数

  - ＠PathVariable允许对应的参数为空

- **传递JSON参数**

  - 控制器使用@RequestBody接收JSON参数，而且一个控制器只能有一个@RequestBody，如果有多个会报错，大概是第二个之后的@RequestBody要解析时，输入流已经关闭
  - @RequestBody也具备映射POJO的能力
  - 客户端请求时要将json转为字符串传递`JSON.stringify(data)`，同时要蛇者**ContentType为"application/json"**
  - 如果是手写json来请求的话，不能使用单引号，而是要使用双引号，否则SpringMVC会解析失败

- 接收列表数据和表单序列化

  - Spring MVC 将传递过来的 JSON 数组数据（基本类型或者POJO都可以，只要请求的数据格式和控制器的参数一致即可） ， 转换为对应的 Java 集合类型。 把 List 转化为数组（ Long[] ）也是可行的

  - 将 form 数据序列化，传递给后台，则数据将以 roleName=xxx && note=xxx 传递

    ```js
    $("form").serialize()
    
    ```

    这样控制器只需要像接收普通参数一样接收即可（@RequestParam或者不用任何注解，具有解析POJO能力）

（2）重定向

- 给数据模型Model附上对应的数据（作为重定向的控制器的参数），然后返回带有redirect的字符串来实现重定向功能

  ```java
  public String addRole(Model model, ...){
  	...
  	model.addAttribute("roleName", roleName);
  	...
  	return "redirect:./showRoleJsonInfo.do";
  }
  
  ```

  **SpringMVC有个约定：当返回的字符串带有 redirect 的时候，它就会认为需要的是一个重定向**，重定向redirect后面的内容是要重定向的url

- 通过返回视图来实现重定向

  ```java
  public ModelAndView addRole2(ModelAndView mv, ...){
  	...
  	mv.addObject("roleName", roleName);
  	mv.setViewName("redirect:./showRoleJsonInfo.do");
  	return mv;
  }
  
  ```

- HTTP的重定向以字符串来传递参数，因此不能有效传递对象，这个时候 SpringMVC
  提供了 一个方法——**flash 属性**，需要提供的数据模型就是一个 **RedirectAttribute**

  ```java
  public String addRole3(RedirectAttributes ra, ...){
  	...
  	ra.addFlashAttribute("role", role);
  	return "redirect:./showRoleJsonInfo.do";
  }
  
  ```

  这样就能给重定向的地址传递POJO对象参数了

  使用
  addFlashAttribute 方法后， Spring MVC 会**将数据保存到 Session** 中 （Session 在一个会话期
  有效），**重定向后就会将其清除**，这样就能够传递数据给下一个地址了

  ![](https://i.loli.net/2019/05/21/5ce3fb60a042f27164.png)

（3）拦截器

- 拦截器可以在请求进入控制器前、后以及视图渲染完成后进行相关的操作

- Spring 要求 处 理器 的 拦截器 都 要实 现 接 口 org.springframework. web.servlet.
  HandlerInterceptor，也可以继承HandlerInterceptorAdapter（实现了HandlerInterceptor接口）

  - preHandle：在处理器之前执行的前置方法，这样 SpringMVC 可以在进入处理
    器前处理一些方法了。注意，它将返回一个 boolean 值，会影响到后面 Spring MVC
    的流程
  - postHandle：在处理器之后执行的后置方法，处理器的逻辑完成后运行它
  - afterCompletion：无论是否产生异常都会**在渲染视图后**执行的方法

  ![](https://i.loli.net/2019/05/26/5cea5390c7bac23050.png)

- springboot中使用拦截器，只需要在配置类（继承了WebMvcConfigurer且注解了@Configuration类）中重写addInterceptors方法即可，使用InterceptorRegistry来添加拦截器及拦截规则

  ```java
  @Configuration
  public class InterceptorConfiguration implements WebMvcConfigurer {
      public final static String SESSION_KEY = "user";
      @Autowired
      AudienceInteceptor audienceInteceptor;
      @Autowired
      AdminInterceptor adminInterceptor;
      @Autowired
      ManagerInterceptor managerInterceptor;
  
      @Override
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(audienceInteceptor).addPathPatterns("/user/**");
          registry.addInterceptor(adminInterceptor).addPathPatterns("/admin/**");
          registry.addInterceptor(managerInterceptor).addPathPatterns("/manager/**");
      }
  }
  ```

- 多个拦截器的执行顺序和责任链模式的执行顺序一致

  ![](https://i.loli.net/2019/05/26/5cea54b87799b56654.png)

  当其中的一个 preHandle 方法返回为 false 后，按配置顺序，后面的 preHandle
  方法都不会运行了，而控制器和所有的后置方法 postHandle 也不会再运行。执行过 preHandle
  方法且该方法返回为 true 的拦截器的完成方法 （afterCompletion）会按照配置的逆序运行 

  ![](https://i.loli.net/2019/05/26/5cea55177a5f619765.png)







## 附：随手记

### Java

1.1、Java接口多继承

第一次注意到这个玩意是springMVC框架源码里面出现的接口多继承：

```java
/**
 * A {@link GenericConverter} that may conditionally execute based on attributes
 * of the {@code source} and {@code target} {@link TypeDescriptor}.
 *
 * <p>See {@link ConditionalConverter} for details.
 *
 * @author Keith Donald
 * @author Phillip Webb
 * @since 3.0
 * @see GenericConverter
 * @see ConditionalConverter
 */
public interface ConditionalGenericConverter extends GenericConverter, ConditionalConverter {

}

```

搜了一下，摘录如下：

> 先从Java 8之前说起。要区分“声明多继承”与“实现多继承”。
> Java是不允许“实现多继承”，简称不允许“多继承”。但是Java支持“声明多继承”——Java的接口的多继承——一个类可以实现多个接口（“继承”了多个接口上的方法声明），而一个接口可以继承多个接口（同样是“继承”了多个接口上的方法声明）。
> 接口只允许有方法声明而不允许有实现，因而不会出现像C++那样的实现多继承的决议问题；抽象类可以有方法实现，但要遵循Java类的单继承限制，也避免了实现多继承的问题。这是早期Java为了与C++区分开的一个决定。
>
> 然后，从Java 8开始，接口允许为方法提供“默认实现”了——默认方法（default method）。因而实质上Java 8的接口多继承其实也会涉及到实现多继承，并且语言层面有专门规定去解决实现多继承时选择哪个版本的问题——哪个都不选择，而是在发现会继承多个默认方法实现并且没有override时报错，逼使用户显式override可能冲突的方法。
> 这使得Java 8开始接口可以当作traits来使用，达到实现多继承的目的。
>
> 作者：RednaxelaFX
>
> 链接：https://www.zhihu.com/question/24317891/answer/65097560
>
> 来源：知乎
>
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

1.2、Java中的byte，short，char进行计算时都会提升为int类型。（来源：<https://www.nowcoder.com/test/question/done?tid=23696962&qid=25211#summary>）

1.3、依赖倒置原则

> 参考：<https://zhuanlan.zhihu.com/p/24175489>

1.4、Spring循环依赖问题

> 参考：<https://zhuanlan.zhihu.com/p/34923466>



### jQuery

2.1 GET与POST的区别

- GET 基本上用于从服务器获得（取回）数据。注释：GET 方法可能返回缓存数据。
- POST 也可用于从服务器获取数据。不过，POST 方法不会缓存数据，并且常用于连同请求一起发送数据。

2.2 同源策略

> 参见：<https://developer.mozilla.org/zh-CN/docs/Web/Security/Same-origin_policy>

2.3 jsonp

> 参考：<https://www.cnblogs.com/dowinning/archive/2012/04/19/json-jsonp-jquery.html>

2.4 jQuery.validate插件

```js
if ( typeof define === "function" && define.amd ) {
	define( ["jquery"], factory );
} else {
	factory( jQuery );
}

```

代码解释：<https://stackoverflow.com/questions/30953589/what-is-typeof-define-function-defineamd-used-for>

2.5 require.js模块开发

> 参见：<https://javascript.ruanyifeng.com/tool/requirejs.html>

2.6 jQuery 库中的 $() 是什么？

$() 函数是 **jQuery() 函数的别称**。$() 函数用于将任何对象包裹成 jQuery 对象，接着你就被允许调用定义在 jQuery 对象上的多个不同方法。你可以将一个选择器字符串传入 $() 函数，它会返回一个包含所有匹配的 DOM 元素数组的 jQuery 对象

2.7 $(document).ready()方法和window.onload有什么区别？

- window.onload方法是在网页中所有的元素(包括元素的所有关联文件)完全加载到浏览器后才执行的。
- $(document).ready() 方法可以在DOM载入就绪时就对其进行操纵，并调用执行绑定的函数。

2.8 jquery中$.get()提交和$.post()提交有区别吗？

相同点：都是异步请求的方式来获取服务端的数据；

异同点：

- 请求方式不同：$.get() 方法使用GET方法来进行异步请求的。$.post() 方法使用POST方法来进行异步请求的。
- 参数传递方式不同：get请求会将参数跟在URL后进行传递，而POST请求则是作为HTTP消息的实体内容发送给Web服务器的，这种传递是对用户不可见的。
- 数据传输大小不同：get方式传输的数据大小不能超过2KB 而POST要大的多
- 安全问题： GET 方式请求的数据会被浏览器缓存起来，因此有安全问题。

2.9 简单的$.ajax()的请求方式（$.ajax()接受的参数是键值对列表）

```js
$.ajax({
    url:'http://www.baidu.com',
    type:'POST',
    data:data,
    cache:true,
    headers:{},
    beforeSend：function(){},
    success:function(){},
    error:function(){},
    complete:function(){}
}); 

```



### JavaScript面试题

（1）参考自：<https://www.cnblogs.com/wdlhao/p/8290436.html#_label1>

（2）**比较typeof与instanceof？**

- 相同点：JavaScript 中 typeof 和 instanceof 常用来判断一个变量是否为空，或者是什么类型的。
- typeof的定义和用法：返回值是一个字符串，用来说明变量的数据类型。
- 细节：
- (1)、typeof 一般只能返回如下几个结果：number,boolean,string,function,object,undefined。
- (2)、typeof 来获取一个变量是否存在，如 if(typeof a!="undefined"){alert("ok")}，而不要去使用 if(a) 因为如果 a 不存在（未声明）则会出错。
- (3)、对于 Array,Null 等特殊对象使用 typeof 一律返回 object，这正是 typeof 的局限性。
- Instanceof定义和用法：instanceof 用于判断一个变量是否属于某个对象的实例。

（3）细节：

​	如下，得到的结果为‘N’,这里的 **instanceof 测试的 object 是指 js 语法中的 object**，不是指 dom 模型对象。

```js
if (window instanceof Object){ alert('Y')} else {  alert('N');}  // 'N'

```

（4）跨域访问

- 什么是跨域：由于浏览器同源策略，凡是发送请求url的**协议、域名、端口**三者之间任意一与当前页面地址不同即为跨域

  > 存在跨域的情况：
  >
  > - 网络协议不同，如http协议访问https协议。
  > - 端口不同，如80端口访问8080端口。
  > - 域名不同，如qianduanblog.com访问baidu.com。
  > - 子域名不同，如abc.qianduanblog.com访问def.qianduanblog.com。
  > - 域名和域名对应ip,如www.a.com访问20.205.28.90.

- Web页面上调用js文件时则不受是否跨域的影响（不仅如此，我们还发现凡是拥有"src"这个属性的标签都拥有跨域的能力，比如\<script>、\<img>、\<iframe>）；客户端请求js文件获取到本地之后**会立即执行**

- 为了便于客户端使用数据，逐渐形成了一种非正式传输协议，人们把它称作JSONP，该协议的一个要点就是<u>*允许用户传递一个callback参数给服务端，然后服务端返回数据时会将这个callback参数作为函数名来包裹住JSON数据，这样客户端就可以随意定制自己的函数来自动处理返回数据了*</u>

### Mysql

（1）限制查询范围时，offset要和limit配合使用，单独使用offset会报错

（2）mysql中不等号使用<>, !=均可；但如果是在mybatis中书写mysql查询语句时，由于<>得进行转义，使用不方便，因此推荐使用!=；mysql中的等号是=而不是==

（3）mysql中的比较符号可以直接用在时间类型的字段比较中；current_timestamp()函数可以返回当前的时间戳

（4）mysql更新语句的update是和set一起配合使用的

```mysql
UPDATE table_name SET field1=new-value1, field2=new-value2 [WHERE Clause]

```

（5）删除记录的语法是delete from

```mysql
DELETE FROM table_name [WHERE Clause]

```

（6）插入记录的语法是insert into

```mysql
INSERT INTO table_name ( field1, field2,...fieldN )
                       VALUES
                       ( value1, value2,...valueN );

```

（7）MySQL LIKE 子句

- LIKE 通常与 **%** 一同使用，类似于一个元字符的搜索，**%**字符表示匹配任意字符

- like语句用在where条件中，用于进行模糊匹配

  ```mysql
  SELECT field1, field2,...fieldN 
  FROM table_name
  WHERE field1 LIKE condition1 [AND [OR]] filed2 = 'somevalue'
  
  ```

（8）MySQL UNION 操作符

- MySQL UNION 操作符用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中；多个 SELECT 语句会删除重复的数据

- 语法格式

  ```mysql
  SELECT expression1, expression2, ... expression_n
  FROM tables
  [WHERE conditions]
  UNION [ALL | DISTINCT]
  SELECT expression1, expression2, ... expression_n
  FROM tables
  [WHERE conditions];
  
  ```

  distinct：删除结果集中重复的数据【这是默认值】

  all：返回所有结果集，包含重复数据

  另外，union的结果集**必须含有相同的列**数，属性不同可以，但是会**以第一个结果集的列属性为准**；如果列属性不一样，那么distinct就会起作用了，它会**根据数据的值来判断是否一致而不是属性类型来判断**

  ```mysql
  mysql> select * from demo;
  +----+------+------+
  | id | name | note |
  +----+------+------+
  |  1 | test | test |
  +----+------+------+
  1 row in set (0.00 sec)
  
  mysql> select id, name from demo union select id, note from demo;
  +----+------+
  | id | name |
  +----+------+
  |  1 | test |
  +----+------+
  1 row in set (0.00 sec)
  
  mysql> select id, name from demo union all select id, note from demo;
  +----+------+
  | id | name |
  +----+------+
  |  1 | test |
  |  1 | test |
  +----+------+
  2 rows in set (0.00 sec)
  
  ```

  可以看到，union的两个结果集的列不一致，但是合并之后，以第一个结果集的属性最终结果集的属性（id   name），然后使用union distinct时，它判断的是数据的值，发现name='test'和note='test'值相同，因此只有最终结果集中只有一条记录返回

（9）排序

```mysql
SELECT field1, field2,...fieldN table_name1, table_name2...
ORDER BY field1 [ASC [DESC][默认 ASC]], [field2...] [ASC [DESC][默认 ASC]]

```

通过order by设置排序的字段，可以有多个；默认按照升序排序ASC，也可以设置DESC设置降序排列

（10）分组

在MYSQL中使用GROUP BY对表中的数据进行分组时：

- **GROUP BY X意思是将所有具有相同X字段值的记录放到一个分组里，**
- **GROUP BY X, Y意思是将所有具有相同X字段值和Y字段值的记录放到一个分组里。**

分组之后需要对分组进行操作，这时候需要用到 COUNT, SUM, AVG,等函数

```mysql
SELECT column_name, function(column_name)
FROM table_name
WHERE column_name operator value
GROUP BY column_name;

```

function指的就是COUNT, SUM, AVG,等函数，函数里面的列是select后面的列中的一个或者多个，也可以是全部列(*)

`GROUP BY column_name`后面在使用rollup关键字可以在分组统计数据的基础上在进行相同的统计（SUM,AVG,COUNT…）

```mysql
mysql> SELECT name, SUM(singin) as singin_count FROM  employee_tbl GROUP BY name WITH ROLLUP;
+--------+--------------+
| name   | singin_count |
+--------+--------------+
| 小丽 |            2 |
| 小明 |            7 |
| 小王 |            7 |
| NULL   |           16 |
+--------+--------------+
4 rows in set (0.00 sec)

```

这个就是在计算了每个分组的sum之后，对所有的分组进行sum，即将所有分组看成一个大的分组进行sum

（11）JOIN

- **INNER JOIN（或者JOIN）**：获取两个表中字段匹配关系的记录

  ![](http://www.runoob.com/wp-content/uploads/2014/03/img_innerjoin.gif)

- **LEFT JOIN（左连接）：**获取左表所有记录，即使右表没有对应匹配的记录（（没有记录的字段为NULL））

  ![](http://www.runoob.com/wp-content/uploads/2014/03/img_leftjoin.gif)

- **RIGHT JOIN（右连接）：** 与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录（没有记录的字段为NULL）

  ![](http://www.runoob.com/wp-content/uploads/2014/03/img_rightjoin.gif)

  示例代码

```mysql
select a.runoob_id, a.runoob_author, a.runoob_title, b.runoob_count from runoob_tbl a right join tcount_tbl b on a.runoob_author = b.runoob_author;

```

使用`...  left join/join/right   ...  on   [条件]` 的语法

（12）NULL值比较

- **IS NULL:** 当列的值是 NULL,此运算符返回 true。

- **IS NOT NULL:** 当列的值不为 NULL, 运算符返回 true。

- **<=>:** 比较操作符（不同于=运算符），当比较的的两个值为 NULL 时返回 true。

  <=>可以代替=运算符，好处是当其中一个值为null时返回false，都为null时返回true，即使得null可以进行比较

- NULL 值与任何其它值的比较（即使是 NULL）永远返回 false，即 **NULL = NULL 返回false** ，同时**NULL != NULL也返回false**

（13）正则表达式

​	在Mysql中可以使用REGEXP操作符来进行正则表达式匹配，可以起到和LIKE操作符模糊匹配一样的功能

```mysql
mysql> SELECT name FROM person_tbl WHERE name REGEXP '^st';
mysql> select * from runoob_tbl where runoob_author regexp '^[^a-zA-Z]+$';

```

（14）alter命令

- `ALTER TABLE testalter_tbl  DROP i;`用于删除表的i字段；如果标准只有一个字段，drop命令会失败

- `ALTER TABLE testalter_tbl ADD i INT;`用于添加表字段；新添加的字段i会追加在原有表字段的末尾

  如果你需要指定新增字段的位置，可以使用MySQL提供的关键字 **FIRST (设定位第一列)**， **AFTER 字段名（设定位于某个字段之后）**

  ```mysql
  ALTER TABLE testalter_tbl DROP i;
  ALTER TABLE testalter_tbl ADD i INT FIRST;
  ALTER TABLE testalter_tbl DROP i;
  ALTER TABLE testalter_tbl ADD i INT AFTER c;
  ```

- `ALTER TABLE testalter_tbl MODIFY c CHAR(10);`用于修改现有表字段的描述，新的描述直接覆盖原有字段的描述；

  **modify不能修改字段名，而change可以修改字段名**

  ```mysql
  ALTER TABLE testalter_tbl CHANGE i j BIGINT;
  ```

  这个表示将原字段i修改名称为j，并且描述是BIGINT

- 也可以直接修改表的类型，`ALTER TABLE testalter_tbl ENGINE = MYISAM;`这个是修改数据表的存储引擎，可以通过`SHOW TABLE STATUS LIKE 'testalter_tbl'\G`来查看表的类型

  ```mysql
  mysql> show table status like 'runoob_tbl'\G
  *************************** 1. row ***************************
             Name: runoob_tbl
           Engine: MyISAM
          Version: 10
       Row_format: Dynamic
             Rows: 5
   Avg_row_length: 3276
      Data_length: 16384
  Max_data_length: 0
     Index_length: 0
        Data_free: 0
   Auto_increment: 6
      Create_time: 2019-05-24 16:04:59
      Update_time: 2019-05-24 15:10:46
       Check_time: NULL
        Collation: utf8_general_ci
         Checksum: NULL
   Create_options: 
          Comment: 
  1 row in set (0.00 sec)
  ```

- 可以使用`rename to`来修改数据表的名称

  ```mysql
  ALTER TABLE testalter_tbl RENAME TO alter_tbl;
  
  ```

  

### HTML+CSS

（1）盒子模型

在网页中，一个元素占有空间的大小由几个部分构成，其中包括元素的内容（content），元素的内边距（padding），元素的边框（border），元素的外边距（margin）四个部分。这四个部分占有的空间中，有的部分可以显示相应的内容，而有的部分只用来分隔相邻的区域或区域。4个部分一起构成了css中元素的盒模型。

（2）src与href的区别

- **href 是指向网络资源所在位置**，建立和当前元素（锚点）或当前文档（链接）之间的链接，用于超链接。
- **src是指向外部资源的位置**，**指向的内容将会嵌入到文档中当前标签所在位置**；在请求src资源时会将其指向的资源下载并应用到文档内，例如js脚本，img图片和frame等元素。
- 当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源**加载、编译、执行完毕（如果是js文件的话，会将js文件里面的代码执行完再接着往下处理）**，图片和框架等元素也如此，类似于将所指向资源嵌入当前标签内。这也是为什么将js脚本放在底部而不是头部。

（3）同步和异步的区别

- **同步是阻塞模式，异步是非阻塞模式**
- 同步就是指一个进程在执行某个请求的时候，若该请求需要一段时间才能返回信息，那么这个进程将会一直等待下去，直到收到返回信息才继续执行下去；
  异步是指进程不需要一直等下去，而是继续执行下面的操作，不管其他进程的状态。当有消息返回时系统会通知进程进行处理，这样可以提高执行的效率。

（4）px和em的区别

- 相同点：px和em都是长度单位；
- 异同点：px的值是固定的，指定是多少就是多少，计算比较容易。**em得值不是固定的，并且em会继承父级元素的字体大小**。
  浏览器的默认字体高都是16px。所以未经调整的浏览器都符合: 1em=16px。那么12px=0.75em, 10px=0.625em。

> px、em、rem区别介绍，参考：<http://www.runoob.com/w3cnote/px-em-rem-different.html>

（5）sessionStorage 、localStorage 和 cookie 之间的区别

> Web Storage 包含如下两种机制：
>
> - `sessionStorage` 为每一个给定的源（given origin）维持一个独立的存储区域，该存储区域在页面会话期间可用（即**只要浏览器处于打开状态**，包括页面重新加载和恢复）。
> - `localStorage` 同样的功能，但是**在浏览器关闭，然后重新打开后数据仍然存在**。

 共同点：用于浏览器端存储的缓存数据

不同点：

- 存储内容是否发送到服务器端
  - 当设置了Cookie后，数据会发送到服务器端，造成一定的宽带浪费；
  - web storage,会将数据保存到本地，不会造成宽带浪费；
- 数据存储大小不同
  - Cookie数据不能超过4K,适用于会话标识；
  - web storage数据存储可以达到5M;
- 数据存储的有效期限不同
  - cookie只在设置了Cookid过期时间之前一直有效，即使关闭窗口或者浏览器；
  - sessionStorage,仅在关闭浏览器之前有效；
  - localStorage,数据存储永久有效；
- 作用域不同
  - cookie和localStorage是在同源同窗口中都是共享的；
  - sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；

（6）请指出document load和document ready的区别？

- 共同点：这两种事件都代表的是页面文档加载时触发。
- 异同点：
  - ready 事件的触发，表示文档结构已经加载完成（不包含图片等非文字媒体文件）。
  - onload 事件的触发，表示页面包含图片等文件在内的所有元素都加载完成。

### HTTP面试题

> 参考：<https://juejin.im/post/5a8102e0f265da4e710f5910>

### web前端面试题

#### 前端攻击方式/前端安全

XSS攻击：跨站脚本攻击

CSRF攻击：跨站请求伪造攻击

#### 网站性能优化/js性能优化

https://juejin.im/post/59672fbff265da6c3f70cd53

#### 跨浏览器兼容

#### react性能优化

（1）重写shouldComponentUpdate来避免不必要的dom操作。

（2）使用 production 版本的react.js。

（3）使用key来帮助React识别列表中所有子组件的最小变化。

virtualDOM、合成事件系统

#### 事件冒泡机制

#### get和post有什么区别？

其实，GET和POST本质上两者没有任何区别。他们都是HTTP协议中的请求方法。底层实现都是基于TCP/IP协议。所谓区别，只是浏览器厂家根据约定，做得限制而已。

- get是通过明文发送数据请求，而post是通过密文；
- get传输的数据量有限，因为url的长度有限，post则不受限；
- GET请求的参数只能是ASCII码，所以中文需要URL编码，而POST请求传参没有这个限制
- GET产生一个TCP数据包；POST产生两个TCP数据包。对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。

#### document.onload和document.ready两个事件的区别

页面加载完成有两种事件，一是ready，表示文档结构已经加载完成（不包含图片等非文字媒体文件），二是onload，指示页面包含图片等文件在内的所有元素都加载完成。

#### 计算机网络部分

##### DNS解析过程

　 **1）** **浏览器缓存**

　　当用户通过浏览器访问某域名时，浏览器首先会在自己的缓存中查找是否有该域名对应的IP地址（若曾经访问过该域名且没有清空缓存便存在）；

　　**2）** **系统缓存**

　　当浏览器缓存中无域名对应IP则会自动检查用户计算机系统Hosts文件DNS缓存是否有该域名对应IP；

　　**3）** **路由器缓存**

　　当浏览器及系统缓存中均无域名对应IP则进入路由器缓存中检查，以上三步均为客服端的DNS缓存；

　　**4）** **ISP（互联网服务提供商）DNS缓存**

　　当在用户客服端查找不到域名对应IP地址，则将进入ISP DNS缓存中进行查询。比如你用的是电信的网络，则会进入电信的DNS缓存服务器中进行查找；

　　**5）** **根域名服务器**

　　当以上均未完成，则进入根服务器进行查询。全球仅有13台根域名服务器，1个主根域名服务器，其余12为辅根域名服务器。根域名收到请求后会查看区域文件记录，若无则将其管辖范围内顶级域名（如.com）服务器IP告诉本地DNS服务器；

　　**6）** **顶级域名服务器**

　　顶级域名服务器收到请求后查看区域文件记录，若无则将其管辖范围内主域名服务器的IP地址告诉本地DNS服务器；

　　**7）** **主域名服务器**

　　主域名服务器接受到请求后查询自己的缓存，如果没有则进入下一级域名服务器进行查找，并重复该步骤直至找到正确纪录；

　　**8）保存结果至缓存**

　　本地域名服务器把返回的结果保存到缓存，以备下一次使用，同时将该结果反馈给客户端，客户端通过这个IP地址与web服务器建立链接。

#### 算法与数据结构

![](https://user-gold-cdn.xitu.io/2018/6/27/1643feb9f196b2c5?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

