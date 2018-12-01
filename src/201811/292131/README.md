# Kotlin Examples - flatMap

## 示例

返回单个列表，其包含从原始集合中的每个元素调用 `transform` 函数的结果中产生的所有元素。

```kt
fun main() {

    //sampleStart
    val numbers = listOf(1, 2, 3)                        // 1

    val tripled1 = numbers.flatMap { listOf(it, it, it) } // 2
    val tripled2 = numbers.flatMap { listOf(it * 4, it, it) } // 2
    //sampleEnd

    println("Numbers: $numbers")
    println("Transformed1: $tripled1")
    println("Transformed2: $tripled2")
}
```

```
Numbers: [1, 2, 3]
Transformed1: [1, 1, 1, 2, 2, 2, 3, 3, 3]
Transformed2: [4, 1, 1, 8, 2, 2, 12, 3, 3]
```

1. 定义数字集合。
2. 转换集合的每个元素，重复三次。
