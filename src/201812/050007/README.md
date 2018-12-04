# Alert

用于显示一个带有指定标题和内容的提示对话框。

支持指定一系列的按钮，点击任何按钮都会回调对应的 `onPress` 方法并关闭对话框。默认情况下，显示的提示对话框只带有一个 `OK / 确定` 按钮。

该 API 在 iOS 和 Android 上都可以使用，但如果你想显示一个对话框并让用户输入一些信息，那你可能需要使用 `AlertIOS`。

示例：

| iOS | Android
| -- | --
| ![](./res/ios.gif) | ![](./res/android.gif)

## iOS

在 iOS 上，你可以指定任意数量的按钮。每个按钮可以指定自己样式，which is one of 'default', 'cancel' or 'destructive'.

## Android

在 Android 上，最多可以指定三个按钮，这三个按钮可分别理解为三种状态：
* 中间态 `neutral`
* 消极态 `negative`
* 积极态 `positive`

在写代码时，如果你：
* 只指定一个按钮，那么它具有「积极态」的属性，比如 “确定”。
* 指定了两个按钮，则分别是「消极态」和「积极态」，比如 ”取消“ 和 “确定”。
* 指定了三个按钮，则分别是「中间态」、「消极态」和「积极态」，比如 “稍后再说”、“取消” 和 ”确定”。

在 Android 平台上，默认点击提示框的外面会自动取消提示框。如果你想阻止这个行为，可以提供一个额外参数 `{ onDismiss: () => {} }`。另外，也可以设置另外一个属性 `{ cancelable: false }` 来阻止。

官方代码片段：
```js
// Works on both iOS and Android
Alert.alert(
  'Alert Title',
  'My Alert Msg',
  [
    {text: 'Ask me later', onPress: () => console.log('Ask me later pressed')},
    {text: 'Cancel', onPress: () => console.log('Cancel Pressed'), style: 'cancel'},
    {text: 'OK', onPress: () => console.log('OK Pressed')},
  ],
  { cancelable: false }
)
```

## 索引

### 方法

#### alert()
```js
static alert(title, message?, buttons?, options?, type?)
```
