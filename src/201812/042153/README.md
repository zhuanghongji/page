# ES6 对 ES5 原有对象的一些扩展

## 字符串的扩展

ES6 加强了对 Unicode 的支持，并且扩展了字符串对象。

下面节选列举了一些常见的知识点。

**字符串的遍历接口**

ES6 为字符串添加了遍历器接口，使得字符串可以被 `for...of` 循环遍历：

```js
for (let c of '123') {
  console.log(c)
}
// 1
// 2
// 3
```

.


**`startsWith()`、`endsWith()` 和 `includes()`**

传统的 JavaScript 只有 `indexOf()` 方法可用来确定一个字符串是否包含在另一个字符串中。

ES6 提供了三种新方法：
* `startsWith()`: 返回布尔值，表示「参数字符串」是否在「原字符串」的头部。
* `endsWith()`: 返回布尔值，表示「参数字符串」是否在「原字符串」的尾部。
* `includes()`: 返回布尔值，表示「原字符串」是否包含「参数字符串」。

```js
// ------123456789012
let s = 'Hello World!'
console.log(s.startsWith('Hello'))  // true
console.log(s.endsWith('d!'))       // true
console.log(s.includes('llo'))      // true
```

上述三个方法都支持第二个参数，表示开始搜索的位置。

```js
// ------123456789012
let s = 'Hello World!'
console.log(s.startsWith('World', 6))   // true
console.log(s.endsWith('Hello', 5))     // true
console.log(s.includes('Hello', 5))     // false
```

注意：使用第二个参数时，`endsWith()` 的行为与其它两个方法有所不同：
* `endsWith()` 针对的是 **前 `n` 个字符**。
* 而 `startsWith()` 和 `includes()` 针对的是 **从第 `n` 个位置知道字符串结束**。

.

**`repeat()`**

`repeat()` 方法返回一个新字符串，表示将原字符串重复 `n` 次。

示例：

```js
console.log('x'.repeat(0))      //
console.log('na'.repeat(2))     // nana
console.log('hello'.repeat(3))  // hellohellohello
```

对于参数的一些边界值：
* 如果是小数，会被取整。
* 如果是负数或 `Infinity`，会报错。
* 如果是 0 到 -1 之间的小数，则等同于 0。
* 如果是 `NaN`，也等同于 0。
* 如果是字符串，则会先转换成数字。

不信你看：

```js
console.log('hi'.repeat(2.9))         // hihi

console.log('hi'.repeat(Infinity))    // RangeError: Invalid count value

console.log('hi'.repeat(-1))          // RangeError: Invalid count value

console.log('hi'.repeat(-0.9))        //
console.log('hi'.repeat(NaN))         //
console.log('hi'.repeat('na'))        //
console.log('hi'.repeat('3'))         // hihihi
```

.

**`padStart()` 和 `padEnd()`**

ES6 引入了字符串不全长度的功能：

* `padStart()`: 用于头部补全。
* `padEnd()`: 用于尾部补全。

```js
'x'.padStart(5, 'ab')  // 'ababx'
'x'.padStart(4, 'ab')  // 'abax'

'x'.padEnd(5, 'ab')   // 'xabab'
'x'.padEnd(4, 'ab')   // 'xaba'
```

要注意的是：

* 如果省略第二个参数，默认使用空格补全长度。
* 如果原字符串的长度，等于或大于指定的长度，则直接返回原字符串。
* 如果用来补全的字符串与原字符串，两者的长度之和超过了指定的最小长度，则会截去超出位数的补全字符串。

```js
'x'.padStart(4)         // '   x'
'x'.padEnd(4)           // 'x   '

'xxx'.padStart(2, 'ab') // 'xxx'
'xxx'.padEnd(2, 'ab')   // 'xxx'

'abc'.padStart(10, '0123456789')    // '0123456abc'
```

这两个方法常用来「为数值补全指定位数」或「提示字符串格式」：

```js
'1'.padStart(10, '0')       // "0000000001"
'12'.padStart(10, '0')      // "0000000012"
'123456'.padStart(10, '0')  // "0000123456"

'12'.padStart(10, 'YYYY-MM-DD')       // "YYYY-MM-12"
'09-12'.padStart(10, 'YYYY-MM-DD')    // "YYYY-09-12"
```

.

**`matchAll()`**

`matchAll()` 方法返回一个正则表达式在当前字符串的所有匹配。


.

**模板字符串**

模板字符串是增强型的字符串，用反引号标识。
* 可以当做普通字符串使用。
* 也可以用来定义多行字符串。
* 也可以在字符串中嵌入变量。

示例：

```js
let a = `In JavaScript '\n' is a line-feed.`
console.log(a)
// In JavaScript '
// ' is a line-feed.

let b = `In JavaScript this is
not legal.`
console.log(b)
// In JavaScript this is
// not legal.

let name = "Bob", time = "today";
let c = `Hello ${name}, how are you ${time}?`
console.log(c)
// Hello Bob, how are you today?
```

有趣的是，模板字符串中的 `${}` 除了可以放入变量外，也可以

* 放入表达式
* 进行运算
* 引用对象属性
* 调用函数

我们来演示下：

```js
let x = 1
let y = 2
console.log(`${x} + ${y} = ${x + y}`)   // 1 + 2 = 3

let obj = {x : 2, y: 3}
console.log(`${obj.x + obj.y}`)         // 5

function foo() {
  return 'John'
}
console.log(`hi ${foo()}`)              // hi John
```


## 数值的扩展

**`Number.isFinite()`**

用来检查一个数值是否为有限的 (finite)，即非 `Infinity`。

> 注意：如果参数类型不是数值，`Number.isFinite()` 一律返回 `false`。

```js
Number.isFinite(15);        // true
Number.isFinite(0.8);       // true

Number.isFinite(NaN);       // false
Number.isFinite(Infinity);  // false
Number.isFinite(-Infinity); // false

Number.isFinite('foo');     // false
Number.isFinite('15');      // false
Number.isFinite(true);      // false
```

.

**`Number.isNaN()`**

用来检查一个值是佛为 `NaN`。

> 注意：如果参数类型不是 `NaN`，`Number.isNaN()` 一律返回 `false`。

```js
Number.isNaN(NaN)               // true
Number.isNaN(9 / NaN)             // true
Number.isNaN('true' / 0)        // true
Number.isNaN('true' / 'true')   // true

Number.isNaN(15)      // false
Number.isNaN('15')    // false
Number.isNaN(true)    // false
```

.

**`Number.isInteger()`**

用来判断一个数值是否是整数。

如果参数不是数值，则返回 `false`。

```js
Number.isInteger(25)    // true
Number.isInteger(25.1)  // false

Number.isInteger()      // false
Number.isInteger(null)  // false
Number.isInteger('15')  // false
Number.isInteger(true)  // false

Number.isInteger(25.0)  // true
```

要注意的是，在 JavaScript 内部，整数和浮点数采用的是相同的存储方法，所以 25 和 25.0 被视为同一个值。

```js
25 === 25.0             // true
Number.isInteger(25.0)  // true
```

还有，如果对数据精度的要求比较好，不建议使用 `Number.isInteger()` 判断一个数值是否为整数。举个栗子：

```js
Number.isInteger(3.0000000000000002)  // true
```

.

**Number.EPSILON**

我们知道在 JavaScript 中浮点数计算是不精确的，比如：

```js
let a = 0.1 + 0.2
let b = 0.3
console.log(a)        // 0.30000000000000004
console.log(b)        // 0.3
console.log(a == b)   // false
```

ES6 在 `Number` 对象上新增了一个极小的常量 `Number.EPSILON`，表示 1 与大于 1 的最小浮点数之间的差。

`Number.EPSILON` 实际上是 JavaScript 能够表示的最小精度，误差如果小于这个值，可以认为已经没有意义了，即不存在误差。该常量长用来作为或设置「能够接受的误差范围」。

举个栗子，为浮点数运算部署一个误差检查函数：

```js
function withinErrorMargin (left, right) {
  return Math.abs(left - right) < Number.EPSILON * Math.pow(2, 2);
}

0.1 + 0.2 === 0.3 // false
withinErrorMargin(0.1 + 0.2, 0.3) // true

1.1 + 1.3 === 2.4 // false
withinErrorMargin(1.1 + 1.3, 2.4) // true
```

.

**`Math.trunc()`**

去除一个数的小数部分，返回整数部分。
* 对于非数值，`Math.trunc` 内部使用 `Number` 方法将其先转为数值。
* 对于空值和无法截取整数的值，返回 `NaN`。

```js
Math.trunc(4.1)     // 4
Math.trunc(4.9)     // 4
Math.trunc(-4.1)    // -4
Math.trunc(-4.9)    // -4
Math.trunc(-0.1234) // -0

Math.trunc('123.456')   // 123
Math.trunc(true)        //1
Math.trunc(false)       // 0
Math.trunc(null)        // 0

Math.trunc(NaN);        // NaN
Math.trunc('foo');      // NaN
Math.trunc();           // NaN
Math.trunc(undefined)   // NaN
```

## 函数的扩展

**函数参数的默认值**

ES6 允许为函数的参数设置默认值 (直接写在参数定义的后面即可)。

```js
function log(x, y = 'World') {
  console.log(x, y)
}

log('Hello')              // Hello World
log('Hello', undefined)   // Hello World
log('Hello', null)        // Hello null
log('Hello', '')          // Hello World
log('Hello', 'China')     // Hello China
```

> 如果传入 `undefined`，将触发该参数等于默认值，`null` 则没有这个效果。

使用参数默认值时，不能有同名函数。

```js
// 不报错
function foo(x, x, y) {
  console.log(`x = ${x}, y = ${y}`)
}
foo(1, 2)     // x = 2, y = undefined
foo(1, 2, 3)  // x = 2, y = 3

// 报错
function bar(x, x, y = 1) { ... }
```

参数默认值是惰性求值的。

```js
let x = 99
function foo(p = x + 1) {
  console.log(p);
}

foo()     // 100

x = 100
foo()     // 101
```

.

**rest 参数**

ES6 引入 rest 参数，形式如 `...变量名`。
* 可用于获取函数的多余参数，这样就不需要使用 `arguments` 对象了。
* rest 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

```JS
function sum(...values) {
  let sum = 0
  for (let v of values) {
    sum += v
  }
  return sum
}

console.log(sum())        // 0
console.log(sum(1))       // 1
console.log(sum(1, 2))    // 3
console.log(sum(1, 2, 3)) // 6
```

注意：
* rest 参数之后不能再有其他参数（即只能是最后一个参数），否则会报错。
* 函数的 `length` 属性，不包括 rest 参数。

.

**箭头函数**

ES6 允许使用 `=>` 定义函数。

```js
var f = v => v;

// 等同于
var f = function (v) {
  return v;
};
```

如果箭头函数不需要参数或需要多个参数，可以使用一个圆括号代表参数部分。

```js
var f = () => 5;
// 等同于
var f = function () {
  return 5
};

var sum = (num1, num2) => num1 + num2;
// 等同于
var sum = function(num1, num2) {
  return num1 + num2;
};
```

如果箭头函数的代码块部分多于一条语句，要使用大括号将它们括起来。此时如果需要返回的，要使用 `return` 语句进行返回。

```js
let sum = (a, b) => {
  console.log('do something else')
  return a + b
}

sum(2, 3)  // 5
```

由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号，否则会报错。

```js
// 报错
let getTempItem = id => { id: id, name: "Temp" };

// 不报错
let getTempItem = id => ({ id: id, name: "Temp" });
```

箭头函数有几个使用注意点:

* **函数体内的 `this` 对象，就是定义时所在的对象，而不是使用时所在的对象**。
* 不可以当作构造函数，也就是说，不可以使用 `new` 命令，否则会抛出一个错误。
* 不可以使用 `arguments` 对象，该对象在函数体内不存在。如果要用，可以用 rest 参数代替。
* 不可以使用 `yield` 命令，因此箭头函数不能用作 `Generator` 函数。

上述的第一点尤为重要，箭头函数使得 `this` 的指向是固定的，这种特性很有利于封装回调函数。

```js
function foo() {
  setTimeout(() => {
    console.log('a: ', this.a)
  }, 100)
}

let a = 1
foo.call({a: 2})
// a: 2
```

## 其它

可参考：
* [ECMAScript 6 入门 - 数组的扩展](http://es6.ruanyifeng.com/#docs/array) 
* [ECMAScript 6 入门 - 对象的扩展](http://es6.ruanyifeng.com/#docs/object)
