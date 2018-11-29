# Kotlin Examples - Classes

## 示例

[类的声明](https://kotlinlang.org/docs/reference/classes.html#classes) 由以下三个部分组成：
* **类名**
* **类头**：可选，指定参数及其类型，主构造函数等。
* **类体**：可选，用花括号括起来。

如果没有类体，花括号可以省略。

```kt
class Customer                                  // 1

class Contact(val id: Int, var email: String)   // 2

fun main() {

    val customer = Customer()                   // 3

    val contact = Contact(1, "mary@gmail.com")  // 4

    println(contact.id)                         // 5
    contact.email = "jane@gmail.com"            // 6
}
```

```
1
```

1. 声明一个名为 `Customer` 的、无参数构造函数的类。
2. 声明一个带有不可变属性 `id`，可变属性 `email` 的类，其主构造函数有 `id` 和 `email` 两个参数。
3. 创建 `Customer` 类的一个实例。注意，没有 `new` 关键字。
4. 通过使用带两个参数的构造函数创建一个 `Contact` 类的实例。
5. 访问 `id` 属性。
6. 为 `email` 属性赋一个新的值。
