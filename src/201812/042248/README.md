# 关于 Activity 的几个最佳实践

## 知晓当前是在哪个 Activity

有时刚接手一份新代码或时间隔得太久，运行到某个页面时却想不起当前是哪个 `Activity`，这时我们可以在 `BaseActivity` 中添加一行代码，打印出当前的 `Activity` 类名：

```java
public abstract class BaseActivity extends AppCompatActivity {
    public static final String TAG = "BaseActivity";

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.i(TAG, getClass().getSimpleName());
    }
}
```

如果你觉得每次都要去看日志很麻烦，你可以在 GitHub 上搜索 **TopActivity** 找到一个可以通过悬浮窗显示 "当前手机中 `Activity` 栈栈顶的 `Activity` 的包名和类名" 的代码仓库。如果没有提供 apk 下载地址的话，可以自己 clone 并编译一个出来用。

以 [AndroidRocket](https://github.com/eicky/AndroidRocket) 为例，它的显示效果是这样的：  

![](./res/android-rocket.png)

> AndroidRocket 好像还支持显示当前对话框的包名和类名噢。 ^@^

## 统一监听 Activity 生命周期的变化

主要有两种方式进行监听：

1. 在 `Application` 中监听
2. 在 `BaseActivity` 中监听

### `Application` 中监听

示例代码：

```java
public class App extends Application {
    public static final String TAG = "App";

    @Override
    public void onCreate() {
        super.onCreate();
        registerActivityLifecycleCallbacks(new ActivityLifecycleCallbacks() {
            @Override
            public void onActivityCreated(Activity activity, Bundle savedInstanceState) {
                Log.i(TAG, activity.getClass().getSimpleName() + " - onActivityCreated");
            }

            @Override
            public void onActivityStarted(Activity activity) {
                Log.i(TAG, activity.getClass().getSimpleName() + " - onActivityStarted");
            }

            @Override
            public void onActivityResumed(Activity activity) {
                Log.i(TAG, activity.getClass().getSimpleName() + " - onActivityResumed");
            }

            @Override
            public void onActivityPaused(Activity activity) {
                Log.i(TAG, activity.getClass().getSimpleName() + " - onActivityPaused");
            }

            @Override
            public void onActivityStopped(Activity activity) {
                Log.i(TAG, activity.getClass().getSimpleName() + " - onActivityStopped");
            }

            @Override
            public void onActivitySaveInstanceState(Activity activity, Bundle outState) {
                Log.i(TAG, activity.getClass().getSimpleName() + " - onActivitySaveInstanceState");
            }

            @Override
            public void onActivityDestroyed(Activity activity) {
                Log.i(TAG, activity.getClass().getSimpleName() + " - onActivityDestroyed");
            }
        });
    }
}
```

打印日志：

```
I/App: MainActivity - onActivityCreated
I/App: MainActivity - onActivityStarted
I/App: MainActivity - onActivityResumed
```

### 在 `BaseActivity` 中监听

示例代码：

```Java
public abstract class BaseActivity extends AppCompatActivity {

    public static final String BASE_TAG = "BaseActivity";

    protected final String TAG = getClass().getSimpleName();

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.i(BASE_TAG, TAG + " - onCreate");
    }

    @Override
    protected void onStart() {
        super.onStart();
        Log.i(BASE_TAG, TAG + " - onStart");
    }

    @Override
    protected void onResume() {
        super.onResume();
        Log.i(BASE_TAG, TAG + " - onResume");
    }

    @Override
    protected void onPause() {
        super.onPause();
        Log.i(BASE_TAG, TAG + " - onPause");
    }

    @Override
    protected void onStop() {
        super.onStop();
        Log.i(BASE_TAG, TAG + " - onStop");
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        Log.i(BASE_TAG, TAG + " - onSaveInstanceState");
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        Log.i(BASE_TAG, TAG + " - onDestroy");
    }
}
```

打印日志：

```
I/BaseActivity: MainActivity - onCreate
I/BaseActivity: MainActivity - onStart
I/BaseActivity: MainActivity - onResume
```

「方式2」相比「方式1」显得更加灵活，不用频繁获取当前 `Activity` 的类名，而且还可以监听 `onRestart()` 、`onActivityResult()` 等其它生命周期的方法。

## 统一管理 Activity

先来看几个问题：

* 如何设计一个让应用随时退出的方案？  
* 如何随时随地获取当前 `Activity` 的实例？
* 如何关闭其它所有 `Activity` 但保留一个指定 `Activity` ?
* ...

我们可以设计一个 `Activity` 管理类，然后用一个专有集合对所有的 `Activity` 进行管理，对外暴露一些方法实现特定需求：

示例代码：

```java
// Activity 管理类
public class ActivityManager {

    private static List<Activity> sActivities = new ArrayList<>();

    public static void addActivity(Activity activity) {
        sActivities.add(activity);
    }

    public static void removeActivity(Activity activity) {
        sActivities.remove(activity);
    }

    @Nullable
    public static Activity getTopActivity() {
        if (sActivities.size() == 0) {
            return null;
        }
        return sActivities.get(sActivities.size() - 1);
    }

    public static void finishAll() {
        for (Activity activity : sActivities) {
            if (!activity.isFinishing()) {
              activity.finish();
            }
        }
    }

    // 注意堆污染
    @SafeVarargs
    public static void finishAllExcept(Class<? extends Activity>... excepts) {
        for (Activity activity : sActivities) {
            boolean isExcept = false;
            for (Class<? extends Activity> except : excepts) {
                if (except == activity.getClass()) {
                    isExcept = true;
                    break;
                }
            }
            if (!isExcept) {
                activity.finish();
            }
        }
    }
}

// Activity 基类
public abstract class BaseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityManager.addActivity(this);
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        ActivityManager.removeActivity(this);
    }
}

// 第一个 Activity
public class MainActivity extends BaseActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    public void gotoSecondActivity(View view) {
        startActivity(new Intent(this, SecondActivity.class));
    }
}

// 第二个 Activity
public class SecondActivity extends BaseActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);
    }

    // android:onClick
    public void getTopActivity(View view) {
        Activity activity = ActivityManager.getTopActivity();
        if (activity instanceof SecondActivity) {
            Toast.makeText(this,
                    "当前页面是 SecondActivity", Toast.LENGTH_LONG).show();
        }
    }

    public void finishAllExceptMainActivity(View view) {
        ActivityManager.finishAllExcept(MainActivity.class);
    }

    public void finishAll(View view) {
        ActivityManager.finishAll();
    }
}
```

示例演示：  

![](./activity-manager.gif)

## 启动 Activity 的最佳写法

通常，我们启动一个 Activity 时时这样写代码的：

```Java
Intent intent = new Intent(this, SecondActivity.class);
intent.putExtra("param1", "data1");
intent.putExtra("param2", "data2");
startActivity(intent);
```

这样写有个缺点，假设 `SecondActivity` 是你开发的，但现在你的小伙伴负责的功能可能需启动这个 `SecondActivity` 并传入一些数据。这时，你的小伙伴可能就需要去仔细阅读你的代码然后去理解 `param1` 和 `param2` 是个什么 gui 了。

其实我们只要换一种写法，就可以轻松解决上面的问题：

```Java
public class SecondActivity extends BaseActivity {

    public static final String INTENT_NAME_FIRST_NAME = "first_name";
    public static final String INTENT_NAME_SECOND_NAME = "second_name";

    public static void actionStart(Context context, String firstName,
                                   String secondName) {
        Intent intent = new Intent(context, SecondActivity.class);
        intent.putExtra(INTENT_NAME_FIRST_NAME, firstName);
        intent.putExtra(INTENT_NAME_SECOND_NAME, secondName);
        context.startActivity(intent);
    }

    /**
     * 启动 SecondActivity
     * @param context context
     * @param firstName 第一个名字
     * @param secondName 第二个名字
     * @param requestCode 请求码
     */
    public static void actionStartForResult(Context context, String firstName,
                                   String secondName, int requestCode) {
        Intent intent = new Intent(context, SecondActivity.class);
        intent.putExtra(INTENT_NAME_FIRST_NAME, firstName);
        intent.putExtra(INTENT_NAME_SECOND_NAME, secondName);
        ((Activity) context).startActivityForResult(intent, requestCode);
    }

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_second);

        Intent intent = getIntent();
        String firstName = intent.getStringExtra(INTENT_NAME_FIRST_NAME);
        String secondName = intent.getStringExtra(INTENT_NAME_SECOND_NAME);
        // ...
    }
}
```

养成一个良好的习惯，给你编写的每个活动都添加类似的启动方法，不仅让启动 Activity 变得非常简单，还可以增强代码的可读性。

## 设计良好的 BaseActivity 模板

`BaseActivity`:

```Java
public abstract class BaseActivity extends AppCompatActivity {

    public static final String BASE_TAG = "BaseActivity";

    // 统一上下文变量，避免重复声明或使用类似 MainActivity.this 的场景
    protected Context mContext;

    // 强制使用 Activity 的类名作为 TAG，也避免在每个具体 Activity 重复声明 TAG 常量
    protected final String TAG = getClass().getSimpleName();

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mContext = this;
        doBeforeSetContentView();
        setContentView(getLayoutResId());
        doAfterSetContentView();
        initView();
        initEvent();
    }

    /**
     * 在执行 {@link #setContentView(int)} 前先执行一些初始化操作
     * <p> (具体 Activity 可选择覆写或不覆写)
     */
    protected void doBeforeSetContentView() {
        Log.i(BASE_TAG, "doBeforeSetContentView");
    }

    /**
     * 在执行 {@link #setContentView(int)} 后再执行一些初始化操作
     * <p> (具体 Activity 可选择覆写或不覆写)
     */
    protected void doAfterSetContentView() {
        Log.i(BASE_TAG, "doAfterSetContentView");
    }

    /**
     * 强制具体 Activity 实现，获取 LayoutResId 供  {@link #setContentView(int)} 进行设置
     * <p> (强制具体 Activity 实现该方法)
     * @return LayoutResId
     */
    @LayoutRes
    protected abstract int getLayoutResId();

    /**
     * 在这里写你的 {@link #findViewById(int)}
     * <p> (强制具体 Activity 实现该方法)
     */
    protected abstract void initView();

    /**
     * 在这里写你的 {@link View#setOnClickListener(View.OnClickListener)} 等事件设置代码
     * <p> (强制具体 Activity 实现该方法)
     */
    protected abstract void initEvent();
}
```

测试代码：

```Java
public class MainActivity extends BaseActivity {

    private Button btnGotoSecondActivity;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
    }

    @Override
    protected void doBeforeSetContentView() {
        super.doBeforeSetContentView();
        Log.i(TAG, "doBeforeSetContentView");
    }

    @Override
    protected void doAfterSetContentView() {
        super.doAfterSetContentView();
        Log.i(TAG, "doAfterSetContentView");
    }

    @Override
    public int getLayoutResId() {
        return R.layout.activity_main;
    }

    @Override
    public void initView() {
        btnGotoSecondActivity = findViewById(R.id.btn_goto_second_activity);
    }

    @Override
    public void initEvent() {
        btnGotoSecondActivity.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                SecondActivity.actionStart(MainActivity.this, "Page", "Larry");
            }
        });
    }
}
```

打印日志：

```
I/BaseActivity: doBeforeSetContentView
I/MainActivity: doBeforeSetContentView
I/BaseActivity: doAfterSetContentView
I/MainActivity: doAfterSetContentView
```

同理，我们也可以根据需求设计一个统一的 `BaseFragment` 模板。
