---
title: YAML配置文件
published: 2025-03-18
description: 'YAML常被用于配置文件、数据交换、数据存储等场景'
tags: [配置文件]
category: '其它'
draft: false 
---
**YAML配置文件详解**
YAML是“YAML Ain't Markup Language”（递归缩写为“YAML不是标记语言”）的缩写，原意是强调这种语言以数据做为中心，而不是标记语言。YAML是一种直观的能够被电脑读取的数据序列化格式，也易于人类阅读。它常被用于配置文件、数据交换、数据存储等场景。

## 一、基本语法

1. **大小写敏感**：YAML对大小写是敏感的，`key`和`value`的大小写需要一致。
2. **缩进表示层级**：YAML使用缩进来表示数据层级关系，缩进只能使用空格，不能使用Tab键。通常建议每个层级缩进两个空格。
3. **冒号后有空格**：`key: value`中的`:`后面必须有一个空格。
4. **支持注释**：YAML允许使用`#`进行注释，从`#`开始到行尾的所有内容都将被视为注释。
5. 在yml格式的配置文件中，如果配置项是以0开头的，需要以`''`引起来，因为0开头在yml中表示8进制的数据。

## 二、数据类型

YAML支持多种数据类型，包括：
1. **纯量（Scalar）**：单个的、不可再分的值，如字符串、布尔值、整数、浮点数等。
   - 字符串：可以直接书写，也可以使用引号包裹。
   - 布尔值：用`true`、`false`表示。
   - 数值：直接书写，支持整数和浮点数。
2. **序列（Sequence）**：一组按顺序排列的值，类似于数组或列表。使用`-`表示每个元素。
   ```yaml
   list:
     - item1
     - item2
     - item3
   ```
3. **映射（Mapping）**：键值对集合，类似于字典或哈希表。使用`key: value`表示。
   ```yaml
   dictionary:
     key1: value1
     key2: value2
     key3: value3
   ```
4. **嵌套**：YAML支持数据的嵌套，可以组合使用序列和映射。
   ```yaml
   nested:
     - key1: value1
       key2: value2
     - key3: value3
       key4: value4
   ```
   
## 三、特殊语法

1. **引用**：使用`&`定义锚点，使用`*`引用锚点。
   
   ```yaml
   anchor: &commonSetting
     setting1: value1
     setting2: value2
   ref1:
     <<: *commonSetting
     additionalSetting: value3
   ref2:
     <<: *commonSetting
     anotherSetting: value4
   ```
2. **合并**：使用`<<`表示合并其他映射的内容。
   ```yaml
   base:
     setting1: value1
     setting2: value2
   override:
     <<: *base
     setting2: newValue
   ```
3. **多行字符串**：使用`|`或`>`表示多行字符串。`|`保留换行符，`>`折叠换行符。
   ```yaml
   multiLineString1: |
     This is a multi-line string
     with preserved newlines.
   multiLineString2: >
     This is another multi-line string
     with folded newlines.
   ```
## 四、示例

以下是一个完整的YAML配置文件示例：
```yaml
# 这是注释
name: Example Configuration
description: This is an example YAML configuration file.
settings:
  debug: true
  verbosity: 2
database:
  host: localhost
  port: 3306
  username: user
  password: pass
servers:
  - id: 1
    name: server1
    ip: 192.168.1.1
  - id: 2
    name: server2
    ip: 192.168.1.2
anchors:
  &defaultPorts
    http: 80
    https: 443
ports:
  <<: *defaultPorts
  ssh: 22
```
在这个示例中，我们定义了配置文件的名称、描述、一些设置、数据库连接信息、服务器列表以及端口号。我们还展示了如何使用锚点和引用来复用数据。
## 五、使用场景
YAML常用于以下场景：

1. **配置文件**：如Docker-compose、Kubernetes、Ansible等工具的配置文件。
2. **数据交换**：在不同系统间交换数据。
3. **数据存储**：存储结构化数据，如配置信息、用户数据等。
**六、注意事项**
1. **缩进**：务必使用空格进行缩进，不要使用Tab键。
2. **引号**：虽然YAML允许不使用引号包裹字符串，但在某些特殊情况下（如字符串包含冒号、换行符等）需要使用引号。
3. **兼容性**：不同的YAML解析器可能存在细微的兼容性问题，建议查阅相关文档以确保兼容性。
通过以上介绍，相信您对YAML配置文件有了更深入的了解。在实际使用中，请根据具体需求和场景选择合适的语法和结构。