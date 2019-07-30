# 如何引入第三方 React Native npm 包

### 如何引入

在 React Native 是使用 JavaScript 语言编写代码的，有些功能使用纯 js 无法实现或很难实现，这时我们可以编写 React Native 原生模块进行混合编程以达到我们的目的。

对于一些常用的功能，可能官方或第三方已经做了实现并发布到 npm 服务器上，这时我们可以通过下面的命令进行安装：

```
npm install <package_name> --save
```

这个命令做了两件事：  
* 把 `<package_name>` 加入到了 `./package.json` 文件的依赖声明中
* 下载 `<package_name>` 代码到 `./node_modules` 目录下

一般来说，React Native npm 源代码的主要构成有如下几种情况：
1. 只有 React Native js 代码
2. 包含 React Native js 代码 和 iOS 原生代码
3. 包含 React Native js 代码 和 Android 原生代码
4. 包含 React Native js 代码 和 iOS & Android 原生代码

对于上述情况一，安装完 npm 源代码后就可以直接在 js 中使用了，其它情况就需要先根据对应文档进行配置后才能使用，具体可参考 [react-native-camera](https://github.com/react-native-community/react-native-camera) 包 README.md 说明。

注意：
* 如果你看了 react-native-camera 文档中的 install 部分。
* 你会看到 `react-native link react-native-camera` 这个命令，这个命令一般来说只会对直接用 `react-native init xxx` 初始化的项目有效。
* 如果你是在原生项目中嵌入 RN 的话，请尽可能根据文档中 "manual install" 的方式进行相关依赖的连接操作，以避免不可预知的问题。
* 如果想做得更好一点，可以直接进行源码依赖，这样如果第三方库有问题的话，可直接修改源代码进行修正，同时调试也比较方便。

### 版本管理相关

通常情况下，我们不会把 `node_modules` 目录下的代码提交到版本库中，新 clone 下来的代码可以通过执行 `npm install` 命令，根据 `package.json` 的依赖声明重新安装所需代码模块。

如果引入的第三方 npm 模块不能满足项目需求，需要进行一定修改后才能使用，此时建议创建一个新的 `libs` 目录来专门存放这些特殊模块的代码。


