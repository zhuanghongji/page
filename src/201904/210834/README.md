# 深入理解 Handler

在 Android 开发过程中，我们经常会通过 Handler 从子线程切换到主线程进行 UI 更新的操作，当然也可以进行一些其它操作。总的来说：
* Handler 是 Android SDK 提供给开发者方便进行异步消息处理的类。
* 我们平时使用的 `AsyncTask` 和 `Retrofit`，其内部都使用到了 Handler 机制并进行一些巧妙的封装。

在这篇文章中，我们会从「基础知识」和「源码分析」这两个维度去深入理解 Handler 的基本使用和实现原理，以便能够在开发过程能够写出更高质量的代码。

## 基础知识

### 介绍

`Handler` 允许你发送和处理 `Message` 和 `Runnable` 对象 (跟线程的 `MessageQueue` 相关联)。每个 Handler 实例都与一个线程以及该线程的消息队列相关联。当你创建一个新的 Handler 时，它会被绑定到正在创建它的线程的 "线程/消息队列"。从那时起，它就会传递 messages 和 runnables 到消息队列中，然后在它们出队列时指向对应逻辑。

`Handler` 主要有两个用途：
1. 调度 messages 和 runnables 在将来的某个时刻执行。
2. 将「在不同于自己线程上执行的操作」压入队列中。

已实现的消息调度方法有：
* `post(Runnable)`
* `postAtTime(java.lang.Runnable, long)`
* `postDelayed(Runnable, Object, long)`
* `sendEmptyMessage(int)`
* `sendMessage(Message)` 
* `sendMessageAtTime(Message, long)` 
* `sendMessageDelayed(Message, long)`

Handler 提供的 `post(Runnable)` 方法允许你将 `Runnable` 对象放到消息队列中，并在它们被消息队列接收时进行调用。另外提供的 `handleMessage(Message)` 方法允许你将一个包含 bundle 数据的 `Message` 对象放到消息队列中，并在该 Handler 子类实现的 `handleMessage(Message)` 方法中进行处理。

不管是 post Runnable 还是 send Message，你都可以指定其是在消息队列准备好后马上 (或延迟一段时间后、或在某个具体时间) 进行处理。后两者允许你实现超时 (timeouts)、嘀嗒 (ticks) 等基于时间的一些行为，在下文中的示例代码会演示如何实现。

在为应用程序创建进程时，其主线程专门用于运行某个消息队列，这个队列负责管理顶级 (top-level) 的应用程序对象 (比如 Activity，BroadcastReceiver) 及其创建的任何窗口 (Window)。你可以创建自己的线程，并通过 Handler 的 post 或 sendMessage 方法与主线程进行通信，给定的 Runnable 或 Message 会在一个适当的时机被消息队列进行调度和处理。

### 使用示例

我们先来看一个简单的 handleeMessage 示例：

```java
// 创建一个 Handler 实例并覆写它的 handleMessage 方法
// 触发按钮事件后，在子线程中发送一个空消息到消息队列
public class MainActivity extends AppCompatActivity {

    private static final String TAG = "MainActivity";
    private static final int MSG_WHAT_FINISH = 0x01;

    @SuppressLint("HandlerLeak")
    private Handler mHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            switch (msg.what) {
                case MSG_WHAT_FINISH:
                    Log.i(TAG, "mHandler 所在线程的 id = "
                            + Thread.currentThread().getId());
                    break;
                default:
                    break;
            }
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button btnStart = findViewById(R.id.btn_start);
        btnStart.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                // 启动一个耗时线程
                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        try {
                            Log.i(TAG, "耗时线程的 id = "
                                    + Thread.currentThread().getId());
                            Thread.sleep(2000);
                            mHandler.sendEmptyMessage(MSG_WHAT_FINISH);
                        } catch (InterruptedException e) {
                            e.printStackTrace();
                        }

                    }
                }).start();
            }
        });
    }
}
```

点击 `btnStart` 按钮后，日志输出如下：

```java
2019-03-12 16:56:27.731 I/MainActivity: 耗时线程的 id = 7269
2019-03-12 16:56:29.733 I/MainActivity: mHandler 所在线程的 id = 2
```

在上面的示例代码中，你可能注意到在创建 `mHandler` 时我们加了个 `@SuppressLint("HandlerLeak")` 注解，以抑制 Lint 的 `This Handler class should be static or leaks might occur..` 警告。如果你不想增加这个注解，也不想看到 Lint 警告，可以换成这种写法：

```java
private Handler mHandler = new Handler(new Handler.Callback() {
    @Override
    public boolean handleMessage(Message msg) {
        switch (msg.what) {
            case MSG_WHAT_FINISH:
                Log.i(TAG, "mHandler 所在线程的 id = "
                        + Thread.currentThread().getId());
                return true;
            default:
                return false;
        }
    }
});
```

> 注意：不管是上述哪种写法，其实都没有真正解决 Handler 可能带来的内存泄漏问题。

在前面的介绍中，我们提到可以利用 Handler 实现超时、滴答等基于时间的行为，下面我们分别通过代码来演示下。

**超时示例：**

```java
Log.i(TAG, "开始");
int delayMillis = 6000;
final Runnable timeoutRunnable = new Runnable() {
    @Override
    public void run() {
        Log.i(TAG, "超时了噢...");
    }
};
mHandler.postDelayed(timeoutRunnable, delayMillis);

btnRemove.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Log.i(TAG, "移除 timeoutRunnable");
        mHandler.removeCallbacks(timeoutRunnable);
    }
});
```

如果你在 `6000` 内都没有点击 `btnRemove` 按钮，日志输出如下：

```java
2019-03-12 17:19:31.160 I/MainActivity: 开始
2019-03-12 17:19:37.168 I/MainActivity: 超时了噢...
```

如果你在 `6000` 内都点击了 `btnRemove` 按钮，日志输出如下：

```java
2019-03-12 17:20:54.895 I/MainActivity: 开始
2019-03-12 17:20:57.319 I/MainActivity: 移除 timeoutRunnable
```


**滴答示例：**

```java
final int delayMillis = 1000;
final Runnable ticksRunnable = new Runnable() {
    @Override
    public void run() {
        Log.i(TAG, "滴..");
        mHandler.postDelayed(this, delayMillis);
    }
};
mHandler.postDelayed(ticksRunnable, delayMillis);

btnStop.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Log.i(TAG, "停止滴答");
        mHandler.removeCallbacks(ticksRunnable);
    }
});
```

会每隔一秒输出 `滴..` 日志，直到你点击了 `btnStop` 按钮。

```java
2019-03-12 17:26:03.702 I/MainActivity: 滴..
2019-03-12 17:26:04.705 I/MainActivity: 滴..
2019-03-12 17:26:05.709 I/MainActivity: 滴..
2019-03-12 17:26:06.722 I/MainActivity: 停止滴答
```

### Handler 的四大组件和运作机制

Handler 的四大组件分别是 `Handler`、`Looper`、`Message`、`MessageQueue`，四者相辅相成。

先来看一个示意图：

<img src="./res/001.png" width="480">

* `Handler`: 用来发送和处理消息。
* `Looper`: 读取消息队列中的消息并交由对应的 Handler 进行处理。
* `Message`: 消息，由 Handler 发送并处理。
* `MessageQueue`: 管理 Message 的队列，即消息队列。


再来看一个示意图：

<img src="./res/002.png" width="600">

* Looper 由线程持有，且每个线程只有一个 Lopper。
* MessageQueue 由 Looper 创建并管理，原则是先进先出。
* Message 会持有发送它的 Handler 的引用，在 Looper 将其从消息队列中取出时会调用 `target.handleMessage()` 进行处理。
* 一个 Looper 可对应多个 Handler。

怎么理解「一个 Looper 可对应多个 Handler」？
* 比如说，你在不同的 Activity 中创建了多个 Handler。如果都是在主线程中进行创建的，那么它们都对应于主线程持有的同一个 Looper。

另外，如果希望 Handler 正常工作，在当前线程就必须由一个 Looper 对象。

我们来演示一下当前线程没有 Looper 的情况：

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        Handler handler = new Handler();    // 1
    }
}).start();
```

铛铛铛.. 在执行 `1` 这行代码时报错了，日志如下：

```java
2019-03-12 17:43:22.151 E/AndroidRuntime: FATAL EXCEPTION: Thread-2
    Process: com.zhuanghongji.handler, PID: 30186
    java.lang.RuntimeException: Can't create handler inside thread Thread[Thread-2,5,main] that has not called Looper.prepare()
        at android.os.Handler.<init>(Handler.java:205)
        at android.os.Handler.<init>(Handler.java:118)
        at com.zhuanghongji.handler.MainActivity$2.run(MainActivity.java:78)
        at java.lang.Thread.run(Thread.java:764)
```

根据报错日志可以看出：我们是不能在没有 `Looper.prepare()` 的线程中创建 Handler 的。那像上面的报错代码，我们应该如何在新的线程中创建 Handler 呢？修改代码后的正确姿势如下：

```java
// 正确姿势1
new Thread(new Runnable() {
    @Override
    public void run() {
        Looper.prepare();
        @SuppressLint("HandlerLeak")
        Handler handler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                super.handleMessage(msg);
                Log.i(TAG, "姿势1A：current thread id = " + Thread.currentThread().getId());
            }
        };
        handler.sendEmptyMessage(0x01);
        handler.post(new Runnable() {
            @Override
            public void run() {
                Log.i(TAG, "姿势1B：current thread id = " + Thread.currentThread().getId());
            }
        });
        Looper.loop();
        Log.i(TAG, "这行代码不会执行");
    }
}).start();

// 正确姿势2
new Thread(new Runnable() {
    @Override
    public void run() {
        Handler handler = new Handler(getMainLooper());
        handler.post(new Runnable() {
            @Override
            public void run() {
                Log.i(TAG, "姿势2：current thread id = " + Thread.currentThread().getId());
            }
        });
    }
}).start();
```

执行上面代码片段后，输出日志如下：

```java
2019-03-13 01:22:10.073 I/MainActivity: main thread id = 2
2019-03-13 01:22:10.075 I/MainActivity: 姿势1A：current thread id = 7540
2019-03-13 01:22:10.075 I/MainActivity: 姿势1B：current thread id = 7540
2019-03-13 01:22:10.127 I/MainActivity: 姿势2：current thread id = 2
```

注意，没有输出 `这行代码不会执行` 这个日志噢。因为 `Looper.loop()` 方法内部是一个 `for (;;) { ... }` 死循环，会阻塞当前线程的运行，除非你显示调用了 `handler.getLooper().quit()` 方法来结束这个循环。

另外从日志打印出来的线程 id 可以看出，姿势 1 中创建的 Handler 不管是 sendMessage 还是 post 的最后处理逻辑都是在子线程中运行的。也就是说，对应的 `handleMessage` 和 `run` 方法并不都是在主线程中执行，得看主线程的 Looper 对应哪一个线程。

<!-- ### 小结

* Handler 在创建的时候会获取 -->


## 源码分析

### 创建 Handler

<!-- 在前面的基础知识部分，我们知道 Handler 机制中有四大组件，下面我们来逐个分析对应的源码。

**Handler** -->

`Handler` 隐式继承自 `Object`，先来看它的一个主要的构造方法源码：

```java
public class Handler {

  public Handler(Callback callback, boolean async) {
      if (FIND_POTENTIAL_LEAKS) {
          final Class<? extends Handler> klass = getClass();
          if ((klass.isAnonymousClass() || klass.isMemberClass() || klass.isLocalClass()) &&
                  (klass.getModifiers() & Modifier.STATIC) == 0) {
              Log.w(TAG, "The following Handler class should be static or leaks might occur: " +
                  klass.getCanonicalName());
          }
      }

      mLooper = Looper.myLooper();                // 1
      if (mLooper == null) {
          throw new RuntimeException(
              "Can't create handler inside thread " + Thread.currentThread()
                      + " that has not called Looper.prepare()");
      }
      mQueue = mLooper.mQueue;
      mCallback = callback;
      mAsynchronous = async;
  }

  // ...
}
```

```java
public final class Looper {
    // sThreadLocal.get() will return null unless you've called prepare().
    static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
    private static Looper sMainLooper;  // guarded by Looper.class

    public static @Nullable Looper myLooper() {   // 2
        return sThreadLocal.get();
    }

    // ...
}
```

结合 `1` 和 `2` 两处可以看出，Handler 在创建的时候会通过 `ThreadLocal` 获取到当前线程的 `Looper` 对象，如果该对象为 `null` 就会抛出 `not called Looper.prepare()` 异常。那 Looper 应该怎样去 `prepare()` 呢？

我们来看 Looper 类里面其中一个用于 prepare 的静态方法：

```java
public final class Looper {

    public static void prepare() {
        prepare(true);
    }

    private static void prepare(boolean quitAllowed) {  
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper(quitAllowed));
    }

    public static void prepareMainLooper() {
        prepare(false);
        synchronized (Looper.class) {
            if (sMainLooper != null) {
                throw new IllegalStateException("The main Looper has already been prepared.");
            }
            sMainLooper = myLooper();
        }
    }

    // ...
}
```

在 `prepare(boolean quitAllowed)` 方法中，会先从 `sThreadLocal` 中获取 `Looper` 对象，如果不为 `null` 的话就会抛出异常，因为一个线程中只能有一个 Looper 对象。也就是说，在同一个线程中，我们只能调用 `Looper.prepare()` 一次。

看到这里可能你会想，我们平时在 Activity 中创建 Handler 时并不需要显式调用 `Looper.prepare()` 方法呀，其中是有什么黑科技么？没什么黑科技，这只是因为有人提前调用了 "另一个用于 prepare 的静态方法" 来帮你准备好主线程的 Looper 对象而已。不信你看下面的代码片段：

```java
public final class Looper {
    // 主线程的 Looper 实例
    private static Looper sMainLooper;  // guarded by Looper.class

    // 另一个用于 prepare 的静态方法
    public static void prepareMainLooper() {
        prepare(false);
        synchronized (Looper.class) {
            if (sMainLooper != null) {
                throw new IllegalStateException("The main Looper has already been prepared.");
            }
            sMainLooper = myLooper();
        }
    }

    // 获取主线程 Looper 实例的静态方法
    public static Looper getMainLooper() {
        synchronized (Looper.class) {
            return sMainLooper;
        }
    }

    // ...
}
```

那 `prepareMainLooper()` 这个静态方法是什么时候被调用的呢？`Command + Mouse Left` 弹出菜单：

<img src="./res/003.png" width="600">

来看下 `ActivityThread` 中第 6624 行代码：

```java

/**
 * Defines operations that a {@link android.app.servertransaction.ClientTransaction} or its items
 * can perform on client.
 */
public abstract class ClientTransactionHandler {
    // ...
}

/**
 * This manages the execution of the main thread in an
 * application process, scheduling and executing activities,
 * broadcasts, and other operations on it as the activity
 * manager requests.
 */
public final class ActivityThread extends ClientTransactionHandler {
  // ...

  public static void main(String[] args) {
        Process.setArgV0("<pre-initialized>");

        Looper.prepareMainLooper();             // Line 6624

        // ...
  }
}
```

`Looper.prepareMainLooper();` 是在 `ActivityThread` 的静态 `main` 方法中进行调用的。`ActivityThread` 是整个应用程序的核心类之一，它的 `main` 方法是整个应用进程的入口。也就是说，在应用启动时系统会在其主线程中 prepared 好对应的 Looper 实例，这样我们在主线程中就可以直接创建 Handler 了。

### Handler.sendMessage(Message) 和 Handler.post(Runnable)

**Message**

在分析 Handler 的 `sendMessage()` 和 `post(Runnable)` 方法之前，我们先来看一下 `Message` 是什么先。

```java
public final class Message implements Parcelable {
  public int what;
  long when;
  Bundle data;
  Handler target;
  Runnable callback;
  
  // sometimes we store linked lists of these things
  Message next;

  private static Message sPool;
  private static int sPoolSize = 0;

  public Message() {}

  /**
   * Return a new Message instance from the global pool. Allows us to
   * avoid allocating new objects in many cases.
   */
  public static Message obtain() {
      synchronized (sPoolSync) {
          if (sPool != null) {
              Message m = sPool;
              sPool = m.next;
              m.next = null;
              m.flags = 0; // clear in-use flag
              sPoolSize--;
              return m;
          }
      }
      return new Message();
  }

  /**
   * Recycles a Message that may be in-use.
   * Used internally by the MessageQueue and Looper when disposing of queued Messages.
   */
  void recycleUnchecked() {
      // Mark the message as in use while it remains in the recycled object pool.
      // Clear out all other details.
      flags = FLAG_IN_USE;
      what = 0;
      // ...
      when = 0;
      target = null;
      callback = null;
      data = null;

      synchronized (sPoolSync) {
          if (sPoolSize < MAX_POOL_SIZE) {
              next = sPool;
              sPool = this;
              sPoolSize++;
          }
      }
  }

  // ...
}
```

贴的代码有点长，没关系我们慢慢看。

由上面的代码可以看出，`Message` 其实是一个 Parcelable 的包装类，其内部包装了：
* 公开的、用户定义的消息标识 `what`。
* 传递 Bundle 数据的 `data` 。
* 发送该消息的 Handler 的引用 `target`。
* 可执行的 Runnable 对象 `callback`。
* 下一条消息的引用 `next`。

同时，`Message` 对象也暴露出了一个静态的 `obtain()` 方法来让我们创建新的 `Message` 对象，或直接从缓存中复用回收的 `Message` 对象 (以便在一些场景下避免重新分配新的对象)。

**Handler.sendMessage(Message)**

```java
public class Handler {
  public final boolean sendMessage(Message msg){
      return sendMessageDelayed(msg, 0);
  }

  public final boolean sendMessageDelayed(Message msg, long delayMillis) {
      if (delayMillis < 0) {
          delayMillis = 0;
      }
      return sendMessageAtTime(msg, SystemClock.uptimeMillis() + delayMillis);
  }

  public boolean sendMessageAtTime(Message msg, long uptimeMillis) {
      MessageQueue queue = mQueue;
      if (queue == null) {
          RuntimeException e = new RuntimeException(
                  this + " sendMessageAtTime() called with no mQueue");
          Log.w("Looper", e.getMessage(), e);
          return false;
      }
      return enqueueMessage(queue, msg, uptimeMillis);
  }

  private boolean enqueueMessage(MessageQueue queue, Message msg, long uptimeMillis) {
      msg.target = this;
      if (mAsynchronous) {
          msg.setAsynchronous(true);
      }
      return queue.enqueueMessage(msg, uptimeMillis);
  }

  // ...
}
```

由上面剪切出来的代码可以看出，发送消息的方法其中最终调用的都是 `enqueueMessage()` 方法，在 `enqueueMessage()` 方法中，先是把消息对象中的 `target` 引用指向当前发送该消息的 Handle，然后在通过消息队列的 `enqueueMessage(Message msg, long when)` 方法将该消息压入栈中。

> `sendEmptyMessage(int what)` 最终也是通过调用 `sendMessageAtTime()` 来讲消息对象压入消息队列中的，只不过中间是多了一个通过调用 `Message.obtain()` 获取消息对象并设置对应的 `what` 属性值的过程。

**Handler.post(Runnable)**

前面我们讲到，`Message` 内部有一个 `Runnable callback` 引用，难道我们 `post` 出去的 Runnable 最终的行为也是将一个 Message 对象压入到消息队列中？是的，想象力丰富的你猜想也是对的，相关源代码如下：

```java
public class Handler {
    public final boolean post(Runnable r) {
       return sendMessageDelayed(getPostMessage(r), 0);
    }

    public final boolean postDelayed(Runnable r, long delayMillis) {
        return sendMessageDelayed(getPostMessage(r), delayMillis);
    }

    private static Message getPostMessage(Runnable r) {
        Message m = Message.obtain();
        m.callback = r;
        return m;
    }

    // ...
}
```

由上面的代码片段可看出，`post(Runnable r)` 通过调用 `sendMessageDelayed()` 方法来最终调用 `enqueueMessage()` 方法来将消息对象压入队列中。只不过是中间多了一个调用 `getPostMessage()` 方法来将 `Runnable` 包装成 `Message` 的过程。


### 创建 Looper

`Looper` 类有两个关键的静态方法：
* `Looper.prepare()` 
* `Looper.loop()` 

前面我们讲到，在子线程中我们可以通过调用静态的 `Looper.prepare()` 方法来为当前线程创建对应的 `Looper` 对象。现在我们来分析一下相关源代码：

```java
public final class Looper {

    static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
    final MessageQueue mQueue;
    final Thread mThread;

    public static void prepare() {
        prepare(true);
    }

    private static void prepare(boolean quitAllowed) {
        if (sThreadLocal.get() != null) {
            throw new RuntimeException("Only one Looper may be created per thread");
        }
        sThreadLocal.set(new Looper(quitAllowed));
    }

    private Looper(boolean quitAllowed) {
        mQueue = new MessageQueue(quitAllowed);
        mThread = Thread.currentThread();
    }

    // ...
}
```

构造方法很简单，创建一个消息队列对象 `mQueue` 并将实例变量 `mThread` 指向当前创建 `Looper` 对象的线程。创建好的 `Looper` 对象会通过 `sThreadLocal` 的 `set()` 方法作为副本保存到当前线程本地。

> ThreadLocal 提供了线程本地的实例，与普通变量的区别在于：
> * 每个使用该变量的线程都会初始化一个完全独立的实例副本。
>
> ThreadLocal 变量通常被 `private static` 修饰，当一个线程结束时，它所使用的所有 ThreadLocal 相对的实例副本都可被回收。


在之前的 “正确姿势 1” 中看到，我们在调用 `Looper.prepare()` 方法之后还需要调用 `Looper.loop()` 方法才能处理 Handler 发送的消息。

**Looper.prepare()**

那这个 `Looper.loop()` 到底做了什么呢？先看下其相关的源代码：

```java
public final class Looper {

  public static void loop() {
    final Looper me = myLooper();
    if (me == null) {
        throw new RuntimeException("No Looper; Looper.prepare() wasn't called on this thread.");
    }
    final MessageQueue queue = me.mQueue;
    // ...

    for (;;) {
        Message msg = queue.next(); // might block
        if (msg == null) {
            // No message indicates that the message queue is quitting.
            return;
        }
        // ...

        final long dispatchStart = needStartTime ? SystemClock.uptimeMillis() : 0;
        final long dispatchEnd;
        try {
            msg.target.dispatchMessage(msg);
            dispatchEnd = needEndTime ? SystemClock.uptimeMillis() : 0;
        } finally {
            if (traceTag != 0) {
                Trace.traceEnd(traceTag);
            }
        }
        // ...

        msg.recycleUnchecked();
    }

    public static @Nullable Looper myLooper() {
        return sThreadLocal.get();
    }

    // ...
}
```

由上面这段代码可知，`Looper.loop()` 方法：
1. 首先从当前线程中拿到 `Looper` 对象，
2. 如果该 `Looper` 对象不为 `null` 的话再从该对象中拿到其内部的 `MessageQueue` 对象 (消息队列)，
3. 紧接着就是通过 `for(;;)` 开始一个死循环，不断通过 `queue.next()` 方法从拿到的消息队列对象中获取 `Message` 对象 (消息)。
4. 拿到消息对象后，通过 `msg.target` 拿到发送该消息的 Handler 并调用其 `dispatchMessage(msg)` 方法分发该消息对象。

在 Handler 中分发消息对象时做了什么呢？我们来看下面这段代码：

```java
public class Handler {
    public void dispatchMessage(Message msg) {
        if (msg.callback != null) {
            handleCallback(msg);
        } else {
            if (mCallback != null) {
                if (mCallback.handleMessage(msg)) {
                    return;
                }
            }
            handleMessage(msg);
        }
    }

    private static void handleCallback(Message message) {
        message.callback.run();
    }

    public void handleMessage(Message msg) {}

    // ...
}
```

可以看出，在 `dispatchMessage(Message msg)` 方法中：
* 场景1：如果 `msg.callback` (类型是 Runnable) 不为 `null`，则直接调用其 `run` 方法。
* 场景2：如果 `msg.callback` (类型是 Runnable) 为 `null`，则调用该 Handler 对象的 `handleMessage(Message msg)` 方法。 

综合前面的代码分析可以，上述 "场景1" 会在我们调用 `handler.post(Runnable)` 之后发生，"场景2" 会在我们调用 `handler.sendMessage(Message)` 之后发生。

> Handler 的 `handleMessage(Message msg)` 方法是个空实现，所以如果要处理发送的消息，就必须在创建 Handler 实例时覆写该方法。

> 埋个坑后面再填：`Looper.loop()` 方法的 `queue.next()` 的 `Message msg = queue.next(); // might block` 这行后面有个注释，意思是或可能会阻塞，这是为什么？


### 创建 MessageQueue

前面讲到，在创建 `Looper` 对象的同时会创建 `MessageQueue` 对象，那创建  `MessageQueue` 这个消息队列对象时，源码都做了哪些操作？继续往下看：

```java
public final class MessageQueue {
    // True if the message queue can be quit.
    private final boolean mQuitAllowed;

    Message mMessages;
    private boolean mQuitting;

    // Indicates whether next() is blocked waiting in pollOnce() with a non-zero timeout.
    private boolean mBlocked;

    private native static long nativeInit();

    MessageQueue(boolean quitAllowed) {
        mQuitAllowed = quitAllowed;
        mPtr = nativeInit();
    }

    boolean enqueueMessage(Message msg, long when) {
        if (msg.target == null) {
            throw new IllegalArgumentException("Message must have a target.");
        }
        if (msg.isInUse()) {
            throw new IllegalStateException(msg + " This message is already in use.");
        }

        synchronized (this) {
            if (mQuitting) {
                IllegalStateException e = new IllegalStateException(
                        msg.target + " sending message to a Handler on a dead thread");
                Log.w(TAG, e.getMessage(), e);
                msg.recycle();
                return false;
            }

            msg.markInUse();
            msg.when = when;
            Message p = mMessages;
            boolean needWake;
            if (p == null || when == 0 || when < p.when) {
                // New head, wake up the event queue if blocked.
                msg.next = p;
                mMessages = msg;
                needWake = mBlocked;
            } else {
                // Inserted within the middle of the queue.  Usually we don't have to wake
                // up the event queue unless there is a barrier at the head of the queue
                // and the message is the earliest asynchronous message in the queue.
                needWake = mBlocked && p.target == null && msg.isAsynchronous();
                Message prev;
                for (;;) {
                    prev = p;
                    p = p.next;
                    if (p == null || when < p.when) {
                        break;
                    }
                    if (needWake && p.isAsynchronous()) {
                        needWake = false;
                    }
                }
                msg.next = p; // invariant: p == prev.next
                prev.next = msg;
            }
            // ...
        }
        return true;
    }

    void quit(boolean safe) {
        if (!mQuitAllowed) {
            throw new IllegalStateException("Main thread not allowed to quit.");
        }

        synchronized (this) {
            if (mQuitting) {
                return;
            }
            mQuitting = true;

            if (safe) {
                removeAllFutureMessagesLocked();
            } else {
                removeAllMessagesLocked();
            }

            // We can assume mPtr != 0 because mQuitting was previously false.
            nativeWake(mPtr);
        }
    }

    private void removeAllMessagesLocked() {
        Message p = mMessages;
        while (p != null) {
            Message n = p.next;
            p.recycleUnchecked();
            p = n;
        }
        mMessages = null;
    }

    boolean hasMessages(Handler h, int what, Object object) { /* ... */ }
    boolean hasMessages(Handler h, Runnable r, Object object) { /* ... */ }
    void removeMessages(Handler h, int what, Object object) { /* ... */ }
    void removeMessages(Handler h, Runnable r, Object object) { /* ... */ }
    void removeCallbacksAndMessages(Handler h, Object object) { /* ... */ }

    // ...
}
```

从上面的代码片段可以看出，创建 `MessageQueue` 时只是做了一些普通操作：
* 其构造函数接收一个 "是否可以停止消息队列" 的标识，该表示会作为消息队列的成员变量。
* 在构造函数内部会调用 native 方法进行相关初始化操作。

大概描述下上面代码片段的 `enqueueMessage(Message msg, long when)` 和 `quit(boolean safe)` 方法。

**`enqueueMessage(Message msg, long when)`：**
* 在调用 `enqueueMessage` 方法将消息对象加入队列时，消息对象的 `target`(Handler) 是不能为 `null` 的。换个角度想，没有 Handler 的话这个消息怎么发出来？即使发出来了，没有 Handler 处理也是难搞呀。所以直接抛出异常时再好不过了。哈哈
* 在加锁的代码块中处理消息，这里有三行关键代码 `msg.when = when;`、`Message p = mMessages;` 和 `msg.next = p;`。前面讲过，`next` 指向的是下一个消息对象，每一条消息对象在出队列时都是有严格的 **时间顺序**的，所以在这里需要通过 `if(p == null || when == 0 || when < p.when) { ... } else { for (;;) { ... } }` 这个逻辑进行判断，以确定新加入队列的消息对象是直接放在队列头部位置还是从头部位置往后遍历以放在合适的 (符合 `when` 的) 位置。

代码分析到这里，应该要特意提醒一下：在 `MessageQueue` 中是没有 `List<Message>` 这样的代码结构的，其内部是通过一个 `Message mMessages;` 变量引用了 "消息队列" 中头部第一个位置的消息对象，然后该消息对象会 `next` 引用其上一个消息对象，这样通过 `next` 的链式引用就形成了队列。

顺便也附张图吧，毕竟文字太抽象了：

<img src="./res/004.png" width="640">
 
看到这里你有木有想过，为什么在将消息对象加入到队列中时要加锁？
* 因为我们可能会中多个线程中同时调用同一个 Handler 的 `post(Runnable)` 或 `sendMessage(Message)` 方法呀。


**quit(boolean safe)**：

* 当我们需要想要结束某个消息队列时，可以调用 Looper 实例的 `quit()` 方法来间接调用 `MessageQueue` 的 `quit(boolean safe)` 方法。
* 如果对应的消息队列是不允许结束的话，就会抛出异常。比如说主线程的消息队列就是不可以借宿的，因为主线程在创建消息队列的时候入参 `quitAllowed` 为 `false`。
* 结束时逻辑需要在锁中执行 (总不能在多个线程中同时调用 `quit` 方法时导致逻辑异常吧)，当将 `mQuitting` 置为 `true` 之后，再调用 `enqueueMessage()` 方法的话就不会再将新的消息加入队列中了。
* 最后通过 `mMessages` 引用遍历消息队列以回收所有消息对象，然后再讲 `mMessages` 置为 `null`。


### MessageQueue.next()

还记不记得前面我们埋下了一个说 `queue.next()` 可能会导致线程阻塞的坑？是时候来填了：

```java
public final class MessageQueue {

    // Indicates whether next() is blocked waiting in pollOnce() with a non-zero timeout.
    private boolean mBlocked;

    Message next() {
        // Return here if the message loop has already quit and been disposed.
        // This can happen if the application tries to restart a looper after quit
        // which is not supported.
        final long ptr = mPtr;
        if (ptr == 0) {
            return null;
        }

        int pendingIdleHandlerCount = -1; // -1 only during first iteration
        int nextPollTimeoutMillis = 0;
        for (;;) {
            if (nextPollTimeoutMillis != 0) {
                Binder.flushPendingCommands();
            }

            nativePollOnce(ptr, nextPollTimeoutMillis);

            synchronized (this) {
                // Try to retrieve the next message.  Return if found.
                final long now = SystemClock.uptimeMillis();
                Message prevMsg = null;
                Message msg = mMessages;
                if (msg != null && msg.target == null) {
                    // Stalled by a barrier.  Find the next asynchronous message in the queue.
                    do {
                        prevMsg = msg;
                        msg = msg.next;
                    } while (msg != null && !msg.isAsynchronous());
                }
                if (msg != null) {
                    if (now < msg.when) {
                        // Next message is not ready.  Set a timeout to wake up when it is ready.
                        nextPollTimeoutMillis = (int) Math.min(msg.when - now, Integer.MAX_VALUE);
                    } else {
                        // Got a message.
                        mBlocked = false;
                        if (prevMsg != null) {
                            prevMsg.next = msg.next;
                        } else {
                            mMessages = msg.next;
                        }
                        msg.next = null;
                        if (DEBUG) Log.v(TAG, "Returning message: " + msg);
                        msg.markInUse();
                        return msg;
                    }
                } else {
                    // No more messages.
                    nextPollTimeoutMillis = -1;
                }

                // Process the quit message now that all pending messages have been handled.
                if (mQuitting) {
                    dispose();
                    return null;
                }

                // ...
            }

            // ...
        }
    }

    // ...
}
```

从上面的代码片段可以清晰的看出，消息队列对象的 `next()` 方法里面也有一个 `for(;;)` 死循环。死循环加锁的代码块中，如果队列的第一个 `Message` 对象不为 `null` 的话，会判断其 `when` 属性，看看是否到时间了，如果到时间了就将该消息对象作为返回值返回到 `Looper.loop()` 方法中 (同时会将消息队列的 `mMessages` 指向 "该消息对象的 `next` 指向的对象")，然后再由 Handler 进行分发和处理。 

### 小结

源码分析这部分有点长，我们通过自问自答来小结一下：

**Looper、Handler、消息队列如何捆绑？**

// TBD


**Looper 如何管理 MessageQueue ？**

// TBD

## 总结

在这篇文章中，我们学习了如何使用 Handler，同时也通过源码分析了解了其内部的实现机制。