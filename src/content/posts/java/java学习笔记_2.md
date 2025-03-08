---
title: java学习笔记（2）
published: 2025-03-08
description: 'Java学习之路'
image: './img/Tifa.PNG'
tags: [java, 学习]
category: 'java'
draft: false 
---


## 可变参数

**Java的函数支持可变参数**。在Java中，可变参数允许在调用方法时传入不定数量的参数。这为方法提供了一种灵活的方式来处理不同数量的输入。

要定义一个可变参数方法，只需在参数类型后加上省略号（…）。例如，以下是一个简单的可变参数方法的示例：

```java
public class VarargsExample {
    public static void main(String[] args) {
        // 调用可变参数方法
        printNumbers(1, 2, 3, 4, 5);
        printNumbers(10, 20);
    }
    // 定义可变参数方法
    public static void printNumbers(int... numbers) {
        for (int number : numbers) {
            System.out.println(number);
        }
    }
}
```

在这个示例中，`printNumbers`方法可以接受任意数量的`int`类型参数。当调用这个方法时，可以传入任意数量的整数，方法会遍历并打印每个数字。

需要注意的是，可变参数必须是方法参数列表中的最后一个参数，并且一个方法只能有一个可变参数。如果方法还有其他参数，它们必须位于可变参数之前。

此外，可变参数在内部实际上是一个数组，因此可以像数组一样进行操作。但是，为了提高代码的可读性和清晰度，建议在需要处理不定数量参数的情况下使用可变参数。



## Java类内实例化与循环引用分析

#### 静态方法实例化类

Java中类的静态方法（比如main）属于类本身，而不是实例。在静态方法中，可以创建该类的实例，这没有问题。比如常见的public static void main里，通常会创建自己类的实例来启动程序。所以这并不会导致循环引用，因为这只是创建一个实例，而不会无限递归。比如:

```java
public class Test {
    public static void main(String[] args) {
    	Test t = new Test(); // 这是合法的，不会导致循环
    }
}
```

这时候，每次运行main方法会创建一个Test实例，但如果没有在构造函数里再次创建Test实例的话，就不会有问题。

#### 循环引用

- **循环引用的定义**：循环引用指两个或多个对象**互相持有引用**，导致无法被垃圾回收。例如：

```java
class A { B b; }
class B { A a; }
```

实际上，只有在构造函数中不加控制地创建自身实例只会导致栈溢出。

```java
public class Test {
    public Test() {
    	new Test(); // 每次构造都会创建新的实例，导致无限递归，栈溢出
    }
    public static void main(String[] args) {
    	new Test();
    }
}
```

同时，对象之间的相互引用，比如A引用B，B引用A，这样可能导致内存泄漏。



```java
public class reference {
    private class Node{
        int a, b, c;
        Node(int a, int b, int c){
            this.a = a;
            this.b = b;
            this.c = c;
        }
        public String toString(){
            return "a: " + a + " b: " + b + " c: " + c;
        }
    }
    public void change(Node node){
        node.a = 1;
        node.b = 2;
        node.c = 3;
    }
    public static void main(String[] args) {
        reference outer = new reference();
        Node node = outer.new Node(0, 0, 0);
        System.out.println(node);
        outer.change(node);
        System.out.println(node);
    }
}
```

这段代码仅存在**单向引用**（`node`→`outer`），而非`reference`与`Node`互相引用。因此不存在循环引用。

非静态内部类在编译后会生成一个包含外部类引用的构造器。例如，`Node`类会被编译为类似以下形式：

```java
class reference$Node {
    final reference outer; // 隐式持有外部类实例
    int a, b, c;
    
    reference$Node(reference outer, int a, int b, int c) {
        this.outer = outer;
        this.a = a;
        // ...
    }
}
```

- 当你通过`outer.new Node(...)`创建`Node`实例时，实际调用的是上述构造器，将`outer`作为参数传入。