# Kotlin Examples - Variables

## 示例

Kotlin 有着强大的类型推导。虽然你可以显示的声明变量的类型，但通常你会让编译器通过推导来完成这个工作。Kotlin 并不强制不变性，尽管这是建议的。本质上，使用 `val` 应该比 `var` 多。

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



变量初始化可以推迟，但再第一次读取之前必须初始化。

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
