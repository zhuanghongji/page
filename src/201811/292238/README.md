# React Native - 属性和状态

在 RN 中有两种类型的数据来控制一个组件：
* `props`: 属性，由父组件指定，且一旦指定将不可再修改。
* `state`: 状态，用于需要改变的数据。

## Props

大多数组件在创建时就可以通过不同的参数进行定制，这个定制参数就称为 `props`（属性）。

比如说，在 RN 中有一个基本组件 `Image`，你可以通过一个名为 `source` 的属性来控制要显示的图片内容。

```js
import React, {Component} from 'react';
import {
  StyleSheet,
  Image
} from 'react-native';

export default class App extends Component<Props> {
  render() {
    // wrong
    // let pic = "https://upload.wikimedia.org/wikipedia/commons/d/de/Bananavarieties.jpg"
    let pic = {
      uri: "https://upload.wikimedia.org/wikipedia/commons/d/de/Bananavarieties.jpg"
    };
    return (
        <Image source={pic} style={{width: 300, height: 200}}/>
    )
  }
}
```

![](./res/1.png)

> 在 iOS 上使用 http 链接的图片地址可能不会显示，见 [解决办法](https://segmentfault.com/a/1190000002933776)

请注意，`{pic}` 有一层花括号，我们需要通过这层括号来把 `pic` 变量嵌入到 JSX 中。

> 花括号的意思是，内部是一个 「js 变量」或「表达式」，需要执行后取值。

你自己定义的组件同样可以使用 `props`，这使得你可以通过不同的参数值让单一组件在应用的不同地方使用。这仅仅需要你在 `render` 函数中引用 `this.props`，举个栗子：

```js
import React, {Component} from 'react';
import {
  StyleSheet,
  View,
  Text,
} from 'react-native';

class Greeting extends Component {
  render() {
    return (
      <Text>Hello {this.props.name}</Text>
    )
  }
}

export default class App extends Component<Props> {
  render() {
    return (
      <View style={styles.container}>
        <Greeting name='张三'/>
        <Greeting name='李四'/>
        <Greeting name='王五'/>
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
  },
});
```

![](./res/2.png)

通过 `name` 作为属性使得我们介意对 `Greeting` 组件进行定制，所以我们可以在不同的场景重复使用该组件。上面的示例把 `Greeting` 写在了 JSX 中，用法和内置组件并无差异。这正是 React 的魅力所在。

上面的示例还用一个名为 `View` 的组件，该组件常用作其它组件的容器，来帮助控制布局和样式。


## State

一般来说，你应该在 `constructor` 中初始化 `state`，然后在需要修改时调用 `setState()` 方法。

举个栗子，现在我们想要做一段不断闪烁的文字。
* 文字内容本身在组件创建时就已经指定好了，所以文字内容是一个 `props`。
* 快速的进行显示隐藏切换就产生了闪烁的效果，所以文字内容的显示或隐藏状态是随着时间变化的，是一个 `state`。

```js
class Blink extends Component {
  constructor(props) {
    super(props)
    this.state = {
      isShowingText: true,
    }

    // Toggle the state every second
    setInterval(() => {
      this.setState(previousState => {
        return {isShowingText: !previousState.isShowingText}
      })
    }, 1000)
  }

  render() {
    let display = this.state.isShowingText ? this.props.text : ' '
    return (
      <Text>{display}</Text>
    )
  }
}

export default class App extends Component<Props> {
  render() {
    return (
      <View style={styles.container}>
        <Blink text='你好'/>
        <Blink text='我好'/>
        <Blink text='大家好才是真的好'/>
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
  },
});
```

![](./res/blink.gif)

实际开发中，我们一般不会通过定时器函数（`setInterval()` `setTimeout()` ..） 来操作 `state`。我们大多数是在接收到服务器返回的新数据或用户输入数据之后，再去操作 `state`。在这种场景下，我们也可以使用一些「状态容器」比如 [Redux](http://redux.js.org/index.html) 来统一管理数据流。

一旦 `setState()` 方法被调用，`Blink` 组件会被重新绘制。

`State` 的工作原理和 `React` 一致，所以如果想进行更加深入的了解细节，可以阅读 [React.Component API](https://facebook.github.io/react/docs/component-api.html)。
