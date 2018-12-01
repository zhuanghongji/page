# Kotlin Examples - Lambda Functions

## 示例

Lambda 函数 ("lambdas") 是一种在创建 ad-hoc 函数的简单方法。由于类型推断和隐式 `it` 变量，Lambdas 在很多情况下的表示非常简洁。

```kt
fun main() {
//sampleStart
    // All examples create a function object that performs upper-casing.
    // So it's a function from String to String

    val upperCase1: (String) -> String = { str: String -> str.toUpperCase() } // 1

    val upperCase2: (String) -> String = { str -> str.toUpperCase() }         // 2

    val upperCase3 = { str: String -> str.toUpperCase() }                     // 3

    // val upperCase4 = { str -> str.toUpperCase() }                          // 4

    val upperCase5: (String) -> String = { it.toUpperCase() }                 // 5

    val upperCase6: (String) -> String = String::toUpperCase                  // 6

    println(upperCase2("hello"))
    println(upperCase1("hello"))
    println(upperCase3("hello"))
    println(upperCase5("hello"))
    println(upperCase6("hello"))
//sampleEnd
}
```

1. 一个充满荣耀的 lambda，到处都有明确的类型。lambda 是花括号的一部分，他被赋值给一个类型为 `(String) -> String` (函数类型) 的变量。
2. lambda 内部的类型推导：从它所分配的变量的类型推导 lambda 参数的类型。
3. lambda 外部的类型推导：根据 lambda 参数的类型和返回值推导变量的类型。
4. 你不能同时做这两件事情，在这种方式下编译器没有机会推导出类型。
5. 对于具有单个参数的 lambda，你不必明确命名它。你可以使用隐式 `it` 变量进行代替。任何情况下若 `it` 可以推导出其类型，是非常有用的，而大多数情况下是可以的。
6. 如果你的 lambda 仅含单个函数调用，你可能更喜欢函数指针。
