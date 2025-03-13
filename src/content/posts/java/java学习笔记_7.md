---
title: java学习笔记（7）
published: 2025-03-13
description: 'Java学习之路'
image: './img/20250211215906_1.jpg'
tags: [java, 学习, 方法引用, 异常]
category: 'java'
draft: false 
---

## 方法引用

- 引用处需要是**函数式接口**
- 被引用的方法已经存在（可以由java提供，也可以是第三方）
- 被引用的方法的参数和返回值要和抽象方法的参数返回值一致
- 核心：lambda表达式改写

例子：排序

```java
public class sort {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<Integer>();
        Collections.addAll(list, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        Collections.sort(list, new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o2 - o1;
            }
        });
        System.out.println(list);
        Collections.shuffle(list);
        Collections.sort(list, (o1, o2) -> o2 - o1);
        System.out.println(list);
        Collections.shuffle(list);
        Collections.sort(list, sort::sub);
        System.out.println(list);
    }
    public static int sub(int a, int b) {
        return b-a;
    }
}
```

### 引用静态方法

- 格式：ClassName::StaticMethodName
- 例子：Integer::parseInt

```java
// list:{"1", "2", "3", "4"}
list.stream().map(Integer::parseInt).collect(Collectors.toList());
```

### 引用成员方法

- 格式：Object::MethodName
- this::name
- super::name

```java
list.stream().filter(new Judge()::judge).collect(Collectors.toList());
```

### 引用构造方法

- 格式：ClassName::new

```java
list.stream().map(Student::new).collect(Collectors.toList());
```



## 异常

异常继承自`Throwable`

> idea快捷键：Ctrl+Alt+T

| 方法名                        | 说明                              |
| ----------------------------- | --------------------------------- |
| public String getMessage()    | 返回此throwable的详细消息的字符串 |
| public String toString()      | 返回此throwable的简短描述         |
| public void printStackTrace() | 打印异常的错误信息                |

> printStackTrace()在系统底层使用System.err.println("")输出

### 捕获异常：

```java
try{
    ...
}catch{
	...    
}
```

### 抛出异常：

```java
public void function() throws Exception{
    throw new Exception();
}
```

- throws: 告诉使用者，可能抛出哪些异常
- throw：抛出异常

### 自定义异常

```java
public class MyException extends RuntimeException{
    public MyException(){};
    public MyException(String message){
        super(message);
    }
}
```

- 运行时异常：RuntimeException
- 编译时异常：Exception

## 字符编码

在Java中，String的编解码主要涉及到字符集（Charset）的概念，字符集决定了如何将字符（例如字母、数字、符号等）映射为字节序列，以及如何将字节序列映射回字符。Java中的String是以UTF-16编码存储的，但是我们可以使用不同的字符集对其进行编码和解码。
### 编码（Encoding）
编码是将String对象转换为字节序列的过程。在Java中，可以使用`String`类的`getBytes()`方法来进行编码，这个方法可以指定使用的字符集。
```java
String str = "你好，世界！";
byte[] bytes = str.getBytes("UTF-8"); // 使用UTF-8编码
```
在上面的例子中，`str`字符串被转换为字节序列`bytes`，使用的是UTF-8编码。如果不指定字符集，`getBytes()`方法会使用平台的默认字符集。
### 解码（Decoding）
解码是将字节序列转换回String对象的过程。在Java中，可以使用`String`类的构造方法来进行解码。
```java
byte[] bytes = ...; // 假设这是某种编码的字节序列
String str = new String(bytes, "UTF-8"); // 使用UTF-8解码
```
在上面的例子中，`bytes`字节序列被转换回字符串`str`，使用的是UTF-8编码。同样，如果不指定字符集，会使用平台的默认字符集。
### 常见字符集
- **UTF-8**：一种变长的编码方式，可以用1到4个字节表示一个字符，兼容ASCII码。
- **UTF-16**：Java内部使用的是UTF-16编码，用2或4个字节表示一个字符。
- **GBK**：中国的国家标准，兼容GB2312，用于中文编码。
- **ISO-8859-1**：西欧语言的编码字符集。
### 编解码中的常见问题
1. **乱码**：如果编码和解码时使用的**字符集不一致**或者**没有读完整个汉字**，就会出现乱码。
2. **字符集不支持**：尝试使用不支持的字符集进行编解码会抛出`UnsupportedEncodingException`。
### 示例
下面是一个完整的示例，展示了如何进行编码和解码：
```java
import java.io.UnsupportedEncodingException;
public class StringEncodingExample {
    public static void main(String[] args) {
        String originalString = "你好，世界！";
        try {
            // 编码
            byte[] utf8Bytes = originalString.getBytes("UTF-8");
            byte[] gbkBytes = originalString.getBytes("GBK");
            // 解码
            String utf8String = new String(utf8Bytes, "UTF-8");
            String gbkString = new String(gbkBytes, "GBK");
            // 输出结果
            System.out.println("原始字符串: " + originalString);
            System.out.println("UTF-8编码后解码: " + utf8String);
            System.out.println("GBK编码后解码: " + gbkString);
            // 错误的解码示例（会导致乱码）
            String wrongString = new String(utf8Bytes, "GBK");
            System.out.println("错误的解码: " + wrongString);
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        }
    }
}
```
在这个示例中，我们首先将原始字符串`originalString`使用UTF-8和GBK编码，然后分别使用相同的字符集进行解码，最后输出结果。我们还展示了一个错误的解码示例，即使用错误的字符集解码，这会导致乱码。