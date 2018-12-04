# Android 中的进程与线程

* 当某个应用组件启动且该应用没有运行其他任何组件时，Android 系统会使用单个执行线程为应用启动新的 Linux 进程。
* 如果某个应用组件启动且该应用已存在进程（因为存在该应用的其他组件），则该组件会在此进程内启动并使用相同的执行线程。
* 但是，您可以安排应用中的其他组件在单独的进程中运行，并为任何进程创建额外的线程。
* 默认情况下，同一应用的所有组件在相同的进程和线程（称为“主”线程）中运行。

## 进程
在 AndroidManifest.xml 文件中，`<activity>` `<service>` `<receiver>` 和 `<provider>` 均支持 `android:process` 属性，通过该属性，你可以指定该逐渐应在哪个进程运行，比如均在各自的进程中运行，或部分组件共享一个进程。

此外，`<application>` 元素也是支持 `android:process` 属性的，用来设置适用于所有组件的默认值。

> `android:process` 属性支持使不同应用的组件在相同的进程中运行，但前提是这些应用共享相同的 Linux 用户 ID 并使用相同的整数进行签署。

当内存紧张时，Android 可能会决定在某一时刻关闭某一进程，进程中运行的应用组件也会随之销毁。


## 进程生命周期

Android 在内存紧张要关闭某一进程时，会优先关闭重要程度低的进程。

重要性层次结构一共有 5 层，如下（由高到底）：

1. **前台进程**  
用户当前操作所必须的进程（满足一下任一条件即是）：  
  * 托管着正在交互的 `Activity` (已调用其 `onResume()` 方法)
  * 托管着某个已经绑定到用户正在交互的 `Activity` 的 `Service`
  * 托管着正在 "前台" 执行的 `Service` (已调用 `startForeground()`)
  * 托管着正在执行一个生命周期回调的 `Service` (`onCreate()`、`onStart()` 或 `onDestroy()`)
  * 托管着正在执行 `onReceive()` 方法的 `BroadcastReceiver`


2. **可见进程**
虽然没有任何前台组件但仍然影响用户屏幕上所见内容的继承（满足一下任一条件即是）  
  * 托管着不在前台但仍对用户可见的 `Activity` (以调用其 `onPause()` 方法)。例如，前台 `Activity` 弹出了一个对话框，且其后显示着上一个 `Activity`
  * 托管着编订到可见(或前台) `Activity` 的 `Service`


3. **服务进程**  
正在运行已使用 `startService()` 方法启动的服务。


4. **后台进程**  
包含目前对用户不可见 `Activity` 的进程（已调用其 `onStop()` 方法）。通常会有很多后台进程在运行，因此它们会保存在 LRU (最近最少使用) 列表中。如果某个 `Activity` 正确实现了生命周期方法并保存了其当前状态，则终止其进程不会对用户体验产生明显影响，因为当用户导航回该 `Activity` 时会恢复其所有可见状态。


5. **空进程**  
不包含任何活动应用组件的进程。通常来说，保留这种进程的唯一目的是用作缓存，以缩短下次再其中运行组件所需的启动时间。


此外，一个进程的级别可能会因其它进程对它的依赖而有所提高，即服务于另一进程的进程，其级别永远不会低于其所服务的进程。

例如，"进程 A 中的内容提供程序为进程 B 中的客户端提供服务" 或 "进程 A 中的服务绑定到进程 B 中的组件"，则进程 A 始终被视为至少和进程 B 同等重要。


## 线程

应用启动时，系统会为应用创建一个名为「主线程」的执行线程，负责将事件分发给相应的用户界面小组件。此外，该线程也是应用与 Android UI 工具包组件（来自 `android.widget` 和 `android.view` 软件包的组件）进行交互的线程。因此，也被称为「UI 线程」。


运行于同一进程的所有组件均在 UI 线程中实例化，并且均由该线程分发对每个组件的系统调用。因此，响应系统回调的方法始终在进程的 UI 线程中运行（如报告用户操作的 `onKeyDown()` 或生命周期回调方法）。

一旦 UI 线程执行耗时很长的操作时导致被阻塞时，将无法分发任何事件（包括屏幕绘制事件），用户就会感觉到卡顿、无响应。如果超过大约 5 秒钟，就会导致系统弹出 "应用无响应" (ANR) 对话框。

此外，Android UI 工具包并非 "线程安全" 工具包，你不能通过工作线程去修改 UI，而只能通过 UI 线程去修改。因此，Android 中的单线程模式必须遵守两条规则：

1. 不要阻塞 UI 线程
2. 不要在 UI 线程之外访问 Android UI 工具包


## 工作线程

通常，我们会在工作线程中执行时间长的操作，然后再通过 UI 线程将结果呈现在页面上。Android 提供了几种途径来从其它线程访问 UI 线程：

* `Activity.runOnUiThread(Runnable)`
* `View.post(Runnable)`
* `View.postDelayed(Runnable, long)`

但如果操作复杂的话，这类代码会变得难以维护，所以 Android 还支持通过 `Handler` 处理来自 UI 线程的消息，但最好的解决方法是扩展 `AsyncTask` 去实现。


## 线程安全方法

> Android 利用远程过程调用 (RPC) 提供了一种进程间通信 (IPC) 机制，通过这种机制，由 Activity 或其他应用组件调用的方法将（在其他进程中）远程执行，而所有结果将返回给调用方。 这就要求把方法调用及其数据分解至操作系统可以识别的程度，并将其从本地进程和地址空间传输至远程进程和地址空间，然后在远程进程中重新组装并执行该调用。 然后，返回值将沿相反方向传输回来。 Android 提供了执行这些 IPC 事务所需的全部代码，因此您只需集中精力定义和实现 RPC 编程接口即可。

> 要执行 IPC，必须使用 bindService() 将应用绑定到服务上。如需了解详细信息，请参阅服务开发者指南。


## 参考文档

* [进程和线程](https://developer.android.com/guide/components/processes-and-threads)