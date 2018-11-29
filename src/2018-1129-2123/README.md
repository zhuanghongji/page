# Kotlin Examples - map

## 示例

map 是在标准库中定义的扩展函数，可用于转将集合转好成另一个集合。它需要一个 transformer 作为 lambda 参数。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)     // 1

    val doubled = numbers.map { x -> x * 2 }      // 2

    val tripled = numbers.map { it * 3 }          // 3
//sampleEnd

    println("Numbers: $numbers")
    println("Doubled Numbers: $doubled")
    println("Tripled Numbers: $tripled")
}
```

1. 定义数字集合。
2. 将数字双倍化。
3. 或者使用更短的方式将数字三倍化。
