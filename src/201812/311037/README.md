# React 实战进阶 - 下一代 React：异步渲染

时间分片(Time Slicing)：
* DOM 操作的优先级低于浏览器原生行qf例如键盘和鼠标输入f从而保证操作的流畅。



渲染挂起(Suspense)
* 虚拟 DOM 节点可以等待某个异步操作的完成，并指定 timeout 后才完成真正的渲染。

## 时间分片

1. 虚拟 DOM 的 diff 操作可以分片进行
2. React 新 API：`unstable_deferredUpdates`
3. Chrome 新 API：`requestIdleCallback`


## 渲染挂起
1. 新内置组件：`Timeout`
2. `unstable_deferUpdate`

Demo
