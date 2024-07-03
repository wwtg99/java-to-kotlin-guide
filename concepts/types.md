# 类型

> Kotlin 中所有的类型都是对象。

## 基本类型

### 数值类型

#### 整型

| 类型  | 大小  | 范围                                                    |
| ----- | ----- | ------------------------------------------------------- |
| Byte  | 8 位  | -128 ~ 127                                              |
| Short | 16 位 | -32768 ~ 32767                                          |
| Int   | 32 位 | -2,147,483,648 ～ 2,147,483,647                         |
| Long  | 64 位 | -9,223,372,036,854,775,808 ～ 9,223,372,036,854,775,807 |

例：

- 十进制（自动推断，在 Int 范围内则为 Int，否则为 Long）：123
- 强制指定长整型用 `L` 标记：123L
- 十六进制：0x0F
- 二进制：0b00001011

#### 无符号整型

| 类型   | 大小  | 范围                           |
| ------ | ----- | ------------------------------ |
| UByte  | 8 位  | 0 ~ 255                        |
| UShort | 16 位 | 0 ~ 65,535                     |
| UInt   | 32 位 | 0 ~ 4,294,967,295              |
| ULong  | 64 位 | 0 ~ 18,446,744,073,709,551,615 |

例：
使用 `u` 或 `U` 标记

```
val a1 = 42u // UInt：未提供预期类型，常量适于 UInt
val a2 = 1UL // ULong，即使未提供预期类型并且常量适于 UInt
```

#### 浮点型

- Float: 32 位
- Double: 64 位

例：
默认推断为 Double，显式指定为 Flout 使用 f 或 F 结尾。

```kotlin
val pi = 3.14 // Double
val d1 = 1.0 // Double
val f1 = 1.1f // Float
```

> 注：具有 Double 参数的函数只能对 Double 值调用，而不能对 Float、 Int 或者其他数字值调用，需要显式类型转换。

#### 字面常量

- 十进制：123
  - 长整型用 `L` 标记：123L
- 十六进制：0x0F
- 二进制：0b00001011
- 浮点数（默认 Double）：123.5
- 单精度（Float）用 `f` 或 `F` 标记：123.5f

Kotlin 不支持八进制。

过长可用下划线分隔，如：

```kotlin
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

#### 显式数值类型转换

所有数字类型都支持转换为其他类型：

- toByte(): Byte
- toShort(): Short
- toInt(): Int
- toLong(): Long
- toFloat(): Float
- toDouble(): Double

#### 数学运算

Kotlin 支持标准数学运算符：`+`, `-`, `*`, `/`, `%`。

可以通过操作符重载自定义覆盖默认行为。

> 注：整数除法总是返回整数，若要返回浮点数，请将其中一个数转换为浮点数。

#### 位运算

Kotlin 对整数提供位运算。

这是完整的位运算列表：

- shl(bits) – 有符号左移
- shr(bits) – 有符号右移
- ushr(bits) – 无符号右移
- and(bits) – 位与
- or(bits) – 位或
- xor(bits) – 位异或
- inv() – 位非

### 布尔型

Boolean: true 或 false

#### 逻辑运算

布尔值的内置运算有：

- ||：逻辑或
- &&：逻辑与
- !：逻辑非

### 字符

Char 单引号包裹，如 `'a'`

支持 `\` 开头的转义字符：

- \t：制表符
- \b：退格符
- \n：换行（LF）
- \r：回车（CR）
- \'：单引号
- \"：双引号
- \\：反斜杠
- \$：美元符

支持 Unicode 转义序列语法，如：'\uFF00'。

### 字符串

String 双引号包裹，如 `"abc"`

> Kotlin 中的字符串也是不可变的。

#### 双引号包裹的字符序列

- 可以使用索引运算访问字符：`s[i]`
- 支持转义字符，如 \n
- 支持字符串模版（使用 `$` 或 `${}`）

```kotlin
val s = "abc"
println("$s.length is ${s.length}")
```

#### 使用三个双引号包裹多行字符串

```kotlin
val text = """
    for (c in "foo")
        print(c)
"""
```

- 不支持转义字符
- 支持模版，模版中输出 `$` 用 `${'$'}_9.99`
- 使用 `trimMargin()` 可删除多余的前导空格（默认 ｜ 作为边界前缀）

```kotlin
val text = """
    |Tell me and I forget.
    |Teach me and I remember.
    |Involve me and I learn.
    |(Benjamin Franklin)
    """.trimMargin()
```

### 数组

保存固定数量相同类型的数据结构。

#### 创建数组

可以使用函数 `arrayOf()`, `arrayOfNulls()` 或者 `emptyArray()` 创建数组，也使用使用 Array 构造函数创建。

例：

```kotlin
val simpleArray = arrayOf(1, 2, 3)
val nullArray: Array<Int?> = arrayOfNulls(3) // 创建长度为 3 都是 null 的数组
var exampleArray = emptyArray<String>() // 创建空数组
val asc = Array(5) { i -> (i * i).toString() } // 创建长度为 5，且元素根据索引为参数的函数创建
```

#### 嵌套数组（多维数组）

嵌套数组的元素同时也是数组。

```kotlin
val twoDArray = Array(2) { Array<Int>(2) { 0 } }
```

#### 访问与修改元素

使用 `[]` 索引操作符访问与修改元素。

```kotlin
val simpleArray = arrayOf(1, 2, 3)
val twoDArray = Array(2) { Array<Int>(2) { 0 } }

// 访问并修改元素
simpleArray[0] = 10
twoDArray[0][0] = 2

// 打印元素
println(simpleArray[0].toString()) // 10
println(twoDArray[0][0].toString()) // 2
```
