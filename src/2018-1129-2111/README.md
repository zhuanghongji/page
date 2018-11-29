# Kotlin Examples - Equality Checks

## 示例

Kotlin 使用 `==` 进行结构比较，使用 `===` 进行引用比较。

更准确地说，`a == b` 会编译成 `a == null ? b == null : a.equals(b)`。

```kt
val authors = setOf("Shakespeare", "Hemingway", "Twain")
val writers = setOf("Twain", "Shakespeare", "Hemingway")

println(authors == writers)   // 1
println(authors === writers)  // 2
```

```
true
false
```

1. 返回 `true` 是因为它调用了 `authors.equals(writers)` 并设置忽略元素的顺序。
2. 返回 `false` 是因为 `authors` 和 `writers` 是不同的引用。
