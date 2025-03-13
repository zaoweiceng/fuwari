---
title: C++ Function

published: 2025-03-10

description: 'C++中函数相关内容'

image: './img/pc.png'

tags: [C++, 学习, 函数]

category: 'cpp'

draft: false 
---

## static

- 静态方法，没有this指针，不属于特定的对象。
- 不访问这个对象的非静态成员。

```c++
class demo{
    private:
    	std::string VERSION = "v1.0.0";
    public:
    	static std::string getVersion();
}
```

------

## const

- 承诺不会在方法内不改变对象内部的值。
- 非const能调用const方法，const不能调用非const

```c++
class demo{
    public:
    	std::string getString() const;
}
```

------

## mutable

- 允许在const中修改的成员变量

```c++
class demo{
    private:
    	int value;
    	mutable size_t numOfAccesses = 0;
    public:
    	int getValue() const{
            numOfAccesses++;
            return value;
        }
}
```

------

## noexcept

- 该方法不会抛出异常

```c++
void test(int a) noexcept;
```

------

## delete

- 显示的删除某个方法

```c++
class demo{
	public:
    	int foo(int a);
    	int foo(double a) = delete;
}  
```

以  `double`为参数调用`foo`时编译器会报错

------

## inline

```c++
inline int getValue(){
    ......
}
```

内联类似于`#define`，但是比使用`#define`更安全

------

## virtual

只有在基类中声明为virtual的方法才能被派生类重写

- 建议所有方法都声明为virtual(对于现代计算机而言开销很小，Java默认使用virtual)
- 析构函数一定要声明为virtual，否则派生类将默认调用基类的析构函数
- 纯虚方法告诉编译器，该方法在该类中不存在定义。包含纯虚方法的类成为***抽象类***

```c++
class Base{
    public:
    	virtual void method();
    	virtual void method1() = 0;//纯虚方法
}
```

## override

```c++
class Next: public Base{
    public:
    	virtual void method() override;
}
```



## constexpr

- 常量表达式，通常在编译时进行计算

```c++
constexpr int getArraySize(){return 32;}
int nums[getArraySize()]; //getArraySize()是一个常量
```