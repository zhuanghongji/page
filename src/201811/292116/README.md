# Kotlin Examples - Object keyword

## 示例

Kotlin 提供了一个 `object` 关键字，可用来声明单例类、包装数据和充当匿名内部类。


`object` 表达式，不需要类声明，通过一个简单的 "对象/属性" 结构就可以直接创建对象并使用：

```kt
fun rentPrice(standardDays: Int, festivityDays: Int, specialDays: Int): Unit {  // 1

    val dayRates = object {                                                     // 2
        var standard: Int = 30 * standardDays
        var festivity: Int = 50 * festivityDays
        var special: Int = 100 * specialDays
    }

    val total = dayRates.standard + dayRates.festivity + dayRates.special       // 3

    print("Total price: $$total")                                               // 4
}


fun main(){
    rentPrice(10, 2, 1)                                                         // 5
}
```
```kt
Total price: $500
```

1. 创建一个 `rentPrice` 函数，带参数 (常规日，节假日，特殊日期)。
2. 创建 `dayRates` 对象，你可以在其中设置一系列变量值。
3. 访问对象的变量。
4. 打印 `total`。
5. 访问实例 (初始化)，调用函数。


对象声明，不能在变量赋值中使用，但可以直接引用或调用其内部定义的函数。

```kt
object DoAuth {                                                 // 1
    fun takeParams(username: String, password: String){         // 2
        println("input Auth parameters = $username:$password")
    }
}

fun main(){
    DoAuth.takeParams("foo", "qwerty")         // 3
}
```
```kt
input Auth parameters = foo:qwerty
```

1. 进行对象声明。
2. 定义方法。
3. 使用对象 (初始化)，调用方法。


类中的对象声明，定义了另一个有用的场景：**伴随对象**。

> 在语法上有点类似 Java 中的静态方法，可以直接使用类名作为限定符来调用对象的成员。注意，在定义伴随对象之前，最好先确定编写简单的包级函数是否是更好的选择。

```kt
class BigBean {                             // 1
    companion object Bonger {               // 2
        fun getBongs(nTimes: Int) {         // 3
            for (i in 1 .. nTimes) {
                print("BONG ")
            }
        }
    }
}

fun main(){
    BigBean.getBongs(4)              // 4
}
```
```kt
BONG BONG BONG BONG 
```

1. 类定义，伴随初始化。
2. 伴随定义 - 名字可省略。
3. 方法定义。
4. 访问伴随对象。



## 扩展

### 对象表达式

如果要创建一个继承自某个 (或某些) 类型的匿名类的对象，可以这样写：

```kt
// 模拟一个支持点击和长按的 View
// 并支持设置对应事件的监听器

interface OnClickListener {
    fun onClick(s: String)
}

interface OnLongPressListener {
    fun onLongPress(s: String)
}

class View {
    var clickCount = 0
    var longPressCount = 0

    var mOnClickListener: OnClickListener? = null
    var mOnLongPressListener: OnLongPressListener? = null

    fun setOnClickListener(listener: OnClickListener) {
        mOnClickListener = listener
    }

    fun setOnLongPressListener(listener: OnLongPressListener) {
        mOnLongPressListener = listener
    }

    fun click(s: String) {
        clickCount++
        mOnClickListener?.onClick(s)
    }

    fun longPress(s: String) {
        longPressCount++
        mOnLongPressListener?.onLongPress(s)
    }
}
```

示例代码 (为 `View` 设置事件监听器) 和日志输出如下：

```kt
fun main(){
    val view1 = View()
    view1.setOnClickListener(object : OnClickListener {
        override fun onClick(s: String) {
            println("onClick: s = $s")
        }
    })
    view1.click("hi")
    view1.click("hello")

    val view2 = View()
    view2.setOnLongPressListener(object : OnLongPressListener {
        override fun onLongPress(s: String) {
            println("onLongPress: s = $s")
        }
    })
    view2.longPress("good morning")
    view2.longPress("good night")
}
```
```kt
onClick: s = hi
onClick: s = hello
onLongPress: s = good morning
onLongPress: s = good night
```

由 `clickCount++` 和 `longPressCount++` 可以看出，对象表达式和 Java 中的匿名内部类有点相似，都可以访问来自包含它的作用域的变量。但有个重要区别，对象表达式不限于访问 `final` 或实际相当于 `final` 的变量。

另外可能有读者会想，上面使用对象表达式的示例中，`object` 都只是实现了一个接口，能不能让它同时实现多个接口？比如同时实现 `OnClickListener` 和 `OnLongPressListener` 这两个监听器接口？

<img src="./res/001.jpg" width="120">

不支持的话我会在这里乱吹牛哔？不信你看下面这段代码：

```kt
val listener = object : OnClickListener, OnLongPressListener {
    override fun onClick(s: String) {
        println("listener_onClick: s = $s")
    }

    override fun onLongPress(s: String) {
        println("listener_onLongPress: s = $s")
    }
}

val view3 = View()
view3.setOnClickListener(listener)
view3.setOnLongPressListener(listener)
view3.click("interesting")
view3.click("long long interesting")
```
```kt
listener_onClick: s = interesting
listener_onClick: s = long long interesting
```

卧槽，牛哔！那如果我的 `object` 不只是想实现接口，还想继承自某个超类型呢？也木有问题，但如果超类型有一个构造函数，在使用对象表达式时就必须传递适当地构造函数参数给它：

```kt
open class A(x: Int) {
    open val y: Int = x
}

interface B {
    fun printSomething()
}

fun main(){
    val ab = object : A(10), B {
        override val y: Int = 15
        override fun printSomething() {
            println("y = $y")
        }
    }
    println(ab.y)
    ab.printSomething()
}
```

日志输出如下：

```kt
15
y = 15
```

任何时候，如果我们只是需要一个对象而需要特殊的超类型，对象表达式就可以写得很简洁：

```kt
fun foo() {
    val adHoc = object {
        var x: Int = 2
        var y: Int = 3
    }
    println(adHoc.x + adHoc.y)  // 5
}
```

注意，匿名对象只能用于「在本地或私有作用域中声明的类型」。也就是说，如果你使用匿名对象作为公有函数的返回值或公有属性的值，那么该函数或属性的实际类型就会是匿名对象声明时的超类型 (如果未显式声明任何超类型，则是 `Any`) ：

```kt
class C {
    // 私有函数，其返回类型是匿名函数类型
    private fun foo() = object {
        val x: String = "x"
    }

    // 公有函数，其返回类型是 Any
    fun publicFoo() = object {
        val x: String = "x"
    }

    fun bar() {
        val x1 = foo().x          // 没问题，x1 的类型为 String
        val x2 = publicFoo().x    // Error: Kotlin: Unresolved reference: x
    }
}
```


### 对象声明

在 Java 中我们经常会用到单例设计模式，单例模式？脑海中马上浮现出双重校验锁单例写法？啊，糟糕，好像忘了，正准备打开浏览器 Google 一下...

<img src="./res/002.png" width="120">

客官别急，我们现在是在写 Kotlin 而不是 Java。让谷歌自己飞一会，因为在 Kotlin 中使用单例非常简单，使用 `object` 关键字声明一下就好了：

```kt
object HttpManager {
    fun get() {
        println("Get..")
    }

    fun post() {
        println("Post..")
    }
}

HttpManager.get()       // Get..
HttpManager.post()      // Post..
```

这就是 Kotlin 中单例的使用姿势，接着引申出我们接下来要讲的对象声明。

对象声明：
* 总是在 `object` 关键字后跟一个名称。
* 有点类似变量的声明，但对象声明并不是表达式，也就不能用在赋值语句的右边了。

对象声明的初始化过程是线程安全的，如果需要引用该对象，直接使用名称即可。比如在上面的示例中，我们通过名称引用了 `HttpManager` 对象并调用其 `get()` 和 `set()` 方法：

```kt
HttpManager.get() 
HttpManager.post() 
```

声明对象可以有超类型或接口实现，比如：

```kt
object CustomClickListener: OnClickListener {
    override fun onClick(s: String) {
        println("CustomClickListener, onClick: s = $s")
    }
}

fun main(){
   val view = View()
    view.setOnClickListener(CustomClickListener)
    view.click("从前有座山")
}
```
```kt
CustomClickListener, onClick: s = 从前有座山
```

### 对象表达式和对象声明之间的语义差异

对象表达式和对象声明之间有一个重要语义差异：
* 对象表达式是在使用它们的地方立即初始化并执行的，而对象声明是延迟到第一次被访问时才进行初始化的。

> 注：伴生对象是在相应的类被加载 (解析) 时初始化的，与 Java 静态初始化器的语义相匹配。

s
## 总结

在这篇文章中，我们简要的介绍了对象表达式和对象声明。由于之前的文章中已经介绍过伴生对象了，所以这里就省略了这部分的内容。

文章还有一点没说明的是，对象声明的 `object` 关键字是怎样做到单例的？又为什么是线程安全的？希望有机会可以回来补充下。

**Wish you are good everyday, my friend.**