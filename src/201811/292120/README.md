# Kotlin Examples - let

## 示例

**let** 是 Kotlin 标准库中一个非常有用的函数。可用于「作用域」和「空检查」。

```kt
var i = 0
fun funWithSideEffect(): String = ".".repeat(i++)

fun getNullableString0(): String? = null
fun getNullableString1(): String? = null

fun main() {
//sampleStart
    val r = funWithSideEffect().let {
        it.isEmpty() || it.contains("Kotlin")                      // 1
    }
    println("r = $r")

    fun checkNullableString(ns: String?) {
        println("for \"$ns\":")

        ns?.let {                                                  // 2
            println("\tis empty? " + it.isEmpty())
            println("\tcontains Kotlin? " + it.contains("Kotlin"))
        }
    }
    checkNullableString(null)
    checkNullableString("")
    checkNullableString("some string with Kotlin")
//sampleEnd
}
```

1. `funWithSideEffect` 的返回结果现在可以用 `it` 进行访问了。
2. 使用安全调用，因此 `let` 及其代码块将带有 non-null 值执行。
