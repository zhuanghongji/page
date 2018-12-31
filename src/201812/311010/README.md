# React 实战进阶：17 React Router(2) 参数定义，嵌套路由的使用场景

## 路由参数的传递

### 通过 URL 传递参数

1. 如何通过 URL 传递参数：`<Route path="/topic/:id" .../>`
2. 如何获取参数：`this.props.match.params`
3. [https://github.com/pillarjs/path-to-regexp](https://github.com/pillarjs/path-to-regexp)


### 何时需要 URL 参数

页面状态尽量通过 URL 参数定义。


## 嵌套路由的实现

1. 每个 React 组件都可以是路由容器。
2. React Router 的声明式语法可以方便的定义嵌套路由。
