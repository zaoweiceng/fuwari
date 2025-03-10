---
title: c++程序编译
published: 2024-05-09
description: '使用g++对c++程序编译，基本的命令介绍'
image: './img/cover.jpeg'
tags: [c++, 学习]
category: 'cpp'
draft: false 
---

## gcc/g++

`g++` 是 GNU 编译器集合（GNU Compiler Collection）中的 C++ 编译器。以下是一些基本的 `g++` 使用方法：
1. **编译一个 C++ 源文件**:
   ```sh
   g++ -o output_file source_file.cpp
   ```
   这条命令会编译 `source_file.cpp` 并生成名为 `output_file` 的可执行文件。
   
2. **编译多个 C++ 源文件**:
   ```sh
   g++ -o output_file source1.cpp source2.cpp
   ```
   如果你有多个源文件，可以一次性编译它们。
   
3. **链接外部库**:
   ```sh
   g++ -o output_file source_file.cpp -llibrary
   ```
   如果你的程序需要链接一个外部库（例如 `-lstdc++` 是 C++ 标准库），可以在命令中加入 `-l` 选项后跟库的名字。
   
4. **指定编译器的搜索路径**:
   ```sh
   g++ -o output_file source_file.cpp -I/include/path
   ```
   使用 `-I` 选项可以指定头文件的搜索路径。
   
5. **启用 C++11 或更高版本的标准**:
   ```sh
   g++ -o output_file source_file.cpp -std=c++11
   ```
   如果你使用了 C++11 或更高版本的特性，需要使用 `-std` 选项指定版本。
   
6. **优化代码**:
   ```sh
   g++ -o output_file source_file.cpp -O2
   ```
   `-O0` 不优化。
   
   `-O1` O或-01对程序做部分编译优化，对于大函数，优化编译占用稍微多的时间和相当大的内存。使用本项优化，编译器会尝试减小生成代码的尺寸，以及缩短执行时间，但并不执行需要占用大量编译时间的优化。
   
   `-O2` 比O1更高级的选项，进行更多的优化。与O1比较而言，O2优化增加了编译时间的基础上，提高了生成代码的执行效率。**推荐使用**
   
   `-O3`比O2更优 。
   
7. **调试信息**:
   
   ```sh
   g++ -o output_file source_file.cpp -g
   ```
   使用 `-g` 选项可以在编译时包含调试信息，这样你可以使用调试器（如 gdb）进行调试。
   
8. **警告和错误**:
   ```sh
   g++ -o output_file source_file.cpp -Wall -Werror
   ```
   `-Wall` 选项启用所有警告，`-Werror` 将警告视为错误，使得编译在遇到警告时停止。
   
9. **运行程序**:
   ```sh
   ./output_file
   ```
   编译完成后，可以通过在命令行中输入可执行文件的名称来运行程序。
这些是 `g++` 的一些基本用法。根据你的具体需求，你可能会使用更多的选项和参数。`g++` 的 man 页面提供了完整的选项列表和详细说明，可以通过运行 `man g++` 来查看。

# 静态库

在 C++ 中，静态库（Static Library）是一种包含编译后的代码的文件，可以在链接阶段与其他源代码一起被链接到可执行文件中。静态库通常以 `.a`（在 Linux 和 macOS 上）或 `.lib`（在 Windows 上）文件扩展名结尾。
以下是创建和使用静态库的基本步骤：
### 创建静态库
1. **编写源代码**:
   创建一个或多个源文件（`.cpp`）和一个头文件（`.h`）。
2. **编译源文件**:
   使用 `g++`（或其他编译器）将源文件编译成目标文件（`.o`）。
   ```sh
   g++ -c source_file1.cpp source_file2.cpp
   ```
   `-c` 选项告诉编译器只编译源文件，不进行链接。
3. **创建静态库**:
   
   ```sh
   g++ -c -o libXXX.a source_file.cpp
   ```
### 使用静态库
1. **包含头文件**:
   在使用静态库的源代码中包含库的头文件。
   ```cpp
   #include "libraryname.h"
   ```
2. **编译源代码**:
   编译源代码时，指定静态库的路径和名称。
   ```sh
   g++ -o output_file source_file.cpp -L/path/to/library -llibraryname
   ```
   `-L` 选项指定静态库的路径，`-l` 选项指定静态库的名称（去掉 `lib` 前缀和 `.a` 或 `.lib` 后缀）。
3. **链接静态库**:
   在链接阶段，链接器会从指定的路径中查找静态库，并将其链接到可执行文件中。
### 示例
假设你有以下文件：
- `library.h`
- `library.cpp`
- `main.cpp`
首先，编译 `library.cpp`：
```sh
g++ -c -o liblibrary.a library.cpp
```
接下来，编译 `main.cpp` 并链接静态库：
```sh
g++ -o main main.cpp -L. -llibrary
```
最后，运行可执行文件：
```sh
./main
```
在这个例子中，`.` 表示当前目录，`-llibrary` 指定了静态库 `liblibrary.a`。
请注意，这些步骤可能会根据你的操作系统和编译器有所不同。以上步骤适用于类 Unix 系统，如 Linux 和 macOS。在 Windows 上，你可能需要使用不同的命令和工具。

# 动态库

在使用g++编译动态库时，需要遵循几个步骤。以下是一个简单的指南：
1. **编写源代码**：
   动态库由源代码文件组成，通常是以`.cpp`为扩展名的C++源文件和以`.h`为扩展名的头文件。
2. **创建编译命令**：
   使用g++编译器，可以使用以下命令来编译一个动态库：
   
   ```bash
   g++ -shared -fPIC -o libyourlibrary.so yoursourcefile.cpp
   ```
   
   这里：
   - `-shared`：告诉g++生成一个共享库。
   - `-fPIC`：表示生成位置无关代码，这是创建动态库所必需的。
   - `-o`：指定输出文件名。在Linux系统中，动态库通常以`lib<name>.so`的格式命名。
   - `yoursourcefile.cpp`：这是的源代码文件。
3. **包含头文件**：
   如果动态库中有函数声明在头文件中，那么在使用这个库的时候需要包含这些头文件。
4. **链接动态库**：
   当编译使用这个动态库的代码时，需要指定动态库的位置，可以使用`-L`选项来指定库文件所在的目录，使用`-l`选项来指定库的名称（去掉`lib`前缀和`.so`后缀）。
   例如，如果的动态库叫`libyourlibrary.so`，在`/usr/local/lib`目录下，那么编译命令可能如下所示：
   ```bash
   g++ -o yourprogram yourprogram.cpp -L/usr/local/lib -lyourlibrary
   ```
5. **设置库路径**：
   在运行使用动态库的程序之前，可能需要确保动态链接器知道库的位置。这可以通过设置`LD_LIBRARY_PATH`环境变量来实现：
   ```bash
   export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
   ```
6. **运行程序**：
   最后，可以通过输入程序名称来运行程序。如果一切设置正确，程序将能够找到并使用动态库。
以上步骤是编译和使用动态库的基本流程。在具体实施时，可能需要根据具体环境和需求进行适当的调整。