---
title: Spring框架原理
published: 2025-03-28
description: 'Java Spring Boot深入学习。'
tags: [java, 学习, spring]
image: './img/20250123204146_1.jpg'
category: 'java'
draft: false 
---

## spring的配置

在Spring Boot项目中，`application.yml`（或`application.properties`）是用于配置应用程序各种属性的主要文件。YAML是“YAML Ain't Markup Language”（递归缩写为“YAML不是标记语言”）的缩写，它是一种直观的能够被电脑读取的数据序列化格式。YAML的语法简洁明了，非常适合用来表示配置信息。
以下是一些常见的`application.yml`配置项及其说明：

### 服务端口配置

```yaml
server:
  port: 8080
```

这行配置设置了Spring Boot应用的运行端口，默认是8080。

### 数据源配置

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/mydb
    username: user
    password: pass
    driver-class-name: com.mysql.cj.jdbc.Driver
```

这里配置了数据库的连接信息，包括URL、用户名、密码和驱动类名。

### JPA配置

```yaml
spring:
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQL5InnoDBDialect
```

这些配置用于设置JPA的相关属性，比如自动更新数据库表结构、显示SQL语句以及设置Hibernate方言。

### 应用名称和端口

```yaml
spring:
  application:
    name: my-spring-boot-app
```

这里设置了应用的名称，可以在分布式系统中用于服务发现。

### 日志配置

```yaml
logging:
  level:
    root: INFO
    org.springframework.web: DEBUG
    com.mycompany.mypackage: TRACE
```

这里配置了日志级别，可以针对不同的包设置不同的日志级别。

### 配置文件加载

```yaml
spring:
  profiles:
    active: dev
```

这行配置表示激活开发环境的配置文件，可以通过设置不同的profiles来加载不同的配置文件，如`application-dev.yml`、`application-test.yml`等。

### Redis配置

```yaml
spring:
  redis:
    host: localhost
    port: 6379
```

这里配置了Redis的连接信息。

### 安全配置

```yaml
security:
  user:
    name: admin
    password: admin
```

这里配置了安全用户的信息，但通常建议使用更安全的方式来存储和管理用户信息。

### Actuator配置

```yaml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```

这里配置了Actuator的端点暴露，`"*"`表示暴露所有端点。

### 自定义配置

你还可以在`application.yml`中添加自定义配置，并通过`@Value`注解或`Environment`对象在代码中获取这些配置值。

```yaml
myapp:
  custom-property: value
```

在Java代码中获取：

```java
@Component
public class MyBean {
    @Value("${myapp.custom-property}")
    private String customProperty;
    // ...
}
```

或者使用`Environment`：

```java
@Autowired
private Environment env;
public String getCustomProperty() {
    return env.getProperty("myapp.custom-property");
}
```

### 配置优先级

SpringBoot项目当中支持的三类配置文件：

- application.properties
- application.yml
- application.yaml

**配置文件优先级排名（从高到低）：**

1. properties配置文件
2. yml配置文件
3. yaml配置文件

在SpringBoot项目当中除了以上3种配置文件外，SpringBoot为了增强程序的扩展性，除了支持配置文件的配置方式以外，还支持另外两种常见的配置方式：

Java系统属性配置 （格式： -Dkey=value）

```YAML
-Dserver.port=9000
```

命令行参数 （格式：--key=value）

```YAML
--server.port=10010
```

**五种配置方式的优先级：** 命令行参数 >  系统属性参数 > properties参数 > yml参数 > yaml参数

### 注意事项

- YAML对缩进非常敏感，必须使用空格进行缩进，不能使用Tab。
- 字符串不需要引号，但若字符串中包含特殊字符（如冒号、逗号等），则需要使用引号。
- 列表可以使用短横线表示。
`application.yml`的配置非常灵活，可以根据项目的具体需求进行定制。以上只是列举了一些常见的配置项，实际上Spring Boot支持大量的配置选项，可以根据需要进行查阅和设置。

## Bean

在Spring中，Bean是指由Spring容器管理的对象。这些对象在Spring容器中创建、配置和管理。Bean可以是任何类型的对象，例如POJO（Plain Old Java Object）、数据访问对象（DAO）等。

### Bean的作用域

在Spring中支持五种作用域，后三种在web环境才生效：

**Singleton**：**默认**作用域，每个Spring容器中只有一个Bean实例。
**Prototype**：每次请求创建一个新的Bean实例。
**Request**：每个HTTP请求创建一个新的Bean实例。
**Session**：每个会话创建一个新的Bean实例。
**GlobalSession**：portlet环境中使用，全局会话范围内创建一个新的Bean实例。

### Bean的生命周期

**实例化**：Spring容器根据Bean定义创建Bean实例。
**属性赋值**：Spring容器为Bean实例的属性赋值。
**初始化**：调用Bean的初始化方法（如`@PostConstruct`注解的方法）。
**使用**：Bean可以被应用程序使用。
**销毁**：调用Bean的销毁方法（如`@PreDestroy`注解的方法），然后释放资源。

### Bean的配置方式

**XML配置**：使用XML文件定义Bean及其属性。
**注解配置**：使用注解（如`@Component`、`@Service`、`@Repository`等）定义Bean。
**Java配置**：使用Java类（使用`@Configuration`注解）定义Bean。

### Bean的依赖注入

Spring支持多种依赖注入方式：

**构造器注入**：通过构造器将依赖注入到Bean中。
**setter注入**：通过setter方法将依赖注入到Bean中。
**字段注入**：直接在字段上注入依赖（不推荐使用）。

### Bean的自动装配

Spring可以自动装配Bean的依赖。自动装配的几种模式：

**no**：不进行自动装配。
**byName**：根据Bean的名字进行自动装配。
**byType**：根据Bean的类型进行自动装配。
**constructor**：类似于byType，但适用于构造器注入。
**autoDetect**：Spring会尝试通过构造器或byType进行自动装配。

### Bean的命名

默认情况下，Spring会根据类名生成Bean的名字（如`MyService`会生成`myService`）。
可以使用`@Component("myBean")`等注解指定Bean的名字。
在XML配置中，可以使用`id`或`name`属性指定Bean的名字。

### Bean的别名

可以为Bean定义别名，以便在多个地方引用同一个Bean。
在XML配置中，使用`alias`标签定义别名。
在注解配置中，使用`@AliasFor`注解定义别名。

### Bean的继承

Bean定义可以继承，子Bean可以继承父Bean的属性和方法。
在XML配置中，使用`parent`属性指定父Bean。
在Java配置中，使用`@Bean`方法的参数指定父Bean。

### Bean的依赖

**强依赖**：Bean在创建时就必须注入依赖。
**弱依赖**：Bean在运行时可以没有依赖。

### Bean的延迟初始化

可以配置Bean为延迟初始化，即在使用时才创建Bean实例。
在XML配置中，使用`lazy-init="true"`属性。
在注解配置中，使用`@Lazy`注解。

### Bean的 profiles

可以定义多个Bean配置文件，根据不同的环境（如开发、测试、生产）选择不同的配置。
在XML配置中，使用`profile`属性。
在注解配置中，使用`@Profile`注解。

### Bean的描述

可以为Bean添加描述信息，以便更好地理解Bean的用途。
在XML配置中，使用`description`属性。
在注解配置中，使用`@Description`注解。

### 第三方Bean

我们项目开发当中，还有一种情况就是这个类它不是我们自己编写的，而是我们引入的第三方依赖当中提供的，那么此时我们是无法使用 `@Component` 及其衍生注解来声明bean的，此时就需要使用**@Bean**注解来声明bean 了。

示例:

- 在启动类中直接声明这个Bean。比如：我们可以将我们之前使用的阿里云OSS操作的工具类，基于@Bean注解的方式来声明Bean。

```java
@ServletComponentScan
@EnableScheduling
@SpringBootApplication
public class TliasWebManagementApplication {

    public static void main(String[] args) {
        SpringApplication.run(TliasWebManagementApplication.class, args);
    }

    @Bean
    public AliyunOSSOperator aliyunOSSOperator(AliyunOSSProperties ossProperties) {
        return new AliyunOSSOperator(ossProperties);
    }
}
```

若要管理的第三方 bean 对象，建议对这些bean进行集中分类配置，可以通过 `@Configuration` 注解声明一个配置类。

```java
@Configuration
public class OSSConfig {
    @Bean
    public AliyunOSSOperator aliyunOSSOperator(AliyunOSSProperties ossProperties) {
        return new AliyunOSSOperator(ossProperties);
    }
}
```

- 通过`@Bean`注解的name 或 value属性可以声明bean的名称，如果不指定，默认bean的名称就是方法名。
- 如果第三方bean需要依赖其他bean对象，直接在bean定义方法中设置形参即可，容器会根据类型自动装配。

## Spring boot的底层原理

Spring是目前世界上最流行的Java框架，它可以帮助我们更加快速、更加容易的来构建Java项目。而在Spring家族当中提供了很多优秀的框架，而所有的框架都是基于一个基础框架的SpringFramework(也就是Spring框架)。但是如果我们直接基于Spring框架进行项目的开发，会比较繁琐。

这个繁琐主要体现在两个地方：

1. 在pom.xml中依赖配置比较繁琐，在项目开发时，需要自己去找到对应的依赖，还需要找到依赖它所配套的依赖以及对应版本，否则就会出现版本冲突问题。
2. 在使用Spring框架进行项目开发时，需要在Spring的配置文件中做大量的配置，这就造成Spring框架入门难度较大，学习成本较高。

SpringBoot框架之所以使用起来更简单更快捷，是因为SpringBoot框架底层提供了两个非常重要的功能：一个是起步依赖，一个是自动配置。

### 起步依赖

假如我们没有使用SpringBoot，用的是Spring框架进行web程序的开发，此时我们就需要引入web程序开发所需要的一些依赖。

当我们引入了 spring-boot-starter-web 之后，maven会通过**依赖传递**特性，将web开发所需的常见依赖都传递下来。

- 在SpringBoot给我们提供的这些起步依赖当中，已提供了当前程序开发所需要的所有的常见依赖([官网地址](https://docs.spring.io/spring-boot/docs/2.7.7/reference/htmlsingle/#using.build-systems.starters))。
- 比如：springboot-starter-web，这是web开发的起步依赖，在web开发的起步依赖当中，就集成了web开发中常见的依赖：json、web、webmvc、tomcat等。我们只需要引入这一个起步依赖，其他的依赖都会自动的通过Maven的依赖传递进来。

### 自动配置

SpringBoot的自动配置就是当spring容器启动后，一些配置类、bean对象就自动存入到了IOC容器中，不需要我们手动去声明，从而简化了开发，省去了繁琐的配置操作。

- SpringBoot项目中的`@SpringBootApplication`注解，具有包扫描的作用，但是它只会扫描启动类所在的当前包以及子包。
- 方案1：`@ComponentScan` 组件扫描
- 方案2：`@Import` 导入（使用`@Import`导入的类会被Spring加载到IOC容器中）

方案一:**使用@Import导入普通类**

```java
@SpringBootApplication
@ComponentScan({"com.itheima","com.example"}) //指定要扫描的包
public class SpringbootWebConfigApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootWebConfigApplication.class, args);
    }
}
```

方案二:**使用@Import导入配置类**

A:

```java
@Import(TokenParser.class) //导入的类会被Spring加载到IOC容器中
@SpringBootApplication
public class SpringbootWebConfigApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootWebConfigApplication.class, args);
    }
}
```

B:

配置类

```java
@Configuration
public class HeaderConfig {
    @Bean
    public HeaderParser headerParser(){
        return new HeaderParser();
    }

    @Bean
    public HeaderGenerator headerGenerator(){
        return new HeaderGenerator();
    }
}
```

启动类:

```java
@Import(HeaderConfig.class) //导入配置类
@SpringBootApplication
public class SpringbootWebConfig2Application {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootWebConfig2Application.class, args);
    }
}
```

测试:

```java
@SpringBootTest
public class AutoConfigurationTests {
    @Autowired
    private ApplicationContext applicationContext;

    @Test
    public void testHeaderParser(){
        System.out.println(applicationContext.getBean(HeaderParser.class));
    }

    @Test
    public void testHeaderGenerator(){
        System.out.println(applicationContext.getBean(HeaderGenerator.class));
    }
    
    //省略其他代码...
}
```

方案三:**使用@Import导入ImportSelector接口实现类**

ImportSelector接口实现类

```java
public class MyImportSelector implements ImportSelector {
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        //返回值字符串数组（数组中封装了全限定名称的类）
        return new String[]{"com.example.HeaderConfig"};
    }
}
```

启动类

```java
@Import(MyImportSelector.class) //导入ImportSelector接口实现类
@SpringBootApplication
public class SpringbootWebConfig2Application {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootWebConfig2Application.class, args);
    }
}
```

方案四:**使用第三方依赖提供的 @EnableXxxxx注解**

第三方提供的注解

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Import(MyImportSelector.class)//指定要导入哪些bean对象或配置类
public @interface EnableHeaderConfig { 
}
```

在使用时只需在启动类上加上@EnableXxxxx注解即可

```java
@EnableHeaderConfig  //使用第三方依赖提供的Enable开头的注解
@SpringBootApplication
public class SpringbootWebConfig2Application {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootWebConfig2Application.class, args);
    }
}
```

自动配置原理源码入口就是 `@SpringBootApplication` 注解，在这个注解中封装了3个注解，分别是：

- @SpringBootConfiguration
  - 声明当前类是一个配置类
- @ComponentScan
  - 进行组件扫描（SpringBoot中默认扫描的是启动类所在的当前包及其子包）
- @EnableAutoConfiguration
  - 封装了@Import注解（Import注解中指定了一个ImportSelector接口的实现类）
  - 在实现类重写的selectImports()方法，读取当前项目下所有依赖jar包中`META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports`两个文件里面定义的配置类（配置类中定义了@Bean注解标识的方法）。

当SpringBoot程序启动时，就会加载配置文件当中所定义的配置类，并将这些配置类信息(类的全限定名)封装到String类型的数组中，最终通过@Import注解将这些配置类全部加载到Spring的IOC容器中，交给IOC容器管理。

### Conditional

**@Conditional注解：**

- 作用：按照一定的条件进行判断，在满足给定条件后才会注册对应的bean对象到Spring的IOC容器中。
- 位置：方法、类
- @Conditional本身是一个父注解，派生出大量的子注解：
  - @ConditionalOnClass：判断环境中有对应字节码文件，才注册bean到IOC容器。
  - @ConditionalOnMissingBean：判断环境中没有对应的bean(类型或名称)，才注册bean到IOC容器。
  - @ConditionalOnProperty：判断配置文件中有对应属性和值，才注册bean到IOC容器。

```java
@Configuration
public class HeaderConfig {

    @Bean
    @ConditionalOnClass(name="io.jsonwebtoken.Jwts")//环境中存在指定的这个类，才会将该bean加入IOC容器
    public HeaderParser headerParser(){
        return new HeaderParser();
    }
    
    //省略其他代码...
}
```

## IOC

IOC，也就是控制反转，是Spring框架的核心之一。它的主要思想是将对象的创建和依赖注入的责任交给容器来处理，而不是由对象自己控制。这样可以降低组件之间的耦合度，提高代码的灵活性和可维护性。

**IOC容器的核心组件**
在Spring中，IOC容器的实现主要依赖于以下几个核心组件：

1. **BeanDefinition**：表示Bean的元数据，包括Bean的类名、属性、依赖关系等。
2. **BeanFactory**：IOC容器的顶层接口，负责创建和管理Bean。
3. **ApplicationContext**：BeanFactory的子接口，提供了更高级的功能，如事件发布、国际化等。
4. **BeanDefinitionRegistry**：用于注册BeanDefinition。
5. **BeanPostProcessor**：用于在Bean初始化前后进行自定义处理。
6. **Autowired**：用于自动注入依赖的Bean。
**IOC容器的过程**
**容器启动**
   - 创建IOC容器实例，通常是通过调用`ClassPathXmlApplicationContext`或`AnnotationConfigApplicationContext`的构造方法。
   - 加载配置文件或配置类，解析得到BeanDefinition。
   - 将解析得到的BeanDefinition注册到BeanDefinitionRegistry中。
**Bean的创建**
   - 当需要获取Bean时，调用BeanFactory的`getBean`方法。
   - `getBean`方法会先从缓存中查找是否已经存在该Bean的实例，如果不存在，则调用`doCreateBean`方法创建新的实例。
   - 在`doCreateBean`方法中，首先通过反射创建Bean的实例，然后设置Bean的属性值，最后调用`initializeBean`方法进行初始化。
**依赖注入**
   - 在设置Bean的属性值时，如果属性值是通过Autowired注解标记的，则会自动注入依赖的Bean。
   - 自动注入的过程是通过AutowiredAnnotationBeanPostProcessor来完成的，它会查找符合条件的Bean并注入到当前Bean中。
**生命周期管理**
   - 在`initializeBean`方法中，会调用Bean的`afterPropertiesSet`方法（如果实现了InitializingBean接口）或自定义的初始化方法。
   - 当容器关闭时，会调用Bean的`destroy`方法（如果实现了DisposableBean接口）或自定义的销毁方法。

Spring框架中的IOC（控制反转）是其核心功能之一，通过将对象的创建与依赖管理交给容器，降低了组件间的耦合。以下从源码角度逐步分析IOC的底层实现：

**一、IOC容器的核心接口**
Spring的IOC容器主要由两个接口定义：
**`BeanFactory`**：基础容器，提供基本的Bean管理功能（如`getBean`）。
**`ApplicationContext`**：扩展了`BeanFactory`，支持国际化、事件传播等企业级功能。

**二、BeanDefinition的加载与注册**
IOC容器通过**BeanDefinition**描述Bean的元数据（如类名、作用域、依赖等）。源码关键类：
**`BeanDefinitionReader`**（如`XmlBeanDefinitionReader`）：解析配置文件（XML/注解），生成`BeanDefinition`。
**`DefaultListableBeanFactory`**：核心实现类，内部通过`Map<String, BeanDefinition>`存储Bean定义。

```java
// 示例：加载XML配置
ClassPathResource resource = new ClassPathResource("beans.xml");
XmlBeanDefinitionReader reader = new XmlBeanDefinitionReader(beanFactory);
reader.loadBeanDefinitions(resource);
```

---

**三、Bean的实例化与依赖注入**
实例化Bean
当调用`getBean()`时，容器通过反射创建Bean实例，源码入口：
**`AbstractAutowireCapableBeanFactory#createBeanInstance()`**：

  ```java
  protected BeanWrapper createBeanInstance(String beanName, RootBeanDefinition mbd, @Nullable Object[] args) {
    // 通过构造函数或工厂方法实例化Bean
    return instantiateBean(beanName, mbd, args);
  }
  ```

依赖注入（DI）

依赖注入通过**`populateBean()`**完成，处理`@Autowired`、`@Resource`等注解：
**`AutowiredAnnotationBeanPostProcessor`**：处理字段/方法的自动注入。
注入流程：

  ```java
  protected void populateBean(String beanName, RootBeanDefinition mbd, BeanWrapper bw) {
    // 通过后处理器注入依赖（如AutowiredAnnotationBeanPostProcessor）
    for (BeanPostProcessor bp : getBeanPostProcessors()) {
      bp.postProcessProperties(pvs, bw.getWrappedInstance(), beanName);
    }
  }
  ```

**四、解决循环依赖**
Spring通过**三级缓存**解决单例Bean的循环依赖：
**`singletonFactories`**：存储Bean的`ObjectFactory`（用于提前暴露半成品Bean）。
**`earlySingletonObjects`**：存储提前暴露的Bean（尚未完成初始化）。
**`singletonObjects`**：存储完全初始化的单例Bean。

源码关键方法：
**`DefaultSingletonBeanRegistry#getSingleton()`**：

  ```java
  protected Object getSingleton(String beanName, boolean allowEarlyReference) {
    Object singletonObject = this.singletonObjects.get(beanName);
    if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
      singletonObject = this.earlySingletonObjects.get(beanName);
      if (singletonObject == null && allowEarlyReference) {
        synchronized (this.singletonObjects) {
          // 从singletonFactories获取ObjectFactory并创建早期引用
          ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
          if (singletonFactory != null) {
            singletonObject = singletonFactory.getObject();
            this.earlySingletonObjects.put(beanName, singletonObject);
            this.singletonFactories.remove(beanName);
          }
        }
      }
    }
    return singletonObject;
  }
  ```

---

**五、Bean的生命周期管理**
Bean的生命周期由多个**BeanPostProcessor**扩展：
**初始化前**：调用`postProcessBeforeInitialization()`（如`@PostConstruct`处理）。
**执行初始化方法**：如`InitializingBean#afterPropertiesSet()`或自定义的`init-method`。
**初始化后**：调用`postProcessAfterInitialization()`（如AOP代理的生成）。

源码入口：
**`AbstractAutowireCapableBeanFactory#initializeBean()`**：
  
  ```java
  protected Object initializeBean(String beanName, Object bean, RootBeanDefinition mbd) {
    // 执行BeanPostProcessor前置处理
    wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
    // 执行初始化方法
    invokeInitMethods(beanName, wrappedBean, mbd);
    // 执行BeanPostProcessor后置处理（如生成AOP代理）
    wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
    return wrappedBean;
  }
  ```

---

**六、注解驱动的IOC实现**
注解配置（如`@Component`、`@Autowired`）通过以下处理器处理：
**`ConfigurationClassPostProcessor`**：解析`@Configuration`类，注册Bean定义。
**`AutowiredAnnotationBeanPostProcessor`**：处理字段和方法的自动注入。

**七、设计模式的应用**
**工厂模式**：`BeanFactory`作为基础容器。
**模板方法模式**：`AbstractApplicationContext#refresh()`定义了容器的初始化流程。
**观察者模式**：`ApplicationEvent`和`ApplicationListener`实现事件驱动。

### 底层原理

Spring 的 **IOC 容器**本质上是一个管理对象生命周期和依赖关系的复杂系统，其底层实现基于以下几个核心机制：

---

**一、存储结构：Bean 定义与实例的存放**
Spring 容器中存储的并不是直接的对象实例，而是 **Bean 定义（`BeanDefinition`）** 和 **Bean 实例**，分为两个阶段管理：

1. **Bean 定义的存储**

- **底层数据结构**：一个全局的 **ConcurrentHashMap**。
- **关键类**：`DefaultListableBeanFactory`（IOC 容器的核心实现类）。
- **字段**：`beanDefinitionMap`。

  ```java
  public class DefaultListableBeanFactory extends ... {
      // 存储所有 BeanDefinition 的 Map（Key: Bean名称, Value: BeanDefinition）
      private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(256);
  }
  ```

- **用途**：保存从 XML/注解配置中解析出的 Bean 元数据（如类名、作用域、依赖关系等）。

1. **Bean 实例的存储**

- **单例 Bean 缓存**：通过三级缓存实现（解决循环依赖）。
  - **`singletonObjects`**：完全初始化的单例 Bean（Map 结构）。
  - **`earlySingletonObjects`**：提前暴露的“半成品” Bean（解决循环依赖）。
  - **`singletonFactories`**：存放创建 Bean 的工厂对象（`ObjectFactory`）。
  
  ```java
  public class DefaultSingletonBeanRegistry {
      // 一级缓存：完全初始化的单例 Bean
      private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
      // 二级缓存：提前暴露的 Bean（未完成依赖注入）
      private final Map<String, Object> earlySingletonObjects = new ConcurrentHashMap<>(16);
      // 三级缓存：Bean 工厂（用于生成提前暴露的 Bean）
      private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);
  }
  ```

---

**二、Bean 的存取流程**
**注册 Bean 定义**

- **步骤**：
  1. 解析 XML/注解配置，生成 `BeanDefinition`。
  2. 将 `BeanDefinition` 注册到 `beanDefinitionMap` 中。
- **源码入口**：`DefaultListableBeanFactory#registerBeanDefinition`。

  ```java
  public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition) {
      this.beanDefinitionMap.put(beanName, beanDefinition);
  }
  ```

1. **获取 Bean 实例（`getBean()` 流程）**

当调用 `getBean()` 时，容器按以下步骤处理：
**检查单例缓存**：

   ```java
   Object sharedInstance = getSingleton(beanName);
   if (sharedInstance != null) {
       return sharedInstance; // 直接返回已初始化的 Bean
   }
   ```

**创建 Bean 实例**：
**反射创建对象**：通过 `BeanDefinition` 中的类名，使用反射（`Class.newInstance()` 或构造函数）创建对象。
**依赖注入**：通过 `populateBean()` 方法注入属性（处理 `@Autowired`、`@Resource` 等注解）。
**初始化**：执行 `InitializingBean#afterPropertiesSet()` 和自定义的 `init-method`。
**缓存 Bean**：
单例 Bean 会被放入 `singletonObjects` 中，后续直接从缓存获取。


**三、核心设计思想**
1. **延迟初始化（Lazy Init）**

- 默认情况下，单例 Bean 在容器启动时初始化（非延迟），但可通过 `@Lazy` 或配置设置为延迟加载。
- **源码入口**：`AbstractBeanFactory#preInstantiateSingletons`。

2. **循环依赖的解决**

- **三级缓存机制**：
  1. **A 创建时**：将自己（半成品）通过 `ObjectFactory` 放入 `singletonFactories`。
  2. **B 依赖 A**：从 `singletonFactories` 获取 A 的工厂，生成 A 的代理对象并放入 `earlySingletonObjects`。
  3. **A 完成初始化**：最终放入 `singletonObjects`，删除其他缓存。

---

**四、源码关键流程分析**

1. **容器启动入口：`AbstractApplicationContext#refresh()`**

```java
public void refresh() {
    // 1. 加载 BeanDefinition
    invokeBeanFactoryPostProcessors();

    // 2. 注册 BeanPostProcessor（如处理 @Autowired 的处理器）
    registerBeanPostProcessors();

    // 3. 初始化单例 Bean（非延迟）
    finishBeanFactoryInitialization(beanFactory);
}
```

2. **Bean 实例化入口：`AbstractAutowireCapableBeanFactory#doCreateBean()`**

```java
protected Object doCreateBean(String beanName, RootBeanDefinition mbd, Object[] args) {
    // 1. 反射创建对象（此时对象尚未注入依赖）
    BeanWrapper instanceWrapper = createBeanInstance(beanName, mbd, args);

    // 2. 提前暴露 Bean（解决循环依赖）
    addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));

    // 3. 依赖注入
    populateBean(beanName, mbd, instanceWrapper);

    // 4. 初始化（执行 @PostConstruct、init-method 等）
    initializeBean(beanName, exposedObject, mbd);
}
```

---

**五、总结**

1. **存储结构**：
   - Bean 定义：`ConcurrentHashMap`（`beanDefinitionMap`）。
   - Bean 实例：通过三级缓存（`singletonObjects`、`earlySingletonObjects`、`singletonFactories`）管理。
2. **存取流程**：
   - 通过 `BeanDefinition` 反射创建对象，依赖注入后缓存到 `singletonObjects`。
3. **设计亮点**：
   - 三级缓存解决循环依赖。
   - `BeanPostProcessor` 扩展点支持 AOP、事务等高级功能。

理解这些底层机制后，可以更好地优化 Spring 应用的性能（如合理使用作用域）或解决复杂依赖问题。