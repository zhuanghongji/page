# 如何对 App 的前后台切换进行监听

**首先，为什么需要对前后台的切换进行监听？**  

举个场景，基本上每个 App 都会存储一段服务器下发下来的配置数据，以动态的控制 App 的一些行为。但是，很多时候我们并不知道该在什么时候去刷新这段配置数据。

简单设计的话，我们可以通过 "切换回前台" 和 "上一次刷新时间" 这两个因素来控制配置数据的刷新。比如说，App 被切换回前台且距离上一次刷新时间超过 1 小时就调用接口刷新配置数据。

当然，适用的场景还有很多，不再一一列举。

在 iOS 中，系统提供了前后台切换的监听回调方法：

```objc
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

   func applicationDidEnterBackground(_ application: UIApplication) {
     // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
     // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
   }

   func applicationWillEnterForeground(_ application: UIApplication) {
     // Called as part of the transition from the background to the active state; here you can undo many of the changes made on entering the background.
   }

   // ...
}
```

但是在 Android 中系统是没有提供前后台的监听回调方法的，那么我们能不能设计一个？  
答：废话！

**在 Android 中，我们可以通过对 Activity 某些生命周期的方法进行计数，然后实现一个前后台的监听器。**


## 分析

假设我们现在有两个 `Activiy` 分别是 A 和 B，那么这两个页面生命周期的一些关键回调大致如下：

```Java
//  A 被启动，或 A 进入前台  
A.onStart();
A.onResume();

// 从 A 进入到 B
A.onPause();
B.onStart();
B.onResume();
A.onStop();

// 从 B 返回到 A
B.onPause();
A.onStart();
A.onResume();
B.onStop();

// A 被关闭，或 A 进入后台
A.onPause();
A.onStop();
```

关于生命周期，记住两个关键点：

* 启动新页面：旧 Activiy 总是先 `onPause()`，新 Activity 才启动。
* 返回旧页面：当前 Activiy 总是先 `onPause()`，旧 Activiy 才 `onResume()`。

所以说，**我们应该尽可能地在 `onStop()` 中做一些操作，而不是 `onPause()`**。

从上面的分析中可知，`onStart()` 和 `onStop()` 可以看做是两个页面之间交叉调用的一组方法。也就是说：

* A 启动到 B，会先调用 `B.onStart()` 然后 `A.onStop()`
* B 返回到 A，会先调用 `A.onStart()` 然后 `B.onStop()`

利用这个特性，我们可以定义一个全局计数变量来记录前台页面的数量：

* 在所有 `Activiy.onStart()` 中计数变量 +1
* 在所有 `Activiy.onStop()` 中计数变量 -1

监听：

* 计数变量从 0 变 1，说明从后台进入到前台
* 计数变量从 1 变 0，说明从前台进入到后台


## 代码设计

设计一个 `前后台管理类` 来进行 “前台页面计数” 和 ”管理前后台监听器”：

```Java
public class ForeAndBackManager {

    // 前台页面数量
    private static int sActivityStartedCount = 0;

    private static List<OnForeAndBackListener> sOnForeAndBackListeners = new ArrayList<>();

    /**
     * 添加前后台监听器
     * @param listener 前后台监听器
     */
    public static void addOnForeBackListener(OnForeAndBackListener listener) {
        sOnForeAndBackListeners.add(listener);
    }

    /**
     * 删除前后台监听器
     * @param listener 前后台监听器
     */
    public static void removeOnForeBackListener(OnForeAndBackListener listener) {
        sOnForeAndBackListeners.remove(listener);
    }

    /**
     * 添加前后台监听器
     * @param listener 前后台监听器
     * @return 当前刚添加的前后台监听器
     */
    public static OnForeAndBackListener newOnForeAndBackListener(OnForeAndBackListener listener) {
        addOnForeBackListener(listener);
        return listener;
    }

    /**
     * 每当有 Activity started 时，调用此方法进行计数（加）
     */
    public static void onActivityStarted() {
        sActivityStartedCount++;
        if (sActivityStartedCount == 1) {
            for (OnForeAndBackListener listeners : sOnForeAndBackListeners) {
                listeners.onEnterForeground();
            }
        }
    }

    /**
     * 每当有 Activity stopped 时，调用此方法进行计数（减）
     */
    public static void onActivityStopped() {
        sActivityStartedCount--;
        if (sActivityStartedCount == 0) {
            for (OnForeAndBackListener listeners : sOnForeAndBackListeners) {
                listeners.onEnterBackground();
            }
        }
    }

    /**
     * 前后台监听器
     */
    public interface OnForeAndBackListener {
        /**
         * 进入前台
         */
        void onEnterForeground();

        /**
         * 进入后台
         */
        void onEnterBackground();
    }
}
```

有一点我们需要注意：  

>当我们需要在一个 `Activiy` 中监听前后台切换时，应该在 `onCreate()` 中添加监听器，然后在 `onDestroy()` 中移除监听器，以免造成内存泄漏。


## 范例设计

`ForeAndBackManager.onActivityStarted()` 和 `ForeAndBackManager.onActivityStopped()` 的调用可以设计在 `Application` 中，也可以设计在 `BaseActivity` 中。

下面的范例代码，是通过 `registerActivityLifecycleCallbacks()` 方法设计在 `Application` 中的：

```Java
public class App extends Application {

    public static final String TAG = "App";

    @Override
    public void onCreate() {
        super.onCreate();
        ForeAndBackManager.addOnForeBackListener(new ForeAndBackManager.OnForeAndBackListener() {
            @Override
            public void onEnterForeground() {
                Log.i(TAG, "onEnterForeground");
            }

            @Override
            public void onEnterBackground() {
                Log.i(TAG, "onEnterBackground");
            }
        });

        registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {

            @Override
            public void onActivityStarted(Activity activity) {
                ForeAndBackManager.onActivityStarted();
            }

            Override
            public void onActivityStopped(Activity activity) {
                ForeAndBackManager.onActivityStopped();
            }

            // ....
          });
    }
}
```

```java
public class MainActivity extends BaseActivity {

  private ForeAndBackManager.OnForeAndBackListener mOnForeAndBackListener;

  @Override
    protected void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
         mOnForeAndBackListener = new ForeAndBackManager.OnForeAndBackListener() {
             @Override
             public void onEnterForeground() {
                 Log.i(TAG, "onEnterForeground");
             }

             @Override
             public void onEnterBackground() {
                 Log.i(TAG, "onEnterBackground");
             }
         };
         ForeAndBackManager.addOnForeBackListener(mOnForeAndBackListener);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        ForeAndBackManager.removeOnForeBackListener(mOnForeAndBackListener);
    }
}
```

```java
public class SecondActivity extends BaseActivity {

  private ForeAndBackManager.OnForeAndBackListener mOnForeAndBackListener;

  @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        mOnForeAndBackListener = ForeAndBackManager.newOnForeAndBackListener(
                new ForeAndBackManager.OnForeAndBackListener() {
                    @Override
                    public void onEnterForeground() {
                        Log.i(TAG, "onEnterForeground");
                    }

                    @Override
                    public void onEnterBackground() {
                        Log.i(TAG, "onEnterBackground");
                    }
                });
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        ForeAndBackManager.removeOnForeBackListener(mOnForeAndBackListener);
    }
}
```

日志打印：

```
// 启动 App
I/App: onEnterForeground
I/MainActivity: onEnterForeground

// 从 MainActivity 跳转到 SecondActivity，再点击 Home 键
I/App: onEnterBackground
I/MainActivity: onEnterBackground
I/SecondActivity: onEnterBackground
```

至此，我们就完成了 Android 前后台的监听设计了。
