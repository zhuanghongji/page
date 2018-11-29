# Kotlin Examples - find

## 示例

返回与给定断言 (predicate) 相匹配的第一个或最后一个元素，如果没有相匹配的话则返回 `null`。

```kt
fun main() {

//sampleStart
    val words = listOf("Lets", "find", "something", "in", "collection", "somehow")  // 1

    val first = words.find { it.startsWith("some") }                                // 2
    val last = words.findLast { it.startsWith("some") }                             // 3

    val nothing = words.find { it.contains("nothing") }                             // 4
//sampleEnd

    println("The first word starting with \"some\" is \"$first\"")
    println("The last word starting with \"some\" is \"$last\"")
    println("The first word containing \"nothing\" is ${nothing?.let { "\"$it\"" } ?: "null"}")
}
```

1. 定义有着不同单词元素的集合。
2. 查找第一个以 "some" 开头的单词。
3. 查找最后一个以 "some" 开头的单词。
4. 查找第一个含有 "nothing" 的单词。
