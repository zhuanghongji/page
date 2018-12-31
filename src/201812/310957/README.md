# React 实战进阶：05 理解 Virtual DOM 的工作原理，理解 key 属性的作用

## JSX 的运行基础：Virtual DOM

### 虚拟 DOM 是如何工作的

<img src="./res/1.png" width="512"/>


### 广度优先分层比较

<img src="./res/2.png" width="512"/>


### 根节点开始比较

<img src="./res/3.png" width="512"/>


### 属性变化及顺序

<img src="./res/4.png" width="512"/>


### 节点类型发生变化

<img src="./res/5.png" width="512"/>


### 节点跨层移动

<img src="./res/6.png" width="512"/>


## 虚拟 DOM 的两个假设

1. 组件的 DOM 结构是相对稳定的
2. 类型相同的兄弟节点可以被唯一标识


## Demo


## 小结

算法复杂度 O(n)

虚拟 DOM 如何计算 diff

key 属性的作用
