# 开始

React Navigation 源于 React Native 社区需要的一个可扩展且易于使用的导航解决方案，该解决方案完全使用 JavaScript 编写（因此你可以阅读并理解所有源代码）。

在你确定为你的项目使用 React Navigation 之前，你可能需要阅读 [推荐和反推荐信息](https://reactnavigation.org/docs/en/pitch.html) - 它可以帮助你了解「选择的权衡」以及「该库目前存在缺陷的区域」。

## 期待什么

如果你已经熟悉 React Native，那么你就可以快速使用 React Navigation 进行导航！如果不是，你可能需要先阅读 [React Native Express](http://www.reactnativeexpress.com/) 的第 1 至 4 部分（含），完成后再返回此处。

本文档的基础部分内容是浏览 React Navigation 的最重要方面。它应该涵盖足够的知识，以便知道如何构建典型的小型移动应用程序，并为你提供需要深入了解 React Navigation 更高级部分的背景知识。

## 安装

在你的 Ract Native 项目中安装 `react-navigation` :
```
yarn add react-navigation
# or with npm
# npm install --save react-navigation
```

然后，你就可以进行愉快的编码了。

# 你好 React Navigation

在 Web 浏览器中，您可以使用锚点 `<a>` 标签链接到不同的页面。当用户点击一个链接时，URL 会被推送到浏览器的「历史堆栈」中。当用户按下后退按钮时，浏览器从「历史堆栈」的顶部弹出 (pop) 该页面，然后当前的活跃页面是先前访问过的页面。React Native 没有像 Web 浏览器那样内置全局「历史堆栈」 - 这就是 React Navigation 诞生的初衷。

React Navigation 的「堆栈导航器」(stack navigatior) 为你的应用提供了一种途径，让你可以轻松地在页面之间切换并管理导航历史记录。如果你的应用程序只使用一个「堆栈导航器」，则它在概念上类似于 Web 浏览器处理导航状态的方式 - 当用户与其交互时，你的应用程序会从「导航堆栈」中推送 (push) 和弹出页面，使得用户可以在不同页面进行不同的操作。在 Web 浏览器和 React Navigation 中，工作原理的一个主要区别是：**React Navigation 的「堆栈导航器」提供了你在 Android 和 iOS 上，在堆栈路由之间导航时期望的手势和动画**。

开始使使用时，我们只需要用到 React Navigation 的函数： `createStackNavigator()`。

## 创建一个堆栈导航器

`createStackNavigator()` 方法会返回一个 React 组件，它接受一个「路由配置」对象，并且可选地包含一个「选项」对象（现在我们省略它）。因为 `createStackNavigator()` 方法返回一个 React 组件，所以我们可以直接从 `App.js` 中导出它，作为我们应用程序的根组件。

```js
// In App.js in a new project

import React from 'react';
import { View, Text } from 'react-native';
import { createStackNavigator } from 'react-navigation';

class HomeScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Home Screen</Text>
      </View>
    );
  }
}

export default createStackNavigator({
  Home: {
    screen: HomeScreen
  },
});
```

运行成功后，显示效果是这样的：

| iOS | Android
| -- | --
| ![](./res/ios.png) | ![](./res/android.png)

页面中包含一个空的导航栏和一个包含你的 `HomeScreen` 组件的灰色内容区域。你看到的导航栏和内容区域的样式是「堆栈导航器」的默认配置，我们将在后面学习如何配置这些样式。

> 路由名称的大小写无关紧要 - 你可以使用小写的 `Home` 或大写的 `Home`，这取决于你。一般来说，建议使用首字母大写。

> 路由的配置只需要页面组件。你可以阅读 [StackNavigator reference](https://reactnavigation.org/docs/en/stack-navigator.html) 获取更多信息。

在 React Native 中，从 `App.js` 中导出的组件是你应用程序的入口 (根组件) - 它是每个其它组件的下降 (descends) 组件。与导出 `createStackNavigator()` 的返回值相比，对应用根组件进行更多控制显得更有用，所以我们可以导出一个只呈现我们的 `RootStack` 堆栈导航器的组件。
```js
const RootStack = createStackNavigator({
  Home: {
    screen: HomeScreen
  },
});

export default class App extends React.Component {
  render() {
    return <RootStack />;
  }
}
```

## 路由配置简写

如果我们唯一的路由配置是「页面组件」，可以不使用 `{screen：HomeScreen}` 格式进行配置，而是直接使用「页面组件」：
```js
const RootStack = createStackNavigator({
  Home: HomeScreen
});
```

## 增加第二个路由

`<RootStack />` 组件不接受任何属性 (props) - 所有配置都在 `createStackNavigator()` 方法的 `options` 参数中指定。我们将 options 留空，所以它只使用默认配置。要查看使用 options 对象的示例，我们将向「堆栈导航器」中添加第二个屏幕。
```js
// Other code for HomeScreen here...

class DetailsScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Details Screen</Text>
      </View>
    );
  }
}

const RootStack = createStackNavigator(
  {
    Home: HomeScreen,
    Details: DetailsScreen,
  },
  {
    initialRouteName: 'Home',
  }
);

// Other code for App component here...
```

现在我们的堆栈有两个路由：
* `Home` 路由，对应于 `HomeScreen` 组件
* `Details` 路由，对应于 `DetailsS​​creen` 组件

`Home` 路由是堆栈的初始路由，在这一点上自然就引申出一个问题是：我如何从 `Home` 路由跳转到 `Details` 路由？这在下一节中介绍。


## 总结

至此，整个示例的完整代码如下：
```js
import React from 'react';
import { View, Text } from 'react-native';
import { createStackNavigator } from 'react-navigation'; // Version can be specified in package.json

class HomeScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Home Screen</Text>
      </View>
    );
  }
}

class DetailsScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Details Screen</Text>
      </View>
    );
  }
}

const RootStack = createStackNavigator(
  {
    Home: HomeScreen,
    Details: DetailsScreen,
  },
  {
    initialRouteName: 'Home',
  }
);

export default class App extends React.Component {
  render() {
    return <RootStack />;
  }
}
```
