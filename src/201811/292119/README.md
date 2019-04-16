# Kotlin Examples - Extension Functions and Properties

<img src="./res/001.png" width="120">

## 示例

在 Kotlin 中，我们无需继承某个类或使用类似装饰器的设计模式就能够扩展该类的功能。比如：
* 通过 **扩展属性** 来为这个类增加一些其它属性。
* 或通过 **扩展函数** 来为这个类正价一些其它方法。

<img src="./res/002.png" width="120">

```kt
data class Item(val name: String, val price: Float)                             // 1
data class Order(val items: Collection<Item>)

fun Order.maxPricedItemValue() = this.items.maxBy { it.price } ?.price ?: 0F    // 2
fun Order.maxPricedItemName() = this.items.maxBy { it.price } ?.name ?: "NONE"

val Order.commaDelimitedItemNames: String                                       // 3
    get() = items.map { it.name } .joinToString()


fun main() {
    val order = Order(listOf(
            Item("Bread", 25.0F),
            Item("Wine", 29.0F),
            Item("Water", 12.0F)
    ))

    println("Max priced item name: ${order.maxPricedItemName()}")
    println("Max priced item value: ${order.maxPricedItemValue()}")
    println("Items: ${order.commaDelimitedItemNames}")
}
```
```kt
Max priced item name: Wine
Max priced item value: 29.0
Items: Bread, Wine, Water
```

1. 定义了两个简单的数据类 `Item` 和 `Order`，其中 `Order` 可以包含任意数量的 `Item`。
2. 为 `Order` 类定义扩展函数，稍后我们可以直接在该类的实例上进行调用。
3. 也可以为 `Order` 类定义扩展属性。


我们甚至可以在 `null` 引用上进行扩展。在实现中，我们可以通过检查 `null` 引用来执行任意逻辑。比如，下面我们写一个 `null` 安全的转换大写扩展函数：

```kt
fun <T> T?.safeToUpperCase() = this?.toString()?.toUpperCase() ?: "NULL"   // 1

fun main() {
    println(null.safeToUpperCase())
    println("zhuanghongji".safeToUpperCase())
}
```
```kt
NULL
ZHUANGHONGJI
```

1. 通过泛型定义一个 `null` 安全的转换大写扩展函数。



## 扩展 

> 上面这个「扩展」标题是只针对前面示例进行的知识扩展，跟 Kotlin 的 `扩展` 是两回事。


### 扩展函数

声明一个扩展函数，需要用一个 **接受者类型 (即被扩展的类型)** 来作为它的前缀。比如下面的代码为 `MutableList<Int>` 类型添加一个 `swap` 函数：

```kt
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val temp = this[index1]     // this 指向的是当前这个 MutableList
    this[index1] = this[index2]
    this[index2] = temp
}

fun main() {
    val numbers = listOf(18, 24).toMutableList()
    numbers.swap(0, 1)
    println(numbers)
}
```
```kt
[24, 18]
```

当然，我们也可以通过泛型来泛化这个扩展函数，比如：

```kt
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val temp = this[index1]     /
    this[index1] = this[index2]
    this[index2] = temp
}

fun main() {
    val names = mutableListOf("张三", "李四", "王五")
    names.swap(names.lastIndex - 1, names.lastIndex)
    println(names)
}
```
```kt
[张三, 王五, 李四]
```


### 扩展是静态解析的

扩展并没有真正的修改对应的类。也就是说，你对某个类进行了扩展，但并没有在这个类中插入任何成员。在使用时，我们仅仅是通过该类型的实例 (用点表达式) 去调用这个扩展函数或使用这个扩展属性而已。

更进一步说，扩展函数是静态分发的，调用的扩展函数是由函数调用所在的表达式的类型决定的，而不是由表达式在运行时的求值结果决定的。举个栗子：

```kt
open class C
open class D: C()

fun C.foo() = "c"
fun D.foo() = "d"

fun printFoo(c: C) {
    println(c.foo())
}

printFoo(D())           // c
```

在调用 `printFoo(c: C)` 函数时，我们的入参是 `D` 类型的实例对象，输出的却是 `c`。这是因为调用的扩展函数是只取决于 `c` 的声明类型 (即 `C` 类型) 的。

如果一个类定义有一个成员函数和一个扩展函数，刚好这两个函数有着相同的接受者类型和相同的名字，在调用时也都适用给定的参数。**在这种情况下，总是取成员函数**。例如：

```kt
class E {
    fun foo() { println("member") }
}

fun E.foo() { println("extension") }

E().foo()    // member
```

那如果扩展函数与成员函数名字相同但接收参数的签名不同会怎样？**在这种情况下，取对应给定参数签名的函数**。例如：

```kt
class E {
    fun foo() { println("member") }
}

fun E.foo(i: Int) { println("extension") }

E().foo()     // member
E().foo(1)    // extension
```


### 可空接受者

在 Kotlin 中，我们可以为「可空的」接受者类型定义扩展。在扩展中如果进行了 `this == null` 类似的检查，即使调用对象的值为 `null` 也不会引起任何异常。比如：

```kt
fun Any?.toString(): String {
    if (this == null) return "null"
    // 空检查后，this 会自动转换成非空类型
    // 所以下面的 toString() 会被解析为 Any 的陈冠函数
    return toString()
}

println(null.toString())    // null
```


### 扩展属性

与扩展函数类似，Kotlin 支持扩展属性：

```kt
val <T> List<T>.secondIndex: Int
    get() = if (size == 0) 0 else 1

fun main() {
    val alphabets = listOf("A", "B", "C")
    println(alphabets[alphabets.secondIndex])   // B
}
```

但要注意的是，由于扩展属性并没有实际地将成员插入对应的类中，所以对于扩展属性来说幕后字段其实是没有意义的。这也正是 **扩展属性不能有初始化器** 的原因，扩展属性的行为只能由显式提供 getter 和 setter 进行定义。

```kt
// Error: Kotlin: Extension property cannot be initialized because it has no backing field
val <T> List<T>.secondIndex: Int = 1
```


### 伴生对象的扩展

如果一个类定义有伴生对象，你也可以为这个伴生对象定义扩展函数和扩展属性：

```kt
class MyClass {
    companion object {}
}

fun MyClass.Companion.foo() { println("foo") }
val MyClass.Companion.name
    get() = "zhanghongji"


fun main() {
    // 类似伴生对象的其它成员
    // 通过类型作为限定符就可以访问伴生对象的扩展函数或扩展属性
    MyClass.foo()             
    println(MyClass.name)      
}
```
```kt
foo
zhanghongji
```


### 扩展的作用域

大多时候，我们会在顶层定义扩展，也就是在包里：

```kt
package foo.bar

fun Baz.goo() { ... }
```

要使用所定义包之外的某个扩展，就需要在调用方导入扩展所在的包：

```kt
package com.example.usage

import foo.bar.goo // 导入所有名为“goo”的扩展
                   // 或者
import foo.bar.*   // 从“foo.bar”导入一切

fun usage(baz: Baz) {
    baz.goo()
}
```


### 可见性说明

* 在文件顶层声明的扩展可以访问同一文件中的其它 `private` 顶层声明。
* 如果扩展是在其接受者类型外部声明的，那么该扩展不能访问接受者的 `private` 成员。

```kt
class User {
    var username = ""
    private var password = ""
}

fun User.printUsername() {
    // 能访问 username 属性
    println(username)
}

fun User.printPassword() {
    // 不能访问 username 属性
    // Error: Kotlin: Cannot access 'password': it is private in 'User'
    println(password)
}
```

### 动机

在 Java 中，我们将类命名为 `*Utils`: `FileUtils`、`StringUtils` 等，然后使用姿势是这样的：

```java
// Java
Collections.swap(list, Collections.binarySearch(list,
    Collections.max(otherList)),
    Collections.max(list));
```

如果觉得 `Collections` 碍手碍脚，可以通过 Java 的静态导入来简化下代码：

```java
// Java
swap(list, binarySearch(list, max(otherList)), max(list));
```

这样是好一点了，但我们并没有得到 IDE 强大的自动补全功能的助攻。要是辅助帮我看下视野以提醒我有木有刺客靠近那该多好：

```kt
// Kotlin
list.swap(list.binarySearch(otherList.max()), list.max());
```

> Kotlin 的类库不可能帮我们实现所有可能用到的方法，但是我们可以通过 `扩展` 来补充我们想要的。


## 总结

通过这篇的讲解我们已经掌握了 `扩展 (Extension)` 在 Kotlin 中的应用。在实际开发过程中，根据项目情况来扩展类库，可以大大减少样板代码的复制黏贴。