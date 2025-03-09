---
title: Java 排序
published: 2025-03-09
description: 'Java学习之路'
image: './img/fn.png'
tags: [java, 学习]
category: 'java'
draft: false 
---

## Java排序
在Java中，`Collections` 类的 `sort()` 方法用于对列表进行排序，而 `Arrays` 类的 `sort()` 方法用于对数组进行排序。如果你想要对自定义对象数组或列表进行排序，你需要确保这些对象实现了 `Comparable` 接口。
以下是如何手动实现 `Comparable` 接口的步骤：

1. **定义自定义类**：首先，定义一个自定义类，比如 `Person`。
2. **实现 `Comparable` 接口**：在自定义类中实现 `Comparable` 接口，并指定比较的对象类型。
3. **覆盖 `compareTo` 方法**：实现 `compareTo` 方法，定义对象的比较逻辑。
下面是一个示例，展示了如何为一个 `Person` 类实现 `Comparable` 接口，以便根据 `Person` 的 `age` 属性进行排序：
```java
public class Person implements Comparable<Person> {
    private String name;
    private int age;
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public int getAge() {
        return age;
    }
    @Override
    public int compareTo(Person other) {
        // 根据年龄升序排序
        return this.age - other.age;
    }
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + '}';
    }
}
```
在这个例子中，`compareTo` 方法根据 `Person` 对象的 `age` 属性进行比较。如果需要降序排序，可以修改 `compareTo` 方法中的返回值为 `other.age - this.age`。
现在，你可以使用 `Arrays.sort()` 或 `Collections.sort()` 方法对 `Person` 对象数组或列表进行排序：
```java
import java.util.Arrays;
import java.util.List;
public class Main {
    public static void main(String[] args) {
        Person[] people = {
            new Person("Alice", 30),
            new Person("Bob", 25),
            new Person("Charlie", 35)
        };
        Arrays.sort(people);
        for (Person person : people) {
            System.out.println(person);
        }
        List<Person> personList = Arrays.asList(people);
        Collections.sort(personList);
        for (Person person : personList) {
            System.out.println(person);
        }
    }
}
```
在这个例子中，`Arrays.sort(people)` 和 `Collections.sort(personList)` 都会根据 `Person` 类中实现的 `compareTo` 方法进行排序。输出将显示根据年龄排序的 `Person` 对象。

## `Arrays`和`Collections`类中常用的一些API方法：
| 类名       | 方法签名                                             | 描述                                                         |
|------------|------------------------------------------------------|--------------------------------------------------------------|
| `Arrays`| `static void sort(int[] a)`                          | 对整型数组进行排序                                           |
|            | `static void sort(Object[] a)`                       | 对对象数组进行排序，要求对象实现`Comparable`接口             |
|            | `static void sort(T[] a, Comparator<? super T> c)`    | 使用指定的`Comparator`对对象数组进行排序                     |
|            | `static int binarySearch(int[] a, int key)`           | 在已排序的整型数组中使用二分搜索查找元素                     |
|            | `static int binarySearch(Object[] a, Object key)`     | 在已排序的对象数组中使用二分搜索查找元素，要求对象实现`Comparable`接口 |
|            | `static int binarySearch(T[] a, T key, Comparator<? super T> c)` | 使用指定的`Comparator`在已排序的对象数组中进行二分搜索       |
|            | `static void fill(int[] a, int val)`                 | 将指定的整数值填充到数组中                                   |
|            | `static void fill(Object[] a, Object val)`           | 将指定的对象引用填充到数组中                                 |
|            | `static boolean equals(int[] a, int[] a2)`            | 判断两个整型数组是否相等                                     |
|            | `static boolean equals(Object[] a, Object[] a2)`      | 判断两个对象数组是否相等                                     |
|            | `static String toString(int[] a)`                    | 返回数组的字符串表示形式                                     |
|            | `static String toString(Object[] a)`                  | 返回对象数组的字符串表示形式                                 |
|`Collections`| `static <T extends Comparable<? super T>> void sort(List<T> list)` | 对列表进行排序，要求列表中的元素实现`Comparable`接口       |
|            | `static <T> void sort(List<T> list, Comparator<? super T> c)` | 使用指定的`Comparator`对列表进行排序                         |
|            | `static int binarySearch(List<? extends Comparable<? super T>> list, T key)` | 在已排序的列表中使用二分搜索查找元素，要求元素实现`Comparable`接口 |
|            | `static int binarySearch(List<? extends T> list, T key, Comparator<? super T> c)` | 使用指定的`Comparator`在已排序的列表中进行二分搜索           |
|            | `static void reverse(List<?> list)`                  | 反转列表中元素的顺序                                         |
|            | `static void shuffle(List<?> list)`                  | 随机打乱列表中元素的顺序                                     |
|            | `static void swap(List<?> list, int i, int j)`        | 交换列表中两个位置上的元素                                   |
|            | `static <T> void fill(List<? super T> list, T obj)`   | 使用指定的对象填充列表                                       |
|            | `static <T> boolean replaceAll(List<T> list, T oldVal, T newVal)` | 替换列表中所有旧值为新值                                     |
|            | `static boolean addAll(Collection<? super T> c, T... elements)` | 将所有指定元素添加到集合中                                   |
|            | `static <T> T min(Collection<? extends T> coll)`      | 返回集合中的最小元素，要求元素实现`Comparable`接口         |
|            | `static <T> T min(Collection<? extends T> coll, Comparator<? super T> comp)` | 使用指定的`Comparator`返回集合中的最小元素                   |
|            | `static <T> T max(Collection<? extends T> coll)`      | 返回集合中的最大元素，要求元素实现`Comparable`接口         |
|            | `static <T> T max(Collection<? extends T> coll, Comparator<? super T> comp)` | 使用指定的`Comparator`返回集合中的最大元素                   |
|            | `static void rotate(List<?> list, int distance)`      | 将列表中的元素循环移动指定的距离                             |
`Arrays`类主要用于数组操作，而`Collections`类主要用于集合框架中的列表、集和映射操作。在使用这些方法时，需要确保传入的参数符合方法的要求，例如数组或列表已经排序、元素类型正确等。

## Comparator
在Java中，`Comparator`接口用于定义对象的比较逻辑，尤其是在需要对某个类的对象进行排序，但该类没有实现`Comparable`接口，或者需要按照不同的排序逻辑进行排序时。实现`Comparator`接口需要创建一个实现了`Comparator`接口的类，并覆盖其`compare`方法。
以下是如何实现`Comparator`接口的步骤：
1. 创建一个实现`Comparator`接口的类。
2. 覆盖`compare`方法，定义比较逻辑。
3. 在需要排序的地方使用这个`Comparator`。
下面是一个简单的示例，展示了如何为`Person`类实现一个`Comparator`来根据年龄进行排序：
```java
import java.util.Comparator;
// Person类
class Person {
    String name;
    int age;
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    @Override
    public String toString() {
        return name + ": " + age;
    }
}
// 实现Comparator接口的AgeComparator类
class AgeComparator implements Comparator<Person> {
    @Override
    public int compare(Person p1, Person p2) {
        return Integer.compare(p1.age, p2.age);
    }
}
// 主类
public class ComparatorExample {
    public static void main(String[] args) {
        Person[] people = {
            new Person("Alice", 30),
            new Person("Bob", 25),
            new Person("Charlie", 35)
        };
        // 使用Arrays.sort方法和AgeComparator来排序
        Arrays.sort(people, new AgeComparator());
        // 输出排序后的结果
        for (Person person : people) {
            System.out.println(person);
        }
    }
}
```
在这个示例中，`AgeComparator`类实现了`Comparator<Person>`接口，并覆盖了`compare`方法，该方法比较两个`Person`对象的年龄。在`main`方法中，我们使用`Arrays.sort`方法和`AgeComparator`实例来对`Person`数组进行排序。
`compare`方法的返回值应该是一个负数、零或正数，分别表示第一个参数小于、等于或大于第二个参数。
此外，从Java 8开始，你可以使用lambda表达式来简化`Comparator`的实现，如下所示：
```java
// 使用lambda表达式创建Comparator
Comparator<Person> ageComparator = (p1, p2) -> Integer.compare(p1.age, p2.age);
// 使用Arrays.sort方法和lambda表达式来排序
Arrays.sort(people, ageComparator);
```
这种方式更加简洁，特别适合于简单的比较逻辑。
