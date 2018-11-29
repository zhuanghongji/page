# Kotlin Examples - zip

## 示例

将一个集合与其它集合合并到新的集合中。返回的集合要么包含一个来自源集合并且具有相同索引的 `Pair`，要么包含具有相同索引的元素给定变化后的结果。

一起来看一下：

```kt
fun main() {

//sampleStart
    val A = listOf("a", "b", "c")                  // 1
    val B = listOf(1, 2, 3, 4)                     // 1

    val resultPairs = A zip B                      // 2
    val resultReduce = A.zip(B) { a, b -> "$a$b" } // 3
//sampleEnd

    println("A to B: $resultPairs")
    println("\$A\$B: $resultReduce")
}
```

```
A to B: [(a, 1), (b, 2), (c, 3)]
$A$B: [a1, b2, c3]
```

1. 定义两个集合。
2. 只用中缀函数将他们 zip 到 pairs 中。
3. 将他们连接在一起。
