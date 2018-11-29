# Kotlin Examples - Null Safety

## 示例

为了摆脱 `NullPointerException`，Kotlin 中的变量类型不允许赋值 `null`。

```kt
var neverNull: String = "This can't be null"            // 1

neverNull = null                                        // 2

var nullable: String? = "You can keep a null here"      // 3

nullable = null                                         // 4

var inferredNonNull = "The compiler assumes non-null"   // 5

inferredNonNull = null                                  // 2
```

```
Null can not be a value of a non-null type String
Null can not be a value of a non-null type String
```

1. 声明一个非 `null` 的变量。
2. 尝试对非 `null` 变量进行赋值，会产生编译错误。
3. 声明一个可为 `null` 的变量。
4. 将可为 `null` 的变量设为 `null`。
5. 当进行类型推导时，编译器会对使用值进行初始化的变量假设为非 `null`。


## Working with nulls

有时 Kotlin 程序是需要使用 null 值的，例如与外部 Java 代码交互或表示真正的值缺失状态。Kotlin 提供 null 跟踪以优雅地处理这种使用场景。

```kt
fun describeString(maybeString: String?): String {              // 1
    if (maybeString != null && maybeString.length > 0) {        // 2
        return "String of length ${maybeString.length}"
    } else {
        return "Empty or null string"
    }
}
```

```
Empty or null string
```

1. 一个接收 nullable 的字符串，并返回其描述短语的函数。
2. 如果给定参数字符串不为 `null` 也不为 `空串`，则告诉调用者它的长度。
