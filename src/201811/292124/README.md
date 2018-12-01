# Kotlin Examples - any, all, none

## 示例

这些扩展函数基于给定断言 (predicate) 回答了关于集合中存有元素的问题。

**`any` 函数**

如果集合含有至少一个与给定断言相匹配的元素，则 `any` 函数将返回 `true`。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val anyNegative = numbers.any { it < 0 }             // 2

    val anyGT6 = numbers.any { it > 6 }                  // 3
//sampleEnd

    println("Numbers: $numbers")
    println("Is there any number less than 0: $anyNegative")
    println("Is there any number greater than 6: $anyGT6")
}
```

1. 定义数字集合。
2. 集合中是否存在小于 0 的元素。
3. 集合中是否存在大于 6 的元素。


**`all` 函数**

如果集合中所有的元素都与给定断言相匹配，则 `all` 函数返回 `true`。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val allEven = numbers.all { it % 2 == 0 }            // 2

    val allLess6 = numbers.all { it < 6 }                // 3
//sampleEnd

    println("Numbers: $numbers")
    println("All numbers are even: $allEven")
    println("All numbers are less than 6: $allLess6")
}
```

1. 定义数字集合。
2. 集合中所有元素是否都是偶数。
3. 集合中所有元素是否都小于 6。


**`none` 函数**

如果集合中没有元素与给定断言相匹配，则 `none` 函数返回 `true`。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val allEven = numbers.none { it % 2 == 1 }           // 2

    val allLess6 = numbers.none { it > 6 }               // 3
//sampleEnd

    println("Numbers: $numbers")
    println("All numbers are even: $allEven")
    println("No element greater than 6: $allLess6")
}
```

1. 定义数字集合。
2. 集合中是否没有单数，即集合中所有的元素都是偶数。
3. 集合中是否没有元素大于 6。
