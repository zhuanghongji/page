# Kotlin Examples - Else

## 示例

### Named Arguments

```kt
fun format(userName: String, domain: String) = "$userName@$domain"

fun main() {
//sampleStart
    println(format("mario", "example.com"))                         // 1
    println(format(userName = "foo", domain = "bar.com"))           // 2
    println(format(domain = "frog.com", userName = "pepe"))         // 3
//sampleEnd
}
```
```kt
mario@example.com
foo@bar.com
pepe@frog.com
```

1. 如同大多数编程语言 (Java, C++ .etc)，Kotlin 支持根据方法和构造方法定义的参数顺序进行传递参数。
2. Kotlin 还支持命名参数，这样可以使得调用时的代码更加清晰，并避免编译器无法检测到的错误 (例如，切换参数的正确顺序并以 *gmail.com@username* 形式构建电子邮件)。
3. 在调用方法或构造函数时，命名参数不必遵循定义的原始顺序。


### String Templates

```kt
fun main() {
//sampleStart
    val greeting = "Kotliner"

    println("Hello $greeting")                  // 1
    println("Hello ${greeting.toUpperCase()}")  // 2
//sampleEnd
}
```
```kt
Hello Kotliner
Hello KOTLINER
```

1. Kotlin 中的字符串可以包含插值变量的引用。
2. 除了简单的变量引用外，它们还可以包含用大括号括起来的任何表达式。


### Destructuring Declarations

解构声明语法非常方便，能帮你减少代码行数。

```kt
fun findMinMax(list: List<Int>): Pair<Int, Int> {
    // do the math
    return Pair(50, 100)
}

fun main() {
//sampleStart
    val (x, y, z) = arrayOf(5, 10, 15)                              // 1

    val map = mapOf("Alice" to 21, "Bob" to 25)
    for ((name, age) in map) {                                      // 2
        println("$name is $age years old")          
    }

    val (min, max) = findMinMax(listOf(100, 90, 50, 98, 76, 83))    // 3

//sampleEnd
}
```
```kt
Alice is 21 years old
Bob is 25 years old
```

1. 在左边创建一个 component 来匹配右边的 arity。
2. 解构声明可用于 map 的遍历，`name` 和 `age` 变量映射到了 map 的 `key` 和 `value`。
3. 即使是作为函数的返回值，也是可以解构内建的 `Pair`s 和 `Triple`s。



```kt
data class User(val username: String, val email: String)    // 1

fun getUser() = User("Mary", "mary@somewhere.com")

fun main() {
    val user = getUser()
    val (username, email) = user                            // 2
    println(username == user.component1())                  // 3

    val (_, emailAddress) = getUser()                       // 4

}
```
```kt
true
```

1. 定义一个数据类，在后面会被解构。
2. 映射到对应字段的值。
3. 数据类自动定义 `component1`，`component2`，这在解构过程中会被调用。
4. 如果你不需要其中某个值，可使用 *下划线 (underscore)*，以避免编译器提示未使用的变量。


```kt
class Pair<K, V>(val first: K, val second: V) {  // 1
    operator fun component1(): K {              
        return first
    }

    operator fun component2(): V {              
        return second
    }
}

fun main() {
    val (num, name) = Pair(1, "one")             // 2
    println("num = $num, name = $name")
}
```
```kt
num = 1, name = one
```

1. 使用 `component1` 和 `component2` 方法自定义一个 `Pair` 类。
2. 解构该类实例的方式与解构内建 `Pair` 的姿势相同。


### Smart Casts

Kotlin 的编译器非常智能，可以在大多数场景下自动执行类型转换，包括：
1. 从 nullable 类型转换成对应的 non-nullable 类型。
2. 从超类型转换成子类型。

```kt
import java.time.LocalDate
import java.time.chrono.ChronoLocalDate

fun main() {
//sampleStart
    val date: ChronoLocalDate? = LocalDate.now()    // 1

    if (date != null) {
        println(date.isLeapYear)                    // 2
    }

    if (date != null && date.isLeapYear) {          // 3
        println("It's a leap year!")
    }

    if (date == null || !date.isLeapYear) {         // 4
        println("There's no Feb 29 this year...")
    }

    if (date is LocalDate) {
        val month = date.monthValue                 // 5
        println(month)
    }
//sampleEnd
}
```
```kt
false
There's no Feb 29 this year...
11
```

1. 定义一个 nullable 变量。
2. 智能转换成 non-nullable (因此允许直接方位 `isLeapYear`)。
3. 在条件内进行智能转换 (这是可能的，类似 Java，Kotlin 使用 [short-circuiting](https://en.wikipedia.org/wiki/Short-circuit_evaluation))。
4. 在条件内进行智能转换 (也可通过 short-circuiting 实现)。
5. 智能转换成子类型 `LocalDate`。

这样，你可以在大多是情况下自动使用变量，而无需手动执行明显的强制转换。


## 扩展

<img src="./res/001.jpg" width="120"> :stuck_out_tongue_closed_eyes: