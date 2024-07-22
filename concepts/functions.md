# 函数

## 函数声明

Kotlin 中使用 `fun` 关键词声明函数。

```kotlin
fun double(x: Int): Int {
    return 2 * x
}
```

如下方式调用，有多个参数时也可以指定参数名。

```kotlin
val result = double(2)
```

### 参数

函数参数使用 Pascal 表示法定义：`name: type`，参数用逗号隔开，每个参数必须有显式类型。

```kotlin
fun powerOf(number: Int, exponent: Int): Int { /*...*/ }
```

可以保留最后一个参数的尾部逗号。

### 默认参数

函数参数可以有默认值，这样可以减少重载方法。当覆盖一个有默认参数值的方法时，必须从签名中省略默认值。

```kotlin
fun read(
    b: ByteArray,
    off: Int = 0,
    len: Int = b.size,
) { /*...*/ }
```

如果一个有默认值参数在一个无默认值的参数之前，那么该默认值只能通过使用具名实参调用该函数来使用。

```kotlin
fun foo(
    bar: Int = 0,
    baz: Int,
) { /*...*/ }

foo(baz = 1) // 默认值 bar = 0 被使用
```

如果在默认参数之后的最后一个参数是 lambda 表达式，那么它既可以作为具名实参在括号内传入，也可以在括号外传入。

```kotlin
fun foo(
    bar: Int = 0,
    baz: Int = 1,
    qux: () -> Unit,
) { /*...*/ }

foo(1) { println("hello") }     // 使用默认值 baz = 1
foo(qux = { println("hello") }) // 使用默认值 bar = 0 和 baz = 1
foo { println("hello") }        // 使用默认值 bar = 0 和 baz = 1
```

### 可变参数

使用 `vararg` 关键词指定可变参数。可以使用具名参数和 `*` 来展开参数。

```kotlin
fun foo(vararg strings: String) { /*...*/ }

foo(strings = *arrayOf("a", "b", "c"))
```

> 注：在 JVM 平台中调用 Java 函数时不能使用具名实参语法。

### 返回 Unit

如果一个函数并不返回有用的值，其返回类型是 Unit，类似于 Java 的 void。Unit 是一种只有一个值的类型。

```kotlin
fun printHello(name: String?): Unit {
    if (name != null)
        println("Hello $name")
    else
        println("Hi there!")
    // `return Unit` 或 `return` 都是可选的
}
```

Unit 的返回类型声明也是可选的，可以省略。

### 单表达式函数

当函数体由单个表达式构成时，可以省略花括号。

```kotlin
fun double(x: Int): Int = x * 2
```

返回值也可以通过类型推断而省略。

> 注：对于有代码块的函数，返回类型显示声明是必须的，除非返回类型是 Unit。

### Infix 表示法

带有 `infix` 关键词的函数也可以使用 Infix 表示法（忽略该调用的点与圆括号）调用。但有如下要求：

- 它们必须是成员函数或扩展函数。
- 它们必须只有一个参数。
- 其参数不得接受可变数量的参数且不能有默认值。

```kotlin
infix fun Int.shl(x: Int): Int { ... }

// infix 表示法
1 shl 2

// 和下面的写法一样
1.shl(2)
```

> Infix 函数调用的优先级低于算术操作符、类型转换以及 rangeTo 操作符，但高于布尔运算符 && 和 ||、is- 与 in- 检测。

### 函数作用域

Kotlin 的函数可以在文件顶层声明，也可以声明在局部作用域，作为成员函数以及扩展函数。

#### 局部函数

Kotlin 支持局部函数，即一个函数在另一个函数内部。

```kotlin
fun dfs(graph: Graph) {
    fun dfs(current: Vertex, visited: MutableSet<Vertex>) {
        if (!visited.add(current)) return
        for (v in current.neighbors)
            dfs(v, visited)
    }

    dfs(graph.vertices[0], HashSet())
}
```

局部函数可以访问外部函数（闭包）的局部变量。

#### 成员函数

成员函数是在类或对象内部定义的函数。

```kotlin
class Sample {
    fun foo() { print("Foo") }
}
```

### 范型函数

函数可以有范型参数，与 Java 类似。

```kotlin
fun <T> singletonList(item: T): List<T> { /*……*/ }
```

### 尾递归函数

Kotlin 支持一种称为尾递归的函数式编程风格。对于某些使用循环的逻辑，可以使用尾递归替代而不会有堆栈溢出的风险。当一个函数用 `tailrec` 关键词标记并满足所需的条件时，编译器会优化该递归，生成一个快速而高效的基于循环的版本。

```kotlin
val eps = 1E-10

tailrec fun findFixPoint(x: Double = 1.0): Double =
    if (Math.abs(x - Math.cos(x)) < eps) x else findFixPoint(Math.cos(x))
```

这段代码计算余弦的不动点（fixpoint of cosine），这是一个数学常数。最终的代码会生成以下这种传统的循环。

```kotlin
val eps = 1E-10

private fun findFixPoint(): Double {
    var x = 1.0
    while (true) {
        val y = Math.cos(x)
        if (Math.abs(x - y) < eps) return x
        x = Math.cos(x)
    }
}
```

函数需要满足如下条件：

- 递归调用（调用函数自身）必须是它执行的最后一个操作。
- 不能用在 try/catch/finally 块中，也不能用于 open 的函数。

## 高阶函数和 Lambda 表达式

Kotlin 中的函数是“一等公民”，也就是它们可以存储在变量或数据结构中，且可以用于函数参数以及返回。

### 高阶函数

高阶函数是用函数作为参数或返回值的函数。

```kotlin
fun <T, R> Collection<T>.fold(
    initial: R,
    combine: (acc: R, nextElement: T) -> R
): R {
    var accumulator: R = initial
    for (element: T in this) {
        accumulator = combine(accumulator, element)
    }
    return accumulator
}
```

上述代码中，函数的参数 combine 具有函数类型 `(R, T) -> R`，而这个参数可以使用 lambda 表达式传参。

```kotlin
fun main() {
    //sampleStart
    val items = listOf(1, 2, 3, 4, 5)

    // lambda 表达式是花括号括起来的代码块
    items.fold(0, {
        // 如果一个 lambda 表达式有参数，-> 前面是参数
        acc: Int, i: Int ->
        print("acc = $acc, i = $i, ")
        val result = acc + i
        println("result = $result")
        // lambda 表达式中的最后一个表达式的值是返回值
        result
    })

    // lambda 表达式的参数类型如果可以推断出来的话是可选的
    val joinedToString = items.fold("Elements:", { acc, i -> acc + " " + i })

    // 函数引用也可以用于高阶函数调用
    val product = items.fold(1, Int::times)
    //sampleEnd
    println("joinedToString = $joinedToString")
    println("product = $product")
}
```

### 函数类型

Kotlin 使用类似 `(Int) -> String` 的表示法来声明函数类型。参数类型列表可以为空，如 `() -> A`，Unit 返回类型不可省略。

函数类型可以有一个额外的接收者类型（也就是[扩展函数](./classes_objects.md#扩展函数)），在表示法的点之前指定，如类型 `A.(B) -> C` 表示可以在 A 的接收者对象上以一个 B 类型参数来调用并返回一个 C 类型值的函数。

如果要指定一个可为 null 的函数类型，可以这样表示 `((Int, Int) -> Int)?`。

### Lambda 表达式

Lambda 表达式的完整语法如下所示：

```kotlin
val sum: (Int, Int) -> Int = { x: Int, y: Int -> x + y }
```

- lambda 表达式总是括在花括号中。
- 完整语法形式的参数声明放在花括号内，并有可选的类型标注。
- 函数体跟在 `->` 后。
- 如果该 lambda 的返回类型不是 Unit，那么该 lambda 主体中的最后一个表达式的值会视为返回值。

如果函数的最后一个参数是函数，那么作为相应参数传入的 lambda 表达式可以放在圆括号之外，这种语法也称为拖尾 lambda 表达式。。

```kotlin
val product = items.fold(1) { acc, e -> acc * e }
```

#### it：单参数的隐式名称

如果该 lambda 表达式是唯一的参数，那么圆括号也可以省略。

```kotlin
run { println("...") }
```

如果 lambda 表达式只有一个参数，该参数和 `->` 也可以省略，会隐式使用 `it` 作为参数。

```kotlin
ints.filter { it > 0 } // 这个函数是 (it: Int) -> Boolean 类型
```

如果 lambda 表达式的参数未使用，那么可以用下划线取代其名称。

```kotlin
map.forEach { (_, value) -> println("$value!") }
```

### 匿名函数

lambda 表达式中没有指定返回值的类型，这可以推断出来。但如果需要显示指定，可以使用匿名函数。

```kotlin
fun(x: Int, y: Int): Int = x + y
```

匿名函数类似于普通函数声明，只不过没有函数名。函数体可以是表达式也可以是代码块（花括号包裹）。

### 闭包

Lambda 表达式或者匿名函数（包括局部函数和对象表达式）可以访问其闭包，其中包含在外部作用域中声明的变量。

```kotlin
var sum = 0
ints.filter { it > 0 }.forEach {
    sum += it
}
print(sum)
```

## 内联函数

使用高阶函数会带来一些运行时的效率损失，每一个函数都是一个对象，并且会捕获一个闭包。在许多情况下通过内联化 lambda 表达式可以消除这类的开销。

例如 `lock()` 函数：

```kotlin
lock(l) { foo() }
```

编译器会生成如下代码：

```kotlin
l.lock()
try {
    foo()
} finally {
    l.unlock()
}
```

如果要这么做，使用 `inline` 关键词修饰函数。

```kotlin
inline fun <T> lock(lock: Lock, body: () -> T): T { …… }
```

`inline` 关键词影响函数本身和传递给它的 lambda 表达式，它们将被内联到调用处。内联可能导致生成的代码量增加。不过如果使用得当（避免内联过大函数），性能上会有所提升。

### 非内联

如果不希望内联所有传递给内联函数的 lambda 表达式，那么可以用 `noinline` 关键词修饰不希望内联的函数参数。

```kotlin
inline fun foo(inlined: () -> Unit, noinline notInlined: () -> Unit) { ... }
```

## 操作符重载

Kotlin 中可以为类型提供预定义的一组操作符的自定义实现，如 +，\*，使用 `operator` 关键词修饰对应的方法，提供对应的参数声明，当覆盖方法时可省略 `operator`。

```kotlin
interface IndexedContainer {
    operator fun get(index: Int)
}
```

### 一元操作

| 表达式 | 对应函数       |
| ------ | -------------- |
| +a     | a.unaryPlus()  |
| -a     | a.unaryMinus() |
| !a     | a.not()        |
| a++    | a.inc()        |
| a--    | a.dec()        |

`inc()` 和 `dec()` 函数必须返回一个值，它用于赋值给使用 `++` 或 `--` 操作的变量。它们不应该改变在其上调用 `inc()` 或 `dec()` 的对象。

例：

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.unaryMinus() = Point(-x, -y)

val point = Point(10, 20)

fun main() {
   println(-point)  // 输出 Point(x=-10, y=-20)
}
```

### 二元操作

#### 算术运算符

| 表达式 | 对应函数        |
| ------ | --------------- |
| a + b  | a.plus(b)       |
| a - b  | a.minus(b)      |
| a \* b | a.times(b)      |
| a / b  | a.div(b)        |
| a % b  | a.rem(b)        |
| a..b   | a.rangeTo(b)    |
| a..<b  | a.rangeUntil(b) |

#### in 操作符

| 表达式  | 对应函数       |
| ------- | -------------- |
| a in b  | b.contains(a)  |
| a !in b | !b.contains(a) |

#### 索引访问操作符

| 表达式               | 对应函数                |
| -------------------- | ----------------------- |
| a[i]                 | a.get(i)                |
| a[i, j]              | a.get(i, j)             |
| a[i_1, ..., i_n]     | a.get(i_1, ..., i_n)    |
| a[i] = b             | a.set(i, b)             |
| a[i, j] = b          | a.set(i, j, b)          |
| a[i_1, ..., i_n] = b | a.set(i_1, ..., i_n, b) |

#### invoke 操作符

| 表达式           | 对应函数                |
| ---------------- | ----------------------- |
| a()              | a.invoke()              |
| a(i)             | a.invoke(i)             |
| a(i, j)          | a.invoke(i, j)          |
| a(i_1, ..., i_n) | a.invoke(i_1, ..., i_n) |

#### 扩增赋值

| 表达式  | 对应函数         |
| ------- | ---------------- |
| a += b  | a.plusAssign(b)  |
| a -= b  | a.minusAssign(b) |
| a \*= b | a.timesAssign(b) |
| a /= b  | a.divAssign(b)   |
| a %= b  | a.remAssign(b)   |

#### 相等与不等操作符

| 表达式 | 对应函数                        |
| ------ | ------------------------------- |
| a == b | a?.equals(b) ?: (b === null)    |
| a != b | !(a?.equals(b) ?: (b === null)) |

> 注：=== 和 !== 不可重载，判断两个引用是否指向同一对象

#### 比较操作符

| 表达式 | 对应函数            |
| ------ | ------------------- |
| a > b  | a.compareTo(b) > 0  |
| a < b  | a.compareTo(b) < 0  |
| a >= b | a.compareTo(b) >= 0 |
| a <= b | a.compareTo(b) <= 0 |

所有的比较都转换为对 `compareTo` 函数的调用，这个函数需要返回 Int 类型。
