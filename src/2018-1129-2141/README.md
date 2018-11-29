# Kotlin Examples - Kotlin Examples - Delegated Properties

## 示例

这有个新的语法：`val 'property name': 'Type' by 'expression'`。

在 `by` 后面的表达式是委托，因为对应该属性 `get()` 和 `set()` 方法会被委托给它。*属性委托* 不需要实现任何接口，但它们必须提供名为 `getValue()` 和 `setValue()` 的方法来被调用。完整文档在[这里](http://kotlinlang.org/docs/reference/delegated-properties.html)。

先来看个示例代码：

```kt
import kotlin.reflect.KProperty

class Example {
    var p: String by Delegate()                                               // 1

    override fun toString() = "Example Class"
}

class Delegate() {
    operator fun getValue(thisRef: Any?, prop: KProperty<*>): String {        // 2     
        return "$thisRef, thank you for delegating '${prop.name}' to me!"
    }

    operator fun setValue(thisRef: Any?, prop: KProperty<*>, value: String) { // 2
        println("$value has been assigned to ${prop.name} in $thisRef")
    }
}

fun main() {
    val e = Example()
    println(e.p)
    e.p = "NEW"
}
```

```
Example Class, thank you for delegating 'p' to me!
NEW has been assigned to p in Example Class
```

1. 类型为 `String` 的委托属性 `p`。
2. 委托方法。对于不可变属性来说，只提供 `getValue` 即可。


**标准委托**

Kotlin 标准库中已经包含了一些有用的委托，比如 `lazy`，`observable` 等。

例如 `lazy`，可在延迟初始化的情况下使用。

```kt
class LazySample {
    init {
      println("created!");            // 1
    }

    val lazy: String by lazy {
        println("computed!")          // 2
        "my lazy"
    }
}

fun main() {
    val sample = LazySample()         // 1
    println("lazy = ${sample.lazy}")  // 2
    println("lazy = ${sample.lazy}")  // 3
}
```

```
created!
computed!
lazy = my lazy
lazy = my lazy
```

1. 属性 `lazy` 在创建对象时没有初始化。
2. 第一次调用 `get()` 会执行传入 lazy() 的 lambda 表达式参数，并记住结果。
3. 后续调用 `get()` 时只是返回记住的结果。

如果你想要线程安全，请使用 `blockingLazy()` 进行替代：它保证只在一个线程中计算值，并且所有线程都将看到相同的值。


**map 中的属性**

存储在 map 中的属性。这在诸如解析 JSON 或执行其它 "动态" 内容的应用程序中比较常见。

```kt
class User(val map: Map<String, Any?>) {
    val name: String by map                // 1
    val age: Int     by map                // 1
}

fun main() {
    val user = User(mapOf(
            "name" to "John Doe",
            "age"  to 25
    ))

    println("name = ${user.name}, age = ${user.age}")
}
```

```
name = John Doe, age = 25
```

1. 委托会从 `map` 中取值 (通过 "字符串键-属性名称")。


当然，你也可以使用可变属性，这将在赋值时修改 map (注意你需要的是 `MutableMap`，而不是 `Map`)。
