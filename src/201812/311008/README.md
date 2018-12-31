# React 实战进阶：15 Redux(6) 理解 Redux 运行的基础：不可变数据 (Immutability)

## 不可变数据 (immutable data)

![](./res/immutable-data.png)

图片来源：[http://arqex.com/991/json-editor-react-immutable-data](http://arqex.com/991/json-editor-react-immutable-data)


## 为何需要不可变数据

1. 性能优化
2. 易于调试和跟踪
3. 易于推测


## 如何操作不可变数据

1. 原生写法：`{ ... }`，`Object.assign`
2. [immutability-helper](https://github.com/kolodny/immutability-helper)
3. [immer](https://github.com/mweststrate/immer)

### 原生写法

```js
const state = { filter: 'completed', todos: [
  'Learn React',
]}

const newState = { ...state, todos: [
  ...state.todos,
  'Learn Redux',
]}

const newState2 = Object.assign({}, state, { todos: [
  ...state.todos,
  'Learn Redux',
]})
```

### immutability-helper

```js
import update from 'immutability-helper';

const state = { filter: 'completed', todos: [
  'Learn React',
]}

const newState = update(state, {
  todos: {$push: ['Learn Redux']}
})
```

### immer

```js
import produce from "immer"

const state = { filter: 'completed', todos: [
  'Learn React',
]}

const newState = produce(state, draftState => {
  draftState.todos.push('Learn Redux')
})
```



## 小结

1. 不可变数据的含义
2. Redux 为什么使用不可变数据
3. 如何操作不可变数据
