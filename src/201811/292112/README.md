# Kotlin Examples - Conditional Expression

## 示例

在 Kotlin 中是没有三元运算符 (condition ? then : else) 的，取而代之的是 `if` 表达式。

```kt
fun max(a: Int, b: Int) = if (a > b) a else b         // 1

println(max(99, -42))
```

```
99
```

1. `if` 是一个表达式，它返回一个值。
