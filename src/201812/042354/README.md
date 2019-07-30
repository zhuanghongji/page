# React Native - 页面间的导航跳转

移动应用很少是单页面的。通常，多个页面的显示、转换管理由「导航器」来处理。

本文主要总结了 RN 中常用的几个「导航组件」(navigation components)。
* 如果你刚开始接触 navigation，使用 [React navigation](https://facebook.github.io/react-native/docs/navigation.html#react-navigation) 是个不错的选择。
> React Navigation 提供了一个易于使用的导航解决方案，能够在 iOS 和 Android上 呈现常见堆栈导航和选项卡式导航模式。由于这是一个 JavaScript 的实现，它在与诸如 [redux](https://reactnavigation.org/docs/redux-integration.html) 等状态管理库进行集成时，能提供了最大的可配置性和灵活性。

* 如果你只针对 iOS 平台开发，并且想和原生外观一致，那么可以选择 [NavigatorIOS](https://facebook.github.io/react-native/docs/navigation.html#navigatorios)
> 因为它提供了本地 `UINavigationController` 类的包装。但是，该组件无法在 Android 上运行。


* 如果你希望在 iOS 和 Android 上实现原生的外观和感觉，或者将 React Native 集成到原生管理导航的应用中，可以考虑使用以下两个库：
  * [native-navigation](http://airbnb.io/native-navigation/)
  * [react-native-navigation](https://github.com/wix/react-native-navigation)


## React Navigation

如今社区推荐的导航方案是使用一个单独的导航库 `react-navigation`，它使得开发者只需几行代码即可设置应用的各个页面。

首先，在你项目中进行安装：
```java
npm install --save react-navigation
```
```
npm WARN eslint-plugin-react-native@3.2.1 requires a peer of eslint@^3.17.0 || ^4.0.0 but none is installed. You must install peer dependencies yourself.

+ react-navigation@2.0.4
added 17 packages in 9.099s
```

然后，你就可以快速创建多页面的应用了，举个栗子：

### 先创建两个页面「Main」和「Profile」

./src/screens/MainScreen.js
```js
import React, { Component } from 'react'
import {
  StyleSheet,
  View,
  Text,
  Button,
} from 'react-native'

export default class MainScreen extends Component {

  static navigationOptions = {
    title: '主页'
  }

  render() {
    const { navigate } = this.props.navigation
    return (
      <View style={styles.container}>
        <Text>This is MainScreen</Text>
        <Button
          title="Go to Jane's Profile"
          onPress={() => {
            navigate('Profile', { name: 'Jane' })
          }}
        />
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'orange'
  }
})
```

./src/screens/ProfileScreen.js
```js
import React, { Component } from 'react'
import {
  StyleSheet,
  View,
  Text,
} from 'react-native'

export default class ProfileScreen extends Component {

  static navigationOptions = ({ navigation }) => ({
    title: `${navigation.state.params.name}'s Profile`,
  })



  render() {
    // const name = this.props.navigation.state.params.name
    const { params } = this.props.navigation.state
    return (
      <View style={styles.container}>
        <Text>This is ProfileScreen of {params.name} </Text>
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: 'green'
  }
})
```

### 其它相关代码

./index.js
```js
import { AppRegistry } from 'react-native';
import App from './App';

AppRegistry.registerComponent('AwesomeProject', () => App);

```

./App.js
```js
import {
  StackNavigator
} from 'react-navigation'

import MainScreen from "./src/screens/MainScreen";
import ProfileScreen from "./src/screens/ProfileScreen";

const App = StackNavigator({
  Main: { screen: MainScreen },
  Profile: { screen: ProfileScreen }
})

export default App
```

### 效果图

| iOS | Android
| -- | --
| ![](./res/react-navigation-ios.gif) | ![](./res/react-navigation-android.gif)

React Navigation 的路由写法使其非常容易扩展导航逻辑，或是整合到 `redux` 中。由于路由可以嵌套使用，因而开发者可以根据不同页面编写不同的导航逻辑，且彼此互不影响。

React Navigation 中的视图是原生组件，同时用到了运行在原生线程上的 `Animated` 动画库，因而性能表现十分流畅。此外其动画形式和手势都非常便于定制。

要想详细了解 `React Navigation`，可以阅读这一篇英文的 [入门文档](https://reactnavigation.org/docs/en/getting-started.html)。


## NavigatorIOS

// TODO





















`
