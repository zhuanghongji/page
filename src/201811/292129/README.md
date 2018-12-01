# Kotlin Examples - associateBy, groupBy

## 示例

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
