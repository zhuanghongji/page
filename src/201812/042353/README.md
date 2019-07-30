# React Native - 特定于平台的代码

当我们在构建一个跨平台应用时，总会想着重用更多的代码。但有时候难免需要针对不同的平台写一些专有代码，比如可能你希望分别为 iOS 和 Android 实现单独的可视化组件。

RN 提供两种方式以让你轻松的管理特定于平台的代码：
1. 使用 `Platform` 模块。
2. 使用「平台文件扩展名」- `platform-specific file extensions`

> 在官网文档中，部分组件可能具有 "只能在其中一个平台上运行的属性"。这些属性都使用了 `@platform` 进行标注，在网站旁边也有一个小徽章。

## Platform 模块

### `Platform.OS`

RN 提供了一个 `Platform` 模块，用于检测当前运行的平台，可在当组件只有一小部分代码需要针对平台进行定制的场景下使用。比如：

```js
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100,
});
```

`Platform.OS` 会在 iOS 上返回 `'ios'` 字符串，在 Android 上返回 `android` 字符串。

### `Platform.select`

`Platform` 模块还提供了一个 `select` 方法，用于从传入对象中返回对应当前平台的值，比如：

```js
import {Platform, StyleSheet} from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({  // 使用了扩展运算符
      ios: {
        backgroundColor: 'red',
      },
      android: {
        backgroundColor: 'blue',
      },
    }),
  },
});
```

在上述代码，在 iOS 和 Android 平台上都会返回 `flex: 1`，但
* 在 iOS 上会另外返回 `backgroundColor: 'red'`
* 在 Android 上会另外返回 `backgroundColor: 'blue'`

因为该方法接收严格的类型合法的参数，所以你也可以直接使用它来返回针对平台的组件，比如：

```js
const Component = Platform.select({
  ios: () => require('ComponentIOS'),
  android: () => require('ComponentAndroid'),
})();    // 注意：多了一对小括号

<Component />;
```

### `Platform.Version`

**检测 iOS 系统版本**

在 iOS 中，`Version` 是 `- [UIDevice systemVersion]` 的返回值，是当前系统版本的字符串。比如说，系统版本为 “10.3”。来看一段代码：

```js
import { Platform } from 'react-native';

const majorVersionIOS = parseInt(Platform.Version, 10);
if (majorVersionIOS <= 9) {
  console.log('Work around a change in behavior');
}
```

**检测 Android 系统版本**

在 Android 中，通过 `Platform` 模块可以获取当前系统的版本号：
```js
import { Platform } from 'react-native';

if (Platform.Version === 25) {
  console.log('Running on Nougat!');
}
```


## 使平台文件扩展名

当特定于平台的代码较为复杂时，我们应该考虑是否需要将这些代码分离到不同的文件中。当文件有 `.ios.` 或 `.android.` 扩展名时，RN 会根据当前运行的平台来 `require` 对应的组件。

假设你的项目中有以下两个文件：

```js
BigButton.ios.js
BigButton.android.js
```

你可以这样去 `require` 相对应的组件：

```js
const BigButton = require('./BigButton');
```

React Native 会根据当前运行的平台正确引入对应的组件。


## 总结

虽然 RN 提供了比较友好的语法来处理特定于某个平台的逻辑，以开发 RN 一年多的经验来看，确实不可避免会用到相关判断逻辑。但要记住一点，能不编写特定平台逻辑代码时，就不编写，尽可能保持统一。