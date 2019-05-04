# Picasso 源码解析：线程池设计和请求处理者

在前面的文章中，我们知道 Picasso 内部实现了一个线程池来执行图片加载操作，加载成功后会保存到内存缓冲并可能会显示到 ImageView 中。下面，我们就通过这篇文章来了解一下这两个相关功能的源码实现。

## 线程池设计

在 Picasso 内部自己实现了一个线程池类 `PicassoExecutorService`，完整源代码如下：

```java
class PicassoExecutorService extends ThreadPoolExecutor {
  // 默认线程数量为 3
  private static final int DEFAULT_THREAD_COUNT = 3;

  PicassoExecutorService() {
    super(DEFAULT_THREAD_COUNT, DEFAULT_THREAD_COUNT, 0, TimeUnit.MILLISECONDS,
        new PriorityBlockingQueue<Runnable>(), new Utils.PicassoThreadFactory());
  }

  // 根据网络情况来调整线程池的线程数量
  void adjustThreadCount(NetworkInfo info) {
    if (info == null || !info.isConnectedOrConnecting()) {
      setThreadCount(DEFAULT_THREAD_COUNT);
      return;
    }
    switch (info.getType()) {
      case ConnectivityManager.TYPE_WIFI:
      case ConnectivityManager.TYPE_WIMAX:
      case ConnectivityManager.TYPE_ETHERNET:
        setThreadCount(4);
        break;
      case ConnectivityManager.TYPE_MOBILE:
        switch (info.getSubtype()) {
          case TelephonyManager.NETWORK_TYPE_LTE:  // 4G
          case TelephonyManager.NETWORK_TYPE_HSPAP:
          case TelephonyManager.NETWORK_TYPE_EHRPD:
            setThreadCount(3);
            break;
          case TelephonyManager.NETWORK_TYPE_UMTS: // 3G
          case TelephonyManager.NETWORK_TYPE_CDMA:
          case TelephonyManager.NETWORK_TYPE_EVDO_0:
          case TelephonyManager.NETWORK_TYPE_EVDO_A:
          case TelephonyManager.NETWORK_TYPE_EVDO_B:
            setThreadCount(2);
            break;
          case TelephonyManager.NETWORK_TYPE_GPRS: // 2G
          case TelephonyManager.NETWORK_TYPE_EDGE:
            setThreadCount(1);
            break;
          default:
            setThreadCount(DEFAULT_THREAD_COUNT);
        }
        break;
      default:
        setThreadCount(DEFAULT_THREAD_COUNT);
    }
  }

  private void setThreadCount(int threadCount) {
    setCorePoolSize(threadCount);
    setMaximumPoolSize(threadCount);
  }

  @Override
  public Future<?> submit(Runnable task) {          // 1
    PicassoFutureTask ftask = new PicassoFutureTask((BitmapHunter) task);
    execute(ftask);
    return ftask;
  }

  // 接收 BitmapHunter 类型参数的 FutureTask
  private static final class PicassoFutureTask extends FutureTask<BitmapHunter>
      implements Comparable<PicassoFutureTask> {
    private final BitmapHunter hunter;

    PicassoFutureTask(BitmapHunter hunter) {
      super(hunter, null);
      this.hunter = hunter;
    }

    @Override
    public int compareTo(PicassoFutureTask other) {
      // 对比优先级
      Picasso.Priority p1 = hunter.getPriority();
      Picasso.Priority p2 = other.hunter.getPriority();

      // High-priority requests are "lesser" so they are sorted to the front.
      // Equal priorities are sorted by sequence number to provide FIFO ordering.
      return (p1 == p2 ? hunter.sequence - other.hunter.sequence : p2.ordinal() - p1.ordinal());
    }
  }
}
```

`PicassoExecutorService` 内部的核心逻辑是 `1` 处的 `submit(Runnable task)` 方法。如果你对之前文章还有印象的话，会注意到我们调用该方法时传入的是 `BitmapHunter` 对象。想不起来？好吧，我们附下相关源代码：

```java
class Dispatcher {
  // ...

  void performSubmit(Action action, boolean dismissFailed) {
    // ...

    hunter = forRequest(action.getPicasso(), this, cache, stats, action);
    hunter.future = service.submit(hunter);
    hunterMap.put(action.getKey(), hunter);
  }
}
```

`submit()` 方法是有返回值的，所以 `PicassoExecutorService` 内部将 `BitmapHunter` 包装成了 `PicassoFutureTask` 并实现了 `Comparable` 接口以进行优先级的处理。

再来回忆一下 `BitmapHunter` 的 `run()` 方法：

```java
class BitmapHunter implements Runnable {
  // ...

  Bitmap result;

  @Override public void run() {
    // ...

    try {
      // ...
      result = hunt();
    } catch() {
      // ...
    } finally {
      // ...
    }
  }

  Bitmap hunt() throws IOException {
    // ...

    RequestHandler.Result result = requestHandler.load(data, networkPolicy);
  }
}
```

可以看到 `run()` 内部调用的 `hunt()` 方法会通过 `requestHandler` 的 `load()` 方法来加载图片。  


## 请求处理者

### 概述

`RequestHandler` 是一个抽象类，其内部声明了以下抽象和实例方法：

```java
public abstract class RequestHandler {
  // ...

  // 返回值是布尔值，表示是否能处理参数中的图片加载请求 `Request`
  public abstract boolean canHandleRequest(Request data);

  // 根据给定的 Request 对象和网络策略来加载对应图片
  @Nullable public abstract Result load(Request request, int networkPolicy) throws IOException;

  // 获取重试次数
  int getRetryCount() {
    return 0;
  }

  // 是否可以重试
  boolean shouldRetry(boolean airplaneMode, NetworkInfo info) {
    return false;
  }

  // 是否支持重播
  boolean supportsReplay() {
    return false;
  }
}
```

其内部同时也声明了一些静态内部类和静态方法，比如：

静态内部类 `RequestHandler.Result`

```java
public abstract class RequestHandler {
  // ...

  public static final class Result {
    private final Picasso.LoadedFrom loadedFrom;
    private final Bitmap bitmap;
    private final Source source;
    private final int exifOrientation;

    public Result(@NonNull Bitmap bitmap, @NonNull Picasso.LoadedFrom loadedFrom) {
      this(checkNotNull(bitmap, "bitmap == null"), null, loadedFrom, 0);
    }

    public Result(@NonNull Source source, @NonNull Picasso.LoadedFrom loadedFrom) {
      this(null, checkNotNull(source, "source == null"), loadedFrom, 0);
    }

    Result(
        @Nullable Bitmap bitmap,
        @Nullable Source source,
        @NonNull Picasso.LoadedFrom loadedFrom,
        int exifOrientation) {
      if ((bitmap != null) == (source != null)) {
        throw new AssertionError();
      }
      this.bitmap = bitmap;
      this.source = source;
      this.loadedFrom = checkNotNull(loadedFrom, "loadedFrom == null");
      this.exifOrientation = exifOrientation;
    }

    /** The loaded {@link Bitmap}. Mutually exclusive with {@link #getSource()}. */
    @Nullable public Bitmap getBitmap() {
      return bitmap;
    }

    /** A stream of image data. Mutually exclusive with {@link #getBitmap()}. */
    @Nullable public Source getSource() {
      return source;
    }

    /**
     * Returns the resulting {@link Picasso.LoadedFrom} generated from a
     * {@link #load(Request, int)} call.
     */
    @NonNull public Picasso.LoadedFrom getLoadedFrom() {
      return loadedFrom;
    }

    /**
     * Returns the resulting EXIF orientation generated from a {@link #load(Request, int)} call.
     * This is only accessible to built-in RequestHandlers.
     */
    int getExifOrientation() {
      return exifOrientation;
    }
  }
}
```

静态方法：

```java
public abstract class RequestHandler {
  // ...

  static BitmapFactory.Options createBitmapOptions(Request data) {
    final boolean justBounds = data.hasSize();
    final boolean hasConfig = data.config != null;
    BitmapFactory.Options options = null;
    if (justBounds || hasConfig || data.purgeable) {
      options = new BitmapFactory.Options();
      options.inJustDecodeBounds = justBounds;
      options.inInputShareable = data.purgeable;
      options.inPurgeable = data.purgeable;
      if (hasConfig) {
        options.inPreferredConfig = data.config;
      }
    }
    return options;
  }

  static boolean requiresInSampleSize(BitmapFactory.Options options) {
    return options != null && options.inJustDecodeBounds;
  }

  static void calculateInSampleSize(int reqWidth, int reqHeight, BitmapFactory.Options options,
      Request request) {
    calculateInSampleSize(reqWidth, reqHeight, options.outWidth, options.outHeight, options,
        request);
  }

  static void calculateInSampleSize(int reqWidth, int reqHeight, int width, int height,
      BitmapFactory.Options options, Request request) {
    int sampleSize = 1;
    if (height > reqHeight || width > reqWidth) {
      final int heightRatio;
      final int widthRatio;
      if (reqHeight == 0) {
        sampleSize = (int) Math.floor((float) width / (float) reqWidth);
      } else if (reqWidth == 0) {
        sampleSize = (int) Math.floor((float) height / (float) reqHeight);
      } else {
        heightRatio = (int) Math.floor((float) height / (float) reqHeight);
        widthRatio = (int) Math.floor((float) width / (float) reqWidth);
        sampleSize = request.centerInside
            ? Math.max(heightRatio, widthRatio)
            : Math.min(heightRatio, widthRatio);
      }
    }
    options.inSampleSize = sampleSize;
    options.inJustDecodeBounds = false;
  }
}
```

### 具体实现类

既然有抽象类，那么就肯定有其具体实现类。`RequestHandler` 默认有以下几个具体实现类：
1. `AssetRequestHandler`
2. `ContactsPhotoRequestHandler`
3. `ContentStreamRequestHandler`
4. `FileRequestHandler`
5. `MediaStoreRequestHandler`
6. `NetworkRequestHandler`
7. `ResourceRequestHandler`

下面我们挑其中常用的 `1`、`4`、`6` 和 `7` 来讲解下相关源代码。

**`AssetRequestHandler.java`**

```java
class AssetRequestHandler extends RequestHandler {
  protected static final String ANDROID_ASSET = "android_asset";
  private static final int ASSET_PREFIX_LENGTH =
      (SCHEME_FILE + ":///" + ANDROID_ASSET + "/").length();

  private final Context context;
  private final Object lock = new Object();
  private AssetManager assetManager;

  AssetRequestHandler(Context context) {
    this.context = context;
  }

  @Override public boolean canHandleRequest(Request data) {
    Uri uri = data.uri;
    return (SCHEME_FILE.equals(uri.getScheme())
        && !uri.getPathSegments().isEmpty() && ANDROID_ASSET.equals(uri.getPathSegments().get(0)));
  }

  @Override public Result load(Request request, int networkPolicy) throws IOException {
    if (assetManager == null) {
      synchronized (lock) {
        if (assetManager == null) {
          assetManager = context.getAssets();
        }
      }
    }
    Source source = Okio.source(assetManager.open(getFilePath(request)));
    return new Result(source, DISK);
  }

  static String getFilePath(Request request) {
    return request.uri.toString().substring(ASSET_PREFIX_LENGTH);
  }
}
```

**`FileRequestHandler.java`**

```java
class FileRequestHandler extends ContentStreamRequestHandler {

  FileRequestHandler(Context context) {
    super(context);
  }

  @Override public boolean canHandleRequest(Request data) {
    return SCHEME_FILE.equals(data.uri.getScheme());
  }

  @Override public Result load(Request request, int networkPolicy) throws IOException {
    Source source = Okio.source(getInputStream(request));
    return new Result(null, source, DISK, getFileExifRotation(request.uri));
  }

  static int getFileExifRotation(Uri uri) throws IOException {
    ExifInterface exifInterface = new ExifInterface(uri.getPath());
    return exifInterface.getAttributeInt(TAG_ORIENTATION, ORIENTATION_NORMAL);
  }
}
```


**`NetworkRequestHandler.java`**

```java
class NetworkRequestHandler extends RequestHandler {
  private static final String SCHEME_HTTP = "http";
  private static final String SCHEME_HTTPS = "https";

  private final Downloader downloader;
  private final Stats stats;

  NetworkRequestHandler(Downloader downloader, Stats stats) {
    this.downloader = downloader;
    this.stats = stats;
  }

  @Override public boolean canHandleRequest(Request data) {
    String scheme = data.uri.getScheme();
    return (SCHEME_HTTP.equals(scheme) || SCHEME_HTTPS.equals(scheme));
  }

  @Override public Result load(Request request, int networkPolicy) throws IOException {
    okhttp3.Request downloaderRequest = createRequest(request, networkPolicy);
    Response response = downloader.load(downloaderRequest);
    ResponseBody body = response.body();

    if (!response.isSuccessful()) {
      body.close();
      throw new ResponseException(response.code(), request.networkPolicy);
    }

    // Cache response is only null when the response comes fully from the network. Both completely
    // cached and conditionally cached responses will have a non-null cache response.
    Picasso.LoadedFrom loadedFrom = response.cacheResponse() == null ? NETWORK : DISK;

    // Sometimes response content length is zero when requests are being replayed. Haven't found
    // root cause to this but retrying the request seems safe to do so.
    if (loadedFrom == DISK && body.contentLength() == 0) {
      body.close();
      throw new ContentLengthException("Received response with 0 content-length header.");
    }
    if (loadedFrom == NETWORK && body.contentLength() > 0) {
      stats.dispatchDownloadFinished(body.contentLength());
    }
    return new Result(body.source(), loadedFrom);
  }

  @Override int getRetryCount() {
    return 2;
  }

  @Override boolean shouldRetry(boolean airplaneMode, NetworkInfo info) {
    return info == null || info.isConnected();
  }

  @Override boolean supportsReplay() {
    return true;
  }

  private static okhttp3.Request createRequest(Request request, int networkPolicy) {
    CacheControl cacheControl = null;
    if (networkPolicy != 0) {
      if (NetworkPolicy.isOfflineOnly(networkPolicy)) {
        cacheControl = CacheControl.FORCE_CACHE;
      } else {
        CacheControl.Builder builder = new CacheControl.Builder();
        if (!NetworkPolicy.shouldReadFromDiskCache(networkPolicy)) {
          builder.noCache();
        }
        if (!NetworkPolicy.shouldWriteToDiskCache(networkPolicy)) {
          builder.noStore();
        }
        cacheControl = builder.build();
      }
    }

    okhttp3.Request.Builder builder = new okhttp3.Request.Builder().url(request.uri.toString());
    if (cacheControl != null) {
      builder.cacheControl(cacheControl);
    }
    return builder.build();
  }

  static class ContentLengthException extends IOException {
    ContentLengthException(String message) {
      super(message);
    }
  }

  static final class ResponseException extends IOException {
    final int code;
    final int networkPolicy;

    ResponseException(int code, int networkPolicy) {
      super("HTTP " + code);
      this.code = code;
      this.networkPolicy = networkPolicy;
    }
  }
}
```


**`ResourceRequestHandler.java`**

```java
class ResourceRequestHandler extends RequestHandler {
  private final Context context;

  ResourceRequestHandler(Context context) {
    this.context = context;
  }

  @Override public boolean canHandleRequest(Request data) {
    if (data.resourceId != 0) {
      return true;
    }

    return SCHEME_ANDROID_RESOURCE.equals(data.uri.getScheme());
  }

  @Override public Result load(Request request, int networkPolicy) throws IOException {
    Resources res = Utils.getResources(context, request);
    int id = Utils.getResourceId(res, request);
    return new Result(decodeResource(res, id, request), DISK);
  }

  private static Bitmap decodeResource(Resources resources, int id, Request data) {
    final BitmapFactory.Options options = createBitmapOptions(data);
    if (requiresInSampleSize(options)) {
      BitmapFactory.decodeResource(resources, id, options);
      calculateInSampleSize(data.targetWidth, data.targetHeight, options, data);
    }
    return BitmapFactory.decodeResource(resources, id, options);
  }
}
```


## 总结

// TBD