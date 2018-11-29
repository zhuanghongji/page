# Kotlin Examples - Loops

## 示例

**for, while, do-while**

```kt
val cakes = listOf("carrot", "cheese", "chocolate")

for (cake in cakes) {                               // 1
    println("Yummy, it's a $cake cake!")
}
```

```
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

```
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

while 和 do-while 结构的工作方式与大多数语言的类似。

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

```
Watch out, it's a zebra
Watch out, it's a lion
```

1. 提供一个迭代器 (iterator) 实现，通过 `operator` 修饰符进行标识。
2. 返回列表的迭代器，它符合以下方法要求：
  1. `next(): Animal`
  2. `hasNext(): Boolean`
3. 遍历 `zoo` 中的每个 `animal`。

迭代器可以向上述方式那样声明，也可以作为扩展函数进行声明。
