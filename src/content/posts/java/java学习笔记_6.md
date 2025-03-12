---
title: java学习笔记（6）
published: 2025-03-12
description: 'Java学习之路'
image: './img/Alice_5.jpg'
tags: [java, 学习, 注解, ]
category: 'java'
draft: false 
---

## 注解

Java注解（Annotation）是一种用于提供元数据（metadata）的机制，它允许我们在代码中嵌入补充信息，这些信息可以在编译时、运行时或部署时被读取和使用。注解不会改变程序的运行逻辑，但可以改变程序的处理方式。
### 注解的基本概念
- **注解定义**：注解是通过`@interface`关键字定义的。
- **注解使用**：注解可以用于类、方法、字段、参数、局部变量等位置。
- **注解Retention**：注解的保留策略，指定注解在什么时期有效（源码阶段、编译阶段、运行时）。
- **注解Target**：注解的目标，指定注解可以用于哪些元素。
### 内置注解
Java提供了几种内置注解：
- `@Override`：表示一个方法声明旨在重写超类中的另一个方法。
- `@Deprecated`：表示不推荐使用该元素，通常是因为它很危险或存在更好的选择。
- `@SuppressWarnings`：表示在编译期间忽略指定的警告。
### 自定义注解
可以创建自定义注解来满足特定需求：
```java
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.TYPE})
public @interface MyAnnotation {
    String value() default "default";
    int count() default 0;
}
```
### 注解元素
注解可以包含元素，类似于方法：
- **元素类型**：可以是基本类型、String、Class、枚举、注解或这些类型的数组。
- **默认值**：可以为元素提供默认值。
### 注解的使用
```java
@MyAnnotation(value = "example", count = 10)
public class MyClass {
    @MyAnnotation
    public void myMethod() {
    }
}
```
### 注解处理
注解本身不包含行为，它们的行为是通过注解处理器来实现的。注解处理器可以在编译时或运行时读取注解并执行相应操作。
- **编译时处理**：使用注解处理器（Annotation Processor）在编译时读取注解并生成代码或执行检查。
- **运行时处理**：通过反射在运行时读取注解并执行操作。
### 示例：运行时注解处理
```java
public class AnnotationProcessor {
    public static void main(String[] args) throws NoSuchMethodException {
        MyClass obj = new MyClass();
        if (obj.getClass().isAnnotationPresent(MyAnnotation.class)) {
            MyAnnotation annotation = obj.getClass().getAnnotation(MyAnnotation.class);
            System.out.println("Value: " + annotation.value());
            System.out.println("Count: " + annotation.count());
        }
    }
}
```
### 注解的优点
- **提供元数据**：为代码提供额外的信息，而不改变代码本身。
- **简化代码**：减少编写重复代码的需要。
- **增强功能**：通过注解可以实现一些特殊功能，如依赖注入、权限控制等。
### 注解的局限性
- **性能影响**：运行时注解处理可能影响性能。
- **复杂性**：过度使用注解可能导致代码复杂度增加。
注解是Java中一个非常强大和灵活的特性，它可以用于各种场景，如框架开发、代码生成、验证等。正确使用注解可以大大提高开发效率和代码质量。



## FunctionalInterface

在Java中，`FunctionalInterface`是一个用于标注接口的注解，它表示该接口是一个函数式接口。函数式接口是Java 8引入的一个新概念，它是指只包含一个抽象方法的接口。这样的接口可以用来表示lambda表达式或者方法引用。
### FunctionalInterface的特性
1. **单一抽象方法**：函数式接口只能有一个抽象方法。但是它可以包含多个默认方法（default method）和静态方法（static method）。
2. **允许继承方法**：如果接口继承了另一个接口的抽象方法，那么这个方法不会计入抽象方法的总数内。
3. **Object类的方法**：函数式接口可以包含Object类的方法，如`toString()`、`equals()`等，这些方法不会计入抽象方法的总数内。
### FunctionalInterface的使用
使用`@FunctionalInterface`注解可以确保接口满足函数式接口的定义。如果接口中定义了超过一个抽象方法，编译器将会报错。
```java
@FunctionalInterface
public interface SimpleFunction {
    // 单一抽象方法
    int apply(int value);
    // 默认方法，不会影响FunctionalInterface的单一抽象方法规则
    default void printValue(int value) {
        System.out.println(value);
    }
    // 静态方法，同样不会影响
    static void printMessage(String message) {
        System.out.println(message);
    }
    // Object类的方法，不会计入抽象方法总数
    @Override
    String toString();
}
```
### FunctionalInterface的好处
1. **保证接口的函数式特性**：通过编译器检查，确保接口只有一个抽象方法，适合用于lambda表达式。
2. **提高代码可读性**：使用`@FunctionalInterface`注解的接口，其目的和用法更加明确，提高了代码的可读性。
3. **支持泛型**：函数式接口可以定义泛型方法，增加了接口的灵活性和适用性。
### 常见的FunctionalInterface
Java 8的`java.util.function`包中定义了许多常用的函数式接口，如：
- `Predicate<T>`：用于表示一个参数的布尔表达式。
- `Consumer<T>`：表示一个接受一个参数并且没有返回值的操作。
- `Function<T, R>`：表示一个接受一个参数并且返回结果的操作。
- `Supplier<T>`：表示一个没有参数但是返回结果的操作。
- `UnaryOperator<T>`：表示一个接受一个参数并且返回相同类型结果的操作。
- `BinaryOperator<T>`：表示一个接受两个相同类型的参数并且返回相同类型结果的操作。
### 示例
```java
import java.util.function.Function;
public class FunctionalInterfaceExample {
    public static void main(String[] args) {
        // 使用lambda表达式实现Function接口
        Function<Integer, Integer> square = x -> x * x;
        System.out.println(square.apply(5)); // 输出25
    }
}
```
在这个示例中，我们定义了一个`Function<Integer, Integer>`类型的变量`square`，它接受一个`Integer`类型的参数并返回一个`Integer`类型的结果。我们使用lambda表达式`x -> x * x`来表示这个函数，它将输入的数平方后返回。
通过`@FunctionalInterface`，Java提供了一种简洁、高效的方式来表示和操作函数式编程中的函数。这在Java 8及以后的版本中极大地增强了Java语言的表达能力和灵活性。

## ArrayList初始化

在Java中，`ArrayList` 是一个常用的动态数组实现，它允许你存储和操作对象。当你想要在定义时给 `ArrayList` 赋值，你可以使用多种方法，包括使用匿名内部类、使用双括号初始化（注意可能带来的潜在问题）或者使用Java 9及以上版本的集合工厂方法。
以下是一些示例：
### 1. 使用匿名内部类
```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
public class Main {
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>() {{
            add("Apple");
            add("Banana");
            add("Cherry");
        }};
        System.out.println(list);
    }
}
```
### 2. 使用双括号初始化（不推荐用于多线程环境）
```java
import java.util.ArrayList;
import java.util.List;
public class Main {
    public static void main(String[] args) {
        List<String> list = new ArrayList<String>() {{
            add("Apple");
            add("Banana");
            add("Cherry");
        }};
        System.out.println(list);
    }
}
```
**注意**：双括号初始化会创建一个匿名内部类，这可能会导致内存泄漏或者在序列化时出现问题。
### 3. 使用Java 9的集合工厂方法
如果你使用的是Java 9或更高版本，你可以使用`List.of()`方法来创建一个不可变的列表：
```java
import java.util.List;
public class Main {
    public static void main(String[] args) {
        List<String> list = List.of("Apple", "Banana", "Cherry");
        System.out.println(list);
    }
}
```
**注意**：`List.of()`创建的列表是不可变的，即不能添加、删除或替换元素。
### 4. 使用Arrays.asList（注意返回的列表是固定大小的）
```java
import java.util.Arrays;
import java.util.List;
public class Main {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("Apple", "Banana", "Cherry");
        System.out.println(list);
    }
}
```
**注意**：`Arrays.asList()`返回的列表是固定大小的，不支持添加或删除操作，但可以修改元素。

## HashMap初始化

**是的，HashMap也有类似的写法**。在Java中，HashMap是一个常用的键值对存储结构，你可以在定义时给它赋值。以下是一些示例：
### 1. 使用匿名内部类
```java
import java.util.HashMap;
import java.util.Map;
public class Main {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<String, String>() {{
            put("key1", "value1");
            put("key2", "value2");
            put("key3", "value3");
        }};
        System.out.println(map);
    }
}
```
### 2. 使用双括号初始化（不推荐用于多线程环境）
```java
import java.util.HashMap;
import java.util.Map;
public class Main {
    public static void main(String[] args) {
        Map<String, String> map = new HashMap<String, String>() {{
            put("key1", "value1");
            put("key2", "value2");
            put("key3", "value3");
        }};
        System.out.println(map);
    }
}
```
### 3. 使用Java 9及以上的集合工厂方法
```java
import java.util.Map;
public class Main {
    public static void main(String[] args) {
        Map<String, String> map = Map.of(
            "key1", "value1",
            "key2", "value2",
            "key3", "value3"
        );
        System.out.println(map);
    }
}
```
注意：`Map.of()`创建的映射是不可变的，即不能添加、删除或替换键值对。
### 4. 使用Stream API（Java 8及以上）
```java
import java.util.HashMap;
import java.util.Map;
import java.util.stream.Collectors;
import java.util.stream.Stream;
public class Main {
    public static void main(String[] args) {
        Map<String, String> map = Stream.of(
            new AbstractMap.SimpleEntry<>("key1", "value1"),
            new AbstractMap.SimpleEntry<>("key2", "value2"),
            new AbstractMap.SimpleEntry<>("key3", "value3")
        ).collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
        System.out.println(map);
    }
}
```
### 5. 使用Collections.unmodifiableMap（创建不可变映射）
```java
import java.util.Collections;
import java.util.HashMap;
import java.util.Map;
public class Main {
    public static void main(String[] args) {
        Map<String, String>modifiableMap = new HashMap<>();
        modifiableMap.put("key1", "value1");
        modifiableMap.put("key2", "value2");
        modifiableMap.put("key3", "value3");
        Map<String, String> map = Collections.unmodifiableMap(modifiableMap);
        System.out.println(map);
    }
}
```
注意：`Collections.unmodifiableMap()`创建的映射是不可变的，即不能添加、删除或替换键值对。


方法二（双括号初始化）不推荐在多线程环境中使用的原因主要与Java的匿名内部类和实例变量的初始化顺序有关。以下是详细解释：
#### 1. 匿名内部类的实例变量
当你使用双括号初始化时，实际上是在创建一个匿名内部类的实例。这个匿名内部类会捕获外部类的实例变量（如果有的话），并且在内部类中创建一个新的HashMap实例。
#### 2. 实例变量的初始化顺序
在Java中，实例变量的初始化顺序是先执行父类的构造方法，然后是子类的成员变量初始化，最后是子类的构造方法。当你在一个类的构造方法中使用双括号初始化时，这个初始化过程可能会在这个类的实例完全构造完成之前开始。
#### 3. 多线程环境中的问题
在多线程环境中，如果多个线程同时访问同一个尚未完全构造的对象，就可能导致以下问题：
- **可见性**：一个线程对共享变量的修改可能对其他线程不可见。
- **竞态条件**：多个线程同时读取和修改共享数据，导致不可预测的结果。
由于双括号初始化涉及到捕获外部类的实例变量，如果在多线程环境中使用，就可能遇到上述问题。特别是当这个HashMap被多个线程共享时，就可能导致数据不一致或其他并发问题。
#### 4. 示例
以下是一个简化的示例，说明为什么双括号初始化在多线程环境中可能有问题：
```java
public class SomeClass {
    private Map<String, String> map = new HashMap<String, String>() {{
        put("key1", "value1");
        put("key2", "value2");
    }};
    public SomeClass() {
        // 构造方法中的其他初始化代码
    }
    public Map<String, String> getMap() {
        return map;
    }
}
```
在这个示例中，如果多个线程在`SomeClass`的实例完全构造完成之前就调用`getMap()`方法，它们可能会看到一个未完全初始化的`map`。

为了避免这些潜在的问题，推荐在多线程环境中使用更明确和线程安全的方法来初始化HashMap，比如方法一（使用匿名内部类但不使用双括号）或其他线程安全的集合初始化方法。这样可以确保在多线程环境中对共享数据的访问是安全的。

## 不可变集合

不可变集合是一个长度不可变，内容也无法修改的集合

### 使用场景

如果某个数据不能被修改，把它防御性地拷贝到不可变集合中是个很好的实践。
当集合对象被不可信的库调用时，不可变形式是安全的。

### 不可变的list集合

```java
List<String> list = List.of("张三", "李四", "王五", "赵六");
System.out.println("---------------------------");
for (String s : list) {
    System.out.println(s);
}
System.out.println("---------------------------");
Iterator<String> it = list.iterator();
while(it.hasNext()){
    String s = it.next();
    System.out.println(s);
}
System.out.println("---------------------------");
for (int i = 0; i < list.size(); i++) {
    String s = list.get(i);
    System.out.println(s);
}
System.out.println("---------------------------");
//list.remove("李四");
//list.add("aaa");
list.set(0,"aaa");
```

### 不可变的Set集合

```java
Set<String> set = Set.of("张三", "张三", "李四", "王五", "赵六");
for (String s : set) {
    System.out.println(s);
}
System.out.println("-----------------------");
Iterator<String> it = set.iterator();
while(it.hasNext()){
    String s = it.next();
    System.out.println(s);
}
System.out.println("-----------------------");
//set.remove("王五");
```

### 不可变的Map集合

#### 键值对个数小于等于10

```java
public class ImmutableDemo3 {
    public static void main(String[] args) {
       /*
        创建Map的不可变集合
            细节1：
                键是不能重复的
            细节2：
                Map里面的of方法，参数是有上限的，最多只能传递20个参数，10个键值对
            细节3：
                如果我们要传递多个键值对对象，数量大于10个，在Map接口中还有一个方法
        */

        //一旦创建完毕之后，是无法进行修改的，在下面的代码中，只能进行查询操作
        Map<String, String> map = Map.of("张三", "南京", "张三", "北京", "王五", "上海", "赵六", "广州", "孙七", "深圳", "周八", "杭州", "吴九", "宁波", "郑十", "苏州", "刘一", "无锡", "陈二", "嘉兴");
        Set<String> keys = map.keySet();
        for (String key : keys) {
            String value = map.get(key);
            System.out.println(key + "=" + value);
        }
        System.out.println("--------------------------");
        Set<Map.Entry<String, String>> entries = map.entrySet();
        for (Map.Entry<String, String> entry : entries) {
            String key = entry.getKey();
            String value = entry.getValue();
            System.out.println(key + "=" + value);
        }
        System.out.println("--------------------------");
    }
}
```

#### 键值对个数大于10

```java
public class ImmutableDemo4 {
    public static void main(String[] args) {

        /*
            创建Map的不可变集合,键值对的数量超过10个
        */

        //1.创建一个普通的Map集合
        HashMap<String, String> hm = new HashMap<>();
        hm.put("张三", "南京");
        hm.put("李四", "北京");
        hm.put("王五", "上海");
        hm.put("赵六", "北京");
        hm.put("孙七", "深圳");
        hm.put("周八", "杭州");
        hm.put("吴九", "宁波");
        hm.put("郑十", "苏州");
        hm.put("刘一", "无锡");
        hm.put("陈二", "嘉兴");
        hm.put("aaa", "111");

        //2.利用上面的数据来获取一个不可变的集合
/*
        //获取到所有的键值对对象（Entry对象）
        Set<Map.Entry<String, String>> entries = hm.entrySet();
        //把entries变成一个数组
        Map.Entry[] arr1 = new Map.Entry[0];
        //toArray方法在底层会比较集合的长度跟数组的长度两者的大小
        //如果集合的长度 > 数组的长度 ：数据在数组中放不下，此时会根据实际数据的个数，重新创建数组
        //如果集合的长度 <= 数组的长度：数据在数组中放的下，此时不会创建新的数组，而是直接用
        Map.Entry[] arr2 = entries.toArray(arr1);
        //不可变的map集合
        Map map = Map.ofEntries(arr2);
        map.put("bbb","222");*/


        //Map<Object, Object> map = Map.ofEntries(hm.entrySet().toArray(new Map.Entry[0]));

        Map<String, String> map = Map.copyOf(hm);
        map.put("bbb","222");
    }
}
```