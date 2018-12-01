# Kotlin Examples - Object keyword

## 示例

首先，让我们从一些进本的 OOP 概念开始：一个类是一个蓝图 (blueprint)，一个对象是一个类的实例。

你定义一个类，然后创建该类的多个实例：

```kt
import java.util.Random

class LuckDispatcher{                     //1
    fun getNumber() {                     //2
        var objRandom = Random()
        println(objRandom.nextInt(90))
    }
}

fun main(){
    val d1 = LuckDispatcher()             //3
    val d2 = LuckDispatcher()

    d1.getNumber()                        //4
    d2.getNumber()
}
```

1. 蓝图定义
2. 方法定义
3. 创建实例
4. 方法调用

简单：我们创建了两个对象，且都是 `LuckDispatcher` 类的实例。

在 Kotlin 中有个 `object` 关键字。这是什么？是一个 **具有单个实现的数据类型**。

如果你是 Java 使用者并且想明白 "单个" 是什么意思，你可以想到单例模式：它允许你检查是否将创建该类的一个 (且仅一个) 实例，即使两个线程访问它也是如此。

要在 Kotlin 中实现这一点，你只需要一个 `object` 声明：没有类，没有构造函数，只是一个惰性实例。什么是惰性？因为如果使用了对象，它将被创建一次，否则不会。

在这个示例中，你将看到 **object 表达式** 的一个典型基础用法：一个简单的 "对象/属性" 结构。不需要类声明：创建单例对象，声明成员并访问它。类似这样的对象，通常像 Java 中的匿名类一样使用。

```kt
fun rentPrice(standardDays: Int, festivityDays: Int, specialDays: Int): Unit {  //1

    val dayRates = object {                                                     //2
        var standard: Int = 30 * standardDays
        var festivity: Int = 50 * festivityDays
        var special: Int = 100 * specialDays
    }

    val total = dayRates.standard + dayRates.festivity + dayRates.special       //3

    print("Total price: $$total")                                               //4

}


fun main(){
    rentPrice(10, 2, 1)                                                         //5
}
```

1. 创建一个 `rentPrice` 函数，带参数 (常规日，节假日，特殊日期)。
2. 创建 rates 对象，你可以在其中设置一系列变量值。
3. 访问对象的变量。
4. 打印 total。
5. 访问实例 (初始化)，调用函数。


同时你也可以进行对象声明 (object declaration)：并非表达式，不能在变量赋值中使用，必须直接使用。

```kt
object DoAuth {                                                 //1
    fun takeParams(username: String, password: String){         //2
        println("input Auth parameters = $username:$password")
    }
}


fun main(){
    DoAuth.takeParams("foo", "qwerty")                          //3
}
```

1. 进行对象声明。
2. 定义方法。
3. 使用对象 (初始化)，调用方法。


类中的对象声明，定义了另一个有用的场景：**伴随对象**。在语法上类似 Java 中的静态方法，你可以直接使用类 (类名) 作为限定符来调用对象的成员。在 Kotlin 中，在定义伴随对象之前，先确定编写简单的包级函数是否更好。

```kt
class BigBen {                                  //1
    companion object Bonger{                    //2
        fun getBongs(nTimes: Int){              //3
            for (i in 1 .. nTimes){
                print("BONG ")
            }
        }
    }
}

fun main(){
    BigBen.getBongs(12)                     //4
}
```

1. 类定义，伴随初始化。
2. 伴随定义 - 名字可省略。
3. 方法定义。
4. 访问伴随对象。
