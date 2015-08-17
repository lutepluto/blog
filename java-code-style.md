# Java编码规范

## 源文件（Source Code Basics）

#### 文件名

文件名以[大驼峰](http://baike.baidu.com/view/1165629.htm)命名，区分大小写，并以`.java`结尾。此外，文件名的命名应该见名知义，使用简单易懂的英文单词（java中多使用**名词**），没有特殊情况切勿使用汉语拼音。

#### 文件编码

代码文件编码统一使用**UTF-8**。

## 源文件结构（Source Code Structure）

一个完整的java源文件包含以下组成部分，依次是：

- 头注释（Beginning Comments）
- 包（Package）和引用（Import）声明语句
- 类（Class）或接口（Interface）声明

#### 头注释（Beginning Comments）

所有的源代码文件都会以一个C语言风格的注释开始，这里面包含了类名／文件名（class name），版本信息（version information），日期（date），版权或许可协议信息（copyright notice or licence）。

```
/**
 * Classname
 *
 * Version information
 *
 * Date
 *
 * Copyright notice or licence
 */
```

#### 包和引用声明语句（Package and Import Statement）

##### 包

紧接头注释的是包声明，然后是引用声明。包名应为反向域名（reverse domain）式，包声明语句不能换行，行宽的规则不适用于包声明语句。

```java
package com.demo2do.abc.xyz
```

##### 引用

- 引用不含通配符（*）

- 引用语句不换行

- 引用顺序和分隔（spacing）

    1. 所有的[static imports](https://en.wikipedia.org/wiki/Static_import)为一组
    2. java imports
    3. javax imports
    4. 第三方引用为一组，以ASCII为顺序。如：`org.apache`, `org.springframework`
    5. com.namespace imports

    在一个引用组（group）内没有空行，并且引用以ASCII为顺序。组与组之间使用空行隔开。

## 代码格式（Formatting）

### 缩进（Indentation）

### 注释（Comments）

### 空格（Whitespace）

### 括号（Braces）

## 声明（Declaration）

## 语句（Statement）

## 命名约定（Naming Convention）

## 编程实践（Programming Practices）

## 文档（JavaDoc）

## 参考链接

[Code Conventions for the Java Programming Language](http://www.oracle.com/technetwork/java/javase/documentation/codeconvtoc-136057.html)

[Google Java Style](https://google.github.io/styleguide/javaguide.html)