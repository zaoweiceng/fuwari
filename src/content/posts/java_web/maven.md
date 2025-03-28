---
title: Maven
published: 2025-03-27
description: 'Maven-包管理工具'
tags: [java, 学习]
image: './img/20250127222505_1.jpg'
category: 'java'
draft: false 
---
# Maven的继承与聚合
### 一、继承

Maven继承是指在一个项目中，子项目可以继承父项目的配置信息，这样可以避免在多个项目中重复配置相同的信息，提高项目的可维护性。
**继承的原理：**

* 子项目的`pom.xml`文件中通过`<parent>`标签指定父项目的坐标（groupId、artifactId、version）。
* 子项目可以继承父项目的依赖、插件、属性等配置。
* 子项目可以覆盖父项目的配置。
**继承的使用场景：**
* 多个项目共享相同的依赖、插件或属性配置。
* 提取公共配置，简化项目结构。

```xml
<parent>
    <groupId>...</groupId>
    <artifactId>...</artifactId>
    <version>...</version>
    <relativePath>....</relativePath>
</parent>
```

### 二、聚合

  Maven聚合是指在一个主项目中，将多个子项目整合在一起进行构建。通过聚合，可以一次性构建多个项目，提高构建效率。
  **聚合的原理：**
* 在主项目的`pom.xml`文件中通过`<modules>`标签列出所有子项目的相对路径。
* Maven在构建主项目时，会自动构建所有列出的子项目。
**聚合的使用场景：**
* 一个大项目由多个子项目组成，需要统一构建。
* 管理多个相关的项目，方便构建和发布。
**三、实际使用示例**
**1. 继承示例**
假设有一个父项目`parent-project`，其中定义了一些公共的依赖和属性。有两个子项目`moduleA`和`moduleB`，它们都需要继承父项目的配置。
**父项目`parent-project/pom.xml`：**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>5.2.9.RELEASE</version>
        </dependency>
    </dependencies>
</project>
```
**子项目`moduleA/pom.xml`：**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.example</groupId>
        <artifactId>parent-project</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <artifactId>moduleA</artifactId>
    <!-- 可以添加模块特有的依赖和配置 -->
</project>
```
**子项目`moduleB/pom.xml`：**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.example</groupId>
        <artifactId>parent-project</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <artifactId>moduleB</artifactId>
    <!-- 可以添加模块特有的依赖和配置 -->
</project>
```
**2. 聚合示例**
假设有一个主项目`main-project`，其中包含了两个子项目`moduleA`和`moduleB`。我们希望一次性构建这两个子项目。
**主项目`main-project/pom.xml`：**
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>main-project</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    <modules>
        <module>moduleA</module>
        <module>moduleB</module>
    </modules>
</project>
```
**子项目`moduleA/pom.xml`和`moduleB/pom.xml`**的配置与继承示例中的相同。
**构建主项目：**
在主项目的目录下执行`mvn clean install`，Maven会自动构建`moduleA`和`moduleB`。