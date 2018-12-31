# RxJava 基础知识：开始 

下面我们在「是什么」「有什么优点」「能做什么」三个方面对 RxJava 进行简要的概述，然后再写一个 Hello World 的小程序的运行下，实例演示下使用效果。最后对编程思想做一个简单描述和总结。

## 概述

### 是什么

RxJava 是 Reactive Extensions 的 Java 实现，是一个在 JVM 上使用可观测的序列来组成异步的、基于事件的程序的库。

> Reactive Extensions for the JVM   
>
> a library for composing asynchronous and event-based programs using observable sequences for the Java VM.


### 有什么优点

用一句话概括的话：
* 简洁，且 **随着程序的逻辑变得越来越复杂，它依旧简洁**。

啰嗦点的话：
* 扩展了观察者模式用于支持「数据」和「事件」序列，添加的操作符让你可以使用声明的方式来组合这些序列，而无需关注底层的实现，如线程、同步、并发等。

> 操作符，比如
> * 过滤 filter
> * 选择 select
> * 变换 transform
> * 结合 combine
> * 组合 compose
> * ...


### 能做什么

服务器不熟悉，Android 的话可以列举下：

* 替换原先的 [EventBus](https://github.com/greenrobot/EventBus) 或 [otto](https://github.com/square/otto).
* 替换 Android 中的 [AsyncTask](https://developer.android.com/reference/android/os/AsyncTask).
* 花几分钟了解一下 [RxAndroid](https://github.com/ReactiveX/RxAndroid) 及其扩展   [RxBinding](https://github.com/JakeWharton/RxBinding) - [RxLifecycle](https://github.com/trello/RxLifecycle) - [RxPermissions](https://github.com/tbruyelle/RxPermissions) ...

## Hello World

### 实例代码

不管学啥，国际惯例都是先来个风骚的 Hello World ...

```java
package com.zhuanghongji.rxjava;

import io.reactivex.Observable;
import io.reactivex.ObservableEmitter;
import io.reactivex.ObservableOnSubscribe;
import io.reactivex.functions.Consumer;

public class Test {

    public static void main(String[] args) {
        Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) throws Exception {
                emitter.onNext("Hello World");
            }
        }).subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                System.out.println(s);
            }
        });
    }
}

// 打印结果：Hello World
```

如果你想通过 Java 8 的 `Lambda` 和 `方法引用` 简化代码，你可以这样写：  

```Java
Observable.create(emitter -> emitter.onNext("Hello World"))
                .subscribe(System.out::println);
```

还想再简单点？那换个操作符：

```Java
Observable.just("Hello World").subscribe(System.out::println);
```


### 基本实现

根据上面的 Hello World 实例代码，可以看出 RxJava 的基本实现有三点：
* 创建被观察者 Observable
* 创建观察者 Observer
* 订阅 subscribe

#### 创建被观察者 Observable

`Observable` 决定什么时候触发事件以及触发怎样的事件。

下面的代码是使用 `create()` 方法来创建 `Observable` 实例：

```java
Observable observable = Observable.create(new ObservableOnSubscribe<String>() {
    @Override
    public void subscribe(ObservableEmitter<String> emitter) throws Exception {
        System.out.println("observable subscribe()");
        emitter.onNext("Hello");
        emitter.onNext("My");
        emitter.onNext("Friend");
        emitter.onComplete();
        //emitter.onError(new RuntimeException("There is something wrong."));
    }
});
```


#### 创建观察者 Observer

`Observer` 决定事件触发的时候将有怎样的行为。

```Java
Observer observer = new Observer<String>() {
    @Override
    public void onSubscribe(Disposable d) {
        System.out.println("observer onSubscribe()");
    }

    @Override
    public void onNext(String s) {
        System.out.println("observer onNext(), " + s);
    }

    @Override
    public void onError(Throwable e) {
        System.out.println("observer onError()");
    }

    @Override
    public void onComplete() {
        System.out.println("observer onComplete()");
    }
};
```


#### 订阅 subscribe

创建了 `Observable` 和 `Observer` 之后，我们还需要通过 `subscribe()` 方法将两者连接起来：

```Java
observable.subscribe(observer);
```

最终运行的打印结果如下：

```java
observer onSubscribe()
observable subscribe()
observer onNext(), Hello
observer onNext(), My
observer onNext(), Friend
observer onComplete()
```

看到这里可能有点懵，我们可以这样来理解这段代码：  

* `Observable` 通过多次调用 `ObservableEmitter` 发射器的 `onNext()` 方法发送出数据，然后调用 `onComplete()` 方法通知我全部发送完数据了。
* `Observer` 通过 `onNext()` 方法接收别人发送过来的数据，通过 `onComplete()` 方法接收别人 “发送数据完毕” 的通知。
* `subscribe()` 方法则是为「发送方」和「接收方」建立联系，指定了 `Observable` 的发送目标为 `Observer`，并开始发送数据。

下面这张图来自官网，描述了 `Observable` 和 `Observer` 之间的联系：

![](./res/observable-create.png)


### 相关源码分析

#### Observable.create()

首先是 `Observable.create()` 方法的相关源码：

```Java
public static <T> Observable<T> create(ObservableOnSubscribe<T> source) {
    ObjectHelper.requireNonNull(source, "source is null");
    return RxJavaPlugins.onAssembly(new ObservableCreate<T>(source));
}
```

```Java
public interface ObservableOnSubscribe<T> {
    void subscribe(@NonNull ObservableEmitter<T> emitter) throws Exception;
}
```

```Java
public interface ObservableEmitter<T> extends Emitter<T> {
    void setDisposable(@Nullable Disposable d);
    void setCancellable(@Nullable Cancellable c);
    boolean isDisposed();
    ObservableEmitter<T> serialize();
    boolean tryOnError(@NonNull Throwable t);
}

public interface Emitter<T> {
    void onNext(@NonNull T value);
    void onError(@NonNull Throwable error);
    void onComplete();
}
```

`create()` 方法接收一个 `ObservableOnSubscribe<T>` 参数，然后返回一个 `Observable<T>` 实例。

> `ObservableOnSubscribe<T>` 是一个带有 `subscribe(@NonNull ObservableEmitter<T> emitter)` 方法的函数式接口，该方法的参数是一个用来发送事件的 `ObservableEmitter<T>` 实例，在 Hello World 示例中，我们就是通过该示例来发送事件到 `Observer` 的。

`Disposable` 类，从字面上理解的意思是「可任意处理的,用完即可丢弃的」,它代表着一个可支配的资源，可用来中断事件的发送。

`Emitter<T>` 中的三个方法：

* `void onNext(@NonNull T value)`：用于发送一个 **正常值**，该值不能为 `null`。
* `void onError(@NonNull Throwable error)`: 用于发送一个 **异常**，该异常也不能为 `null`。
* `void onComplete()`: 用于发送一个 **全部事件发送完毕** 的通知。


RxJava 中规定：

* `onNext()` 用于发送事件，当不再有新的事件发出时需要调用 `onComplete()` 方法通知事件队列发送完毕，当出现异常时需要调用 `onError()` 方法通知事件队列发送异常。
* 在一个正确运行的时间序列中，`onNext()` 方法触发次数不受限制，但 `onComplete()` 和 `onError()` 方法的触发有且只有一次，并且是事件序列中的最后一个。
* `onComplete()` 和 `onError()` 这两个方法是互斥的，也就是说，在队列中调用了两个方法之中的任意一个之后，都不应该再调用另外一个。


#### Observable.subscribe()

然后是 `subscribe()` 方法的相关源码：

```java
public final void subscribe(Observer<? super T> observer) {
    ObjectHelper.requireNonNull(observer, "observer is null");
    try {
        observer = RxJavaPlugins.onSubscribe(this, observer);
        ObjectHelper.requireNonNull(observer, "Plugin returned null Observer");
        subscribeActual(observer);
    } catch (NullPointerException e) { // NOPMD
        throw e;
    } catch (Throwable e) {
        ...
        throw npe;
    }
}
```

`subscribe()` 方法接收一个不为 `null` 的 `Observer<T>` 参数，用于建立 `Observable` 和 `Observer` 两者之间的联系。


#### Observer<T>

最后是 `Observer<T>` 类的相关源码：

```java
public interface Observer<T> {
    void onSubscribe(@NonNull Disposable d);
    void onNext(@NonNull T t);
    void onError(@NonNull Throwable e);
    void onComplete();
}
```

`Observer<T>` 提供了一种机制来接收「基于发送」的通知。它有四个方法，并与 `Observable` 相关的四个方法相对应，下面细说。

##### `onSubscribe()`

* 对应于 `Observable` 的 `subscribe()` 方法。
* 触发时机介于 `Observable.subscribe()` 和 `ObservableOnSubscribe.subscribe()` 之间。
* 为 `Observer` 提供了取消与 `Observable` 之间的联系机制。

##### `onNext()`

* 对应 `Emitter` 的 `onNext` 方法。
* 调用时机在其后。
* 为观察者提供一个要观察的新项目。

##### `onError()`

* 对应 `Emitter` 的 `onError` 方法。
* 调用时机在其后。
* 通知观察者 `Observable`遇到了错误情况。

##### `onComplete()`

* 对应 `Emitter` 的 `onComplete` 方法。
* 调用时机在其后。
* 通知观察者 `Observable` 已完成发送的通知。

## 编程思想

* 一开始学 C 的时候，我们写代码主要是 **面向过程**。
* 后来我们开始学 Java 时，知道了 **面向对象编程**。
* 再后来学习 Spring 的时候，原来还有 **面向切面编程**。

> 皮一下：工作后我们才发现，终极思想其实是面向工资 ... `^_^`


### 函数式编程

函数式编程的主要事项是 **把运算过程尽量写成一系列嵌套的函数调用**。

#### 特性

* **闭包和高阶函数**  
  * 闭包：能够读取其它函数内部变量的函数。
  * 高阶函数：满足 “接受一个或多个函数作为输入” 或 “输出一个函数” 两个条件之一的函数。
* **惰性计算**  
  * 表达式不是在绑定到变量时立即计算，而是在求职程序需要产生表达式的值时再进行计算。
* **递归**  
  * 用递归作为控制流程的机制。

#### 特点

* **函数是一等公民**  
  * 函数与其它数据类型一样，可以赋值给其它变量。
  * 也可以作为参数传入另一个函数。
  * 或者作为其它函数的返回值。
* **只用「表达式」，不用「语句」**
  * 表达式 (expression)：一个单纯的运算过程，总有返回值。
  * 语句 (statement)：执行某种操作，没有返回值。
* **没有「副作用」**
  * 函数要保持独立，所有功能就是返回一个新的值。
  * 没有其它行为，尤其是不得修改外部变量的值。
* **不修改状态**
  * 只是返回新的值，不修改系统变量（变量往往用来保存 “状态”）。
* **引用透明性**
  * 同样的输入，总是返回通用的结果，不依赖可以改变值得全局状态。

#### 优点

* 代码简洁
* 接近自然语言，易于理解
* 更方便代码管理
* 易于并发编程
* 代码的热升级


### 响应式编程

传统的编程方式是顺序执行的，总是需要等待上一个任务完成后才执行下一个任务。如果需要响应迅速，就得把同步执行的方式换成异步执行，方法执行变成消息发送。

响应式编程是一种面向数据流和变化传播的编程范式，可以很方便地表达静态或动态的数据流。

#### 特点

* **异步编程**
  * 能够挖掘多核 CPU 的能力，提高运行效率，降低延迟和阻塞等。
* **数据流**
  * 支持静态和动态的数据流，允许复用和同时接入多个订阅者。
* **变化传播**
  * 以一个数据流为输入，经过一连串操作转化为另一个数据流，然后分发给各个订阅者。


### 函数响应式编程

结合了「函数式」和「响应式」的优点，通过函数的组合来解决问题，可避免回调地狱的问题。