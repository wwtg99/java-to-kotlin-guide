# 控制流程

## if 条件判断

在 Kotlin 中，if 可以是表达式，因此替代了三元操作符 `条件 ? 然后 : 否则`。

```kotlin
var max = a
if (a < b) max = b

// 使用 else
if (a > b) {
    max = a
} else {
    max = b
}

// 作为表达式
max = if (a > b) a else b

// 在表达式中使用 else if
val maxLimit = 1
val maxOrLimit = if (maxLimit > a) maxLimit else if (a > b) a else b
```

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

> 注：在赋值表达式中 else 是必须的。

## 多分支判断

类似于 Java 的 `switch`, Kotlin 提供了更灵活的 `when`。

```kotlin
when (x) {
  1 -> print("x == 1")
  2 -> print("x == 2")
  else -> {
    print("x is neither 1 nor 2")
  }
}
```

`when` 将它的参数与所有的分支条件顺序比较，直到某个分支满足条件。

`when` 也可以作为表达式使用，配合枚举类也非常简便

```kotlin
enum class Bit {
    ZERO, ONE
}

val numericValue = when (getRandomBit()) {
    Bit.ZERO -> 0
    Bit.ONE -> 1
    // 'else' 这里 else 不是必须的，如果前面没有全部覆盖就必须提供
}
```

合并部分条件

```kotlin
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```

可以使用任意表达式作为条件判断

```kotlin
when (x) {
    s.toInt() -> print("s encodes x")
    else -> print("s does not encode x")
}
```

使用 `in` 或 `!in` 检测区间或者集合

```kotlin
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}
```

`when` 也可以用来取代 if-else 链。 如果不提供参数，所有的分支条件都是简单的布尔表达式，而当一个分支的条件为真时则执行该分支

```kotlin
when {
    x.isOdd() -> print("x is odd")
    y.isEven() -> print("y is even")
    else -> print("x+y is odd")
}
```

在条件块中绑定条件变量

```kotlin
fun Request.getBody() =
    when (val response = executeRequest()) {
        is Success -> response.body
        is HttpError -> throw HttpException(response.status)
    }
```