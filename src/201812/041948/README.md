# 了解 JavaScript 中的 Number、Array、Date 和 Boolean

快捷方式：[Number](#number) - [Array](#array) - [Date](#date) - [Boolean](#boolean)

## Number

在 JavaScript 只有一种数字类型 `number`，并没有整型和浮点型之分，且所有的数字都存储为 "根为 10 的 64 位 (8 比特)" 的浮点数。

注意在 JavaScript 中浮点运算并不总是 100% 正确的。比如：

```js
console.log(0.1 + 0.2 === 0.3);  // false
```


### 属性

`MAX_VALUE`: 可表示的最大的数。

`MIN_VALUE`: 可表示的最小的数 (接近 0 但大于 0)。

`POSITIVE_INFINITY`: 表示正无穷，比 `Number.MAX_VALUE` 还要大。

`NEGATIVE_INFINITY`: 表示负无穷，比最小负数 (`-Number.MAX_VALUE`) 还小的数。


> 注：任何数乘以无穷大结果仍为无穷大，任何数被无穷大除的结果为 0。


示例：

```js
console.log(Number.MAX_VALUE)           // 1.7976931348623157e+308
console.log(Number.MIN_VALUE)           // 5e-324
console.log(Number.POSITIVE_INFINITY)   // Infinity
console.log(Number.NEGATIVE_INFINITY)   // -Infinity

if (Number.MIN_VALUE > 0) {
  console.log('Number.MIN_VALUE bigger than 0')
}
// Number.MIN_VALUE bigger than 0

var x = Number.MAX_VALUE * 2
if (x == Number.POSITIVE_INFINITY) {
  console.log('Value of x is ' + x)
}
// Value of x is Infinity

var y = (-Number.MAX_VALUE) * 2
if (y == Number.NEGATIVE_INFINITY) {
  console.log('Value of y is ' + y)
}
// Value of y is -Infinity


var i = 999 / Number.POSITIVE_INFINITY
var j = 9999 / Number.NEGATIVE_INFINITY
console.log(i)      // 0
console.log(j)      // -0
console.log(i == j) // true
```


### 方法

**`toString()`**

把数字转换为字符串并返回结果，可使用指定的基数。

语法：

```js
NumberObject.toString(radix)
```

参数：

* `radix`: 可选，指定数字的基数，范围是在 2 ~36 之间的整数。如果忽略该参数，则使用基数 10。

返回值：

* 数字的字符串表示。
* 比如当 `radix` 为 2 时，该数字就会被转换为二进制值表示的字符串。

示例：

```js
var x = 256
console.log(x.toString())     // 256
console.log(x.toString(2))    // 100000000
console.log(x.toString(8))    // 400
console.log(x.toString(16))   // 100

var x = 256.00
console.log(x.toString())     // 256
console.log(x.toString(2))    // 100000000
console.log(x.toString(8))    // 400
console.log(x.toString(16))   // 100

var x = 256.12
console.log(x.toString())     // 256.12
console.log(x.toString(2))    // 100000000.0001111010111000010100011110101110000101001
console.log(x.toString(8))    // 400.075341217270244
console.log(x.toString(16))   // 100.1eb851eb852
```

.

**`toFixed()`**

把 Number 四舍五入为指定小数位数的数字。

语法：

```js
NumberObject.toFixed(num)
```

参数：

* `num`: 必需，指定最小的位数，是 0 ~20 之间的值。如果省略，将用 0 代替。

返回值：

* 返回 NumberObject 的字符串表示，不采用指数计数法，小数点后有固定的 num 位数字。
* 如果必要，该数字会被舍入，也可以用 0 补足，以便它达到指定的长度。

示例：

```js
var x = 12
var y = 12.00
var z = 12.34
var i = 12.35
var j = 12.351
var k = 12.36

console.log(x.toFixed(1))   // 12.0
console.log(y.toFixed(1))   // 12.0
console.log(z.toFixed(1))   // 12.3
console.log(i.toFixed(1))   // 12.3 (可以理解为 "小于或等于一半" 就舍)
console.log(j.toFixed(1))   // 12.4 (可以理解为 "大于一半" 就进)
console.log(k.toFixed(1))   // 12.4
```

.

**`toExponential()`**

把对象的值转换为指数计数法。

语法：

```js
NumberObject.toExponential(num)
```

参数：

* `num`: 必需。规定指数计数法中的小数位数，是 0 ~ 20 之间的值。如果省略该参数，将使用尽可能多的数字。

返回值：

* 返回 NumberObject 的字符串表示，采用指数计数法，即小数点之前有一位数字，小数点之后有 num 位数字。
* 该数字的小数部分将被舍入，必要时用 0 补足，以便它达到指定的长度。

示例：

```js
var x = 12345

console.log(x.toExponential(0))    // 1e+4
console.log(x.toExponential(1))    // 1.2e+4
console.log(x.toExponential(2))    // 1.23e+4
console.log(x.toExponential(3))    // 1.235e+4
console.log(x.toExponential(4))    // 1.2345e+4
console.log(x.toExponential(5))    // 1.23450e+4
console.log(x.toExponential(6))    // 1.234500e+4
```

.

**`toPrecision()`**

可在对象的值超出指定位数时将其转换为指数计数法。

语法：

```js
NumberObject.toPrecision(num)
```

参数：

* `num`: 必需。规定必须被转换为指数计数法的最小位数。
  * 该参数是 1 ~ 21 之间（且包括 1 和 21）的值。
  * 如果省略了该参数，则调用方法 toString()，而不是把数字转换成十进制的值。


返回值：

* 返回 NumberObject 的字符串表示，包含 num 个有效数字。
  * 如果 num 足够大，能够包括 NumberObject 整数部分的所有数字，那么返回的字符串将采用定点计数法。 
  * 否则，采用指数计数法，即小数点前有一位数字，小数点后有 num-1 位数字。
  * 必要时，该数字会被舍入或用 0 补足。

示例：

```js
var x = 12345

console.log(x.toPrecision())     // 12345
// console.log(x.toPrecision(0))    
console.log(x.toPrecision(1))    // 1.e+4
console.log(x.toPrecision(2))    // 1.2e+4
console.log(x.toPrecision(3))    // 1.23e+4
console.log(x.toPrecision(4))    // 1.235e+4
console.log(x.toPrecision(5))    // 12345
console.log(x.toPrecision(6))    // 12345.0
```


## Array

`Array` 对象用于在单个的变量中存储多个值。

创建：

```js
new Array();
new Array(size);
new Array(element0, element1, ..., elementn);
```

参数：

* `size`: 期望的数组元素个数，同时 `size` 的值会作为返回数组的 `length` 属性的值。
* `element0, element1, ..., elementn`: 参数列表，新创建数组的元素会被初始化成这些值，同时参数列表的个数会作为返回数组的 `length` 属性的值。

返回值：

* 返回新创建并初始化好的数组。
  * 如果没有使用参数，返回的是空数组，`length` 属性值为 `0`。
  * 如果传递了一个 **数字参数**，返回的是具有指定个数且元素都为 `undefined` 的数组。
  * 如果传递了其它的参数，返回的是具有参数指定的值的数组。

> 注：不使用 new 运算符时，行为与使用 new 运算符调用它时的行为完全一样。

示例：

```js
var arr1 = new Array()
console.log(arr1)             // []
console.log(arr1[0])          // undefined
console.log(arr1.length)      // 0

var arr2 = new Array(1)
console.log(arr2)             // [undefined]
console.log(arr2[0])          // undefined
console.log(arr2.length)      // 1

var arr3 = new Array(1, 2)
console.log(arr3)             // [1, 2]
console.log(arr3[0])          // 1
console.log(arr3.length)      // 2

var arr4 = [1, 2]
console.log(arr4)             // [1, 2]
console.log(arr4[0])          // 1
console.log(arr4.length)      // 2
```

> 注：如需修改已有数组中的值，只要向指定下标号并设置一个新值即可，比如 `arr[2] = 'ABC'`


### 属性

**`length`**

设置或返回数组中元素的数目。

设置属性的值可改变数组的大小：
* 如果设置的值比其当前值小，数组就会被截断，其尾部的元素会丢失。
* 如果设置的值比其当前值大，数组就会增大，新的值为 `undefined` 的元素会添加到数组的尾部。

```js
var arr = [1, 2, "3"]
console.log(arr.length) // 3

arr.length = 5
console.log(arr.length) // 5

arr.push("A")
console.log(arr.length) // 6

console.log(arr)        // [1, 2, '3', undefined, undefined, 'A']
```


### 方法

**`concat()`**

连接两个或更多的数组，并返回结果。

该方法不会改变现有的数组，仅仅是返回被连接数组的一个副本。

语法：

```js
arrayObject.concat(arrayX, arrayX, ..., arrayX)
```

参数：

* 必须，可以是具体的值，也可以是数组。可以任意多个。

返回值：

* 返回一个新的数组。如果要进行 `concat` 操作的参数是数组，那么添加的是数组中的元素，而不是数组。

示例：

```js
var a = [1, 2, 3]
console.log(a.concat(4, 5))       // [ 1, 2, 3, 4, 5 ]

var b = ['A', 'B', 'C']           
var c = ['D', 'E', 'F']
console.log(b.concat(c))          // [ 'A', 'B', 'C', 'D', 'E', 'F' ]

var d = ['G', 'H']
console.log(b.concat(c, d))       // [ 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H' ]

console.log(b.concat(c, d, 123))  // [ 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 123 ]
```

.

**`join()`**

把数组的所有元素放入一个字符串。元素通过指定的分隔符进行分隔。

语法：

```js
arrayObject.join(separator)
```

参数：

* 可选，指定要使用的分隔符。如果该参数缺省，则使用逗号作为分隔符。

返回值：

* 返回一个字符串，在相邻的两个元素之间插入 separator 字符串而生成。

示例：

```js
var a = [1, 2, 3]
console.log(a.join())         // 1,2,3

var b = ['A', 'B', 'C']     
console.log(b.join(''))       // ABC    

var c = ['D', 'E', 'F', 123]
console.log(c.join('-'))      // D-E-F-123
```

.

**`pop()`**

删除并返回数组的最后一个元素，数组长度减 1。

如果数组已经为空，则 `pop()` 不改变数组，并返回 `undefined` 值。

语法：

```js
arrayObject.pop()
```

返回值：

* arrayObject 的最后一个元素。

示例：

```js
var a = ['A', 'B', 'C']     
console.log(a.pop(''))    // C
console.log(a)            // [ 'A', 'B' ]

var b = []
console.log(b.pop())      // undefined
console.log(b)            // []
```

.

**`push()`**

向数组的末尾添加一个或更多元素，并返回新的长度。

它直接修改原数组，而不是创建一个新的。

语法：

```js
arrayObject.push(newelement1, newelement2, ..., newelementX)
```

参数：

* `newelement1`: 必需，要添加到数组的第一个元素。
* `newelement2`: 可选，要添加到数组的第二个元素。
* `newelementX`: 可选，可添加多个元素。

返回值：

* 把指定的值添加到数组后的新长度。

示例：

```js
var a = ['A', 'B', 'C']     
console.log(a.push('D'))        // 4
console.log(a.push('E', 'F'))   // 6
console.log(a)                  // [ 'A', 'B', 'C', 'D', 'E', 'F' ]
```

.

**`reverse()`**

颠倒数组中元素的顺序。

该方法会改变原来的数组，不会创建新的。

语法：

```js
arrayObject.reverse()
```

示例：

```js
var a = ['A', 'B', 'C']     
console.log(a.reverse())  // [ 'C', 'B', 'A' ]     
console.log(a)            // [ 'C', 'B', 'A' ]
```

.


**`shift()`**

删除并返回数组的第一个元素，会改变该数组的长度。

语法：

```js
arrayObject.shift()
```

返回值：

* 数组原来的第一个元素的值。

示例：

```js
var a = ['A', 'B', 'C']     
console.log(a.shift())  // A
console.log(a)          // [ 'B', 'C' ]
```

.

**`slice()`**

从某个已有的数组返回选定的元素。

该方法不会修改原数组，而是返回一个新的子数组。

语法：

```js
arrayObject.slice(start,end)
```

参数：

* `start`: 必需，指定从何处开始选取。
  * 如果是负数，则指定从数组尾部开始算起的位置。比如说，-1 指的是最后一个元素，-2 指的是倒数第二个元素，以此类推。
* `end`: 可选，指定从何处结束选取。
  * 如果没有指定该参数，那么切分的数组包含从 `start` 位置到数组结束的所有元素。
  * 如果这个参数是负数，则它指定的是从数组尾部开始算起的元素。

返回值：

* 返回一个新的数组，包含从 `start` 到 `end` (不包括该元素) 的 arrayObject 中的元素。

示例：

```js
//        0    1    2    3    4
//       -5   -4   -3   -2   -1
var a = ['A', 'B', 'C', 'D', 'E']     
console.log(a.slice(1))        // [ 'B', 'C', 'D', 'E' ]
console.log(a.slice(1, 1))     // []
console.log(a.slice(1, 3))     // [ 'B', 'C' ]
console.log(a.slice(-1, 3))    // []
console.log(a.slice(-4, 3))    // [ 'B', 'C' ]

console.log(a.slice(-4, -2))   // [ 'B', 'C' ]
```

.

**`sort()`**

对数组的元素进行排序，是在原数组上进行排序，不生成副本。
* 如果调用该方法时没有使用参数，则按照字符编码的顺序进行排序。
* 如果要按照其它顺序标准进行排序，那么就需要提供 **比较函数**。

语法：

```js
arrayObject.sort(sortby)

sortby = function(left, right) {
  // return ...
}
```

参数：

* `sortby`: 可选，指定排序顺序的函数。

返回值：

* 对数组的引用。

示例：

```js
var a = ['D', 'B', 'E', 'A', 'C']
var b = [4, 2, 3, 0, 1]

console.log(a.sort())   // [ 'A', 'B', 'C', 'D', 'E' ]
console.log(b.sort())   // [ 0, 1, 2, 3, 4 ]

function compareString(leftStr, rightStr) {
  return rightStr > leftStr
}
function compareNumber(leftNum, rightNum) {
  return rightNum - leftNum
}
console.log(a.sort(compareString))    // [ 'E', 'D', 'C', 'B', 'A' ]
console.log(b.sort(compareNumber))    // [ 4, 3, 2, 1, 0 ]
```

.

**`splice()`**

删除元素，并向数组添加新元素。

> 从字母上看 `splice` 和前面提到的 `slice` 很像，下面是词典对这两个单词的翻译：
> * `splice`: 拼接，结合；接合处，连接的绳。
> * `slice`: 薄片，菜刀；切开，割破。

语法：

```js
arrayObject.splice(index, howmany, item1, ..., itemX)
```

> 注：`splice()` 方法可删除从 index 处开始的零个或多个元素，并且用参数列表中声明的零个或多个值来替换那些被删除的元素。如果从 arrayObject 中删除了元素，则返回的是含有被删除元素的新数组。

参数：

* `index`: 必须，整数，指定 *添加/删除* 元素的位置，使用负数可从数组结尾处指定位置。
* `howmany`: 必须，指定要 *删除* 的项目数量。如果设置为 0，则不会删除元素。
* `item1, ..., itemX`: 可选，指定要像数组中添加的新元素。

返回值：

* 包含被删除项目的新数组，如果有的话。

示例：

```js
// 创建一个数组，并在 index 2 的位置添加一个元素 (`1`)
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G']
var result = arr.splice(2, 0, '1')
console.log(result)  // []
console.log(arr)     // [ 'A', 'B', '1', 'C', 'D', 'E', 'F', 'G' ]


// 删除位于 index 2 位置的元素 (`C`)，并在该位置添加一个新元素 (`1`)：
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G']
var result = arr.splice(2, 1, '1')
console.log(result)  // [ 'C' ]
console.log(arr)     // [ 'A', 'B', '1', 'D', 'E', 'F', 'G' ]


// 删除从 index 2 开始的三个元素 (`C` `D` `E`)，并在该位置添加两个新元素 (`@` `#`)：
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G']
var result = arr.splice(2, 3, '@', '#')
console.log(result)  // [ 'C', 'D', 'E' ]
console.log(arr)     // [ 'A', 'B', '@', '#', 'F', 'G' ]
```

另外，试下添加零个元素：
```js
var arr = ['A', 'B', 'C', 'D', 'E', 'F', 'G']
var result = arr.splice(2, 3)
console.log(result)  // [ 'C', 'D', 'E' ]
console.log(arr)     // [ 'A', 'B', 'F', 'G' ]
```

.

**`toString()`**

把数组转换为字符串，并返回结果，数组中的元素之间用逗号分隔。

语法：

```js
arrayObject.toString()
```

返回值：

* arrayObject 的字符串表示。
* 返回值与没有参数的 `join()` 方法返回的字符串相同。

示例：

```js
var arr = [123, 'ABC', '朋友你好']
console.log(arr.toString())        // 123,ABC,朋友你好
```

> 当数组用于字符串环境时，JavaScript 会调用这一方法将数组自动转换成字符串。但是在某些情况下，需要显式地调用该方法。

.

**`unshift()`**

向数组的开头添加一个或更多元素，并返回新的长度。

语法：

```js
arrayObject.unshift(newelement1, newelement2, ..., newelementX)
```

参数：

* `newelement1`: 必需，向数组添加的第一个元素。
* `newelement2`: 可选，向数组添加的第二个元素。
* `newelementX`: 可选，可添加若干个元素。

> `unshift()` 方法将把它的参数插入 arrayObject 的头部，并将已经存在的元素顺次地移到较高的下标处，以便留出空间。该方法的第一个参数将成为数组的新元素 0，如果还有第二个参数，它将成为新的元素 1，以此类推。

> 注意：`unshift()` 方法不创建新的数组，而是直接修改原有的数组。

返回值：

* arrayObject 的新长度。

示例：

```js
var arr = ['A', 'B', 'C']
var newLength = arr.unshift('1', '2')
console.log(newLength)  // 5
console.log(arr)        // [ '1', '2', 'A', 'B', 'C' ]
```


## Date

`Date` 对象可用于处理日期和事件。

先来看一组示例：

```js
// 获得当日的日期
var d1 = Date()
console.log(d1)               // Sat Sep 29 2018 10:47:48 GMT+0800 (CST)

// 获取从 1970 年 1 月 1 日至今的毫秒数
var d2 = new Date()   
var time = d2.getTime()       
console.log(d2)               // 2018-09-29T02:47:48.373Z
console.log(time)             // 1538189268373

// 设置具体的日期：
var d3 = new Date()
d3.setFullYear(2008, 1, 1)    // 2008-02-01T02:47:48.374Z
console.log(d3)

// 将当日的日期（根据 UTC）转换为字符串
var d4 = new Date()
console.log(d4.toUTCString()) // Sat, 29 Sep 2018 02:47:48 GMT

// 获取今天是星期几
var d5 = new Date()
var arr = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六']
var day = d5.getDay()
var desc = arr[day]
console.log(day)              // 6
console.log(desc)             // 星期六
```

补充说明：

* `Date` 对象自动使用当前的日期和时间作为其初始值。
* 设置特定日期时，月份的参数是介于 0 到 11 之间的。也就是说，如果你希望把月份设置为 8 月，则参数应该为 7。
* 增加天数会改变月份或者年份，`Date` 对象会自动完成这种转换。

试着将日期对象设置为 5 天后的日期：

```js
var d = new Date()
console.log(d)              // 2018-09-29T02:55:11.836Z
d.setDate(d.getDate() + 5)
console.log(d)              // 2018-10-04T02:55:11.836Z
```

另外，日期对象是可以进行比较的：

```js
var someDate = new Date()
someDate.setFullYear(2008, 08, 09)

var today = new Date()
if (someDate > today) {
  console.log('Today is before 9th August 2008')
} else {
  console.log("Today is after 9th August 2008");
}

// Today is after 9th August 2008
```


### 方法

| 方法 | 描述
| -- | --
| `Date()` | 返回当日的日期和时间。
| `getDate()` | 从 Date 对象返回一个月中的某一天 (1 ~ 31)。
| `getDay()` | 从 Date 对象返回一周中的某一天 (0 ~ 6)。
| `getMonth()` | 从 Date 对象返回月份 (0 ~ 11)。
| `getFullYear()` | 从 Date 对象以四位数字返回年份。
| `getHours()` | 返回 Date 对象的小时 (0 ~ 23)。
| `getMinutes()` | 返回 Date 对象的分钟 (0 ~ 59)。
| `getSeconds()` | 返回 Date 对象的秒数 (0 ~ 59)。
| `getMilliseconds()` | 返回 Date 对象的毫秒(0 ~ 999)。
| `getTime()` | 	返回 1970 年 1 月 1 日至今的毫秒数。
| `getTimezoneOffset()` | 返回本地时间与格林威治标准时间 (GMT) 的分钟差
| `getUTCDate()` | 根据世界时从 Date 对象返回月中的一天 (1 ~ 31)。
| `getUTCDay()` | 根据世界时从 Date 对象返回周中的一天 (0 ~ 6)。
| `getUTCMonth()` | 根据世界时从 Date 对象返回月份 (0 ~ 11)。
| `getUTCFullYear()` | 根据世界时从 Date 对象返回四位数的年份。
| `getUTCHours()` | 根据世界时返回 Date 对象的小时 (0 ~ 23)。
| `getUTCMinutes()` | 根据世界时返回 Date 对象的分钟 (0 ~ 59)。
| `getUTCSeconds()` | 根据世界时返回 Date 对象的秒钟 (0 ~ 59)。
| `getUTCMilliseconds()` | 根据世界时返回 Date 对象的毫秒(0 ~ 999)。
| `setXxx()` ... | 对应上面的一系列 `getXxxx()` ...
| `toString()` | 	把 Date 对象转换为字符串。
| `toTimeString()` | 把 Date 对象的时间部分转换为字符串。
| `toDateString()` | 把 Date 对象的日期部分转换为字符串。
| `toUTCString()` | 根据世界时，把 Date 对象转换为字符串。
| `toLocaleString()` | 根据本地时间格式，把 Date 对象转换为字符串。
| `toLocaleTimeString()` | 根据本地时间格式，把 Date 对象的时间部分转换为字符串。
| `toLocaleDateString()` | 根据本地时间格式，把 Date 对象的日期部分转换为字符串。

补充示例：

```js
var date = new Date()

console.log(date.getDate())              // 29
console.log(date.getDay())               // 6
console.log(date.getMonth())             // 8
console.log(date.getFullYear())          // 2018
console.log(date.getHours())             // 11
console.log(date.getMinutes())           // 17
console.log(date.getSeconds())           // 41
console.log(date.getMilliseconds())      // 942
console.log(date.getTime())              // 1538191061942
console.log(date.getTimezoneOffset())    // -480

console.log(date.getUTCDate())           // 29
console.log(date.getUTCDay())            // 6
console.log(date.getUTCMonth())          // 8
console.log(date.getUTCFullYear())       // 2018
console.log(date.getUTCHours())          // 3
console.log(date.getUTCMinutes())        // 17
console.log(date.getUTCSeconds())        // 41
console.log(date.getUTCMilliseconds())   // 942

console.log(date.toString())             // Sat Sep 29 2018 11:17:41 GMT+0800 (CST)
console.log(date.toTimeString())         // 11:17:41 GMT+0800 (CST)
console.log(date.toDateString())         // Sat Sep 29 2018
console.log(date.toUTCString())          // Sat, 29 Sep 2018 03:17:41 GMT
console.log(date.toLocaleString())       // 2018-9-29 11:17:41
console.log(date.toLocaleTimeString())   // 11:17:41
console.log(date.toLocaleDateString())   // 2018-9-29
```


## Boolean

`Boolean` 对象可将 "非逻辑值" 转换为 "逻辑值"，即转换为 `true` / `false` 。

注意，如果逻辑对象为以下几种情况：

* 无初始值
* 或值为 `0`
* 或值为 `-0`
* 或值为 `null`
* 或值为 `""`
* 或值为 `false`
* 或值为 `undefined`
* 或值为 `NaN`

那么对象的值为 `false`，否则对象的值为 `true`。

> 特别地，当变量为字符串 `"false"`时，对象的值是 `true` 噢。

示例：

```js
console.log(0 ? 'T' : 'F')          // F
console.log(-0 ? 'T' : 'F')         // F
console.log(null ? 'T' : 'F')       // F
console.log('' ? 'T' : 'F')         // F
console.log(false ? 'T' : 'F')      // F
console.log(undefined ? 'T' : 'F')  // F
console.log(NaN ? 'T' : 'F')        // F

console.log('false' ? 'T' : 'F')    // T
```

```js
var a
var b = 1
console.log(a ? 'T' : 'F')    // F
console.log(b ? 'T' : 'F')    // T
```

```js
function test(x, y) {
  if (x) {
    console.log('do x thing')
  }
  if (y) {
    console.log('do y thing')
  }
}

test()
//

test(1)
// do x thing


test(1, 2)
// do x thing
// do y thing
```



## 总结

这篇文章主要是讲了 JavaScript 中 Number、Array、Date 和 Boolean 的一般用法。