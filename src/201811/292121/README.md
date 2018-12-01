# Kotlin Examples - with

## 示例

with 是在标准库中定义的函数，使得我们可以以更简洁的方式访问对象的成员，从而避免必须为每个成员添加实例名称前缀。

```kt
class Configuration(var host: String, var port: Int)

fun main() {
    val configuration = Configuration(host = "127.0.0.1", port = 9000)

//sampleStart
    with(configuration) {
        println("$host:$port")
    }

    // instead of:
    println("${configuration.host}:${configuration.port}")    
//sampleEnd
}
```
