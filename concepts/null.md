# 空安全

在 Kotlin 中，类型系统区分一个引用可以容纳 null （可空引用）还是不能容纳（非空引用）。 例如，String 类型的常规变量不能容纳 null。如果要允许为空，可以声明一个变量为可空字符串（写作 String?）。

例：

```kotlin
var a: String = "abc"  // 不可以为 null
var b: String? = "abc"  // 可以为 null
```

## 判断 null

使用 `if` 判断检测

```kotlin
// 获取 b 的长度，b 为 null 则取 -1
val l = if (b != null) b.length else -1
```

简化的安全调用

```kotlin
val b: String? = null
println(b?.length)
```

如果 b 非空，就返回 b.length，否则返回 null，这个表达式的结果类型是 Int?。

更长的链式调用

```kotlin
staff?.department?.head?.name
```

如果任意一个属性（环节）为 null，这个链式调用就会返回 null。

## 对非空操作

如果要只对非空值执行某个操作，安全调用操作符可以与 let 一起使用。

```kotlin
val listWithNulls: List<String?> = listOf("Kotlin", null)
for (item in listWithNulls) {
    item?.let { println(it) } // 输出 Kotlin 并忽略 null
}
```

安全调用也可以出现在赋值的左侧。这样，如果调用链中的任何一个接收者为 null 都会跳过赋值，而右侧的表达式根本不会求值。

```kotlin
person?.department?.head = managersPool.getManager()
```

如果 `person` 或者 `person.department` 其中之一为空，都不会调用该函数。

## Elvis 操作符

类似于 Java 的三元操作符。

Java 中：

```java
String a = null;
String str = a != null ? a : "";
```

Kotlin 中这样写：

```kotlin
val a: String? = null
val str = if (a != null) a else ""
```

也可以使用 `?:` 操作符：

```kotlin
val str = a ?: ""
```

如果 `?:` 左侧表达式不是 null，Elvis 操作符就返回其左侧表达式，否则返回右侧表达式。请注意，当且仅当左侧为 null 时，才会对右侧表达式求值。

和 return 或 throw 组合很方便：

```kotlin
fun foo(node: Node): String? {
    val parent = node.getParent() ?: return null
    val name = node.getName() ?: throw IllegalArgumentException("name expected")
}
```

## !! 操作符

非空断言运算符`（!!）`将任何值转换为非空类型，若该值为 null 则抛出异常。

```kotlin
val l = b!!.length
```

## 安全的类型转换

如果类型尝试转换不成功则返回 null，而不是抛出异常。

```kotlin
val aInt: Int? = a as? Int
```

## 可空类型的集合

对于可空类型的集合，如果要获取所有非空的元素，可以使用 `filterNotNull` 方法。

```kotlin
val nullableList: List<Int?> = listOf(1, 2, null, 4)
val intList: List<Int> = nullableList.filterNotNull()
```
