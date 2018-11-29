# Kotlin Examples - Higher-Order Functions

## 示例

高阶函数：将另一个函数作为参数或返回值的函数。

**将函数作为参数**

```kt
fun calculate(x: Int, y: Int, operation: (Int, Int) -> Int): Int {  // 1
    return operation(x, y)                                          // 2
}

fun sum(x: Int, y: Int) = x + y                                     // 3

fun main() {
    val sumResult = calculate(4, 5, ::sum)                          // 4
    val mulResult = calculate(4, 5) { a, b -> a * b }               // 5
    println("sumResult $sumResult, mulResult $mulResult")
}
```

1. 定义一个高阶函数，它接收两个类型为 interger 参数，x 和 y。另外，它还接收另一个名为 `operation` 的函数作为参数，该函数参数接收两个类型为 interger 的参数并返回一个类型为 interger 的整数。
2. 调用该参数函数，并传递另外提供的两个整型参数。
3. 声明匹配相同签名的函数。
4. 调用高阶函数并传入作为参数的函数，`::sum` 是我们在 Kotlin 中按名称引用函数的方式。
5. 调用高阶函数并传入作为参数的 lambda 函数。这样看起来更加清晰，不是吗？


**将函数作为参数返回**

```kt
fun operation(): (Int) -> Int {                                     // 1
    return ::square
}

fun square(x: Int) = x * x                                          // 2

fun main() {
    val func = operation()                                          // 3
    println(func(2))                                                // 4
}
```

1. 声明一个返回函数的高阶函数。
2. 返回的与签名相同的函数。
3. 调用 `operation` 获取返回结果并将其赋值给一个变量。
4. 调用该函数。
