# Kotlin Examples - When, Loops, Ranges, Conditional Expression, Equality Checks

## 示例

### when

Kotlin 中并没有 `switch` 声明，替代它的有着更加智能、结构更加清晰的 `when`。

```kt
fun main() {
    cases("Hello")
    cases(1)
    cases(0L)
    cases(MyClass())
    cases("hello")
}

fun cases(obj: Any) {
    when (obj) {
        1 -> println("One")                          // 1
        "Hello" -> println("Greeting")               // 2
        is Long -> println("Long")                   // 3
        !is String -> println("Not a string")        // 4
        else -> println("Unknown")                   // 5
    }
}

class MyClass
```

```kt
Greeting
One
Long
Not a string
Unknown
```

1. 检查 `obj` 是否等同于 `1`。
2. 检查字符串。
3. 实例检查。
4. 否定的实例检查。
5. 其它情况语句 (在部分情况下可省略)。


### Loops

**for, while, do-while**

```kt
val cakes = listOf("carrot", "cheese", "chocolate")

for (cake in cakes) {                               // 1
    println("Yummy, it's a $cake cake!")
}
```

```kt
Yummy, it's a carrot cake!
Yummy, it's a cheese cake!
Yummy, it's a chocolate cake!
```

1. 循环遍历列表中的每个蛋糕。


```kt
fun eatACake() = println("Eat a Cake")
fun bakeACake() = println("Bake a Cake")

fun main(args: Array<String>) {
    var cakesEaten = 0
    var cakesBaked = 0

    while (cakesEaten < 5) {                    // 1
        eatACake()
        cakesEaten ++
    }

    do {                                        // 2
        bakeACake()
        cakesBaked++
    } while (cakesBaked < cakesEaten)
}
```

```kt
Eat a Cake
Eat a Cake
Eat a Cake
Eat a Cake
Eat a Cake
Bake a Cake
Bake a Cake
Bake a Cake
Bake a Cake
Bake a Cake
```

while 和 do-while 结构的工作方式与大多数语言的类似：

1. 当执行条件为真是，执行代码块。
2. 先执行代码块，然后在判断 while 的条件看是否需要循环。


**Iterators**

```kt
class Animal(val name: String)

class Zoo(val animals: List<Animal>) {
    operator fun iterator(): Iterator<Animal> {             // 1
        return animals.iterator()                           // 2
    }
}

fun main() {

    val zoo = Zoo(listOf(Animal("zebra"), Animal("lion")))

    for (animal in zoo) {                                   // 3
        println("Watch out, it's a ${animal.name}")
    }
}
```

```kt
Watch out, it's a zebra
Watch out, it's a lion
```

1. 提供一个迭代器 (iterator) 实现，通过 `operator` 关键字进行修饰。
2. 返回列表的迭代器，它符合以下方法要求：
   * `next(): Animal`
   * `hasNext(): Boolean`
3. 遍历 `zoo` 中的每个 `animal`。

迭代器可以向上述方式那样声明，也可以作为扩展函数进行声明。


### Ranges

```kt
for(i in 0..3) {             // 1
    print(i)
}
print(" ")

for(i in 2..8 step 2) {      // 2
    print(i)
}
print(" ")

for (c in 'a'..'d') {        // 3
    print(c)
}
print(" ")

for (c in 'a'..'g' step 2) { // 4
    print(c)
}
print(" ")

for (i in 3 downTo 0) {      // 5
    print(i)
}
print(" ")

val x = 2
if (x in 1..10) {            // 6
    print(x)
}
print(" ")

val y = 3
if (y !in 1..4) {            // 7
    print(y)
}
```

```kt
0123 2468 abcd aceg 3210 2
```

1. 创建一个从 0 到 3 (包含) 的范围 (range)，并进行迭代。
2. 你可以为连续的元素定义增量步长 (step)。
3. 也可以创建字符范围。
4. 增量步长对字符范围同样有效。
5. 使用 `downTo()` 函数创建反向顺序的范围。
6. 范围在 `if` 语句中也有很大用处。
7. `!in` 与 `in` 相仿。


### Conditional Expression

在 Kotlin 中是没有三元运算符 (condition ? then : else) 的，取而代之的是 `if` 表达式。

```kt
fun max(a: Int, b: Int) = if (a > b) a else b         // 1

println(max(99, -42))
```

```kt
99
```

1. `if` 是一个表达式，它返回一个值。


### Equality Checks

Kotlin 使用 `==` 进行结构比较，使用 `===` 进行引用比较。

更准确地说，`a == b` 会被编译成 `a == null ? b == null : a.equals(b)`。

```kt
val authors = setOf("Shakespeare", "Hemingway", "Twain")
val writers = setOf("Twain", "Shakespeare", "Hemingway")

println(authors == writers)   // 1
println(authors === writers)  // 2
```

```kt
true
false
```

1. 返回 `true` 是因为它调用了 `authors.equals(writers)` 并忽略了元素的顺序。
2. 返回 `false` 是因为 `authors` 和 `writers` 是不同的引用。


### 扩展

### If 表达式

在 Kotlin 中，`if` 是一个表达式，即会返回一个值。因此就不再需要三元运算符了，通过普通的 `if` 就能完成类似操作。比如：

```kt
// 传统用法
var max = a 
if (a < b) max = b

// With else 
var max: Int
if (a > b) {
    max = a
} else {
    max = b
}
 
// 作为表达式
val max = if (a > b) a else b
```

有趣的是，`if` 的分支可以是代码块，最后的表达式的值作为该代码块的值：

```kt
val max = if (a > b) {
    print("Choose a")
    a
} else {
    print("Choose b")
    b
}
```


### When 表达式

`when` 将它的参数与所有的分支条件按顺序进行比较，直到某个分支满足条件。

`when` 既可以被当做表达式使用，也可以被作为语句使用：
* 如果被当做表达式，符合条件的分支的值就是整个表达式的值，
* 如果被当做语句，则忽略个别分支的值。

要注意的是，当 `when` 作为一个表达式使用时，必须要有 `else` 语句，除非编译器能够检测出所有的可能情况并且都已被覆盖 (比如枚举类或密封类与 `when` 结合使用时)。

> 类似 `if`，每一个分支可以是一个代码块，它的值是块中最后的表达式的值。

如果很多分支需要用相同的方式进行处理，则可以把多个分支放在一起，并用逗号分隔：

```kt
when (x) {
    0, 1 -> print("x == 0 or x == 1")
    else -> print("otherwise")
}
```

还有一些其它的使用姿势：

```kt
// 通过 `in` 或 `!in` 检测一个值在或不在一个区间中
when (x) {
    in 1..10 -> print("x is in the range")
    in validNumbers -> print("x is valid")
    !in 10..20 -> print("x is outside the range")
    else -> print("none of the above")
}

// 通过 `is` 或 `!is` 检测一个值是不是一个特定类型的实例
// 注：由于智能转换，在访问该类型的方法和属性时不需要再进行额外检测或转换
fun hasPrefix(x: Any) = when(x) {
    is String -> x.startsWith("prefix")
    else -> false
}

// 用来取代 `if - else if` 链
when {
    x.isOdd() -> print("x is odd")
    x.isEven() -> print("x is even")
    else -> print("x is funny")
}
```

### For 循环

`for` 循环可以对任何提供了迭代器 (Iterator) 的对象进行遍历，语法如下：

```kt
for (item in collection) print(item)
```

循环体也可以是一个代码块：

```kt
for (item: Int in ints) {
    // ...
}
```

如上所述，`for` 可以循环遍历任何提供了迭代器的对象，即有一个成员函数或扩展函数返回了 `Iterator` 类或其子类的对象。

我们来看下迭代器 `Iterator` 在源码中的定义：

```kt
package kotlin.collections

/**
 * An iterator over a collection or another entity that can be represented as a sequence of elements.
 * Allows to sequentially access the elements.
 */
public interface Iterator<out T> {
    /**
     * Returns the next element in the iteration.
     */
    public operator fun next(): T

    /**
     * Returns `true` if the iteration has more elements.
     */
    public operator fun hasNext(): Boolean
}


public interface MutableIterator<out T> : Iterator<T> {
    public fun remove(): Unit
}

public interface ListIterator<out T> : Iterator<T> {
    override fun next(): T
    override fun hasNext(): Boolean
    public fun hasPrevious(): Boolean

    public fun previous(): T
    public fun nextIndex(): Int
    public fun previousIndex(): Int
}


public interface MutableListIterator<T> : ListIterator<T>, MutableIterator<T> {
    override fun next(): T
    override fun hasNext(): Boolean

    override fun remove(): Unit
    public fun set(element: T): Unit
    public fun add(element: T): Unit
}
```

由继承关系可以，不管是普通的迭代器还是列表迭代器，迭代器对象至少有两个 `operator` 标识函数：
* 获取下一个迭代元素的 `next()`。
* 判断是否接下来是否还有迭代元素的 `hasNext()`。

另外，如果你想通过索引遍历一个数组或者一个 List，可以这么做：

```kt

val array = arrayOf("a", "b", "c")
for (i in array.indices) {
    println(array[i])
}

// a
// b
// c
```

或者使用库函数 `withIndex`：

```kt
val array = arrayOf("a", "b", "c")
for ((index, value) in array.withIndex()) {
    println("the element at $index is $value")
}

// the element at 0 is a
// the element at 1 is b
// the element at 2 is c
```

库函数 `withIndex` 在源码中的定义时这样的：

```kt
package kotlin.collections
public fun <T> Array<out T>.withIndex(): Iterable<IndexedValue<T>> {
    return IndexingIterable { iterator() }
}

package kotlin.collections
public data class IndexedValue<out T>(public val index: Int, public val value: T)
```

> 注意：对区间或者数组的 `for` 循环会被编译为并不创建迭代器的基于索引的循环。


### 区间表达式

区间表达式由具有操作符形式的 `..` 的 `rangTo` 函数辅以 `in` 和 `!in` 形成。

区间是为任何可比较类型定义的，但对于整型原生类型有一个优化的实现。比如：

```kt
// 等同于 1 <= i && i <= 10
if (i in 1..10) { 
    println(i)
}
```

整型区间 (`IntRange`, `LongRange`, `CharRange`) 有一个额外的特性：它们可以迭代。编译器会将其转换为类似 Java 的基于索引的 `for` 循环，从而避免了额外的开销：

```kt
for (i in 1..4) print(i)
```

区间表达式支持一些实用的函数：

* `rangeTo()`: 整型类型的 `rangeTo()` 操作符只是调用 `*Range` 类的构造函数。
* `downTo()`: 扩展函数 `downTo()` 是为任何整型类型对定义。
* `reversed()`: 扩展函数 `reversed()` 是为每个 `*Progression` 类定义的，并且所有这些函数返回反转后的数列。
* `step()`: 扩展函数 `step()` 是为每个 `*Progression` 类定义的， 所有这些函数都返回带有修改了 `step` 值 (函数参数）的数列。 步长 `step` 值必须始终为正，因此该函数不会更改迭代的方向。

```kt
for (i in 1..8) print(i)                        // 12345678
for (i in 1.rangeTo(8)) print(i)                // 12345678

for (i in 8 downTo 1) print(i)                  // 87654321
for (i in 8.downTo(1)) print(i)                 // 87654321

for (i in (8 downTo 1).reversed()) print(i)     // 12345678
for (i in (8 downTo 1) step 2) print(i)         // 8642
```


### 循环中的 Break 与 continue

Kotlin 中有三种结构化跳转表达式：
* `return`: 默认直接从最直接包围它的函数或者匿名函数返回。
* `break`: 终止最直接包围它的循环。
* `continue`: 继续下一次最直接包围它的循环。

所有这些表达式都可以用作更大表达式的一部分：

```kt
val s = person.name ?: return
```

这些表达式的类型是 `Nothing`: 

```kt
/**
 * Nothing has no instances.
 *
 * You can use Nothing to represent "a value that never exists": for example,
 * if a function has the return type of Nothing, it means that it never returns (always throws an exception).
 */
public class Nothing private constructor()
```


**Break 与 Continue 标签**

在 Kotlin 中，任何表达式都可以用标签 `label` 进行标记：
* 标签格式为 `标识符@`，比如 `abc@`、`fooBar@` 等。
* 要为一个表达式设置标签，只需在其前面添加标签即可。

```kt
loop@ for (i in 1..8) {
    for (j in 1..4) {
        if (i == 2 && j == 2) break@loop
        println("i = $i, j = $j")
    }
}

/*
i = 1, j = 1
i = 1, j = 2
i = 1, j = 3
i = 1, j = 4
i = 2, j = 1
*/
```


```kt
loop@ for (i in 1..8) {
    innerLoop@ for (j in 1..4) {
        if (j == 2) break@innerLoop
        println("i = $i, j = $j")
    }
}

// 等同于：
loop@ for (i in 1..8) {
    for (j in 1..4) {
        if (j == 2) continue@loop
        println("i = $i, j = $j")
    }
}

/*
i = 1, j = 1
i = 2, j = 1
i = 3, j = 1
i = 4, j = 1
i = 5, j = 1
i = 6, j = 1
i = 7, j = 1
i = 8, j = 1
*/
```


**标签处返回**

Kotlin 有函数字面量、局部函数和对象表达式，因此 Kotlin 的函数可以被嵌套。标签限制的 `return` 允许我们从外层函数返回。最重要的一个用途就是从 `lambda` 表达式中返回。

假设我们写了这样一段代码：

```kt
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return
        println(it)
    }
    println("This point is unreachable.")
}
```

运行结果输出是这样的：

```kt
1
2
```

因为 `if (it == 3) return` 语句直接返回到了 `foo()` 函数的调用者，所以不会执行下面的 `println("This point is unreachable.")` 语句。那有木有什么姿势使得只返回到 `forEach` 调用之后呢？往下看。

如果我们需要从 lambda 表达式中返回，就必须给他加标签以限制 `return`，姿势是这样的：

```kt
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach ha@{
        if (it == 3) return@ha
        println(it)
    }
    println("This point is unreachable.")
}
```
```kt
1
2
4
5
This point is unreachable.
```

上面的示例中，我们显式声明了一个 `ha@` 标签，但这种情况下使用隐式标签更方便，比如：

```kt
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach
        println(it)
    }
    println("This point is unreachable.")
}
// 输出日志相同，略
```

与此同时，通过使用匿名函数替代 lambda 表达式也能实现类似效果。匿名函数内部的 `return` 语句将从匿名函数自身返回：

```kt
fun foo() {
    listOf(1, 2, 3, 4, 5).forEach(fun(value: Int) {
        if (value == 3) return
        println(value)
    })
    print("Done with anonymous function.")
}
```
```kt
1
2
4
5
Done with anonymous function.
```


## 总结

在这篇文章中，我们讲解了 Kotlin 中一些常用的逻辑控制语法，比如 `when`、 `for`、`while`、`if` 等。