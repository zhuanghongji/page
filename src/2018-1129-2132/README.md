# Kotlin Examples - min, max

## 示例

返回集合中的最小或最大元素，如果集合为空则返回 `null`。

```kt
fun main() {

    val numbers = listOf(1, 2, 3)
    val empty = emptyList<Int>()

    println("Numbers: $numbers, min = ${numbers.min()} max = ${numbers.max()}") // 1
    println("Empty: $empty, min = ${empty.min()}, max = ${empty.max()}")        // 2
}
```

```
Numbers: [1, 2, 3], min = 1 max = 3
Empty: [], min = null, max = null
```

1. 对于非空集合元素，返回最小和最大元素。
2. 对于空集合，两个函数都会返回 `null`。
