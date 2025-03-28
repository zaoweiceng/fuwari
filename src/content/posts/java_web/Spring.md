---
title: Spring框架
published: 2025-03-18
description: 'Java Spring Boot是一个基于Spring框架的开源项目，旨在简化Spring应用的创建、配置和部署过程。'
tags: [java, 学习, spring]
category: 'java'
draft: false 
---
## Spring 框架

Java Spring Boot是一个基于Spring框架的开源项目，旨在简化Spring应用的创建、配置和部署过程。以下是Spring Boot中的主要概念：
1. **Spring Boot Starter**：
   - Spring Boot Starter是一系列依赖描述符，用于简化依赖管理。例如，`spring-boot-starter-web`包含了构建Web应用所需的所有依赖。
2. **自动配置（Auto-Configuration）**：
   - Spring Boot通过自动配置尝试根据类路径设置、其他bean和各种属性设置来配置Spring应用。这样可以减少或消除需要手动编写的配置。
3. **嵌入式服务器**：
   - Spring Boot支持嵌入式服务器，如Tomcat、Jetty或Undertow，使得开发人员可以轻松地将应用打包为独立的JAR文件，直接运行。
4. **Spring Boot CLI**：
   - Spring Boot CLI是一个命令行工具，用于快速开发和测试Spring Boot应用。它支持Groovy脚本，使得编写Spring应用更加简洁。
5. **Actuator**：
   - Spring Boot Actuator提供了生产级别的功能，如健康检查、审计、指标收集和HTTP跟踪等，帮助监控和管理应用。
6. **Properties和YAML配置**：
   - Spring Boot允许使用`.properties`或`.yaml`文件来外部化配置，使得配置管理更加灵活。
7. **Profile**：
   - Profile是Spring Boot中用于在不同环境中配置应用的方式。例如，可以有不同的配置文件用于开发、测试和生产环境。
8. **Spring Boot Starter Parent**：
   - `spring-boot-starter-parent`是一个特殊的 Starter，它提供了默认的配置，如Maven依赖管理、插件配置等。
9. **Spring Boot Maven/Gradle插件**：
   - 这些插件提供了打包、运行和测试Spring Boot应用的便捷方式。
10. **主类（Main Class）**：
    - Spring Boot应用通常包含一个主类，使用`@SpringBootApplication`注解，并包含一个`main`方法来启动应用。
11. **SpringApplicationBuilder**：
    - `SpringApplicationBuilder`用于以编程方式配置和启动Spring Boot应用。
12. **事件和监听器**：
    - Spring Boot提供了事件和监听器机制，允许在应用的生命周期中执行特定操作。
13. **安全性（Security）**：
    - Spring Boot集成了Spring Security，提供了安全认证和授权的功能。
14. **数据访问**：
    - Spring Boot支持多种数据访问技术，如JPA、JDBC、MongoDB等，并通过Starter简化配置。
15. **Thymeleaf**：
    - Thymeleaf是Spring Boot推荐的模板引擎，用于构建动态Web页面。
16. **RESTful API**：
    - Spring Boot使得创建RESTful API变得简单，通过Controller来处理HTTP请求。
17. **AOP（面向切面编程）**：
    - Spring Boot支持AOP，允许开发者在不修改核心代码的情况下添加新功能。
18. **测试**：
    - Spring Boot提供了丰富的测试支持，包括单元测试、集成测试和端到端测试。
19. **日志**：
    - Spring Boot集成了日志框架，如Logback、Log4j2等，提供了灵活的日志配置。
20. **国际化（i18n）**：
    - Spring Boot支持国际化，允许根据用户的语言和地区设置显示不同的内容。
这些概念共同构成了Spring Boot框架，使得Java开发更加高效、简洁和强大。

## 核心概念

- **控制反转：** Inversion Of Control，简称**IOC**。对象的创建控制权由程序自身转移到外部（容器），这种思想称为控制反转。
  - 对象的创建权由程序员主动创建转移到容器(由容器创建、管理对象)。这个容器称为：IOC容器或Spring容器。
  - 
- **依赖注入：** Dependency Injection，简称**DI**。容器为应用程序提供运行时，所依赖的资源，称之为依赖注入。
  - 程序运行时需要某个资源，此时容器就为其提供这个资源。
  - 例：EmpController程序运行时需要EmpService对象，Spring容器就为其提供并注入EmpService对象。



- **bean对象：**IOC容器中创建、管理的对象，称之为：bean对象。



要把某个对象交给IOC容器管理，需要在类上添加一个注解：**@Component**

而Spring框架为了更好的标识web应用程序开发当中，bean对象到底归属于哪一层，又提供了@Component的衍生注解：

| 注解        | 说明                 | 位置                                              |
| ----------- | -------------------- | ------------------------------------------------- |
| @Component  | 声明bean的基础注解   | 不属于以下三类时，用此注解                        |
| @Controller | @Component的衍生注解 | 标注在控制层类上                                  |
| @Service    | @Component的衍生注解 | 标注在业务层类上                                  |
| @Repository | @Component的衍生注解 | 标注在数据访问层类上（由于与mybatis整合，用的少） |