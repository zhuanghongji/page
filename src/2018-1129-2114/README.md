# Kotlin Examples - Enum Classes

## 示例

枚举类用于模拟表示一组有限的、不同值的类型，比如方向、状态和模式等。

类似其它类，它们可能包含属性和方法，通过分号与枚举实例分隔。

```kt
enum class State {
    IDLE, RUNNING, FINISHED                           // 1
}

enum class Color(val rgb: Int) {                      // 2
    RED(0xFF0000),                                    // 3
    GREEN(0x00FF00),
    BLUE(0x0000FF),
    YELLOW(0xFFFF00);

    fun containsRed() = (this.rgb and 0xFF0000 != 0)  // 4
}

fun main() {
    val red = Color.RED                               // 5
    println(red)                                      // 6
    println(red.containsRed())
    println(Color.BLUE.containsRed())

    val state = State.RUNNING
    val message = when (state) {                      // 7
        State.IDLE -> "It's idle"
        State.RUNNING -> "It's running"
        State.FINISHED -> "It's finished"
    }
    println(message)
}
```

```
RED
true
false
It's running
```

1. 一个带有三个枚举实例的简单枚举类 (注意实例是有限且不同的)。
2. 一个具有属性一个属性和一个方法的枚举类。
3. 每个实例都必须为构造函数参数传递一个参数值。
4. 使用分号将成员与实例分开。
5. 可以通过类名直接访问枚举实例。
6. 默认的 `toString` 方法会返回实例的名字，这里是 `"RED"`。
7. 使用枚举时，编译器可以推断出 `when`-表达式 是否是详尽的，因此你不需要使用 `else`-case。
