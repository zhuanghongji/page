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

* 我们给 `main` 函数加上 `async` 关键字，表示这是一个异步函数。
* 在其函数体中，我们在调用每一个返回值为 `Future<T>` 的函数时都使用了 `await` 关键字修饰，表示我们要等待这个在将来 (future) 的操作完成，才执行后续逻辑。

这样一来，代码看起来就像是同步执行一样。实际上，单单从 `main` 函数中的逻辑来看确实也是同步执行的，但准确来说应该是按顺序执行的。因为在 `await` 过程中，会转而执行 `main` 之外的逻辑先。


如果你没写过 Dart 异步代码，可能看到这还是有点懵。别担心，下面我们从从零开始，对 Dart 异步做进一步的了解和探索。


## Future

...




## async, await

...



## 总结

...



推荐阅读：
* [Future<T> class](https://api.flutter.dev/flutter/dart-async/Future-class.html)
* [Dart asynchronous programming: Isolates and event loops](https://medium.com/dartlang/dart-asynchronous-programming-isolates-and-event-loops-bffc3e296a6a)