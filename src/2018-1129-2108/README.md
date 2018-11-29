# Kotlin Examples - When

## 示例

在 Kotlin 中并没有 `switch` 声明，替代它的有着更加智能、结构更加清晰的 `when`。

```kt
fun main() {
    cases("Hello")
    cases(1)
    cases(0L)
    cases(MyClass())
    cases("hello")
}

fun cases(obj: Any) {
    when (obj) {
        1 -> println("One")                          // 1
        "Hello" -> println("Greeting")               // 2
        is Long -> println("Long")                   // 3
        !is String -> println("Not a string")        // 4
        else -> println("Unknown")                   // 5
    }
}

class MyClass
```

```
Greeting
One
Long
Not a string
Unknown
```

1. 检查 `obj` 是否等同于 `1`。
2. 检查字符串。
3. 检查实例。
4. 否定的实例检查。
5. 默认语句 (可能省略)。
