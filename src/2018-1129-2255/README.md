# 理解 React Native 中的 Style

在 RN 中，你不需要使用特殊的语言或语法来定义样式，仍然是使用 JavaScript 定义样式（传说中的 `All in js`)。所有的核心组件都接受名为 `style` 的属性，且样式名基本匹配了 web 上的 CSS 命名，只是按照了 js 的语法要求使用了驼峰命名法，比如 `background-color` 改为 `backgroundColor`。

`Style` 属性可以是一个普通的 JavaScript 对象，也可以是一个数组。
* 对象用法是相对简单的，比较常用。
* 在数组用法中，位置居后的样式对象比居前的优先级更高，这样你就可以间接实现样式的继承了。

实际开发中，组件的样式会越来越复杂，所以我们通常会使用 `StyleSheet.create` 来集中定义组件的样式。举个栗子：
```js
export default class App extends Component<Props> {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.red}>仅仅红色{'\n'}</Text>
        <Text style={styles.bigBlue}>仅仅大蓝{'\n'}</Text>
        <Text style={[styles.bigBlue, styles.red]}>大蓝，接着红，红色优先级高{'\n'}</Text>
        <Text style={[styles.red, styles.bigBlue]}>红，接着大蓝，蓝色优先级高{'\n'}</Text>
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
  red: {
    color: 'red',
  },
  bigBlue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 24,
  }
});
```

![](./res/text.png)

常用的做法是按顺序声明和使用 `style` 属性，以借鉴 CSS 中的「层叠」做法，**即后声明的属性会覆盖先声明的同名属性**。
