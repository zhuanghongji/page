# 快速开始

OkHttp 是一个适用于 Android 和 Java 应用程序的 HTTP 和 Http/2 客户端，能够有效地执行 HTTP 以加快负载并节省带宽。

OkHttp 是一个默认就很高效的 HTTP 客户端：

* HTTP/2 支持对同一主机的所有请求共享一个套接字。
* 如果 HTTP/2 不可用，连接池会减少请求的延时。
* GZIP 能有效缩小下载内容的大小。
* 通过缓存返回结果，能完全避免重复的网络请求。


即使网络很糟糕，OkHttp 也坚持不懈：

* OkHttp 会从常见的连接异常中静默恢复。
* 假设你的服务有多个 IP 地址，当第一个连接失败时，OkHttp 会尝试使用备用地址。
* OkHttp 初始化新的连接时具有现代的 TLS 功能 (SNI、ALPN)，如果握手失败会回退到 TLS 1.0。

OkHttp 的使用非常简单：

* 请求和响应 API 采用了流畅的构建器和不变性设计。
* 支持阻塞的同步调用和带回调的异步调用。

支持情况：

* 对于 Android，支持 Android 2.3 及以上版本。
* 对于 Java，最低要求 Java 1.7 版本。

以下内容有：

* [添加依赖](#添加依赖)
* [官方示例](#官方示例)
* [Android 示例](#android-示例)
* [MockWebServer](#MockWebServer)

## 添加依赖

当我们创建好项目之后，需要先添加 OkHttp 的依赖。  

目前最新依赖版本号为 `3.11.0`，添加依赖的方式有三种：

1. Jar   
先下载 [最新 Jar 包](https://search.maven.org/remote_content?g=com.squareup.okhttp3&a=mockwebserver&v=LATEST)，再手动加入到项目中。

2. Maven
```xml
<dependency>
  <groupId>com.squareup.okhttp3</groupId>
  <artifactId>okhttp</artifactId>
  <version>3.11.0</version>
</dependency>
```

3. Gradle
```groovy  
implementation 'com.squareup.okhttp3:okhttp:3.11.0'
```


## 官方示例

### GET 请求

下面的代码是通过一个 URL 获取其内容并打印出来：

关键代码：

```java
OkHttpClient client = new OkHttpClient();
String run(String url) throws IOException {
  Request request = new Request.Builder()
      .url(url)
      .build();

  Response response = client.newCall(request).execute();
  return response.body().string();
}
```

完整代码：

```java
public class GetExample {
  OkHttpClient client = new OkHttpClient();

  String run(String url) throws IOException {
        Request request = new Request.Builder()
                .url(url)
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
  }

  public static void main(String[] args) throws IOException {
    GetExample example = new GetExample();
    String response = example.run("https://raw.github.com/square/okhttp/master/README.md");
    System.out.println(response);
  }
}
```

打印日志 (只截取了开头部分)：

```
OkHttp
======

An HTTP & HTTP/2 client for Android and Java applications. For more information see [the website][1] and [the wiki][2].

Download
--------

Download [the latest JAR][3] or grab via Maven:

...
```

### POST 请求

下面的代码演示了如何 post 数据到服务端：

关键代码：

```java
public static final MediaType JSON
    = MediaType.parse("application/json; charset=utf-8");

OkHttpClient client = new OkHttpClient();

String post(String url, String json) throws IOException {
  RequestBody body = RequestBody.create(JSON, json);
  Request request = new Request.Builder()
      .url(url)
      .post(body)
      .build();
  Response response = client.newCall(request).execute();
  return response.body().string();
}
```

完整代码：

```java
public class PostExample {
  public static final MediaType JSON = MediaType.get("application/json; charset=utf-8");

  OkHttpClient client = new OkHttpClient();

  String post(String url, String json) throws IOException {
        RequestBody body = RequestBody.create(JSON, json);
        Request request = new Request.Builder()
                .url(url)
                .post(body)
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
  }

  String bowlingJson(String player1, String player2) {
    return "{'winCondition':'HIGH_SCORE',"
        + "'name':'Bowling',"
        + "'round':4,"
        + "'lastSaved':1367702411696,"
        + "'dateStarted':1367702378785,"
        + "'players':["
        + "{'name':'" + player1 + "','history':[10,8,6,7,8],'color':-13388315,'total':39},"
        + "{'name':'" + player2 + "','history':[6,10,5,10,10],'color':-48060,'total':41}"
        + "]}";
  }

  public static void main(String[] args) throws IOException {
    PostExample example = new PostExample();
    String json = example.bowlingJson("Jesse", "Jake");
    String response = example.post("http://www.roundsapp.com/post", json);
    System.out.println(response);
  }
}
```

打印日志：

```
http://www.roundsapp.com/160361002
```

## Android 示例

这里的示例演示使用了 [wanandroid.com](http://www.wanandroid.com) 的开放 API :

```
首页 Banner
http://www.wanandroid.com/banner/json

方法：GET
参数：无
```

可直接点击在网页端查看示例：[http://www.wanandroid.com/banner/json](http://www.wanandroid.com/banner/json)

下面的 Android 代码一起演示了同步请求和异步请求：

```java
public class MainActivity extends AppCompatActivity {

    public static final String TAG = "MainActivity";

    private OkHttpClient mOkHttpClient;
    private Request mRequest;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        String testUrl = "http://www.wanandroid.com/banner/json";

        mOkHttpClient = new OkHttpClient();
        mRequest = new Request.Builder().url(testUrl).build();

        testExecute();
        testEnqueue();
    }

    /** 同步请求 */
    private void testExecute() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Response response = mOkHttpClient.newCall(mRequest).execute();
                    ResponseBody responseBody = response.body();
                    if (responseBody != null) {
                        Log.i(TAG, "testExecute body = " + responseBody.string());
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

    /** 异步请求 */
    private void testEnqueue() {
        mOkHttpClient.newCall(mRequest).enqueue(new Callback() {
            @Override
            public void onFailure(@NonNull Call call, @NonNull IOException e) {
                Log.w(TAG, "testEnqueue onFailure");
            }

            @Override
            public void onResponse(@NonNull Call call, @NonNull Response response) throws IOException {
                final ResponseBody responseBody =  response.body();
                if (responseBody != null) {
                    Log.i(TAG, "testEnqueue body = " + responseBody.string());
                }
            }
        });
    }
}
```

不管是同步还是异步，上述示例中最终的响应结果是相同，如下：
```
{"data":[{"desc":"","id":15,"imagePath":"http://www.wanandroid.com/blogimgs/ec492618-d03e-4380-81df-5a990228a37e.jpg","isVisible":1,"order":0,"title":"当当网图书满200立减120，满400-250，本站福利","type":0,"url":"http://www.wanandroid.com/blog/show/2234"},{"desc":"","id":6,"imagePath":"http://www.wanandroid.com/blogimgs/62c1bd68-b5f3-4a3c-a649-7ca8c7dfabe6.png","isVisible":1,"order":1,"title":"我们新增了一个常用导航Tab~","type":0,"url":"http://www.wanandroid.com/navi"},{"desc":"一起来做个App吧","id":10,"imagePath":"http://www.wanandroid.com/blogimgs/50c115c2-cf6c-4802-aa7b-a4334de444cd.png","isVisible":1,"order":1,"title":"一起来做个App吧","type":0,"url":"http://www.wanandroid.com/blog/show/2"},{"desc":"","id":7,"imagePath":"http://www.wanandroid.com/blogimgs/ffb61454-e0d2-46e7-bc9b-4f359061ae20.png","isVisible":1,"order":2,"title":"送你一个暖心的Mock API工具","type":0,"url":"http://www.wanandroid.com/blog/show/10"},{"desc":"","id":4,"imagePath":"http://www.wanandroid.com/blogimgs/ab17e8f9-6b79-450b-8079-0f2287eb6f0f.png","isVisible":1,"order":0,"title":"看看别人的面经，搞定面试~","type":1,"url":"http://www.wanandroid.com/article/list/0?cid=73"},{"desc":"","id":3,"imagePath":"http://www.wanandroid.com/blogimgs/fb0ea461-e00a-482b-814f-4faca5761427.png","isVisible":1,"order":1,"title":"兄弟，要不要挑个项目学习下?","type":1,"url":"http://www.wanandroid.com/project"},{"desc":"加个友情链接吧~","id":11,"imagePath":"http://www.wanandroid.com/blogimgs/84810df6-adf1-45bc-b3e2-294fa4e95005.png","isVisible":1,"order":1,"title":"加个友情链接吧~","type":1,"url":"http://www.wanandroid.com/ulink"},{"desc":"","id":2,"imagePath":"http://www.wanandroid.com/blogimgs/90cf8c40-9489-4f9d-8936-02c9ebae31f0.png","isVisible":1,"order":2,"title":"JSON工具","type":1,"url":"http://www.wanandroid.com/tools/bejson"},{"desc":"","id":5,"imagePath":"http://www.wanandroid.com/blogimgs/acc23063-1884-4925-bdf8-0b0364a7243e.png","isVisible":1,"order":3,"title":"微信文章合集","type":1,"url":"http://www.wanandroid.com/blog/show/6"}],"errorCode":0,"errorMsg":""}
```

> 有两点需要注意的是：

* 发起同步请求时不能在主线程发起，否则会报错 `android.os.NetworkOnMainThreadException`。
* 异步请求返回响应结果时，代码并不是运行在主线程的，所以不能直接进行 UI 相关的操作。

## MockWebServer


// TODO
