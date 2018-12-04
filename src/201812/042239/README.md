# Deep links

在Android 6.0之前，Android中有个技术名词 “Deep Links”。
比如说一个购物 APP，你在别人的网页中投放了一个广告链接，希望用户在点击这个网页时，可以直接跳转到你的 App 的页面进行下单。

实际上，这是个 “与其他应用交互” 的过程，允许其他应用启动你的 `Activity`。  
要允许其他应用启动你的 `Activity`，你需要在 AndroidManifest.xml 文件中为对应的 `<Activity>` 元素添加一个 `<intent-filter>` 元素。

当你的应在安装在手机上时，系统会识别你的 Intent 过滤器并添加信息至所有已安装应用支持的 Intent 内部目录。当应用通过隐式 `Intent` 调用 `startActivity() `或 `startActivityForResult()` 时，系统会找到可以响应该 `Intent` 的 `Activity`。

## 添加 Intent 过滤器

先看一段添加 `Intent` 过滤器的示例代码：

```xml
<activity android:name="ShareActivity">
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="text/plain"/>
        <data android:mimeType="image/*"/>
    </intent-filter>
</activity>
```

上面这段代码声明之后，`ShareActivity` 可以处理 “数据类型为文本或图像，且 `Action` 为 `ACTION_SEND`时” 的 `Intent`。  
我们在添加 `Intent` 过滤器的时候，应该将 `action`、`category`、`data` 描述得尽可能具体些，这样能最大程度上保证是我们 `Activity` 想要处理的 `Intent`。

**action** ：对要执行操作的命名字符串，通常是Android平台定义的值之一。  

`Intent` 类里就定义了很多个常量值，如：
* `ACTION_SEND` = "android.intent.action.SEND";
* `ACTION_VIEW` = "android.intent.action.VIEW";

**category** ：提供临一中表征处理 `Intent` 的 `Activity` 的方法，通常与用户手势或 `Activity` 启动的位置有关。系统支持多种不同的类别，但大多数都很少用。  

比如：

* CATEGORY_DEFAULT = "android.intent.category.DEFAULT";  
* CATEGORY_BROWSABLE = "android.intent.category.BROWSABLE";
* CATEGORY_APP_EMAIL = "android.intent.category.APP_EMAIL"  

但是，所有隐式Intent默认使用 `CATEGORY_DEFAULT` 进行定义。

**data** : 与 `Intent` 关联的数据描述。该元素有多个属性，你可以指定 MIME 类型、URI 前缀、URI 架构或这些数据的组合。

> **注**：如果你无需声明关于数据的具体信息URI。比如你的Activity处理MIME类型数据而不是URI，你应只指定 `android:mimeType`属性，如 `text/plain`或`image/jpeg`。示例代码可见上面的代码块。

每个 `Intent` 仅指定一个 `action` 和一个 `data`，但是可以在每个 `<intent-filter>` 中声明 `<action>`、`<category>` 和 `<data>` 的多个实例。  

在 java 代码中创建隐式 `Intent` 的代码：

```java
Intent intent = new Intent();
intent.setAction(Intent.ACTION_SEND);

Uri uri = Uri.parse("http://www.zhuanghongji.com/test");
intent.setData(uri);
intent.setDataAndType(uri, "text/plain");

intent.addCategory(Intent.CATEGORY_BROWSABLE);
intent.addCategory(Intent.CATEGORY_APP_MUSIC);
intent.removeCategory(Intent.CATEGORY_APP_CONTACTS);
// 可以发现，其实是没有addAction()或addData()这两个方法的
// 但是确有addCategory()和removeCategory()方法
```

如果任何两对操作和数据的行为互斥，你应创建单独的Intent过滤器进行指定。  
假定你的Activity同时处理 `ACTION_SEND`和`ACTION_SENDTO` Intent的文本和图像。  
在这种情况下，你必须为两个操作定义两种不同的Intent过滤器。例如：

```xml
<activity android:name="ShareActivity">
    <!-- filter for sending text; accepts SENDTO action with sms URI schemes -->
    <intent-filter>
        <action android:name="android.intent.action.SENDTO"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:scheme="sms" />
        <data android:scheme="smsto" />
    </intent-filter>
    <!-- filter for sending text or images; accepts SEND action and text or image data -->
    <intent-filter>
        <action android:name="android.intent.action.SEND"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <data android:mimeType="image/*"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
</activity>
```

> **注**：为了接收隐含 `Intent`，你必须在 `Intent` 过滤器中包含 CATEGORY_DEFAULT 类别。方法 `startActivity()` 和 `startActivityForResult()` 将按照已声明 CATEGORY_DEFAULT 类别的方式处理所有 `Intent`。如果你不在 `Intent` 过滤器中声明它，则没有隐含 `Intent` 分解为你的 `Activity`。


### 处理 Activity 中的 Intent

当 `Activity` 启动时，调用 `getIntent()` 方法获取启动 `Activity` 的 `Intent`。

你可以在 `Activity` 声明周期的任何时间执行此操作，但通常应在早期回调时 (如 `onCreate()` 或 `onStart()`) 执行。例如：

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);
    // Get the intent that started this activity
    Intent intent = getIntent();
    Uri data = intent.getData();
    // Figure out what to do based on the intent type
    if (intent.getType().indexOf("image/") != -1) {
        // Handle intents with image data ...
    } else if (intent.getType().equals("text/plain")) {
        // Handle intents with text ...
    }
}
```

## 返回结果

如果你想向调用你 `Activity` 的 `Activity` 返回结果，只需调用 `setResult()` 指定结果代码和结果 `Intent`。  

当你的操作完成且应用应该返回原始 `Activity` 时，调用 `finish()` 方法关闭你的 `Activity` 即可。例如：

```java
// Create intent to deliver some kind of result data
Intent result = new Intent("com.example.RESULT_ACTION", Uri.parse("content://result_uri"));
setResult(Activity.RESULT_OK, result);
finish();
```

你必须始终为结果指定「结果代码」，通常为 `RESULT_OK` 或 `RESULT_CANCELED`。对于返回结果的数据，可根据需要而定。

> **注**：默认情况下，结果代码设置为 `RESULT_CANCELED`。因此，如果用户在完成操作动作或设置结果之前按了返回按钮，原始 `Activity` 会收到「已取消」的结果。

如果你只需返回指示若干结果选项之一的整数，你可以将结果代码设置为大于 0 的任何值。如果你使用结果代码传递整数，且无需包括 `Intent`，则可调用 `setResult()`且仅传递结果代码。例如：

```java
setResult(RESULT_COLOR_RED);
finish();
```

在这种情况下，只有几个可能的结果，因此结果代码是一个本地定义的整数 (大于 0) 。当你向自己应用中的 `Activity` 返回结果时，这将非常有效，因为接收结果的 `Activity` 可引用公共常数来确定结果代码的值。

> **注**：无需检查你的 `Activity` 是使用 `startActivity()` 还是 ` startActivityForResult()` 启动的。  
> * 如果启动你 `Activity` 的 `Intent` 可能需要结果，只需调用 `setResult()`。  
> * 如果原始 `Activity` 已调用 `startActivityForResult()`，则系统将向其传递你提供给 `setResult()` 的结果；***否则，会忽略结果***。

## 测试你的深度链接

你可以使用「Android Debug Bridge」和「activity管理(am)工具」来测试你指定的 intent filter URI，能否正确解析到正确的 app activity。你可以在设备或者模拟器上运行 adb 命令。

测试 intent filter URI 的一般 adb 语法是:

```
$ adb shell am start
        -W -a android.intent.action.VIEW
        -d <URI> <PACKAGE>
```

例如，下面的命令试图浏览与指定 URI 相关的目标 app activity。

```
$ adb shell am start
        -W -a android.intent.action.VIEW
        -d "example://gizmos" com.example.android
```

## 实践

`MainActiviy`:  

![](./res/MainActivity.png)

```java
public class MainActivity extends AppCompatActivity {
    private static final String TAG = "MainActivity";
    private static final int REQUEST_CODE_TEST_ACTIVITY = 0x01;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button btnGotoTestActivity = (Button) findViewById(R.id.btn_goto_test_activity);
        btnGotoTestActivity.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                intent.setAction("com.zhuanghongji.android.links.test.activity");
                Uri uri = Uri.parse("custom://www.zhuanghongji.com/this/is/path" +
                        "?parameter=\"123\"&parameter=\"456\"&param1=\"1\"&param2=\"2\"");
                intent.setData(uri);
                intent.addCategory(Intent.CATEGORY_DEFAULT);
                intent.setDataAndType(uri, "text/plain");
                startActivityForResult(intent, REQUEST_CODE_TEST_ACTIVITY);}
        });
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == REQUEST_CODE_TEST_ACTIVITY && resultCode == RESULT_OK){
            CommonUtils.logIntent(TAG, data);
        }
    }
}
```

`TestActivity`:

```xml
<activity android:name=".TestActivity">
    <intent-filter>
        <action android:name="com.zhuanghongji.android.links.test.activity"/>
        <category android:name="android.intent.category.DEFAULT"/>
        <category android:name="android.intent.category.APP_MUSIC"/>
        <data
            android:scheme="custom"
            android:host="www.zhuanghongji.com"
            android:pathPrefix="/this/is/path"/>
        <data android:mimeType="text/plain"/>
    </intent-filter>
</activity>
```

```java
public class TestActivity extends AppCompatActivity {
    private static final String TAG = "TestActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_test);
        Intent intent = getIntent();
        CommonUtils.logIntent(TAG, intent);
    }

    @Override
    public void onBackPressed() {
        Uri uri = Uri.parse("custom://www.testactivity.net/abcd/efg?p1=\"1\"&p2=\"2\"");
        Intent intent = new Intent("action.from.test.activity", uri);
        setResult(RESULT_OK, intent);
        super.onBackPressed();
    }
}
```

打印日志工具类：

```java
public class CommonUtils {
    public static void logIntent(String tag, Intent intent){
        Log.d(tag, "action = " + intent.getAction());

        Set<String> categories = intent.getCategories();
        if (categories != null && categories.size() == 0) {
            for (String s : categories) {
                Log.d(tag, "category = " + s);
            }
        }

        Uri uri = intent.getData();
        if (uri != null) {
            Log.d(tag, "scheme = " + uri.getScheme());
            Log.d(tag, "host = " + uri.getHost());
            Log.d(tag, "port = " + uri.getPort());
            Log.d(tag, "path = " + uri.getPath());
            Log.d(tag, "parameter = " + uri.getQueryParameter("parameter"));
            Log.d(tag, "parameters = " + uri.getQueryParameters("parameter").toString());
            Log.d(tag, "query = " + uri.getQuery());
            Set<String> parameterNames = uri.getQueryParameterNames();
            if (parameterNames != null && parameterNames.size() != 0){
                for (String name : parameterNames){
                    Log.d(tag, "parameterNames: name = " + name
                            + ", value = " + uri.getQueryParameter(name));
                }
            }
        }

        Log.d(tag, "type = " + intent.getType());
    }
}
```

点击 `MainActivity` 按钮跳转到 `TestActivity` 后打印出来的日志：

```
D/TestActivity: action = com.zhuanghongji.android.links.test.activity
D/TestActivity: scheme = custom
D/TestActivity: host = www.zhuanghongji.com
D/TestActivity: port = -1
D/TestActivity: path = /this/is/path
D/TestActivity: parameter = "123"
D/TestActivity: parameters = ["123", "456"]
D/TestActivity: query = parameter="123"&parameter="456"&param1="1"&param2="2"
D/TestActivity: parameterNames: name = parameter, value = "123"
D/TestActivity: parameterNames: name = param1, value = "1"
D/TestActivity: parameterNames: name = param2, value = "2"
D/TestActivity: type = text/plain
```

在 `TestActivity` 点击实体返回键返回到 `MainActivity` 后打印出来的日志：

```
D/MainActivity: action = action.from.test.activity
D/MainActivity: scheme = custom
D/MainActivity: host = www.testactivity.net
D/MainActivity: port = -1
D/MainActivity: path = /abcd/efg
D/MainActivity: parameter = null
D/MainActivity: parameters = []
D/MainActivity: query = p1="1"&p2="2"
D/MainActivity: parameterNames: name = p1, value = "1"
D/MainActivity: parameterNames: name = p2, value = "2"
D/MainActivity: type = null
```
