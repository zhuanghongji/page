# Kotlin Examples - Generics

## 示例

泛型类和泛型函数通过封装公共逻辑 (独立于特定类型的逻辑) 来增加代码的可重用性，比如 `List<T>` 内部的逻辑是独立于 `T` 的具体类型的。


### 泛型类

在 Kotlin 中使用泛型的一种方式是创建泛型类。

```kt
class MutableStack<E>(vararg items: E) {              // 1

  private val elements = items.toMutableList()

  fun push(element: E) = elements.add(element)        // 2

  fun peek(): E = elements.last()                     // 3

  fun pop(): E = elements.removeAt(elements.size - 1)

  fun isEmpty() = elements.isEmpty()

  fun size() = elements.size

  override fun toString() = "MutableStack(${elements.joinToString()})"
}
```

```
MutableStack(0.62, 3.14, 2.7, 9.87)
peek(): 9.87
MutableStack(0.62, 3.14, 2.7, 9.87)
pop(): 9.87
MutableStack(0.62, 3.14, 2.7)
pop(): 2.7
MutableStack(0.62, 3.14)
pop(): 3.14
MutableStack(0.62)
pop(): 0.62
MutableStack()
```

1. 定义一个泛型类 `MutableStack<E>`，其中 `E` 称为 **泛型类型参数**。在使用时，它会被指定为一个具体的类型，比如指定为 `Int` 类型来声明一个 `MutableStack<Int>`。
2. 在泛型类中，`E` 可以像其它任何类型一样作为参数的类型。
3. 也可以将 `E` 作为返回值的类型。

注意，上面的实现大量使用了 Kotlin 的简写语法，使得函数可以在单个表达式中定义。


### 泛型函数

如果函数的逻辑独立于具体的类型，可以使用 [泛型函数](https://kotlinlang.org/docs/reference/generics.html#generic-functions)。比如，接下来我们可以创建一个泛型工具函数，使得创建上面示例中的泛型类 (可变堆栈) 变得更加容易:

```kt
fun <E> mutableStackOf(vararg elements: E) = MutableStack(*elements)

fun main() {
  val stack = mutableStackOf(0.62, 3.14, 2.7)
  println(stack)
}
```

```
MutableStack(0.62, 3.14, 2.7)
```

注意，编译器可以从 `mutableStackOf` 的参数中推导出泛型类型，所以你并不需要写成 `mutableStackOf<Int>(...)`。


## 扩展

### 型变

Java 类型系统中最棘手的部分之一是类型通配符。Kotlin 中并没有这个概念，但提供了另外两个的东西：
* 声明处型变 (declaration-site variance)
* 类型投影 (type projections)

> 官方文档在泛型的介绍中，对 Java 和 Kotlin 做了详细的说明和对比，我看了几次还是觉得有点懵，所以这篇文章了直接跳过了对比部分。


**声明处型变**

Kotlin 提供了 `out` 修饰符，当一个类 (假设是 `C`) 的类型参数 (假设是 `T`) 被声明为 `out` 时，它就只能出现在 `C` 的成员的输出位置，以此获得的回报是 `C<Base>` 可以安全的作为 `C<Derived>` 的超类。比如：

```kt
interface Source<out T> {
    fun nextT(): T
}

fun demo(strs: Source<String>) {
    // 这个没问题，因为 T 是一个 out 类型参数
    val objects: Source<Any> = strs
}
```

> * 协变 (covariant)：简而言之，带 `extends` 限定的通配符类型。
> * 逆变 (contravariance): 与协变正好是反过来。

也就是说，类 `C` 在参数 `T` 上是协变的，或者说 `T` 是一个协变的类型参数。另外，可以认为 `C` 是 `T` 的生产者，而不是 `T` 的消费者。

`out` 修饰符称为 **型变注解**。由于它是在类型声明处提供的，所以可称为 **声明处型变**。除了 `out`，Kotlin 还提供了另一个型变注解 `in`。

型变注解 `in` 使得修饰的类型参数是逆变的，即可以被消费而不可以被生产。比如说：

```kt
interface Comparable<in T> {
    operator fun compareTo(other: T): Int
}

fun demo(x: Comparable<Number>) {
    // 1.0 是 Double 类型，Double 类型是 Number 的子类型
    x.compareTo(1.0)    
    
    // 所以，我们可以将 x 赋给类型为 Comparable<Double> 的变量
    val y: Comparable<Double> = x
}
```

秘诀三遍：
* 转换：消费者 `in`，生产者 `out`
* 转换：消费者 `in`，生产者 `out`
* 转换：消费者 `in`，生产者 `out`


**类型投影**

前面讲的是 **声明处** 型变，下面我们来讲 **使用处** 型变：**类型投影**。

将类型参数 `T` 声明为 `out` 非常方便，并且能够避免使用处「子类型化」的麻烦。但有些情况下，我们不能限制只返回 `T`，比如经常用到的 `Array`: 

```kt
class Array<T>(val size: Int) {
    fun get(index: Int): T { ... }
    fun set(index: Int, value: T) { ... }
}
```

该类在 `T` 上既不能是协变的也不能是逆变的，所有在使用上缺乏一些灵活性。

下面我们来看一个函数 (将一个数组的数据复制到另一个数组中)：

```kt
fun copy(from: Array<Any>, to: Array<Any>) {
    assert(from.size == to.size)
    for (i in from.indices) {
        to[i] = from[i]
    }
}
```

实际应用这个函数是：

```kt
fun main() {
    val ints: Array<Int> = arrayOf(1, 2, 3)
    val any = Array<Any>(3) { "" }
    copy(ints, any)
}
```

擦，有埋伏... 

编译报错了：`Error: Kotlin: Type mismatch: inferred type is Array<Int> but Array<Any> was expected`。

注意，我们前面提到过 `Array<T>` 的 `T` 是不型变的，因此 `Array<Int>` 和 `Array<Any>` 都不是另一个类型的子类型。为什么呢？因为 `copy` 函数可能会做坏事呀，比如它可能会尝试写一个 `String` 到 `from` 中，这种情况下如果我们传递给 `from` 的是一个 `Int` 数组，那就会触发 `ClassCastException` 异常了。

既然这样，那我们怎么确保 `copy` 不会做坏事？可以酱紫：

```kt
fun copy(from: Array<out Any>, to: Array<Any>) { ... }
```

上面这段代码发生的事情我们称为类型投影：即 `from` 不仅仅是一个数组，还是一个受限制的 (投影的) 数组。我们只可以调用返回类型为 `T` 的方法。比如上面的 `Array<T>`，只可以调用 `get()` 方法。

你也可以使用 `in` 修饰符投影一个类型：

```kt
fun fill(dest: Array<in String>, value: String) { ... }
```

上面的 `Array<in String>` 对应于 Java 中的 `Array<? super String>`，也就是说，你可以传递一个 `CharSequence` 数组或一个 `Object` 数组给 `fill()` 函数。


**星投影**

有时候，你对类型参数一无所知，但仍希望能够以安全的方式调用方法。这里的安全方式是指，泛型类型的每个具体实例都是定义的泛型类型的投影的子类型。

> 觉得有点绕？Maybe kotlin isn't a good boy, haha ..

Kotlin 为此提供了 **星投影** 语法：

* 对于 `Foo<out T: TUpper>`，`T` 是一个具有上界 `TUpper` 的协变类型参数。
  * 此时 `Foo<*>` 等价于 `Foo<out TUpper>`。
  * 这意味着当 `T` 未知时，你也可以安全地从 `Foo<*>` 中读取 `TUpper` 的值。
  
* 对于 `Foo<in T>`，`T` 是一个逆变类型参数。
  * 此时 `Foo<*>` 等价于 `Foo<in Nothing>`。
  * 这意味着当 `T` 未知时，没有什么可以安全地写入 `Foo<*>` 中。

* 对于 `Foo<T: TUpper>`，`T` 是一个具有上界 `TUpper` 的 **不型变** 类型参数。
  * `Foo<*>` 对于读取值时等价于 `Foo<out TUpper>`。
  * 而对于写值时等价于 `Foo<in Nothing>`。

如果泛型类型具有多个类型参数，则每个类型参数都可以单独进行投影。例如，如果类型被声明为 `interface Function <in T, out U>`，我们就可以想象出下面的星投影：

* `Function<*, String>` 表示 `Function<in Nothing, String>`
* `Function<Int, *>` 表示 `Function<Int, out Any?>`
* `Function<*, *>` 表示 `Function<in Nothing, out Any?>`

> 注意：星投影非常像 Java 中的原始对象，但是安全。


### 泛型约束

通过泛型约束限制，我们能够替换给定类型参数的所有可能类型的集合。

**上界**

上界约束类型跟 Java 中的 `extends` 关键字相对应：

```kt
fun <T: Comparable<T>> sort(list: List<T>) { ... }
```

冒号 `:` 后面指定的是类型上界，表示只有 `Comparable<T>` 的子类型可以替代 `T`。

<img src="./res/001.jpg" width="120">

```kt
package kotlin
public class Int private constructor() : Number(), Comparable<Int> { ... }

package kotlin.collections
@SinceKotlin("1.1") public actual typealias HashMap<K, V> = java.util.HashMap<K, V>

package java.util;
public class HashMap<K,V> extends AbstractMap<K,V>
    implements Map<K,V>, Cloneable, Serializable { ... }



// 正常：Int 是 Comparable<Int> 的子类型
sort(listOf(1, 2, 3)) 

// 错误：HashMap<Int, String> 不是 Comparable<HashMap<Int, String>> 的子类型
sort(listOf(HashMap<Int, String>())) 
```
 
如果没有显式声明上界，则上界默认为 `Any?`。在尖括号中我们只能指定一个上界，如果想要在同一个类型参数上指定多个上界，就需要写一个单独 `where` 子句：

```kt
fun <T> copyWhenGenerate(list: List<T>, threshold: T): List<String> 
        where T: CharSequence, 
              T: Comparable<T>
{
    return list.filter { it > threshold }.map { it.toString() }
}
```

这样，我们在调用 `copyWhenGenerate` 方法时指定的具体类型就必须满足 `where` 子句的所有条件。也就是说，类型 `T` 必须同时实现了 `CharSequence` 和 `Comparable` 接口。


### 类型擦除

Kotlin 为泛型声明进行安全检测的行为仅在编译期间执行，运行时的泛型类型实例不保留 (再编译时被擦除了) 关于其类型实参的任何信息。这就是我们通常所说的类型擦除。

因此，实际上是没有通用的方法可以在运行时检测一个泛型类型的实例是否是通过某个类型参数进行创建的，并且编译器也禁止了 `is` 这种检查。


## 总结

这篇文章的内容都是根据官方文档中的泛型部分进行展开，比如泛型类、泛型函数、型变、类型投影、泛型约束等。总体上感觉这篇文章写得很好，后面对 Kotlin 的泛型部分熟练之后需要再回来进行内容补充。