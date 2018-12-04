# Android 中的 Service（一）: 是什么

`Service`（服务）应用程序四大组件的一种，可在应用程序希望在不与用户交互的情况下执行长时间运行的操作，或者为其他应用程序提供功能使用。

**每个 `Service` 类必须在其包的 AndroidManifest.xml 中具有相应的 `<service>` 声明**，然后通过 `Context.startService()` 或 `Context.bindService()` 方法启动。

注意：`Service` 是在所在进程的主线程中运行的，如果想在其中做任何 CPU 密集型（如 MP3 播放）或阻塞（如网络）的操作，你应该产生自己的线程来完成。另外，`IntentService` 可作为 `Service` 的标准实现，它具有自己的线程，用于调度要完成的工作。

本文主要内容如下：

1. 什么是 Service
2. Service 的生命周期
3. 相关权限
4. 进程生命周期
5. 本地 Service 示例
6. 远程 Messenger Service 示例

## 什么是 Service

关于服务类的大多数混淆实际上围绕着「不是什么」：

* **`Service` 并不是一个分离的线程**。  
Service 对象本身并不意味着它就一定在自己的进程中运行。除非通过 `android:process` 进行了显示的指定，否则它与其所属应用程序的运行过程相同。

* **`Service` 并不是一个线程**。  
也并不意味着它的运行脱离了主线程（以避免应用程序不响应错误）。


Service 本身其实非常简单，主要提供了两个功能：

* **告诉系统它想要在后台执行的操作**。  
这对应于 `Context.startService()` 方法的调用，它要求系统安排 Service 的工作，直到 Service 停止或其他人明确停止为止。

* **将某些功能展示给其他应用程序**。  
这对应于 `Context.bindService()` 方法的调用，允许与 Service 进行长期连接以与其进行交互。

不管出于以上那种调用，当真正创建 Service 组件时，系统实际所做的一切都是实例化组件并在主线程上调用 `onCreate()` 方法及其它对应的回调函数。

> 注意：由于服务本身非常简单，你可以根据自己的需要，简单或复杂地与它进行交互：提供一个使用 AIDL 完整的可远程接口，将其视为你的本地 Java 对象然后直接调用其方法（如本地服务示例所示）。


## Service 的生命周期

### `Context.startService()`

当你通过 `Context.startService()` 启动一个服务时，系统会检索到该服务然后进行创建、调用其 `onCreate()` 方法（如果需要的话）、调用其带有入参的 `onStartCommand(Intent, int, int)`方法。

该服务会开始运行直到有人调用了 `Context.stopService()` 或 `stopSelf()` 方法。

> 注意，虽然每次调用 `Context.startService()` 方法，`onStartCommand()` 方法都会被调用，但实际上每个服务都只会存在一个实例。因此，不管你调用了多少次 `startService()` 方法，只需调用一次 `stopService()` 或 `stopSelf()` 方法，该服务就会停止。


### `Context.bindService()`

客户端可以通过 `Context.bindService()` 方法获得一个与 Service 的长连接。这中方式同样会调用 `onCreate()` 方法（如果需要的话），但不会调用 `onStartCommand()` 方法。客户端会获得一个从 `onBind(Intent)` 方法返回的 `IBinder` 对象，从而使得客户端可以对 Service 进行一些调用操作。


// 剩余内容，待完善

## 参考文档
* [官方索引：Service](https://developer.android.com/reference/android/app/Service)
