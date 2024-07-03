# 类和对象

## 类

Kotlin 中使用 `class` 关键词声明类。

```kotlin
class Person { /*...*/ }
```

如果没有类体，可以省略花括号。

## 构造函数

在 Kotlin 中类有一个主构造函数和若干个次构造函数，主构造函数在类头中声明。

```kotlin
class Person constructor(firstName: String) { /*...*/ }
```

如果主构造函数没有任何注解或可见性修饰符，可以省略 `constructor` 关键词。

主构造函数初始化类实例和属性，但不能在类头中包含任何可运行的代码。如果需要在初始化时执行代码，可在类体中声明初始化块。初始化块使用 `init` 关键词声明。

```kotlin
class InitOrderDemo(name: String) {
    val firstProperty = "第一个属性: $name".also(::println)

    init {
        println("第一个初始化块，打印 $name")
    }

    val secondProperty = "第二个属性: ${name.length}".also(::println)

    init {
        println("第二个初始化块，打印 ${name.length}")
    }
}
```

主构造函数的参数能在初始化块和属性初始化时使用。

```kotlin
class Customer(name: String) {
    val customerKey = name.uppercase()
}
```

Kotlin 还有一种简便的方式直接在主构造函数中声明属性，并初始化。

```kotlin
class Person(val firstName: String, val lastName: String, var age: Int)
```

并可以给属性添加默认值。

```kotlin
class Person(val firstName: String, val lastName: String, var isEmployed: Boolean = true)
```

其中，可以添加最后尾部的逗号，便于统一格式化。

```kotlin
class Person(
    val firstName: String,
    val lastName: String,
    var age: Int, // 尾部逗号
) { /*...*/ }
```

与普通属性一样，主构造函数中声明的属性可以是可变的（var）或只读的（val）。

## 次构造函数

可以在类体中使用 `constructor` 关键词声明次构造函数。

```kotlin
class Person(val pets: MutableList<Pet> = mutableListOf())

class Pet {
    constructor(owner: Person) {
        owner.pets.add(this)
    }
}
```

如果类有一个主构造函数，每个次构造函数需要直接或通过其他次构造函数间接委托给主构造函数，使用 `this` 关键词。

```kotlin
class Person(val name: String) {
    val children: MutableList<Person> = mutableListOf()
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this)
    }
}
```

> 注：初始化块中的代码实际上会成为主构造函数的一部分。对主构造函数的委托发生在访问次构造函数的第一条语句前，因此所有初始化块与属性初始化器中的代码都会在次构造函数体之前执行。即使类没有主构造函数，仍然会隐式执行初始化块。

如果一个非抽象类没有声明任何主或次构造函数，它也会有一个生成的不带参数的 public 的主构造函数。如果你不希望类有一个公有构造函数，那么声明一个带有非默认可见性的空的主构造函数即可。

```kotlin
class DontCreateMe private constructor() { /*……*/ }
```

> 注：在 JVM 中，如果所有主构造函数的参数都有默认值，则会生成一个用默认值初始化的无参构造函数。这样便于 Jackson 或 JPA 使用。

## 创建类的实例

创建类的实例只需要调用它的构造函数即可，也就是不需要 `new` 关键词了。

```kotlin
val invoice = Invoice()

val customer = Customer("wwtg99")
```

## 继承
