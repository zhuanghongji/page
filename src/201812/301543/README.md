# 通过路由传递参数

我们已经知道如何创建具有某些路由的「堆栈导航器」并在这些路由之间进行导航，现在，让我们来看看在导航到路由时如何进行数据传递。

传递过程分为两部，「发送」和「接收」：
* 将参数作为 `navigation.navigate()` 方法的第二个参数，发送到另一个路由页面：
```js
this.props.navigation.navigate('RouteName'，{
    // params go here
})
```
* 接受页面组件中的参数：
```js
this.props.navigation.getParam（paramName，defaultValue）。
```

示例如下：
```js
class HomeScreen extends React.Component {
  render() {
    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Home Screen</Text>
        <Button
          title="Go to Details"
          onPress={() => {
            /* 1. Navigate to the Details route with params */
            this.props.navigation.navigate('Details', {
              itemId: 86,
              otherParam: 'anything you want here',
            });
          }}
        />
      </View>
    );
  }
}

class DetailsScreen extends React.Component {
  render() {
    /* 2. Get the param, provide a fallback value if not available */
    const { navigation } = this.props;
    const itemId = navigation.getParam('itemId', 'NO-ID');
    const otherParam = navigation.getParam('otherParam', 'some default value');

    return (
      <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
        <Text>Details Screen</Text>
        <Text>itemId: {JSON.stringify(itemId)}</Text>
        <Text>otherParam: {JSON.stringify(otherParam)}</Text>
        <Button
          title="Go to Details... again"
          onPress={() =>
            this.props.navigation.push('Details', {
              itemId: Math.floor(Math.random() * 100),
            })}
        />
        <Button
          title="Go to Home"
          onPress={() => this.props.navigation.navigate('Home')}
        />
        <Button
          title="Go back"
          onPress={() => this.props.navigation.goBack()}
        />
      </View>
    );
  }
}
```

运行结果如下：  

| HomeScreen | DetailsScreen
| -- | --
| ![](./res/1.png) | ![](./res/2.png)

> 你也可以直接使用 `this.props.navigation.state.params` 访问 `params` 对象。但如果没有提供参数，这可能为空，所以使用 `getParam()` 通常更容易，不必处理这种情况。

> 如果你想直接通过 props (例如 `this.props.itemId`) 访问参数而不是 `this.props.navigation.getParam`，则可以使用社区开发的 [react-navigation-props-mapper](https://github.com/vonovak/react-navigation-props-mapper) 软件包。


## 总结

* `navigate` 和 `push` 方法接受可选的第二个参数，以便将参数传递给要导航到的路由页面。例如：
```js
this.props.navigation.navigate（'RouteName'，{
  paramName：'value'
}）
```
* 你可以通过 `this.props.navigation.getParam()` 来获取参数值，
作为 `getParam` 的替代方法，你可以使用 `this.props.navigation.state.params()` 方法。如果没有指定参数，则为 `null`。
