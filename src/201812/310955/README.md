# React 实战进阶 - JSX 语法的本质不是模板引擎，只是一种语法糖

在 JavaScript 代码中直接写 HTML 标记

```js
const name = 'Nate Wang'
const element = <h1>Hello, {name}</h1>
```

## JSX 的本质

动态创建组件的语法糖

```js
const name = 'Nate Wang'
const element = <h1>Hello, {name}</h1>
```

```js
const name = 'Josh Perez'
const element = React.createElement(
  'h1',
  null,
  'Hello',
  name,
)
```

## 如何使用 JSX

### 在 JSX 中使用表达式

1. JSX 本身也是表达式
```js
const element = <h1>Hello, world!</h1>
```

2. 在属性中使用表达式
```js
<MyComponent foo={ 1 + 2 + 3 + 4 } />
```

3. 延展属性
```js
const props = { firstName: 'Ben', lastName: 'Hector' }
const greeting = <Greeting {...props} />
```

4. 表达式作为子元素
```js
const element = <li>{props.message}</li>
```

### 对比其它模板语言


### 约定：自定义组件以大写字母开头

1. React 认为小写的 tag 是原生 DOM 节点，如 div
2. 大写字母开头为自定义组件
3. JSX 标记可以直接使用属性语法，例如 <menu.Item />


## JSX 的优点

1. 声明式创建界面的直观
2. 代码动态创建界面的灵活
3. 无需学习新的模板语言
