# 自定义请求

本文主要描述了如何去自定义自己需要的请求类型（非 Volley 工具箱默认支持类型）

## 创建自定义请求类

大多数请求类型在工具箱中已经实现好了，比如响应是一个字符串、一张图片或 Json ,即时可用，无需自定义。

对于一些需要自己去定义请求类型的场景，你只需要以下两步：

* 写一个继承自 `Request<T>` 的类，`<T>` 是请求期望的响应解析类型。所以，如果你的响应解析是字符串类型，那么你自定义的请求应该继承自 `Request<String>`
* 实现抽象方法 `parseNetworkResponse()` 和 `deliverResponse()`

> 有关继承 `Request<T>` 的示例代码，可参阅 Volley 工具箱类 `StringRequest` 和 `ImageRequest`

## parseNetworkResponse

`Response` 封装了给定类型（字符串、图像或JSON）的响应解析。  

下面是 `parseNetworkResponse()` 的一个示例实现：

```java
@Override
protected Response<T> parseNetworkResponse(
        NetworkResponse response) {
    try {
        String json = new String(response.data,
        HttpHeaderParser.parseCharset(response.headers));
    return Response.success(gson.fromJson(json, clazz),
    HttpHeaderParser.parseCacheHeaders(response));
    }
    // handle errors
    ...
}
```

**注意：**

* `parseNetworkResponse()` 的参数类型 `NetworkResponse` 包含了 **byte[]格式的响应内容**、**HTTP状态码** 和 **响应头**
* 你的实现返回类型必须是 `Response<T>`，其中包含了 **你指定的响应对象类型**、**缓存元数据** 或 **错误信息**

如果你的协议具有非标准的缓存语义，可以自己构建一个 `Cache.Entry`，但是大多数请求都可以使用类似下面的方法：

```java
return Response.success(myDecodedObject,
        HttpHeaderParser.parseCacheHeaders(response));
```

Volley 会在工作线程调用 `parseNetworkResponse` 方法，以确保类似 ”将 JPEG 解析成 Bitmap“ 的昂贵操作不会阻塞 UI 线程。


## deliverResponse

Volley 会在主线程回调你在 `parseNetworkResponse()` 中返回的对象，大多数请求都会在这里调用一个回调接口，例如：

```java
protected void deliverResponse(T response) {
        listener.onResponse(response);
}
```

## 自定义请求示例 GsonRequest

`Gson` 是一个通过反射互相转换 `Java objects` 和 `JSON` 的库。你可以定义一个跟 JSON 键值相对应的 Java 对象，然后通过 Gson 对象的转换方法为该 Java 对象填充字段值。

下面是一个使用 Gson 进行解析 Volley 请求的完整实现：

```Java
public class GsonRequest<T> extends Request<T> {
    private final Gson gson = new Gson();
    private final Class<T> clazz;
    private final Map<String, String> headers;
    private final Listener<T> listener;

    /**
     * Make a GET request and return a parsed object from JSON.
     *
     * @param url URL of the request to make
     * @param clazz Relevant class object, for Gson's reflection
     * @param headers Map of request headers
     */
    public GsonRequest(String url, Class<T> clazz, Map<String, String> headers,
            Listener<T> listener, ErrorListener errorListener) {
        super(Method.GET, url, errorListener);
        this.clazz = clazz;
        this.headers = headers;
        this.listener = listener;
    }

    @Override
    public Map<String, String> getHeaders() throws AuthFailureError {
        return headers != null ? headers : super.getHeaders();
    }

    @Override
    protected void deliverResponse(T response) {
        listener.onResponse(response);
    }

    @Override
    protected Response<T> parseNetworkResponse(NetworkResponse response) {
        try {
            String json = new String(
                    response.data,
                    HttpHeaderParser.parseCharset(response.headers));
            return Response.success(
                    gson.fromJson(json, clazz),
                    HttpHeaderParser.parseCacheHeaders(response));
        } catch (UnsupportedEncodingException e) {
            return Response.error(new ParseError(e));
        } catch (JsonSyntaxException e) {
            return Response.error(new ParseError(e));
        }
    }
}
```

实际上 Volley 已经提供了即时可用的 `JsonArrayRequest` 和 `JsonObjectObject` 类，如果你喜欢采用这种方法的话，可参考「发起一个标准请求」。
