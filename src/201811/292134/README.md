# Kotlin Examples - Map element access

## 示例

`[]` 运算符返回与给定键 (key) 对应的值，如果该 map 中不存在该键的话则返回 `null`。

`getValue` 函数返回与给定键对应的现有值，或抛出异常，提示未找到该键。

如果该 map 是使用 withDefault 生成的，则该函数将返回默认值，而不是抛出异常。

```kt
fun main(args: Array<String>) {

//sampleStart
    val map = mapOf("key" to 42)

    val value1 = map["key"]                                     // 1
    val value2 = map["key2"]                                    // 2

    val value3: Int = map.getValue("key")                       // 1

    val mapWithDefault = map.withDefault { k -> k.length }
    val value4 = mapWithDefault.getValue("key2")                // 3

    try {
        map.getValue("anotherKey")                              // 4
    }
    catch (e: NoSuchElementException) {
        println("Message: $e")
    }

//sampleEnd

    println("value1 is $value1")
    println("value2 is $value2")
    println("value3 is $value3")
    println("value4 is $value4")
}
```

```
Message: java.util.NoSuchElementException: Key anotherKey is missing in the map.
value1 is 42
value2 is null
value3 is 42
value4 is 4
```

1. 产生 42 因为 `"key"` 在 map 中存在。
2. 产生 `null` 因为 `"key2"` 是缺失的。
3. 产生默认值 4 因为 `"key2"` 是缺失的。
4. 抛出 NoSuchElementException 因为 "anotherKey" 并不在 map 中。
