# React Native - Slider

`Slider` 是 React Native 中一个内置的、用于在一个范围值内选择单个值的组件，但现在官方已不推荐直接使用该组件，而是推荐使用社区维护的 [react-native-community/react-native-slider](https://github.com/react-native-community/react-native-slider) 依赖库中的 `Slider` 组件，两者相差不大，但使用社区版的话显然更好一点。社区版的独立于 React Native 框架本身，出现问题能够快速进行更新。

因为本文是比较久前写的了，所以使用的是内置的 `Slider`。

## 示例

话不多说，上两颗栗子。

### 示例一：属性参数

```js
import React, {Component} from 'react';
import {
  StyleSheet,
  View,
  Text,
  Slider
} from 'react-native';

export default class App extends Component<Props> {
  render() {
    return (
      <View style={styles.container}>
        <View style={styles.row}>
          <Text style={styles.text}>默认样式</Text>
          <Slider style={styles.slide} value={0.3}/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>disabled</Text>
          <Slider style={styles.slide} value={0.3} disabled={true}/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>maximumTrackTintColor</Text>
          <Slider style={styles.slide} value={0.3} maximumTrackTintColor='green'/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>minimumTrackTintColor</Text>
          <Slider style={styles.slide} value={0.3} minimumTrackTintColor='red'/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>thumbTintColor - android</Text>
          <Slider style={styles.slide} value={0.3} thumbTintColor='red'/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>thumbImage - ios</Text>
          <Slider style={styles.slide} value={0.3} thumbImage={require('./res/images/thumb.png')}/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>trackImage - ios</Text>
          <Slider style={styles.slide} value={0.3} trackImage={require('./res/images/track.png')}/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>minimumTrackImage - ios</Text>
          <Slider style={styles.slide} value={0.3} minimumTrackImage={require('./res/images/mini.png')}/>
        </View>

        <View style={styles.row}>
          <Text style={styles.text}>maximumTrackImage - ios</Text>
          <Slider style={styles.slide} value={0.3} maximumTrackImage={require('./res/images/maxi.png')}/>
        </View>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'column',
    justifyContent: 'center',
    alignItems: 'center',
  },
  row: {
    flexDirection: 'row',
    justifyContent: 'flex-start',
    alignItems: 'center',
    paddingLeft:16,
  },
  text: {
    width: 96,
    justifyContent: 'flex-start'
  },
  slide: {
    width: 240,
    height: 36,
  },
  marginTop: {
    marginTop: 12,
  }
});
```

两个平台的运行效果图分别如下：

| ios | android
| -- | --
| ![](./res/ios.png) | ![](./res/android.png)


### 示例二：方法参数

```js
export default class App extends Component<Props> {
  constructor(props) {
    super(props)
    this.state = {
      value: 0,
      onValueChange: 0,
      onSlidingComplete: 0,
    }
  }

  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.marginTop}>回调方法</Text>
        <Text>onValueChange: {this.state.onValueChange}</Text>
        <Text>onSlidingComplete: {this.state.onSlidingComplete}</Text>

        // 不设置默认值，最小值默认为0，最大值默认为1
        <Slider
          style={styles.slide}
          onValueChange={(value) => {
            this.setState({onValueChange: value})
          }}
          onSlidingComplete={(value) => {
            this.setState({onSlidingComplete: value})
          }}
        />

        <Slider
          style={styles.slide}
          step={1}
          minimumValue={-5}
          maximumValue={20}
          value={this.state.value}
          onValueChange={(value) => {
            this.setState({onValueChange: value})
          }}
          onSlidingComplete={(value) => {
            this.setState({onSlidingComplete: value})
          }}
        />
      </View>
    );
  }
}
```

iOS 和 Android 两个平台的效果是一致的，如下动态图：  

![](./res/function.gif)


## 常用参数

**`style`**

如果你有用 TypeScript 写过 React Native 应用的话，可以将其理解为是 `ViewStyle`。如果没有用过的话，则可参考 `ViewStylePropTypes.js` 以获取更多信息。

```ts
/**
 * @see https://facebook.github.io/react-native/docs/view.html#style
 * @see https://github.com/facebook/react-native/blob/master/Libraries/Components/View/ViewStylePropTypes.js
 */
export interface ViewStyle extends FlexStyle, ShadowStyleIOS, TransformsStyle {
    backfaceVisibility?: "visible" | "hidden";
    backgroundColor?: string;
    borderBottomColor?: string;
    borderBottomEndRadius?: number;
    borderBottomLeftRadius?: number;
    borderBottomRightRadius?: number;
    borderBottomStartRadius?: number;
    borderBottomWidth?: number;
    borderColor?: string;
    borderEndColor?: string;
    borderLeftColor?: string;
    borderLeftWidth?: number;
    borderRadius?: number;
    borderRightColor?: string;
    borderRightWidth?: number;
    borderStartColor?: string;
    borderStyle?: "solid" | "dotted" | "dashed";
    borderTopColor?: string;
    borderTopEndRadius?: number;
    borderTopLeftRadius?: number;
    borderTopRightRadius?: number;
    borderTopStartRadius?: number;
    borderTopWidth?: number;
    borderWidth?: number;
    opacity?: number;
    testID?: string;
    /**
     * Sets the elevation of a view, using Android's underlying
     * [elevation API](https://developer.android.com/training/material/shadows-clipping.html#Elevation).
     * This adds a drop shadow to the item and affects z-order for overlapping views.
     * Only supported on Android 5.0+, has no effect on earlier versions.
     *
     * @platform android
     */
    elevation?: number;
}
```

<br/>


**`disabled`**

设为 `true` 时 `Slider` 不能滑动，默认为 `false`。

<br/>


**`maximumValue`**

`Slider` 的最大值，默认为 `1`。

<br/>


**`minimumTrackTintColor`**

"滑块" 左侧滑条的颜色值。

<br/>


**`minimumValue`**

`Slider` 的最小值，默认为 `0`。

<br/>


**`onSlidingComplete`**

不管值是否发生了变化，当用户释放 "滑块" (结束滑动) 时都会回调该方法，释放时当前的值会作为该方法的参数。

<br/>


**`onValueChange`**

在用户滑动 "滑块" 过程中会持续回调该方法。

<br/>


**`step`**

滑块的最小步长，这个值应该在 0 到 (maximumValue - minimumValue) 之间，默认值为 `0`。

<br/>


**`maximumTrackTintColor`**

"滑块" 右侧的滑条颜色值。

<br/>


**`value`**

"滑块" 的初始值，这个值应该在最小值和最大值（默认 0 - 1）之间，默认值是 `0`。

> 注意：这不是一个受约束的组件，也就是说，在用户操作后即使你不更新该属性的值，对应组件也不会还原到初始值。

<br/>


**`thumbTintColor`**

"滑块" 的前景色，仅适用于 Android 平台。

<br/>


**`maximumTrackImage`**

可指定一张静态图片来作为 "滑块" 右侧轨道的背景图，图片最中央的像素会被平铺直至填满轨道，仅适用于 iOS 平台。

<br/>


**`minimumTrackImage`**

同 `maximumTrackImage`，只不过指定的静态图片来作用于右侧轨道。

<br/>


**`thumbImage`**

可指定一张静态图片来为 "滑块" 本身设置一张静态图片，仅适用于 iOS 平台。

<br/>


**`trackImage`**

为整个轨道设置一张静态背景图，图片最中央的像素会被平铺直至填满轨道，仅适用于 iOS 平台。


## 常用方法

哈，这个真没有。


## 总结

这篇文章主要是介绍 `Slider` 组件的基本使用。

>注：在看属性介绍时，结合示例效果图味道更佳噢。