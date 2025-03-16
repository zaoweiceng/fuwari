---
title: C++可变参数
published: 2025-03-16
description: '在C++中，使用可变参数主要有两种方式：一种是通过C风格的变参函数，另一种是利用C++11引入的初始化列表和变参模板。'
image: './img/20250309221132_1.jpg'
tags: [c++, 学习]
category: 'cpp'
draft: false 
---
在C++中，使用可变参数主要有两种方式：一种是通过C风格的变参函数，另一种是利用C++11引入的初始化列表和变参模板。
### C风格的变参函数
C风格的变参函数使用`stdarg.h`头文件中的宏来实现。下面是一个简单的例子：
```cpp
#include <iostream>
#include <cstdarg>
void printNumbers(int count, ...) {
    va_list args;
    va_start(args, count);
    for (int i = 0; i < count; ++i) {
        int num = va_arg(args, int);
        std::cout << num << " ";
    }
    va_end(args);
    std::cout << std::endl;
}
int main() {
    printNumbers(3, 1, 2, 3);  // 输出: 1 2 3
    printNumbers(5, 4, 5, 6, 7, 8);  // 输出: 4 5 6 7 8
    return 0;
}
```
在这个例子中，`printNumbers`函数可以接受任意数量的整数参数。第一个参数`count`指定了后面参数的数量。`va_list`、`va_start`、`va_arg`和`va_end`是处理可变参数的宏。

```c++
#include <iostream>
#include <cstdarg> // C++中应使用cstdarg而不是stdarg.h

// 计算所有参数的和
double sum(int count, ...) {
    va_list args;
    va_start(args, count); // 初始化args来存储参数

    double total = 0;
    for (int i = 0; i < count; ++i) {
        total += va_arg(args, double); // 获取下一个参数
    }

    va_end(args); // 清理工作
    return total;
}

int main() {
    std::cout << "Sum: " << sum(3, 1.1, 2.2, 3.3) << std::endl; // 输出应该是6.6
    return 0;
}
```



### C++11的初始化列表和变参模板
C++11引入了更加现代化和类型安全的方法来处理可变参数，即使用初始化列表和变参模板。下面是一个例子：
```cpp
#include <iostream>
#include <initializer_list>
void printNumbers(std::initializer_list<int> args) {
    for (int num : args) {
        std::cout << num << " ";
    }
    std::cout << std::endl;
}
int main() {
    printNumbers({1, 2, 3});  // 输出: 1 2 3
    printNumbers({4, 5, 6, 7, 8});  // 输出: 4 5 6 7 8
    return 0;
}
```
在这个例子中，`printNumbers`函数接受一个`initializer_list<int>`类型的参数，它可以包含任意数量的整数。
此外，C++11还支持变参模板，允许模板接受任意数量的参数。下面是一个使用变参模板的例子：

```cpp
#include <iostream>
template<typename... Args>
void printNumbers(Args... args) {
    (std::cout << ... << args) << std::endl;
}
int main() {
    printNumbers(1, " ", 2, " ", 3);  // 输出: 1 2 3
    printNumbers(4, " ", 5, " ", 6, " ", 7, " ", 8);  // 输出: 4 5 6 7 8
    return 0;
}
```
在这个例子中，`printNumbers`是一个变参模板函数，可以接受任意类型和数量的参数。参数展开使用折叠表达式`(std::cout << ... << args)`实现。
这两种方法都可以在C++中实现可变参数的功能，但C++11的初始化列表和变参模板提供了更好的类型安全和便利性。

```c++
#include <iostream>
#include <tuple>

// 变参模板函数的递归终止条件
template<typename T>
T sum(T last) {
    return last;
}

// 变参模板函数
template<typename T, typename... Args>
T sum(T first, Args... args) {
    return first + sum(args...); // 递归调用
}

int main() {
    std::cout << "Sum: " << sum(1, 2, 3, 4, 5) << std::endl; // 输出应该是15
    return 0;
}

```

### 折叠表达式

**折叠表达式**是C++17引入的一种特性，用于在变参模板中简化对参数包的处理。在上述例子中，`(std::cout << ... << args)`就是一个折叠表达式。
#### 折叠表达式的语法
折叠表达式的语法如下：
* `(op ...)`：一元右折叠
* `(... op)`：一元左折叠
* `(init op ...)`：二元右折叠
* `(... op init)`：二元左折叠
其中：
* `op` 是一个二元操作符，如`+`、`<<`等。
* `...` 表示参数包。
* `init` 是一个初始值。
#### 折叠表达式的工作原理
折叠表达式会将参数包中的每个参数依次与操作符`op`结合，最终得到一个结果。具体来说：
* **一元右折叠**：从右向左结合，例如`(a + ... + z)`等价于`a + (b + (c + ... + z))`。
* **一元左折叠**：从左向右结合，例如`(... + a + b + c)`等价于`((a + b) + c)`。
* **二元右折叠**：从右向左结合，每个参数都与`init`结合，例如`(init + ... + z)`等价于`init + (a + (b + (c + ... + z)))`。
* **二元左折叠**：从左向右结合，每个参数都与`init`结合，例如`(... + a + b + c + init)`等价于`(((a + b) + c) + init)`。
#### 在例子中的应用
在上述例子中，`(std::cout << ... << args)`是一个一元左折叠表达式。它的工作原理如下：
1. 从左向右开始，首先将`args`中的第一个参数与`std::cout`结合，使用`<<`操作符输出。
2. 然后将结果（即更新后的`std::cout`）与`args`中的第二个参数结合，再次使用`<<`操作符输出。
3. 重复上述过程，直到所有参数都被输出。
最终，这个折叠表达式会依次将所有参数输出到标准输出流`std::cout`中。
#### 优点
使用折叠表达式处理变参模板参数具有以下优点：
* **简洁**：避免了冗长的递归函数或循环。
* **直观**：直接表达了参数的处理方式。
* **高效**：编译器可以优化折叠表达式的实现。

以下是一些使用C17折叠表达式处理可变参数的例子：
#### 例子1：打印多个值
```cpp
#include <iostream>
#include <string>
template<typename... Args>
void print(Args... args) {
    (std::cout << ... << args) << std::endl;
}
int main() {
    print("Hello, ", "world!", " The answer is ", 42);
    // 输出: Hello, world! The answer is 42
    return 0;
}
```
#### 例子2：计算多个数的和
```cpp
#include <iostream>
template<typename... Args>
auto sum(Args... args) {
    return (args + ...);
}
int main() {
    std::cout << "The sum is: " << sum(1, 2, 3, 4, 5) << std::endl;
    // 输出: The sum is: 15
    return 0;
}
```
#### 例子3：连接字符串
```cpp
#include <iostream>
#include <string>
template<typename... Args>
std::string concatenate(Args... args) {
    return (args + ...);
}
int main() {
    std::cout << "Concatenated string: " << concatenate(std::string("Hello, "), "world", "!")
              << std::endl;
    // 输出: Concatenated string: Hello, world!
    return 0;
}
```
#### 例子4：检查所有参数是否满足某个条件
```cpp
#include <iostream>
template<typename... Args>
bool all(Args... args) {
    return (... && args);
}
int main() {
    std::cout << std::boolalpha; // 打印布尔值而非0或1
    std::cout << "All true: " << all(true, true, true) << std::endl;
    // 输出: All true: true
    std::cout << "All true: " << all(true, false, true) << std::endl;
    // 输出: All true: false
    return 0;
}
```
#### 例子5：使用初始化列表和折叠表达式创建数组
```cpp
#include <iostream>
#include <array>
template<typename T, typename... Args>
auto createArray(T first, Args... args) {
    return std::array<T, 1 + sizeof...(Args)>{first, args...};
}
int main() {
    auto arr = createArray(1, 2, 3, 4, 5);
    std::cout << "Array elements: ";
    for (auto elem : arr) {
        std::cout << elem << " ";
    }
    std::cout << std::endl;
    // 输出: Array elements: 1 2 3 4 5
    return 0;
}
```
这些例子展示了折叠表达式在处理不同类型和数量的参数时的灵活性和强大功能。通过折叠表达式，我们可以编写更简洁、更直观的代码来处理可变参数。