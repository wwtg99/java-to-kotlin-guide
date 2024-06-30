# Java 转 Kotlin 指南 | Java To Kotlin Guide

> Java 转 Kotlin 快速入门指南和速查手册

## 简介

Kotlin 已经是一门成熟的编程语言，它简洁、高效、安全，可与 Java 互操作，出于 Java 而胜于 Java。本文用于向有 Java 基础的人员提供快速的 Kotlin 入门。Kotlin 的优势和详细介绍可参考[官方网站](https://kotlinlang.org/)。

## 快速开始

由于 Kotlin 就是 JetBrains 公司研发的，所以使用他们的 IDE（IntelliJ IDEA） 开发非常简单。

对于新项目直接使用 Kotlin 开发的，在创建项目时选择 Kotlin，选择对应的 JDK 版本即可。如果要在原 Java 项目中添加 Kotlin 进行混合开发，只要在项目中创建一个 `Kotlin 类/文件`，在项目中添加一个 `.kt` 文件，IDE 会自动提示 Kotlin 未配置，点击配置即可。IDE 会在项目配置文件（POM/Gradle）中添加 Kotlin 的依赖和插件，并提供 `kotlin.version` 的变量，用于指定 Kotlin 的版本。

下面是两者语法和特性的快速指南。

## 基础

### 变量赋值

Java

```java
String name = "wwtg99";
final String name = "wwtg99";
```

Kotlin

```kotlin
var name = "wwtg99"  // 可变
val name = "wwtg99"  // 不可变
```

Kotlin 的类型可自动推断，也可显式指定。

```
var name: String = "wwtg99"  // 指定类型为 String
```

### 空值

Kotlin 的类型不能为空，如果可能为空，需要加 `?`。

```kotlin
var otherName: String? = null
```

Kotlin 提供了很多空值判断的简化写法：

Java

```java
if (text != null) {
  int length = text.length();
}
```

Kotlin

```kotlin
// 当 text 不为 null 则执行
text?.let {
    val length = text.length
}
// 当 text 不为 null 则获取，不然为 null
val length = text?.length
```

更多用法请参考[空安全](concepts/null.md)。

### 字符串判断空

字符串判断 null 或者空字符串不再需要其他的工具类了。

Kotlin 为字符串提供了很多判断方法。

- isEmpty(): 字符串长度是否为 0
- isNotEmpty()：字符串长度是否不为 0
- isNullOrEmpty()：字符串为 null 或者长度是否为 0
- isBlank()：字符串为空（仅包含空字符）
- isNotBlank()：字符串不为空
- isNullOrBlank()：字符串为 null 或者为空

同时，可通过扩展方法扩展更多的功能，参考[扩展]()。

### 字符串拼接、格式化

Kotlin 提供了模版字符串，简化了字符串拼接和格式化的操作。

```kotlin
val str1 = "abc"
val str2 = "123"
val str3 = "${str1}/${str2}"  // abc/123
```

具体详见[字符串](concepts/types.md)

### 字符串操作（取字符、子串）

Kotlin 的字符串操作也非常简单。

```kotlin
val str = "Java to Kotlin"
printlin(str[0])  // J
println(str.substring(8..13))  // Kotlin, 前面包含，后面不包含
```

### 类型转换

Java

```java
if (object instanceof Car) {
  Car car = (Car) object;
}
```

Kotlin

```kotlin
if (object is Car) {
  var car = object as Car  // kotlin 支持自动类型转换，不需要显式转换，详见类型转换
}

// 安全的类型转换，不成功则返回 null
var car = object as? Car
```

可参考[类型转换]()与[安全的类型转换](concepts/null.md)。

### 打印

Java

```java
System.out.print("wwtg99");
System.out.println("wwtg99");
```

Kotlin

```kotlin
print("wwtg99")  // 不换行
println("wwtg99")  // 换行
```

## 条件判断

if 在 Kotlin 中可以是表达式：

```kotlin
val max = if (a > b) a else b
```

这就替代了三元操作符，所以 Kotlin 中没有三元操作符 `条件 ? 然后 : 否则`。

if 表达式也可以是代码块，最后的表达式就是返回值。

```kotlin
val max = if (a > b) {
  print("Choose a")
  a
} else {
  print("Choose b")
  b
}
```

同时 Kotlin 还提供了类似 `switch` 的多分支判断表达式 `when`，详见[控制流程](concepts/control_flow.md)。

## 循环

```kotlin
for (i in 1..10) { }

for (i in 1 until 10) { }

for (i in 10 downTo 0) { }

for (i in 1..10 step 2) { }

for (i in 10 downTo 0 step 2) { }

for (item in collection) { }

for ((key, value) in map) { }
```

详见[控制流程](concepts/control_flow.md)。

## 集合

Kotlin 中的集合分为了可变集合和不可变集合。

## 方法

Kotlin 使用 `fun` 定义方法。

```kotlin
fun doSomething() {
   // logic here
}
```

带返回类型的方法

```kotlin
fun doSomething(): Int {
   // logic here
   return 1
}
```

单表达式函数

```kotlin
fun double(x: Int): Int = x * 2
```

## 方法参数默认值

Kotlin 支持方法参数的默认值，不需要额外重载方法。

```kotlin
fun calculateCost(quantity: Int, pricePerItem: Double = 20.5) = quantity * pricePerItem

calculateCost(10, 25.0) // 250
calculateCost(10) // 205
```

## 可变数量的参数

Kotlin 使用 `varargs` 定义可变数量的参数。

```kotlin
fun doSomething(vararg numbers: Int) {
   // logic here
}
```

## 构造方法

## Getters 和 Setters

## POJO（数据类）

## 初始化代码块

## 静态方法/变量
