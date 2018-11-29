# Kotlin Examples - Hello World

## 示例

```kt
package com.zhuanghongji.kotlin.examples.introduction   // 1

fun main() {                                            // 2
    println("Hello, World!")                            // 3
}
```

```
Hello, World!
```

1. 通常情况下，Kotlin 代码会在包内定义。如果你没定义代码所在的包名，则会使用默认包名。
2. Kotlin 程序主要入口点是一个名为 `main` 的函数，从 Kotlin 1.3 开始可以将其定义为没有参数的函数。
3. `println` 是隐式导入的，可用来打印标准输出。要注意的是，分号是可选的。


在早于 Kotlin 1.3 的版本中，`main` 函数必须定义一个参数：

```kt
fun main(args: Array<String>) {                        
    println("Hello, World!")                           
}
```

```
Hello, World!
```

## 拓展

Kotlin 是一种在 Java 虚拟机上运行的静态类型编程语言，由 JetBrains 设计开发并开源，第一个官方稳定版本 V1.0 于 2016 年 02 月 15 日发布。在 Google I/O 2017 中，Goolge 宣布 Kotlin 成为 Android 开发一级语言。

Kotlin 非常适合开发 Android 应用程序，将现代语言的所有优势带入 Android 平台并且不会引入任何新的限制。

* 兼容性：与 JDK 6 完全兼容，在 Android Studio 中得到完全支持。
* 性能：运行速度与 Java 类似，内联函数的支持使得 lambda 表达式代码通常比 Java 写的运行更快。
* 互操作性：可与 Java 代码进行 100% 的互操作性，可使用所有现有的 Android 库。
* 占用：在实际应用程序中，Kotlin 运行时只增加几百个方法，增加大小不到 100K。
* 编译时长：增量构建通常与 Java 一样快或者更快。
* 学习曲线：对于 Java 开发人员，Kotlin 入门很容易。
