# JavaScript 基础

> 本文主要是以 ES5 在浏览器中的使用为基础进行介绍。 

内容概要：
1. 简介
2. 数据类型和变量
3. 基本数据类型
4. 复合类型
5. 运算符
6. 语句和流程控制
7. 函数
8. 函数的参数处理
9. 使用对象
10. 创建对象

## 1. 简介

### 是什么

* JavaScript 是一种脚本语言，其源代码无需经过编译，由浏览器解释执行。
* JavaScript 是基于对象进行设计的，提供了丰富的内部对象和函数供开发人员使用。
* JavaScript 是弱类型变量语言，其变量在使用前无需声明，由解释器在运行时检查其数据类型。

### 如何运行

在 HTML 页面中嵌入 JavaScript 代码有两种方式：

* 使用 `javascript:` 前缀构建执行 JavaScript 代码的 URL。
* 使用 `<script.../>` 元素来包含 JavaScript 代码。

```html
<body>
  <a href="javascript:alert('运行 JavaScript!')">运行 JavaScript</a>
  <script>
    alert("直接运行的 JavaScript")
  </script>
</body>
```

### 如何导入

如果想将 HTML 代码与 JavaScript 脚本分离，我们可以将 JavaScript 脚本单独保存在一个 `.js` 文件中，然后在 HTML 页面中导入该 `.js` 文件即可。语法格式如下：

```js
<script src="./test.js"/>
```

## 2. 数据类型和变量

### 定义变量

JavaScript 支持两种方式来引入变量：

* 隐式定义：直接给变量赋值。
* 显式定义：使用 `var` 关键字定义变量。

隐式定义的方式简单，在需要使用的时候直接给变量赋值即可：

```html
<script>
  a = "Hello JavaScript"
  alert(a)
</script>
```

显式定义时可以没有初始值，此时变量的数据类型是不确定的。在第一次赋值后可以确定变量的数据类型，但是其数据类型在使用过程中也是可以变的：

```html
<script>
  var a
  a = true
  alert(a)
</script>
```

### 类型转换

JavaScript 支持自动类型转换，转换规律如下：

* 对于减号运算符，系统自动将字符串转换成数值。
* 对于加号运算符，系统自动将数值转换为字符串，然后将两个字符串进行连接运算。

```html
<script>
  var a = "3.145"
  var b = a - 2    // 1.145
  var c = a + 2    // 3.1452
</script>
```

自动类型转换虽然方便，但可读性很差。JavaScript 提供了几个函数来执行「强制类型转换」，如下：

* `toString()`: 将布尔值、数值等转换成字符串。
* `parseInt()`: 将布尔值、字符串等转换为整数。
* `parseFloat()`: 将布尔值、字符串等转换为浮点数。

```HTML
<script>
  var a = "3.145"
  var b = a - 2                // 1.145
  var c = parseFloat(a) + 2    // 5.145
</script>
```

注意，当使用 `parseInt()` 或 `parseFloat()` 将各种类型的变量转换为数值类型时，结果如下：
* **字符串值**：如果字符串是一个数值字符串，则可以转换为一个数值，否则将转换成 `NaN`。
* **`undefined`、`null`、布尔值及其它对象**：一律转换为 `NaN`。

```HTML
<script>
  console.log(parseInt("1.234"))          // 1
  console.log(typeof parseInt("1.234"))   // number
  console.log(typeof parseFloat("1.234")) // number

  console.log(parseInt("abc"))      // NaN        
  console.log(parseInt(undefined))  // NaN
  console.log(parseInt(null))       // NaN
  console.log(parseInt(true))       // NaN

  var a = 12;
  console.log(typeof toString(a))   // string
</script>
```

> `NaN` 是一个特殊的值，是 Not a Number 的缩写，表示「非数」。

### 变量

变量范围：

* 全局变量：直接定义的变量，可以被所有的脚本访问。
* 局部变量：在函数里定义的变量，只在函数内部有效。

注意 1：**如果全局变量和局部变量使用相同的变量名，则局部变量会覆盖全局变量。**

```HTML
<script>
  var test = "全局变量"
  function check() {
    var test = "局部变量"
    console.log(test)   // 局部变量
  }
  check()
</script>
```

注意 2：**JavaScript 的变量没有块范围。**

```HTML
<script>
  function test() {
    var i = 1
    if (typeof i == "number") {
      var j = 5
      for(var k = 0; k < 10; k++) {
        // do something
      }
    }
    // 变量 j 的作用范围是整个函数，而不是 if 块内
    // 变量 k 的作用范围是整个函数，而不是循环体内

    console.log(j)  // 5
    console.log(k)  // 10
  }
  test()
</script>
```


## 3. 基本数据类型

JavaScript 是弱类型脚本语言，变量的数据类型时解释时动态决定的，基本数据类型有如下 5 个：

* 数值类型：包含整数和浮点数。
* 布尔类型：只含 `true` 和 `false` 两个值。
* 字符串类型：字符串变量的值必须用引号（单引号/双引号）括起来。
* `undefined` 类型：表示一个已经创建但没有初始值的变量。
* `null` 类型：表示某个变量的值为空。

### 数值类型

JavaScript 中的算数运行允许除数为 0：

* 除数和被除数同时为 0，结果为 `NaN`
* 正数除以零，结果为 `Infinity` (正无穷大)
* 负数除以零，结果为 `-Infinity` (负无穷大)
* 以 `Infinity` 执行算数运算，结果都为 `NaN`
* 算数表达式中只要有一个 `NaN` 数值变量，则结果为 `NaN`

> 注意：`NaN` 不会与任何数值变量相等，也就是 `NaN == NaN` 为 `false`，JavaScript 提供了 `isNaN()` 函数来判断某个变量的值是否为 `NaN`。

JavaScript 中没有字符类型，比如说：

```HTML
<script>
  var a = 'Hello World'
  var b = "Hello World"

  console.log(typeof a)   // string
  console.log(typeof b)   // string
  console.log(a == b)     // true
  console.log(a === b)    // true
</script>
```

### 字符串类型

JavaScript 以 `String` 内建类表示字符串，`String` 类提供了以下常见的基本方法和属性操作符：

* `chartAt()`: 获取字符串特定索引处的字符。
* `toUpperCase()`: 将字符串的所有字母转换成大写的。
* `toLowerCase()`: 将字符串的所有字母转换成小写的。
* `indexOf()`: 返回字符串中特定字符串第一次出现的位置。
* `lastIndexOf()`: 返回字符串中特定字符串最后一次出现的位置。
* `substring()`: 返回字符串的某个子串。
* `slice()`: 返回字符串的某个子串，相比 `substring()` 更加强大，支持负数参数。
* `match()`: 使用正在表达式搜索目标子字符串。
* `search()`: 使用正在表达式搜索目标子字符串。
* `concat()`:将多个字符串连接拼成一个字符串。
* `split()`: 将字符串分隔成多个字符串，可以指定分隔符。
* `replace()`: 将字符串中某个子串以特定字符串替代。
* `length`: 属性，获取字符串的长度（中文字符算是一个字符）。

```HTML
<script>
  var a = "abcdefg.abcdefg.abcdefg"

  console.log(a.charAt(2))        // c
  console.log(a.toUpperCase())    // ABCDEFG.ABCDEFG.ABCDEFG
  console.log(a.toLowerCase())    // abcdefg.abcdefg.abcdefg
  console.log(a.indexOf("d"))     // 3
  console.log(a.indexOf("d", 5))  // 11 (5 表示索引时不含前五个字符)
  console.log(a.lastIndexOf("d")) // 19
  console.log(a.substring(1, 3))  // bc
  console.log(a.slice(2, -2))     // cdefg.abcdefg.abcde
  console.log(a.match(/abc/gi))   // ["abc", "abc", "abc"]
  console.log(a.search("abc"))    // 0
  console.log(a.concat("1234"))   // abcdefg.abcdefg.abcdefg1234
  console.log(a.split("."))       // ["abcdefg", "abcdefg", "abcdefg"]
  console.log(a.replace("abc", "ABC"))  // ABCdefg.abcdefg.abcdefg
  console.log(a.length)           // 23
</script>
```

> * 使用 `indexOf()` 或 `lastIndexOf()` 函数时，如果该字符串没有包含目标字符串，则返回 `-1`。
> * 使用 `slice()` 时，负数索引值表示从字符串的最右边开始计算索引，最右边的索引为 `-1`。


### undefined 和 null

`undefined` 和 `null` 的差别比较微妙：

* `undefined`: 表示没有为变量设置值或属性不存在。
* `null`: 表示变量是有值得，只是值为 `null`。

> `undefined == null` 为 `true`, `undefined === null` 为 `false`。


## 4. 复合类型

JavaScript 中的复合类型大致有如下 3 种：

* Objecct: 对象
* Array: 数组
* Function: 函数

### 对象

JavaScript 是基于对象的脚本语言，提供了以下常见的内置类：

* `Array`: 数组类
* `Date`: 日期类
* `Error`: 错误类
* `Function`: 函数类
* `Math`: 数学类
* `Number`: 数值类
* `Objecct`: 对象类
* `String`: 字符串类

### 数组

定义一个数组有以下 3 种语法：

* `var a = [1, true, "a"]`
* `var b = []`
* `var c = new Array()`

JavaScript 作为动态、弱类型的语言，其数组有着以下 3 个特征：

* 数组长度可变。
* 同一个数组的元素类型可互不相同。
* 访问数组时不会产生数组越界，访问并未赋值的数组元素时其值为 `undefined`。

### 函数

函数定义的语法格式如下：

```js
function functionName(param1, param2, ...) {
  // ...
}
```

JavaScript 函数有着以下 4 个特点：

* 无需声明返回值类型
* 无需声明形参类型
* 可以独立存在，不属于任何类
* 必须使用 `function` 关键字定义


## 5. 运算符

### 比较运算符

JavaScript 的比较运算符有点特殊，比如说：

* `==`: 等于，如果前后两个变量的值相等则返回 `true`。
* `!=`: 不等于，如果前后两个变量的值不相等则返回 `true`。
* `===`: 严格等于，必须前后两个变量的值相等且数据类型相同，才返回 `true`。
* `!==`: 严格不等于，值不同或类型不同，都会返回 `true`。

> 上述四者的主要区别在于：是否支持自动类型转换。

举个例子：

```js
<script>
  console.log(5 == '5')   // true
  console.log(5 === '5')  // false
</script>
```

比较运算符不仅可以在数值之间比较，也可以在字符串之间比较（按 Unicode 值进行比较）：

```js
<script>
  console.log('a' > 'b')      // false
  console.log('a' > 'abc')    // false
  console.log('ABB' > 'ABC')  // false
</script>
```

### void 运算符

`void` 运算符用于强行指定表达式不会返回值：

```js
<script>
  var a = 2
  var b = 3
  var c = void(a + b)
  console.log(c)  // undefined
</script>
```

### typeof 和 instanceof 运算符

`typeof` 运算符用于判断某个变量的数据类型，不同类型参数的返回值如下：

* `undefined` 值：undefined
* `null` 值：object
* 布尔类型：boolean
* 数值类型：number
* 字符串类型：string
* 对象：object
* 函数：function

`instanceof` 运算符用于判断某个变量是否为指定类的实例，如果是的话返回 `true` 否则返回 `false`:

```js
<script>
  var a = [2, 3]
  console.log(a instanceof Array)   // true
  console.log(a instanceof Object)  // true
</script>
```

JavaScript 中所有的类都是 `Object` 的子类，所以两个都为 `true`。

## 6. 语句和流程控制

### 异常

JavaScript 支持异常处理，支持手动抛出异常，所有的异常都是 `Error` 对象。

抛出异常对象的语法如下：

```js
throw new Error(errorString)
```

捕捉异常的语法如下：

```js
try {
  // ...
} catch(e) {
  // ...
} finally {
  // ...
}
```

示例：

```js
<script>
  try {
    for(var i = 0; i< 10; i ++) {
      console.log(i)
      if (i > 2) {
        throw new Error("i is begger than 2")
      }
    }
  } catch(e) {
    console.log("错误信息：" + e.message)
  } finally {
    console.log("End")
  }
  console.log("out of try")
</script>
```

控制台信息：

```
1
2
3
错误信息：i is begger than 2
out of try
```

在代码运行过程中，一旦遇到异常，立即寻找对应的异常捕捉 (`catch`) 块。如果没有进行捕捉的话，异常将传播给浏览器，程序非正常终止。

异常类有且只要一个，所以 `try` 后面最多只能有一个 `catch` 块。


### for in 循环

`for in` 循环主要有两个作用：

* 遍历数组里的所有元素。
* 遍历 JavaScript 对象的所有属性。

举个例子，遍历数组：

```js
<script>
  var a = ['hello', 'javasctipt', 'haha']
  for (index in a) {
    console.log('索引 ' + index + ' 的值是：' + a[index])
  }
</script>

// 索引 0 的值是：hello
// 索引 1 的值是：javasctipt
// 索引 2 的值是：haha
```

再举个例子，遍历对象的所有属性：

```js
<script>
  for (propName in navigator) {
    console.log('属性 ' + propName + ' 的值是：' + navigator[propName])
  }
</script>

// 属性 vendorSub 的值是：
// 属性 productSub 的值是：20030107
// 属性 vendor 的值是：Google Inc.
// 属性 maxTouchPoints 的值是：0
// 属性 hardwareConcurrency 的值是：8
// 属性 cookieEnabled 的值是：true
// 属性 appCodeName 的值是：Mozilla
// 属性 appName 的值是：Netscape
// ...

```

## 7. 函数

JavaScript 代码复用的单位是函数，函数是「一等公民」，可以独立存在。函数本身也是一个对象，实例是 `Function`。

### 定义函数

定义函数的方式有 3 种：

* 定义命名函数
```js
<script>
  hello()
  function hello() {
    console.log("Hello friends.")
  }
</script>
```

* 定义匿名函数
```js
<script>
  var f = function(name) {
    console.log(name)
  }
  f("zhuanghongji")
</script>
```

* 使用 Function 类定义函数
```js
<script>
  var f = new Function('name', 'console.log(name)')
  f('Mr.zhuang')
</script>
```

由上可知：

* 在同一个 `<script../>` 元素中，JavaScript 允许先调用函数，然后再定义该函数。
* 匿名函数实际上是定义了一个函数对象，然后将这个对象赋值给一个变量，可读性好。
* `Function` 类的构造器参数个数不受限制，最后一个字符串参数是函数的执行体，执行体的各个语句以分号隔开。当函数体的语句很多时，代码臃肿，可读性差。


另外，在 JavaScript 中，函数是可以定义在函数内部的，即「局部函数」。来看个示例：

```js
<script>
  function outer() {
    function inner1() {
      console.log("调用局部函数 inner1")
    }
    function inner2() {
      console.log("调用局部函数 inner2")
    }

    inner1()
    inner2()
    console.log("调用全局函数")
  }
  outer()
</script>

// 调用局部函数 inner1
// 调用局部函数 inner2
// 调用全局函数
```

### 函数、方法、对象和类

当使用 JavaScript 定义一个函数后，实际上得到了以下 4 项东西：

* 函数：可以被调用。
* 对象：系统会创建一个对象，该对象是 `Function` 的实例。
* 方法：该函数通常会附加给某个对象，作为该对象的方法。
* 类：得到了一个与函数同名的类。

不信的话，看个例子：

```js
<script>
  var f = function(name) {
    return "your name is" + name
  }
  console.log(f instanceof Function)  // true
  console.log(f instanceof Object)    // true
</script>
```

当我们定义一个函数后，通常有两种方式进行调用：

* 直接调用函数，如果函数包含 `return` 语句的话就会返回对应的值。
* 使用 `new` 关键字调用，总有一个 JavaScript 对象的返回值。

```js
<script>
  var f = function(name) {
    return "your name is " + name
  }

  var a = f("John")
  var b = new f("Micheal")
  console.log(a)  // your name is John
  console.log(b)  // f {}
</script>
```

下面的代码片段定义了一个 `Person` 对象，该 `Person` 函数也会作为 `Person` 类唯一的构造器：

```js
<script>
  function Person(name, age) {
    Person.country = "China"  // 类属性
    this.name = name          // 实例属性
    this.age = age
    this.info = function() {
      return "my name is " + name + " and age is " + age
    }
  }
  var p = new Person("zhuanghongji", 18)
  console.log(Person.country) // China
  console.log(p.country)      // undefined
  console.log(p.name)   // zhuanghongji
  console.log(p.age)    // 18
  console.log(p.info()) // my name is zhuanghongji and age is 18
</script>
```

实际上，我们也可以通过 `call()` 和 `apply()` 方法来调用函数：

```js
<script>
  var f = function(num1, num2) {
    console.log("num1 = " + num1 + ", num2 = " + num2)
  }

  // call  函数引用.call(调用者, 参数1, 参数2...)
  f.call(null, 1, 2)            // num1 = 2, num2 = 3
  f.call(null, [1, 2])          // num1 = 1,2, num2 = undefined

  // apply
  // f.apply(null, 4)  Uncaught TypeError: CreateListFromArrayLike called on non-object
  f.apply(null, [4, 5])         // num1 = 4, num2 = 5
  var ap = function(n1, n2) {
    f.apply(this, arguments)
  }
  ap(7, 8)                      // num1 = 7, num2 = 8
</script>
```

`call()` 和 `apply()` 方法都可以动态的调用函数，区别在于：

* 通过 `call()` 调用函数时，必须在括号中详细地列出每个参数。
* 通过 `apply()` 调用函数时，可以在括号中以 `Array` 或 `arguments` 来代表所有参数。


## 8. 函数的参数处理

### 值传递

在 JavaScript 中，不管是「基本类型」还是「复合类型」，调用函数时都是采用值传递。也就是说，传入函数里的不是参数本身，而是参数的副本。因此，在函数中修改参数值并不会对参数本身造成任何影响。

> 要注意的是，当参数是复合类型时，所谓的副本是指变量的副本。

看两个示例：

* 基本类型
```js
<script>
  // 基本类型
  function change(arg) {
    arg = 10
    console.log("函数内部 arg = " + arg)  
  }
  var arg = 2
  change(arg)
  console.log("函数外部 arg = " + arg)    
  // 函数内部 arg = 10
  // 函数外部 arg = 2
</script>
```

* 复合类型
```js
<script>
  function changePersonSex(person) {
    person.sex = "woman"
    console.log("函数内部 person.sex = " + person.sex)
    person = null
    console.log("函数内部 person = " + person)
  }
  var person = {sex: "man"}
  changePersonSex(person)
  console.log("函数外部 person.sex = " + person.sex)
  console.log("函数外部 person = " + person)
  // 函数内部 person.sex = woman
  // 函数内部 person = null
  // 函数外部 person.sex = woman
  // 函数外部 person = [object Object]
</script>
```

在上述示例可以看出：
* 参数 `arg` 副本的值被修改了，`arg` 本身没变。
* 参数 `person` 副本的值被赋为 `null` 了，但是 `person` 本身还是 `object` 对象。因为 `person` 副本的值指向的对象跟 `person` 本身指向的对象是同一个，所以 `sex` 值是发生变化了的。


### 空参数

先来看段代码：

```js
<script>
  var f = function test(p1, p2) {
    console.log("p1 = " + p1 + ", p2 = " + p2)
  }
  f()           // p1 = undefined, p2 = undefined
  f(1)          // p1 = 1, p2 = undefined
  f(1, 2)       // p1 = 1, p2 = 2
  f(1, 2, 3)    // p1 = 1, p2 = 2
</script>
```

上面的代码是能够正常运行的，JavaScript 会将没有传入实参的参数值自动设置为 `undefined`，传入多个参数也没有关系。

另外，在 JavaScript 中是没有「方法重载」的概念的，函数名是函数的唯一标识。如果你先后定义了两个同名但参数列表不同的的函数，也不是重载，因为后者会覆盖前者。


## 9. 使用对象

* JavaScript 并不是面向对象的程序设计语言，而是基于对象的脚本语言。
* JavaScript 中的每个函数都可以都可用于创建对象，返回的对象既是该类的实例，也是 `Object` 类的实例。
* JavaScript 中的函数定义并不支持继承语法，没有完善的继承机制。
* JavaScript 不允许开发者指定类与类之间的继承关系，但所有的类都是 `Object` 的子类。

JavaScript 中的对象本质上是一个关联数组，由 `key - value` 对组成。特别地，JavaScript 对象的 value 不仅可以是指，也可以是方法。

### prototype

JavaScript 是一种动态语言，允许自由的为对象增加属性和方法。当程序为对象的某个不存在的属性赋值时，即可认为是为该对象增加属性。

JavaScript 的所有类（即函数）都有一个 `prototype` 属性，当我们为 JavaScript 类的 `prototype` 属性增加属性、函数时，可视为对原有类的扩展。

> 可以这样理解：增加了 `prototype` 属相的类继承了原有的类，这就是 JavaScript 所提供的「伪继承机制」。

示例代码：

```js
<script>
  function Person(name, age) {
    this.name = name
    this.age = age
    this.info = function() {
      return 'name is ' + this.name + ', age is ' + this.age
    }
  }

  var p1 = new Person("zhangsan", 21)
  console.log("p1: " + p1.info())

  Person.prototype.walk = function() {
    console.log(this.name + " is walking...")
  }
  p1.walk()  // 此时 p1 才具有了 walk() 方法

  var p2 = new Person("lisi", 22)
  console.log("p2: " + p2.info())
  p2.walk()
</script>


// p1: name is zhangsan, age is 21
// zhangsan is walking...
// p2: name is lisi, age is 22
// lisi is walking...
```

另外，通过 `prototype` 也可以为 JavaScript 的内建类进行扩展，比如下面的代码演示了如何为 `Array` 类增加了一个 `indexOf()` 方法：

```js
<script>
  Array.prototype.indexOf = function(obj) {
    var result = -1
    for (var i = 0; i < this.length; i++) {
      if (this[i] === obj) {
        result = i
        break
      }
    }
    return result
  }

  var arr = [4, 5, '4', 'a']
  console.log(arr.indexOf(4))   // 0
  console.log(arr.indexOf('4')) // 2
</script>
```

虽然可以在任何时候为一个类增加属性和方法，但通常建议在类定义结束后立即增加该类所需的方法，以避免造成不必要的混乱。


## 10. 创建对象

创建对象并不需要先创建类，大致有以下 3 种方式：

* 使用 `new` 关键字调用构造器创建对象。
* 使用 `Object` 直接创建对象。
* 使用 `JSON` 语法创建对象。

示例代码前面基本都有了，不再列举。

end.