# Kotlin Examples - Functions

## 示例

### 默认参数值和命名参数

```kt
package com.zhuanghongji.kotlin.examples.introduction

fun printMessage(message: String): Unit {                                 // 1
    println(message)
}

fun printMessageWithPrefix(message: String, prefix: String = "Info") {    // 2
    println("[$prefix] $message")
}

fun sum(x: Int, y: Int): Int {                                            // 3
    return x + y
}

fun multiply(x: Int, y: Int) = x * y                                      // 4

fun main() {
    printMessage("Hello")                                                 // 5
    printMessageWithPrefix("Hello", "Log")                                // 6
    printMessageWithPrefix("Hello")                                       // 7
    printMessageWithPrefix(prefix = "Log", message = "Hello")             // 8
    println(sum(1, 2))                                                    // 9
}
```

```
Hello
[Log] Hello
[Info] Hello
[Log] Hello
3
```

1. 创建一个函数，其接收一个 `String` 类型参数，返回值类型为 `Unit` (即没有返回值)。
2. 创建一个函数，其接收的第二个参数默认值为 `"Info"`，返回值类型同样为 `Unit` (可省略)。
3. 创建一个函数，返回值类型为 `Int`。
4. 创建一个单表达式函数，返回值类型为 `Int` (推断)。
5. 调用一个函数并传递值为 `Hello` 的参数。
6. 调用一个接收两个参数的函数，第二个可选参数也传值。
7. 调用一个接收两个参数的函数，第二个可选参数不传值 (忽略)。
8. 使用「命名参数」调用一个函数，并且改变了参数传递的顺序。
9. 调用一个函数，并且打印其返回值。


### 中缀函数 (Infix functions)

具有单个参数的成员函数或扩展函数都可以转换为「中缀函数」。

```kt
fun main() {

    infix fun Int.times(str: String) = str.repeat(this)         // 1
    println(2 times "Bye")                                      // 2

    val pair = "Ferrari" to "Katrina"                           // 3
    println(pair)

    infix fun String.onto(other: String) = Pair(this, other)    // 4
    val myPari = "McLaren" onto "Lucas"
    println(myPari)

    val sophia = Person("Sophia")
    val claudia = Person("Claudia")
    sophia likes claudia                                        // 5
}

class Person(val name: String) {

    val likedPeople = mutableListOf<Person>()

    infix fun likes(other: Person) {
        likedPeople.add(other)                                  // 6
    }
}
```

```
ByeBye
(Ferrari, Katrina)
(McLaren, Lucas)
```

1. 在 `Int` 上定义了一个中缀扩展函数。
2. 调用中缀函数。
3. 来自标准库的中缀函数 `to` 可以让你更容易的创建 `Pair`。
4. 这是你自己的 `to` 实现，名为 `onto`。
5. 中缀同样适用于成员函数。
6. 包含的类作为第一个参数。


### 操作函数 (Operator functions)

某些函数可以 "升级" 为 [operators](https://kotlinlang.org/docs/reference/operator-overloading.html)，允许将它们与相应的操作符号一起使用。

```kt
operator fun Int.times(str: String) = str.repeat(this)        // 1
println(2 * "Bye ")                                           // 2

operator fun String.get(range: IntRange) = substring(range)   // 3
val str = "Always forgive your enemies; nothing annoys them so much."
println(str[0..14])                                           // 4
```

```
Bye Bye
Always forgive
```

1. 这里使用 `operator` 操作符进一步使用了上面的中缀函数。
2. `times()` 的操作符是 `*`，所以你使用 `2 * "Bye "` 进行调用。
3. 一个操作函数可以使得访问字符串更加容易。
4. `get()` 操作者，[中括号语法](https://kotlinlang.org/docs/reference/operator-overloading.html#indexed)。


### 带 `vararg` 参数的函数**

[Varargs](https://kotlinlang.org/docs/reference/functions.html#variable-number-of-arguments-varargs) 允许我们传入任意数量的参数 (通过逗号进行分隔)。

```kt
fun printAll(vararg messages: String) {                            // 1
    for (m in messages) println(m)
}
printAll("Hello", "Hallo", "Salut", "Hola", "你好")                 // 2

fun printAllWithPrefix(vararg messages: String, prefix: String) {  // 3
    for (m in messages) println(prefix + m)
}
printAllWithPrefix(
    "Hello", "Hallo", "Salut", "Hola", "你好",
    prefix = "Greeting: "                                          // 4
)

fun log(vararg entries: String) {
    printAll(*entries)                                             // 5
}
```

```
Hello
Hallo
Salut
Hola
你好
Greeting: Hello
Greeting: Hallo
Greeting: Salut
Greeting: Hola
Greeting: 你好
```

1. `vararg` 修饰符将一个参数转换为可变参数。
2. 这允许你使用任意数量的字符串参数调用 `printAll` 函数。
3. 得益于命名参数，你甚至可以在 `vararg` 之后添加相同类型的另一个参数。而这在 Java 中是不允许的，因为已经没法儿去传递一个值了。
4. 使用命名参数时，你可以为 `prefix` 参数设置一个值，尽管它前面是 `vararg` 参数。
5. 在运行时，`vararg` 只是一个数组。要想传递给一个 `vararg` 参数，你可以使用特殊的延展操作符 `*foo` 来传入 `*entries` (一个 `String` 可变参数) 而不是 `entries` (一个 `Array<String>`)。


## 扩展

### 默认参数

在继承类时，如果需要覆盖父类的带有默认值参数的方法，覆写时就必须在方法签名中省略默认参数值。也就是说，「覆盖方法」总是使用与「父类方法」相同的默认参数值：

```kt
open class A {
    open fun foo(i: Int = 10) {
        println("A: i = $i")
    }
}

class B: A() {
    override fun foo(i: Int) {
        println("B: i = $i")
    }
}

fun main() {
    A().foo()           // A: i = 10
    B().foo()           // B: i = 10
    B().foo(i = 18)     // B: i = 18
}
```

如果在默认参数之后的最后一个参数是 Lambda 表达式，那么它既可以作为命名参数在括号内传入，也可以在括号外传入：

```kt
fun foo(bar: Int = 2, baz: Int = 3, calc: (Int, Int) -> Int) {
    val result = calc(bar, baz)
    println(result)
}

fun main() {
    foo(calc = { bar, baz -> bar + baz })                      // 5
    foo(bar = 0, calc = { bar, baz -> bar + baz })             // 3
    foo(bar = 1, baz = 2, calc = { bar, baz -> bar + baz })    // 3

    foo { bar, baz -> bar + baz }           // 5
    foo(0) { bar, baz -> bar + baz }        // 3
    foo(1, 2) { bar, baz -> bar + baz }     // 3
}
```


## 命名参数

当一个函数有着大量的参数或默认参数时，如果是通过命名参数进行调用的话，代码的可读性就会显得更加清晰。比如说：

```kt
// 方法声明
fun reformat(str: String,
             normalizeCase: Boolean = true,
             upperCaseFirstLetter: Boolean = true,
             divideByCamelHumps: Boolean = false,
             wordSeparator: Char = ' ') {
    // ...             
}


val str = "abc"

// 不使用命名参数调用
reformat(str, true, true, false, '_')

// 使用命名参数
reformat(str,
        normalizeCase = true,
        upperCaseFirstLetter = true,
        divideByCamelHumps = false,
        wordSeparator = '_'
)

// 使用命名参数 + 参数默认值
reformat(str, wordSeparator = '_')
```

通过「命名参数」和「星号运算符 `*`」可以将 **可变数量参数** 传入函数中，比如：

```kt
fun foo(vararg strings: String, prefix: String) {
    for (s in strings) {
        println("$prefix $s")
    }
}

foo(strings = *arrayOf("a", "b", "c"), prefix = "*")
// * a
// * b
// * c
```

### 中缀表示法

通过 `infix` 关键字标识的函数称为中缀函数，中缀函数可以使用中缀表示法进行调用。也就是说，在调用时介意忽略 `.` 和 `()`。

注意，中缀函数必须满足以下三个条件：
* 必须是成员函数或扩展函数。
* 有且只有一个参数。
* 该参数不是「可变数量参数」，而且不能有默认值。

```kt
infix fun Int.plusSomething(x: Int): Int {
    return this + x
}

val a = 2
a.plusSomething(3)    // 常规调用，返回值是 5
a plusSomething 3     // 中缀法调用，返回值是 5
```

中缀函数总是要求制定「接受者」与「参数」。当使用中缀表示法在当前接受者上调用方法时，需要显式使用 `this`，以确保表达上的清晰：

```kt
class Something {
    private infix fun log(s: String) {
        println(s)
    }

    fun testLog() {
        log("abc")       // 正确
        this log "abc"   // 正确
        // log "abc"     // 错误：必须显式使用 this
    }
}

Something().testLog()   // 日志输出：abc
```


### 局部函数

Kotlin 支持局部函数，即一个函数可以声明在另一个函数的内部：

```kt
fun outerFun() {
    fun innerFun(x: String) {
        println(x)
    }

    innerFun("a")
    innerFun("b")
    innerFun("c")
}

outerFun()
// a
// b
// c
```


### 函数类型

在 Kotlin 中，函数也是一等公民，可以使用类似 `(Int) -> String` 的函数类型进行声明。比如：

```kt
val onClick: () -> Unit = { println("onClick") }

val doSomething: (Int) -> String = {
    num -> if (num > 0) "Positive" else "Negative"
}

onClick()                   // onClick
println(doSomething(2))     // Positive
```

函数类型具有与函数签名相对应的表示，也就是说，它们的参数和返回值都是相对应的。

* 所有函数类型都有一个圆括号括起来的「参数类型列表」和「返回类型」：
  * 参数列表可以为空，比如 `() -> D`。
  * 当返回类型为 `Unit` 时，`Unit` 声明不能省略。
  * 比如说，`(A, B) -> C` 表示该函数接收两个类型分别为 `A` 和 `B` 的参数，返回值类型为 `C`。
* 函数类型可以有一个额外的接受者类型，但该接受者类型需要在 `.` 符号之前指定：
  * 比如说，`A.(B) -> C` 表示该函数可以在类型为 `A` 的接受者对象上以一个 `B` 类型参数来调用，返回值类型为 `C`。
* 挂起函数属于属于特殊种类的函数类型，它的表示法中有一个 `suspend` 修饰符；
  * 例如，`suspend () -> Unit` 或 `suspend A.(B) -> C`。

在函数类型表示法中，我们可以选择性地包含函数的参数名：

```kt
val doSomething1: (Int) -> String = { ... }
val doSomething2: (num: Int) -> String = { ... }
```

也可以通过「类型别名」来给函数类型起一个别称：

```kt
typealias DoSomething = (num: Int) -> String 

val doSomething3: DoSomething = { ... }
val doSomething4: DoSomething = { ... }
```

补充说明：
* 如需将函数类型指定为可空，请使用圆括号：`((Int, Int) -> Int)?`。
* 函数类型可以使用圆括号进行接合：`(Int) -> ((Int) -> Unit)`。
* 箭头表示法是右结合的，所以 `(Int) -> (Int) -> Unit` 等价于 `(Int) -> ((Int) -> Unit)`。注意，等价并不意味着相同。

下面是一段示例代码：

```kt
// 通过类型别名定义了三个函数类型
typealias DoSomething1 = ((Int) -> String)?
typealias DoSomething2 = (Int) -> ((Boolean) -> String)
typealias DoSomething3 = (Int) -> (Boolean) -> String

// 通过函数类型声明函数，并实例化
val doSomething1: DoSomething1 = { it.toString() }
val doSomething2: DoSomething2 = {
    if (it > 0) {
        { a -> if (a) "T" else "F" }
    } else {
        { a -> if (a) "t" else "f" }
    }
}
val doSomething3: DoSomething3 = { num ->
    if (num > 0) {
        { a -> if (a) "T" else "F" }
    } else {
        { a -> if (a) "t" else "f" }
    }
}

// 测试
fun main() {
    // 需要对 doSomething1 进行判空，否则会报错
    // Error:(34, 17) Kotlin: Reference has a nullable type
    // 'DoSomething1 /* = ((Int) -> String)? */', use explicit '?.invoke()'
    // to make a function-like call instead
    if (doSomething1 != null) {
        println(doSomething1(2))            // 2
    }
    println(doSomething1?.invoke(3))        // 3


    // 等价并不意味着相同
    println(doSomething2 == doSomething3)   // false

    println(doSomething2(0)(true))          // t
    println(doSomething2(0)(false))         // f
    println(doSomething2(1)(true))          // T
    println(doSomething2(1)(false))         // F

    println(doSomething3(0)(true))          // t
    println(doSomething3(0)(false))         // f
    println(doSomething3(1)(true))          // T
    println(doSomething3(1)(false))         // F
}
```

**函数类型实例化**

有以下几种方法可以获得函数类型的实例：

* 使用函数字面值代码块，比如：
  * Lambda 表达式：`{ a, b -> a + b}`。
  * 匿名函数：`fun(s: String): Int { return s.toIntOrNull() ?: 0}`
* 使用已有声明的可调用引用：
  * 顶层、成员、局部、扩展函数：`::isOdd`、`String::toInt` 等。
  * 顶层、成员、扩展属性：`List<Int>::size` 等。
  * 构造函数：`::Regex`。
* 使用实现函数类型接口的自定义类的实例，比如：
  ```kt
  class IntChecker: (Int) -> String {
      override fun invoke(p1: Int): String {
          return if (p1 % 2 == 1) "奇数" else "偶数"
      }
  }

  val intChecker: (Int) -> String = IntChecker()
  println(intChecker.invoke(7))   // 奇数
  println(intChecker.invoke(8))   // 偶数
  ```

如果有足够信息，编译器也是可以推断出变量的函数类型的：

```kt
val a = { i: Int -> i + 1 }  // 推断出函数类型为 (Int) -> Int
```

带与不带接受者的函数类型之间的非字面量是可以互换的，其中接收者可以替代第一个参数，第一个参数也可以替代接受者。例如，`(A, B) -> C` 类型的值可以传给或赋值给期待 `A.(B) -> C` 类型的参数或变量，反之亦然。示例如下：

```kt
// 示例
val repeatFun: String.(Int) -> String = { times -> this.repeat(times) }
val twoParameters: (String, Int) -> String = repeatFun

fun runTransformation(f: (String, Int) -> String): String {
    return f("B", 4)
}


// 测试
fun main() {
    println(repeatFun("A", 3))          // AAA
    println(twoParameters("A", 3))      // AAA

    val result = runTransformation(repeatFun)
    println(result)                     // BBBB
}
```

**函数类型实例调用**

函数类型实例的调用方式有两种：

* 实例的值通过 `invoke(...)` 操作符进行调用，比如：`f.invoke(x)`。
* 直接通过 `f(x)` 进行调用。


## 高阶函数

高阶函数：可以将函数作为参数或者返回值的函数。

<img src="./res/001.jpg" width="120">

```kt
fun hof(num: Int, check: (Int) -> Boolean): (String) -> String {
    if (check(num)) {
        return { value: String -> value.repeat(1) }
    } else {
        return { value: String -> value.repeat(2)}
    }
}
```

上面的 `hof` 函数是一个高阶函数，它接收一个类型为 `Int` 和一个类型为 `(Int) -> Boolean` 的参数，并且返回值类型为 `(String) -> String`，它的函数体逻辑是这样的：
* 当 `check(num)` 返回值为 `true` 时，返回值函数会 **将其参数重复一遍** 并作为结果返回。
* 当 `check(num)` 返回值为 `false` 时，返回值函数会 **将其参数重复两遍** 并作为结果返回。

现在我们来测试一下 `hof` 这个高阶函数：

```kt
// hoc 函数的返回结果是一个类型为 (String) -> String 的新函数

// 如果 num 是奇数，该新函数：重复字符串参数一遍再返回
// 如果 num 是偶数，该新函数：重复字符串参数两遍再返回
val a = hof(3) { num -> num % 2 == 1 } ("A")
val b = hof(4) { num -> num % 2 == 1 } ("B")
println("a = $a, b = $b")     // a = A, b = BB

// 如果 num 小于十，该新函数：重复字符串参数一遍再返回
// 如果 num 大于十，该新函数：重复字符串参数两遍再返回
val param = { num: Int -> num < 10 }
val c = hof(9, param) ("C")
val d = hof(11, param) ("D")
println("c = $c, d = $d")     // c = C, d = DD
```


### Lambda 表达式

> Lambda 发音为: `['læmdə]`
> 
> 前面我们已经使用了很多类似 `{ a, b -> c }` 的语法，这其实就是我们接下来要讲的 Lambda 表达式。

Lambda 表达式是未声明的函数 (函数字面值)，可立即作为表达式进行传递。比如：

```kt
max(strings, { a, b -> a.length < b.length })
```

函数 `max` 是一个高阶函数，它接收的第二个参数 `{ a, b -> a.length < b.length }` 就是一个 Lambda 表达式，该表达式与下面的命名函数是等价的：

```kt
fun compare(a: String, b: String): Boolean = a.length < b.length
```

Lambda 表达式的完整语法形式如下：

```kt
val sum = { x: Int, y: Int -> x + y }
```

Lambda 表示式总是括在 `{ }` 中，参数在 `->` 符号之前，函数体在 `->` 符号之后，其中参数的类型标注是可选的。另外，如果推断出该 Lambda 表达式的返回值类型不是 `Unit`，则其主体的最后一个表达式会自动被视为返回值。

如果把所有类型标注都留下，看起来是酱紫的： :stuck_out_tongue_winking_eye:

```kt
val sum: (Int, Int) -> Int = { x, y -> x + y }
```

**将 Lambda 表达式传给最后一个参数**

如果函数的最后一个参数是函数，那么相应传入的 Lambda 表达式是可以放在圆括号外面的。比如：

```kt
fun printNum(num: Int, f: (Int) -> Int) {
    println(f(num))
}

printNum(2) { it * 3 }    // 6
```

另外，如果该 Lambda 表达式是调用函数时唯一的参数，则圆括号是可以省略的。比如：

```
fun printResult(f: (Int) -> Int) {
    println(f(2))
}

printResult { it * 4 }   // 8
```

如果编译器自己可以识别出「只有一个参数的 Lambda 表达式」的签名，则可以不声明该参数并忽略 `->` 符号。这样的话，该参数会被隐式声明为 `it`。

> 这部分内容在前面讲命名参数的时候提到过，这里又啰嗦了一下下。


**Lambda 补充示例**

由于类型推断和隐式的 `it` 变量，Lambdas 在 Kotlin 中的表示可以做到非常简洁：

```kt
fun main() {
    // All examples create a function object that performs upper-casing.
    // So it's a function from String to String

    // 显式声明了所有相关的类型
    // Lambda 是花括号部分，赋值给了函数类型为 (String) -> String 的变量
    val upperCase1: (String) -> String = { str: String -> str.toUpperCase() } 

    // Lambda 内部进行了类型推导 (不再对 str 显式声明类型)
    // 从它所分配的变量的类型推导出 Lambda 参数的类型
    val upperCase2: (String) -> String = { str -> str.toUpperCase() }   

    // Lambda 外部进行了类型推导 (不再显式声明 upperCase3 的类型)
    val upperCase3 = { str: String -> str.toUpperCase() }              

    // 但你不能同时进行 "内部类型推导" 和 "外部类型推导" 这两件事
    // 因为这样并没有提供任何线索给编译器进行类型推导
    // val upperCase4 = { str -> str.toUpperCase() }   

    // 对于具有单个参数的 Lambda，不必进行明确声明，可直接使用隐式 it 变量代替
    // 大多数情况下，it 的类型都是可以推导出来的
    val upperCase5: (String) -> String = { it.toUpperCase() }   

    // 如果 Lambda 仅含单个函数调用，你可能更喜欢函数指针
    val upperCase6: (String) -> String = String::toUpperCase    

    println(upperCase1("hello"))    // HELLO
    println(upperCase2("hello"))    // HELLO
    println(upperCase3("hello"))    // HELLO
    println(upperCase5("hello"))    // HELLO
    println(upperCase6("hello"))    // HELLO
}
```


## 总结

在这篇文章中，我们主要讲解了 Kotlin 函数部分的内容，比如：
* 参数默认值
* 命名参数
* 中缀函数
* 函数类型
* 高阶函数
* Lambda 表达式
* ...

实际上，Kotlin 函数部分还有很多知识点没有讲到，比如：闭包、内联函数等。大家可以查看官方文档，或搜索相关内容进行了解。