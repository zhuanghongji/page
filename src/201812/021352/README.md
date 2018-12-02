# Swift 编码之旅

参考自：
* [About Swift](https://swift.org/about/#platform-support)
* [A Swift Tour](https://docs.swift.org/swift-book/GuidedTour/GuidedTour.html)

## 1. 关于

Swift 是编写应用程序的绝佳选择，无论是用于开发手机、电脑、服务器还是其它任何运行代码的软件。它是一种安的全、快速的和交互式的编程语言，是现代编程语言最佳思维、广泛 Apple 工程文化智慧以及开源社区各种贡献的结合体。编译器同时​​针对性能和开发进行了优化，不会影响其中任何一种。

Swift 对新程序员很友好。它是一种工业级编程语言，且与脚本语言一样富有表现力和乐趣。在 playground 上编写 Swift 代码时，你可以快速试验代码并即时查看运行结果，而无需等待应用程序的构建和运行。

Swift 通过采用现代编程模式来避免一些常见的编程错误：

* 变量在使用前始终初始化
* 检查数组索引是否存在越界错误
* 检查整数是否溢出
* Optionals 确保处理 nil 值时是明确的
* 内存自动管理
* 错误处理允许从意外故障中受控恢复

得益于编译和优化，Swift 代码可以充分地利用现代硬件。语法和标准库的设计基于指导原则，编写代码的方式清晰且性能最佳。安全性和速度的结合使得 Swift 成为从 “Hello，world!” 到整个操作系统的绝佳选择。

Swift 将强大的类型推导、模式匹配与现代轻量级语法相结合，允许以清晰简洁的方式表达复杂的想法。因此，Swift 代码不仅容易编写，且易于阅读和维护。

Swift 多年来一直在不断进化，持续发展新特性和功能。我们对 Swift 的目标雄心勃勃，也迫不及待地想看看你用它创造了什么。

## 2. 编码之旅

通常来说，学习编程语言时的第一个姿势就是在控制台中打印出 `Hello World`。  

在 Swift 中，只用一行代码就可以做到：

```swift
print("Hello World!")
```

这行代码就是一个完整的程序：
* 不需要为了输入输出导入任何库。
* 不需要 `main()` 函数，全局作用域中的代码会被自动当做程序的入口。
* 不需要在每个语句结尾写上分号。

编码之旅部分包含以下内容：
* 简单的值
* 控制流
* 函数和闭包
* 对象和类
* 枚举和结构体
* 协议和扩展
* 泛型

### 2.1 简单的值

#### 变量和常量

* **使用 `var` 关键字声明变量**：可以多次进行相同类型的赋值。
* **使用 `let` 关键字声明常量**：常量的值在编译时并不需要明确，但你只能对它赋值一次。

```swift
var myVariable = 42
myVariable = 50
myVariable = "ABC"  // Wrong: Cannot assign value of type 'String' to type 'Int'

let myConstant = 3
myConstant = 4      // Wrong: Cannot assign to value: 'myConstant' is a 'let' constant
```

如果我们在声明变量或常量的同时进行赋初始值的话，是可以省略声明类型的，因为编译器会自动推导出类型。比如上面示例中的 `myVariable`，编译器根据它的初始值推导出其类型是整型。

但如果我们不提供初始值或初始值没有提供足够信息用于推导时，我们就需要通过冒号在变量后面声明类型，比如

```swift
var a       // Wrong: Type annotation missing in pattern
var b: Int  // Right

let implicitInteger = 70
let implicitDouble = 70.0
let explicitDouble: Double = 70
```

在 Swift 中，所有的值都是不可能被隐式转换成其它类型的。如果你需要把一个值转换成其它类型，需要显式进行转换。

```swift
let label = "The width is "
let width = 96
let widthLabel = label + width          // Wrong: Binary operator '+' cannot be applied to operands of type 'String' and 'Int'
let widthLabel = label + String(width)  // Right
```

Swift 提供了一个简单的把值转换成字符串的方法：**把值写到括号中，并且在括号前面写一个反斜杠**。例如：

```swift
let apples = 3
let oranges = 5
let appleSummary = "I have \(apples) apples."
let orangeSummary = "I have \(oranges) oranges."
```

#### 数组和字典

使用方括号 `[]` 来创建数组和字典，并使用 `下标` 或者 `键(key)` 来访问元素。**最后一个元素后面是允许有逗号的**。

```swift
// catfish 鲶鱼
// tulip 郁金香
var shoppingList = ["catfish", "water", "tulips", "blue paint"]
shoppingList[1] = "bottle of water"
print(shoppingList)     // ["catfish", "bottle of water", "tulips", "blue paint"]

// occupation 职业
// mechanic 技工
var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
]
occupations["Jayne"] = "Public Relations"
print(occupations)      // ["Kaylee": "Mechanic", "Malcolm": "Captain", "Jayne": "Public Relations"]
```

使用初始化语法创建一个空数组和空字典：

```swift
let emptyArray = [String]()
let emptyDictionary = [String: Float]()

print(emptyArray.count)         // 0
print(emptyDictionary.count)    // 0
```

如果类型信息可以被推导出来，则可以用 `[]` 和 `[:]` 来创建空数组和空字典（类似声明变量或者给函数传参）。比如说，我们现在将上面示例中的 `shoppingList` 和 `occupations` 设置成空的：

```swift
shoppingList = []
occupations = [:]

print(shoppingList.count)   // 0
print(occupations.count)    // 0
```

### 2.2 控制流

使用 `if` 和 `switch` 来进行条件操作，使用 `for-in`、`for`、`while` 和 `repeat-while` 来进行循环操作：
* 包裹条件和循环变量括号可以省略。
* 但与语句体的大括号是必须的。

#### if

```swift
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in individualScores {
    if score > 50 {
        teamScore += 3
    } else {
        teamScore += 1
    }
}
print(teamScore)    // 11
```

在 `if` 语句中，条件必须是一个布尔表达式。

你可以一起使用 `if` 和 `let` 来处理值缺失的情况。

> 在类型后面加一个问号来标记这个变量的值是可选的。  
> 一个可选的值可以是 **一个具体值** 或者是 **表示值缺失的 `nil`**。

```swift
var optionalString: String? = "Hello"
print(optionalString == nil)  // false

var optionalName: String? = "John Appleseed"
var greeting = "Hello!"
if let name = optionalName {
    greeting = "Hello, \(name)"
} else {
    greeting = "optionalName is nil"
}
print(greeting)               // Hello, John Appleseed
```

> 如果把 `optionalName` 改成 `nil`，则 `greeting` 的值为 `optionalName is nil`。

* 如果变量的可选值为 `nil`，条件会判断为 `false`，大括号中的代码会被跳过。
* 如果不是 `nil`，会将值赋给 `let` 后面的常量，这样代码块中就可以使用这个值了。

#### switch

`switch` 支持任意类型的数据以及各种比较操作，而不仅仅是整数以及测试相等。

```swift
let vegetable = "red pepper"
switch vegetable {
case "celery":
    print("Add some raisins and make ants on a log.")
case "cucumber", "watercress":
    print("That would make a good tea sandwich.")
case let x where x.hasSuffix("pepper"):
    print("That would make a good tea sandwich.")
default:
    print("Everything tastes good in soup.")
}

// 打印结果：That would make a good tea sandwich.
```

> 如果删除 `default` 语句，编译不会通过，报错 `Switch must be exhaustive`。

上面例子的等式中，`let` 将匹配等式的值赋给常量 `x`。运行 `switch` 中匹配到的子句之后，程序会退出 `switch` 语句，并不会继续向下执行，所以不需要在每个子句结尾写 `break`。

#### for-in

你可以使用 `for-in` 来遍历字典，需要两个变量来表示每个键值对。

> 字典是一个无序的集合，所以它们的键和值是以任意顺序进行迭代直到结束。

```swift
let interestingNumbers = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25]
]
var largestKind = ""
var largestNumber = 0
for (kind, numbers) in interestingNumbers {
    for number in numbers {
        if number > largestNumber {
            largestKind = kind
            largestNumber = number
        }
    }
}
print("largestKind is \(largestKind), largestNumber is \(largestNumber)")
// largestKind is Square, largestNumber is 25
```

#### while

使用 `while` 来重复运行一段代码知道不满足条件。

```swift
var n = 2
while n < 100 {
    n = n * 2
}
print(n)    // 128

// 循环条件也可以在结尾，保证能至少循环一次
var m = 2
repeat {
    m = m * 2
} while m < 100
print(m)    // 128
```

你可以在循环中使用 `..<` 来表示范围，也可以使用传统的写法，两者是等价的：
```swift
var firstForLoop = 0
for i in 0..<4 {            // 0, 1, 2, 3
    firstForLoop += i
}
print(firstForLoop)         // 6

var secondForLoop = 0
for var i = 0; i < 4; ++i { // Wrong: C-style for statement has been removed in Swift 3
    secondForLoop += i
}
print(secondForLoop)
```

使用 `..<` 创建的范围不包括右区间，如果想包含的话需要使用 `...`:
```swift
for i in 0..<4 {
    print(i)  // 0, 1, 2, 3
}

for i in 0...4 {
    print(i)  // 0, 1, 2, 3, 4
}
```

### 2.3 函数和闭包

#### 函数

* 使用 `func` 来声明一个函数
* 使用 `->` 来指定函数返回值的类型
* 使用名字和参数来调用函数

```swift
func greet(name: String, day: String) -> String {
    return "Hello \(name), today is \(day)"
}
print(greet(name: "Bob", day: "Tuesday"))
// 打印结果：Hello Bob, today is Tuesday
```

使用元组来让一个函数返回多个值，该元组的元素可以用 **名称** 和 **下标** 来表示：

```swift
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0

    for score in scores {
        if score > max {
            max = score
        } else if score < min {
            min = score
        }
        sum += score
    }
    return (min, max, sum)
}
let statistics = calculateStatistics(scores: [5, 3, 100, 3, 9])
print(statistics.min)   // 3
print(statistics.max)   // 100
print(statistics.2)     // 120
```

函数可以带有可变个数的参数，这些参数在函数内表现为数组的形式：

```swift
// 求和
func sumOf(numbers: Int...) -> Int {
    var sum = 0
    for number in numbers {
        sum += number
    }
    return sum
}
print(sumOf())                      // 0
print(sumOf(numbers: 42, 597, 12))  // 651

// 求平均数
func averagef(numbers: Int...) -> Int {
    if numbers.count == 0 {
        return 0
    }

    var sum = 0
    for number in numbers {
        sum += number
    }
    return sum / numbers.count
}
print(averagef())                      // 0
print(averagef(numbers: 42, 597, 12))  // 217
```

函数是可以嵌套的。被嵌套的函数可以访问外层函数的变量，可用来重构一个太长或太复杂的函数。

```swift
func returnFifteem() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
print(returnFifteem())  // 15
```

函数是第一等类型，可以作为另一个函数的返回值。

```swift
func makeIncrementer() -> ((Int) -> Int) {
    func addOne(number: Int) -> Int {
        return number + 1
    }
    return addOne
}
var increment = makeIncrementer()
print(increment(7))   // 8
```

函数也可以当做参数传入另一个函数。

```swift
func hasAnyMatches(list: [Int], condition: (Int) -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
print(hasAnyMatches(list: numbers, condition: lessThanTen)) // true
```

#### 闭包

函数实际上是一种特殊的闭包（一段能在之后被调用的代码）：
* 闭包中的代码能访问闭包所建作用域中能得到的变量和函数，即使闭包是在一个不同的作用域被执行的。
* 可以使用 `{}` 来创建一个匿名闭包。
* 使用 `in` 将 `参数和返回值的类型声明` 与 `闭包函数体` 进行分离。

```swift
var numbers = [1, 2, 3, 4]

// 对所有数值其双倍
let res1 = numbers.map({
    (number: Int) -> Int in
    let result = number * 2
    return result
})
print(res1)     // [2, 4, 6, 8]

// 对所有奇数返回0
let res2 = numbers.map({
    (number: Int) -> Int in
    let result = (number % 2 == 1 ? 0 : number)
    return result
})
print(res2)     // [0, 2, 0, 4]
```

创建闭包还有其它更简洁的方法：

* 如果一个闭包的类型已知，你可以忽略参数的类型和返回值。
* 单个语句闭包会把它语句的值当做结果返回。
```swift
var numbers = [1, 2, 3, 4]
let mappedNumbers = numbers.map({ number in number * 3})
print(mappedNumbers)  // [3, 6, 9, 12]
```
* 在非常短的闭包中，可以通过 **参数位置** 而不是 **参数名字** 来引用参数。
* 当一个闭包作为最后一个参数传给一个函数的时候，它可以直接跟在括号后面。
* 当一个闭包是传给函数的唯一参数，你可以完全忽略括号。
```swift
var numbers = [1, 2, 3, 4]
let sortedNumbers = numbers.sorted { $0 > $1 }
print(sortedNumbers)  // [4, 3, 2, 1]
```


### 2.4 对象和类

使用 `class` 和类名来创建一个类。
* 类中 **属性的声明** 和 **常量、变量的声明** 一样（方法和函数的声明同理一样）。
* 唯一的区别就是它们的上下文是 **类**。

```swift
class Shape {
    var numberOfSides = 0
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```

* 要创建一个类的实例，在类名后面加上 **括号** 即可。  
* 使用 **点语法** 来访问实例的属性和方法。

```swift
var shape = Shape()
shape.numberOfSides = 7
var shapeDescription = shape.simpleDescription()
print(shapeDescription)  // A shape with 7 sides.
```

* 使用 `init` 来创建一个构造器，以初始化类实例。

```swift
class NamedShape {
    var numberOfSides = 0
    var name: String

    init(name: String) {
        self.name = name
    }
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}
```

> 注意：`self` 可被用来区别实例变量。 
>  
> 当你创建实例的时候，像传入函数参数一样给类传入构造器的参数。  
> 每个属性都需要赋值，无论是通过声明 (如 `numberOfSides`) 还是构造器(如 `name`)。

如果你需要在删除对象之前进行一些清理工作，使用 `deinit` 创建一个 **析构函数**。  

定义子类的方法：
* 在子类类名后面加上冒号，再加上父类的名字。  

子类如果要重写父类的方法，需要用 `override` 标记：

* 如果没有添加 `override` 就重写父类方法的话，编译器会报错。
* 添加 `override` 标记的方法如果没有在父类中的话，编译器也会报错。

```swift
class Square: NamedShape {
    var sideLength: Double

    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
    }

    func area() -> Double {
        return sideLength * sideLength
    }

    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)"
    }
}
let test = Square(sideLength: 5.2, name: "my test square")
print(test.area())                // 27.04
print(test.simpleDescription())   // A square with sides of length 5.2
```

#### getter 和 setter

除了存储简单的属性之外，属性可以有 getter 和 setter ：

```swift
/** 等边三角形 */
class EquilateralTriangle: NamedShape {
    var sideLength: Double

    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 3
    }

    // 周长
    var perimeter: Double {
        get {
            return sideLength * 3.0
        }

        set {
            sideLength = newValue / 3.0
        }
    }

    override func simpleDescription() -> String {
        return "An equilateral triangle with sides of length \(sideLength)"
    }
}

var triangle = EquilateralTriangle(sideLength: 3.1, name: "a triangle")
print(triangle.perimeter)   // 9.3

triangle.perimeter = 9.9
print(triangle.sideLength)  // 3.3
```

在 `perimeter` 的 setter 中，新值默认变量名为 `newValue`。当然，你也可以在 `set` 之后显式的设置一个变量名，比如：

```swift
set (newSideLength) {
    sideLength = newSideLength / 3.0
}
```

注意，`EquilateralTriangle` 类的构造器执行了三步：

1. 设置子类声明的属性值。
2. 调用父类的构造器。
3. 改变父类定义的属性值。
  

#### willset 和 didSet

如果你不需要计算属性，但是仍然需要在设置一个新值之前或之后运行代码，可以使用 `willset` 和 `didSet`。  

比如，下面的类确保三角形的边长总是和正方形的边长相同：

```swift
class TriangleAndSquare {
    var triangle: EquilateralTriangle {
        willSet {
            square.sideLength = newValue.sideLength
        }
    }

    var square: Square {
        willSet {
            triangle.sideLength = newValue.sideLength
        }
    }

    init(size: Double, name: String) {
        triangle = EquilateralTriangle(sideLength: size, name: name)
        square = Square(sideLength: size, name: name)
    }
}

var triangleAndSquare = TriangleAndSquare(size: 10, name: "another test shape")
print(triangleAndSquare.triangle.sideLength)    // 10.0
print(triangleAndSquare.square.sideLength)      // 10.0

triangleAndSquare.square = Square(sideLength: 50, name: "large square")
print(triangleAndSquare.triangle.sideLength)    // 50.0
````

处理变量的可选值时，你可以在操作 (比如 `方法`、`属性` 和 `子脚本`) 之前加 `?`：
* 如果 `?` 之前的值是 `nil`，`?` 后面的东西都会被忽略，并且整个表达式返回 `nil`。
* 否则，`?` 之后的代码都会被执行。

上述 **两种情况下，整个表达式的值都是一个可选值**。

```swift
// let optionalSquare: Square? = nil

let optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
let sideLength = optionalSquare?.sideLength
if let length = sideLength {
    print("sideLength is \(length)")    // sideLength is 2.5
} else {
    print("sideLength is nil")
}
````


### 2.5 枚举和结构体

#### 枚举

使用 `enum` 关键字来创建一个枚举。

> 枚举是可以包含方法的。

```swift
enum Rank: Int {
    case Ace = 1
    case Two, Three, Four, Five, Six, Seven, Eight, Nine, Ten
    case Jack, Queen, King

    func simpleDescription() -> String {
        switch self {
        case .Ace:
            return "ace"
        case .Jack:
            return "jack"
        default:
            return String(self.rawValue)
        }
    }
}

let a = Rank.Ace
print("a = \(a), aRawValue = \(a.rawValue), "
    + "aSimpleDescription = \(a.simpleDescription())")
// a = Ace, aRawValue = 1, aSimpleDescription = ace

let j = Rank.Jack
if a.rawValue > j.rawValue {
    print("rawValue: a is bigger than j")
} else {
    print("rawValue: a is smaller than j")
}
// rawValue: a is smaller than j

let two = Rank.Two
let three = Rank.Three
let queen = Rank.Queen
let king = Rank.King
print("rawValue: two = \(two.rawValue), "
    + "three = \(three.rawValue), queen = \(queen.rawValue), king = \(king.rawValue)")
// rawValue: two = 2, three = 3, queen = 12, king = 13
````

在上面的例子中，枚举原始值的类型是 `Int`，所以你只需要设置第一个原始值，剩下的原始值会按照顺序赋值：
* 你也可以使用字符串或者浮点数作为枚举的原始值。
* 使用 `rawValue` 属性来访问一个枚举成员的原始值。

使用 `init?(rawValue:)` 初始化构造器在原始值和枚举值之间转换：

```swift
if let convertedRank = Rank(rawValue: 3) {
    let threeDescription = convertedRank.simpleDescription()
    print(threeDescription) // 3
}
````

枚举的成员值是 **实际值**，并不是原始值的另一种表达方法。  

实际上，你可以不设置原始值：

```swift
enum Suit {
    case Spades, Hearts, Diamonds, Clubs

    func simpleDescription() -> String {
        switch self {
        case .Spades:
            return "spades"
        case .Hearts:
            return "hearts"
        case .Diamonds:
            return "diamonds"
        case .Clubs:
            return "clubs"
        // 这里可以不设置 default case
        }
    }
}
let hearts = Suit.Hearts
print(hearts.simpleDescription())   // hearts
````

> 注意：有两种方式可以引用 `Hearts` 成员：
> * 给 `hearts` 赋值时，使用了枚举成员的全名 `Suit.Hearts` 来引用，因为赋值的常量没有显示指定类型。
> * 在 `switch` 里面时，使用了枚举成员的缩写 `.Hearts` 来引用，因为 `self` 的值已经知道是一个 `Suit`。
>
> 也就是说，未知变量或常量的类型时需要使用 **全名** 来引用，而已知类型时可使用 **缩写** 来引用。

给 `Suit` 添加一个 `colorDescription()` 方法，对 `spades` 和 `clubs` 返回 “black”，对 `hearts` 和 `diamonds` 返回 “red” :

```swift
enum Suit {
    // ...
    func colorDescription() -> String {
        if self == Suit.Spades || self == Suit.Clubs {
            return "black"
        }
        return "red"
    }
}

let spades = Suit.Spades
print(spades.colorDescription())    // black

let hearts = Suit.Hearts
print(hearts.colorDescription())    // red
````

#### 结构体

使用 `sturct` 来创建一个结构体：
* 结构体和类有很多相同的地方，比如 `方法` 和 `构造器`。
* 最大区别在于，**结构体是值传递，而类是引用传递**。

```swift
struct Card {
    var rank: Rank
    var suit: Suit

    func simpleDescription() -> String {
        return "The \(rank.simpleDescription()) of \(suit.simpleDescription())"
    }
}

let threeOfSpades = Card(rank: .Three, suit: .Spades)
print(threeOfSpades.simpleDescription())    // The 3 of spades
```

// TODO 不确定下面这段的描述是否完全正确

一个枚举成员的实例可以有 **实例值** 和 **原始值**，实例的实例值和原始值是不同的：

* 相同枚举成员的实例可以有不同的实例值，创建实例时传入值即可。
* 枚举成员的原始值对于该枚举成员的所有实例都是相同的，而且是在定义枚举时设置原始值。

例如，考虑从服务器获取日出和日落的时间时，服务器会返回正常结果或错误结果：

```swift
enum ServerResponse {
    case Result(String, String)
    case Error(String)
}

let sunrise = "6:30 am"
let sunset = "8:30 pm"
let error = "Out of cheese."

let success = ServerResponse.Result(sunrise, sunset)
let failure = ServerResponse.Error(error)

var serverResponse: String
switch success {
case .Result(sunrise, sunset):
    serverResponse = "Sunrise is at \(sunrise) and sunset is at \(sunset)"
case .Error(error):
    serverResponse = "Failure...   \(error)"
default:
    serverResponse = "Default..."
}
print(serverResponse)   // Sunrise is at 6:30 am and sunset is at 8:30 pm
```


### 2.6 协议和扩展

#### 协议

使用 `protocol` 来声明一个协议：

```swift
protocol ExampleProtocol {
    var simpleDescription: String { get }
    mutating func adjust()
}
```

类、枚举和结构体都可以实现协议：

```swift
class SimpleClass: ExampleProtocol {
    var simpleDescription: String = "A very simple class."
    var anotherProperty: Int = 69105
    func adjust() {
        simpleDescription += " Now 100% adjusted."
    }
}
var a = SimpleClass()
a.adjust()
print(a.simpleDescription)  // A very simple class. Now 100% adjusted.

struct SimpleStructure: ExampleProtocol {
    var simpleDescription: String = "A simple structure."
    mutating func adjust() {
        simpleDescription += " (adjusted)"
    }
}
var b = SimpleStructure()
b.adjust()
print(b.simpleDescription)  // A simple structure. (adjusted)
```

* 声明 `SimpleStructure` 时使用了 `mutating` 关键字来标记一个会修改结构体的方法。
* 声明 `SimpleClass` 时不需要标记任何方法，因为类中的方法通常可以修改类的属性。

#### 扩展

* 可以通过 `extension` 来为现有的类型添加功能，比如新的方法和计算属性。
* 也可以使用 `extension` 在别处修改定义，甚至是从外部库或者框架引入的一个类型，使得这个类型遵循某个协议。

```swift
extension Int: ExampleProtocol {
    var simpleDescription: String {
        return "The number is \(self)"
    }

    mutating func adjust() {
        self += 42
    }
}
var number = 7
number.adjust()
print(number.simpleDescription)  // The number is 49
```

你可以像使用其它命名类型一样使用协议名，例如创建一个有不同类型但是都实现一个协议的对象集合。

```swift
let protocalValue: ExampleProtocol = a;
print(protocalValue.simpleDescription)  // A very simple class. Now 100% adjusted.

// Error: Value of type 'ExampleProtocol' has no member 'anotherProperty'
// print(protocalValue.anotherProperty)
```

即使 `protocalValue` 变量运行时的类型是 `SimpleClass`, 编译器会把它的类型当做 `ExampleProtocol`，所以你不能调用类在它实现的协议之外的方法或属性。要理解好 **运行时类型** 和 **编译时类型** 的概念。



### 2.7 泛型

在尖括号里写一个名字来创建一个泛型函数或类型。

```swift
func repeatItem<Item>(item: Item, numberOfTimes: Int) -> [Item] {
    var result = [Item]()
    for _ in 0..<numberOfTimes {
        result.append(item)
    }
    return result
}
let result = repeatItem(item: "konck", numberOfTimes: 4)
print(result)   // ["konck", "konck", "konck", "konck"]
```

你也可以创建泛型函数、方法、类、枚举和结构体。

```swift
// Reimplement the Swift standard library's optional type
enum OptionalValue<Wrapped> {
    case None
    case Some(Wrapped)
}

var possibleInteger: OptionalValue<Int> = .None
possibleInteger = .Some(100)
print(possibleInteger)      // Some(100)
```

在类型名后面使用 `where` 来指定对类型的要求，比如：
* 限定类型实现了某一个协议
* 限定两个类型是相同的
* 限定某个类必须有一个特定的父类

```swift
func anyCommonElements<T: Sequence, U: Sequence>(_ lhs: T, _ rhs: U) -> Bool
    where T.Element: Equatable, T.Element == U.Element
{
    for lhsItem in lhs {
        for rhsItem in rhs {
            if lhsItem == rhsItem {
                return true
            }
        }
    }
    return false
}
anyCommonElements([1, 2, 3], [3])
```

Writing <T: Equatable> is the same as writing <T> ... where T: Equatable.

end.
