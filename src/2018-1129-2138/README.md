# Kotlin Examples - Destructuring Declarations

## 示例

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

```
Alice is 21 years old
Bob is 25 years old
```

1. 在左手边创建一个 component 来匹配右手边的 arity。
2. 它 (结构声明) 可用于 map 的遍历。`name` 和 `age` 变量映射到了 map 的 `key` 和 `value`。
3. 你可以结构内建的 Pairs 和 Triples，即使是作为函数的返回值。



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

```
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

```
num = 1, name = one
```

1. 使用 `component1` 和 `component2` 方法自定义一个 `Pair` 类。
2. 结构该类实例的方式与结构内建 `Pair` 的相同。
