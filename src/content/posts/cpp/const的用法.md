---
title: const的用法
published: 2025-03-12
description: 'const是C++中的一个关键字，用于声明变量、函数参数、返回值或成员函数为常量，即它们的值在初始化后不能被修改。'
image: './img/IMG_1484.JPG'
tags: [c++, 学习]
category: 'cpp'
draft: false 
---
`const`是C++中的一个关键字，用于声明变量、函数参数、返回值或成员函数为常量，即它们的值在初始化后不能被修改。`const`的用法非常灵活，可以用于多种场景，以下是一些主要的用法：
### 常量变量
```cpp
const int MAX_SIZE = 100;
```
这里，`MAX_SIZE`被声明为一个常量，其值在初始化后不能改变。
### 指向常量的指针
```cpp
const int *ptr = &MAX_SIZE;
```
`ptr`是一个指向常量的指针，不能通过`ptr`来修改`MAX_SIZE`的值。
### 常量指针
```cpp
int value = 10;
int *const ptr = &value;
```
`ptr`是一个常量指针，它始终指向`value`，但不能指向其他变量。
### 指向常量的常量指针
```cpp
const int value = 10;
const int *const ptr = &value;
```
`ptr`既不能改变指向的值，也不能改变指向的地址。
### 常量引用
```cpp
const int &ref = value;
```
`ref`是`value`的常量引用，不能通过`ref`来修改`value`。
### 常量成员函数
在类中，可以声明成员函数为`const`，表示这个函数不会修改类的成员变量。
```cpp
class MyClass {
public:
    int getValue() const {
        return value;
    }
private:
    int value;
};
```
### 常量表达式
C++11引入了`constexpr`，用于声明常量表达式，这些表达式在编译时就能计算出来。
```cpp
constexpr int factorial(int n) {
    return (n <= 1) ? 1 : n * factorial(n - 1);
}
constexpr int result = factorial(5); // 在编译时计算
```
### 对象常量
可以声明对象为常量，这样对象的成员就不能被修改。
```cpp
const MyClass obj;
```
### 常量与多态
`const`成员函数可以用于多态，因为它们可以被子类覆盖，并且保持`const`属性。
```cpp
class Base {
public:
    virtual void display() const {
        // ...
    }
};
class Derived : public Base {
public:
    void display() const override {
        // ...
    }
};
```
### 常量与模板
`const`可以与模板一起使用，以创建通用的常量类型。
```cpp
template<typename T>
void print(const T& value) {
    // ...
}
```
### 保护参数

函数的参数可以加上const修饰，以避免无意识的修改const变量。

通过指针可以绕过`const`修饰，但这种方法不推荐，因为它破坏了`const`的语义，可能导致未定义行为。

**示例：**

```
void modifyValue(const int* value) {
    int* nonConstPtr = const_cast<int*>(value); // 强制转换去除const
    *nonConstPtr = 10; // 修改值
}

int main() {
    int a = 5;
    modifyValue(&a);
    std::cout << a << std::endl; // 输出10
    return 0;
}
```

**注意：** 使用`const_cast`时要非常小心，因为它破坏了常量性，可能导致未定义行为，特别是如果原始数据确实不应该被修改时。

### 注意事项：

- `const`变量必须在声明时初始化，除非它是静态存储期变量。
- `const`对象只能调用`const`成员函数。
- `const`指针和`const`引用可以指向非`const`对象，但通过它们不能修改对象的值。
- `const`在函数参数中用于表示参数不会被修改，这有助于提高代码的可读性和安全性。
  `const`的这些用法有助于编写更安全、更可维护的代码，通过限制变量的可变性，可以避免一些常见的编程错误。