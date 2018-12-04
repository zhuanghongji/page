# Interceptors

拦截器是一种强大的机制，可以监视、重写和重试 `Call`。下面是一个简单的拦截器，进行记录传出的请求和传入的响应。

```java
class LoggingInterceptor implements Interceptor {
  @Override public Response intercept(Interceptor.Chain chain) throws IOException {
    Request request = chain.request();

    long t1 = System.nanoTime();
    logger.info(String.format("Sending request %s on %s%n%s",
        request.url(), chain.connection(), request.headers()));

    Response response = chain.proceed(request);

    long t2 = System.nanoTime();
    logger.info(String.format("Received response for %s in %.1fms%n%s",
        response.request().url(), (t2 - t1) / 1e6d, response.headers()));

    return response;
  }
}
```

调用 `chain.proceed(request)` 是每个拦截器实现的关键部分。这个看起来很简单的方法是所有 HTTP 工作的开始，会产生满足请求的响应。

拦截器可以进行链接的，假设你同时拥有「压缩拦截器」和「校验和拦截器」：你需要确定数据是否已压缩然后进行校验和，或校验和再后进行压缩。OkHttp 使用列表来跟踪拦截器，并按顺序进行调用。

<image src="./res/interceptors.png" width="360" />


## 应用程序拦截器

一个拦截器可以被注册为「应用程序拦截器」或「网络拦截器」。下面我们使用上面定义的 `LoggingInterceptor` 方法来显示这两者的差异。

通过调用 `OkHttpClient.Builder` 的 `addInterceptor()` 方法可注册一个应用程序拦截器：

```java
OkHttpClient client = new OkHttpClient.Builder()
    .addInterceptor(new LoggingInterceptor())
    .build();

Request request = new Request.Builder()
    .url("http://www.publicobject.com/helloworld.txt")
    .header("User-Agent", "OkHttp Example")
    .build();

Response response = client.newCall(request).execute();
response.body().close();
```

这个 `http://www.publicobject.com/helloworld.txt` URL 会重定向到 `https://publicobject.com/helloworld.txt`，并且 OkHttp 会自动跟随此重定向。我们的应用程序拦截器被调用 **一次**， `chain.proceed()` 返回的响应是重定向后的：


```
INFO: Sending request http://www.publicobject.com/helloworld.txt on null
User-Agent: OkHttp Example

INFO: Received response for https://publicobject.com/helloworld.txt in 1179.7ms
Server: nginx/1.4.6 (Ubuntu)
Content-Type: text/plain
Content-Length: 1759
Connection: keep-alive
```

可以看到被重定向了，因为 `response.request().url()` 不同于 `request.url()`。这两个日志语句记录了两个不同的 URL。


## 网络拦截器

注册网络拦截器与注册应用程序拦截器是非常相似的。但调用的方法是 `addNetworkInterceptor()` 而不是 `addInterceptor()`：

```java
OkHttpClient client = new OkHttpClient.Builder()
    .addNetworkInterceptor(new LoggingInterceptor())
    .build();

Request request = new Request.Builder()
    .url("http://www.publicobject.com/helloworld.txt")
    .header("User-Agent", "OkHttp Example")
    .build();

Response response = client.newCall(request).execute();
response.body().close();
```

当我们运行这个代码时，拦截器运行两次：

* 一次用于初始请求 `http://www.publicobject.com/helloworld.txt`
* 另一次用于重定向 `https://publicobject.com/helloworld.txt`


```
INFO: Sending request http://www.publicobject.com/helloworld.txt on Connection{www.publicobject.com:80, proxy=DIRECT hostAddress=54.187.32.157 cipherSuite=none protocol=http/1.1}
User-Agent: OkHttp Example
Host: www.publicobject.com
Connection: Keep-Alive
Accept-Encoding: gzip

INFO: Received response for http://www.publicobject.com/helloworld.txt in 115.6ms
Server: nginx/1.4.6 (Ubuntu)
Content-Type: text/html
Content-Length: 193
Connection: keep-alive
Location: https://publicobject.com/helloworld.txt

INFO: Sending request https://publicobject.com/helloworld.txt on Connection{publicobject.com:443, proxy=DIRECT hostAddress=54.187.32.157 cipherSuite=TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA protocol=http/1.1}
User-Agent: OkHttp Example
Host: publicobject.com
Connection: Keep-Alive
Accept-Encoding: gzip

INFO: Received response for https://publicobject.com/helloworld.txt in 80.9ms
Server: nginx/1.4.6 (Ubuntu)
Content-Type: text/plain
Content-Length: 1759
Connection: keep-alive
```

网络请求还包含更多的数据，例如 OkHttp 添加的 `Accept-Encoding: gzip` 请求头，用于通告支持响应压缩。网络拦截器 `Chain` 具有非空值 `Connection`，可用于询问用于连接到 Web 服务器的 IP 地址和 TLS 配置。


## 在应用程序和网络拦截器之间进行选择

每个拦截链都有相对的优点。


### 应用拦截器

* 不需要担心类似重定向或重试的中间响应
* 始终调用一次，即使 HTTP 响应是缓存提供的
* 观察应用程序的初始意图，没有关注 OkHttp 注入的标题 `If-None-Match`
* 允许 short-circuit 而不是调用 `Chain.proceed()`
* 允许重试并进行多次调用 `Chain.proceed()`


### 网络拦截器

* 能够对重定向和重试等中间响应进行操作
* 未针对 short-circuit 网络的缓存响应
* 观察数据，就像它将通过网络传输一样
* 访问携带请求的 `Connection`


## 重写请求

拦截器可以添加、删除或替换请求头，也可以转换那些拥有一个请求的主体内容。例如，如果要连接到已知支持它的 Web 服务器，则可以使用应用程序拦截器添加请求主体压缩。

```java
/** This interceptor compresses the HTTP request body. Many webservers can't handle this! */
final class GzipRequestInterceptor implements Interceptor {
  @Override public Response intercept(Interceptor.Chain chain) throws IOException {
    Request originalRequest = chain.request();
    if (originalRequest.body() == null || originalRequest.header("Content-Encoding") != null) {
      return chain.proceed(originalRequest);
    }

    Request compressedRequest = originalRequest.newBuilder()
        .header("Content-Encoding", "gzip")
        .method(originalRequest.method(), gzip(originalRequest.body()))
        .build();
    return chain.proceed(compressedRequest);
  }

  private RequestBody gzip(final RequestBody body) {
    return new RequestBody() {
      @Override public MediaType contentType() {
        return body.contentType();
      }

      @Override public long contentLength() {
        return -1; // We don't know the compressed length in advance!
      }

      @Override public void writeTo(BufferedSink sink) throws IOException {
        BufferedSink gzipSink = Okio.buffer(new GzipSink(sink));
        body.writeTo(gzipSink);
        gzipSink.close();
      }
    };
  }
}
```


## 重写响应

对称地，拦截器可以重写响应头并转换响应主体。这通常比重写请求头更危险，因为它可能违反了网络服务器的期望！

如果你处于棘手的情况并准备应对后果，重写响应头是解决问题的有效方法。例如，你可以修复服务器配置错误的 `Cache-Control` 响应头以启用更好的响应缓存：

```java
/** Dangerous interceptor that rewrites the server's cache-control header. */
private static final Interceptor REWRITE_CACHE_CONTROL_INTERCEPTOR = new Interceptor() {
  @Override public Response intercept(Interceptor.Chain chain) throws IOException {
    Response originalResponse = chain.proceed(chain.request());
    return originalResponse.newBuilder()
        .header("Cache-Control", "max-age=60")
        .build();
  }
};
```

通常，这种方法在补充 Web 服务器上的相应修补程序时效果最佳！


## 可用性

OkHttp 的拦截器需要 OkHttp 2.2 或更高版本。不幸的是，拦截器无法使用 `OkUrlFactory` 或者基于它的库，包括 [Retrofit](http://square.github.io/retrofit/) ≤ 1.8 和 [Picasso](http://square.github.io/picasso/) ≤ 2.4。
