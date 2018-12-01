# Kotlin Examples - count

## 示例

该扩展函数返回集合元素的总个数，或返回与给定断言 (predicate) 相匹配的元素的个数。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val totalCount = numbers.count()                     // 2
    val evenCount = numbers.count { it % 2 == 0 }        // 3
//sampleEnd

    println("Total number of elements: $totalCount")
    println("Number of even elements: $evenCount")
}
```

1. 定义数字集合。
2. 计算元素总数。
3. 计算偶数元素的总数。
