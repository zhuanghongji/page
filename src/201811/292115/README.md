# Kotlin Examples - Sealed Classes

## 示例

```kt
sealed class Mammal(val name: String)                                                   // 1

class Cat(val catName: String) : Mammal(catName)
class Human(val humanName: String, val job: String) : Mammal(humanName)

fun greetMammal(mammal: Mammal): String {
    when (mammal) {                                                                     // 2
        is Human -> return "Hello ${mammal.name}; You're working as a ${mammal.job}"    // 3
        is Cat -> return "Hello ${mammal.name}"                                         // 4
                                                                                        // 5
    }
}

fun main() {
    println(greetMammal(Cat("Snowy")))
}
```

1. 使用两个子类定义一个密封类，这两个子类必须在同一个文件内。
2. 密封类可作为 `when` 表达式的参数。
3. 执行 smartcast 后，Mammal 转换为 Human。
4. 执行 smartcast 后，Mammal 转换为 Cat。
5. `else` 语句是不需要的，因为密封类的所有子类都覆盖了。这有悖于一般的超类，一般的超类总是需要 `else` 的。
