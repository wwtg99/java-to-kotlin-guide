# 包和导入

Kotlin 包和导入与 Java 一样，源文件头部声明包。

```kotlin
package org.example

fun printMessage() { /*...*/ }
class Message { /*...*/ }
```

其中，函数和属性可以直接在包中声明，如果没有指定包，则默认从属于默认没有名字的包。

## 默认导入

任何 Kotlin 文件都默认导入了以下一些包：

- [kotlin.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/index.html)
- [kotlin.annotation.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.annotation/index.html)
- [kotlin.collections.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/index.html)
- [kotlin.comparisons.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.comparisons/index.html)
- [kotlin.io.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/index.html)
- [kotlin.ranges.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.ranges/index.html)
- [kotlin.sequences.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.sequences/index.html)
- [kotlin.text.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.text/index.html)

另外一些包根据平台导入：

JVM:

- java.lang.\*
- [kotlin.jvm.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/index.html)

JS:

- [kotlin.js.\*](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.js/index.html)

## 导入

导入和 Java 一样

```kotlin
import org.example.Message
import org.example.*
import org.test.Message as TestMessage
```

import 不仅可以导入类，还可以导入：

- 顶层的函数和属性
- 在对象声明中的函数和属性
- 枚举常量
