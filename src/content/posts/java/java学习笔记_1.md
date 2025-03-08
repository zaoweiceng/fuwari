---
title: java学习笔记（1）
published: 2025-03-07
description: 'Java学习之路'
image: './starrail.png'
tags: [java, 学习, default, 枚举, 堆, 抽象类, 接口类, instanceof]
category: 'java'
draft: false 
---

### default关键字

像其他语言一样，Java可以使用修饰符来修饰类中方法和属性。

- 访问控制修饰符 : default, public , protected, private
- 非访问控制修饰符 : final, abstract, static, synchronized

在Java中，`default` 关键词有两个主要的用途：

1. **默认访问修饰符**：
   - 当一个类、方法或变量没有明确指定访问修饰符（如`public`、`protected`、`private`）时，它默认具有包级私有（package-private）访问权限。这意味着只有同一个包内的类可以访问这个类、方法或变量。
2. **接口中的默认方法**：
   - 从Java 8开始，接口中可以包含默认方法（default methods）。这些方法是带有具体实现的方法，使用`default`关键字修饰。默认方法允许在接口中提供方法的实现，这样现有的实现类不需要修改就可以使用新添加的方法。这为接口的扩展提供了更大的灵活性。

```java
// 默认访问修饰符示例
class DefaultExample {
    int number; // 默认访问权限，同一包内的类可以访问

    void display() { // 默认访问权限，同一包内的类可以访问
        System.out.println("Number: " + number);
    }
}

// 接口中的默认方法示例
interface MyInterface {
    void method1(); // 抽象方法

    default void method2() { // 默认方法
        System.out.println("Default method implementation");
    }
}

class ImplementationClass implements MyInterface {
    public void method1() {
        System.out.println("Implementation of method1");
    }
}

public class Main {
    public static void main(String[] args) {
        ImplementationClass obj = new ImplementationClass();
        obj.method1(); // 调用实现的方法
        obj.method2(); // 调用接口中的默认方法
    }
}

```

在这个示例中，`DefaultExample`类没有明确指定访问修饰符，因此它的成员具有包级私有访问权限。`MyInterface`接口包含一个默认方法`method2`，`ImplementationClass`实现了这个接口，并可以直接使用`method2` 而不用提供实现。

### 枚举

Java 5.0引入了枚举，枚举限制变量只能是预先设定好的值。使用枚举可以减少代码中的 bug。

例如，我们为果汁店设计一个程序，它将限制果汁为小杯、中杯、大杯。这就意味着它不允许顾客点除了这三种尺寸外的果汁。

```java
class FreshJuice {
   enum FreshJuiceSize{ SMALL, MEDIUM , LARGE }
   FreshJuiceSize size;
}
 
public class FreshJuiceTest {
   public static void main(String[] args){
      FreshJuice juice = new FreshJuice();
      juice.size = FreshJuice.FreshJuiceSize.MEDIUM  ;
   }
}
```

**注意：**枚举可以单独声明或者声明在类里面。方法、变量、构造函数也可以在枚举中定义。

### 数组存在于堆上

Java中的数组作为对象，确实是在堆上存储的，而指向这些数组的引用则存储在栈上。在Java中，所有的对象，包括数组，都是在堆上分配内存的。这是因为对象通常需要较大的内存空间，并且生命周期较长，因此将它们存储在堆上可以更有效地管理内存。
当你在Java中创建一个数组时，例如：

```java
int[] myArray = new int[10];
```
这里，`myArray`是一个引用变量，它存储在栈上，而实际的数组对象（包含10个整数元素）是在堆上分配的。`myArray`变量持有的只是这个堆上数组对象的引用（或地址）。
这种设计允许Java具有更灵活的内存管理，并支持复杂的数据结构和动态内存分配。但是，这也意味着开发者需要负责管理对象的创建和销毁，以避免内存泄漏和其他内存相关的问题。

**Java具有自动垃圾回收机制，可以自动回收不再被引用的对象所占用的空间**。当对象不再被任何引用所指向时，垃圾回收器会在适当的时候回收这些对象所占用的内存。
然而，尽管Java提供了自动垃圾回收，开发者仍然需要谨慎地管理对象的引用，以避免不必要的内存占用和潜在的内存泄漏。例如，如果对象之间存在循环引用，且这些对象不再被外部引用，垃圾回收器可能无法回收它们，从而导致内存泄漏。
此外，垃圾回收器的具体行为和效率也受到多种因素的影响，包括Java虚拟机的实现、垃圾回收算法的选择、系统的内存压力等。因此，尽管Java提供了自动垃圾回收，开发者仍然需要关注内存管理和性能优化。Java的自动垃圾回收机制确实可以自动回收不再被引用的对象所占用的空间，但开发者仍需谨慎管理对象引用并关注内存使用情况。



### 抽象类和接口类

在Java中，抽象类（Abstract Class）和接口（Interface）都是用来实现抽象化的重要机制，但它们之间有一些关键的区别：
1. **定义**：
   - **抽象类**：是一种不能被实例化的类，可以包含具体实现的方法和抽象方法（没有方法体的方法）。
   - **接口**：是一种完全抽象的类，只能包含抽象方法和默认方法（Java 8+），以及常量。
2. **继承与实现**：
   - **抽象类**：一个类只能继承一个抽象类，因为Java不支持多重继承。
   - **接口**：一个类可以实现多个接口，提供多继承的效果。
3. **成员**：
   - **抽象类**：可以包含构造器、非final的变量、具体实现的方法、抽象方法、静态方法、初始化块等。
   - **接口**：只能包含静态常量、抽象方法、默认方法（Java 8+）、静态方法（Java 9+）。
4. **访问修饰符**：
   - **抽象类**：可以有任何访问修饰符（public、protected、private）。
   - **接口**：默认是public的，成员变量默认是public static final的，方法默认是public的。
5. **使用场景**：
   - **抽象类**：当多个类共享一些共同的方法和属性时，可以使用抽象类来定义这些共同的特性。
   - **接口**：用于定义公共的方法规范，实现类可以以任何方式实现这些方法。
6. **扩展性**：
   - **抽象类**：扩展性有限，因为子类只能继承一个抽象类。
   - **接口**：提供更高的扩展性，因为一个类可以实现多个接口。
7. **默认实现**：
   - **抽象类**：可以提供某些方法的具体实现，子类可以选择性地覆盖这些方法。
   - **接口**：在Java 8之前不能提供方法的实现，Java 8引入了默认方法，允许接口提供方法的默认实现。
8. **多态性**：
   - **抽象类**：可以通过抽象类引用指向子类对象来实现多态。
   - **接口**：可以通过接口引用指向实现类对象来实现多态。
总的来说，抽象类更适合于那些部分实现或共享代码的场景，而接口则适用于定义完全抽象的API，使得实现类可以以多种方式实现这些API。选择使用抽象类还是接口，取决于具体的设计需求和场景。



### instanceof

`instanceof` 是 Java 语言中的一个二元操作符，用于测试左边的对象是否是右边类或接口的实例，返回 boolean 类型的结果。如果左边的对象是右边类或接口的实例，或者是右边类的子类实例，那么 `instanceof` 操作符返回 true，否则返回 false。
其基本语法如下：
```java
object instanceof Class
```
- `object`：要测试的对象。
- `Class`：类或接口的类型。
`instanceof` 操作符的主要用途包括：
1. **类型检查**：在运行时确定对象的类型，以便进行相应的操作。
2. **向下转型**：在向下转型（将父类对象转换为子类对象）之前，使用 `instanceof` 检查可以避免 `ClassCastException`。
示例：
```java
class Animal {}
class Dog extends Animal {}
public class Test {
    public static void main(String[] args) {
        Animal myAnimal = new Dog();
        if (myAnimal instanceof Dog) {
            Dog myDog = (Dog) myAnimal; // 安全的向下转型
            System.out.println("This is a Dog.");
        }
    }
}
```
在这个例子中，`myAnimal instanceof Dog` 检查 `myAnimal` 是否是 `Dog` 类的实例，因为 `myAnimal` 实际上指向的是一个 `Dog` 对象，所以这个表达式返回 true，随后可以安全地进行向下转型。
需要注意的是，`instanceof` 操作符只能用于对象类型，不能用于基本数据类型。例如，以下代码是错误的：
```java
int num = 5;
if (num instanceof Integer) { // 错误，基本数据类型不能使用 instanceof
    // ...
}
```
在使用 `instanceof` 时，还应该注意避免与过度的类型检查，这可能会使代码变得冗余和难以维护。在设计类和接口时，应尽量使用多态和接口来减少对 `instanceof` 的依赖。