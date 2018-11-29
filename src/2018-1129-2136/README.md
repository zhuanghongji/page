# Kotlin Examples - Named Arguments

## 示例

```kt
fun format(userName: String, domain: String) = "$userName@$domain"

fun main() {
//sampleStart
    println(format("mario", "example.com"))                         // 1
    println(format(userName = "foo", domain = "bar.com"))           // 2
    println(format(domain = "frog.com", userName = "pepe"))         // 3
//sampleEnd
}
```

```
mario@example.com
foo@bar.com
pepe@frog.com
```

1. 如同大多数编程语言 (Java, C++ etc)，Kotlin 支持根据方法和构造方法定义的参数顺序进行它们传递参数。
2. Kotlin 还支持命名参数，使得可以调用更加清晰，并避免编译器无法检测到的错误 (例如，切换参数的正确顺序并以 *gmail.com@username* 形式构建电子邮件)。
3. 在调用方法或构造函数时，命名参数不必遵循定义的原始顺序。
