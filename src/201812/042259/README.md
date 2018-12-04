# 设置请求队列

本文将引导你完成创建 `RequestQueue` 的明确步骤，以允许你提供自己的自定义行为。同时还介绍了将 `RequestQueue` 创建为单例的推荐做法，这使得 `RequestQueue` 可以持续在应用程序的整个生命周期。

## 设置网络和缓存

请求队列需要两项东西去做他的工作：

* 执行传输请求的网络
* 处理缓存的缓存

Volley 工具箱（toolbox）中提供了上述的标准实现：

* `BasicNetwork` 提供了一个基于你的首选 HTTP 客户端的网络传输
* `DiskBasedCache` 提供了一个 "一个文件一个响应" 的内存索引缓存

`BasicNetwork` 是 Volley 的默认网络实现。`BasicNetwork` 必须由你的 app 用于连接到网络的 HTTP 客户端进行初始化，通常情况下，这是一个 `HttpURLConnection` 。

下面的代码片段展示了设置 `RequestQueue` 所涉及的步骤：

```java
RequestQueue mRequestQueue;

// Instantiate the cache
Cache cache = new DiskBasedCache(getCacheDir(), 1024 * 1024); // 1MB cap

// Set up the network to use HttpURLConnection as the HTTP client.
Network network = new BasicNetwork(new HurlStack());

// Instantiate the RequestQueue with the cache and network.
mRequestQueue = new RequestQueue(cache, network);

// Start the queue
mRequestQueue.start();

String url ="http://www.example.com";

// Formulate the request and handle the response.
StringRequest stringRequest = new StringRequest(Request.Method.GET, url,
        new Response.Listener<String>() {
    @Override
    public void onResponse(String response) {
        // Do something with the response
    }
},
    new Response.ErrorListener() {
        @Override
        public void onErrorResponse(VolleyError error) {
            // Handle error
    }
});

// Add the request to the RequestQueue.
mRequestQueue.add(stringRequest);

// ...
```

如果你只是想发送一个一次性的请求，且不想离开线程池，则可以在需要的地方创建 `RequestQueue`，并在你的响应或错误返回时使用 `RequestQueue` 的 `stop()` 方法。

创建简单请求队列可参考「发送一个简单请求」中的 `Volley.newRequestQueue()` 方法部分。

但实际上，更常见的使用方法是将 `RequestQueue` 创建为一个单例。

## 使用单例模式

如果你的应用是需要经常用到网络功能的，最高效的方式就是创建一个 `RequestQueue` 单例对象，使其可以在应用的整个生命周期中保持运行。

实现单例的方式有多种:

* 推荐方式：实现一个封装了 `RequestQueue` 和其他 `Volley` 功能的单例类
* 不推荐方式：在 `Application` 子类的 `onCreate()` 方法中设置 `RequestQueue`

显然，一个静态单例类能够以更加模块化的方式提供相同的功能。

以下是推荐方式的示例代码：

```java
public class MySingleton {
    private static MySingleton mInstance;
    private RequestQueue mRequestQueue;
    private ImageLoader mImageLoader;
    private static Context mCtx;

    private MySingleton(Context context) {
        mCtx = context;
        mRequestQueue = getRequestQueue();

        mImageLoader = new ImageLoader(mRequestQueue,
                new ImageLoader.ImageCache() {
            private final LruCache<String, Bitmap>
                    cache = new LruCache<String, Bitmap>(20);

            @Override
            public Bitmap getBitmap(String url) {
                return cache.get(url);
            }

            @Override
            public void putBitmap(String url, Bitmap bitmap) {
                cache.put(url, bitmap);
            }
        });
    }

    public static synchronized MySingleton getInstance(Context context) {
        if (mInstance == null) {
            mInstance = new MySingleton(context);
        }
        return mInstance;
    }

    public RequestQueue getRequestQueue() {
        if (mRequestQueue == null) {
            // getApplicationContext() is key, it keeps you from leaking the
            // Activity or BroadcastReceiver if someone passes one in.
            mRequestQueue = Volley.newRequestQueue(mCtx.getApplicationContext());
        }
        return mRequestQueue;
    }

    public <T> void addToRequestQueue(Request<T> req) {
        getRequestQueue().add(req);
    }

    public ImageLoader getImageLoader() {
        return mImageLoader;
    }
}
```

以下是使用单例类执行 `RequestQueue` 操作的示例代码：

```java
// Get a RequestQueue
RequestQueue queue = MySingleton.getInstance(this.getApplicationContext()).
    getRequestQueue();

// ...

// Add a request (in this example, called stringRequest) to your RequestQueue.
MySingleton.getInstance(this).addToRequestQueue(stringRequest);
```
