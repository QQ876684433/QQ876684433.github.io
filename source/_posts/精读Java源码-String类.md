---
title: 精读Java源码-String类
date: 2019-07-16 20:59:00
categories:
- 源码
tags:
- Java
---

# 精读Java源码 - String类

> 基于jdk1.8
>
> 推荐资料：
>
> - https://www.kaelli.com/33.html
> - http://cmsblogs.com/?p=5256
> - http://cmsblogs.com/?p=5258

## 类定义

public没什么好说的，final修饰符则决定了String类是不可继承的且对象是不可变的，你无法自己写一个继承自String的类，而String对象的不可变性也是大家耳熟能详的。

实现了java.io.Serializable、Comparable和CharSequence共3个接口：

- Serializable是用来保证String可以序列化和反序列化的。
- Comparable的compareTo(String str)方法则是实现String的对比排序的。
- CharSequence的length()方法用来返回字符串长度，charAt(int index)则可以获取到给定位置的单个字符，subSequence(int start, int end)则提供了截取子字符串的功能，toString()方法对于String来说就是返回它自己了

## 重要属性

```java
/** The value is used for character storage. */
private final char value[];

/** Cache the hash code for the string */
private int hash; // Default to 0

/** use serialVersionUID from JDK 1.0.2 for interoperability */
private static final long serialVersionUID = -6849794470754667710L;

/**
 * Class String is special cased within the Serialization Stream Protocol.
 */
private static final ObjectStreamField[] serialPersistentFields =
        new ObjectStreamField[0];

/**
 * A Comparator that orders {@code String} objects as by
 * {@code compareToIgnoreCase}. This comparator is serializable.
 */
public static final Comparator<String> CASE_INSENSITIVE_ORDER
        = new CaseInsensitiveComparator();
```

- value 

  用于存储字符串对象的值，在jdk1.8里面是char数组，在后面的版本中改成了byte数组；被final修饰说明它是不可变的

- hash 

  为字符串对象的哈希值，默认值为0

- serialPersistentFields 

  *看定义，它是一个私有的、静态的、不可改变的*ObjectStreamField数组，实际上在JDK源码里有很多类都有这样一个属性，这些类的共同点是都实现了java.io.Serializable接口。在默认情况下一个实现了Serializable接口的类，所有的非 transient 非 static 修饰的字段都会被序列化，但**如果还定义了serialPersistentFields字段，则只有serialPersistentFields里添加的字段才会被序列化**。当一个字段用transient修饰，但又位于serialPersistentFields数组里时，它依然会被序列化——说明**serialPersistentFields的作用优先级是比transient高的**。

  在String类里，*serialPersistentFields是一个容量为0的空数组，显然String的字段都不会被序列化*

- CASE_INSENSITIVE_ORDER 

  表示用于排序的比较器

## 构造函数

排除@Deprecated的构造函数，String类提供的构造函数主要有以下几类：

- 无参构造函数

  ```java
  public String() {
  	this.value = "".value;
  }
  ```

- 从原有字符串构造新的字符串

  ```java
  public String(String original) {
  	this.value = original.value;
  	this.hash = original.hash;
  }
  ```

  直接对参数里的原字符串进行浅拷贝构造新的字符串，由于String类是final的，而且内部的value也是final的，所以字符序列在不同的String实例中共享不会带来任何副作用

- 通过字符数组构造字符串

  ```java
  public String(char value[]) {
  	this.value = Arrays.copyOf(value, value.length);
  }
  
  public String(char value[], int offset, int count) {
      if (offset < 0) {
      	throw new StringIndexOutOfBoundsException(offset);
      }
      if (count <= 0) {
          if (count < 0) {
              throw new StringIndexOutOfBoundsException(count);
          }
          if (offset <= value.length) {
              this.value = "".value;
              return;
          }
  	}
      // Note: offset or count might be near -1>>>1.
      if (offset > value.length - count) {
      	throw new StringIndexOutOfBoundsException(offset + count);
      }
      this.value = Arrays.copyOfRange(value, offset, offset+count);
  }
  ```

  这个也没什么好讲的，它直接对参数里的字节数组进行了拷贝，这样的目的是使得外部的字节数组更改之后不会对字符串的字符序列造成影响。`offset > value.length - count`的处理是为了防止`offset+count`的int类型溢出

- 通过整型数组构造字符串

  ```java
  public String(int[] codePoints, int offset, int count) {
      if (offset < 0) {
          throw new StringIndexOutOfBoundsException(offset);
      }
      if (count <= 0) {
          if (count < 0) {
              throw new StringIndexOutOfBoundsException(count);
          }
          if (offset <= codePoints.length) {
              this.value = "".value;
              return;
          }
      }
      // Note: offset or count might be near -1>>>1.
      if (offset > codePoints.length - count) {
          throw new StringIndexOutOfBoundsException(offset + count);
      }
  
      //=============================分割线============================
      
      final int end = offset + count;
  
      // Pass 1: Compute precise size of char[]
      int n = count;
      for (int i = offset; i < end; i++) {
          int c = codePoints[i];
          if (Character.isBmpCodePoint(c))
              continue;
          else if (Character.isValidCodePoint(c))
              n++;
          else throw new IllegalArgumentException(Integer.toString(c));
      }
  
      // Pass 2: Allocate and fill in char[]
      final char[] v = new char[n];
  
      for (int i = offset, j = 0; i < end; i++, j++) {
          int c = codePoints[i];
          if (Character.isBmpCodePoint(c))
              v[j] = (char)c;
          else
              Character.toSurrogates(c, v, j++);
      }
  
      this.value = v;
  }
  ```

  分割线上部分没什么好讲的，就是常规的参数边界检查，下半部分有点意思，它涉及到了代码点和代码单元的知识以及Java对Unicode字符集的处理方法，具体的可以参考：[https://chph.xyz/2019/07/17/%E7%94%B1String%E7%9A%84%E6%9E%84%E9%80%A0%E6%96%B9%E6%B3%95%E5%BC%95%E7%94%B3%E5%87%BA%E6%9D%A5%E7%9A%84java%E5%AD%97%E7%AC%A6%E7%BC%96%E7%A0%81/#%E7%94%B1String%E7%9A%84%E6%9E%84%E9%80%A0%E6%96%B9%E6%B3%95%E5%BC%95%E7%94%B3%E5%87%BA%E6%9D%A5%E7%9A%84java%E5%AD%97%E7%AC%A6%E7%BC%96%E7%A0%81](https://chph.xyz/2019/07/17/由String的构造方法引申出来的java字符编码/#由String的构造方法引申出来的java字符编码)

  通俗来讲，它的处理分为两个阶段：

  （1）计算int数组转成char数组后的精确长度，之所以两者不同，是因为Java采用了UTF-16字符编码，而Unicode代码点在不同的编码中的代码单元是不同的，UTF-16的字符编码由**基本多语言面（BMP UBasic Multilingual Plane ）**和**增补字符（Supplementary Character）**两部分组成，前者占用一个char，后者占用两个char，因此在遍历int数组时，`Character.isBmpCodePoint(c)`用来判断`c`是否是BMP，如果是，则长度不变，如果不是，而且它是有效的代码点`Character.isValidCodePoint(c)`，那么就要在原来int数组的长度是加一`n++`

  （2）计算出char数组的长度后，就可以分配空间并填充该空间了，道理类似，如果是BMP，则直接将int转为char，否则的话就要使用`Character.toSurrogates(c, v, j++)`来转换

  ```java
  static void toSurrogates(int codePoint, char[] dst, int index) {
      // We write elements "backwards" to guarantee all-or-nothing
      dst[index+1] = lowSurrogate(codePoint);
      dst[index] = highSurrogate(codePoint);
  }
  ```

  这个涉及到增补字符的高代理项和低代理项的转换，具体的原理在上面的链接里有提到了，这里不再深究，留待精读`Character`源码时再研究

  最后将填充完成的char数组赋值给新的String实例的value即可

- 通过字节数组构造字符串

  ```java
  public String(byte bytes[], int offset, int length, String charsetName)
          throws UnsupportedEncodingException {
      if (charsetName == null)
          throw new NullPointerException("charsetName");
      checkBounds(bytes, offset, length);
      this.value = StringCoding.decode(charsetName, bytes, offset, length);
  }
  
  public String(byte bytes[], int offset, int length, Charset charset) {
      if (charset == null)
          throw new NullPointerException("charset");
      checkBounds(bytes, offset, length);
      this.value =  StringCoding.decode(charset, bytes, offset, length);
  }
  
  public String(byte bytes[], String charsetName)
          throws UnsupportedEncodingException {
      this(bytes, 0, bytes.length, charsetName);
  }
  
  public String(byte bytes[], Charset charset) {
      this(bytes, 0, bytes.length, charset);
  }
  
  public String(byte bytes[], int offset, int length) {
      checkBounds(bytes, offset, length);
      this.value = StringCoding.decode(bytes, offset, length);
  }
  
  public String(byte bytes[]) {
      this(bytes, 0, bytes.length);
  }
  ```

  这几个构造函数的功能类似，根据给定的字符编码，调用`StringCoding.decode`来对字节数组进行解码来构造字符串，如果不提供字符编码的话，`StringCoding.decode`内部会调用`String csn = Charset.defaultCharset().name();`来使用Java默认的字符串编码来解码。

## 内部类

### CaseInsensitiveComparator

`CaseInsensitiveComparator`是String类内部一个大小写不敏感的字符串比较器，被String类内部的方法`compareToIgnoreCase`使用

```java
private static class CaseInsensitiveComparator
        implements Comparator<String>, java.io.Serializable {
    // use serialVersionUID from JDK 1.2.2 for interoperability
    private static final long serialVersionUID = 8575799808933029326L;

    public int compare(String s1, String s2) {
        int n1 = s1.length();
        int n2 = s2.length();
        int min = Math.min(n1, n2);
        for (int i = 0; i < min; i++) {
            char c1 = s1.charAt(i);
            char c2 = s2.charAt(i);
            if (c1 != c2) {
                c1 = Character.toUpperCase(c1);
                c2 = Character.toUpperCase(c2);
                if (c1 != c2) {
                    c1 = Character.toLowerCase(c1);
                    c2 = Character.toLowerCase(c2);
                    if (c1 != c2) {
                        // No overflow because of numeric promotion
                        return c1 - c2;
                    }
                }
            }
        }
        return n1 - n2;
    }

    /** Replaces the de-serialized object. */
    private Object readResolve() { return CASE_INSENSITIVE_ORDER; }
}
```

`compareTo`方法的主要思路是，循环遍历比较两个字符串长度匹配的部分，如果该部分忽略大小写后相等，那么比较的结果就是两者长度的比较，按照字典序，结果就是长度小的靠前。

长度匹配部分的比较比较有意思，它有三次判断：直接比较、转成大写字母比较、转成小写字母比较。这么做的原因和`regionMatches`方法的处理方式大致相同，因为对有些语言比如格鲁吉亚字母（Georgian alphabet），它的大小写转换规则有点特殊：**大写不等还不一定不等，还需要转小写   比如 ქართული დამწერლობა**。因此在退出比较之前还要再次转换成小写字母进行最后一次检查。

关于`readResolve`，我在stackoverflow找到的解释如下：

> `readResolve` is used for *replacing* the object read from the stream. The only use I've ever seen for this is enforcing singletons; when an object is read, replace it with the singleton instance. This ensures that nobody can create another instance by serializing and deserializing the singleton.
>
> The `readResolve` method is called when `ObjectInputStream` has read an object from the stream and is preparing to return it to the caller. `ObjectInputStream` checks whether the class of the object defines the `readResolve` method. If the method is defined, the `readResolve` method is called to allow the object in the stream to designate the object to be returned. The object returned should be of a type that is compatible with all uses. If it is not compatible, a `ClassCastException` will be thrown when the type mismatch is discovered.
>
> Item 90, Effective Java, 3rd Ed covers `readResolve` and `writeReplace` for serial proxies - their main use. The examples do not write out `readObject` and `writeObject` methods because they are using default serialisation to read and write fields.
>
> `readResolve` is called after `readObject` has returned (conversely `writeReplace` is called before `writeObject` and probably on a different object). The object the method returns replaces `this`object returned to the user of `ObjectInputStream.readObject` and any further back references to the object in the stream. Both `readResolve` and `writeReplace` may return objects of the same or different types. Returning the same type is useful in some cases where fields must be `final` and either backward compatibility is required or values must copied and/or validated.
>
> Use of `readResolve` does not enforce the singleton property.
>
> ......
>
> 源于：https://stackoverflow.com/questions/1168348/java-serialization-readobject-vs-readresolve/24459741

按照我的理解来讲就是当`ObjectInputStream`读到一个对象时，会检查这个对象的类是否定义了`readResolve`方法，如果定义了该方法，那么会将该方法的返回值作为对象返回，而不是返回`ObjectInputStream`在流中读到的对象，这样就可以强制实现单例模式。但是如果流中读到的对象和`readResolve`方法返回的对象类型不兼容，那么就会抛出`ClassCastException`。

## 主要方法

### regionMatches

`regionMatches`功能是判断两个字符串指定区间、且具有相同长度的子串是否相等，它有两个重载的方法：

```java
public boolean regionMatches(int toffset, String other, int ooffset, int len);
public boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)
```

它们的唯一区别是前者大小写敏感，后者可以指定是否忽略大小写。只有当一下某一个条件满足（即返回值为true）时，该方法的结果是false，即两个子串不相等：

- 下标越界：toffset<0、ooffset<0、toffset+len>this.length()、ooffset+len>other.length()
- 下标不越界情况下，两个子串的某一个位置对应的字符不相等

由于两个重载的方法中后者的功能涵盖了前者，因此我们只看后者的源码：

```java
public boolean regionMatches(boolean ignoreCase, int toffset,
                             String other, int ooffset, int len) {
    char ta[] = value;
    int to = toffset;
    char pa[] = other.value;
    int po = ooffset;
    // Note: toffset, ooffset, or len might be near -1>>>1.
    if ((ooffset < 0) || (toffset < 0)
            || (toffset > (long)value.length - len)
            || (ooffset > (long)other.value.length - len)) {
        return false;
    }
    while (len-- > 0) {
        char c1 = ta[to++];
        char c2 = pa[po++];
        if (c1 == c2) {
            continue;
        }
        if (ignoreCase) {
            // If characters don't match but case may be ignored,
            // try converting both characters to uppercase.
            // If the results match, then the comparison scan should
            // continue.
            char u1 = Character.toUpperCase(c1);
            char u2 = Character.toUpperCase(c2);
            if (u1 == u2) {
                continue;
            }
            // Unfortunately, conversion to uppercase does not work properly
            // for the Georgian alphabet, which has strange rules about case
            // conversion.  So we need to make one last check before
            // exiting.
            if (Character.toLowerCase(u1) == Character.toLowerCase(u2)) {
                continue;
            }
        }
        return false;
    }
    return true;
}
```

转成大写再转小写这个做法在前面的部分`CaseInsensitiveComparator`中已经提到了，道理是类似的，我们来看看这个地方：

```java
// Note: toffset, ooffset, or len might be near -1>>>1.
if ((ooffset < 0) || (toffset < 0)
         || (toffset > (long)value.length - len)
         || (ooffset > (long)other.value.length - len)) {
     return false;
}
```

这个地方的处理比较精妙，`-1>>>1`等于2147483647，即int类型的上界，它考虑到了toffset和ooffset的值有可能是很靠近甚至等于这个上界的，如果使用`toffset + len > (long)value.length`来判断的话，有可能出现`toffset + len`溢出的情况，而源码中的写法就能很好地解决了这个问题。

### length

直接返回内部字符数组的长度

```java
public int length() {
	return value.length;
}
```

### isEmpty

```java
public boolean isEmpty() {
    return value.length == 0;
}
```

### charAt

返回String类内部字符数组value在参数索引处的字符，如果该位置的字符是高低代理项，那么它返回的字符也是高低代理项的值

```java
public char charAt(int index) {
    if ((index < 0) || (index >= value.length)) {
        throw new StringIndexOutOfBoundsException(index);
    }
    return value[index];
}
```

### codePointAt

这个方法和`charAt`不同的地方在于，它返回的是int型的Unicode代码点，因此他会有两种情况：

- index位置的字符是BMP的话，则直接返回index位置的字符对应的int值
- index位置的字符是在高代理项的范围内，如果index+1<value.length，而且index+1位置的字符在低代理项范围内，那么将会返回(index, index+1)这对字符对应的Unicode增补代码点

```java
public int codePointAt(int index) {
    if ((index < 0) || (index >= value.length)) {
        throw new StringIndexOutOfBoundsException(index);
    }
    return Character.codePointAtImpl(value, index, value.length);
}
```

### codePointBefore

返回参数index位置之前的Unicode代码点，因此它会有如下情况：

- index-1位置的字符在低代理项范围内，且index-2是非负数，那么返回(index-2, index-1)对应的Unicode代码点
- index-1位置的字符是没有配对的高代理项（可能的情况是index-1和index位置的字符是配对的）或者低代理项（*这个不是很理解*）的话，则直接返回index-1位置字符的int值，不作转换

```java
public int codePointBefore(int index) {
    int i = index - 1;
    if ((i < 0) || (i >= value.length)) {
        throw new StringIndexOutOfBoundsException(index);
    }
    return Character.codePointBeforeImpl(value, index, 0);
}
```

### codePointCount

计算`[beginIndex, endIndex)`范围内的子串的代码点个数，怎么计算的很容易，只需要注意的是，对于该范围内的子串，如果有字符是没有配对的代理项，那么它也当成一个代码点来计数

```java
public int codePointCount(int beginIndex, int endIndex) {
    if (beginIndex < 0 || endIndex > value.length || beginIndex > endIndex) {
        throw new IndexOutOfBoundsException();
    }
    return Character.codePointCountImpl(value, beginIndex, endIndex - beginIndex);
}
```

### offsetByCodePoints

计算从index位置开始，偏移codePointOffset个代码点后的索引位置，注意，这个codePointOffset可以是负数，这种情况下是往左偏移；同理，如果遇到不匹配的代理项，也是当成一个代码点来计数

```java
public int offsetByCodePoints(int index, int codePointOffset) {
    if (index < 0 || index > value.length) {
        throw new IndexOutOfBoundsException();
    }
    return Character.offsetByCodePointsImpl(value, 0, value.length,
            index, codePointOffset);
}
```

### getChars

该方法的主要功能是将字符串读取成字符数组，默认修饰符的`getChars`是没有任何边界参数检测的，原因很简单，因为它只在类库内部才能使用（concat方法），按照契约式编程原则是不会有意外情况发生的；public修饰的方法，它会对参数进行边界条件检测，用于将指定范围（srcBegin、srcEnd）的子串复制到字符数组参数dst中

```java
void getChars(char dst[], int dstBegin) {
    System.arraycopy(value, 0, dst, dstBegin, value.length);
}

public void getChars(int srcBegin, int srcEnd, char dst[], int dstBegin) {
    if (srcBegin < 0) {
        throw new StringIndexOutOfBoundsException(srcBegin);
    }
    if (srcEnd > value.length) {
        throw new StringIndexOutOfBoundsException(srcEnd);
    }
    if (srcBegin > srcEnd) {
        throw new StringIndexOutOfBoundsException(srcEnd - srcBegin);
    }
    System.arraycopy(value, srcBegin, dst, dstBegin, srcEnd - srcBegin);
}
```

### getBytes

获取字符串指定编码的字节数组，比如 charsetName 为 utf8，则将字符串转为 utf8 编码后对应的字节数组。如果不传参数则使用 JVM 默认编码，即`Charset.defaultCharset()`

```java
public byte[] getBytes(String charsetName)
        throws UnsupportedEncodingException {
    if (charsetName == null) throw new NullPointerException();
    return StringCoding.encode(charsetName, value, 0, value.length);
}

public byte[] getBytes(Charset charset) {
    if (charset == null) throw new NullPointerException();
    return StringCoding.encode(charset, value, 0, value.length);
}

public byte[] getBytes() {
    return StringCoding.encode(value, 0, value.length);
}
```

### equals

用来判断两个字符串的内容是否相同，但是仔细看可以发现，`equals`方法的参数其实是`Object`类型的，其实道理很简单，`String`类的`equals`方法是直接继承自`Object`类的，属于方法重写，参数个数和类型要相同，`Object`中的`equals`方法参数就是`Object`类型，该方法的实现思路如下：

- 首先判断是不是同一个对象，如果是则返回`true`
- 判断参数`anObject`是否是`String`类型（防御式编程），如果不是，则直接返回`false`
- 最后再逐个字符进行比较，因为这是在`String`类内部，可以直接访问`value`

```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

### contentEquals

`contentEquals`的功能和`equals`差不多，都是比较字符串的内容是否相同，区别在于它的参数是`StringBuffer`或者`CharSequence`，适用范围更广。

`AbstractStringBuilder`是`StringBuffer`和`StringBuilder`的公共父类，前者是线程安全的，后者是非线程安全的，因此需要分别对他们进行处理，`StringBuffer`需要加上`synchronized`同步块；注意到，无论是`StringBuffer`还是`StringBuilder`，cs都被强制转换为`StringBuilder`，最后调用`nonSyncContentEquals`方法来进行内容比较。

不同类型的字符串内容的比较算法都差不多，基本上都是先比较长度是否相同，这样可以避免不必要的循环遍历比较，如果相同再逐个字符比较

```java
public boolean contentEquals(StringBuffer sb) {
    return contentEquals((CharSequence)sb);
}

private boolean nonSyncContentEquals(AbstractStringBuilder sb) {
    char v1[] = value;
    char v2[] = sb.getValue();
    int n = v1.length;
    if (n != sb.length()) {
        return false;
    }
    for (int i = 0; i < n; i++) {
        if (v1[i] != v2[i]) {
            return false;
        }
    }
    return true;
}

public boolean contentEquals(CharSequence cs) {
    // Argument is a StringBuffer, StringBuilder
    if (cs instanceof AbstractStringBuilder) {
        if (cs instanceof StringBuffer) {
            synchronized(cs) {
                return nonSyncContentEquals((AbstractStringBuilder)cs);
            }
        } else {
            return nonSyncContentEquals((AbstractStringBuilder)cs);
        }
    }
    // Argument is a String
    if (cs instanceof String) {
        return equals(cs);
    }
    // Argument is a generic CharSequence
    char v1[] = value;
    int n = v1.length;
    if (n != cs.length()) {
        return false;
    }
    for (int i = 0; i < n; i++) {
        if (v1[i] != cs.charAt(i)) {
            return false;
        }
    }
    return true;
}
```

### equalsIgnoreCase

判断两个字符串在忽略大小写情况下内容是否相同，遇到大小写就应该想到个别字母的大小写转换规则比较特殊，参见`CaseInsensitiveComparator`部分具体说明，因此对应位置两个字符是否相同条件是以下其中一个：

- 直接`==`比较返回true
- `Character.toUpperCase`转换成大写字母后`==`比较相同
- `Character.toLowerCase`转换成小写字母后`==`比较相同

当然，这部分的判断直接调用了内部的方法`regionMatches`进行比较了。

这个方法首先判断两个字符串是否是同一个对象，是则返回true，否则再判断参数字符串是否是null（防御式编程），然后再判断两个字符串长度是否相同，最后再比较字符串内容。

```java
public boolean equalsIgnoreCase(String anotherString) {
    return (this == anotherString) ? true
            : (anotherString != null)
            && (anotherString.value.length == value.length)
            && regionMatches(true, 0, anotherString, 0, value.length);
}
```

