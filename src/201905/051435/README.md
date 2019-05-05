# Retrofit 基本使用

[Retrofit](https://github.com/square/retrofit) 也是 Square 公司的一个开源库，为 Android 和 Java 提供了类型安全的 HTTP 客户端。目前最新的发布版本是 `2.5.0`，能工作在 Android 2.3+ 和 Java 7+ 上。


## 介绍

Retrofit 能够将你的 HTTP API 转换为 Java 接口。

```java
public interface GitHubService {
  @GET("users/{user}/repos")
  Call<List<Repo>> listRepos(@Path("user") String user);
}
```

`Retrofit` 类可以为该 `GitHubService` 接口生成一个实现。

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com/")
    .build();

GitHubService service = retrofit.create(GitHubService.class);
```

每个来自创建好的 `GitHubService` 接口实现的 `Call` 都可以向远程服务器发起同步或异步的 HTTP 请求。

```java
Call<List<Repo>> repos = service.listRepos("octocat");
```

使用注解来描述 HTTP 请求：
* 支持 URL 参数和 Query 参数替换。
* 对象转换成请求体 (比如 JSON、协议缓冲区)。
* Multipart 请求体和文件上传。


## API 声明

接口方法及其参数上的注解表明了请求会如何被处理。

### 请求方法

每个方法都必须有一个 HTTP 注解来提供请求方法和对应的 URL。Retrofit 内置了五种相关的注解：`GET`、`POST`、`PUT`、`DELETE` 和 `HEAD`。资源相关的 URL 在注解内指定。

```java
@GET("users/list")
```

同时，你也可以在 URL 中指定查询参数。

```java
@GET("users/list?sort=desc")
```


### URL 操作

可以通过方法上的替换块 (replacement blocks) 和相应的参数 (parameters) 来动态更新请求 URL：
* 替换块是有 `{` 和 `}` 包围着的一个字母数字。
* 相应的参数必须通过 `@path` 和使用相同字符串来进行标注。

```java
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId);
```

查询参数也可以通过类似的方法进行添加。

```java
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId, @Query("sort") String sort);
```

也可以使用复杂的参数组合 `Map`。

```java
@GET("group/{id}/users")
Call<List<User>> groupList(@Path("id") int groupId, @QueryMap Map<String, String> options);
```

### 请求体

可以通过 `@Body` 注解来将一个对象指定为一个 HTTP 请求体。

```java
@POST("users/new")
Call<User> createUser(@Body User user);
```

该对象也可以由 `Retrofit` 实例上指定的转换器进行转换。如果没有添加任何转换器，就只可以使用 `RequestBody`。


### FORM ENCODED 和 MULTIPART

还可以声明方法以发送 form-encoded 和 multipart 数据。

当方法上有 `@FormUrlEncoded` 注解时会发送 form-encoded 数据。每个 key-value 对由包含 name 和对象提供的 value 的 `@Field` 注解进行标识。

```java
@FormUrlEncoded
@POST("user/edit")
Call<User> updateUser(@Field("first_name") String first, @Field("last_name") String last);
```

当方法上有 `@Multipart` 注解时会使用 multipart 请求。Parts 需要使用 `@Part` 注解进行声明。

```java
@Multipart
@PUT("user/photo")
Call<User> updateUser(@Part("photo") RequestBody photo, @Part("description") RequestBody description);
```

Multipart 的 parts 使用 `Retrofit` 的转换器或他们可以实现 `RequestBody` 接口来处理他们的序列化。


## HEADER 操作

你可以使用 `@Headers` 注解来为请求方法设置静态的请求头 (headers)。

```java
@Headers("Cache-Control: max-age=640000")
@GET("widget/list")
Call<List<Widget>> widgetList();
```

```java
@Headers({
    "Accept: application/vnd.github.v3.full+json",
    "User-Agent: Retrofit-Sample-App"
})
@GET("users/{username}")
Call<User> getUser(@Path("username") String username);
```

注意，请求头不会相互覆盖，所有含相同名字的请求头都会被包含进请求中。

当需要动态更新请求头时可以使用 `@Header` 注解。对应于 `@Header` 注解的值必须提供，如果为 `null` 的话该请求头会被忽略。否则，会使用该值 `toString` 结果作为请求头对应的值。

```java
@GET("user")
Call<User> getUser(@Header("Authorization") String authorization)
```

类似于查询阐述，对于复杂的请求头组合可以使用 `Map`。

```java
@GET("user")
Call<User> getUser(@HeaderMap Map<String, String> headers)
```

如果有请求头数据需要在每个请求上进行添加时，可以使用 [OkHttp 拦截器](https://github.com/square/okhttp/wiki/Interceptors)。


### 同步与异步

`Call` 实例可以同步或异步执行，但一个实例只能使用一次。如果你想多次调用调用同一个类型的 `Call`，可以调用其实例的 `clone()` 方法来创建一个新实例。

在 Android 平台中，回调将会在主线程上执行。在 JVM 中，回调会在执行 HTTP 请求的同一个线程中发生。


## Retrofit 配置

`Retrofit` 是将你的 API 接口转换为可调用对象的类。默认情况下，Retrofit 会为你的平台提供合理的默认值，但也允许你进行自定义。


### 转换器

默认情况下，Retrofit 只能将 HTTP 主体反序列化成 OkHttp 的 `RequestBody` 类型，并且也只能接受其 `@Body` 为 `RequestBody`。

可以添加转换器 (Converters) 来支持其它类型，以下是 6 个开箱即用的流行序列化库：

* [Gson](https://github.com/google/gson): com.squareup.retrofit2:converter-gson
* [Jackson](http://wiki.fasterxml.com/JacksonHome): com.squareup.retrofit2:converter-jackson
* [Moshi](https://github.com/square/moshi/): com.squareup.retrofit2:converter-moshi
* [Protobuf](https://developers.google.com/protocol-buffers/): com.squareup.retrofit2:converter-protobuf
* [Wire](https://github.com/square/wire): com.squareup.retrofit2:converter-wire
* [Simple XML](http://simple.sourceforge.net/): com.squareup.retrofit2:converter-simplexml

下面是使用 `GsonConverterFactory` 类来生成 `GitHubService` 接口实现的示例，该接口使用 Gson 来进行反序列化。

```java
Retrofit retrofit = new Retrofit.Builder()
    .baseUrl("https://api.github.com")
    .addConverterFactory(GsonConverterFactory.create())
    .build();

GitHubService service = retrofit.create(GitHubService.class);
```


### 自定义转换器

如果你的 API 通信数据有点特殊 (如 YAML、txt 或自定义的格式)，或者想使用其它非上面开箱即用的库来进行反序列化。你可以创建一个继承自 `Converter.Factory` 类的类，并将其传入你构建的适配器中。


## 添加依赖

GRADLE

```groovy
implementation 'com.squareup.retrofit2:retrofit:2.5.0'
```

MAVEN 

```xml
<dependency>
  <groupId>com.squareup.retrofit2</groupId>
  <artifactId>retrofit</artifactId>
  <version>2.5.0</version>
</dependency>
```

## 简单示例

### API

以 wanandroid.com 站点的两个开放 API 为例：

```
https://www.wanandroid.com/banner/json
说明：首页 Banner
方法：GET
参数：无
返回示例：
  {
    data: [
      {
      desc: "一起来做个App吧",
      id: 10,
      imagePath: "https://www.wanandroid.com/blogimgs/50c115c2-cf6c-4802-aa7b-a4334de444cd.png",
      isVisible: 1,
      order: 1,
      title: "一起来做个App吧",
      type: 0,
      url: "http://www.wanandroid.com/blog/show/2"
      }
    ],
    errorCode: 0,
    errorMsg: ""
  }
```

```
https://www.wanandroid.com/article/list/0/json
说明：首页文章列表
方法：GET
参数：页码，拼接在连接中，从 0 开始。
返回示例：
  {
    data: {
      curPage: 1,
      datas: [
        {
          apkLink: "",
          author: "飞吧小蚊子",
          chapterId: 443,
          chapterName: "Android 10.0",
          collect: false,
          courseId: 13,
          desc: "",
          envelopePic: "",
          fresh: true,
          id: 8358,
          link: "https://www.jianshu.com/p/77f319ea53aa",
          niceDate: "18小时前",
          origin: "",
          prefix: "",
          projectLink: "",
          publishTime: 1556976828000,
          superChapterId: 151,
          superChapterName: "5.+高新技术",
          tags: [ ],
          title: "Android Q适配（1）-------图标篇",
          type: 0,
          userId: -1,
          visible: 1,
          zan: 0
        },
      ],
      offset: 0,
      over: false,
      pageCount: 322,
      size: 20,
      total: 6428
    },
    errorCode: 0,
    errorMsg: ""
  }
```

### 实体类

根据上面两个 API 返回示例定义出以下实体类。

`ResultList.java` 和 `Banner.java`

```java
public class ResultList<T> {
    private int errorCode;
    private String errorMsg;
    private List<T> data;

    // getter, setter and toString ..
}
```
```java
public class Banner {
    private String desc;
    private int id;
    private String imagePath;
    private int isVisible;
    private String title;
    private int type;
    private String url;

    // getter, setter and toString ..
}
```

`Result.java` 和 `Articles.java`

```java
public class Result<T> {
    private int errorCode;
    private String errorMsg;
    private T data;

    // getter, setter and toString ..
}
```
```java
public class Articles {
    private int curPage;
    private int offset;
    private boolean over;
    private int pageCount;
    private int size;
    private int total;
    private List<Item> datas;

    // getter, setter and toString ..

    public static class Item {
        private String apkLink;
        private String author;
        private int chapterId;
        private String chapterName;
        private boolean collect;
        private int courseId;
        private String desc;
        private String envelopePic;
        private boolean fresh;
        private int id;
        private String link;
        private String niceDate;
        private String origin;
        private String prefix;
        private String projectLink;
        private long publishTime;
        private int superChapterId;
        private String superChapterName;
        private List<TAG> tags;
        private String title;
        private int type;
        private int userId;
        private int visible;
        private int zan;

        // getter, setter and toString ..
    }

    public static class TAG {
        private String name;
        private String url;

        // getter, setter and toString ..
    }
}
```

### 相关配置

```xml
<uses-permission android:name="android.permission.INTERNET" />

<application 
  android:name=".App">
  <!-- ... -->
</application>
```

```groovy
// ...

android {
    compileSdkVersion 28
    defaultConfig {
        applicationId "com.zhuanghongji.retrofit"
        minSdkVersion 21
        targetSdkVersion 26
        // ...
    }
}

implementation 'com.squareup.okhttp3:logging-interceptor:3.12.0'

implementation 'com.squareup.retrofit2:retrofit:2.5.0'
implementation 'com.squareup.retrofit2:converter-gson:2.5.0'
// implementation 'com.squareup.retrofit2:adapter-rxjava2:2.5.0'
// implementation 'io.reactivex.rxjava2:rxandroid:2.1.1'
```


### HTTP 管理类

`App.java`

```java
public class App extends Application {
    // 为求简单写的 getContext 逻辑
    @SuppressLint("StaticFieldLeak")
    private static Context mContext;

    @Override
    public void onCreate() {
        super.onCreate();
        mContext = this;
    }

    public static Context getContext() {
        return mContext;
    }
}
```

`HttpManager.java`

```java
public class HttpManager {
    /** 最大缓存大小 50M */
    private static final long MAX_CACHE_SIZE = 1024 * 1024 * 50;
    /** 默认超时时间(s) */
    private static final long DEFAULT_TIMEOUT = 15;
    /** WAN ANDROID BASE URL */
    private static final String BASE_URL = "https://www.wanandroid.com";

    private static WanService sWanService;
    private static Retrofit sRetrofit;

    public static WanService service() {
        if (sWanService == null) {
            synchronized (HttpManager.class) {
                if (sWanService == null) {
                    sWanService = getRetrofit().create(WanService.class);
                }
            }
        }
        return sWanService;
    }

    private static Retrofit getRetrofit() {
        if (sRetrofit == null) {
            synchronized (HttpManager.class) {
                if (sRetrofit == null) {
                    sRetrofit = new Retrofit.Builder()
                            .baseUrl(BASE_URL)
                            .client(getOkHttpClient())
                            .addConverterFactory(GsonConverterFactory.create())
                            // .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                            .build();
                }
            }
        }
        return sRetrofit;
    }

    private static OkHttpClient getOkHttpClient() {
        OkHttpClient.Builder builder = new OkHttpClient.Builder();

        HttpLoggingInterceptor loggingInterceptor = new HttpLoggingInterceptor();
        if (BuildConfig.DEBUG) {
            loggingInterceptor.setLevel(HttpLoggingInterceptor.Level.BODY);
        } else {
            loggingInterceptor.setLevel(HttpLoggingInterceptor.Level.NONE);
        }

        File cacheFile = new File(App.getContext().getCacheDir(), "cache");
        Cache cache = new Cache(cacheFile, MAX_CACHE_SIZE);

        return builder.addInterceptor(loggingInterceptor)
                .cache(cache)
                .connectTimeout(DEFAULT_TIMEOUT, TimeUnit.SECONDS)
                .readTimeout(DEFAULT_TIMEOUT, TimeUnit.SECONDS)
                .writeTimeout(DEFAULT_TIMEOUT, TimeUnit.SECONDS)
                .retryOnConnectionFailure(true)
                .build();
    }
}
```

### 页面布局及相关代码

`activity_main.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center_horizontal"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/btn_banners"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:textAllCaps="false"
        android:textSize="18sp"
        android:text="getBanners"/>

    <Button
        android:id="@+id/btn_articles"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="16dp"
        android:textAllCaps="false"
        android:textSize="18sp"
        android:text="getArticles"/>
</LinearLayout>
```

`MainActivity.java`

```java
public class MainActivity extends AppCompatActivity {
    private static final String TAG = "MainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Button btnBanners = findViewById(R.id.btn_banners);
        btnBanners.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Call<ResultList<Banner>> bannersCall = HttpManager.service().getBanners();
                bannersCall.enqueue(new Callback<ResultList<Banner>>() {
                    @Override
                    public void onResponse(Call<ResultList<Banner>> call, Response<ResultList<Banner>> response) {
                        Log.v(TAG, "onResponse isOnMainThread = "
                                + (Thread.currentThread() == getMainLooper().getThread()));
                        if (response.body() == null) {
                            Log.i(TAG, "getBanners() response.body() is null.");
                            return;
                        }
                        List<Banner> banners = response.body().getData();
                        Log.i(TAG, "banners = " + banners);
                    }

                    @Override
                    public void onFailure(Call<ResultList<Banner>> call, Throwable t) {
                        Log.v(TAG, "onFailure isOnMainThread = "
                                + (Thread.currentThread() == getMainLooper().getThread()));
                        Log.i(TAG, "getBanners() failed.");
                    }
                });
            }
        });


        Button btnArticles = findViewById(R.id.btn_articles);
        btnArticles.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // 取第一页文章数据
                Call<Result<Articles>> articlesCall = HttpManager.service().getArticles(0);
                articlesCall.enqueue(new Callback<Result<Articles>>() {
                    @Override
                    public void onResponse(Call<Result<Articles>> call, Response<Result<Articles>> response) {
                        if (response.body() == null) {
                            Log.i(TAG, "getArticles response.body() is null.");
                            return;
                        }
                        Articles articles = response.body().getData();
                        Log.i(TAG, "articles = " + articles);
                    }

                    @Override
                    public void onFailure(Call<Result<Articles>> call, Throwable t) {
                        Log.i(TAG, "getArticles() failed.");
                    }
                });
            }
        });
    }
}
```

### 运行结果

<img src="./res/001.png" width="320">

点击 **getBanners** 按钮，日志输出如下：

```java
V/MainActivity: onResponse isOnMainThread = true
I/MainActivity: articles = Articles{curPage=1, offset=0, over=false, pageCount=322, size=20, total=6428, datas=[Item{apkLink='', author='飞吧小蚊子', chapterId=443, chapterName='Android 10.0', collect=false, courseId=13, desc='', envelopePic='', fresh=true, id=8358, link='https://www.jianshu.com/p/77f319ea53aa', niceDate='19小时前', origin='', prefix='', projectLink='', publishTime=1556976828000, superChapterId=151, superChapterName='5.+高新技术', tags=[], title='Android Q适配（1）-------图标篇', type=0, userId=-1, visible=1, zan=0}, Item{apkLink='', author='蒙伟', chapterId=31, ...
```

点击 **getArticles** 按钮，日志输出如下：

```java
I/MainActivity: articles = Articles{curPage=1, offset=0, over=false, pageCount=322, size=20, total=6428, datas=[Item{apkLink='', author='飞吧小蚊子', chapterId=443, chapterName='Android 10.0', collect=false, courseId=13, desc='', envelopePic='', fresh=true, id=8358, link='https://www.jianshu.com/p/77f319ea53aa', niceDate='20小时前', origin='', prefix='', projectLink='', publishTime=1556976828000, superChapterId=151, superChapterName='5.+高新技术', tags=[], title='Android Q适配（1）-------图标篇', type=0, userId=-1, visible=1, zan=0}, Item{apkLink='', author='蒙伟', chapterId=31, chapterName='Dialog', collect=false, courseId=13, desc='', ...
```

因为在构建 `OkHttpClient` 实例时，我们增加了 `HttpLoggingInterceptor` 拦截器，所以我们也可以在 Logcat 中看到 OkHttp 的网络请求数据。比如发起 `getArticles` 请求时，相关日志输出如下：

```java
D/OkHttp: --> GET https://www.wanandroid.com/article/list/0/json
D/OkHttp: --> END GET
D/OkHttp: <-- 200 OK https://www.wanandroid.com/article/list/0/json (368ms)
D/OkHttp: Server: Apache-Coyote/1.1
D/OkHttp: Cache-Control: private
D/OkHttp: Expires: Thu, 01 Jan 1970 08:00:00 CST
D/OkHttp: Set-Cookie: JSESSIONID=DF680351F1DA0BAECAB2F86A61D8ADCB; Path=/; Secure; HttpOnly
D/OkHttp: Content-Type: application/json;charset=UTF-8
D/OkHttp: Transfer-Encoding: chunked
D/OkHttp: Date: Sun, 05 May 2019 09:34:39 GMT
D/OkHttp: {"data":{"curPage":1,"datas":[{"apkLink":"", ...
D/OkHttp: <-- END HTTP (9721-byte body)
```


## 进阶示例

在上面的简单示例中，我们在进行相关配置和构建 `Retrofit` 实例时分别注释了三行代码：

```groovy
// implementation 'com.squareup.retrofit2:adapter-rxjava2:2.5.0'
// implementation 'io.reactivex.rxjava2:rxandroid:2.1.1'

// .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
```

这两行代码都是跟 RxJava2 相关的，现在我们来使用 RxJava2 对 "简单示例" 进行改造。

**1. 首先解除上面三行代码的注释。**

点击 `Sync With File System` 下载 RxJava2 Adapter 依赖相关代码。


**2. 改造 `WanService`。**

接口方法的返回值改为 `Observable<T>` 的形式。

```java
public interface WanService {

    @GET("/banner/json")
    Observable<ResultList<Banner>> getBanners();

    @GET("/article/list/{pageNum}/json")
    Observable<Result<Articles>> getArticles(@Path("pageNum") int num);
}
```

**3. 改造 `MainActivity.java` 中事件回调的执行逻辑。**

`btnBanners`

```java
btnBanners.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        HttpManager.service().getBanners()
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Observer<ResultList<Banner>>() {
                    @Override
                    public void onSubscribe(Disposable d) {
                        Log.v(TAG, "onSubscribe isOnMainThread = "
                                + (Thread.currentThread() == getMainLooper().getThread()));
                    }

                    @Override
                    public void onNext(ResultList<Banner> bannerResultList) {
                        Log.v(TAG, "onNext isOnMainThread = "
                                + (Thread.currentThread() == getMainLooper().getThread()));
                        Log.i(TAG, "banners = " + bannerResultList.getData());
                    }

                    @Override
                    public void onError(Throwable e) {
                        Log.v(TAG, "onError isOnMainThread = "
                                + (Thread.currentThread() == getMainLooper().getThread()));
                    }

                    @Override
                    public void onComplete() {
                        Log.v(TAG, "onComplete isOnMainThread = "
                                + (Thread.currentThread() == getMainLooper().getThread()));
                    }
                });
    }
});
```

`btnArticles`

```java
btnArticles.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        // 取第一页的第一个文章的标题
        HttpManager.service().getArticles(0)
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .map(new Function<Result<Articles>, String>() {
                    @Override
                    public String apply(Result<Articles> articlesResult) throws Exception {
                        if (articlesResult.getErrorCode() != 0) {
                            throw new Exception(articlesResult.getErrorMsg());
                        }
                        List<Articles.Item> items = articlesResult.getData().getDatas();
                        if (items == null || items.isEmpty()) {
                            return null;
                        }
                        return items.get(0).getTitle();
                    }
                })
                .subscribe(new Observer<String>() {
                    @Override
                    public void onSubscribe(Disposable d) {
                        Log.v(TAG, "onSubscribe");
                    }

                    @Override
                    public void onNext(String s) {
                        Log.v(TAG, "onNext");
                        Log.i(TAG, "第一页的第一个文章的标题 = " + s);
                    }

                    @Override
                    public void onError(Throwable e) {
                        Log.v(TAG, "onError message = " + e.getMessage());
                    }

                    @Override
                    public void onComplete() {
                        Log.v(TAG, "onComplete");
                    }
                });
    }
});
```

**4. 查看点击相关按钮后的输出日志。**

点击 **getBanners** 按钮，日志输出如下：

```java
V/MainActivity: onSubscribe isOnMainThread = true
V/MainActivity: onNext isOnMainThread = true
I/MainActivity: banners = [Banner{desc='一起来做个App吧', id=10, imagePath= ...
V/MainActivity: onComplete isOnMainThread = true
```

点击 **btnArticles** 按钮，日志输出如下：

```java
V/MainActivity: onSubscribe
V/MainActivity: onNext
I/MainActivity: 第一页的第一个文章的标题 = Android Q适配（1）-------图标篇
V/MainActivity: onComplete
```


## 总结

在这篇文章中，我们讲解了 Retrofit 的基本使用，并且分别通过 "不结合 RxJava2" 和 "结合 RxJava2" 两个示例进行了相关演示.