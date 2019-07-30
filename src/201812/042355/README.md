# React Native - 图片

本文主要包含以下内容：

* [静态的图片资源](#静态的图片资源)
* [静态的非图片资源](#静态的非图片资源)
* [使用混合应用中的图片资源](#使用混合应用中的图片资源)
* [网络图片](#网络图片)
* [网络图片的请求参数](#网络图片的请求参数)
* [Uri 数据图片](#uri-数据图片)
* [缓存控制 (仅 iOS)](#缓存控制-仅-iOS)
* [本地文件系统中的图片](#本地文件系统中的图片)
* [为什么不在所有情况下都自动指定尺寸](#为什么不在所有情况下都自动指定尺寸)
* [资源属性是一个对象](#资源属性是一个对象)
* [背景图片与嵌套写法](#背景图片与嵌套写法)
* [iOS 边框圆角的注意事项](#ios-边框圆角的注意事项)
* [在主线程外解码图片](#在主线程外解码图片)

## 静态的图片资源

React Native 提供了一个统一的方式来在 iOS 和 Android 应用中管理图片和其它媒体资源。在你的应用中添加添加一张图片，可以放在源代码结构的某个位置，然后通过类似下面的代码来引用它：

```js
// 测试1：未设置宽高
<Image source={require('./my-icon.png')}/>

// 测试2：假设长宽都为 240
<Image
  style={{width: 240, height: 240}}
  source={require('./my-icon.png')}
/>
```

| my-icon.png | ios | Android | ios 240 | Android 240
|--|--|--|--|--
| ![](./res/my-icon.png) | ![](./res/my-icon-ios.png) | ![](./res/my-icon-android.png) | ![](./res/my-icon-240-ios.png) | ![](./res/my-icon-240-android.png)


图片的查找方式与 JS 模块的查找方式一样。在上面的示例代码中，packager 会在与当前 require `my-icon.png` 图片的组件的相同目录下去查找该图片资源。与此同时，如果你在对应目录下存在 `my-icon.ios.png` 和 `my-icon.android.png` 文件，那么 packager 会根据当前平台去选择其中一张正确的。

你也可以使用 `@2x` 和 `@3x` 后缀来为不同密度的屏幕提供图片。如果你的代码结构如下：

```
.
├── button.js
└── img
    ├── check@2x.png
    └── check@3x.png
```

并且 `button.js` 代码含有：

```js
<Image source={require('./img/check.png')} />
```

则 packager 会打包并根据屏幕密度提供对应的图片资源。举个例子，在 iPhone7 上会使用 `check@2x.png` 而在 iPhone7 Plus 或 Nexus5 上使用 `check@3x.png`。如果没有对应屏幕密度的图片资源，则自动会选择最接近对应的那张。

在 Windows 上，你在添加新的图片资源到项目中时，可能需要重启下 packager。

下面是你得到的一些益处：

1. 该系统在 iOS 和 Android 上是一致的。
2. 图片放在与 JavaScript 代码一致的目录下，包含在了组件内部。
3. 没有全局命名空间，不需要担心命名冲突。
4. 只有实际上应用到的图片才会打包到你的应用中。
5. 添加和修改图片资源不需要重新编译应用，只需跟平常一样刷新下模拟器就可以了。
6. packager 知道图片的尺寸，不需要在代码中重复声明。
7. 可以通过 [npm](https://www.npmjs.com/) 包管理器来发布图片资源。

为了使该图片资源机制正常工作，require 中的图片名字必须是一个静态字符串。

> 不能使用变量，因为 require 是在编译时期执行，而非运行时期执行！

```js
// 正确
<Image source={require("./my-icon.png")} />;

// 错误
var icon = this.props.active ? "my-icon-active" : "my-icon-inactive";
<Image source={require("./" + icon + ".png")} />;

// 正确
var icon = this.props.active
  ? require("./my-icon-active.png")
  : require("./my-icon-inactive.png");
<Image source={icon} />;
```

注意，通过这种方式引用的图片资源需要包含图片的尺寸信息 (宽度，高度)，如果你需要动态缩放图片 (例如通过 `flex`)，你可能需要手动在 style 属性中设置 `{ width: undefined, height: nulundefinedl}`。

## 静态的非图片资源

上面描述的require语法也可以用来静态地加载你项目中的声音、视频或者文档文件。支持大多数常见的文件类型，包括 `.mp3` `.wav` `.mp4` `.mov` `.htm` 和 `.pdf`，完整列表见 [packager defaults](https://github.com/facebook/react-native/blob/master/local-cli/util/Config.js#L68) 。

你可以通过创建一个包配置文件来添加对其它类型文件的支持，配置项完整列表见 [packager config file](https://github.com/facebook/metro/blob/master/packages/metro/src/defaults.js#L14-L44) 。

警告，视频必须使用绝对位置尺寸来代替 `flexGrow`，因为目前还未能从非图片资源中获取到尺寸信息。另外要说明的一点是，对于直接链接到 Xcode 或者 Android Assets 目录的视频，不存在该限制。

## 使用混合应用中的图片资源

如果你编写的是一个混合 App (一部分 UI 使用 React Native，而另一部分使用平台原生代码)，你是可以使用已经打包到 App 中的图片资源的。

对于放置在 Xcode asset 类目中或是在 Android drawable 目录中的图片资源，直接使用不带扩展名的图片名称即可。

我们先来在 iOS 和 Android 的子项目中添加一个命名为 `hybrid.png` 的图片：

![](./res/hybrid.png)

然后编写引用该图片资源的代码：

```js
<Image source={{ uri: "hybrid" }} style={{ width: 160, height: 160 }} />
```

执行 `react-native run-ios` 和 `react-native run-android` 重新编译并运行应用，显示结果如下：

| iOS | Android
| -- | --
| ![](./res/hybrid-ios.png) | ![](./res/hybrid-android.png)

另外，对于放置在 Android 的 assets 目录中的图片，可以使用 `asset:/` 前缀进行引用：

```js
<Image
  source={{ uri: "asset:/test.png" }}
  style={{ width: 160, height: 160 }}
/>
```

需要注意的是：这些做法并没有任何安全检查，你需要自己确保图片在应用中确实存在，而且还需要指定尺寸。

## 网络图片

大多数要在 App 中显示的图片并不能在编译的时候获得，又或者有时候需要动态载入图片以减少打包后的二进制文件的大小。这时与静态资源不同有点不同，你需要手动指定图片的尺寸。同时我们强烈建议你使用 https 以满足 [iOS App Transport Security](https://facebook.github.io/react-native/docs/running-on-device#app-transport-security) 的要求。

```js
// 正确
<Image source={{uri: 'https://facebook.github.io/react/logo-og.png'}}
       style={{width: 400, height: 400}} />

// 错误
<Image source={{uri: 'https://facebook.github.io/react/logo-og.png'}} />
```

示例，加载的图片是网上随意找的小猪佩奇：

```
<Image
  source={{uri: 'https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1532626236105&di=55a74815b7a1c001f9cb0980e9e717e2&imgtype=0&src=http%3A%2F%2Fimg.mp.itc.cn%2Fupload%2F20170406%2F89ebc087620343f2ac7ab8c2dd6aee0f_th.jpeg'}}
  style={{width: 400, height: 400}}
/>
```

| iOS | Android
| -- | --
| ![](./res/network-ios.png) | ![](./res/network-android.png)

## 网络图片的请求参数

你可以在 Image 组件的 source 属性中指定一些请求参数，如下面的示例：

```js
<Image
  source={{
    uri: "https://facebook.github.io/react/logo-og.png",
    method: "POST",
    headers: {
      Pragma: "no-cache"
    },
    body: "Your Body goes here"
  }}
  style={{ width: 400, height: 400 }}
/>
```

## Uri 数据图片

有时候你拿到的可能是图片编码后的数据，此时可以使用 `'data:'` 格式来显示该图片。与加载网络图片相同，你需要手动指定图片的尺寸。

> 建议仅对非常小的图片使用 base64 数据，比如一些小图标。

```js
// 记得指定宽高
<Image
  style={{
    width: 51,
    height: 51,
    resizeMode: Image.resizeMode.contain
  }}
  source={{
    uri:
      "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAADMAAAAzCAYAAAA6oTAqAAAAEXRFWHRTb2Z0d2FyZQBwbmdjcnVzaEB1SfMAAABQSURBVGje7dSxCQBACARB+2/ab8BEeQNhFi6WSYzYLYudDQYGBgYGBgYGBgYGBgYGBgZmcvDqYGBgmhivGQYGBgYGBgYGBgYGBgYGBgbmQw+P/eMrC5UTVAAAAABJRU5ErkJggg=="
  }}
/>
```

| iOS | Android
| -- | --
| ![](./res/data-ios.png) | ![](./res/data-android.png)

## 缓存控制 (仅 iOS)

在某些情况下你可能仅仅想展示一张已经在本地缓存的图片，例如一个低分辨率的占位符，直到高分辨率的图片可用。又或者你无所谓图片是否过时，而且也不在乎显示过时的图片，节省带宽相对更重要。缓存资源属性提供给了你控制网络层与缓存交互的方式。

* `default`: 使用原生平台默认策略。
* `reload`: URL 的数据将从原始地址加载。不使用现有的缓存数据。
* `force-cache`: 现有的缓存数据将用于满足请求，忽略其期限或到期日。如果缓存中没有对应请求的数据，则从原始地址加载。
* `only-if-cached`: 现有的缓存数据将用于满足请求，忽略其期限或到期日。如果缓存中没有对应请求的数据，则不尝试从原始地址加载，并且认为请求是失败的。

```js
<Image
  source={{
    uri: 'https://facebook.github.io/react/logo-og.png',
    cache: 'only-if-cached'
  }}
  style={{ width: 400, height: 400 }}
/>
```


## 本地文件系统中的图片

参考相册 [CameraRoll](https://facebook.github.io/react-native/docs/cameraroll) 这个例子来看如何使用在 `Images.xcassets` 以外的本地资源。

### 最合适的相册图片

iOS 会为同一张图片在相册中保存多个不同尺寸的副本。为了性能考虑，从这些副本中挑出最合适的尺寸显得尤为重要。对于一处 200x200 大小的缩略图，显然不应该选择最高质量的 3264x2448 大小的图片。如果恰好有匹配的尺寸，那么 React Native 会自动为你选好。如果没有，则会选择最接近的尺寸进行缩放，但也至少缩放到比所需尺寸大出 50%，以使图片看起来仍然足够清晰。这一切过程都是自动完成的，所以你不用操心自己去完成这些繁琐且易错的代码。


## 为什么不在所有情况下都自动指定尺寸

在浏览器中，如果你不给图片指定尺寸，那么浏览器会首先渲染一个 0x0 大小的元素占位，然后下载图片，在下载完成后再基于正确的尺寸来渲染图片。这样做的最大问题是 UI 会在图片加载的过程中上下跳动，使得用户体验非常糟糕。

在 React Native 中我们有意避免了这一行为。如此一来开发者就需要做更多工作来提前知晓远程图片的尺寸（或宽高比），但我们相信这样可以带来更好的用户体验。然而，从已经打包好的应用资源文件中读取图片（使用 `require('my-icon')` 语法）则无需指定尺寸，因为它们的尺寸在加载时就可以立刻知道。

比如这样一个引用 `require('my-icon')` 的实际输出结果可能是：

```js
{"__packager_asset":true,"uri":"my-icon.png","width":591,"height":573}
```


## 资源属性是一个对象

在 React Native 中，另一个值得一提的变动是我们把 `src` 属性改为了 `source` 属性，而且并不接受字符串，正确的值是一个带有 `uri` 属性的对象。

```js
<Image source={{ uri: "something.jpg" }} />
```

深层次的考虑是，这样可以使我们在对象中添加一些元数据 (metadata)。

假设你在使用 `require('./my-icon.png')`，那么我们就会在其中添加真实文件路径以及尺寸等信息（这只是举个例子，未来的版本中 require 的具体行为可能会变化）。此外这也是考虑了未来的扩展性，比如我们可能会加入精灵图（sprites）的支持：在输出 `{uri: ...}` 的基础上，我们可以进一步输出裁切信息 `{uri: ..., crop: {left: 10, top: 50, width: 20, height: 40}}`，这样理论上就可以在现有的代码中无缝支持精灵图的切分。

对于开发者来说，则可以在其中标注一些有用的属性，例如图片的尺寸，这样可以使图片自己去计算将要显示的尺寸（而不必在样式中写死）。请在这一数据结构中自由发挥，存储你可能需要的任何图片相关的信息。


## 背景图片与嵌套写法

开发者们常面对的一种需求就是类似 web 中的背景图 `background-image`。要实现这一用例，只需使用 `<ImageBackground>` 组件（其 props 与<Image>完全相同），然后把需要背景图的子组件嵌入其中即可。

也可能你并不需要使用 `<ImageBackground>`，因为它的实现其实非常简单，实质就是对图片使用了绝对定位。你可以阅读其源码然后思考你是否有更好更简单的布局方案。

```js
return (
  <ImageBackground source={...}>
    <Text>Inside</Text>
  </ImageBackground>
);
```

全屏背景图片的实例：

```js
<View style={{flex: 1}}>
  <ImageBackground
    source={require('./background.jpg')}
    style={{flex: 1, width: null, height: null, resizeMode: Image.resizeMode.contain}}
  >
    <Text style={{margin: 48, color: 'blue'}}>这是要显示的文本</Text>
  </ImageBackground>
</View>
```

| iOS | Android
| -- | --
| ![](./res/background-ios.png) | ![](./res/background-android.png)


## iOS 边框圆角的注意事项

请注意下列边框圆角样式目前在 iOS 的图片组件上还不支持：

* `borderTopLeftRadius`
* `borderTopRightRadius`
* `borderBottomLeftRadius`
* `borderBottomRightRadius`


## 在主线程外解码图片

图片解码有可能会需要超过一帧的时间。在 web 上这是页面掉帧的一大因素，因为解码是在主线程中完成的。然而在 React Native 中，图片解码则是在另一线程中完成的。在实际开发中，一般对图片还没下载完成时的场景都做了处理（添加 loading 等），而图片解码时显示的占位符只占用几帧时间，并不需要你改动代码去额外处理。
