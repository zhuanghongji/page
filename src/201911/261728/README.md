# Dart 异步编程：futures, async, await

## 概述

尽管 Dart 本质上是单线程模型 (single-threaded) 开发语言，但它也提供了 futures、streams 和其它一些东西来方便我们编写异步代码。

本文主要是讲其中的 futures 部分：`Future`、`async`、`await`。


### 简单的异步代码示例

**异步神功第一层：通过函数参数进行实现**

```dart
import 'dart:async';

void getName(void callback(String name)) {
  // 通过 Timer 模拟异步操作
  Timer(Duration(seconds: 2), () {
    callback('zhuanghongji');
  });
}

void main() {
  print('before getName..');
  getName((name) => print('hello, $name'));
  print('after getName..');
}
```

执行后，日志如下：

```dart
before getName..
after getName..
hello, zhuanghongji
```

可以看到 `hello, zhuanghongji` 是在 `after getName..` 之后才打印出来的。

**异步神功第二层：通过 `Future` 进行实现**

如果仅仅是从上面 "第一层" 的示例来看，通过函数参数编写异步代码好像不太复杂。 但是，如果是异步中嵌套着异步的话，你就知道什么叫回调地狱了！来，一起感受一下：

```dart
void getName(void callback(String name)) { ... }
void getAgeByName(String name, callback(String age)) { ... }
void getSomethingByAge(String age, void callback(String something)) { ... }

void main() {
  print('before getName..');
  // hell-start
  getName((name) => getAgeByName(
      name,
      (age) =>
          getSomethingByAge(age, (something) => print('hello, $something'))));
  // hell-end
  print('after getName..');
}
```

额..这嵌套... 别怂，拿起 `Future` 就是干！

```dart
import 'dart:async';

// 通过 Future.value 来模拟异步操作
Future<String> getName() => Future.value('zhuanghongji');
Future<String> getAgeByName(String name) => Future.value('18');
Future<String> getSomethingByAge(String age) => Future.value('pandora');

void main() {
  print('before getName..');
  // no-callback-hell
  getName()
      .then((name) => getAgeByName(name))
      .then((age) => getSomethingByAge(age))
      .then((something) => print('hello, $something'));
  // no-callback-hell
  print('after getName..');
}
```

执行后日志输出如下：

```dart
before getName..
after getName..
hello, pandora
```

哎哟，不错噢！逻辑看起来清晰了很多，但是感觉 `then` 和 `=>` 有点多，感觉就像是毫无作用的模板代码，有木有更好一点的写法？当然有，接下来我们进入下一层神功的探索。

**异步神功第二层： `async` 和 `await` 来减少模板代码**

代码秘籍直接献上：

```dart
import 'dart:async';

// 通过 Future.value 来模拟异步操作（与第二层神功代码相同）
Future<String> getName() => Future.value('zhuanghongji');
Future<String> getAgeByName(String name) => Future.value('18');
Future<String> getSomethingByAge(String age) => Future.value('pandora');

void main() async {
  print('before getName..');
  // example-start
  try {
    var name = await getName();
    var age = await getAgeByName(name);
    var something = await getSomethingByAge(age);
    print('hello, $something');
  } catch (e) {
    // ...
  }
  // example-end
  print('after getName..');
}
```

执行后日志如下：

```
before getName..
hello, pandora
after getName..
```

* 我们给 `main` 函数加上 `async` 关键字，表示这是一个异步函数。
* 在其函数体中，我们在调用每一个返回值为 `Future<T>` 的函数时都使用了 `await` 关键字修饰，表示我们要等待这个在将来 (future) 的操作完成，才执行后续逻辑。

这样一来，代码看起来就像是同步执行一样。实际上，单单从 `main` 函数中的逻辑来看确实也是同步执行的，但准确来说应该是按顺序执行的。因为在 `await` 过程中，会转而执行 `main` 之外的逻辑先。不信？您再瞅瞅上面输出日志中 `hello, pandora` 的位置！手动狗头.. 

如果你没写过 Dart 异步代码，可能看到这还是有点懵。别担心，下面我们从从零开始，对 Dart 异步做进一步的了解和探索。


## Future

官方文档是这样描述 `Future` 的：An object representing a delayed computation.

`Future` 用于表示一个潜在的 "值" 或 "错误"，它在未来的某个时刻才可用。既然未来才可用，那是不是需要设置接受者来接收这个未来可用的 "值" 或 "错误" ？是的，`Future` 允许可以注册回调来接收这个潜在变化，比如：

```dart
Future<String> futureSuccess =
      Future.delayed(Duration(seconds: 1), () => "Hello");
  futureSuccess
      .then((value) => print(value))
      .catchError((error) => print(error));       // Hello

Future<String> futureFail =
    Future.delayed(Duration(seconds: 1), () => throw ("Error"));
futureFail
    .then((value) => print(value))
    .catchError((error) => print(error));         // Error
```

在上述代码中，我们通过 `Future` 的实例方法 `then` 来注册接收 "值" 的回调，通过另外一个实例方法 `catchError` 来注册接收 "错误" 的回调。


### then 和 catchError 方法原型

这两个方法的原型如下：

```dart
abstract class Future<T> {
  ...

  /// Register callbacks to be called when this future completes.
  Future<R> then<R>(FutureOr<R> onValue(T value), {Function onError});

  /// Handles errors emitted by this [Future].
  Future<T> catchError(Function onError, {bool test(Object error)});
}
```

**then**

仔细看，发现 `then` 方法有个 `onError` 命名参数，这个是怎么用的呢？看下面两个代码片段：

```dart
// 1
futureFail.then((value) => print(value), onError: (error) {
    print('onError: $error');                                 // onError: Error
}).catchError((error) => print('catchError: $error'));        // ... will not be excuted ...

// 2
futureFail.then((value) => print(value), onError: (error) {
    print('onError: $error');                                 // onError: Error
    throw 'The error throw by onError function.';
}).catchError((error) => print('catchError: $error'));        // catchError: The error throw by onError function.
```

从运行结果分析，我们可以得出两个结论：
* `then` 方法的 `onError` 函数参数也可以接收 "错误"，而且会 "消耗" 掉这个 "错误"，不再往后传。
* 如果 `then` 方法的 `onError` 函数参数内部抛出异常，则这个异常可以被后面的 `catchError` 接收到。


**catchError** 

在仔细看，发现 `catchError` 也有一个类型是函数的 `test` 命名参数，那这个又是干啥用的呢？我们再通过两个代码片段来对比下：

```dart

```



## async, await

...



## 总结

...



推荐阅读：
* [Future<T> class](https://api.flutter.dev/flutter/dart-async/Future-class.html)
* [Dart asynchronous programming: Isolates and event loops](https://medium.com/dartlang/dart-asynchronous-programming-isolates-and-event-loops-bffc3e296a6a)