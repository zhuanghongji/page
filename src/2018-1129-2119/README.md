# Kotlin Examples - Extension Functions and Properties

## 示例

```kt
data class Item(val name: String, val price: Float)                                   // 1  

data class Order(val items: Collection<Item>)  

fun Order.maxPricedItemValue(): Float = this.items.maxBy { it.price }?.price ?: 0F    // 2  
fun Order.maxPricedItemName() = this.items.maxBy { it.price }?.name ?: "NO_PRODUCTS"

val Order.commaDelimitedItemNames: String                                             // 3
    get() = items.map { it.name }.joinToString()

fun main() {

    val order = Order(listOf(Item("Bread", 25.0F), Item("Wine", 29.0F), Item("Water", 12.0F)))

    println("Max priced item name: ${order.maxPricedItemName()}")
    println("Max priced item value: ${order.maxPricedItemValue()}")
    println("Items: ${order.commaDelimitedItemNames}")

}
```

1. 我们定义了两个简单的数据类 `Item` 和 `Order`。`Order` 可以包含任意 `Collection` (of `Item` s)。
2. 为 `Order` 类型定义扩展扩展函数，稍后我们直接在该类型的实例上调用这些函数。
3. 我们还可以定义扩展属性。


阅读更多有关 [扩展](https://kotlinlang.org/docs/reference/extensions.html) 签名 (signature) 的更多信息。签名非常类似于标准函数或属性签名，另外我们需要制定所附加函数或附加属性的类型。

甚至可以在 `null` 引用上进行扩展。在实现中我们可以检查 `null` 引用，基于此我们可以作任何的任意逻辑。比如：

```kt
//sampleStart
fun <T> T?.nullSafeToString() = this?.toString() ?: "NULL"  // 1
//sampleEnd
fun main() {
    println(null.nullSafeToString())
    println("Kotlin".nullSafeToString())
}
```
