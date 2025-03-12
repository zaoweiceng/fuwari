---
title: Java数据结构
published: 2025-03-11
description: '数组、列表、集合、映射、栈、队列、堆'
image: './img/Alice_4.jpg'
tags: [java, 学习]
category: 'java'
draft: false 
---

## 数组（Arrays）

数组（Arrays）是一种基本的数据结构，可以存储固定大小的相同类型的元素。

```java
int[] array = new int[5];
```

- **特点：** 固定大小，存储相同类型的元素。
- **优点：** 随机访问元素效率高。
- **缺点：** 大小固定，插入和删除元素相对较慢。

## 列表（Lists）

Java 提供了多种列表实现，如 `ArrayList` 和 `LinkedList`。

```java
List<String> arrayList = new ArrayList<>();
List<Integer> linkedList = new LinkedList<>();
```

**ArrayList:**

ArrayList 类是一个可以动态修改的数组，与普通数组的区别就是它是没有固定大小的限制，可以添加或删除元素。

```java
import java.util.ArrayList; // 引入 ArrayList 类
ArrayList<E> objectName =new ArrayList<>();　 // 初始化
```

- **特点：** 动态数组，可变大小。

- **优点：** 高效的随机访问和快速尾部插入。

- **缺点：** 中间插入和删除相对较慢。

- 添加元素: add(obj)

- 访问元素: get(int index)

- 修改元素: set(int index, obj)

- 删除元素: remove(int index)

- 删除所有元素: removeAll(obj)

- 元素数量: size()

- 元素索引: indexOf(obj)

- 是否存在: contains(obj)

- 复制: clone()

- 清空: clear()

- 替换所有: replaceAll(UnaryOperator<E> operator

  - `sites.replaceAll(e -> e.toUpperCase());`
  - ` numbers.replaceAll(e -> e * 2);;`

- 删除满足条件元素: removeIf(Predicate<E> filter)

  - `sites.removeIf(e -> e.contains("Tao"));`
  - `numbers.removeIf(e -> (e % 2) == 0);`

- 遍历: forEach(Consumer<E> action)

  - ```java
    numbers.forEach((e) -> {
                e = e * 10;
                System.out.print(e + " ");
            });
    ```

**LinkedList:**

链表（Linked list）是一种常见的基础数据结构，是一种线性表，但是并不会按线性的顺序存储数据，而是在每一个节点里存到下一个节点的地址。

```java
// 引入 LinkedList 类
import java.util.LinkedList; 
LinkedList<E> list = new LinkedList<E>();   // 普通创建方法
LinkedList<E> list = new LinkedList(Collection<? extends E> c); // 使用集合创建链表
```

- **特点：** 双向链表，元素之间通过指针连接。
- **优点：** 插入和删除元素高效，迭代器性能好。
- **缺点：** 随机访问相对较慢。
- 添加元素: add(obj)
- 头部添加: addFirst(obj)
- 尾部添加: addLast(obj)
- 移除开头: removeFirst()
- 移除尾部: removeLast()
- 获取头部: getFirst()
- 获取尾部: getLast()

## 集合（Sets）

集合（Sets）用于存储不重复的元素，常见的实现有 `HashSet` 和 `TreeSet`。

```java
Set<String> hashSet = new HashSet<>();
Set<Integer> treeSet = new TreeSet<>();
```

**HashSet:**

```java
import java.util.HashSet; // 引入 HashSet 类
HashSet<String> sites = new HashSet<String>();
```

- **特点：** 无序集合，基于HashMap实现。
- **优点：** 高效的查找和插入操作。
- **缺点：** 不保证顺序。
- 添加元素：add(obj)
- 判断元素是否存在：contains(obj)
- 删除元素：remove()
- 清空元素：clear()
- 计算大小：size()
- 

**TreeSet:**

- **特点：**TreeSet 是有序集合，底层基于红黑树实现，不允许重复元素。
- **优点：** 提供自动排序功能，适用于需要按顺序存储元素的场景。
- **缺点：** 性能相对较差，不允许插入 null 元素。

## 映射（Maps）

映射（Maps）用于存储键值对，常见的实现有 `HashMap` 和 `TreeMap`。

```java
Map<String, Integer> hashMap = new HashMap<>();
Map<String, Integer> treeMap = new TreeMap<>();
```

**HashMap:**

```java
import java.util.HashMap; // 引入 HashMap 类
HashMap<Integer, String> Sites = new HashMap<Integer, String>();
```

- **特点：** 基于哈希表实现的键值对存储结构。
- **优点：** 高效的查找、插入和删除操作。
- **缺点：** 无序，不保证顺序。
- 添加元素：put(key, value)
- 访问元素：get(key)
- 删除元素：remove(key)
- 删除所有：clear()
- 计算元素数量：size()
- 获取所有key：keySet()

**TreeMap:**

- **特点：** 基于红黑树实现的有序键值对存储结构。
- **优点：** 有序，支持按照键的顺序遍历。
- **缺点：** 插入和删除相对较慢。
```java
ThreeMap<Integer, String> tm = new TreeMap<>(new Comparator<Integer>(){
  @Override
  public int compare(Integer o1, Integer o2){
    return o2-o1;
  }
});
```
## 栈（Stack）

栈（Stack）是一种线性数据结构，它按照后进先出（Last In, First Out，LIFO）的原则管理元素。在栈中，新元素被添加到栈的顶部，而只能从栈的顶部移除元素。这就意味着最后添加的元素是第一个被移除的。

```java
Stack<Integer> stack = new Stack<>();
```

**Stack 类:**

- **特点：** 代表一个栈，通常按照后进先出（LIFO）的顺序操作元素。

## 队列（Queue）

队列（Queue）遵循先进先出（FIFO）原则，常见的实现有 `LinkedList` 和 `PriorityQueue`。

```java
Queue<String> queue = new LinkedList<>();
```

**Queue 接口:**

- **特点：** 代表一个队列，通常按照先进先出（FIFO）的顺序操作元素。
- **实现类：** `LinkedList`, `PriorityQueue`, `ArrayDeque`。

## 堆（Heap）

堆（Heap）优先队列的基础，可以实现最大堆和最小堆。

```java
PriorityQueue<Integer> minHeap = new PriorityQueue<>();
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());
```

