# 全面了解 Android 中的广播机制（一）：基础知识

Android 应用可以发送和接受来自系统或其它应用的广播消息，有点类似「发布 - 订阅」设计模式。比如说：

* Android 系统会在完成启动、开始或结束充电、飞行模式切换等事件发生时发出对应的广播。
* Android 应用也本身也可以发送全局广播从而通知对该广播感兴趣的其它应用。

当广播被发送时，系统会自动路由该广播到已做相应订阅的应用中去。

从接受范围来看，广播可以分为以下两类:

* 全局广播：一个应用发送出去后，所有的应用都可以接受该广播。
* 本地广播：一个应用发送出去后，只有发送的应用本身才可以接受到。

从广播发送角度来看，广播可以分为以下两类：

* 标准广播：是完全异步执行的，所有对应的广播接收器几乎在同一时刻接收到，没有先后顺序，效率高，无法被截断。
* 有序广播：是同步执行的，同一时刻只有一个对应的广播接收器能收到，在该接收器相关逻辑执行完毕后其它接收器才能收到，优先级越高越先收到，当前接收器也是可以选择是否截断的。

> 一般来说，全局广播可以用作应用程序之间进行通信的一种机制。但如果滥用的可能导致手机性能下降，比如传说中的相互唤醒.....哈哈！

下面是一个「监听网络连接状态」的广播接收器例子：

```Java
class NetworkChangeReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        ConnectivityManager manager = (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
        NetworkInfo info = manager.getActiveNetworkInfo();
        String networkDesc;
        if (info != null && info.isAvailable()) {
            networkDesc = "network is available";
        } else {
            networkDesc = "network is unavailable";
        }
        Toast.makeText(context, networkDesc, Toast.LENGTH_SHORT).show();
    }
}
```

## 关于系统的广播

在 Android 7.0 及以上的系统中，系统不会再发出 `ACTION_NEW_PICTURE
` 和 `ACTION_NEW_VIDEO` 广播。这会影响到所有的 App，是不管你的应用是否是 target 向 Android 7.0 的。

对于 target 向 Android 7.0 (API 24) 的 App，如果你想注册 `Android 7.0` 广播，你需要通过 `registerReceiver(BroadcastReceiver, IntentFilter)` 方法动态注册，如果仅在 manifest 文件中注册将不会起作用。

> Beginning with Android 8.0 (API level 26), the system imposes additional restrictions on manifest-declared receivers. If your app targets API level 26 or higher, you cannot use the manifest to declare a receiver for most implicit broadcasts (broadcasts that do not target your app specifically).

## 接受广播

注册广播接收器有两种方式：

1. 在 `AndroidManifest.xml` 文件中声明注册。
2. 在 Java 代码通过 `Context` 进行注册。

我们先来创建一个继承自 `BroadcastReceiver` 并实现了 `onReceive(Context, Intent)` 方法的子类作为广播接收器：

```Java
// 在接受到对应广播后会打印出相应数据
public class MyBroadcastReceiver extends BroadcastReceiver {
    private static final String TAG = "MyBroadcastReceiver";
    @Override
    public void onReceive(Context context, Intent intent) {
        StringBuilder sb = new StringBuilder();
        sb.append("Action: " + intent.getAction() + "\n");
        sb.append("URI: " + intent.toUri(Intent.URI_INTENT_SCHEME).toString() + "\n");
        String log = sb.toString();
        Log.d(TAG, log);
        Toast.makeText(context, log, Toast.LENGTH_LONG).show();
    }
}
```

### Manifest-declared receivers

如果你在 manifest 文件中注册了广播接收器，当对应广播发送时系统会先启动你的应用 (如果不是正在运行)。

> Note: If your app targets API level 26 or higher, you cannot use the manifest to declare a receiver for implicit broadcasts (broadcasts that do not target your app specifically), except for a few implicit broadcasts that are exempted from that restriction. In most cases, you can use scheduled jobs instead.

在应用的 manifest 文件对应位置，通过 `<receiver>` 元素声明你的广播接收器：

```xml
<receiver android:name=".MyBroadcastReceiver"  android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="android.intent.action.INPUT_METHOD_CHANGED" />
    </intent-filter>
</receiver>
```

### Context-registered receivers

通过 `Context` 注册广播接受器，一般遵循以下几个步骤：

```Java
// 创建一个 BroadcastReceiver 实例
BroadcastReceiver br = new MyBroadcastReceiver();

// 创建一个 IntentFilter 后，再调用注册方法
IntentFilter filter = new IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION);
filter.addAction(Intent.ACTION_AIRPLANE_MODE_CHANGED);
this.registerReceiver(br, filter);

// 在不需要接受广播时，进行解注册
this.unregisterReceiver(br);
```

注意：上面的示例是注册和解注册一个全局广播，如果你的预期是本地广播的话，可使用 `LocalBroadcastManager.registerReceiver(BroadcastReceiver, IntentFilter)` 和 ` LocalBroadcastManager.unregisterReceiver(BroadcastReceiver)` 方法进行注册和解注册。

另外，请注意你注册和解注册广播接收器的时机：

* 如果你是在 `onCreate(Bundle)` 中注册接收器，那么你应该在 `onDestroy()` 中进行解注册，以防止导致 Activity 的内存泄漏。
* 如果你在是在 `onResume()` 中注册接收器，那么你应该在 `onPause()` 中进行解注册，以防止多次注册。

### 对进程状态的影响

`BroadcastReceiver` 的状态（无论是否正在运行）会影响其包含进程的状态，从而可能会增大其被系统杀死的可能性。例如，当一个进程正在执行一个接收器的 `onReceive()` 代码逻辑时，它会被认为是一个前台进程，系统会使得该进程可以保持运行，除非存在极大的内存压力。

但是，一旦你的代码在 `onReceive()` 中 `return`，`BroadcastReceiver` 就不再处于活动状态。接收器的 host 进程变得与其中运行的其他应用程序组件一样重要。如果该进程仅 host 清单文件中声明的接收器，则在 `onReceive()` 中 `return` 时，系统将其进程视为低优先级进程，并可能杀死它以使资源可用于其它更重要的进程。

出于这个原因，你不应该在广播接收器中进行长时间的后台运行线程。在 `onReceive()` 之后，系统可以随时终止进程以回收内存，并且这样做会终止在进程中运行的衍生线程。为了避免这种情况，你应该调用 `goAsync()` 或使用 `JobScheduler` 从接收器中调度 `JobService`，这样系统就知道该进程改在执行相关工作，从而争取更多时间在后台线程中处理广播相关逻辑。

下面的代码片段演示了一个 `BroadcastReceiver` 使用 `goAsync()` 来标记 `onReceive()` 完成后需要更多时间才能完成。如果你想在你的 `onReceive()` 中完成的工作足够长而不像让 UI 线程错过任何一帧（> 16ms），这样子是特别有用的，这使得它更适合后台线程。

```Java
public class MyBroadcastReceiver extends BroadcastReceiver {
    private static final String TAG = "MyBroadcastReceiver";

    @Override
    public void onReceive(final Context context, final Intent intent) {
        final PendingResult pendingResult = goAsync();
        AsyncTask<String, Integer, String> asyncTask = new AsyncTask<String, Integer, String>() {
            @Override
            protected String doInBackground(String... params) {
                StringBuilder sb = new StringBuilder();
                sb.append("Action: " + intent.getAction() + "\n");
                sb.append("URI: " + intent.toUri(Intent.URI_INTENT_SCHEME).toString() + "\n");
                Log.d(TAG, log);
                // Must call finish() so the BroadcastReceiver can be recycled.
                pendingResult.finish();
                return data;
            }
        };
        asyncTask.execute();
    }
}
```

## 发送广播

三种方式：

* `sendBroadcast(Intent)`: 发送标准广播，接受器的接受顺序是不定的。
* `sendOrderedBroadcast(Intent, String)`: 发送有序广播，接收器可以截断广播的发送。接收器的优先级是在 manifest 文件中的 `<intent-filter/>` 中定义的，比如：
```xml
<receiver android:name=".NetworkChangeReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="100">
        <action android:name="com.zhuanghongji.demo.MainActivity.action_test"/>
    </intent-filter>
</receiver>
```

* `LocalBroadcastManager.sendBroadcast`: 发送本地广播，只有在同一个应用中的接收器才能收到。**如果你发广播的目的不是为了跨应用通信，请使用本地广播**，因为这更加高效，且不用去担心任何相关的安全问题。

> 发送有序广播时，具有相同优先级的接收器将以任意顺序运行。

下面代码片段演示了如何发送和截断一个广播：

```Java
// 发送广播
Intent intent = new Intent();
intent.setAction("com.example.broadcast.MY_NOTIFICATION");
intent.putExtra("data","Notice me senpai!");
sendBroadcast(intent);

// 截断广播
private class CustomBroadcastReceiver extends BroadcastReceiver {
        @Override
        public void onReceive(Context context, Intent intent) {
            abortBroadcast();
        }
    }
}
```

广播携带的信息都被包含在 `Intent` 对象中，该对象的 `action` 字符串必须提供应用的 Java 包名语法并唯一标识广播事件。你可以使用 `putExtra(String，Bundle)` 将附加信息附加到 `Intent` 中，也可以通过在 `Intent` 上调用 `setPackage(String)` 将广播限制在同一组织中的一组应用程序。

> **Note**: Although intents are used for both sending broadcasts and starting activities with startActivity(Intent), these actions are completely unrelated. Broadcast receivers can't see or capture intents used to start an activity; likewise, when you broadcast an intent, you can't find or start an activity.

## 通过权限对广播进行限制

权限检查使得你可以限定拥有特定权限的应用才能发送或接受对应广播。

### 带权限发送
当你调用 `sendBroadcast(Intent, String)` 或 `sendOrderedBroadcast(Intent, String, BroadcastReceiver, Handler, int, String, Bundle)` 时，你可以指定权限参数，使得只有拥有该权限的应用的接收器才可以收到该广播，比如：
```Java
sendBroadcast(new Intent("com.example.NOTIFY"),
              Manifest.permission.SEND_SMS);
```

如果想接收该广播消息，应用就必须在请求获取该权限：
```xml
<uses-permission android:name="android.permission.SEND_SMS"/>
```

### 带权限接收
如果你通过以下任意一种方式在注册广播接收器是指定了权限参数
* `registerReceiver(BroadcastReceiver, IntentFilter, String, Handler)`
* `<receiver/>` 标签

那么广播发送者必须也要拥有对应的权限才能将响应 `Intent` 发送到该广播接收器中。举个栗子，假设接收器应用在 manifest 中做了以下声明：
```xml
<receiver android:name=".MyBroadcastReceiver"
          android:permission="android.permission.SEND_SMS">
    <intent-filter>
        <action android:name="android.intent.action.AIRPLANE_MODE"/>
    </intent-filter>
</receiver>
```

或在 Java 代码中是这样注册广播的：

```java
IntentFilter filter = new IntentFilter(Intent.ACTION_AIRPLANE_MODE_CHANGED);
registerReceiver(receiver, filter, Manifest.permission.SEND_SMS, null );
```
那么，要想将广播发送到上述的接收器中，发送就必须拥有以下权限：
```xml
<uses-permission android:name="android.permission.SEND_SMS"/>
```

## 安全考虑和最佳实践

以下是发送和接收广播的一些安全考虑事项和最佳做法：

* 如果你不需要将广播发送到应用以外的组件，则可以使用支持库中的 `LocalBroadcastManager` 发送和接收本地广播。

* 如果许多应用程序已注册接收清单中的相同广播，则可能会导致系统启动大量应用程序，从而对设备性能和用户体验产生重大影响。为了避免这种情况，优先使用清单声明上的注册。有时，Android 系统本身会强制使用 `Context` 注册的接收器。例如，`CONNECTIVITY_ACTION` 广播仅传递给 `Context` 注册的接收器。

* 不要使用隐式 Intent 广播敏感信息，任何注册的应用程序都可以接收广播以读取信息。有三种方法可以控制谁可以接收你的广播：
 * 在发送广播时指定权限。
 * 在 Android 4.0 及更高版本中，你可以在发送广播时使用 `setPackage(String)` 指定一个包名。系统将广播限制在与该包名相匹配的一组应用程序。
 * 使用 `LocalBroadcastManager` 发送本地广播。


* 当你注册接收器时，任何应用程序都可能将潜在的恶意广播发送到你应用的接收器。有三种方法可以限制你的应用收到的广播：

 * 你可以在注册广播接收机时指定权限。
 * 对于清单声明的接收者，你可以在清单中将 `android:exported` 属性设置为 `false`。接收器不接收来自应用程序外部的广播。
 * 使用 `LocalBroadcastManager` 将自己限制为本地广播。


* 广播操作的命名空间是全局的。确保 `action` 名称和其它字符串被写入你拥有的名称空间中，否则你可能会无意中与其它应用程序发生冲突。

* 因为接收者的 `onReceive(Context，Intent)` 方法在主线程上运行，所以它应该快速执行并返回。如果你需要执行长时间运行的工作，请注意产生线程或启动后台服务，因为系统会在 `onReceive()` 返回后终止整个进程。有关更多信息，请参阅前面提到的内容，建议：

* 在接收器的 `onReceive()` 方法中调用 `goAsync()` 并将 `BroadcastReceiver.PendingResult` 传递给后台线程。这使得从 `onReceive()` `return` 后的广播保持 active 状态。它确实允许你将工作移至另一个线程以避免妨碍主线程，但即使采用这种方法，系统也希望你能够很快完成广播（不到10秒）。

* 使用 `JobScheduler` 调度工作。有关更多信息，请参阅 [Intelligent Job Scheduling](https://developer.android.com/topic/performance/scheduling.html)。
请勿在广播接收器中启动 Activity，因为用户体验很不好，特别是如果有多个接收器的话，可考虑显示通知进行替代。

## 参考文档

* [Broadcasts overview](https://developer.android.com/guide/components/broadcasts#context-registered-receivers)
* [Android 限制广播消息的接收者](https://blog.csdn.net/mingli198611/article/details/17762149)
