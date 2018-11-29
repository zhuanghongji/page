# Kotlin Examples - String Templates

## 示例

```kt
fun main() {
//sampleStart
    val greeting = "Kotliner"

    println("Hello $greeting")                  // 1
    println("Hello ${greeting.toUpperCase()}")  // 2
//sampleEnd
}
```

```
Hello Kotliner
Hello KOTLINER
```

1. Kotlin 中的字符串可以包含插值变量的引用。
2. 除了简单的变量引用外，它们还可以包含用大括号括起来的任何表达式。
