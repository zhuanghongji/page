# React Native - Text

`Text` 是一个用来显示文本的组件，并支持嵌套、样式和触摸事件处理。


## 示例

在下面的示例中，演示了一个嵌套的 `title` 和 `body` 文本组件，并且继承了来自 `styles.baseText` 的 `fontFamily` 字体样式，但其中 `title` 文本组件使用了额外的 `styles.titleText` 样式。

示例代码：

```js
import React, {Component} from 'react';
import {
  StyleSheet,
  View,
  Text
} from 'react-native';


export default class App extends Component<Props> {
  constructor(props) {
    super(props)
    this.state = {
      titleText: "Bird's Nest",
      bodyText: 'This is not really a bird nest.'
    }
  }

  onPressTitle() {
    this.setState({bodyText: 'You click the title text.'});
  }

  render() {
    return (
      <View style={styles.container}>
        // 增加这个 Text 是为了方便对比字体显示效果
        <Text style={styles.titleText} onPress={this.onPressTitle.bind(this)}>
          {this.state.titleText}{'\n'}
        </Text>

        <Text style={styles.baseText}>
          <Text style={styles.titleText}>
            {this.state.titleText}{'\n'}
          </Text>
          <Text numberOflines={5}>
            {this.state.bodyText}
          </Text>
        </Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center'
  },
  baseText: {
    fontFamily: 'open-sans-regular'
  },
  titleText: {
    fontSize: 20,
    fontWeight: 'bold'
  }
});
```

示例效果图：

![](./res/1.png)

> 注：在 Android 平台下需要在 `/assets/fonts` 目录下放一个同名字体文件，即 `open-sans-regular.ttf`。

要注意下 `onPress` 的写法，以下两种方式是错误的：

```jsx
// 错误写法一：点击后提示 “undefined is not a function ...”
<Text 
  style={styles.titleText} 
  onPress={this.onPressTitle}
>
  {this.state.titleText}{'\n'}
</Text>

/*
 错误写法二：页面无法加载，提示
  Invariant Violation: Maximun update depth exceeded. 
  This can hanppen when a component repeatedly calls setState inside componentWillUpdate or componentDidUpdate.
  React limits the number of nested updates to prevent infinite loops.

  大致意思就是发生了“死循环”，代码 `this.onPressTitle()` 是函数的执行而不是绑定。
  页面在渲染时会执行这个函数，因为该函数体执行了 setState，导致又重新渲染，接着又重复执行了个函数...
*/  
<Text 
  style={styles.titleText} 
  onPress={this.onPressTitle()}
>
  {this.state.titleText}{'\n'}
</Text>
```


## 嵌套文本

iOS 和 Android 都允许你通过 "标识一段字符串" 来使其显示特定的样式，比如加粗或显示特定颜色等（类似 iOS 中的 `NSAttributedString` 和 Android 中的 `SpannableString`）。在 React Native 来说，可以通过嵌套 `Text` 来实现该效果。

示例代码：

```js
<Text style={{fontWeight: 'bold'}}>
  I am bold {}
  <Text style={{color: 'red'}}>
    and red
  </Text>
</Text>
```

示例效果图：

![](./res/2.png)

本质上，React Native 是通过将上述代码转换为包含以下信息的 `NSAttributedString` 或 `SpannableString` 来实现的：

```js
"I am bold and red"
0-9: bold
9-17: red
```


## 嵌套 View

在 iOS 平台中，你可以在 `Text` 组件中嵌套 `View` 比如：

```js
<Text style={{fontWeight: 'bold'}}>
  There is a blue square {}
  <View style={{
    width: 50,
    height: 50,
    backgroundColor: 'blue'
  }}/>
  {} in between my text.
</Text>
```

![](./res/3.png)  

但是，这在 Android 中是会直接报错的，如下图：  

![](./res/4.png)  


## 布局容器

`Text` 元素相对布局而言是特殊的，其内部的一切都不再使用 flexbox 布局。这意味着 `Text` 的内部元素不再是长方形的了，超过末尾时可以进行换行。

示例代码：

```js
<Text>
  <Text style={{fontWeight: 'bold'}}>
    There is long long long text.
  </Text>
  <Text style={{fontWeight: 'bold'}}>
    Ha ha ha ha ha ha ha ...............{'\n'}
  </Text>
</Text>

<View>
  <Text style={{fontWeight: 'bold'}}>
    There is long long long text.
  </Text>
  <Text style={{fontWeight: 'bold'}}>
    Ha ha ha ha ha ha ha ...............
  </Text>
</View>
```

示例效果图：

![](./res/5.png)


## 限制的样式继承

在 Web 开发中，为整个 document 设置字体和大小的常用方法是利用继承的 CSS 属性，如下所示：

```css
html {
  font-family: 'lucida grande', tahoma, verdana, arial, sans-serif;
  font-size: 11px;
  color: #141823;
}
```

然后，这个 document 中的所有元素都会继承该样式除非它们的父元素指定了新的样式规则。

而在 React Native 中，文本的显示与样式相对 Web 来说是更加严格的：
* 你必须将所有的文本内容放在 `<Text>` 组件内部，不能直接放在 `<View>` 组件内部。

```js
// BAD: will raise exception,
// can't have a text node as child of a <View>
<View>
  Some text
</View>

// GOOD
<View>
  <Text>
    Some text
  </Text>
</View>
```

此外，你还失去了为整个子树设置默认字体的能力。同时，与 CSS 中的 `font-family` 不同的是，`fontFamily` 只接受一个字体名称。要在应用程序中使用一致的字体和大小，推荐的方法是创建一个包含这些字体和大小的组件 `MyAppText`，并在应用程序中使用这个组件来显示文本。当然，你也可以基于 `MyAppText` 组件来为其它类型文本创建更具体的组件，比如 `MyAppHeaderText`。

```js
// 使用 MyAppText 和 MyAppHeaderText 的姿势大致是这样的：
<View>
  <MyAppText>
    Text styled with the default font for the entire application
  </MyAppText>
  <MyAppHeaderText>Text styled as a header</MyAppHeaderText>
</View>
```

假设 `MyAppText` 是一个组件，一个只是将其子组件呈现为带有样式的文本组件，那么可以将 `MyAppHeaderText` 定义如下:

```js
class MyAppHeaderText extends Component {
  render() {
    return (
      <MyAppText>
        <Text style={{fontSize: 20}}>{this.props.children}</Text>
      </MyAppText>
    );
  }
}
```

以这种方式来组合 `MyAppText` 可以确保我们从顶级组件获得样式，同时也允许我们在特定的场景中 "添加" 或 "覆盖" 它们。

React Native 仍然有样式继承的概念，但仅限于文本子树。比如下面示例中的第二部分文字将会同时应用到粗体和红色。

```js
<Text style={{fontWeight: 'bold'}}>
  I am bold
  <Text style={{color: 'red'}}>and red</Text>
</Text>
```

官方相信，这种更受约束的文本样式将产生更好的应用程序:
* React 组件在设计时考虑到了强大的隔离性：
  * 你应该能够在应用程序的任何地方删除组件；
  * 只要相信这些属性是相同的，它的外观和行为就会是相同的；
  * 可以从外部继承的文本属性将打破这种隔离。
* React Native 的实现得到了简化：
  * 我们不需要在每个元素上都有一个 `fontFamily` 字段；
  * 也不需要每次显示文本节点时都遍历树直到根。
  * 样式继承仅在本机 Native 组件内部编码，不会泄漏到其它组件或系统本身。


## 常用属性

**`selectable`**

决定用户是否可以长按选择文本，以便进行复制和粘贴。

<br/>


**`ellipsizeMode`**

省略模式，当设置了 `numberOfLines` 时，可结合该参数定义了文字会如何被如何截短。其可能值有：
* `'head'`：该行末尾适应，开头的缺失文本由省略号表示，比如 "...wxyz"。
* `'middle'`：该行头尾适应，中部缺失文本由省略号表示，比如 "ab...yz"。
* `'tail'`：该行头部适应，末尾的缺失文本由省略号表示，比如 "abcd..."。
* `'clip'`：Lines are not drawn past the edge of the text container, only work on iOS.

默认值为 `tail`。

<br/>


**`nativeID`**

Used to locate this view from native code.

<br/>


**`numberOfLines`**

用于限制文本行数，通常会与 `ellipsizeMode` 一起使用，在计算文本布局后通过省略号截短文本 (包括折叠产生的换行在内），总的行数不会超过这个属性的控制。

<br/>


**`onLayout`**

类型为函数，在挂载或布局发生变化时会被调用，参数为：`{nativeEvent: {layout: {x, y, width, height}}}`。

<br/>


**`onLongPress`**

当文本被长按时会回调的函数，比如：`onLongPress={this.increaseSize}>`。

<br/>


**`onPress`**

当文本被点击时会回调的函数，比如：`onPress={() => console.log('1st')}`。

<br/>


**`pressRetentionOffset`**

> 官方文档：When the scroll view is disabled, this defines how far your touch may move off of the button, before deactivating the button. Once deactivated, try moving it back and you'll see that the button is once again reactivated! Move it back and forth several times while the scroll view is disabled. Ensure you pass in a constant to reduce memory allocations.
> 
> 类型: `object: {top: number, left: number, bottom: number, right: number}`

<br/>


**`allowFontScaling`**

指定字体是否需要随着给定样式的限制而自动缩放，默认为 `true`。

注：可通过该属性来实现 "RN 页面文字不随系统字体大小变化而变化" 的效果，另外，`TextInput` 也有这个属性噢。

<br/>


**`style`**

与常见的 `ViewStyle` 不同，`Text` 的 style 类型为 `TextStyle`，挑些特别的讲下：
* `fontWeight`: 字重，可选值有 'normal', 'bold', '100', '200', ... '900'。
* `lineHeight`: 行高。
* `textAlign`:  文本对齐方式，可选值有 'auto', 'left', 'right', 'center', 'justify'。注意，其中 `'justify'` 仅支持 iOS，Android 上会变成 `'left'`。
* `fontFamily`: 用于指定字体，中文字体文件体积太大了，不建议使用。
* `includeFontPadding`: Android 在默认情况下会为文字额外保留一些 padding，以便留出空间摆放上标或是下标的文字。对于某些字体来说，这些额外的 padding 可能会导致文字难以垂直居中。如果你把 `textAlignVertical` 设置为 `'center'` 之后，文字看起来依然不在正中间，那么可以尝试将本属性设置为 `false`。
* ...

> 有意思的是：在实际使用中发现 `textAlign` 和 `textAlignVertical` 在 iOS 并不生效，需要包一层 `View` 来解决对齐问题。

<br/>


**`selectionColor`**

用于指定选中文本的高亮色.

<br/>


**`textBreakStrategy`**

enum('simple', 'highQuality', 'balanced')

Set text break strategy on Android API Level 23+, possible values are simple, highQuality, balanced The default value is highQuality.

<br/>


**`adjustsFontSizeToFit`**

指定字体是否随着给定样式的限制而自动缩放，仅适用于 iOS 平台。

<br/>


**`minimumFontScale`**

当 `adjustsFontSizeToFit` 启用时，指定字体的最小缩放比（0.01 - 1.00），仅适用于 iOS 平台。

<br/>


**`suppressHighlighting`**

如果将其设置为 `true` 值，那么文本被按下时没有任何视觉效果。默认情况下，文本被按下时会有一个灰色的、椭圆形的高光。


## 常用方法

这个没有，这个真没有。



## 总结

这篇文章主要是介绍 `Text` 组件的基本使用，有个已知问题时：嵌入式 `Text` 组件不支持 `numberOfLines` 属性，详情可见 [react-native#22811](https://github.com/facebook/react-native/issues/22811)。