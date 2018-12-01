# Kotlin Examples - sorted

## 示例

`sorted` 会返回一个列列表，根据其自然排序顺序排序的所有元素。

`sorted` 会返回，根据指定选择器函数返回的值的自然排序顺序排序的所有元素。

```kt
fun main() {

//sampleStart
    val shuffled = listOf(5, 4, 2, 1, 3)     // 1
    val natural = shuffled.sorted()          // 2
    val inverted = shuffled.sortedBy { -it } // 3
//sampleEnd

    println("Shuffled: $shuffled")
    println("Natural order: $natural")
    println("Inverted natural order: $inverted")
}
```

```
Shuffled: [5, 4, 2, 1, 3]
Natural order: [1, 2, 3, 4, 5]
Inverted natural order: [5, 4, 3, 2, 1]
```

1. 定义混乱的数字集合。
2. 按自然顺序排序。
3. 按方向自然顺序排序。
