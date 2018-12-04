# HTTPS

OkHttp 试图平衡两个相互竞争的问题：

* **连接到尽可能多的主机**：  
这包括运行最新版本的 [boringssl](https://boringssl.googlesource.com/boringssl/) 的高级主机，和运行较旧版本 [OpenSSL](https://www.openssl.org/) 的较少过时的主机。

* **连接的安全性**：  
这包括使用证书验证远程 Web 服务器以及使用强密码交换的数据的隐私。

在协商与 HTTPS 服务器的连接时，OkHttp 需要知道要提供哪些 TLS 版本和密码套件。希望最大化连接的客户端将包括过时的 [TLS versions](http://square.github.io/okhttp/3.x/okhttp/okhttp3/TlsVersion.html) 和 [cipher suites](http://square.github.io/okhttp/3.x/okhttp/okhttp3/CipherSuite.html)。想要最大化安全性的严格客户端将仅限于最新的 TLS 版本和最强的密码套件。

[ConnectionSpec](http://square.github.io/okhttp/3.x/okhttp/okhttp3/ConnectionSpec.html) 实现了特定的安全性与连接性决策。

OkHttp 包含三个内置连接规范：

* `MODERN_TLS`: 一种连接到现代 HTTPS 服务器的安全配置。
* `COMPATIBLE_TLS`: 一种安全配置，可连接到安全但非当前的 HTTPS 服务器。
* `CLEARTEXT`: 一种用于 `http://URL` 的不安全配置。

默认情况下，OkHttp 将尝试 `MODERN_TLS` 连接，`COMPATIBLE_TLS` 如果现代配置失败，则回退到连接。

每个规范中的 TLS 版本和密码套件都可以随每个版本而变化。例如，在 OkHttp 2.2 中，我们放弃了对 SSL 3.0 的支持以响应 [POODLE](http://googleonlinesecurity.blogspot.ca/2014/10/this-poodle-bites-exploiting-ssl-30.html) 攻击。在 OkHttp 2.3 中，我们放弃了对 [RC4](http://en.wikipedia.org/wiki/RC4#Security) 的支持。与你的桌面 Web 浏览器一样，保持与 OkHttp 的最新状态是保持安全的最佳方式。

你可以使用一组自定义 TLS 版本和密码套件构建自己的连接规范。例如，此配置仅限于三个备受推崇的密码套件。它的缺点是它需要 Android 5.0+ 和类似的网络服务器。

```java
ConnectionSpec spec = new ConnectionSpec.Builder(ConnectionSpec.MODERN_TLS)  
    .tlsVersions(TlsVersion.TLS_1_2)
    .cipherSuites(
          CipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
          CipherSuite.TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256,
          CipherSuite.TLS_DHE_RSA_WITH_AES_128_GCM_SHA256)
    .build();

OkHttpClient client = new OkHttpClient.Builder()
    .connectionSpecs(Collections.singletonList(spec))
    .build();
```


## [固定证书](https://github.com/square/okhttp/blob/master/samples/guide/src/main/java/okhttp3/recipes/CertificatePinning.java)

默认情况下，OkHttp 信任主机平台的证书颁发机构。此策略可最大限度地提高连接性，但它受到诸如 [2011 DigiNotar attack](http://www.computerworld.com/article/2510951/cybercrime-hacking/hackers-spied-on-300-000-iranians-using-fake-google-certificate.html) 等证书颁发机构的攻击。它还假定你的 HTTPS 服务器的证书由证书颁发机构签名。

使用 [CertificatePinner](http://square.github.io/okhttp/3.x/okhttp/okhttp3/CertificatePinner.html) 限制受信任的证书和证书颁发机构。证书锁定可提高安全性，但会限制服务器团队更新其 TLS 证书的能力。**没有服务器的 TLS 管理员的祝福，请不要使用固定证书！**

```java
public CertificatePinning() {
  client = new OkHttpClient.Builder()
      .certificatePinner(new CertificatePinner.Builder()
          .add("publicobject.com", "sha256/afwiKY3RxoMmLkuRW1l7QsPZTJPwDS2pdDROQjXw8ig=")
          .build())
      .build();
}

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("https://publicobject.com/robots.txt")
      .build();

  Response response = client.newCall(request).execute();
  if (!response.isSuccessful()) throw new IOException("Unexpected code " + response);

  for (Certificate certificate : response.handshake().peerCertificates()) {
    System.out.println(CertificatePinner.pin(certificate));
  }
}
```


## [自定义可信证书](https://github.com/square/okhttp/blob/master/samples/guide/src/main/java/okhttp3/recipes/CustomTrust.java)

完整的代码示例显示了如何使用你自己的集合替换主机平台的证书颁发机构。同上所述，**如果没有服务器的TLS管理员的祝福，请不要使用自定义证书！**

```java
private final OkHttpClient client;

public CustomTrust() {
  SSLContext sslContext = sslContextForTrustedCertificates(trustedCertificatesInputStream());
  client = new OkHttpClient.Builder()
      .sslSocketFactory(sslContext.getSocketFactory())
      .build();
}

public void run() throws Exception {
  Request request = new Request.Builder()
      .url("https://publicobject.com/helloworld.txt")
      .build();

  Response response = client.newCall(request).execute();
  System.out.println(response.body().string());
}

private InputStream trustedCertificatesInputStream() {
  ... // Full source omitted. See sample.
}

public SSLContext sslContextForTrustedCertificates(InputStream in) {
  ... // Full source omitted. See sample.
}
```
