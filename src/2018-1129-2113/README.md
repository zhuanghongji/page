# Kotlin Examples - Data Classes

## 示例

数据类使得声明用于存储某些值的类变得更加容易。

```kt
data class User(val name: String, val id: Int)

fun main() {
    val user = User("Alex", 1)
    println(user)                                          // 1

    val secondUser = User("Alex", 1)
    val thirdUser = User("Max", 2)

    println("user == secondUser: ${user == secondUser}")   // 2
    println("user == thirdUser: ${user == thirdUser}")

    println(user.hashCode())                               // 3
    println(thirdUser.hashCode())

    // copy() function
    println(user.copy())                                   // 4
    println(user.copy("Max", 2))                           // 5
    println(user.copy("Max"))                              // 6
    println(user.copy(id = 2))                             // 7

    println("name = ${user.component1()}")                 // 8
    println("id = ${user.component2()}")
}
```

```
User(name=Alex, id=1)
user == secondUser: true
user == thirdUser: false
63347075
2390846
User(name=Alex, id=1)
User(name=Max, id=2)
User(name=Max, id=1)
User(name=Alex, id=2)
name = Alex
id = 1
```

1. 方法 `toString` 是自动生成的，这使得 `println` 的输出更加清晰。
2. 自动生成的 `equals` 使得具有相同信息的数据类也相等。
3. 相等的数据类有着相等的 `hashCode()`。
4. 预定义的 `copy` 函数使得获取新实例更加容易。
5. 可以在副本上更改属性值。顺序对应于构造函数的参数顺序。
6. 也可以仅修改某些值。
7. 使用命名参数更改第二个值，而不更改第一个值。
8. 此外，海水生成特殊的 `componentN` 函数。
