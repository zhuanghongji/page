# Kotlin Examples - Smart Casts

## 示例

Kotlin 的编译器非常智能，可以在大多数场景下自动执行类型转换，包括：
1. 从 nullable 类型转换成对应的 non-nullable 类型。
2. 从超类型转换成子类型。


```kt
import java.time.LocalDate
import java.time.chrono.ChronoLocalDate

fun main() {
//sampleStart
    val date: ChronoLocalDate? = LocalDate.now()    // 1

    if (date != null) {
        println(date.isLeapYear)                    // 2
    }

    if (date != null && date.isLeapYear) {          // 3
        println("It's a leap year!")
    }

    if (date == null || !date.isLeapYear) {         // 4
        println("There's no Feb 29 this year...")
    }

    if (date is LocalDate) {
        val month = date.monthValue                 // 5
        println(month)
    }
//sampleEnd
}
```

```
false
There's no Feb 29 this year...
11
```

1. 定义一个 nullable 变量。
2. 智能转换成 non-nullable (因此允许直接方位 `isLeapYear`)。
3. 在条件内进行智能转换 (这是可能的，类似 Java，Kotlin 使用 [short-circuiting](https://en.wikipedia.org/wiki/Short-circuit_evaluation))
4. 在条件内进行智能转换 (也可通过 short-circuiting 实现)
5. 智能转换成子类型 `LocalDate`

这样，你可以在大多是情况下自动使用变量，而无需手动执行明显的强制转换。
