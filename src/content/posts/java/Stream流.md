---
title: Stream API
published: 2025-03-12
description: 'Stream API的简单用法'
image: './img/20250222200704_1.jpg'
tags: [java, 学习, 流, stream]
category: 'java'
draft: false 
---

Java 8引入了Stream API，这是一个用于处理集合的强大工具，它可以让你以声明式的方式处理数据。`List`是Java集合框架中的一个接口，表示一个有序的集合（也称为序列）。当你有一个`List`并且想要使用Stream来处理它时，你可以利用Stream API提供的各种操作来进行转换、过滤、排序、聚合等操作。

### 创建Stream
从`List`创建Stream非常简单，只需要调用`stream()`方法即可：
```java
List<String> list = Arrays.asList("apple", "banana", "cherry");
Stream<String> stream = list.stream();
```
如果你想要并行处理，可以使用`parallelStream()`方法：
```java
Stream<String> parallelStream = list.parallelStream();
```
只有单列集合才有stream，双列集合需要进一步操作：

```java
HashMap<String, Integer> hm = new HashMap<>();
hm.keySet().stream();
hm.entrySet().stream;
```

数组获取stream：

```java
int[] arr = {1, 2, 3, 4, 5, 6};
Arrays.stream(arr);
```

同时，stream中的静态方法of可以针对一些零散的数据获取stream流。

```java
Stream.of(1, 2, 3, 4, 5, 6);
Srream.of(arr); // 可变参类型，如果传递的是数组，那么数组必须是引用类型
```



### 中间操作

中间操作会返回一个新的Stream，允许操作链继续下去。常见的中间操作包括：

| 方法名                                          | 说明                                                       |
| ----------------------------------------------- | ---------------------------------------------------------- |
| Stream<T> filter(Predicate predicate)           | 用于对流中的数据进行过滤                                   |
| Stream<T> limit(long maxSize)                   | 返回此流中的元素组成的流，截取前指定参数个数的数据         |
| Stream<T> skip(long n)                          | 跳过指定参数个数的数据，返回由该流的剩余元素组成的流       |
| static <T> Stream<T> concat(Stream a, Stream b) | 合并a和b两个流为一个流                                     |
| Stream<T> distinct()                            | 返回由该流的不同元素（根据Object.equals(Object) ）组成的流 |
| Stream<T> map(Function<T, R>)                   | 转换元素。                                                 |
|                                                 |                                                            |

例如，过滤出长度大于4的字符串，并将其转换为大写：

```java
List<String> result = list.stream()
                          .filter(s -> s.length() > 4)
                          .map(String::toUpperCase)
                          .collect(Collectors.toList());
```
### 终端操作
终端操作会触发Stream的执行，并产生一个结果或副作用。常见的终端操作包括：
- `collect(Collector<T, A, R>)`：收集元素到另一个集合中。
- `reduce(T, BinaryOperator<T>)`：将元素聚合为一个值。
- `forEach(Consumer<T>)`：对每个元素执行操作。
- `count()`：返回元素数量。
- `findFirst()` / `findAny()`：返回第一个或任意一个元素。
- `anyMatch(Predicate<T>)` / `allMatch(Predicate<T>)` / `noneMatch(Predicate<T>)`：检查是否有元素匹配给定条件。
例如，计算所有字符串长度的总和：
```java
int totalLength = list.stream()
                      .mapToInt(String::length)
                      .sum();
```
### 短路操作
短路操作可以提前结束Stream的遍历。例如：
- `anyMatch(Predicate<T>)`：只要有一个元素匹配就返回true，不再继续检查。
- `findFirst()`：找到第一个元素就返回，不再继续查找。
### 使用示例
下面是一个完整的示例，展示了如何使用Stream来处理一个字符串列表：
```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;
public class StreamExample {
    public static void main(String[] args) {
        List<String> fruits = Arrays.asList("apple", "banana", "cherry", "date", "fig", "grape");
        // 过滤出长度大于4的单词，转换为大写，并排序
        List<String> processedFruits = fruits.stream()
                                             .filter(fruit -> fruit.length() > 4)
                                             .map(String::toUpperCase)
                                             .sorted()
                                             .collect(Collectors.toList());
        // 输出结果
        processedFruits.forEach(System.out::println);
    }
}
```
这个示例中，我们首先过滤出长度大于4的单词，然后将它们转换为大写字母，接着对结果进行排序，并最终收集到一个新的列表中。
### 注意事项
- Stream只能被消费一次，一旦进行了终端操作，就不能再使用这个Stream了。
- Stream操作不会修改原始数据源，它们是惰性的，只有在终端操作时才会执行。
- 并行Stream可能会提高性能，但也可能导致线程安全问题，需要谨慎使用。
通过Stream API，你可以以更简洁、更声明式的方式处理集合数据，这使得代码更易于理解和维护。