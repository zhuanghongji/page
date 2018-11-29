# Kotlin Examples - first, last

## 示例

**`first`, `last`**

这些函数返回集合的第一个或最后一个元素，或返回与给定断言 (predicate) 相匹配的第一个或最后一个元素。

在空集合或集合没有与断言相匹配元素的情况下，会抛出一个 `NoSuchElementException`。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val first = numbers.first()                          // 2
    val last = numbers.last()                            // 3

    val firstEven = numbers.first { it % 2 == 0 }        // 4
    val lastOdd = numbers.last { it % 2 != 0 }           // 5
//sampleEnd

    println("Numbers: $numbers")
    println("First $first, last $last, first even $firstEven, last odd $lastOdd")
}
```

1. 定义数字集合。
2. 选择第一个元素。
3. 选择最后一个元素。
4. 选择第一个偶数。
5. 选择最后一个单数。
