# Kotlin Examples - Inheritance

## 示例

```kt
open class Dog {                // 1
    open fun sayHello() {       // 2
        println("wow wow!")
    }
}

class Yorkshire : Dog() {       // 3
    override fun sayHello() {   // 4
        println("wif wif!")
    }
}

fun main() {
    val dog: Dog = Yorkshire()
    dog.sayHello()
}
```

```
wif wif!
```

1. Kotlin 中的类默认是 *final* 的。如果你想让类可以被覆写，则必须标记上 `open` 修饰符。
2. Kotlin 中的方法默认同样是 *final* 的。正如类一样，`open` 修饰符标识允许覆写。
3. 统一通过使用 **子类名称 + `: SuperclassName()`** 来覆写一个类。空的圆括号 `()` 表示对超类默认构造函数的调用。
4. 覆写方法或属性时需要 `overview` 修饰符。


## 不使用默认构造函数进行继承

```kt
open class Tiger(val origin: String) {
    fun sayHello() {
        println("A tiger from $origin says: grrhhh!")
    }
}

class SiberianTiger : Tiger("Siberia")                  // 1

fun main() {
    val tiger: Tiger = SiberianTiger()
    tiger.sayHello()
}
```

```
A tiger from Siberia says: grrhhh!
```

1. 当超类没有提供默认构造函数时，从子类调用父类构造函数必须提供相关参数。



## 绕过构造函数参数

```kt
open class Lion(val name: String, val origin: String) {
    fun sayHello() {
        println("$name, the lion from $origin says: graoh!")
    }
}

class Asiatic(name: String) : Lion(name = name, origin = "India") // 1

fun main() {
    val lion: Lion = Asiatic("Rufo")
    lion.sayHello()
}
```

```
Rufo, the lion from India says: graoh!
```

1. 可以看到，`name` 既不是 `var` 也不是 `val`，这是因为它是 `Asiatic` 构造函数的参数且它的值会传递到 `Lion` 的 `name` 属性 (it does not override it)。
