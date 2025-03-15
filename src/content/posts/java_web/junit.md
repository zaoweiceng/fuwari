---
title: JUnit
published: 2025-03-15
description: 'JUnit是一个Java语言的单元测试框架。它广泛用于测试小型到大型代码库，以确保代码的正确性和稳定性。'
image: './img/20250210131811_1.jpg'
tags: [java, 学习, 测试]
category: 'java'
draft: false 
---

JUnit是一个Java语言的单元测试框架。它广泛用于测试小型到大型代码库，以确保代码的正确性和稳定性。以下是JUnit的详细用法：

### JUnit 基本用法
#### 添加依赖
首先，你需要在项目的`pom.xml`文件中添加JUnit的依赖：
```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>unit-jupiter</artifactId>
    <version>5.9.1</version>
    <scope>test</scope>
</dependency>
```
#### 编写测试类
- 使用`@Test`注解标记测试方法。
- 使用`assertEquals`、`assertTrue`、`assertFalse`等断言方法验证结果。
```java
import org.junit.Test;
import static org.junit.Assert.*;
public class CalculatorTest {
    @Test
    public void testAdd() {
        Calculator calculator = new Calculator();
        int result = calculator.add(2, 3);
        assertEquals(5, result);
    }
}
```
- 命名规则：
  - 类名：XXXXTest
  - 方法名：testXXXX

#### 运行测试

- 使用IDE的测试运行器。
- 使用构建工具（如Maven或Gradle）运行测试。
### JUnit 高级特性
#### 注解



| 注解               | 说明                                                         | 备注                            |
| ------------------ | ------------------------------------------------------------ | ------------------------------- |
| @Test              | 测试类中的方法用它修饰才能成为测试方法，才能启动执行         | 单元测试                        |
| @BeforeEach        | 用来修饰一个实例方法，该方法会在每一个测试方法执行之前执行一次。 | 初始化资源(准备工作)            |
| @AfterEach         | 用来修饰一个实例方法，该方法会在每一个测试方法执行之后执行一次。 | 释放资源(清理工作)              |
| @BeforeAll         | 用来修饰一个静态方法，该方法会在所有测试方法之前只执行一次。 | 初始化资源(准备工作)            |
| @AfterAll          | 用来修饰一个静态方法，该方法会在所有测试方法之后只执行一次。 | 释放资源(清理工作)              |
| @ParameterizedTest | 参数化测试的注解 (可以让单个测试运行多次，每次运行时仅参数不同) | 用了该注解，就不需要@Test注解了 |
| @ValueSource       | 参数化测试的参数来源，赋予测试方法参数                       | 与参数化测试注解配合使用        |
| @DisplayName       | 指定测试类、测试方法显示的名称 （默认为类名、方法名）        |                                 |

```java
public class CalculatorTest {
    @Before
    public void setUp() {
        // 初始化代码
    }
    @Test
    public void testAdd() {
        // 测试代码
    }
    @After
    public void tearDown() {
        // 清理代码
    }
}
```
#### 断言

Assertions下的静态方法：

- `assertEquals(expected, actual)`: 检查两个值是否相等。
- `assertTrue(condition)`: 检查条件是否为真。
- `assertFalse(condition)`: 检查条件是否为假。
- `assertNull(object)`: 检查对象是否为null。
- `assertNotNull(object)`: 检查对象是否不为null。
- `assertSame(expected, actual)`: 检查两个对象是否引用相同。
- `assertNotSame(expected, actual)`: 检查两个对象是否引用不同。

每个方法最后都可以传入一个String类型的message用于错误的提示

### JUnit 的新特性
#### JUnit 5架构
JUnit 5由三个主要模块组成：
- **JUnit Platform**: 为在JVM上启动测试提供基础设施。
- **JUnit Jupiter**: JUnit 5的新测试引擎，提供注解、断言等。
- **JUnit Vintage**: 允许在JUnit 5中运行JUnit 3和JUnit 4的测试。
#### 注解改进
- `@BeforeEach`: 替代了JUnit 4的`@Before`。
- `@AfterEach`: 替代了JUnit 4的`@After`。
- `@BeforeAll`: 替代了JUnit 4的`@BeforeClass`。
- `@AfterAll`: 替代了JUnit 4的`@AfterClass`。
- `@DisplayName`: 为测试类或方法提供显示名称。
- `@Nested`: 允许编写嵌套的测试类。
#### 断言改进
- `assertAll()`: 组合多个断言，即使某个断言失败，也能执行所有断言。
- `assertThrows()`: 检查代码是否抛出特定的异常。
```java
public class CalculatorTest {
    @Test
    public void testAdd() {
        Calculator calculator = new Calculator();
        assertAll(
            () -> assertEquals(2, calculator.add(1, 1)),
            () -> assertEquals(4, calculator.add(2, 2))
        );
    }
    @Test
    public void testDivide() {
        Calculator calculator = new Calculator();
        assertThrows(ArithmeticException.class, () -> calculator.divide(1, 0));
    }
}
```
#### 参数化测试改进
JUnit 5提供了更简洁的参数化测试方式。
```java
public class ParameterizedTest {
    @ParameterizedTest
    @CsvSource({
        "1, 1, 2",
        "2, 2, 4",
        "3, 3, 6"
    })
    public void testAdd(int numberA, int numberB, int expected) {
        Calculator calculator = new Calculator();
        assertEquals(expected, calculator.add(numberA, numberB));
    }
}
```
### 示例

```java
package com.auroralilis;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class demoTest {

    @Test
    public void testDemo(){
        int a = 1;
    }
    @Test
    public void testAsert(){
        int a = 1;
        assertEquals(a,1, "这是一个提示信息");
    }
    @Test
    public void testException(){
        Assertions.assertThrows(ArithmeticException.class, () -> {
            int i = 1, b = 0;
            int i1 = i / b;
        });
    }
    @ParameterizedTest
    @CsvSource({
            "1, 1, 2",
            "2, 2, 4",
            "3, 3, 6"
    })
    public void testAdd(int numberA, int numberB, int expected) {
        assertEquals(expected, (numberA + numberB));
    }
    @DisplayName("输出输入数字")
    @ParameterizedTest
    @ValueSource(ints = {1, 2, 3})
    public void testSub(int numberA) {
        System.out.println(numberA);
    }
}

```

