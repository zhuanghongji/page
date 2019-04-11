# Kotlin Examples - Variables

## 示例

Kotlin 有着强大的类型推导。你可以显式声明变量的类型，也可以留给编译器进行推导。

Kotlin 建议但不强制不变性，所以整体来看，你使用 `val` 的次数应该比 `var` 多。

```kt
var a: String = "initial"  // 1
println(a)
val b: Int = 1             // 2
val c = 3                  // 3
```

```
initial
```

1. 声明一个可变变量并进行初始化。
2. 声明一个不可变变量并进行初始化。
3. 声明一个不可变变量并进行初始化，且类型由编译器进行推导。


```kt
var e: Int  // 1
println(e)  // 2
```

```
Variable 'e' must be initialized
```

1. 声明一个变量，但不进行初始化。
2. 产生编译错误：Variable 'e' must be initialized



变量的初始化可以推迟，但必须是在第一次读取之前。

```kt
val d: Int  // 1

if (someCondition()) {
    d = 1   // 2
} else {
    d = 2   // 2
}

println(d) // 3
```

```
1
```

1. 声明一个变量，但不进行初始化。
2. 根据某些条件，使用不同的值来初始化变量。
3. 第一次使用该变量。


## 扩展

先来看一段代码：

```kt
package com.zhuanghongji.kotlin.examples

const val TAG = "tag"

class User {

    companion object {
        const val DESC = "This is user desc."
    }

    var name = ""
    var age = 18
    val adult
        get() = age >= 18

    var sex = 1
    var sexDesc: String
        get() = if (sex == 1) "男" else "女"
        set(value) {
            sex = if (value == "男") 1 else 0
        }
}

fun main() {
    println("TAG = $TAG, DESC = ${User.DESC}")

    val user = User()
    user.name = "zhuanghongji"
    user.age = 17
    println("姓名：${user.name}，年龄：${user.age}，性别描述：${user.sexDesc}，是否成年：${user.adult}")

    user.sexDesc = "女"
    println("修改后的性别：${user.sex}，描述：${user.sexDesc}")
}
```

```kt
TAG = tag, DESC = This is user desc.
姓名：zhuanghongji，年龄：17，性别描述：男，是否成年：false
修改后的性别：0，描述：女
```

看不懂？那接着往下看，会有相关的知识点。


### 定义变量

在 Kotlin 中，有两个关键字可以用来定义变量：
* `var`: 定义可重新赋值的变量。
* `val`: 定义只读变量，只能为其赋值一次。

比如说：

```kt
var r = 2
val pi = 3.14
val area
    get() = r * pi
```

> 注意：`val` 的「只读」是说不能再次对其赋值，并不是说读出来的值是不变的。比如上面声明的 `area` 变量，实际上如果 `r` 的值发生了变化，那 `area` 读出来的值也会发生变化。

另外，按所在位置区分可将 `var` 和 `val` 分为三种：
* 顶层属性：作为文件的一个属性。
* 类的属性：作为类的一个属性。
* 局部变量：作为函数内部的一个变量。


**现在知道了如何定义变量，那如何定义常量呢？**

Kotlin 提供了关键字 `const` 来定义常量，比如：

```kt
// 顶层常量
const val NAME = "zhuanghongji"

class Constants {
    companion object {
        // 类的常量
        const val BASE_URL = "http://zhuanghongji.com"
    }

    // 在类中是不能直接这样声明常量的
    // 否则编译时会报错：Const 'val' are only allowed on top level or in objects
    // const val BASE_URL = "http://zhuanghongji.com"
}
```

小结一下，`var`、`val` 和 `const` 有什么区别？

* 首先，`var` 和 `val` 的区别主要在于前者可读可写，而后者是只读的。
* 其次，`const` 表示的常量的值是在编译时就可以确定 (即编译时常量)，而 `var` 和 `val` 表示的变量的值在编译期间是不能确定的。


### 声明属性

前面内容讲到，Kotlin 中的类可以有属性，其中通过 `var` 声明的属性是可变的，通过 `val` 声明的属性是只读的，比如：

```kt
class Address {
    var name: String = ……
    val somethingElse: String = ……
}
```

使用类的属性时，直接用属性名称进行引用即可：

```kt
var address = Address()
println(address.name)
```

声明一个属性的完整语法如下：

```kt
var <propertyName>[: <PropertyType>] [= <property_initializer>]
    [<getter>]
    [<setter>]

var <属性名称>[: <属性类型>] [= <属性初始器>]
    [<获取访问器>]
    [<设置访问器>]
```

一般情况下，`property_initializer`、`getter` 和 `setter` 都是可选声明的。另外，如果 `PropertyType` 可以在初始器中推断出来，也是可选声明，比如说：

```kt
var allByDefault: Int? // 错误：需要显式初始器 (隐含默认的 getter 和 setter)
var initialized = 1    // 正确：类型推断为 Int  (隐含默认的 getter 和 setter)
```

注意，只读属性不允许有 `setter`，如下：

```kt
val simple: Int?       // 类型 Int、默认 getter、必须在构造函数中初始化
val inferredType = 1   // 类型 Int、默认 getter
```

下面是自定义 getter 和 setter 的实例：

```kt
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value)   // 解析字符串并赋值给其他属性
    }

val isEmpty: Boolean
    get() = this.size == 0
```

> 注意：属性本身不会存储值 (引用)，它的值是存储在一个编译器自动生成的字段 (field) 中，平时写代码可能不需要用到，于是被称为 **幕后字段**。


### 幕后字段

在 Kotlin 类中是不能直接声明字段的，当一个属性需要对应字段时 Kotlin 会自动提供，也就是我们上面提到的幕后字段。幕后字段可以通过 `field` 标识符进行引用。比如：

```kt
var count = 0  // 该初始器会直接为幕后字段赋值
    set(value) {
        if (value >= 0) field = value
    }
```

> 注意：`field` 标识符只能在访问器内使用。

**前面说到的「需要」二字有点含糊，怎样才算是需要？**

「需要」是指，该属性至少有一个访问器使用了默认实现，或自定义的访问器有通过 `field` 标识符来引用幕后字段。比如，下面的 `isEmpty` 属性就属于「不需要」幕后字段的情况：

```kt
// 只读声明
// 且 get 访问器没有使用默认实现，也没有使用 field 标识符
val isEmpty: Boolean
    get() = this.size == 0
```


### 字符串模板

* 字符串模板：即包含了模板表达式的字符串。
* 模板表达式：以美元符号 `$` 开头的一段代码，执行时会进行求值并把结果合并到字符串中。

字符串模板的使用主要有两种形式：

**第一种： `$` + 变量名称**

```kt
val i = 10
println("i = $i") // 输出“i = 10”
```

**第二种： `$` + 花括号括起来的任意表达式**

```kt
val s = "abc"
println("$s.length is ${s.length}") // 输出“abc.length is 3”
```

> 如果想在原始字符串中表示字面量 `$` 字符，可以酱紫：`"${'$'}9.99"`。