# 在 React Native 中如何构建项目和管理静态资源【译】

原文地址：[https://medium.freecodecamp.org/...](https://medium.freecodecamp.org/how-to-structure-your-project-and-manage-static-resources-in-react-native-6f4cfc947d92)

React 和 React Native 都只是框架，并没有规定我们应该如何去构建项目。这一切都取决你的个人品味和你正在进行的项目。

在这篇文章中，我们将介绍如何构建项目和管理本地资源。这当然不是一成不变的，你可以自由地采纳适合你的那部分。希望这对你有所帮助。

对于一个使用 `react-native init` 引导初始化的项目，我们只得到了一个基本结构。`ios` 目录用于 Xcode 项目，`android` 目录用于 Android 项目，另外 `index.js` 和 `App.js` 作为 React Native 的起点。

```
ios/
android/
index.js
App.js
```

作为一个在 Windows Phone，iOS 和 Android 三个平台开发过原生项目的人，我发现构建一个项目都可归结为按 **类型** 或 **功能** 分离文件。


## 类型 vs 功能

按类型分离文件，意味着我们是根据类型来组织文件：
* 如果是 component 类型，则有 container 和 presentational 文件。
* 如果是 Redux 类型，则有 action、reducer 和 store 文件。
* 如果是 View 类型，则有 JavaScript、HTML 和 CSS 文件。

### 按类型分组

```
redux
  actions
  store
  reducers
components
  container
  presentational
view
  javascript
  html
  css
```

在这种方式下，我们可以看到每个文件的类型，而且可以轻松地对某个文件类型运行脚本。这对所有的项目来说都是通用的，但这没有回答以下问题：
* 这个项目是做什么的？
* 这是一个新闻应用吗？
* 这是关于营养检测的？

根据文件类来构建项目是面向机器的，而不是面向人的。很多时候我们解决某个问题时，需要在多个目录中查找要修复的文件，这显得有点麻烦。同时，如果我们想在项目中将某个框架移除时也很痛苦，因为文件遍布在很多个地方。


### 按功能分组

更合理的解决方案是按功能来组织文件，与功能相关的文件应该放在一起，同时测试文件应该靠近源文件。查看 [这篇文章](https://medium.com/@JeffLombardJr/organizing-tests-in-jest-17fc431ff850) 以了解更多信息。

功能可以与登录、注册、入职或用户资料相关。功能可以包含子功能，只要它们是属于同一个流程的。如果我们想要转移某个子功能是很容易的，因为所有的相关文件是已经组织好在一起的。

我的一个基于功能的典型项目结构如下：

```
index.js
App.js
ios/
android/
src
  screens
    login
      LoginScreen.js
      LoginNavigator.js
    onboarding
      OnboardingNavigator    
      welcome
        WelcomeScreen.js
      term
        TermScreen.js
      notification
        NotificationScreen.js
    main
      MainNavigator.js
      news
        NewsScreen.js
      profile
        ProfileScreen.js
      search
        SearchScreen.js
  library
    package.json
    components
      ImageButton.js
      RoundImage.js
    utils
      moveToBottom.js
      safeArea.js
    networking
      API.js
      Auth.js
  res
    package.json
    strings.js
    colors.js
    palette.js
    fonts.js
    images.js
    images
      logo@2x.png
      logo@3x.png
      button@2x.png
      button@3x.png
scripts
  images.js
  clear.js
```

除了一些传统的文件 (`App.js`、`index.js`) 和目录 (`ios`、`android`)，我将所有的源文件放在了 `src` 目录。

在 `src` 内部：
* `res` 目录用于放置资源文件。
* `library` 目录用于放置跨功能使用的公共文件。
* `screens` 目录用于放置内容页面。


## 尽可能少的依赖

由于 React Native 严重依赖于大量的依赖项，在添加更多依赖时我想尽量做到清晰明了。在我的项目中，仅使用 `react-navigation` 进行导航。而且我并不喜欢 `redux`，因为它增加了不必要的复杂性。只有在你真正需要某个依赖项时再去添加，否则你得到更多的是麻烦而不是价值。

我喜欢 React 中的组件，组件是我们定义视图、样式和行为的地方。React 具有内联样式，这和使用 JavaScript 来定义脚本、HTML 和 CSS 一样，这与我们的目标是相符的。这就是我不使用 [样式组件](https://github.com/styled-components/styled-components) 的原因。因为样式仅仅是一个 JavaScript 对象，所以我们可以在 `library` 中共享一些样式。


## src

我喜欢 Android 多一些，所以我使用 `src` 和 `res` 命名来匹配它约定的文件夹。

`react-native init` 已经为我们设置好了 babel。但对于一个典型的 JavaScript 项目，在 `src` 文件夹中组织文件是比较好的。在我的 `electron.js` 应用 [IconGenerator](https://github.com/onmyway133/IconGenerator/tree/master/src) 中，我将源文件放置了 `src` 目录中。这不仅有助于文件组织，还有助于 babel 一次性转换整个文件夹。仅需要一个命令，一眨眼我就可以将 `src` 转换成 `dist`。

```
babel ./src --out-dir ./dist --copy-files
```


## screen

React 是基于组件的，有 [容器型的和表示型的组件](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0)。我们可以通过组合一些简单组件来构建更复杂组件，通常它们最终是以全屏的形式来展现。这在 Windows Phone 中称为 `Page`，在 iOS 中称为 `ViewController`，在 Android 中称为 `Activity`。而在整个 React Native 指导文档中经常提到的是 [screen](https://facebook.github.io/react-native/docs/navigation) 关键字：

> 移动应用很少是由单个页面 (screen) 组成的。管理多个页面的展现和过渡通常由所谓的导航器 (navigator) 来处理。


## index.js or not ？

每个页面可以视为某个功能的入口点。你可以将 `LoginScreen.js` 重命名为 `index.js` 然后通过 Node 模块功能进行引用：

> 模块不一定是文件。我们可以在 `node_modules` 目录下创建一个 `find-me` 目录并在其中放置一个 `index.js` 文件。这样 `require('find-me')` 相当于是使用该目录下的 `index.js` 文件。

所以我们可以仅仅使用 `import LoginScreen from './screens'` 来代替 `import LoginScreen from './screens/LoginScreen'`。

使用 `index.js` 对结果进行封装并提供公共接口，这是个人喜好。我自己更喜欢文件命名，因此得名 `LoginScreen.js`。


## Navigator

在 React Native 中，[react-navigation](https://github.com/react-navigation/react-navigation) 库似乎是处理导航的最佳选项。对于类似入职这样的功能，可能有很多个页面需要用堆栈导航器 (stack navigation) 进行管理，所以有了 `OnboardingNavigator`。

你可以将 Navigator 视为一个对子页面或功能进行分组的东西。因为我们是根据功能分组的，所以将 Navigator 放在功能文件夹中是合理的。基本上它看起来是这样的：

```js
import { createStackNavigator } from 'react-navigation'
import Welcome from './Welcome'
import Term from './Term'
const routeConfig = {
  Welcome: {
    screen: Welcome
  },
  Term: {
    screen: Term
  }
}
const navigatorConfig = {
  navigationOptions: {
    header: null
  }
}
export default OnboardingNavigator = createStackNavigator(routeConfig, navigatorConfig)
```


## 库

这在项目构建过程中最受争议的部分。如果你不喜欢 `library` 这个名字，你可以将其命名为 `utilities`，`common`，`citadel`，`whatever` ...

这不是为了给那些无处可放的文件准备的，它是我们放置许多功能会使用到的通用工具或组件的地方。比如原子组件、包装器、快速修改函数(quick fixes function)、网络和登录信息这些会被大量使用，并且很难将其放置到某个具体功能文件夹的东西。有时，我们需要务实一点(parctial)并把工作完成。

在 React Native 中，我们经常需要在多个页面中实现一个带有图像背景的按钮。下面的 `library/components/ImageButton.js` 就是一个简单的实现。`components` 目录用于放置可重用组件，有时也被称为 [原子组件](https://medium.com/joeydinardo/a-brief-look-at-atomic-components-39cbe71d38b5)。根据 React 的命名约定，第一个字母应该使用大写。

```js
import React from 'react'
import { TouchableOpacity, View, Image, Text, StyleSheet } from 'react-native'
import images from 'res/images'
import colors from 'res/colors'
export default class ImageButton extends React.Component {
  render() {
    return (
      <TouchableOpacity style={styles.touchable} onPress={this.props.onPress}>
        <View style={styles.view}>
          <Text style={styles.text}>{this.props.title}</Text>
        </View>
        <Image
          source={images.button}
          style={styles.image} />
      </TouchableOpacity>
    )
  }
}
const styles = StyleSheet.create({
  view: {
    position: 'absolute',
    backgroundColor: 'transparent'
  },
  image: {
  },
  touchable: {
    alignItems: 'center',
    justifyContent: 'center'
  },
  text: {
    color: colors.button,
    fontSize: 18,
    textAlign: 'center'
  }
})
```

如果我们想要将按钮放在底部，可以使用一个实用函数的防止代码重复。下面是 `library/utils/moveToBottom.js` :

```js
import React from 'react'
import { View, StyleSheet } from 'react-native'
function moveToBottom(component) {
  return (
    <View style={styles.container}>
      {component}
    </View>
  )
}
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'flex-end',
    marginBottom: 36
  }
})
export default moveToBottom
```


## 使用 package.json 来避免相对路径

在 `src/screens/onboarding/term/Term.js` 某处，我们可以使用相对路径进行导入操作：

```js
import moveToBottom from '../../../../library/utils/move'
import ImageButton from '../../../../library/components/ImageButton'
```

这些点点点简直亮瞎了我的双眼。因为我们需要认为地去计算 `..` 执行的数量，所以是很容易出错的。另外，如果我们移动了该功能的位置，有需要重新进行计算。

由于我们是打算在很多个地方使用 `library` 的，所以讲其作为绝对路径进行引用是更好的实践。在 JavaScript 中，单个问题通常有着上千种解决方案，在 Google 上进行快速搜索就可以发现大量解决该问题的库。但是我们并不需要其它的依赖，因为当前这个问题非常容易解决。

解决的方案是把 `library` 转好为 `module`，这样 `node` 就能找到它。添加 `package.json` 到任何文件夹使其成为节点 `module`。添加有以下内容的 `package.json` 文件到 `library` 目录：

```JSON
{
  "name": "library",
  "version": "0.0.1"
}
```

现在在 `Term.js` 中我们就可以很方便地进行导入操作，因为 `library` 现在已经是一个 `module` 了：

```js
import React from 'react'
import { View, StyleSheet, Image, Text, Button } from 'react-native'
import strings from 'res/strings'
import palette from 'res/palette'
import images from 'res/images'
import ImageButton from 'library/components/ImageButton'
import moveToBottom from 'library/utils/moveToBottom'

export default class Term extends React.Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.heading}>{strings.onboarding.term.heading.toUpperCase()}</Text>
        {
          moveToBottom(
            <ImageButton style={styles.button} title={strings.onboarding.term.button.toUpperCase()} />
          )
        }
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center'
  },
  heading: {...palette.heading, ...{
    marginTop: 72
  }}
})
```

## res

你可能对上面例子中的 `res/colors`， `res/strings`, `res/images` 和 `res/fonts` 感到有点意外。好吧，在前端项目中，我们通常会使用字体、本地化的字符串、颜色、图片和样式来风格化组件。JavaScript 是一个非常动态的语言，并且很容易在任何地方使用字符串类型。我们可以有横跨多个文件的一连串 `#00B75D` `color`，或作为多个 `Text` 组件 `fontFamily` 的 `Fira`。这也很容易出错并且难以重构。

让我们使用更友好对象来封装 `res` 文件夹内部资源的使用，它们看起来就像下面的示例：

### res/colors

```js
const colors = {
  title: '#00B75D',
  text: '#0C222B',
  button: '#036675'
}
export default colors
```

### res/strings

```js
const strings = {
  onboarding: {
    welcome: {
      heading: 'Welcome',
      text1: "What you don't know is what you haven't learn",
      text2: 'Visit my GitHub at https://github.com/onmyway133',
      button: 'Log in'
    },
    term: {
      heading: 'Terms and conditions',
      button: 'Read'
    }
  }
}
export default strings
```

### res/fonts

```js
const fonts = {
  title: 'Arial',
  text: 'SanFrancisco',
  code: 'Fira'
}
export default fonts
```

### res/images

```js
const images = {
  button: require('./images/button.png'),
  logo: require('./images/logo.png'),
  placeholder: require('./images/placeholder.png')
}
export default images
```

类似 `library`，我们想让 `res` 中的文件可以从任何地方访问，所以需要将其转变为一个 `module`。添加 `package.json` 文件到 `res` 目录中：

```js
{
  "name": "res",
  "version": "0.0.1"
}
```

这样我们就可以想普通模块那样访问资源文件：

```js
import strings from 'res/strings'
import palette from 'res/palette'
import images from 'res/images'
```

## 使用调色板(palette)来组织颜色、图片、字体

应用程序的设计应该是一致的。某些元素应该具有相同的外观和感觉，以避免它们混淆用户。例如，标题 `Text` 应该使用一种颜色、字体和文字大小，`Image` 组件应该使用相同的占位符图像。在 React Native 中，我们已经在 `const styles = StyleSheet.create({})` 使用了 `styles` 名称，所以这里我们使用了 `palette` 吧。

下面是我 `palette` 的简单实现，它定义了标题的常用样式和 `Text`：

```js
import colors from './colors'
const palette = {
  heading: {
    color: colors.title,
    fontSize: 20,
    textAlign: 'center'
  },
  text: {
    color: colors.text,
    fontSize: 17,
    textAlign: 'center'
  }
}
export default palette
```

然后我们可以这样的页面中进行使用：

```js
const styles = StyleSheet.create({
  container: {
    flex: 1,
    alignItems: 'center'
  },
  heading: {...palette.heading, ...{
    marginTop: 72
  }}
})
```

在这里我们使用了 **对象扩展运算符** 来合并 `palette.heading` 到我们自定义的样式对象中。这意味着我们可以通过 `styles` 使用了 `palette.heading`，同时也可以在此基础上定义更多样式。

如果我们要为多个品牌重新设置应用程序，可能会有多个调色板，这是一个非常强大的模式。


## 生成 images

在 `/src/res/images.js` 中，可以看到 `src/res/images` 中的每个图像都有对应的属性：

```js
const images = {
  button: require('./images/button.png'),
  logo: require('./images/logo.png'),
  placeholder: require('./images/placeholder.png')
}
export default images
```

如果手动来管理的话是非常繁琐的，而且如果图像命名发生变化时，我们必须更新 `images.js` 文件。换个思路，我们添加一个脚本使得可以基于图片文件来生成 `images.js` 文件。在项目的根目录添加文件 `/scripts/images.js`：

```js
const fs = require('fs')
const imageFileNames = () => {
  const array = fs
    .readdirSync('src/res/images')
    .filter((file) => {
      return file.endsWith('.png')
    })
    .map((file) => {
      return file.replace('@2x.png', '').replace('@3x.png', '')
    })
return Array.from(new Set(array))
}

const generate = () => {
  let properties = imageFileNames()
    .map((name) => {
      return `${name}: require('./images/${name}.png')`
    })
    .join(',\n  ')

const string = `const images = {
  ${properties}
}
export default images
`
fs.writeFileSync('src/res/images.js', string, 'utf8')
}
generate()
```

在 Node 中，一个很酷的事情就是我们可以访问 `fs` 模块，它非常擅长于文件处理。在这里，我们只是遍历图片，然后响应地更新 `/src/res/images.js` 文件。

每当我们增加或修改文件时，可以运行：

```
node scripts/images.js
```

我们也可以将该脚本声明在 `package.json` 内部：

```
"scripts": {
  "start": "node node_modules/react-native/local-cli/cli.js start",
  "test": "jest",
  "lint": "eslint *.js **/*.js",
  "images": "node scripts/images.js"
}
```

现在我们只要运行 `npm run images` 就可以获得最新的 `images.js` 资源文件。


## 如何自定义字体

React Native 有一些 [自定义字体](https://medium.com/react-native-training/list-of-available-react-native-fonts-ed78b48bd45e)，这对你的项目来说可能足够用了，但你也可以使用自己定义的字体。

需要注意的一点是，Android 使用的是字体文件的名称而 iOS 使用的是全名。你可以在 Font Book 应用中查看全名，也可以在运行的应用中查看：

```objc
for (NSString* family in [UIFont familyNames]) {
    NSLog(@"%@", family);

    for (NSString* name in [UIFont fontNamesForFamilyName: family]) {
        NSLog(@"Family name:  %@", name);
    }
}
```

对于要在 iOS 中注册的字体，我们需要在 `Info.plist` 中使用字体的文件名声明 `UIAppFonts` 。而对于 Android 来说，字体文件必须放置在 `app/src/main/assets/fonts`。

将字体文件命名为全名是一个好的实践。据说 React Native 会动态加载自定义字体，但防止


// TODO 


























@
