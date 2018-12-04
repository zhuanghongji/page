# Connections

即使你只提供了 URL，但 OkHttp 会使用三种类型与你的 Web 服务器进行连接：

* URL
* 地址
* 路由

## [URLs](http://square.github.io/okhttp/3.x/okhttp/okhttp3/HttpUrl.html)

URLs (如 `https://github.com/square/okhttp`) 是 HTTP 和 Internet 的基础。除了作为 Web 上所有内容通用的标识和分散的命名之外，它们还指定了如何访问对应的 Web 资源。

URLs 是抽象的：

* 它们指定 `call` 可以是明文的 (`http`) 或加密的 (`https`)，但没有指定使用哪种加密算法。它们也没有指定如何验证对等方的证书 (the [HostnameVerifier](http://developer.android.com/reference/javax/net/ssl/HostnameVerifier.html)) 或可以信任哪些证书 (the [SSLSocketFactory](http://developer.android.com/reference/org/apache/http/conn/ssl/SSLSocketFactory.html))。
* 它们不指定是否应使用特定代理服务器或如何使用该代理服务器进行身份验证。

URLs 也是具体的：每个 URL 标识一个特定路径 (如 `/square/okhttp`) 和查询参数 (如 `?q=sharks&lang=en`)。每个 Web 服务器都托管许多 URLs。


## [Addresses](http://square.github.io/okhttp/3.x/okhttp/okhttp3/Address.html)

Addresses 指定一个 Web 服务器 (如 `github.com`) 以及连接到该服务器所需的所有 **静态** 配置：

* 端口号
* HTTPS 设置
* 首选网络协议 (如 HTTP/2 或 SPDY) 。

共享相同地址的 URLs 也可以共享相同的底层 TCP 套接字连接。共享连接具有显著的性能优势：

* 更低的延迟
* 更高的吞吐量 (得益于 [TCP slow start](http://www.igvita.com/2011/10/20/faster-web-vs-tcp-slow-start/))
* 节省电量

OkHttp 使用的 [ConnectionPool](http://square.github.io/okhttp/3.x/okhttp/okhttp3/ConnectionPool.html) 能够自动重用 HTTP/1.x 连接，并多路复用 HTTP/2 和 SPDY 连接。

在 OkHttp 中，address 的某些字段来自 URL (scheme、主机名、端口)，其余字段来自 [OkHttpClient](http://square.github.io/okhttp/3.x/okhttp/okhttp3/OkHttpClient.html)。


## [Routes](http://square.github.io/okhttp/3.x/okhttp/okhttp3/Route.html)

路由能够提供实际连接到 Web 服务器所需的 **动态** 信息。这是要尝试的特定 IP 地址 (由 DNS 查询发现)，要使用的确切代理服务器 (如果正在使用 [ProxySelector](http://developer.android.com/reference/java/net/ProxySelector.html)) ，以及要协商的 TLS 版本 (对于 HTTPS 连接) 。

单个地址可能有很多路由。例如，托管在多个数据中心中的 Web 服务器可能会在其 DNS 响应中生成多个 IP 地址。


## [Connections](http://square.github.io/okhttp/3.x/okhttp/okhttp3/Connection.html)

当你使用 OkHttp 请求 URL 时，它做了以下事情：

1. 它使用 URL 并配置 OkHttpClient 来创建一个 **address**。这个 address 指定了我们将如何连接到 Web 服务器。
2. 它尝试从连接池中检索具有该 address 的连接。
3. 如果它在池中找不到连接，则会选择要尝试的 **路由**。这通常意味着发出 DNS 请求以获取服务器的 IP 地址。然后，如有必要的话它会选择 TLS 版本和代理服务器。
4. 如果它是新路由，则通过构建一个直连的套接字连接和一个 TLS 通道 (通过 HTTP 代理的 HTTPS)，或一个直连的 TLS 连接。它根据需要进行 TLS 握手。
5. 它发送 HTTP 请求并读取响应。

如果连接出现问题，OkHttp 将选择另一条理由再次尝试连接。这允许 OkHttp 在服务器地址的子集无法访问时进行恢复。当连接池里的连接过时或者尝试的 TLS 版本不支持时，这同样显得很有用。

收到响应后，连接将返回到池中，以便可以将其重新用于将来的请求。一段时间不活动后，连接将从池中逐出。
