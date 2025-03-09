---
title: Java String 类
published: 2025-03-08
description: 'Java学习之路'
image: './img/Alice_2.PNG'
tags: [java, 学习]
category: 'java'
draft: false 
---
字符串广泛应用 在 Java 编程中，在 Java 中字符串属于对象，Java 提供了 String 类来创建和操作字符串。

### String 基础用法

> 创建String

```java
String a = "hello world!";
String b = new String("hello world!");
char[] chs = {'h', 'e', 'l', 'l', 'o'};
String c = new String(chs);
```

> 获取字符串长度

```java
int len = str.length();
```

> 连接字符串

```java
string1.concat(string2); // 返回 string2 连接 string1 的新字符串。
"hello," + " world" + "!";
```

### 常用API

> char charAt(int index);

- 返回指定索引处的 char 值。

> int compareTo(String anotherString)

- 字符串比较(字典序)。

> boolean startsWith(String prefix)

- 测试此字符串是否以指定的前缀开始。

> boolean endsWith(String suffix)

- 测试此字符串是否以指定的后缀结束。

> byte[] getBytes()

- 使用平台的默认字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。

> byte[] getBytes(String charsetName)

- 使用指定的字符集将此 String 编码为 byte 序列，并将结果存储到一个新的 byte 数组中。

> int indexOf(int ch)

- 返回指定字符在此字符串中第一次出现处的索引。

> int indexOf(int ch, int fromIndex)

- 返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索。

> int indexOf(String str)

- 返回指定子字符串在此字符串中第一次出现处的索引。

> int indexOf(String str,  int fromIndex)

- 返回指定子字符串在此字符串中第一次出现处的索引。

> int length()

- 返回此字符串的长度。

> String replace(char oldChar, char newChar)

- 返回一个新的字符串，它是通过用 newChar 替换此字符串中出现的所有 oldChar 得到的。

> String replaceAll(String regex, String replacement)

- 使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。

> String[] split(String regex)

- 根据给定正则表达式的匹配拆分此字符串。

> String substring(int beginIndex, int endIndex)

- 返回一个新字符串，它是此字符串的一个子字符串。

> char[] toCharArray()

- 将此字符串转换为一个新的字符数组。

> String toLowerCase()

- 使用默认语言环境的规则将此 String 中的所有字符都转换为小写。

> String toUpperCase()

- 使用默认语言环境的规则将此 String 中的所有字符都转换为大写。

> String trim()

- 返回字符串的副本，忽略前导空白和尾部空白。

> isEmpty()

- 判断字符串是否为空。

### String 基础特性

> 1. 不可变性

Java的`String`对象一旦创建，其内容不可修改。所有看似修改的操作（如拼接、替换）实际上是创建新的`String`对象。

```java
String s = "Hello";
s = s + " World"; // 创建新对象，原"Hello"仍存在内存中
```

> 2. 字符串常量池

Java通过字符串池优化内存，相同字面量的字符串会复用池中的对象（通过`intern()`方法可手动入池）。

```java
String s1 = "abc"; // 从池中获取
String s2 = new String("abc"); // 强制创建新对象
System.out.println(s1 == s2); // false（比较对象地址）
System.out.println(s1.equals(s2)); // true（比较内容）
```

> 3. 字符串比较

必须使用`equals()`而非`==`来比较内容，因为`==`比较的是对象地址。

### String 深入用法

> 1. 高效字符串操作
> 

- **StringBuilder**与**StringBuffer**
  频繁修改字符串时，使用`StringBuilder`（非线程安全）或`StringBuffer`（线程安全）避免频繁创建新对象。

	- ```java
      StringBuilder sb = new StringBuilder();
      sb.append("Hello").append(" World");
      String result = sb.toString(); // 高效拼接
      ```

- **字符串分割与正则表达式**
  使用`split()`和正则表达式处理复杂字符串：
  - ```java
    String str = "a,b,c";
    String[] parts = str.split(","); // ["a", "b", "c"]
    ```

> 2. 字符串格式化

- **String.format()**

  似C的`printf`，支持格式化输出：

  - ```java
    String formatted = String.format("Name: %s, Age: %d", "Alice", 30);
    ```

- **Java 15+ 的文本块（Text Blocks）**

  - ```java
    String json = """
                  {
                      "name": "Alice",
                      "age": 30
                  }
                  """;
    ```

> 3. 编码与字节转换

- ```java
  byte[] utf8Bytes = str.getBytes(StandardCharsets.UTF_8);
  String decoded = new String(utf8Bytes, StandardCharsets.UTF_8);
  ```


### 与C++ std::string和Python str的对比

> 1. 不可变性

- Java：`String`不可变，修改操作生成新对象。

- C++：`std::string`是可变的，直接修改原对象。

  ```
std::string s = "Hello";
  s += " World"; // 直接修改原对象
  ```

- Python：`str`不可变，类似Java，修改生成新对象。

> 2. 内存管理

- Java：依赖JVM垃圾回收，字符串池优化内存。
- C++：手动管理内存（RAII），`std::string`内部使用动态数组。
- Python：引用计数和垃圾回收，字符串驻留（类似池化）优化。

> **3. **常用操作

- 拼接效率
  - Java：推荐`StringBuilder`（`O(n)`复杂度）。
  - C++：直接使用`+=`或`append()`（动态数组扩容，高效）。
  - Python：推荐`join()`（高效处理列表拼接）。
- 切片与索引
  - Java：需用`substring(begin, end)`。
  - C++：`substr(pos, length)`。
  - Python：直接使用`s[start:end]`语法，最简洁。

> 4. Unicode支持

- Java：`String`内部为UTF-16，需注意代理对（Surrogate Pairs）。
- Python3：`str`默认支持Unicode，透明处理多语言文本。