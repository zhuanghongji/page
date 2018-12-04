# 发起一个简单请求

在高层次上，使用 Volley 是通过创建一个 `RequestQueue`（请求队列）来处理 `Request`（请求对象）。

* `RequestQueue` 管理着一系列用于网络操作、缓存读写和响应解析的工作线程
* `Request` 负责对原始响应进行解析
* `Volley` 负责将解析后的响应传递回主线程

本文描述了如何通过 `Volley.newRequestQueue` 方法创建简单的请求队列并发送请求（如何将请求加入请求队列和取消请求）。

如何对请求队列进行定制可以看下一篇文章 [设置请求队列](https://github.com/zhuanghongji/volley-note/blob/master/guide/setting-up-a-requestqueue.md) 。

## 添加网络权限

首先，在 AndroidManifest.xml 文件中添加 **android.permission.INTERNET** 权限。

## 使用请求队列

Volley 提供了一个便捷的方法 `Volley.newRequestQueue` 来创建一个请求队列，示例代码：

```java
final TextView mTextView = (TextView) findViewById(R.id.text);
...

// Instantiate the RequestQueue.
RequestQueue queue = Volley.newRequestQueue(this);
String url ="http://www.google.com";

// Request a string response from the provided URL.
StringRequest stringRequest = new StringRequest(Request.Method.GET, url,
            new Response.Listener<String>() {
    @Override
    public void onResponse(String response) {
        // Display the first 500 characters of the response string.
        mTextView.setText("Response is: "+ response.substring(0,500));
    }
}, new Response.ErrorListener() {
    @Override
    public void onErrorResponse(VolleyError error) {
        mTextView.setText("That didn't work!");
    }
});
// Add the request to the RequestQueue.
queue.add(stringRequest);
```

**Volley 总是在主线程传递解析后的响应**  
**Volley 总是在主线程传递解析后的响应**  
**Volley 总是在主线程传递解析后的响应**  

重要的事情说三遍，所以

* 应尽量简化 `onResponse()` 和 `onErrorResponse()` 方法中的逻辑代码，以避免阻塞主线程造成卡顿现象。
* 可以直接执行 UI 操作代码，无需通过 `runOnUiThread(new Runnable() {...}` 或 `handler.post(new Runnable() {...}` 切换到主线程执行。

## 发送一个请求

如上所述，你只需要简单构造一个请求并通过 `add()` 方法将其添加到 `RequestQueue` 中。一旦你将请求添加到队列中，它就会在队列管道中移动、获得服务、解析和传递原始响应。

当你调用 `add()` 方法时，Volley 会运行一个缓存处理线程和一个网络调度线程池。

当请求加入队列时，该请求由缓存线程拾取并进行分流：

* 如果可以在缓存中处理该请求，则在缓存线程上解析缓存的响应，然后在主线程上传递解析后的响应
* 如果请求不能在缓存中完成处理，则将其放置在网络队列中。第一个可用的网络线程会接收来自队列的请求，然后执行 HTTP 事务、解析工作线程上的响应、将响应写入缓存，然后再将解析后的响应发送回主线程进行传递

**注意**

* 类似"阻塞IO"和"解析/解码"等昂贵的操作可以放在工作线程中完成
* 你可以从任何线程中添加请求，但总是在主线程上传递响应

下图描述了一个请求的处理过程：  

![](./res/life-of-a-request.png)  

## 取消一个请求

你可以调用一个 `Request` 对象的 `cancel()` 方法取消一个请求。一旦取消，Volley 可以保证处理响应的方法永远不会被调用。

这实际上意味着，你可以在 `Activity` 的 `onStop()` 方法中取消所有待处理的请求，这样你就不用再写一些 “检查`getActivity（）== null`”、”是否已经调用了 `onSaveInstanceState()`方法” 或 ”其他防御性“ 的样板响应处理代码。

要利用此行为，你通常必须跟踪所有正在进行的请求，以便能够在适当的时间取消它们。有一种更简单的方法：你可以将一个 tag 对象与每个 request 相关联，然后你就可以通过这个 tag 来提供取消的 request 范围。

例如，你可以使用所代表的 `Activity` 来标记所有请求，并在 `onStop()` 调用 `requestQueue.cancelAll(this)` 来取消当前页面所有的请求。同样，你也可以在 `ViewPager` 选项卡中标记所有的缩略图请求及其各自的选项卡，然后在滑动时取消请求，以确保新选项卡不会受到来自另一个选项卡的请求的阻碍。

下面是一个使用 string 字符串作为 tag 的一个范例：  

1. 定义 tag 并将其设置到请求中
```java
public static final String TAG = "MyTag";
StringRequest stringRequest; // Assume this exists.
RequestQueue mRequestQueue;  // Assume this exists.

// Set the tag on the request.
stringRequest.setTag(TAG);

// Add the request to the RequestQueue.
mRequestQueue.add(stringRequest);
```

2. 在 `Activity` 的 `onStop()` 方法中取消所有带这个 tag 的请求
```java
@Override
protected void onStop () {
    super.onStop();
    if (mRequestQueue != null) {
        mRequestQueue.cancelAll(TAG);
    }
}
```

取消请求的时候请注意，如果你要依靠你的响应处理代码来推进某个状态或启动另一个流程，就需要对此进行解释(account)。同样，响应处理代码将不会被执行。
