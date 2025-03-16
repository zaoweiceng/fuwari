---
title: virtual在使用中的问题
published: 2025-03-16
description: 'virtual在使用中的问题，bug！！！'
image: './img/20250309215810_1.jpg'
tags: [c++, 学习]
category: 'cpp'
draft: false 
---

## virtual在使用中的问题

想要重写基类的方法，应该将基类方法上加入关键字`virtual`。

如果不加，在重写父类方法时可能会有问题，虽然编译器不会报错，但是使用多态时，会调用到基类的方法。

```c++
#include<iostream>  
using namespace std;
class Animal{
    public:
    void say(){
        cout<<"Animal say"<<endl;
    }
};
class Cat:public Animal{
    public:
    void say(){
        cout<<"Cat say"<<endl;
    }
};

int main(){
    Animal *a=new Animal();
    Cat *c=new Cat();
    a->say();
    c->say();
    Animal *ac=new Cat();
    ac->say();
    return 0;
}
```

输出：

```c++
Animal say
Cat say
Animal say
```

父类方法使用`virtual`关键字之后，仍然可以通过父类获取到父类的方法，但是使用多态时不会出现问题。

```c++
#include<iostream>  
using namespace std;
class Animal{
    public:
    virtual void say(){
        cout<<"Animal say"<<endl;
    }
};
class Cat:public Animal{
    public:
    void say(){
        cout<<"Cat say: ";
        Animal::say();
    }
};

int main(){
    Animal *a=new Animal();
    Cat *c=new Cat();
    a->say();
    c->say();
    cout << endl;
    Animal *ac=new Cat();
    ac->say();
    return 0;
}
```

输出：

```c++
Animal say
Cat say: Animal say

Cat say: Animal say
```



C++在编译类时，会创建一个包含类中所有方法的二进制对象。在非虚的情况下，将控制交给正确的方法的代码是硬编码，此时会根据编译时的类型调用方法。这成为静态绑定(static binding)， 也成为早绑定(early binding)。

如果方法声明为virtual，会使用名为虚表(vtable)的特定内存区域调用正确的实现。每个具有一个或多个虚方法的类都有一张虚表，这种类的每个对象都包含指向虚表的指针，这个虚表包含指向虚方法实现的指针。通过这种方法，当使用某个对象调用方法时，指针也进入虚表，然后根据实际的对象类型执行正确版本的方法。这成为动态绑定(dynamic binding)或晚绑定(late binding)。



## 纯虚方法和抽象基类

纯虚方法是在类定义中显示说明该方法不需要定义。如果将某个方法设置为纯虚方法，就是告诉编译器当前类中不存在这个方法的定义。具有至少一个纯虚方法的类称为抽象类。

纯虚方法定义：

```c++
virtual void set() = 0;
```

抽象类不能够被直接实例化，需要实现了纯虚方法的继承类。



## override关键字

`override`是C++11引入的关键字，用于显式地指出某个函数是重写基类中的虚函数。它的主要目的是增加代码的可读性和维护性，同时让编译器能够检查重写是否正确。
**基本用法**：
当在派生类中重写基类的一个虚函数时，可以在函数声明后加上`override`关键字。这样，编译器会检查该函数是否真的重写了基类的某个虚函数，即函数签名（包括函数名、参数列表、常量性等）是否与基类中的虚函数完全匹配。
**示例**：

```cpp
class Base {
public:
    virtual void func(int) const {}
};
class Derived : public Base {
public:
    void func(int) const override {}  // 正确：显式指出重写基类的func函数
};
```
**作用**：
1. **编译器检查**：如上所述，`override`让编译器能够检查重写是否正确。如果派生类中的函数没有正确重写基类的虚函数（例如，参数列表不匹配），编译器会报错。
2. **提高代码可读性**：通过使用`override`，其他阅读代码的人可以很容易地知道这个函数是重写基类的虚函数，而不是派生类特有的函数。
3. **维护性**：如果基类中的虚函数签名发生变化，而派生类没有相应更新，使用`override`的代码会在编译时出错，从而提醒开发者进行修正。
**注意事项**：
* `override`只能用于派生类中的函数声明或定义上，且该函数必须重写基类的虚函数。
* 如果基类中的函数不是虚函数，则在派生类中使用`override`会编译错误。
* `override`关键字应该紧跟在函数声明后，逗号与函数声明之间不能有空格。
**错误示例**：
```cpp
class Base {
public:
    void func(int) {}  // 不是虚函数
};
class Derived : public Base {
public:
    void func(int) override {}  // 错误：基类中的func不是虚函数
};
```

`override`关键字是C++11中一个非常有用的特性，它提高了代码的健壮性、可读性和维护性。在实际开发中，建议总是使用`override`来显式指出重写基类的虚函数。

## 虚基类

在C++中，虚基类（Virtual Base Class）是多重继承中用于解决菱形继承问题的一种机制。所谓菱形继承问题，是指一个派生类从两个或多个基类继承，而这些基类又共同继承自同一个更高级的基类，导致派生类中出现了多份高级基类的实例，引起潜在的二义性和资源浪费。
虚基类的引入就是为了解决这一问题。通过将共同基类声明为虚基类，C++确保在派生类中只存在一份虚基类的实例，从而避免了数据重复和二义性。
以下是虚基类的一个简单示例：
```cpp
class A {
public:
    int a;
};
class B : virtual public A {
public:
    int b;
};
class C : virtual public A {
public:
    int c;
};
class D : public B, public C {
public:
    int d;
};
```
在这个例子中，类B和类C都从类A虚继承，而类D又从类B和类C继承。由于类A是虚基类，所以类D中只会有一个类A的实例，避免了菱形继承问题。
虚基类的实现机制相对复杂，它通常通过虚基类表（Vtbl）来维护虚基类的指针，从而确保在派生类中只存在一份虚基类的实例。在使用虚基类时，需要注意构造函数的调用顺序和初始化，因为虚基类的构造函数会在派生类的构造过程中被调用，以确保虚基类的正确初始化。