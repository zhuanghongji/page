# React Native - Image

## 概述

`Image` 组件用于显示多种不同类型 (来源) 图片，包括网络图片、静态资源、临时的本地图片以及本地磁盘上的图片。

下面的例子分别演示了如何显示从本地缓存、网络甚至是以 `'data:'` 的 base64 uri 形式提供的图片。

> 注意：对于网络和 base64 数据的图片需要手动指定尺寸。

```js
import React, { Component } from 'react';
import { AppRegistry, View, Image } from 'react-native';

// 注：在 DisplayAnImage.js 文件同级目录放置了 favicon.png 文件
export default class DisplayAnImage extends Component {
  render() {
    return (
      <View>
        <Image source={require('./favicon.png')} />
        <Image
          style={{width: 50, height: 50}}
          source={{uri: 'https://facebook.github.io/react-native/docs/assets/favicon.png'}}
        />
        <Image
          style={{width: 66, height: 58}}
          source={{uri: 'data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg=='}}
        />
      </View>
    );
  }
}
```

显示效果如下图：

<img src="./res/001.png" width="240"/>

另外，你也可以给图片添加 `style` 属性：

```js
import React, { Component } from 'react';
import { StyleSheet, View, Image } from 'react-native';

const styles = StyleSheet.create({
  stretch: {
    width: 50,
    height: 200
  }
});

export class DisplayAnImageWithStyle extends Component {
  render() {
    return (
      <View>
        <Image
          style={styles.stretch}
          source={require('./favicon.png')}
        />
      </View>
    );
  }
}
```

此时，显示效果就变成了这样：

<img src="./res/002.png" width="240"/>


## 在 Android 上支持 GIF 和 WebP 格式图片

默认情况下，Android 平台不支持显示 GIF 和 WebP 格式图片。如有需要，可在 `android/app/build.gradle` 文件中根据需要手动添加以下模块：

```gradle
dependencies {
  // 如果你需要支持 Android4.0 (API level 14) 之前的版本
  compile 'com.facebook.fresco:animated-base-support:1.9.0'

  // 如果你需要支持 GIF 动图
  compile 'com.facebook.fresco:animated-gif:1.9.0'

  // 如果你需要支持 WebP 格式，包括 WebP 动图
  compile 'com.facebook.fresco:animated-webp:1.9.0'
  compile 'com.facebook.fresco:webpsupport:1.9.0'

  // 如果只需要支持 WebP 格式而不需要动图
  compile 'com.facebook.fresco:webpsupport:1.9.0'
}
```

> Fresco 是 Facebook 开源的一款 Android 图片加载库，支持显示 GIF 和 Webp (静态和动态) 格式的图片。


## 常用属性

**`style`**

`ImageResizeMode` is an `Enum` for different image resizing modes, set via the `resizeMode` style property on `Image` components. The values are `contain`, `cover`, `stretch`, `center`, `repeat`.

* 布局属性... *layout-props#props*
* 阴影属性... *shadow-props#props*
* 动画变换 Transforms... *transforms#props*
* `opacity`: number
* `overflow`: enum('visible', 'hidden')
* `resizeMode`: Object.keys(ImageResizeMode)
* `tintColor`: 为所有非透明的像素指定一个颜色。
* `overlayColor`: string (_Android_)
  当图片有圆角的时候，指定一个颜色用于填充圆角处的空白。虽然一般情况下圆角处是透明的，但在某些情况下，Android 并不支持圆角透明，比如：
  * 某些 resize 模式比如'contain'
  * GIF 动画
  * 常见的用法就是在不能圆角透明时，设置`overlayColor`和背景色一致。
  * 详细说明可参考<http://frescolib.org/rounded-corners-and-circles.md>。

<br/>


**`blurRadius`**

blurRadius(模糊半径)：为图片添加一个指定半径的模糊滤镜，类型为 `number`。

<br/>


**`onLayout`**

当元素加载或者布局改变的时候调用，参数为：`{nativeEvent: {layout: {x, y, width, height}}}`.

| 类型 | 必填 
| -- | -- 
| function | 否  

<br/>


**`onLoad`**

加载成功完成时调用此回调函数。

| 类型 | 必填 
| -- | --
| function | 否   

<br/>


**`onLoadEnd`**

加载结束后，不论成功还是失败，调用此回调函数。

| 类型 | 必填 
| -- | --
| function | 否   

<br/>


**`onLoadStart`**

在加载开始时回调该函数。

示例：`onLoadStart={(e) => this.setState({loading: true})}`


<br/>


**`resizeMode`**

决定当组件尺寸和图片尺寸不成比例的时候如何调整图片的大小。
* `cover`: 在保持图片宽高比的前提下缩放图片，直到宽度和高度都大于等于容器视图的尺寸（如果容器有 padding 内衬的话，则相应减去）。**译注**：这样图片完全覆盖甚至超出容器，容器中不留任何空白。
* `contain`: 在保持图片宽高比的前提下缩放图片，直到宽度和高度都小于等于容器视图的尺寸（如果容器有 padding 内衬的话，则相应减去）。**译注**：这样图片完全被包裹在容器中，容器中可能留有空白。
* `stretch`: 拉伸图片且不维持宽高比，直到宽高都刚好填满容器。
* `repeat`: 重复平铺图片直到填满容器。图片会维持原始尺寸，但是当尺寸超过容器时会在保持宽高比的前提下缩放到能被容器包裹。
* `center`: 居中不拉伸。

<br/>


**`source`**

图片源数据（远程 URL 地址或本地数据）。

This prop can also contain several remote URLs, specified together with their width and height and potentially with scale/other URI arguments. The native side will then choose the best `uri` to display based on the measured size of the image container. A `cache` property can be added to control how networked request interacts with the local cache.

目前原生支持的图片格式有 `png`、`jpg`、`jpeg`、`bmp`、`gif`、`webp` (仅 Android)、`psd` (仅 iOS)。

| 类型 | 必填 
| -- | -- 
| ImageSourcePropType | 否  

<br/>

**`loadingIndicatorSource`**

Similarly to `source`, this property represents the resource used to render the loading indicator for the image, displayed until image is ready to be displayed, typically after when it got downloaded from network.

| 类型 | 必填 
| -- | -- 
| array of ImageSourcePropTypes, number | 否   

> Can accept a number as returned by `require('./image.jpg')`

<br/>


**`onError`**

当加载错误的时候调用此回调函数，参数 `{nativeEvent: {error}}`。

<br/>


**`resizeMethod`**

当图片实际尺寸和容器样式尺寸不一致时，决定以怎样的策略来调整图片的尺寸，仅支持 Android 平台。默认为`auto`。
* `auto`: 使用启发式算法来在`resize`和`scale`中自动决定。
* `resize`: 在图片解码之前，使用软件算法对其在内存中的数据进行修改。当图片尺寸比容器尺寸大得多时，应该优先使用此选项。
* `scale`: 对图片进行缩放。和`resize`相比，`scale`速度更快（一般有硬件加速），而且图片质量更优。在图片尺寸比容器尺寸小或者只是稍大一点时，应该优先使用此选项。

> 关于 `resize` 和 `scale` 的详细说明请参考 `http://frescolib.org/docs/resizing.html`。

<br/>


**`accessibilityLabel`**

设置一段文字。当用户与图片交互时，读屏器（无障碍功能）会朗读你所设置的这段文字。

| 类型 | 必填 | 平台 
| -- | -- | -- 
| node | 否 | iOS  

<br/>


**`accessible`**

当此属性为 true 时，表示此图片是一个启用了无障碍功能的元素。

| 类型 | 必填 | 平台 
| -- | -- | -- 
| bool | 否   | iOS  

<br/>


**`capInsets`**

当图片被缩放的时候，capInsets 指定的角上的尺寸会被固定而不进行缩放，而中间和边上其他的部分则会被拉伸。这在制作一些可变大小的圆角按钮、阴影、以及其它资源的时候非常有用（译注：这就是常说的九宫格或者.9 图。了解更多信息，可以参见[苹果官方文档](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImage_Class/index.html#//apple_ref/occ/instm/UIImage/resizableImageWithCapInsets)。

| 类型 | 必填 | 平台 
| -- | -- | -- 
| object: {top: number, left: number, bottom: number, right: number} | 否 | iOS  

<br/>


**`defaultSource`**

在读取图片时默认显示的图片。仅限 iOS 使用。

| 类型           | 必填     | 平台     |
| -------------- | -------- | -------- |
| object, number | 否       | iOS      |
| -------------- | -------- | -------- |
| number         | 否       | Android  |

If passing an object, the general shape is `{uri: string, width: number, height: number, scale: number}`:

* `uri` - 是一个表示图片的资源标识的字符串，它可以是一个本地文件路径或是一个静态资源引用（使用`require(相对路径)`来引用）。
* `width`, `height` - 如果你知道图片的尺寸，那么可以在这里指定。这一尺寸会被用作`<Image/>`组件的默认尺寸。
* `scale` - 图片的缩放系数。默认是 1.0，意味着每一个图片像素都对应一个设备独立像素（DIP）。
* `number` - 静态图片引用语法`require('./image.jpg')`所返回的资源 id。

**Android**: It works only on release builds, don't worry if it shows nothing on DEBUG builds

<br/>

**`onPartialLoad`**

如果图片本身支持逐步加载，则逐步加载的过程中会调用此方法。“逐步加载”的具体定义与具体的标准和实现有关。

| 类型     | 必填 | 平台 |
| -------- | ---- | ---- |
| function | 否   | iOS  |

<br/>


**`onProgress`**

在加载过程中不断调用，参数为 `{nativeEvent: {loaded, total}}`。

| 类型 | 必填 | 平台 
| -- | -- | -- 
| function | 否 | iOS  

<br/>


**`fadeDuration`**

Android only. By default, it is 300ms.

| Type | Required | Platform 
| -- | -- | -- 
| number | No | Android 

<br/>


## 常用方法

**`getSize()`**

```js
Image.getSize(uri, success, [failure]);
```

在显示图片前获取图片的宽高(以像素为单位)。如果图片地址不正确或下载失败,此方法也会失败。

要获取图片的尺寸,首先需要加载或下载图片(同时会被缓存起来)。这意味着理论上你可以用这个方法来预加载图片，虽然此方法并没有针对这一用法进行优化，而且将来可能会换一些实现方案使得并不需要完整下载图片即可获取尺寸。所以更好的预加载方案是使用下面那个专门的预加载方法。

此方法不能用于静态图片资源。

参数：

| 名称 | 类型 | 必填 | 说明 
| -- | -- | -- | -- 
| uri     | string   | 是 | 图片地址             
| success | function | 是 | 成功的回调函数，返回图片宽高数据 
| failure | function | 否 | 失败的回调函数     

<br/>


### `prefetch()`

```js
Image.prefetch(url);
```

预加载一个远程图片(将其下载到本地磁盘缓存)。

参数：

| 名称 | 类型 | 必填 | 说明 
| -- | -- | -- | -- 
| url  | string | 是 | 图片的远程地址 

<br/>


**`abortPrefetch()`**

```js
Image.abortPrefetch(requestId);
```

中断预加载操作。仅 Android 可用。

参数：

| 名称 | 类型 | 必填 | 说明 
| -- | -- | -- | -- 
| requestId | number | 是   | `prefetch()` 返回的 id 

<br/>


**`queryCache()`**

```js
Image.queryCache(urls);
```

查询图片缓存状态。根据图片 URL 地址返回缓存状态，比如 "disk"（缓存在磁盘文件中）或是 "memory"（缓存在内存中）。

参数：

| 名称 | 类型  | 必填 | 说明
| -- | -- | -- | -- |
| urls | array | 是   | 要查询缓存状态的图片 URL 数组。 |

<br/>


**`resolveAssetSource()`**

```js
Image.resolveAssetSource(source);
```

Resolves an asset reference into an object which has the properties `uri`, `width`, and `height`.

参数：

| 名称 | 类型 | 必填 | 说明   
| -- | -- | -- | -- 
| source | number, object | 是   | 静态图片引用语法 `require('./image.jpg')`所返回的资源 id 或是一个 `ImageSource`。 

> `ImageSource` 是一个对象，其结构为 `{ uri: '<http location || file path>' }`


## 总结

这篇文章主要..
