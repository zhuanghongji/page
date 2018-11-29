# Kotlin Examples - getOrElse

## 示例

提供对集合元素的安全访问。它采用索引和一个函数来计算索引超出范围时的默认值。

```
fun main() {

//sampleStart
    val list = listOf(0, 10, 20)
    println(list.getOrElse(1) { 42 })    // 1
    println(list.getOrElse(10) { 42 })   // 2
//sampleEnd
}
```

```
10
42
```

1. 打印出索引为 1 的元素。
2. 打印出 42 因为索引 10 超出了范围。


`getOrElse` 也可以应用在 `Map` 上来获取给定键对应的值。

```kt
fun main() {

//sampleStart
    val map = mutableMapOf<String, Int?>()
    println(map.getOrElse("x") { 1 })       // 1

    map["x"] = 3
    println(map.getOrElse("x") { 1 })       // 2

    map["x"] = null
    println(map.getOrElse("x") { 1 })       // 1
//sampleEnd
}
```

```
1
3
1
```

1. 打印出默认值，因为键 `"X"` 是缺失的。
2. 打印出键 `"X"` 对应的值 `3`。
