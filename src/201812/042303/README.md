# Calls

HTTP 客户端的工作是接受你的请求并生成其响应。这看起来很简单，但在实践中却很棘手。

## [Request](https://square.github.io/okhttp/3.x/okhttp/okhttp3/Request.html)

每个 HTTP 请求都包含：

* 一个 URL
* 一个方法 (如 `GET` 或 `POST`)
* 一系列的响应头 (`headers`)

请求还可以包含一个主体 (`body`)：一个特定内容类型的数据流。


## [Responses](https://square.github.io/okhttp/3.x/okhttp/okhttp3/Response.html)

请求对应的响应带有响应码 (例如 `200` 表示成功，或 `404` 表示未找到)、响应头及其自己的可选主体。


## 重写请求

当你向 OkHttp 提供一个 HTTP 请求时，你相当于站在一个更高的层次去描述这个请求：“请使用这些请求头来获取此 URL 的数据”。

为了正确性和效率，OkHttp 会在发起这个请求之前重写你的请求。

OkHttp 可能会在原始请求中添加确实的请求头，包括：

* `Content-Length`
* `Transfer-Encoding`
* `User-Agent`
* `Host`
* `Connection`
* `Content-Type`。

比如说：

* OkHttp 会添加一个 `Accept-Encoding` 请求头来为响应做压缩，除非该响应头已存在。
* 如果你已经有 `cookies` 的话，OkHttp 会添加一个 `Cookie` 请求头。

某些请求会有缓存的响应结果。当这个缓存的响应没有刷新时，OkHttp 会发起一个条件化的 **GET** 请求来下载响应结果，如果它比缓存还新的话就会更新缓存。当然，这需要你在请求头中添加 `If-Modified-Since` 和 `If-None-Match`。


## 重写响应

如果你使用了透明压缩，OkHttp 将删除相应的响应头 `Content-Encoding` 和 `Content-Length`，因为它们不适用于解压缩响应主体。

如果条件化的 **GET** 请求成功，来自网络和缓存的响应结果则会根据规范进行合并。


## 后续请求

当你请求的 URL 被移除后，Web 服务器将返回一个响应码 `302` 来指明对应文档内容的新 URL。OkHttp 将自动进行重定向来检索最终的响应。

如果响应发出授权验证，OkHttp 将询问 [`Authenticator`](http://square.github.io/okhttp/3.x/okhttp/okhttp3/Authenticator.html) (如果已配置）来满足这个授权验证。如果验证者提供了凭证，则会使用包含的凭据重试该请求。


## 重试请求

有时连接可能会直白失败：池连接失效并断开连接，或者无法访问 Web 服务器本身。OkHttp 将使用不同的路由重试该请求，如果其中一个可用的话。


## [Calls](https://square.github.io/okhttp/3.x/okhttp/okhttp3/Call.html)

通过重写、重定向、后续请求和重试，你简单的请求可能会产生许多请求和响应。OkHttp 使用了 `Call` 来模拟满足你的请求任务，尽管这需要许多的中间请求和响应。通常情况下，这不会很多！令人欣慰的一点是，如果你的 URL 被重定向或者故障转移到备用 IP 地址，你的代码会继续执行。

`Call` 的执行有两种方式：

* **同步**：你的线程会阻塞，直到响应可读。
* **异步**：将请求排入任何线程，并在响应可读时在另一个线程获得 [回调](http://square.github.io/okhttp/3.x/okhttp/okhttp3/Callback.html)。

你可以在任意线程取消 `Call`。如果该 `Call` 尚未完成，会导致失败！正在写入请求主体或读取响应主体的代码将在 `Call` 取消时将遭受到 `IOException`。


## 调度

对于同步调用来说，你可以自带线程并负责管理你同时发出的请求数。同时连接太多请求的话会浪费资源，太少的话则会导致延迟。

对于异步调用来说，`Dispatcher` 实现了最大同时请求的策略。你可以设置每个网络服务器的最大值 (默认为 5)，和总体最大值 (默认为 64)。
