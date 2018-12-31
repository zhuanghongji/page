# React 实战进阶：00 开始

> 极客时间 - React 实战进阶 45 讲 - 笔记

## 概述

通过该课程系列，能全面学习 React 相关技术，了解其设计模式和最佳实践，掌握使用 React 开发大型项目的能力。

该 Repository 采用 create-react-app 进行构建。

代码从零到一，涵盖所有课程。

课程提供的部分源码运行不符预期，所以在此有所增减。

$ git colne https://github.com/zhuanghongji/react-geek-time.git
$ cd react-geek-time
$ npm start

## 课程大纲

### React 基础

01 React 出现的历史背景及特性介绍
02 以组件方式考虑 UI 的构建
03 JSX 语法的本质：不是模板引擎，只是一种语法糖
04 React 组件的生命周期及其使用场景
05 理解 Virtual DOM 的工作原理，理解 key 属性的作用
06 组件复用的其它形式：高阶组件和函数作为子组件
07 理解新的 Context API 及其使用场景
08 使用脚手架工具创建 React 项目：Create React App，Rekit，Codesandbox
09 打包和部署：静态服务器，CDN 或 Github Pages

### React 生态圈

10 Redux(1)：前端为何需要状态管理库
11 Redux(2)：深入理解 Store，action，reducer
12 Redux(3)：在 React 中使用 Redux
13 Redux(4)：理解异步 action，Redux 中间件
14 Redux(5)：如何组织 action 和 reducer
15 Redux(6)：理解 Redux 运行的基础：不可变数据 (Immutability)
16 React Router(1)：路由不只是页面切换，更是代码组织方式
17 React Router(2)：参数定义，嵌套路由的使用场景
18 UI 组件库对比和介绍：Ant.Design，Material UI，Semantic UI
19 使用 Next.js 创建服务器渲染的 React 应用
20 使用 Jest，Enzyme 等工具进行单元测试
21 常用开发调试工具：ESLint，Prettier，React DevTool，Redux DevTool

### 构建可维护可扩展的前端应用

22 前端项目的理想架构：可维护，可扩展，可测试，易开发，易构建
23 拆分复杂度(1)：按领域模型 (feature) 组织代码，降低耦合度
24 拆分复杂度(2)：如何组织 component，action 和 reducer
25 拆分复杂度(3)：如何组织 React Router 的路由配置
26 使用 Rekit 构建可扩展的 React 应用(1)：创建项目，代码生成和重构
27 使用 Rekit 构建可扩展的 React 应用(2)：遵循最佳实践，保持代码一致性

### 常见场景的最佳实践

28 使用 React Router 管理登录和授权
29 实现表单(1)：初始数据，提交和跳转
30 实现表单(2)：错误处理，动态表单元素，内容动态加载
31 列表页(1)：搜索，数据缓存和分页
32 列表页(2)：加载状态，错误处理
33 页面数据需要来源多个请求的处理
34 内容页的加载和缓存
35 基于 React Router 实现分布操作
36 常见页面布局的实现
37 使用 React Portals 实现对话框，使用 antd 对话框
38 继承第三方 js 库：以 d3.js 为例
39 基于路由实现菜单导航
40 React 中施放的实现

### React 性能优化

41 性能永远是第一需求：时刻考虑性能问题
42 网络性能优化：自动化按需加载
43 使用 reselect 避免重复计算
44 v下一代 React：异步渲染
45 使用 Chrome DevTool 进行性能调优