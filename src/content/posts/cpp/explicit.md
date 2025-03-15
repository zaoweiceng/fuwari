---
title: explicit关键字
published: 2025-03-15
description: 'explicit是C++中的一个关键字，用于修饰构造函数或转换函数，以防止隐式类型转换。'
image: './img/IMG_1472.JPG'
tags: [c++, 学习]
category: 'cpp'
draft: false 
---

`explicit`是C++中的一个关键字，用于修饰构造函数或转换函数，以防止隐式类型转换。它的主要目的是增加代码的清晰性和避免潜在的错误。
###  `explicit`与构造函数
在C++中，当某个类有一个单参数的构造函数时，这个构造函数通常会被用作从参数类型到该类的隐式类型转换。有时这种隐式转换可能会导致意外的结果或错误。
**示例：**
```cpp
class MyClass {
public:
    MyClass(int value) { /* ... */ }
};
void func(MyClass obj) { /* ... */ }
int main() {
    func(10);  // 隐式地将int转换为MyClass
    return 0;
}
```
在上面的代码中，`func(10)`会隐式地调用`MyClass`的构造函数，将`int`类型的`10`转换为`MyClass`类型的对象。
**使用`explicit`：**
为了防止这种隐式转换，可以使用`explicit`关键字来修饰构造函数：
```cpp
class MyClass {
public:
    explicit MyClass(int value) { /* ... */ }
};
void func(MyClass obj) { /* ... */ }
int main() {
    func(10);  // 错误：无法隐式地将int转换为MyClass
    func(MyClass(10));  // 正确：显式地调用构造函数
    return 0;
}
```
在这个修改后的版本中，`func(10)`会导致编译错误，因为`MyClass`的构造函数被声明为`explicit`，从而阻止了隐式类型转换。但你可以通过显式地调用构造函数来创建一个`MyClass`对象，如`func(MyClass(10))`。
###  `explicit`与转换函数
除了构造函数，`explicit`还可以用于修饰转换函数（即类型转换运算符）。
**示例：**
```cpp
class MyClass {
public:
    operator int() const { /* ... */ return 0; }
};
void func(int value) { /* ... */ }
int main() {
    MyClass obj;
    func(obj);  // 隐式地将MyClass转换为int
    return 0;
}
```
在上面的代码中，`func(obj)`会隐式地调用`MyClass`的转换函数，将`MyClass`类型的`obj`转换为`int`类型。
**使用`explicit`：**
为了防止这种隐式转换，可以使用`explicit`关键字来修饰转换函数：
```cpp
class MyClass {
public:
    explicit operator int() const { /* ... */ return 0; }
};
void func(int value) { /* ... */ }
int main() {
    MyClass obj;
    func(obj);  // 错误：无法隐式地将MyClass转换为int
    func(static_cast<int>(obj));  // 正确：显式地调用转换函数
    return 0;
}
```
在这个修改后的版本中，`func(obj)`会导致编译错误，因为`MyClass`的转换函数被声明为`explicit`，从而阻止了隐式类型转换。但你可以通过显式地使用`static_cast`来执行类型转换，如`func(static_cast<int>(obj))`。
### 为什么使用`explicit`？
* **避免意外行为**：隐式类型转换有时会导致意外的行为，特别是当转换不是程序员预期的时候。
* **提高代码可读性**：显式类型转换使得代码的意图更明确，其他阅读代码的人可以更容易地理解类型转换的目的是什么。
* **调试更简单**：当出现类型转换相关的问题时，如果使用了`explicit`，编译器通常会提供更明确的错误信息，从而使得调试更简单。
总之，`explicit`关键字是C++中一个非常有用的工具，可以帮助程序员编写更清晰、更健壮的代码。