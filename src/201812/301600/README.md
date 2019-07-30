# React Native 代码规范

> 参考自：[Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)

本规范主要分为四个部分：

* ES6 代码规范
* TypeScript 代码规范
* React/JSX 代码规范
* React Native 项目结构规范

## 目录

ES6 代码规范

* [变量与常量声明](#变量与常量声明)
* [字符串](#字符串)
* [解构](#解构)
* [数组](#数组)
* [函数](#函数)
* [类](#类)
* [模块](#模块)

TypeScript 代码规范

* TODO
	
React/JSX 代码规范

* [文件与组件命名](#文件与组件命名)
* [组件声明](#组件声明)
* [对齐](#对齐)
* [引号](#引号)
* [空格](#空格)
* [state / props](#state-/-props)
* [括号](#括号)
* [标签](#标签)
* [方法](#方法)
* [方法声明的顺序](#方法声明的顺序)

React Native 项目结构规范

* TODO


## ES6代码规范

[⇧ 返回目录](#目录)

### 变量与常量声明

#### 变量

**尽量使用 `let` 来代替 `var`。**

**对于全局变量声明，采用 `global.xxx = xxx`，但应避免声明过多全局变量污染环境。**

#### 常量

**对于常量应使用 `const` 进行声明，命名采用驼峰写法。**

**对于使用 immutable 数据应用 `const` 进行声明。**

```js
// bad
let someNum = 123;
const AnotherStr = '不变的字符串';
let arr = ['不', '变', '数', '组'];
var ANOTHER_OBJ = {
  '不变对象': true
};


// good
const someNum = 123;
const anotherStr = '不变的字符串';
const arr = ['不', '变', '数', '组'];
const anotherObj = {
  '不变对象': true
};

```

### 字符串

**处理多行字符串，使用模板字符串。**

* 以反引号( ` )标示
* 可读性更强，代码更易编写
* 注意排版引起空格的问题，使用场景为声明HTML模板字符串

```js
// bad
const tmpl = '<div class="content"> \n' +
              '<h1>这是换行了。</h1> \n' +
            '</div>';


// good
const tmpl = `
<div class="content">
  <h1>这是换行了。</h1>
</div>`;
```

**处理字符串拼接变量时，使用模板字符串。**

```js
  // bad
  function sayHi(name) {
    return 'How are you, ' + name + '?';
  }


  // good
  function sayHi(name) {
    return `How are you, ${name}?`;
  }
```

### 解构

**解构语句中不使用圆括号。**

```js
// bad
[(a)] = [11]; // a未定义
let { a: (b) } = {}; // 解析出错


// good
let [a, b] = [11, 22];
```

#### 对象解构

* 对象解构：元素与顺序无关  
* 对象指定默认值时仅对恒等于undefined ( !== null ) 的情况生效

**若函数形参为对象时，使用对象解构赋值。**

```js
// bad
function someFun(opt) {
  let opt1 = opt.opt1;
  let opt2 = opt.opt2;
  console.log(op1);
}


// good
function someFun(opt) {
  let { opt1, opt2 } = opt;
  console.log(`$(opt1) 加上 $(opt2)`);
}

function someFun({ opt1, opt2 }) {
  console.log(opt1);
}
```

**若函数有多个返回值时，使用对象解构，不使用数组解构，以避免添加顺序的问题。**

```js
// bad
function anotherFun() {
  const one = 1, two = 2, three = 3;
  return [one, two, three];
}
const [one, three, two] = anotherFun(); // 顺序乱了
// one = 1, two = 3, three = 2


// good
function anotherFun() {
  const one = 1, two = 2, three = 3;
  return { one, two, three };
}
const { one, three, two } = anotherFun(); // 不用管顺序
// one = 1, two = 2, three = 3
```

**已声明的变量不能用于解构赋值(语法错误)。**

```js
// syntax error
let a;
{ a } = { b: 123};
```

#### 数组解构

* 数组元素与顺序相关

**交换变量的值。**

```js
let x = 1;
let y = 2;

// bad
let temp;
temp = x;
x = y;
y = temp;

// good
[x, y] = [y, x]; 
```

* 将数组成员赋值给变量时，使用数组解构

```js
const arr = [1, 2, 3, 4, 5];

// bad
const one = arr[0];
const two = arr[1];

// good
const [one, two] = arr;
```

### 数组

* 将类数组(array * like)对象与可遍历对象(如 `Set`, `Map`)转为真正数组
  
**采用 `Array.from` 进行转换。**

```js
// bad
function foo() {
  let args = Array.prototype.slice.call(arguments);
}

// good
function foo() {
  let args = Array.from(arguments);
}

```

**数组去重：**

* 结合 `Set` 结构与 `Array.from`
* 使用 `indexOf`，`HashTable` 等形式，不够简洁清晰

```js
// good
function deduplication(arr) {
  return Array.from(new Set(arr));
}
```

**数组拷贝：**

* 采用数组扩展 `...` 形式

```js
const items = [1, 2, 3];

// bad
const len = items.length;
let copyTemp = [];
for (let i = 0; i < len; i++) {
  copyTemp[i] = items[i];
}

// good
let copyTemp = [...items];
```

**将一组数值转为数组：**

* 采用 `Array.of` 进行转换

```js
// bad
let arr1 = new Array(2);        // [undefined x 2]
let arr2 = new Array(1, 2, 3);  // [1, 2, 3]

// good
let arr1 = Array.of(2);         // [2]
let arr2 = Array.of(1, 2, 3);   // [1, 2, 3]
```

### 函数

**当要用函数表达式或匿名函数时，使用箭头函数 (Arrow Functions)：**

* 箭头函数更加简洁，并且绑定了 `this`

```js
// bad
const foo = function(x) {
  console.log(foo.name);      // 返回'' ，函数表达式默认省略 name 属性
};

[1, 2, 3].map(function(x) {
  return x + 1;
});

var testObj = {
  name: 'testObj',
  init() {
    var _this = this;        // 保存定义时的 this 引用
    document.addEventListener('click', function() {
      return _this.doSth();
    }, false);
  },
  doSth() {
    console.log(this.name);
  }
};

// good
const foo = (x) => {
  console.log(foo.name);     // 返回 'foo'
};

[1, 2, 3].map( (x) => {
  return x + 1;
});

var testObj = {
  name: 'testObj',
  init() {
    // 箭头函数自动绑定定义时所在的对象
    document.addEventListener('click', () => this.doSth(), false);
  },
  doSth() {
    console.log(this.name);
  }
};
```

**箭头函数书写约定：**

* 函数体只有单行语句时，允许写在同一行并去除花括号
* 当函数只有一个参数时，允许去除参数外层的括号

```js
// good
const foo = x => x + x;  // 注意此处会隐性 return x + x

const foo = (x) => {
  return x + x;          // 若函数体有花括号语句块时须进行显性的return
}; 

[1, 2, 3].map( x => x * x);
```

**用箭头函数返回一个对象，应用括号包裹。**

```js
// bad
let test = x => { x: x };    // 花括号会变成语句块，不表示对象

// good
let test = x => ({ x: x });  // 使用括号可正确 return {x:x}
```

**立即调用函数(IIFE)，使用箭头函数。**

```js
// bad
(function() {
  console.log('哈');
})();

// good
(() => {
  console.log('哈');
})();
```

**函数参数指定默认值，采用函数默认参数赋值语法。**

```js
// bad
function foo(opts) {
  opts = opts || {};     // 此处有将 0，'' 等假值转换掉为默认值的副作用
}

// good
function foo(opts = {}) {
  console.log('更加简洁，安全');
}
```

**对象中的函数方法使用缩写形式：**

* 更加简洁
* 函数方法不要使用箭头函数，避免 `this` 指向的混乱

```js
// bad
const shopObj = {
  des: '对象模块写法',
  foo: function() {
    console.log(this.des);
  }
};

const shopObj = {
  des: '对象模块写法',
  foo: () => {
    console.log(this.des); // 此处会变成undefined.des，因为指向顶层模块的this
  }
};

// good
const des = '对象模块写法'; // 使用对象属性值简写方式
const shopObj = {
  des,
  foo() {
    console.log(this.des);
  }
};
```

### 类

**类名应使用帕斯卡(PascalCased)写法。**

```js
// good
class SomeClass {

}
```

**类名与花括号须保留一个空格间距：**

* 类中的方法定义时，括号 `)` 也须与花括号 `{` 保留一个空格间距

```js
// bad
class Foo{
  constructor(){
    // constructor
  }
  sayHi()    {
    // 仅保留一个空格间距
  }
}

// good
class Foo {
  constructor() {
    // constructor
  }
  sayHi() {
    // 仅保留一个空格间距
  }
}
```

**定义类时，方法的顺序如下：**

* `constructor`
* public `get/set` 公用访问器，`set`只能传一个参数
* public methods 公用方法，公用相关命名使用小驼峰式写法(`lowerCamelCase`)
* private `get/set` 私有访问器，私有相关命名应加上下划线 `_` 为前缀
* private methods 私有方法

```js
// good
class SomeClass {
  constructor() {
    // constructor
  }

  get aval() {
    // public getter
  }

  set aval(val) {
    // public setter
  }

  doSth() {
    // 公用方法
  }

  get _aval() {
    // private getter
  }

  set _aval() {
    // private setter
  }

  _doSth() {
    // 私有方法
  }
}

```

**如果不是 class 类，不使用 `new`。**

```js
// bad
function Foo() {

}
const foo = new Foo();


// good
class Foo {

}
const foo = new Foo();
```

**使用真正意思上的类 Class 写法，不使用 `prototype` 进行模拟扩展。**

* Class 更加简洁，易维护
 
```js
// bad
function Dog(names = []) {
  this._names = [...names];
}
Dog.prototype.bark = function() {
  const currName = this._names[0];
  alert(`one one ${currName}`);
}

// good
class Dog {
  constructor(names = []) {
    this._names = [...names];
  }
  bark() {
    const currName = this._names[0];
    alert(`one one ${currName}`);
  }
}
```

**`this` 的注意事项：**

* 子类使用 `super` 关键字时，`this` 应在调用 `super` 之后才能使用
* 可在方法中 `return this` 来实现链式调用写法

```js
class Foo {
  constructor(x, y) {
    this.x = x;
    this.y = y;
  }
}

// bad
class SubFoo extends Foo {
  constructor(x, y, z) {
    this.z = z;    // 引用错误
    super(x, y);
  }
}


// good
class SubFoo extends Foo {
  constructor(x, y, z) {
    super(x, y);
    this.z = z;    // this 放在 super 后调用
  }
  setHeight(height) {
    this.height = height;
    return this;
  }
}
```


### 模块

**使用 `import` / `export` 来做模块加载导出，不使用非标准模块写法。**

```js
// bad
const colors  = require('./colors');
module.exports = color.lightRed;

// good
import { lightRed } from './colors';
export default lightRed;
```

**`import` / `export` 后面采用花括号 `{ }` 引入模块的写法时，须在花括号内左右各保留一个空格**

```js
// bad
import {lightRed} from './colors';
import { lightRed} from './colors';

// good
import { lightRed } from './colors';
```

## TypeScript 代码规范

[⇧ 返回目录](#目录)


### 普通类型

声明对象时禁止使用 `Number`、`String`、`Boolean` 和 `Object`，因为这些类型指的是非原始的装盒对象。  
应该使用 `number`、`string`、`boolean` 和 `object`。

```ts
// bad
function reverse(s: String): String;

// good
function reverse(s: string): string;
```

### 回调函数类型

不要为返回值被忽略的回调函数设置一个 `any` 类型的返回值类型。  
返回值被忽略的回调函数的返回值类型应设置为 `void`。

```ts
// bad
function fn(x: () => any) {
  x();
}

// good
function fn(x: () => void) {
  x();
}
```

为什么使用 `void` 相对安全？

```ts
// 使用 void 能防止你不小心使用 x 的返回值
function fn(x: () => void) {
  var k = x();     // 哎哟，好像你对返回值 k 有想法
  k.doSomething(); // 报错，哥们你不能对 void 类型进行操作 (但如果 `x()` 返回值类型为 any 的话是可以操作的)
}
```

### 回调函数里的可选参数

不要在回调函数中使用可选参数。

```ts
// bad
interface Fetcher {
  getObject(done: (data: any, elapsedTime?: number) => void): void;
}

// good
interface Fetcher {
  getObject(done: (data: any, elapsedTime: number) => void): void;
}
```

### 重载与回调函数

不要因为回调函数参数个数不同而写不同的重载，应该只使用最大参数个数写一个重载。

```ts
// bad
declare function beforeAll(action: () => void, timeout?: number): void;
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;

// good
declare function beforeAll(action: (done: DoneFn) => void, timeout?: number): void;
```

> 原因：回调函数总是可以忽略某个参数的，因此没必要为参数少的情况写重载。参数少的回调函数首先允许错误类型的函数被传入，因为它们匹配第一个重载。


### 函数重载

**顺序**

不要把一般的重载放在精确的重载前面，排序重载 "精确的" 应该排在 "一般的" 之前。

```ts
// bad
declare function fn(x: any): any;
declare function fn(x: HTMLElement): number;
declare function fn(x: HTMLDivElement): string;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: any, wat?


// good
declare function fn(x: HTMLDivElement): string;
declare function fn(x: HTMLElement): number;
declare function fn(x: any): any;

var myElem: HTMLDivElement;
var x = fn(myElem); // x: string, :)
```

> 原因：TypeScript 在调用的时候会选择第一个匹配到的重载当解析函数。当前面的重载比后面的 "普通"，那么后面的会被隐藏，不会被调用。


**使用可选参数**

不要为仅在末尾参数不同时写不同的重载，应该尽可能使用可选参数。

```ts
// bad
interface Example {
  diff(one: string): number;
  diff(one: string, two: string): number;
  diff(one: string, two: string, three: boolean): number;
}

// good
interface Example {
  diff(one: string, two?: string, three?: boolean): number;
}
```

> 原因：
> 1. TypeScript 解析签名兼容性时会查看是否存在能够使用源参数调用的某个目标签名，且允许外来参数。
> 2. 当启用了 TypeScript 的 "严格 `null` 检查" 特性时。因为没有指定的参数在 JavaScript 里表示为 `undefined`，通常显式地为可选参数传入一个 `undefined`。


**使用联合类型**

不要仅因为某个位置上的参数类型不同而定义重载，应尽可能地使用联合类型。

```ts
// bad
interface Moment {
  utcOffset(): number;
  utcOffset(b: number): Moment;
  utcOffset(b: string): Moment;
}

// good
interface Moment {
  utcOffset(): number;
  utcOffset(b: number|string): Moment;
}
```

> 注：没有让 `b` 成为可选的，是因为签名的返回值类型不同。



## React / JSX 代码规范

[⇧ 返回目录](#目录)

### 文件与组件命名

**扩展名**: 使用 `.js` 作为 js 文件的扩展名。如果同一个文件夹下有同名而不同作用的 js 文件，则通过中缀(小写)进一步区分，例如：
* `HomeView.component.js`
* `HomeView.style.js`
* `HomeView.action.js`

**文件名**: 使用驼峰命名法且首字母大写，如 `HomeView.js`。

**组件命名**: 与文件名(除中缀外)完全一致。如果组件单独放置在目录中，则目录名也一致。  

```javascript
// bad
import Footer from './Component/Footer/FooterView'

// good
import Footer from './Component/Footer/Footer'

// good
import Footer from './Footer'
```


### 组件声明

**使用 `class` 与 `extends` 关键字。不使用 `React.createClass` 方法。需要导出的组件直接在 `class` 关键字前使用 `export default`。**

```javascript
// bad
export default React.createClass({
});

// good
export default class HomeView extends React.Component {
}
```

### 对齐

**按下面的案例对齐：**

```javascript
// bad
<Foo superLongParam="bar"
      anotherSuperLongParam="baz" />

// good
<Foo
  superLongParam="bar"
  anotherSuperLongParam="baz"
/>

// 如果一行能摆下 props，那就摆在一行
<Foo bar="bar" />

// 子组件照常缩进
<Foo
  superLongParam="bar"
  anotherSuperLongParam="baz"
>
  <Spazz />
</Foo>
```

### 引号
  
**对于 JSX 的字符串属性使用双引号(`"`)，其它情况下使用单引号。**

```javascript
// bad
<Foo bar='bar' />

// good
<Foo bar="bar" />

// bad
<Foo style={{ left: "20px" }} />

// good
<Foo style={{ left: '20px' }} />
```

### 空格

**在自闭合的标签中包含一个空格。**

```javascript
// bad
<Foo/>

// very bad
<Foo                 />

// bad
<Foo
  />

// good
<Foo />
```

### state / props

**对于多个单词组成的 props，使用驼峰命名法。不使用下划线或连接线。**
 
```javascript
// bad
<Foo
  UserName="hello"
  phone_number={12345678}
/>

// good
<Foo
  userName="hello"
  phoneNumber={12345678}
/>
```

**读取 state 和 props 时，使用 `const` 与解构。必要时可使用 `let`，不使用 `var`。**

```javascript
// bad
var userName = this.props.userName;
let checked = this.state.checked;

// good
const { userName, age, sex } = this.props;
const { checked } = this.state;
```  
    
### 括号

**当 JSX 标签超过一行时，使用括号包裹。**

```javascript
/// bad
render() {
  return <MyComponent className="long body" foo="bar">
            <MyChild />
          </MyComponent>;
}

// good
render() {
  return (
    <MyComponent className="long body" foo="bar">
      <MyChild />
    </MyComponent>
  );
}

// good, when single line
render() {
  const body = <div>hello</div>;
  return <MyComponent>{body}</MyComponent>;
}
```

### 标签

**对于没有子组件的 JSX 标签，始终自闭合。**

```javascript
// bad
<Foo className="stuff"></Foo>

// good
<Foo className="stuff" />
```

**如果组件有多行属性，则另起一行进行自闭合。**
 
```javascript
// bad
<Foo
  bar="bar"
  baz="baz" />

// good
<Foo
  bar="bar"
  baz="baz"
/>
```

### 方法

**为方法命名时，不使用下划线开头(哪怕是想用作私有方法)。**
  
```javascript
// bad
React.createClass({
  _onClickSubmit() {
    // do stuff
  }

  // other stuff
});

// good
class extends React.Component {
  onClickSubmit() {
    // do stuff
  }

  // other stuff
});
```

### 方法声明的顺序

**原则上按如下顺序排列 React 组件的各个 Field 和 Method (生命周期)：**

* 静态 Field
* 静态方法 (static methods)
* 实例 Field
* `constructor`
* 实例方法 (instance methods)
* `getChildContext`
* `componentWillMount`
* `componentDidMount`
* `componentWillReceiveProps`
* `shouldComponentUpdate`
* `componentWillUpdate`
* `componentDidUpdate`
* `componentWillUnmount`
* 点击处理或事件处理函数，比如 `onClickSubmit()`、`onChangeDescription()`
* 用在 `render` 中的 `getter` 方法，比如 `getSelectReason()`、`getFooterContent()`
* 可选的 `render` 方法，比如 `renderNavigation()`、`renderProfilePicture()`
* `render`

**按如下方式定义 propTypes, defaultProps, contextTypes 等。**

```javascript
import React, { Component, PropTypes } from 'react';

const propTypes = {
  id: PropTypes.number.isRequired,
  url: PropTypes.string.isRequired,
  text: PropTypes.string,
};

const defaultProps = {
  text: 'Hello World',
};

class Link extends Component {
  static methodsAreOk() {
    return true;
  }

  render() {
    return (
      <a href={this.props.url} >
       {this.props.text}
      </a>
  }
}

Link.propTypes = propTypes;
Link.defaultProps = defaultProps;

export default Link;
```


## 总结

这篇文章主要是列举了 ES6、TypeScript 和 React 中一些的 **good/bad**。

