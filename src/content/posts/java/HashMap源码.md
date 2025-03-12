---
title: HashMap源码
published: 2025-03-12
description: '手撕源码'
image: './img/IMG_1181.jpg'
tags: [java, 学习, HashMap]
category: 'java'
draft: false 
---

## 内部定义

### Node

基本哈希表的节点，用于大多数条目。哈希表内部存储键值对时，采用Node进行存储。

```java
// Node节点实现Map.Entry接口
// 该接口主要包含以下方法：
// K getKey();
// V getValue();
// V setValue(V value);
// boolean equals(Object o);
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;	// 键的哈希值
    final K key;	// 建
    V value;		// 值
    Node<K,V> next; // 下一个节点的地址
	...   
}
```

### 红黑树

当单个节点下挂载的链表长度超过一定值(8)时，会转换为红黑树。

```java
 static final class TreeNode<K,V> extends LinkedHashMap.Entry<K,V> {
     TreeNode<K,V> parent;  // red-black tree links
     TreeNode<K,V> left;
     TreeNode<K,V> right;
     TreeNode<K,V> prev;    // needed to unlink next upon deletion
     boolean red;
     ...
 }
```







### 内部节点表

HashMap内部存储数据的表，表在首次使用时初始化，并根据需要调整大小。分配后，长度始终为 2 的幂。

```java
transient Node<K,V>[] table;
```

### 默认容量

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16
```

### 默认加载因子

```java
static final float DEFAULT_LOAD_FACTOR = 0.75f;
```



## 构造函数

```java
public HashMap() {
    this.loadFactor = DEFAULT_LOAD_FACTOR; // all other fields defaulted
}
```

默认构造函数不会初始化内部的节点表，节点表在首次使用时才会进行初始化。



## Put操作

操作定义如下：

```java
public V put(K key, V value) {
    return putVal(hash(key), key, value, false, true);
}
```

- 如果覆盖了元素，则返回覆盖元素的值，否则返回null。

put方法中的**hash**函数的定义如下：

```java
static final int hash(Object key) {
    int h;
    return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

- 利用键计算出对应的hash值，并经过一系列位操作以优化哈希表的性能，最后把返回值作为键的哈希值。

put方法中**putVal**函数定义如下：

添加元素的时候至少考虑三种情况：

- 数组位置为null

- 数组位置不为null，键不重复，挂在下面形成链表或者红黑树

- 数组位置不为null，键重复，元素覆盖

```java
final V putVal(int hash, K key, V value, boolean onlyIfAbsent,
               boolean evict) {
    Node<K,V>[] tab; //定义一个局部变量，用来记录哈希表中数组的地址值。
    Node<K,V> p; 	 //临时的第三方变量，用来记录键值对对象的地址值
    int n, i;		 //n表示当前数组的长度，i表示索引
    //把哈希表中数组的地址值，赋值给局部变量tab
    if ((tab = table) == null || (n = tab.length) == 0)
        //resize()函数执行以下操作：
        //1.如果当前是第一次添加数据，底层会创建一个默认长度为16，加载因子为0.75的数组
        //2.如果不是第一次添加数据，会看数组中的元素是否达到了扩容的条件
        //如果没有达到扩容条件，底层不会做任何操作
        //如果达到了扩容条件，底层会把数组扩容为原先的两倍，并把数据全部转移到新的哈希表中
        n = (tab = resize()).length;
    	//表示把当前数组的长度赋值给n
    
    //将数组的长度跟键的哈希值进行计算，计算出当前键值对对象，在数组中应存入的位置
    //获取数组中对应元素的数据
    if ((p = tab[i = (n - 1) & hash]) == null)
        //如果获取到的值是null，底层会创建一个键值对对象，直接放到数组当中
        tab[i] = newNode(hash, key, value, null);
    else {
        Node<K,V> e; K k;
        if (p.hash == hash &&
            ((k = p.key) == key || (key != null && key.equals(k))))
            
            e = p;
        else if (p instanceof TreeNode)
            //判断数组中获取出来的键值对是不是红黑树中的节点
			//如果是，则调用方法putTreeVal，把当前的节点按照红黑树的规则添加到树当中。
            e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
        else {
            //如果从数组中获取出来的键值对不是红黑树中的节点
			//表示此时下面挂的是链表
            for (int binCount = 0; ; ++binCount) {
                if ((e = p.next) == null) {
                    //此时就会创建一个新的节点，挂在下面形成链表
                    p.next = newNode(hash, key, value, null);
                    //判断当前链表长度是否超过8，如果超过8，就会调用方法treeifyBin
                    //treeifyBin方法的底层还会继续判断
                    //判断数组的长度是否大于等于64
                    //如果同时满足这两个条件，就会把这个链表转成红黑树
                    if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                        treeifyBin(tab, hash);
                    break;
                }
                //如果哈希值一样（哈希碰撞），就会调用equals方法比较内部的属性值是否相同，若相同则break
                if (e.hash == hash &&
                    ((k = e.key) == key || (key != null && key.equals(k))))
                    break;
                p = e;
            }
        }
        //判断是否有元素的覆盖
        if (e != null) { // existing mapping for key 
            V oldValue = e.value;
            if (!onlyIfAbsent || oldValue == null)
                // 仅修改数据，而不修改节点
                e.value = value;
            afterNodeAccess(e);
            return oldValue;
        }
    }
    ++modCount;
    //threshold：记录的就是数组的长度 * 0.75，哈希表的扩容时机  16 * 0.75 = 12
    if (++size > threshold)
        resize();
    afterNodeInsertion(evict);
    //表示当前没有覆盖任何元素，返回null
    return null;
}
```

- 参数：
  - 键的哈希值
  - 键
  - 值
  - 如果为`true`，则只有在键不存在时才插入新值。如果键已经存在，则不会替换现有的值。这个参数在`putIfAbsent`方法中会用到。
  - 这个参数在`putVal`方法中的默认值为`false`。它在`LinkedHashMap`的子类中用于控制是否在插入后执行删除操作。在`HashMap`中，这个参数通常不被使用。

- `putVal`方法的工作原理大致如下：
  - 计算键的哈希值，并找到对应的桶（bucket）。
  - 检查桶中是否已经有键值对。如果有，则根据键的相等性（`equals`方法）检查是否是同一个键。
  - 如果是同一个键，根据`onlyIfAbsent`参数决定是否更新值。
  - 如果不是同一个键，或者桶为空，则将新的键值对插入到桶中。
  - 如果插入后桶的大小超过了负载因子与容量的乘积，则进行扩容操作。

这个方法的设计考虑了性能和线程安全（在`ConcurrentHashMap`中）。在`HashMap`中，它不是线程安全的，但在`ConcurrentHashMap`中，`putVal`方法会采用不同的策略来保证线程安全。

[参考视频](https://www.bilibili.com/video/BV1yW4y1Y7Ms?p=15)