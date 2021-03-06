# Java编码规范

## 源文件（Source Code Basics）

#### 文件名

文件名以[大驼峰](http://baike.baidu.com/view/1165629.htm)命名，区分大小写，并以`.java`结尾。此外，文件名的命名应该见名知义，使用简单易懂的英文单词（java中多使用**名词**），没有特殊情况切勿使用汉语拼音。

#### 文件编码

代码文件编码统一使用**UTF-8**。

## 源文件结构（Source Code Structure）

一个完整的java源文件包含以下组成部分，依次是：

- 头注释（Beginning Comments）
- 包（Package）和引入（Import）声明语句
- 类（Class）或接口（Interface）声明

#### 头注释（Beginning Comments）

所有的源代码文件都会以一个C语言风格的注释开始，这里面可包含类名／文件名（class name），版本信息（version information），日期（date），版权或许可协议信息（copyright notice or licence）。

```java
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

#### 包和引入声明语句（Package and Import Statement）

##### 包（Package）

紧接头注释的是包声明，然后是引入声明。包名应为反向域名（reverse domain）式，包声明语句不能换行，[行宽的规则](#user-content-行宽line-lengthcolumn-limit)不适用于包声明语句。

```java
package com.demo2do.abc.xyz
```

##### 引入（Imports）

- 引入不含通配符（*）

- 引入语句不换行

- 引入顺序和分隔（spacing）

    1. 所有的[static imports](https://en.wikipedia.org/wiki/Static_import)为一组
    2. java imports
    3. javax imports
    4. 第三方引入为一组，以ASCII为顺序。如：`org.apache`, `org.springframework`
    5. com.namespace imports

    在一个引入组（group）内没有空行，并且引入以ASCII为顺序。组与组之间使用空行隔开。

## 代码格式（Formatting）

### 缩进（Indentation）

除了换行符外，所有的ASCII横向空白符（**ASCII horizontal space character**）是代码中出现的唯一空白符号。这表明：

  - 所有在字符串（string）和字符自面量（character literals）中出现的其他空白字符都应该被转义(escape)
  - Tab（制表符）**不能**用于缩进

缩进的宽度为**4**个空格符，不使用制表符Tab！

#### 行宽（Line Length/Column Limit）

通常，行宽不应该超过80个字符，因为许多终端（terminals）和工具在行宽超过80的时候不能正确的显示。所以推荐使用默认的80宽度，但最宽不宜超过100。

例外的情况：

  1. 不适宜遵循行宽限制的地方。（比如Javadoc中很长的链接）
  2. 包和引用语句
  3. 注释中的有可能被复制到shell中的命令语句，数据库查询操作中有可能被复制的SQL语句等

#### 换行（Wrapping Lines）
  
换行的基本原则包含：

  - 在逗号后换行
  - 在非赋值操作符的前面换行
  - 在赋值操作符的后面换行
  - 方法或者构造方法名与紧随的左括号保持同行
  - 逗号与其前面的代码保持同行
  - 高级别换行（higher-level/higher syntactic level）优于低级别换行（lower-level/lower syntactic level）
  - 新行代码与之前同级别代码的表达式对齐

换行时，每一行至少相对于初始行缩进至少4个空格。如果有多行连续的换行，则缩进可能不仅仅有4个空格。行个连续的换行如果有相同的缩进，则表明他们是在同一语法层级上的（syntatically parallel）。

```java
// Bad
// Line break before comma
someMethod(longExpression1, longExpression2
    ,longExpression3)

// Good
// Line break after comma,
// but lines with the same syntactic level do not align
someMethod(longExpression1, longExpression2,
    longExpression3)

// Better
someMethod(longExpression1, longExpression2,
           longExpression3)

// Bad
// Conventional indentation, causing a deep indents
private static synchronized aFuckingVeryLongLongLongLongLongLongMethodName(int anArg, String anotherArg,
                                                                           Object thirdArg)

// Good
// Avoid deep indents
private static synchronized aFuckingVeryLongLongLongLongLongLongMethodName(int anArg, String anotherArg,
    Object thirdArg)
```

```java
// Bad
// Line break exists in an arithmetic expression
// but inside the parenthesized expression, which has a higher syntactic level
longName1 = longName2 * (longName3 + longName4
          - longName5) + 4 * longName6

// Good
longName1 = longName2 * (longName3 + longName4 - longName5)
          + 4 * longName6
```

```java
// Good
alpha = aLongBooleanExpression ? beta : gamma;

// Preferred
alpha = aLongBooleanExpression ?
            betaLongBooleanExpression :
            gammaLongBooleanExpression;

```

### 注释（Comments）

Java中有两类注释，实现注释（implementation comments）和文档注释（documentation comments）。实现注释是C／C++风格的注释，为`//...`和`/*...*/`。文档注释是Java独有的，为`/**...*/`。可以使用javadoc工具将文档注释提取出来生成相应的javadoc。

#### 实现注释（Implementation Comments）

大致可分为3类实现注释：块注释，单行注释，行尾注释。

##### 块注释

块注释用以描述文件，类，方法或是算法。块注释可以被用在类开头或和方法开头，也可以用在方法内。在方法内的块注释应与它描述的代码有一样的缩进。

块注释之前应该有一个空行来把注释和其他代码分隔开。

```java

/*
 * Here is a block comment
 */
```

##### 单行注释

单行注释可以注释一整行或者一整行的一部分。多行连续的文字注释应该使用块注释而不是单行注释，但是单行注释确可以用来注释多行连续的代码。

单行注释之前应该有一个空行

```java
if(foo > 1) {
  
    // Do something...
} else {

    // Do something else...
}

// if(bar > 1) {
// 
//     // Do something... 
// } else {
//
//     // Do something else...
// }
```

##### 行尾注释

和代码在同一行，在代码的行尾的非常简短的注释。行尾注释应该离代码有一定的距离（至少1个空格）以便区分代码语句。

如果在一段代码中有多个行尾注释，多行的行尾注释并不一定需要纵向对齐。

```java
private int x; // this is fine
private Color color; // this too

private int x;       // permitted, but future edits
private Color color; // may leave it unaligned
```

#### 文档注释（Documentation Comments）

See [文档（ Javadoc）](#user-content-文档javadoc)

#### IDE注释关键字

值得一提的是IDE，如Eclipse，提供了默认注释关键如`TODO`，`FIXME`，也提供了自定义注释关键字的功能。

See [IDE comment keywords](http://stackoverflow.com/questions/9586478/ide-comment-keywords)

### 空格（Whitespace）

#### 空行（Blank Lines）

空行使得逻辑相关的代码被分隔成不同的部分，从而能够提高代码的可读性。在下列情形中应该出现单行空行：

- 两个方法之间
- 在[块注释](#user-content-块注释)和[单行注释](#user-content-单行注释)之前
- 在类成员之间：属性，构造方法，方法，内部类，静态初始块
- 在方法内部的逻辑相关的代码片段之间
- 在类的第一个成员之前活着最后一个成员之后

#### 空格（Blank Spaces）

下列情形中应该使用空格：

- 分隔关键字，如`if`，`for`，`whilte`，`catch`，和之后的左圆括号
  
  ```java
  while (true) {
      // ...
  }
  ```

  需要注意的是方法名和它后面的左圆括号之间不应有空格。

- 分隔关键字，如`else`，`catch`，和之前的右花括号

  ```java
  if (condition) {
      // ...
  } else {
      // ...
  }
  ```

- 在所有左花括号之前

  ```java
  try {
      // ...
  } catch (Exception e) {
    // ...
  }
  ```

- 在方法的参数列表里，逗号`,`之后应该有一个空格
- 在任意二元（除了`.`运算符）或三元运算符的左右两边都应有空格，任意一元运算符和操作数之间不应有空格

  ```java
  // unary operators
  while (d++ = s++) {
      n++;
  }

  // binary operators
  a += b + c;
  a = (a + b) / (c * d);

  // ternary operator
  a ? b = 1 : c = 1;
  ```

- 在`,`，`:`，`;`之后应有一个空格

  ```java
  for(expression1; expression2; expression3) {
      // ...
  }

  new int[] {1, 2, 3};
  ```

- 强制转换的右圆括号之后应该有一个空格

  ```java
  String a = (String) map.get('string');
  ```

- 在单行注释的双斜杠（//）之后应该有一个空格

### 花括号／大括号（Braces）

#### 可省略的花括号

在`if`，`else`，｀for｀，`do`，`while`语句中，当它们的代码块是空或者只有一条代码语句的时候，花括号是可省略的。

#### 非空的语句块：K&R Style

在Kernighan and Ritchie风格(“[Egyptian brackets](http://www.codinghorror.com/blog/2012/07/new-programming-jargon.html)”)的语句块后面要跟上花括号。

+ 左花括号之前不换行
+ 左花括号之后换行
+ 右花括号之前换行
+ 如果右花括号结束一条语句，方法体，构造函数或具名类，则右花括号之后换行。如果右花括号后面接着`else`或者逗号，则右花括号之后不换行。

```java
return new MyClass() {
    @Override public void method() {
        if (condition()) {
            try {
              something();
            } catch (ProblemException e) {
              recover();
            }
        }
    }
};
```

## 声明（Declaration）

## 语句（Statement）

## 命名约定（Naming Convention）

## 编程实践（Programming Practices）

## 文档（Javadoc）

## 参考链接

[Code Conventions for the Java Programming Language](http://www.oracle.com/technetwork/java/javase/documentation/codeconvtoc-136057.html)

[Google Java Style](https://google.github.io/styleguide/javaguide.html)