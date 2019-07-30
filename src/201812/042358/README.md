# React Native - ActivityIndicator

显示一个圆形的加载指示器。

## 示例
```js

import React, { Component } from 'react';
import {
  ActivityIndicator,
  StyleSheet,
  Text,
  View
} from 'react-native';

type Props = {};
export default class App extends Component<Props> {
  render() {
    return (
      <View style={[styles.container, styles.horizontal]}>
        <ActivityIndicator size='large' color='#0000FF' animating={true}/>
        <ActivityIndicator size='small' color='#00FF00'/>
        <ActivityIndicator size='large' color='#0000FF'/>
        <ActivityIndicator size='small' color='#00FF00'/>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
  },
  horizontal: {
    flexDirection: 'row',
    justifyContent: 'space-around',
    padding: 10,
  }
});
```

| iOS | Android
| -- | --
| ![](./res/ios.gif) | ![](./res/android.gif)


## 参数

### `animating`
是否显示指示器
  * `true`: 显示（默认值）
  * `false`: 不显示

### `color`
指示器的颜色（默认灰色）

### `size`
指示器的大小
  * `'small'`: 小（默认值）
  * `'large'`: 大
  * `number`: 通过数字指定大小，仅支持 Android 平台

### `hidesWhenStopped`
指示器动画停止时，是否隐藏指示器（默认为 `true`），仅支持 iOS 平台。

> 注：当 `animating` 和 `hidesWhenStopped` 同时为 `false` 时
* iOS 平台的指示器会暂停动画，但指示器本身还是可见的
* Android 平台指示器会直接被隐藏












·
