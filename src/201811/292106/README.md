# Kotlin Examples - Generics

## 示例

[泛型](https://kotlinlang.org/docs/reference/generics.html) 是一种通用机制并在现代语言中成为了标准。泛型类和函数通过封装公共逻辑 (独立于特定泛型类型) 来增加代码的可重用性，比如 `List<T>` 内部的逻辑独立于 `T` 是什么。


## 泛型类

在 Kotlin 中使用泛型的第一种方式是创建泛型类。

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

1. 定义一个泛型类 `MutableStack<E>`，其中 `E` 被称为 *泛型类型参数*。在使用时，它会被赋一个具体的类型，比如 `Int` 来声明一个 `MutableStack<Int>`。
2. 在泛型类中，`E` 可以像其它任何类型一样用作参数的类型。
3. 你也可以将 `E` 作为返回类型。

注意，上面的实现大量使用了 Kotlin 的简写语法，使得函数可以在单个表达式中定义。



## 泛型函数

如果函数的逻辑是独立于具体的类型，你也可以使用 [泛型函数](https://kotlinlang.org/docs/reference/generics.html#generic-functions)。例如，你可以创建一个工具函数，使得创建可变堆栈更加容易。

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

注意编译器是如何从 `mutableStackOf` 的参数中推导出泛型类型的，所以你并不需要写成 `mutableStackOf<Int>(...)`。
