# Kotlin Examples - filter

## 示例

filter 是标准库中定义的函数，可用于集合的过滤，它接收的断言 (predicate) 作为 lambda 参数。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)      // 1

    val positives = numbers.filter { x -> x > 0 }  // 2

    val negatives = numbers.filter { it < 0 }      // 3
//sampleEnd

    println("Numbers: $numbers")
    println("Positive Numbers: $positives")
    println("Negative Numbers: $negatives")
}
```

1. 定义一个数字集合。
2. 过滤负数.
3. 或者使用更短的方式来过滤整数。
