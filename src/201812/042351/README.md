# React Native - 完全理解事件处理

移动应用上的交互主要是「触摸」，比如 “点击按钮”、”在一个列表上滚动” 和 “对地图进行缩放” 等。RN 既提供了用于处理常见触摸手机的组件，也提供了识别复杂手势的 [手势响应系统](https://facebook.github.io/react-native/docs/gesture-responder-system.html)。


这篇文章有点长，主要分为以下几个部分：

* **简单按钮**：通过 `Button` 和 `Text` 组件接收点击事件。
* **Touchables 系列组件**：这四个 Touchables 组件之间的差异和联系。
* **PanResponder API**：基本知识、基本用法及其实际应用示例。
* **手势响应系统**：基本知识及其使用示例等。

## 简单按钮

### Button

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

注意，一个简单的 `Button` 组件在 iOS 和 Andoroid 上的显示效果不同的：

* 在 iOS 上会渲染成一个蓝色的标签状按钮。
* 在 Android 上会渲染成一个蓝色圆角巨星带白色文本的按钮。

> 相关颜色可以通过 `Button` 的 `color` 属性进行指定。

![](./res/button.png)


`Button` 相对复杂点的示例，注意观察按钮颜色和弹出框样式在两个平台上的差异：

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

### Text

// TODO 


## Touchables 系列组件

### 概述

如果基本的 `Button` 组件满足不了你的需求，可以尝试使用 RN 提供的 `Touchables` 系列组件。

`Touchables` 系列组件提供了以下能力：

* 捕捉点击动作
* 当识别到手势时进行反馈显示

`Touchables` 系列组件并没有提供默认样式，需要根据你的 App 进行相关定义，一共有四个：

* [TouchableHighlight](https://facebook.github.io/react-native/docs/touchablehighlight.html): 常用，此组件的背景会在用户手指按下时变暗。
* [TouchableNativeFeedback](https://facebook.github.io/react-native/docs/touchablenativefeedback.html): Android 平台上可用，在用户手指按下时形成类似墨水涟漪的视觉效果。
* [TouchableOpacity](https://facebook.github.io/react-native/docs/touchableopacity.html): 在用户手指按下时降低按钮的透明度，而不会改变背景的颜色。
* [TouchableWithoutFeedback](https://facebook.github.io/react-native/docs/touchablewithoutfeedback.html): 在处理点击事件时没有视觉反馈。

示例如下：

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

### 区别和联系

// TODO


### TouchableHighlight

// TODO


### TouchableNativeFeedback

// TODO


### TouchableOpacity

// TODO


### TouchableWithoutFeedback

// TODO


### 补充说明

#### 长按

在一些场景下，你可能想要检测用户是否长时间按住一个视图。这些长按的操作可以通过任何 `Touchables` 相关组件的 `onLongPress` 方法属性进行处理。

#### 双指缩放

如果在 `ScrollView` 中只放置一个组件，则可以用来实现缩放操作。设置其 `maximumZoomScale` 和 `minimumZoomScale` 属性即可以使用户能够缩放其中的内容。

#### 在列表中上下滑动和在视图上左右滑动

用户会在列表中或快或慢的各种滑动。`ScrollView` 组件可以满足这一需求。`ScrollView` 可以在垂直或水平方向滚动，还可以配置 `pagingEnabled` 属性来让用户整屏整屏的滑动。此外，水平方向的滑动还可以使用 `Android` 上的 `ViewPagerAndroid` 组件。

`ListView` 则是一种特殊的 `ScrollView`，用于显示比较长的垂直列表。它还可以显示分区块的头部和尾部，类似 `iOS` 上的 `UITableView` 控件。

#### 处理其他的手势

如果你想实现视图的拖拽，或是实现自定义的手势，可参阅 [PanResponder API](https://facebook.github.io/react-native/docs/panresponder.html) 或 [Gesture Responder System](https://facebook.github.io/react-native/docs/gesture-responder-system.html) 文档。


## PanResponder API

### 概述 

`PanResponder` 类可以将多点触摸操作协调成一个手势。它使得一个单点触摸可以接受更多的触摸操作，也可以用于识别简单的多点触摸手势。

默认情况下 `PanResponder` 会通过 `InteractionManager` 来阻止长时间运行的 JS 事件打断当前的手势活动。

它提供了一个对触摸响应系统响应器的可预测的包装。对于每一个处理函数，它在原生事件之外提供了一个新的 `gestureState` 对象：

```js
onPanResponderMove: (event, gestureState) => {}
```

原生事件是指由以下字段组成的合成触摸事件：

* `nativeEvent`
  * `changedTouches` - 在上一次事件之后，所有发生变化的触摸事件的数组集合（即上一次事件后，所有移动过的触摸点）
  * `identifier` - 触摸点的 ID
  * `locationX` - 触摸点相对于父元素的横坐标
  * `locationY` - 触摸点相对于父元素的纵坐标
  * `pageX` - 触摸点相对于根元素的横坐标
  * `pageY` - 触摸点相对于根元素的纵坐标
  * `target` - 触摸点所在的元素 ID
  * `timestamp` - 触摸事件的时间戳，可用于移动速度的计算
  * `touches` - 当前屏幕上的所有触摸点的集合

一个 `gestureState` 对象有如下的字段：

* `stateID` - 触摸状态的 ID。在屏幕上有至少一个触摸点的情况下，这个 ID 会一直有效。
* `moveX` - 最近一次移动时的屏幕横坐标
* `moveY` - 最近一次移动时的屏幕纵坐标
* `x0` - 当响应器产生时的屏幕坐标
* `y0` - 当响应器产生时的屏幕坐标
* `dx` - 从触摸操作开始时的累计横向路程
* `dy` - 从触摸操作开始时的累计纵向路程
* `vx` - 当前的横向移动速度
* `vy` - 当前的纵向移动速度
* `numberActiveTouches` - 当前在屏幕上的有效触摸点的数量

基本用法示例如下：

```js
componentWillMount: function() {
  this._panResponder = PanResponder.create({
    // 要求成为响应者：
    onStartShouldSetPanResponder: (evt, gestureState) => true,
    onStartShouldSetPanResponderCapture: (evt, gestureState) => true,
    onMoveShouldSetPanResponder: (evt, gestureState) => true,
    onMoveShouldSetPanResponderCapture: (evt, gestureState) => true,

    onPanResponderGrant: (evt, gestureState) => {
      // 开始手势操作。给用户一些视觉反馈，让他们知道发生了什么事情！

      // gestureState.{x,y} 现在会被设置为0
    },
    onPanResponderMove: (evt, gestureState) => {
      // 最近一次的移动距离为gestureState.move{X,Y}

      // 从成为响应者开始时的累计手势移动距离为gestureState.d{x,y}
    },
    onPanResponderTerminationRequest: (evt, gestureState) => true,
    onPanResponderRelease: (evt, gestureState) => {
      // 用户放开了所有的触摸点，且此时视图已经成为了响应者。
      // 一般来说这意味着一个手势操作已经成功完成。
    },
    onPanResponderTerminate: (evt, gestureState) => {
      // 另一个组件已经成为了新的响应者，所以当前手势将被取消。
    },
    onShouldBlockNativeResponder: (evt, gestureState) => {
      // 返回一个布尔值，决定当前组件是否应该阻止原生组件成为JS响应者
      // 默认返回true。目前暂时只支持android。
      return true;
    },
  });
},

render: function() {
  return (
    <View {...this._panResponder.panHandlers} />
  );
},
``` 

### 实际应用示例

// TODO


## 手势响应系统

### 基础知识

// TODO


### 使用示例

// TODO