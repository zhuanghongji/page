# Kotlin Examples - partition

## 示例

该函数将原始集合拆分为一对列表，第一个列表所含元素是给定断言为 `true` 时的，第二个列表所含元素时给定断言为 `false` 时的。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)                // 1

    val (postives, negatives) = numbers.partition { it > 0 } // 2
//sampleEnd

    println("Numbers: $numbers")
    println("Positive numbers: $postives")
    println("Negative numbers: $negatives")
}
```

1. 定义数字集合。
2. 使用 `partition` 函数将 `numbers` 分成两个列表，分别用于存储正数和负数。同时应用解构来获得 `pair` 中的成员。
