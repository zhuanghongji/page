# Recipes

我们已经写了一些 recipes，演示了如何使用 OkHttp 解决常见问题。仔细阅读它们，了解这一切是如何协同工作的。你可以自由剪切和粘贴这些示例，这本就是他们的目的所在。

## 同步 Get

下面的代码片段，演示了：

* 下载一个文件
* 打印其响应头
* 将其响应主体作为字符串打印出来

> 响应主体 `ResponseBody` 的 `string()` 方法对于小文档来说是非常方便和高效的。但如果响应主体很大的话 (大于 1MB)，应避免使用 `string()` 因为它会将整个文档加载到内存中。在这种情况下，应该将响应主体作为流来处理。

```java
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("https://publicobject.com/helloworld.txt")
      .build();

  try (Response response = client.newCall(request).execute()) {
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    Headers responseHeaders = response.headers();
    for (int i = 0; i < responseHeaders.size(); i++) {
      System.out.println(responseHeaders.name(i) + ": " + responseHeaders.value(i));
    }

    System.out.println(response.body().string());
  }
}
```

## 异步 Get

下面的代码片段，演示了：

* 在工作线程上下载文件，并在响应可读时进行回调
* 回调是在响应头准备好之后进行的
* 阅读响应正文可能仍会阻止

> OkHttp 目前没有提供异步 APIs 来接收部分响应主体。

```java
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("http://publicobject.com/helloworld.txt")
      .build();

  client.newCall(request).enqueue(new Callback() {
    @Override public void onFailure(Call call, IOException e) {
      e.printStackTrace();
    }

    @Override public void onResponse(Call call, Response response) throws IOException {
      try (ResponseBody responseBody = response.body()) {
        if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

        Headers responseHeaders = response.headers();
        for (int i = 0, size = responseHeaders.size(); i < size; i++) {
          System.out.println(responseHeaders.name(i) + ": " + responseHeaders.value(i));
        }

        System.out.println(responseBody.string());
      }
    }
  });
}
```

## 访问 Headers

通常情况下，HTTP Headers 的工作方式如 `Map<String, String>`：

* 每个字段都有一个值或没有
* 但是一些标题允许多个值，例如 Guava 的 [Multimap](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/collect/Multimap.html)。

例如，HTTP 响应提供多个 `Vary` headers 是合法且常见的。OkHttp 的 API 试图使两种情况都显得自然。

当写入请求头时，可使用 `header(name, value)` 设置唯一的 `name` 的值为 `value`。如果存在现有值，则在添加新值之前将它们删除。使用 `addHeader(name, value)` 添加一个请求头的值，无需删除当前对应存在的值。

当读取响应头时，可使用 `header(name)` 返回指定 `name` 的最后一次出现的值。通常这也是唯一的！如果没有值，`header(name)` 会返回 `null`。要将所有字段的值作为列表读取，请使用 `headers(name)`。

要访问所有标头，请使用Headers支持按索引访问的类。

```java
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("https://api.github.com/repos/square/okhttp/issues")
      .header("User-Agent", "OkHttp Headers.java")
      .addHeader("Accept", "application/json; q=0.5")
      .addHeader("Accept", "application/vnd.github.v3+json")
      .build();

  try (Response response = client.newCall(request).execute()) {
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println("Server: " + response.header("Server"));
    System.out.println("Date: " + response.header("Date"));
    System.out.println("Vary: " + response.headers("Vary"));
  }
}
```


## POST 一个字符串

使用 HTTP POST 将请求主体发送到服务端。下面的示例将一个 markdown 文档发布到一个会将 markdown 呈现为 HTML 的 Web 服务端。由于整个请求主体会同时存在内存中，因此需避免使用此 API 发布大型 (大于1 MB) 的文档。

```java
public static final MediaType MEDIA_TYPE_MARKDOWN
    = MediaType.parse("text/x-markdown; charset=utf-8");

private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  String postBody = ""
      + "Releases\n"
      + "--------\n"
      + "\n"
      + " * _1.0_ May 6, 2013\n"
      + " * _1.1_ June 15, 2013\n"
      + " * _1.2_ August 11, 2013\n";

  Request request = new Request.Builder()
      .url("https://api.github.com/markdown/raw")
      .post(RequestBody.create(MEDIA_TYPE_MARKDOWN, postBody))
      .build();

  try (Response response = client.newCall(request).execute()) {
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println(response.body().string());
  }
}
```


## POST 流

这里我们将请求主体作为流去 POST。这个请求主体的内容在写入时生成。下面的示例中，内容直接流入 [Okio](https://github.com/square/okio) 缓冲接收器。你的程序可能更青睐于一个 `OutputStream`，你可以从中获得 `BufferedSink.outputStream()`。

```java
public static final MediaType MEDIA_TYPE_MARKDOWN
      = MediaType.parse("text/x-markdown; charset=utf-8");

  private final OkHttpClient client = new OkHttpClient();

  public void run() throws Exception {
    RequestBody requestBody = new RequestBody() {
      @Override public MediaType contentType() {
        return MEDIA_TYPE_MARKDOWN;
      }

      @Override public void writeTo(BufferedSink sink) throws IOException {
        sink.writeUtf8("Numbers\n");
        sink.writeUtf8("-------\n");
        for (int i = 2; i <= 997; i++) {
          sink.writeUtf8(String.format(" * %s = %s\n", i, factor(i)));
        }
      }

      private String factor(int n) {
        for (int i = 2; i < n; i++) {
          int x = n / i;
          if (x * i == n) return factor(x) + " × " + i;
        }
        return Integer.toString(n);
      }
    };

    Request request = new Request.Builder()
        .url("https://api.github.com/markdown/raw")
        .post(requestBody)
        .build();

    try (Response response = client.newCall(request).execute()) {
      if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

      System.out.println(response.body().string());
    }
  }
```


## POST 一个文件

将文件用作请求主体是很容易的，示例如下：

```java
public static final MediaType MEDIA_TYPE_MARKDOWN
    = MediaType.parse("text/x-markdown; charset=utf-8");

private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  File file = new File("README.md");

  Request request = new Request.Builder()
      .url("https://api.github.com/markdown/raw")
      .post(RequestBody.create(MEDIA_TYPE_MARKDOWN, file))
      .build();

  try (Response response = client.newCall(request).execute()) {
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println(response.body().string());
  }
}
```


## POST 表单参数

使用 `FormBody.Builder` 建立一个请求，请求主体就像是一个 HTML `<form>` 标签。名称和值将使用与 HTML 兼容的表单 URL 编码进行编码。

```java
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  RequestBody formBody = new FormBody.Builder()
      .add("search", "Jurassic Park")
      .build();
  Request request = new Request.Builder()
      .url("https://en.wikipedia.org/w/index.php")
      .post(formBody)
      .build();

  try (Response response = client.newCall(request).execute()) {
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println(response.body().string());
  }
}
```


## POST 一个 multipart 请求

`MultipartBody.Builder` 可以构建与 HTML 文件上载表单兼容的复杂请求主体。multipart 请求主体的每个部分本身都是一个请求主体，并且可以定义自己的请求头。如果存在，这些请求头应该描述部分的主体，例如它的 `Content-Disposition`。对于 `Content-Length` 和 `Content-Type` 请求头信息，如果它们可用的话会被自动添加。

```java
/**
 * The imgur client ID for OkHttp recipes. If you're using imgur for anything other than running
 * these examples, please request your own client ID! https://api.imgur.com/oauth2
 */
private static final String IMGUR_CLIENT_ID = "...";
private static final MediaType MEDIA_TYPE_PNG = MediaType.parse("image/png");

private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  // Use the imgur image upload API as documented at https://api.imgur.com/endpoints/image
  RequestBody requestBody = new MultipartBody.Builder()
      .setType(MultipartBody.FORM)
      .addFormDataPart("title", "Square Logo")
      .addFormDataPart("image", "logo-square.png",
          RequestBody.create(MEDIA_TYPE_PNG, new File("website/static/logo-square.png")))
      .build();

  Request request = new Request.Builder()
      .header("Authorization", "Client-ID " + IMGUR_CLIENT_ID)
      .url("https://api.imgur.com/3/image")
      .post(requestBody)
      .build();

  try (Response response = client.newCall(request).execute()) {
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println(response.body().string());
  }
}
```


## 使用Moshi解析JSON响应

[Moshi](https://github.com/square/moshi) 是一个方便的 API 来在 JSON 和 Java 对象之间进行转换。在这里，我们使用它来解码来自 GitHub API 的 JSON 响应。

请注意，`ResponseBody.charStream()` 使用 `Content-Type` 响应头来选择解码响应主体时要使用的字符集。如果未指定 charset 则默认为 `UTF-8`。

```java
private final OkHttpClient client = new OkHttpClient();
private final Moshi moshi = new Moshi.Builder().build();
private final JsonAdapter<Gist> gistJsonAdapter = moshi.adapter(Gist.class);

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("https://api.github.com/gists/c2a7c39532239ff261be")
      .build();
  try (Response response = client.newCall(request).execute()) {
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    Gist gist = gistJsonAdapter.fromJson(response.body().source());

    for (Map.Entry<String, GistFile> entry : gist.files.entrySet()) {
      System.out.println(entry.getKey());
      System.out.println(entry.getValue().content);
    }
  }
}

static class Gist {
  Map<String, GistFile> files;
}

static class GistFile {
  String content;
}
```


## 响应缓存

要缓存响应，你需要一个可以读取和写入的缓存目录，以及缓存大小的限制。缓存目录应该是私有的，不受信任的应用程序不应该能够读取其内容！

让多个缓存同时访问同一缓存目录是错误的。大多数应用程序应该只调用 `new OkHttpClient()` 一次，进行缓存相关的配置，并在任何地方使用相同的实例。否则，两个缓存实例将相互踩踏，破坏响应缓存，并可能导致程序崩溃。

响应缓存使用 HTTP 标头进行所有配置。你可以添加类似 `Cache-Control: max-stale=3600` 的请求头，OkHttp 的缓存将遵循这些请求头。你的网络服务器会配置会通过使用自己的响应头配置响应的缓存时间，例如 `Cache-Control: max-age=9600`。有些缓存的响应头会响应强制缓存，强制网络响应，或强制使用条件 `GET` 验证网络响应。

```java
private final OkHttpClient client;

public CacheResponse(File cacheDirectory) throws Exception {
  int cacheSize = 10 * 1024 * 1024; // 10 MiB
  Cache cache = new Cache(cacheDirectory, cacheSize);

  client = new OkHttpClient.Builder()
      .cache(cache)
      .build();
}

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("http://publicobject.com/helloworld.txt")
      .build();

  String response1Body;
  try (Response response1 = client.newCall(request).execute()) {
    if (!response1.isSuccessful()) throw new IOException("Unexpected code " + response1);

    response1Body = response1.body().string();
    System.out.println("Response 1 response:          " + response1);
    System.out.println("Response 1 cache response:    " + response1.cacheResponse());
    System.out.println("Response 1 network response:  " + response1.networkResponse());
  }

  String response2Body;
  try (Response response2 = client.newCall(request).execute()) {
    if (!response2.isSuccessful()) throw new IOException("Unexpected code " + response2);

    response2Body = response2.body().string();
    System.out.println("Response 2 response:          " + response2);
    System.out.println("Response 2 cache response:    " + response2.cacheResponse());
    System.out.println("Response 2 network response:  " + response2.networkResponse());
  }

  System.out.println("Response 2 equals Response 1? " + response1Body.equals(response2Body));
}
```

* 要防止响应使用缓存，请使用 [`CacheControl.FORCE_NETWORK`](http://square.github.io/okhttp/3.x/okhttp/okhttp3/CacheControl.html#FORCE_NETWORK)。
* 要防止使用网络，请使用 [`CacheControl.FORCE_CACHE`](http://square.github.io/okhttp/3.x/okhttp/okhttp3/CacheControl.html#FORCE_CACHE)。

> 警告：如果你使用 `FORCE_CACHE` 并且响应需要网络，OkHttp 将返回 `504 Unsatisfiable Request` 响应。


## 取消一个 Call

使用 `Call.cancel()` 立即停止正在进行的 call 请求。如果某个线程当前正在写一个请求或正在读取一个响应，它将收到一个 `IOException`。当不再需要 call 时，你可以使用它来 conserve 网络; 例如，当你的用户导航离开应用程序时，同步和异步调用都可以取消。

```java
private final ScheduledExecutorService executor = Executors.newScheduledThreadPool(1);
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("http://httpbin.org/delay/2") // This URL is served with a 2 second delay.
      .build();

  final long startNanos = System.nanoTime();
  final Call call = client.newCall(request);

  // Schedule a job to cancel the call in 1 second.
  executor.schedule(new Runnable() {
    @Override public void run() {
      System.out.printf("%.2f Canceling call.%n", (System.nanoTime() - startNanos) / 1e9f);
      call.cancel();
      System.out.printf("%.2f Canceled call.%n", (System.nanoTime() - startNanos) / 1e9f);
    }
  }, 1, TimeUnit.SECONDS);

  System.out.printf("%.2f Executing call.%n", (System.nanoTime() - startNanos) / 1e9f);
  try (Response response = call.execute()) {
    System.out.printf("%.2f Call was expected to fail, but completed: %s%n",
        (System.nanoTime() - startNanos) / 1e9f, response);
  } catch (IOException e) {
    System.out.printf("%.2f Call failed as expected: %s%n",
        (System.nanoTime() - startNanos) / 1e9f, e);
  }
}
```

## [Timeouts](https://github.com/square/okhttp/blob/master/samples/guide/src/main/java/okhttp3/recipes/ConfigureTimeouts.java)

当对等方无法访问时，可使用 timeouts 来使 call 失败。网络分区可能是由于客户端连接问题，服务器可用性问题或其他任何问题。OkHttp 支持连接、读取和写入超时。

```java
private final OkHttpClient client;

public ConfigureTimeouts() throws Exception {
  client = new OkHttpClient.Builder()
      .connectTimeout(10, TimeUnit.SECONDS)
      .writeTimeout(10, TimeUnit.SECONDS)
      .readTimeout(30, TimeUnit.SECONDS)
      .build();
}

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("http://httpbin.org/delay/2") // This URL is served with a 2 second delay.
      .build();

  try (Response response = client.newCall(request).execute()) {
    System.out.println("Response completed: " + response);
  }
}
```


## Call 的单独配置

所有的 HTTP 客户端配置都包含在 `OkHttpClient` 中，包括代理设置，超时和缓存。当你需要更改单个 Call 的配置时，请使用 `OkHttpClient.newBuilder()`，这将返回与原始客户端共享相同连接池、调度程序和配置的构建器。在下面的示例中，我们发出一个请求，其中 500 毫秒超时，另一个请求 3000 毫秒超时。

```java
private final OkHttpClient client = new OkHttpClient();

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("http://httpbin.org/delay/1") // This URL is served with a 1 second delay.
      .build();

  // Copy to customize OkHttp for this request.
  OkHttpClient client1 = client.newBuilder()
      .readTimeout(500, TimeUnit.MILLISECONDS)
      .build();
  try (Response response = client1.newCall(request).execute()) {
    System.out.println("Response 1 succeeded: " + response);
  } catch (IOException e) {
    System.out.println("Response 1 failed: " + e);
  }

  // Copy to customize OkHttp for this request.
  OkHttpClient client2 = client.newBuilder()
      .readTimeout(3000, TimeUnit.MILLISECONDS)
      .build();
  try (Response response = client2.newCall(request).execute()) {
    System.out.println("Response 2 succeeded: " + response);
  } catch (IOException e) {
    System.out.println("Response 2 failed: " + e);
  }
}
```


## [处理身份验证](https://github.com/square/okhttp/blob/master/samples/guide/src/main/java/okhttp3/recipes/Authenticate.java)

OkHttp 可以自动重试未经身份验证的请求。当响应是 `401 Not Authorized`，`Authenticator` 会被要求提供凭证。实现上，应该构建一个包含缺少凭据的新请求，如果没有可用的凭据，则返回 `null` 以跳过重试。

使用 `Response.challenges()` 来获取任何身份验证的 schemes 和 realms。在完成 `Basic` 验证时，使用 `Credentials.basic(username, password)` 编码请求头。

```java
private final OkHttpClient client;

public Authenticate() {
  client = new OkHttpClient.Builder()
      .authenticator(new Authenticator() {
        @Override public Request authenticate(Route route, Response response) throws IOException {
          if (response.request().header("Authorization") != null) {
            return null; // Give up, we've already attempted to authenticate.
          }

          System.out.println("Authenticating for response: " + response);
          System.out.println("Challenges: " + response.challenges());
          String credential = Credentials.basic("jesse", "password1");
          return response.request().newBuilder()
              .header("Authorization", credential)
              .build();
        }
      })
      .build();
}

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("http://publicobject.com/secrets/hellosecret.txt")
      .build();

  try (Response response = client.newCall(request).execute()) {
    if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

    System.out.println(response.body().string());
  }
}
```

为避免在身份验证不起作用时进行多次重试，你可以返回 `null` 以放弃。例如，你可能希望在尝试这些确切凭据时跳过重试：

```java
if (credential.equals(response.request().header("Authorization"))) {
  return null; // If we already failed with these credentials, don't retry.
 }
```

当你达到应用程序定义的尝试限制时，你也可以跳过重试：

```java
if (responseCount(response) >= 3) {
  return null; // If we've failed 3 times, give up.
}
```

以上代码依赖于此 `responseCount()` 方法：

```java
private int responseCount(Response response) {
  int result = 1;
  while ((response = response.priorResponse()) != null) {
    result++;
  }
  return result;
}
```
