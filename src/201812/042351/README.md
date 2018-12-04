# 处理触摸事件

移动应用上的交互主要是「触摸」，比如 “点击按钮”、”在一个列表上滚动” 和 “对地图进行缩放” 等。RN 提供了用于处理常见触摸手机的组件，也提供了识别复杂手势的 [手势响应系统](https://facebook.github.io/react-native/docs/gesture-responder-system.html)。

## Button

`Button` 一个简单示例如下：
```js
<Button
  onPress={() => {
    Alert.alert('You tapped the button!');
  }}
  title="Press Me"
/>
```

点击该按钮时，会弹出一个对话框。

> 注意，一个简单的 `Button` 组件在 iOS 和 Andoroid 上的显示效果不同的：  
>
> ![](./res/button.png)


`Button` 相对复杂点的示例：
```js
export default class App extends Component<Props> {

  _onPressButton() {
    Alert.alert('You tapped the button!')
  }

  render() {
    return (
      <View style={styles.container}>
        <View style={styles.buttonContainer}>
          <Button
            onPress={this._onPressButton}
            title="Press Me"
          />
        </View>
        <View style={styles.buttonContainer}>
          <Button
            onPress={this._onPressButton}
            title="Press Me"
            color="#841584"
          />
        </View>
        <View style={styles.alternativeLayoutButtonContainer}>
          <Button
            onPress={this._onPressButton}
            title="This looks great!"
          />
          <Button
            onPress={this._onPressButton}
            title="OK!"
            color="#841584"
          />
        </View>
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  buttonContainer: {
    margin: 20
  },
  alternativeLayoutButtonContainer: {
    margin: 20,
    flexDirection: 'row',
    justifyContent: 'space-between'
  }
})
```

| iOS | Android
| -- | --
| ![](./res/button-ios.gif) | ![](./res/button-android.gif)

## Touchables

如果基本的 `Button` 组件满足不了你的需求，可以尝试使用 RN 提供的 `Touchables` 相关的组件。

`Touchables` 相关组件提供了以下能力：
* 捕捉点击动作
* 当识别到手势时进行反馈显示

以下 `Touchables` 相关组件并没有提供默认样式，所以你需要根据你的 App 进行相关定义：
* [TouchableHighlight ](https://facebook.github.io/react-native/docs/touchablehighlight.html): 常用，此组件的背景会在用户手指按下时变暗。
* [TouchableNativeFeedback](https://facebook.github.io/react-native/docs/touchablenativefeedback.html): Android 平台上可用，在用户手指按下时形成类似墨水涟漪的视觉效果。
* [TouchableOpacity](https://facebook.github.io/react-native/docs/touchableopacity.html): 在用户手指按下时降低按钮的透明度，而不会改变背景的颜色。
* [TouchableWithoutFeedback](https://facebook.github.io/react-native/docs/touchablewithoutfeedback.html): 在处理点击事件的同时不显示人格视觉反馈。

举个栗子：
```js
export default class App extends Component<Props> {

  _onPressButton() {
    Alert.alert('You tapped the button!')
  }

  _onLongPressButton() {
    Alert.alert('You long-pressed the button!')
  }

  render() {
    return (
      <View style={styles.container}>
        <TouchableHighlight onPress={this._onPressButton} underlayColor="white">
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableHighlight</Text>
          </View>
        </TouchableHighlight>
        <TouchableOpacity onPress={this._onPressButton}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableOpacity</Text>
          </View>
        </TouchableOpacity>
        <TouchableNativeFeedback
          onPress={this._onPressButton}
          background={Platform.OS === 'android' ? TouchableNativeFeedback.SelectableBackground() : ''}>
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableNativeFeedback</Text>
          </View>
        </TouchableNativeFeedback>
        <TouchableWithoutFeedback
          onPress={this._onPressButton}
        >
          <View style={styles.button}>
            <Text style={styles.buttonText}>TouchableWithoutFeedback</Text>
          </View>
        </TouchableWithoutFeedback>
        <TouchableHighlight onPress={this._onPressButton} onLongPress={this._onLongPressButton} underlayColor="white">
          <View style={styles.button}>
            <Text style={styles.buttonText}>Touchable with Long Press</Text>
          </View>
        </TouchableHighlight>
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    paddingTop: 60,
    alignItems: 'center'
  },
  button: {
    marginBottom: 30,
    width: 260,
    alignItems: 'center',
    backgroundColor: '#2196F3'
  },
  buttonText: {
    padding: 20,
    color: 'white'
  }
})
```

| iOS | Android
| -- | --
| ![](./res/touchables-ios.gif) | ![](./res/touchables-android.gif)

## 长按

在一些场景下，你可能想要检测用户是否长时间按住一个视图。这些长按的操作可以通过任何 `Touchables` 相关组件的 `onLongPress` 方法属性进行处理。

## 双指缩放

如果在 `ScrollView` 中只放置一个组件，则可以用来实现缩放操作。设置 `maximumZoomScale` 和 `minimumZoomScale` 属性即可以使用户能够缩放其中的内容。

## 在列表中上下滑动和在视图上左右滑动#

可滚动的列表是移动应用中一个常见的模式。用户会在列表中或快或慢的各种滑动。`ScrollView` 组件可以满足这一需求。

`ScrollView` 可以在垂直或水平方向滚动，还可以配置 `pagingEnabled` 属性来让用户整屏整屏的滑动。此外，水平方向的滑动还可以使用 `Android` 上的 `ViewPagerAndroid` 组件。

`ListView` 则是一种特殊的 `ScrollView`，用于显示比较长的垂直列表。它还可以显示分区块的头部和尾部，类似 `iOS` 上的 `UITableView` 控件。

## 处理其他的手势

如果你想实现视图的拖拽，或是实现自定义的手势，可参阅 [PanResponder API](https://facebook.github.io/react-native/docs/panresponder.html) 或 [Gesture Responder System](https://facebook.github.io/react-native/docs/gesture-responder-system.html) 文档。
