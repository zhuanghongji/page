# Kotlin Examples - Functions

## 示例

**默认参数值和命名参数**

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


**中缀函数 (Infix functions)**

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


**操作函数 (Operator functions)**

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


**带 `vararg` 参数的函数**

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
