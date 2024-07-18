# 类和对象

## 类

Kotlin 中使用 `class` 关键词声明类。

```kotlin
class Person { /*...*/ }
```

如果没有类体，可以省略花括号。

### 构造函数

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

### 次构造函数

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

### 创建类的实例

创建类的实例只需要调用它的构造函数即可，也就是不需要 `new` 关键词了。

```kotlin
val invoice = Invoice()

val customer = Customer("wwtg99")
```

## 继承

在 Kotlin 中所有类都有一个共同的超类：Any，类似 Java 的 Object。Any 有三个方法，`equals()`, `hashCode()` 和 `toString()`，所以所有类都有这三个方法。

默认 Kotlin 的类都是 final （不可继承）的，若要类可继承，要使用 `open` 关键词声明。

```kotlin
open class Base
```

然后使用分号(:)声明继承的父类。

```kotlin
open class Base(p: Int)

class Derived(p: Int) : Base(p)
```

如果子类有主构造函数，父类必须在主构造函数中初始化参数。如果子类没有主构造函数，那么次构造函数必须通过 `super` 关键词委托给父类或其他构造函数，这类似于 Java 的子类构造函数中必须使用 super 调用（或隐式调用）父类构造函数。

```kotlin
class MyView : View {
    constructor(ctx: Context) : super(ctx)

    constructor(ctx: Context, attrs: AttributeSet) : super(ctx, attrs)
}
```

### 覆盖方法

Kotlin 要求使用 `override` 关键词声明覆盖的方法，类似 Java 的 `@override` 注解，但不能省略。

```kotlin
open class Shape {
    open fun draw() { /*...*/ }
    fun fill() { /*...*/ }
}

class Circle() : Shape() {
    override fun draw() { /*...*/ }
}
```

标记为 `override` 的成员本身就是开放的，如果不想被再次覆盖，请使用 `final` 关键词。

```kotlin
open class Rectangle() : Shape() {
    final override fun draw() { /*……*/ }
}
```

### 覆盖属性

覆盖属性和覆盖方法类似，必须使用 `override` 关键词且必须使用兼容的类型。

```kotlin
open class Shape {
    open val vertexCount: Int = 0
}

class Rectangle : Shape() {
    override val vertexCount = 4
}
```

可以使用 `var` 属性覆盖一个 `val` 声明的属性，但反过来就不行。因为 `var` 声明的属性有 get 和 set 方法，而 `val` 声明的属性只有 get 方法。

### 继承的注意事项

在派生类实例化的过程中，会首先完成父类的初始化，因此在父类初始化块的执行中请不要使用 `open` 的方法或属性，因为它们在子类中还没有初始化。

子类中可以使用 `super` 关键词调用父类中的方法或属性。如果父类中有同名的成员，可以使用尖括号区分。

```kotlin
open class Rectangle {
    open fun draw() { /* ... */ }
}

interface Polygon {
    fun draw() { /* ... */ } // 接口的成员默认都是 open 的
}

class Square() : Rectangle(), Polygon {
    // 必须实现方法
    override fun draw() {
        super<Rectangle>.draw() // 调用 Rectangle.draw()
        super<Polygon>.draw() // 调用 Polygon.draw()
    }
}
```

## 属性

### 声明属性

Kotlin 类中使用 `var` 声明可变属性，`val` 声明不可变属性。使用属性，直接用名称即可。

```kotlin
class Address {
    var name: String = "wwtg99"
    var street: String = "street"
}

fun copyAddress(address: Address): Address {
    val result = Address()
    result.name = address.name // 将调用访问器
    result.street = address.street
    // ……
    return result
}
```

### Getter 与 Setter

声明属性的完整语法如下所示：

```kotlin
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]
```

其中，初始化器、getter、setter 都是可选的，如果类型可以推断出来的话也是可选的。

不可变属性没有 setter。

```kotlin
val simple: Int? // 类型 Int, 默认的 getter, 必须在构造函数中初始化
val inferredType = 1 // 推断出类型 Int，默认的 getter
```

可以自定义属性获取方法，这样每次获取属性时都会调用。

```kotlin
class Rectangle(val width: Int, val height: Int) {
    val area: Int // 类型可省略，可推断出来
        get() = this.width * this.height
}
```

自定义的属性设置方法，这样每次设置属性都会调用。

```kotlin
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // 解析字符串，或设置其他属性
    }
```

### 延迟初始化

可以使用 `lateinit` 关键词来声明一个延迟初始化的属性或变量。

```kotlin
public class MyTest {
    lateinit var subject: TestSubject

    @SetUp fun setup() {
        subject = TestSubject()
    }

    @Test fun test() {
        subject.method()
    }
}
```

这个关键词只能用于类体中 `var` 定义的属性（不在主构造函数中声明，且没有自定义的 getter 或 setter）、顶层属性或局部变量，该属性或变量必须是非空类型，且不能是原生类型。

可以使用 `.isInitialized` 来检查一个延迟属性是否被初始化了。

```kotlin
if (foo::bar.isInitialized) {
    println(foo.bar)
}
```

## 接口

Kotlin 中的接口和 Java 类似，可以有抽象或者实现方法，但不能保存状态。它可以有属性但必须声明为抽象或提供访问器实现。

```kotlin
interface MyInterface {
    fun bar()
    fun foo() {
      // 可选的方法体
    }
}
```

实现接口

```kotlin
class Child : MyInterface {
    override fun bar() {
        // body
    }
}
```

### 接口中的属性

在接口中声明的属性要么是抽象的，要么提供访问器的实现。

```kotlin
interface MyInterface {
    val prop: Int // 抽象的

    val propertyWithImplementation: String
        get() = "foo"

    fun foo() {
        print(prop)
    }
}

class Child : MyInterface {
    override val prop: Int = 29
}
```

### 接口继承

接口也可以继承其他接口，这和 Java 类似。当实现多个接口时，可能遇到同名冲突，使用 `super<>` 来指定具体哪个方法。

```kotlin
interface A {
    fun foo() { print("A") }
    fun bar()
}

interface B {
    fun foo() { print("B") }
    fun bar() { print("bar") }
}

class C : A {
    override fun bar() { print("bar") }
}

class D : A, B {
    override fun foo() {
        super<A>.foo()
        super<B>.foo()
    }

    override fun bar() {
        super<B>.bar()
    }
}
```

## 函数式（SAM）接口

只有一个抽象方法的接口叫函数式接口或单一抽象方法（single abstract method, SAM）接口，类似于 Java 的 `@FunctionalInterface` 注解标记的接口。在 Kotlin 中使用 `fun` 关键词来定义。

```kotlin
fun interface KRunnable {
   fun invoke()
}
```

函数式接口可以方便地用于 Lambda 表达式中。

```kotlin
// 现定义如下函数式接口
fun interface IntPredicate {
   fun accept(i: Int): Boolean
}

// 使用 Lambda 创建一个实例
val isEven = IntPredicate { it % 2 == 0 }
```

## 可见性修饰符

在 Kotlin 中有这四个可见性修饰符：private、protected、internal 和 public。默认可见性是 public。

### 包

函数、属性、类、对象和接口可以直接在包内的顶层声明。

```kotlin
// 文件名: example.kt
package foo

fun baz() { ... }
class Bar { ... }
```

- 如果不使用任何可见性修饰符，默认为 public，这意味着你的声明将随处可见。
- 如果声明为 private，它只会在声明它的文件内可见。
- 如果声明为 internal，它会在相同模块内随处可见。
- protected 修饰符不适用于顶层声明。

### 类成员

对于类内部的成员：

- private 意味着该成员只在这个类内部（包含其所有成员）可见。
- protected 意味着该成员具有与 private 一样的可见性，但也在子类中可见。
- internal 意味着能见到类声明的本模块内都可见。
- public 意味着能见到类声明的都可见。

如果覆盖一个 protected 或 internal 成员并且没有显式指定其可见性，该成员还会具有与原始成员相同的可见性。

> 注：局部变量、函数和类不能有可见性修饰符。

### 模块

在 Kotlin 中模块指的是编译在一起的一套 Kotlin 文件，如：

- 一个 IntelliJ IDEA 模块
- 一个 Maven 项目
- 一个 Gradle 源代码集（例外是 test 源代码集可以访问 main 的 internal 声明）
- 一次 <kotlinc> Ant 任务执行所编译的一套文件

## 扩展

Kotlin 能够对一个类或接口扩展新功能而无需继承该类或者使用像装饰者这样的设计模式。

### 扩展函数

声明一个扩展函数需用一个被扩展的类型来作为它的前缀。例如下面代码为 MutableList<Int> 添加一个 swap 函数：

```kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1] // 'this' 对应 list 实例
    this[index1] = this[index2]
    this[index2] = tmp
}
```

然后就可以像下面这样使用

```kotlin
val list = mutableListOf(1, 2, 3)
list.swap(0, 2) // 'swap()' 方法中的 this 就是这个 list
```

> 注：扩展是静态解析的，并不是真正在类中添加成员，不过是用一个函数来调用。

### 可空接收者

如果需要为可以为 null 的对象调用扩展，获取的 this 就为 null，注意做好 null 的判断。

```kotlin
fun Any?.toString(): String {
    if (this == null) return "null"
    // 在 null 检查之后，this 就是非空对象了
    // 可解析为 Any 的任何成员函数
    return toString()
}
```

### 扩展属性

与扩展函数类似，Kotlin 支持扩展属性。

```kotlin
val <T> List<T>.lastIndex: Int
    get() = size - 1
```

> 注：扩展属性并不是添加一个属性到对象中，因此不能有初始化器，只能通过 getter/setter 来处理。

### 伴生对象扩展

同样可以扩展伴生对象的方法或属性。

```kotlin
class MyClass {
    companion object { }  // 通过 "Companion" 引用
}

fun MyClass.Companion.printCompanion() { println("companion") }

fun main() {
    MyClass.printCompanion()
}
```

## 数据类

Kotlin 中有一个专门用于保存数据的类，类似于 Java 的 POJO （Plain Ordinary Java Object，简单 Java 对象），使用数据类可以替代 Lombok 的 `@Data`、`@ToString`、`@EqualsAndHashCode` 等注解。

```kotlin
data class User(val name: String, val age: Int)
```

数据类会自动生成几个方法：

- .equals()/.hashCode()
- .toString() 形式为 "User(name=John, age=42)"
- .componentN() 函数按声明的顺序对应所有的属性
- .copy() 函数

同时对数据类有一些要求：

- 主构造函数必须至少有一个参数（可以有默认值）。
- 主构造函数的所有参数必须标记为 val 或 var。
- 数据类不能是抽象、开放、密封或者内部的。

其中，.equals()、.hashCode() 或者 .toString() 可以显式覆盖，而 .componentN() 和 .copy() 不允许。

### 类体中定义的属性

自动生成仅针对主构造函数的属性，如果要排除某个属性，就在类体中定义。

```kotlin
data class Person(val name: String) {
    var age: Int = 0
}
```

上例中，只有 name 属性会生成到 .toString()、.equals()、.hashCode()、 .copy() 的实现中，也只会生成一个 .component1()。

### 解构声明

数据类生成的 .componentN() 函数可用于对象解构。

```kotlin
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age")
// Jane, 35 years of age
```

## 密封类和密封接口

密封类和密封接口为类层次结构提供受控继承。密封类的所有直接子类在编译时都是已知的。在定义密封类的模块和包之外，不得出现其他子类。一旦编译了带有密封接口的模块，就不能再创建新的子类。

当密封类和 `when` 语句结合使用时，就可以判断所有已知的子类。

密封类主要用于以下场景：

- 需要受控继承，在编译期间有哪些子类是确定的。
- 类型安全的设计
- 对关闭的 API，不希望第三方客户端来扩展

### 声明密封类和密封接口

使用 `sealed` 关键词来声明。

```kotlin
// 创建一个密封接口
sealed interface Error

// 创建一个密封类实现密封接口
sealed class IOError(): Error

// 定义密封类的子类
class FileReadError(val file: File): IOError()
class DatabaseError(val source: DataSource): IOError()

// 创建一个单例实例实现密封接口
object RuntimeError : Error
```

> 注：密封类总是抽象类

### 密封类结合 when 表达式

```kotlin
// 记录错误的函数
fun log(e: Error) = when(e) {
    is Error.FileReadError -> println("Error while reading file ${e.file}")
    is Error.DatabaseError -> println("Error while reading from database ${e.source}")
    Error.RuntimeError -> println("Runtime error")
    // 不需要 else，因为所有类型已经遍历了
}
```

## 范型

Kotlin 与 Java 一样可以有范型参数。

```kotlin
class Box<T>(t: T) {
    var value = t
}
```

创建类实例只需提供类型参数，如果可以推断则可省略。

```kotlin
val box: Box<Int> = Box<Int>(1)

// 编译器可根据 1 的类型推断出 Box<Int>
val box = Box(1)
```

### 型变

Java 中的通配符范型参数是一个很棘手的问题，但 Kotlin 中没有，取而代之的是有声明处型变（declaration-site variance）与类型投影（type projections）。

Java 中的范型有类型不兼容的问题，例如 `Collection<E>` 可以读取 `E` 类型及其子类型，但是不能添加，要使用 `extends` 和 `super` 来确定范型的上界和下界。

```java
// Java
interface Collection<E> ... {
    void addAll(Collection<? extends E> items);
}
```

Kotlin 中如果范型 T 仅出现在方法返回，不在方法参数中，可以使用 `out` 修饰符。

```kotlin
interface Source<out T> {
    fun nextT(): T
}

fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs // 这个没问题，因为 T 是一个 out 参数
    // ……
}
```

反过来，如果范型参数只在函数方法中，不在返回值中，则可以使用 `in` 修饰符。

```kotlin
interface Comparable<in T> {
    operator fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    x.compareTo(1.0) // 1.0 是 Double 类型，是 Number 的子类型
    // 因此 x 可以兼容类型 Comparable<Double>
    val y: Comparable<Double> = x // 没问题
}
```

## 嵌套类

类可以嵌套在其他类中。

```kotlin
class Outer {
    private val bar: Int = 1
    class Nested {
        fun foo() = 2
    }
}

val demo = Outer.Nested().foo() // == 2
```

## 内部类

使用 inner 关键词的嵌套类能够访问其外部类的成员，内部类会带有一个对外部类的对象的引用。

```kotlin
class Outer {
    private val bar: Int = 1
    inner class Inner {
        fun foo() = bar
    }
}

val demo = Outer().Inner().foo() // == 1
```

要访问来自外部作用域的 `this` 使用 `this@label`，其中 `@label` 是一个代指 `this` 来源的标签。

## 匿名内部类

使用对象表达式可创建匿名内部类。

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { ... }

    override fun mouseEntered(e: MouseEvent) { ... }
})
```

## 枚举类

枚举类与 Java 类似。

```kotlin
enum class Direction {
    NORTH, SOUTH, WEST, EAST
}
```

枚举类可以有参数，实现接口（但不能继承类）。

## 内联类

内联类是基于值的类的子集，它们没有标识，只能容纳值。使用 `value` 关键词声明内联类。

```kotlin
value class Password(private val s: String)
```

对于 JVM 需要加上 `@JvmInline` 注解。

```kotlin
@JvmInline
value class Password(private val s: String)
```

内联类必须在主构造函数中含有唯一的一个属性并初始化。在运行时，将使用这个唯一属性来表示内联类的实例。

内联类支持定义属性和方法，包含初始化块等。

```kotlin
@JvmInline
value class Person(private val fullName: String) {
    init {
        require(fullName.isNotEmpty()) {
            "Full name shouldn't be empty"
        }
    }

    constructor(firstName: String, lastName: String) : this("$firstName $lastName") {
        require(lastName.isNotBlank()) {
            "Last name shouldn't be empty"
        }
    }

    val length: Int
        get() = fullName.length

    fun greet() {
        println("Hello, $fullName")
    }
}

fun main() {
    val name1 = Person("Kotlin", "Mascot")
    val name2 = Person("Kodee")
    name1.greet() // greet() 作为静态方法调用
    println(name2.length) // 属性获取作为静态方法调用
}
```

内联类可以实现接口，但不能继承类或被继承。

## 对象表达式

有时候需要创建一个对某个类做了轻微改动的类的对象，而不用为之显式声明新的子类。

对象表达式为一个匿名类创建实例，适合于一次性使用。

```kotlin
val helloWorld = object {
    val hello = "Hello"
    val world = "World"
    // 对象表达式继承 Any 类, 因此 toString() 方法的 override 是必须的
    override fun toString() = "$hello $world"
}

print(helloWorld)
```

对象表达式可以使用继承。

```kotlin
window.addMouseListener(object : MouseAdapter() {
    override fun mouseClicked(e: MouseEvent) { /*...*/ }

    override fun mouseEntered(e: MouseEvent) { /*...*/ }
})
```

## 对象声明

Kotlin 让单例模式很容易使用。

```kotlin
object DataProviderManager {
    fun registerDataProvider(provider: DataProvider) {
        // ...
    }

    val allDataProviders: Collection<DataProvider>
        get() = // ...
}
```

这称为对象声明，它不是一个表达式，不能用在赋值语句的右边。对象声明的初始化过程是线程安全的并且在首次访问时进行。如需引用该对象，直接使用其名称即可。

```kotlin
DataProviderManager.registerDataProvider(...)
```

对象可以继承其他类。

可以用 `data` 关键词修饰，将对象声明为数据类，自动生成相关的方法（但是没有 copy() 和 componentN() 方法）。

```kotlin
data object MyDataObject {
    val x: Int = 3
}

fun main() {
    println(MyDataObject) // MyDataObject
}
```

## 伴生对象

类内部的对象可以用 `companion` 关键词声明为伴生对象。

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass()
    }
}
```

该伴生对象的成员可通过只使用类名来调用。

```kotlin
val instance = MyClass.create()
```

可以省略伴生对象的名称，在这种情况下将使用名称 Companion 。

```kotlin
class MyClass {
    companion object { }
}

val x = MyClass.Companion
```

> 注：即使伴生对象的成员看起来像 Java 的静态成员，在运行时他们仍然是真实对象的实例成员

在 JVM 平台中，如果使用 `@JvmStatic` 注解，则可以生成为真正的静态成员。

## 对象表达式和对象声明的差异

- 对象表达式是在使用它们的地方立即执行以及初始化的。
- 对象声明是在第一次被访问到时延迟初始化的。
- 伴生对象的初始化是在相应的类被加载时，与 Java 静态初始化器的语义相匹配。

## 委托

委托是一种很好的实现继承的方式。

如下所示，`Derived` 类可以通过将其所有公有成员都委托给指定对象来实现一个接口 `Base`。

```kotlin
interface Base {
    fun print()
}

class BaseImpl(val x: Int) : Base {
    override fun print() { print(x) }
}

class Derived(b: Base) : Base by b

fun main() {
    val base = BaseImpl(10)
    Derived(base).print()
}
```

## 属性委托

Kotlin 支持属性委托，例如在如下的场景中：

- 延迟属性，只在首次访问时计算
- 可观察属性，监听器会收到属性变更的通知
- 把多个属性存储在一个 map 中，而不是单独的属性中

基本的语法

```kotlin
class Example {
    var p: String by Delegate()
}
```

无需实现接口，但需要提供 `getValue` 方法和 `setValue` 方法（用于 var）。

```kotlin
import kotlin.reflect.KProperty

class Delegate {
    operator fun getValue(thisRef: Any?, property: KProperty<*>): String {
        return "$thisRef, thank you for delegating '${property.name}' to me!"
    }

    operator fun setValue(thisRef: Any?, property: KProperty<*>, value: String) {
        println("$value has been assigned to '${property.name}' in $thisRef.")
    }
}
```

`val` 必须提供 `getValue`，参数为：

- thisRef 必须与属性所有者类型（对于扩展属性必须是被扩展的类型）相同或者是其父类型。
- property 必须是类型 KProperty<\*> 或其父类型。

返回与属性相同的类型或其子类型。

`var` 还必须提供 `setValue`，参数为：

- thisRef 必须与属性所有者类型（对于扩展属性必须是被扩展的类型）相同或者是其父类型。
- property 必须是类型 KProperty<\*> 或其父类型。
- value 必须与属性类型相同（或者是其父类型）。

两函数都需要用 `operator` 关键词来进行标记。

可以创建匿名对象实现接口 `ReadOnlyProperty` 和 `ReadWriteProperty` 来实现属性委托。

```kotlin
fun resourceDelegate(resource: Resource = Resource()): ReadWriteProperty<Any?, Resource> =
    object : ReadWriteProperty<Any?, Resource> {
        var curValue = resource
        override fun getValue(thisRef: Any?, property: KProperty<*>): Resource = curValue
        override fun setValue(thisRef: Any?, property: KProperty<*>, value: Resource) {
            curValue = value
        }
    }

val readOnlyResource: Resource by resourceDelegate()
var readWriteResource: Resource by resourceDelegate()
```

### 延迟属性

`lazy()` 是接受一个 lambda 并返回一个 Lazy <T> 实例的函数，返回的实例可以作为实现延迟属性的委托。第一次调用 get() 会执行已传递给 lazy() 的 lambda 表达式并记录结果。后续调用 get() 只是返回记录的结果。

```kotlin
val lazyValue: String by lazy {
    println("computed!")
    "Hello"
}

fun main() {
    println(lazyValue)
    println(lazyValue)
}
```

### 可观察属性

`Delegates.observable()` 接受两个参数：初始值与修改时的处理程序。每当我们给属性赋值时会调用该处理程序（在赋值后执行）。它有三个参数：被赋值的属性、旧值与新值。

```kotlin
import kotlin.properties.Delegates

class User {
    var name: String by Delegates.observable("<no name>") {
        prop, old, new ->
        println("$old -> $new")
    }
}

fun main() {
    val user = User()
    user.name = "first"
    user.name = "second"
}
```

如果想截获赋值并否决它们，那么使用 `vetoable()` 取代 `observable()`。在属性被赋新值之前会调用传递给 `vetoable` 的处理程序。

### 委托给另一个属性

一个属性可以把它的 getter 与 setter 委托给另一个属性。这种委托对于顶层和类的属性（成员和扩展）都可用。该委托属性可以为：

- 顶层属性
- 同一个类的成员或扩展属性
- 另一个类的成员或扩展属性

使用 `::` 限定符将一个属性委托给另一个属性。

```kotlin
var topLevelInt: Int = 0
class ClassWithDelegate(val anotherClassInt: Int)

class MyClass(var memberInt: Int, val anotherClassInstance: ClassWithDelegate) {
    var delegatedToMember: Int by this::memberInt
    var delegatedToTopLevel: Int by ::topLevelInt

    val delegatedToAnotherClass: Int by anotherClassInstance::anotherClassInt
}
var MyClass.extDelegated: Int by ::topLevelInt
```

当要向后兼容的时候很有用，可以引入一个新的属性，使用 `@Deprecated` 注解标记旧的属性，并委托其实现。

### 将属性存储在映射中

```kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}
```

代理属性使用字符串键从 map 中获取值。如果对 MutableMap 使用 var 也是可以的。

```kotlin
class User(val map: Map<String, Any?>) {
    val name: String by map
    val age: Int     by map
}

fun main() {
    val user = User(mapOf(
        "name" to "wwtg99",
        "age"  to 25
    ))

    println(user.name) // Prints "wwtg99"
    println(user.age)  // Prints 25

}
```

### 局部委托属性

可以将局部变量声明为委托属性，可用于惰性初始化。

```
fun example(computeFoo: () -> Foo) {
    val memoizedFoo by lazy(computeFoo)

    if (someCondition && memoizedFoo.isValid()) {
        memoizedFoo.doSomething()
    }
}
```

## 类型别名

类型别名为现有类型提供替代名称。如果类型名称太长，可以另外引入较短的名称，非常适合用于范型类型。

```kotlin
typealias NodeSet = Set<Network.Node>

typealias FileTable<K> = MutableMap<K, MutableList<File>>
```

也可以为函数类型提供别名。

```kotlin
typealias MyHandler = (Int, String, Any) -> Unit

typealias Predicate<T> = (T) -> Boolean
```

还可以为内部类或嵌套类提供别名。

```kotlin
class A {
    inner class Inner
}
class B {
    inner class Inner
}

typealias AInner = A.Inner
typealias BInner = B.Inner
```
