# Kotlin Examples - Collections

在这篇文章中，我们主要是将 Kotlin 中的集合，以及标准库中提供的集合操作函数。

主要有以下内容：

* List, Set, Map
* filter
* map 
* any, all, none
* first, last
* count
* associateBy, groupBy
* partition
* flatMap
* min, max
* sorted
* Map element access
* zip
* getOrElse


## 示例

### List

一个列表是项目的有序集合。在 Kotlin 中，列表可以是 mutable (MutableList) 或只读 (List) 。对于列表创建，请将标准库函数 `listOf()` 用于只读列表和 `mutableListOf()` 可变列表。要防止不必要的修改，请通过将可变列表强制转换为只读视图 `List`。

```kt
val systemUsers: MutableList<Int> = mutableListOf(1, 2, 3)        // 1
val sudoers: List<Int> = systemUsers                              // 2

fun addSudoer(newUser: Int) {                                     // 3
    systemUsers.add(newUser)                      
}

fun getSysSudoers(): List<Int> {                                  // 4
    return sudoers
}

fun main() {
    addSudoer(4)                                                  // 5 
    println("Tot sudoers: ${getSysSudoers().size}")               // 6
    getSysSudoers().forEach{                                      // 7
        i -> println("Some useful info on user $i")
    }
    // getSysSudoers().add(5) <- Error!                           // 8
}
```

1. 创建一个 `MutableList`。
2. 创建列表的只读视图。
3. 添加一个新项目 `MutableList`。
4. 一个返回不可变的函数 `List`。
5. 更新 `MutableList`。所有相关的只读视图也会更新，因为它们指向同一个对象。
6. 检索只读列表的大小。
7. 迭代列表并打印其元素。
8. 尝试写入只读视图会导致编译错误。


### Set

一组是一个无序的集合，它不支持重复。对于创建集，有功能 `setOf()` 和 `mutableSetOf()`。可以通过将其转换为可变集来获得只读视图 `Set`。

```kt
val openIssues: MutableSet<String> = mutableSetOf("uniqueDescr1", "uniqueDescr2", "uniqueDescr3") // 1

fun addIssue(uniqueDesc: String): Boolean {                                                       
    return openIssues.add(uniqueDesc)                                                             // 2
}

fun getStatusLog(isAdded: Boolean): String {                                                       
    return if (isAdded) "registered correctly." else "marked as duplicate and rejected."          // 3
}

fun main() {
    val aNewIssue: String = "uniqueDescr4"
    val anIssueAlreadyIn: String = "uniqueDescr2" 

    println("Issue $aNewIssue ${getStatusLog(addIssue(aNewIssue))}")                              // 4
    println("Issue $anIssueAlreadyIn ${getStatusLog(addIssue(anIssueAlreadyIn))}")                // 5 
}
```

1. 添加元素到 `Set`。
2. 返回一个布尔值，显示元素是否实际添加。
3. 返回基于函数输入参数的字符串。
4. 打印成功消息：将新元素添加到 `Set`。
5. 打印失败消息：无法添加元素，因为它复制了现有元素。


### Map

甲地图是键/值对，其中每个密钥都是唯一的并用于检索相应的值的集合。对于创建地图，有功能 `mapOf()` 和 `mutableMapOf()`。可以通过将其转换为可变映射获得只读视图 `Map`。

```kt
const val POINTS_X_PASS: Int = 15
val EZPassAccounts: MutableMap<Int, Int> = mutableMapOf(1 to 100, 2 to 100, 3 to 100)   // 1
val EZPassReport: Map<Int, Int> = EZPassAccounts                                        // 2

fun updatePointsCredit(accountId: Int) {
    if (EZPassAccounts.containsKey(accountId)) {                                        // 3
        println("Updating $accountId...")                                               
        EZPassAccounts[accountId] = EZPassAccounts.getValue(accountId) + POINTS_X_PASS  // 4
    } else {
        println("Error: Trying to update a non-existing account (id: $accountId)")
    } 
}

fun accountsReport() {
    println("EZ-Pass report:")
    EZPassReport.forEach{                                                               // 5
        k, v -> println("ID $k: credit $v")
    }
}

fun main() {
    accountsReport()                                                                    // 6
    updatePointsCredit(1)                                                               // 7
    updatePointsCredit(1)                                                               
    updatePointsCredit(5)                                                               // 8 
    accountsReport()                                                                    // 9
}
```

1. 创建一个可变的Map。
2. 创建一个只读的视图Map。
3. 检查是否Map存在密钥。
4. 读取相应的值并使用常量值递增。
5. 迭代不可变Map并打印键/值对。
6. 在更新之前读取帐户点余额。
7. 两次更新现有帐户。
8. 尝试更新不存在的帐户：输出错误消息。
9. 更新后读取帐户点余额。


### filter

filter 是标准库中定义的函数，可用于集合的过滤，它接收的断言 (predicate) 作为 lambda 参数。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)      // 1

    val positives = numbers.filter { x -> x > 0 }  // 2

    val negatives = numbers.filter { it < 0 }      // 3
//sampleEnd

    println("Numbers: $numbers")
    println("Positive Numbers: $positives")
    println("Negative Numbers: $negatives")
}
```

1. 定义一个数字集合。
2. 过滤负数.
3. 或者使用更短的方式来过滤整数。


### map

map 是在标准库中定义的扩展函数，可用于转将集合转好成另一个集合。它需要一个 transformer 作为 lambda 参数。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)     // 1

    val doubled = numbers.map { x -> x * 2 }      // 2

    val tripled = numbers.map { it * 3 }          // 3
//sampleEnd

    println("Numbers: $numbers")
    println("Doubled Numbers: $doubled")
    println("Tripled Numbers: $tripled")
}
```

1. 定义数字集合。
2. 将数字双倍化。
3. 或者使用更短的方式将数字三倍化。


### any, all, none

这三扩展函数基于给定断言 (predicate) 回答了关于集合中存有元素的问题。

**`any` 函数**

如果集合含有至少一个与给定断言相匹配的元素，则 `any` 函数将返回 `true`。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val anyNegative = numbers.any { it < 0 }             // 2

    val anyGT6 = numbers.any { it > 6 }                  // 3
//sampleEnd

    println("Numbers: $numbers")
    println("Is there any number less than 0: $anyNegative")
    println("Is there any number greater than 6: $anyGT6")
}
```

1. 定义数字集合。
2. 集合中是否存在小于 0 的元素。
3. 集合中是否存在大于 6 的元素。


**`all` 函数**

如果集合中所有的元素都与给定断言相匹配，则 `all` 函数返回 `true`。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val allEven = numbers.all { it % 2 == 0 }            // 2

    val allLess6 = numbers.all { it < 6 }                // 3
//sampleEnd

    println("Numbers: $numbers")
    println("All numbers are even: $allEven")
    println("All numbers are less than 6: $allLess6")
}
```

1. 定义数字集合。
2. 集合中所有元素是否都是偶数。
3. 集合中所有元素是否都小于 6。


**`none` 函数**

如果集合中没有元素与给定断言相匹配，则 `none` 函数返回 `true`。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val allEven = numbers.none { it % 2 == 1 }           // 2

    val allLess6 = numbers.none { it > 6 }               // 3
//sampleEnd

    println("Numbers: $numbers")
    println("All numbers are even: $allEven")
    println("No element greater than 6: $allLess6")
}
```

1. 定义数字集合。
2. 集合中是否没有单数，即集合中所有的元素都是偶数。
3. 集合中是否没有元素大于 6。


### find

返回与给定断言 (predicate) 相匹配的第一个或最后一个元素，如果没有相匹配的话则返回 `null`。

```kt
fun main() {

//sampleStart
    val words = listOf("Lets", "find", "something", "in", "collection", "somehow")  // 1

    val first = words.find { it.startsWith("some") }                                // 2
    val last = words.findLast { it.startsWith("some") }                             // 3

    val nothing = words.find { it.contains("nothing") }                             // 4
//sampleEnd

    println("The first word starting with \"some\" is \"$first\"")
    println("The last word starting with \"some\" is \"$last\"")
    println("The first word containing \"nothing\" is ${nothing?.let { "\"$it\"" } ?: "null"}")
}
```

1. 定义有着不同单词元素的集合。
2. 查找第一个以 "some" 开头的单词。
3. 查找最后一个以 "some" 开头的单词。
4. 查找第一个含有 "nothing" 的单词。


### first, last

这些函数返回集合的第一个或最后一个元素，或返回与给定断言 (predicate) 相匹配的第一个或最后一个元素。

在空集合或集合没有与断言相匹配元素的情况下，会抛出一个 `NoSuchElementException`。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val first = numbers.first()                          // 2
    val last = numbers.last()                            // 3

    val firstEven = numbers.first { it % 2 == 0 }        // 4
    val lastOdd = numbers.last { it % 2 != 0 }           // 5
//sampleEnd

    println("Numbers: $numbers")
    println("First $first, last $last, first even $firstEven, last odd $lastOdd")
}
```

1. 定义数字集合。
2. 选择第一个元素。
3. 选择最后一个元素。
4. 选择第一个偶数。
5. 选择最后一个单数。


### count

该扩展函数返回集合元素的总个数，或返回与给定断言 (predicate) 相匹配的元素的个数。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)            // 1

    val totalCount = numbers.count()                     // 2
    val evenCount = numbers.count { it % 2 == 0 }        // 3
//sampleEnd

    println("Total number of elements: $totalCount")
    println("Number of even elements: $evenCount")
}
```

1. 定义数字集合。
2. 计算元素总数。
3. 计算偶数元素的总数。


### associateBy, groupBy

这两个方法都会返回包含给定集合元素的 `Map`，这些元素由 `keySelector` 函数返回的键值进行索引。如果 `valueSelector` 也被传递，则也其会应用到每个元素。

`associateBy` 和 `groupBy` 的差异在于相同键值 (key) 对应的值 (value) 的行为。`associateBy` 只将最后一个元素放入集合，而 `groupBy` 会将所有的元素都放入集合中。

返回的 map 保留着元素集合 entry 的迭代顺序。


```kt
fun main() {

//sampleStart

    data class Person(val name: String, val city: String, val phone: String) // 1

    val people = listOf(                                                     // 2
      Person("John", "Boston", "+1-888-123456"),
      Person("Sarah", "Munich", "+49-777-789123"),
      Person("Svyatoslav", "Saint-Petersburg", "+7-999-456789"),
      Person("Vasilisa", "Saint-Petersburg", "+7-999-123456"))

    val phoneBook = people.associateBy { it.phone }                          // 3
    val cityBook = people.associateBy(Person::phone, Person::city)           // 4
    val peopleCities = people.groupBy(Person::city, Person::name)            // 5

//sampleEnd

    println("People: $people")
    println("")
    println("Phone book: $phoneBook")
    println("")
    println("City book: $cityBook")
    println("")
    println("People living in each city: $peopleCities")
}
```

```
People: [Person(name=John, city=Boston, phone=+1-888-123456), Person(name=Sarah, city=Munich, phone=+49-777-789123), Person(name=Svyatoslav, city=Saint-Petersburg, phone=+7-999-456789), Person(name=Vasilisa, city=Saint-Petersburg, phone=+7-999-123456)]

Phone book: {+1-888-123456=Person(name=John, city=Boston, phone=+1-888-123456), +49-777-789123=Person(name=Sarah, city=Munich, phone=+49-777-789123), +7-999-456789=Person(name=Svyatoslav, city=Saint-Petersburg, phone=+7-999-456789), +7-999-123456=Person(name=Vasilisa, city=Saint-Petersburg, phone=+7-999-123456)}

City book: {+1-888-123456=Boston, +49-777-789123=Munich, +7-999-456789=Saint-Petersburg, +7-999-123456=Saint-Petersburg}

People living in each city: {Boston=[John], Munich=[Sarah], Saint-Petersburg=[Svyatoslav, Vasilisa]}
```


1. 定义数据类来描述一个 Person。
2. 定义已知 Person 的集合。
3. 构建一个 map，由 phone number 映射到 person information。
4. 构建另一个 map，由 phone number 映射到他们所住的 city。
5. 构建第三个 map，其包含城市信息及所住的人的名字。


### partition

该函数将原始集合拆分为一对列表，第一个列表所含元素是给定断言为 `true` 时的，第二个列表所含元素时给定断言为 `false` 时的。

```kt
fun main() {

//sampleStart
    val numbers = listOf(1, -2, 3, -4, 5, -6)                // 1

    val (postives, negatives) = numbers.partition { it > 0 } // 2
//sampleEnd

    println("Numbers: $numbers")
    println("Positive numbers: $postives")
    println("Negative numbers: $negatives")
}
```

1. 定义数字集合。
2. 使用 `partition` 函数将 `numbers` 分成两个列表，分别用于存储正数和负数。同时应用解构来获得 `pair` 中的成员。



### flatMap

返回单个列表，其包含从原始集合中的每个元素调用 `transform` 函数的结果中产生的所有元素。

```kt
fun main() {

    //sampleStart
    val numbers = listOf(1, 2, 3)                        // 1

    val tripled1 = numbers.flatMap { listOf(it, it, it) } // 2
    val tripled2 = numbers.flatMap { listOf(it * 4, it, it) } // 2
    //sampleEnd

    println("Numbers: $numbers")
    println("Transformed1: $tripled1")
    println("Transformed2: $tripled2")
}
```

```
Numbers: [1, 2, 3]
Transformed1: [1, 1, 1, 2, 2, 2, 3, 3, 3]
Transformed2: [4, 1, 1, 8, 2, 2, 12, 3, 3]
```

1. 定义数字集合。
2. 转换集合的每个元素，重复三次。


### min, max

返回集合中的最小或最大元素，如果集合为空则返回 `null`。

```kt
fun main() {

    val numbers = listOf(1, 2, 3)
    val empty = emptyList<Int>()

    println("Numbers: $numbers, min = ${numbers.min()} max = ${numbers.max()}") // 1
    println("Empty: $empty, min = ${empty.min()}, max = ${empty.max()}")        // 2
}
```

```
Numbers: [1, 2, 3], min = 1 max = 3
Empty: [], min = null, max = null
```

1. 对于非空集合元素，返回最小和最大元素。
2. 对于空集合，两个函数都会返回 `null`。


### sorted

`sorted` 会返回一个列列表，根据其自然排序顺序排序的所有元素。

`sorted` 会返回，根据指定选择器函数返回的值的自然排序顺序排序的所有元素。

```kt
fun main() {

//sampleStart
    val shuffled = listOf(5, 4, 2, 1, 3)     // 1
    val natural = shuffled.sorted()          // 2
    val inverted = shuffled.sortedBy { -it } // 3
//sampleEnd

    println("Shuffled: $shuffled")
    println("Natural order: $natural")
    println("Inverted natural order: $inverted")
}
```

```
Shuffled: [5, 4, 2, 1, 3]
Natural order: [1, 2, 3, 4, 5]
Inverted natural order: [5, 4, 3, 2, 1]
```

1. 定义混乱的数字集合。
2. 按自然顺序排序。
3. 按方向自然顺序排序。


### Map element access

`[]` 运算符返回与给定键 (key) 对应的值，如果该 map 中不存在该键的话则返回 `null`。

`getValue` 函数返回与给定键对应的现有值，或抛出异常，提示未找到该键。

如果该 map 是使用 withDefault 生成的，则该函数将返回默认值，而不是抛出异常。

```kt
fun main(args: Array<String>) {

//sampleStart
    val map = mapOf("key" to 42)

    val value1 = map["key"]                                     // 1
    val value2 = map["key2"]                                    // 2

    val value3: Int = map.getValue("key")                       // 1

    val mapWithDefault = map.withDefault { k -> k.length }
    val value4 = mapWithDefault.getValue("key2")                // 3

    try {
        map.getValue("anotherKey")                              // 4
    }
    catch (e: NoSuchElementException) {
        println("Message: $e")
    }

//sampleEnd

    println("value1 is $value1")
    println("value2 is $value2")
    println("value3 is $value3")
    println("value4 is $value4")
}
```

```
Message: java.util.NoSuchElementException: Key anotherKey is missing in the map.
value1 is 42
value2 is null
value3 is 42
value4 is 4
```

1. 产生 42 因为 `"key"` 在 map 中存在。
2. 产生 `null` 因为 `"key2"` 是缺失的。
3. 产生默认值 4 因为 `"key2"` 是缺失的。
4. 抛出 NoSuchElementException 因为 "anotherKey" 并不在 map 中。


### zip

将一个集合与其它集合合并到新的集合中。返回的集合要么包含一个来自源集合并且具有相同索引的 `Pair`，要么包含具有相同索引的元素给定变化后的结果。

一起来看一下：

```kt
fun main() {

//sampleStart
    val A = listOf("a", "b", "c")                  // 1
    val B = listOf(1, 2, 3, 4)                     // 1

    val resultPairs = A zip B                      // 2
    val resultReduce = A.zip(B) { a, b -> "$a$b" } // 3
//sampleEnd

    println("A to B: $resultPairs")
    println("\$A\$B: $resultReduce")
}
```

```
A to B: [(a, 1), (b, 2), (c, 3)]
$A$B: [a1, b2, c3]
```

1. 定义两个集合。
2. 只用中缀函数将他们 zip 到 pairs 中。
3. 将他们连接在一起。



### getOrElse

提供对集合元素的安全访问。它采用索引和一个函数来计算索引超出范围时的默认值。

```
fun main() {

//sampleStart
    val list = listOf(0, 10, 20)
    println(list.getOrElse(1) { 42 })    // 1
    println(list.getOrElse(10) { 42 })   // 2
//sampleEnd
}
```

```
10
42
```

1. 打印出索引为 1 的元素。
2. 打印出 42 因为索引 10 超出了范围。


`getOrElse` 也可以应用在 `Map` 上来获取给定键对应的值。

```kt
fun main() {

//sampleStart
    val map = mutableMapOf<String, Int?>()
    println(map.getOrElse("x") { 1 })       // 1

    map["x"] = 3
    println(map.getOrElse("x") { 1 })       // 2

    map["x"] = null
    println(map.getOrElse("x") { 1 })       // 1
//sampleEnd
}
```

```
1
3
1
```

1. 打印出默认值，因为键 `"X"` 是缺失的。
2. 打印出键 `"X"` 对应的值 `3`。
