# React 实战进阶：14 Redux(5) 如何组织 action 和 reducer

## 标准形式 Redux Action 的问题

1. 所有 Action 放一个文件，会无限扩展
2. Action, Reducer 分开，实现业务逻辑时需要来回切换
3. 系统中有哪些 Action 不够直观


## 新的方式

### 单个 action 和 reducer 放在同一个文件

![](./res/actions-reducers.png)


### 每个文件一个 Action

1. 易于开发：不用在 action 和 reducer 文件间来回切
换
2. 易于维护：每个 action 文件都很小，容易理解
3. 易于测试：每个业务逻辑只需对应一个测试文件
4. 易于理解：文件名就是 action 名字，文件列表就是
action 列表

counterPlusOne.js

```js
import {
  COUNTER_PLUS_ONE,
} from './constants'

export function counterPlusOne() {
  return {
    type: COUNTER_PLUS_ONE,
  }
}

export function reducer(state, action) {
  switch (action.type) {
    case COUNTER_PLUS_ONE:
      return {
        ...state,
        count: state.count + 1,
      }
    default:
      return state
  }
}
```

Demo 






















@