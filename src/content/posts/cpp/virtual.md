---
title: 纯虚函数

published: 2025-03-10

description: '纯虚函数'

image: './img/IMG_1162.JPG'

tags: [C++, 学习, 函数]

category: 'cpp'

draft: false 
---

纯虚函数（Pure Virtual Function）是C++中的一种特殊类型的虚函数，它在基类中没有具体的实现，并且要求任何继承自该基类的子类都必须提供这个函数的具体实现。纯虚函数在基类中的声明形式是在函数声明后面加上“= 0”，这表示这个函数是纯虚的，没有实际的函数体。
纯虚函数的作用是定义一个接口，使得继承自基类的子类能够以多态的方式实现这个接口。由于纯虚函数没有实现，包含纯虚函数的类不能被实例化，这种类通常被称为**抽象类**（Abstract Class）。
以下是一个简单的示例：
```cpp
class Base {
public:
    virtual void pureVirtualFunction() = 0; // 纯虚函数
};
class Derived : public Base {
public:
    void pureVirtualFunction() override { // 实现纯虚函数
        // 函数的具体实现
    }
};
```
在这个示例中，`Base`是一个抽象类，因为它包含了一个纯虚函数`pureVirtualFunction()`。`Derived`类继承自`Base`类，并且提供了`pureVirtualFunction()`的具体实现。如果`Derived`类没有实现这个纯虚函数，那么`Derived`也会成为一个抽象类，不能被实例化。
纯虚函数是C++实现多态和接口的一种重要机制，它允许基类定义一个通用的接口，而具体的实现则由子类根据具体的需求来完成。
