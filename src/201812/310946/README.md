# RxJs 基础知识：可观察者对象、观察者和订阅

## 可观察对象 (Observable)

Observable 是多个值的惰性推送集合，填补了下面表格中的空白：

| | 单个值 | 多个值
| -- | -- | --
| 拉取 | [Function](https://developer.mozilla.org/en-US/docs/Glossary/Function) | [Iterator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)
| 推送 | [Promise](https://developer.mozilla.org/en-US/docs/Mozilla/JavaScript_code_modules/Promise.jsm/Promise) | Observable

下面的代码示例中，Observable 会同步推送值 `1`、`2`、`3`，然后等待 1 秒后再推送值 `4` ，并推送「完成」通知：

```js
const observable = Observable.create(observer => {
  observer.next(1)
  observer.next(2)
  observer.next(3)
  setTimeout(() => {
    observer.next(4)
    observer.complete()
    // observer.error('there is a custom worng.')
  }, 1000)
})
```

若想获取到这些值，需要对 Observable 进行订阅：

```js
observable.subscribe({
  next: x => console.log(`got value ${x}`),
  complete: () => console.log('done'),
  error: err => console.log(`something wrong: ${err}`)
})
```

控制台输出如下：

```js
before subscribe
got value 1
got value 2
got value 3
after subscribe
got value 4
done
// something wrong: there is a custom worng.
```

### 拉取 vs 推送 (Pull vs Push)

「拉取」和「推送」是用于描述 **数据生产者 (Producer)** 和 **数据消费者 (Consumer)** 进行通信的两种不同概念。

#### 什么是拉取？

在拉取体系中，是由消费者来决定何时从生产者那里获取数据，生产者本身不知道数据是何时交付到消费者手中的。

在 JavaScript 中，每个函数都可以看做是拉取体系。函数相当于数据生产者，调用该函数的代码相当于消费者。消费者从函数调用中取出 **单个** 返回值来对该函数进行消费。

在 ES2015 中引入了 [Generator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function*) 函数，这是另一种类型的拉取体系。调用 `iterator.next()` 的代码是消费者，它会从 `iterator` (生产者) 那里获取 **多个** 返回值。

#### 什么是推送？

在推送体系中，由生产者来决定何时把数据发送给消费者，消费者本身不知道何时会接收到数据。

在 JavaScript 中，Promise 是最常见的推送体系。Promise (生产者) 将一个解析过的值传递给已注册的回调函数 (消费者)，不同于函数的是，这是由 Promise 来决定何时把值推送给消费者。

#### 对比

| | 生产者 | 消费者
| -- | -- | --
| 拉取 | 被动的：当被请求时产生数据 | 主动的：决定何时请求数据
| 推送 | 主动的：按自己的节奏产生数据 | 被动的：对接收到的数据做出相应

RxJS 引入了一个新的 JavaScript 推送体系 Observables，Observable 是多个值的生产者，并将值推送给消费者 (Observer)。

* **Function**：惰性评估运算，调用时会同步地返回一个单一值。
* **Generator**：惰性评估运算，调用时可同步地返回零到无限多个值。
* **Promise**：是最终「可能」或「可能不」返回单个值的运算。
* **Observer**：惰性评估运算，调用时可同步或异步地返回零到无限多个值。


### Observables 作为函数的泛化

Observables 既不像 EventEmitter 也不像多个值的 Promise。在某些情况下，当使用 RxJS 的 Subject 进行多路传播时行为比较像 EventEmitter，当通常情况下并不像。

> Observables 像是没有参数，但可以泛化为多个值的函数。

示例：

```js
function foo() {
  console.log('Hello')
  return 42
}

let x = foo.call()  // 等同于 foo()
console.log(x)

let y = foo.call()  // 等同于 foo()
console.log(y)

// Hello
// 42
// Hello
// 42
```

用 Observables 重写上面代码：

```js
import { Observable } from 'rxjs';

let foo = Observable.create(observer => {
  console.log('Hello')
  observer.next(42)
})
foo.subscribe(x => console.log(x))
foo.subscribe(y => console.log(y))

// 打印结果同上
```

打印结果是一样的，因为函数和 Observables 都是惰性运算：

* 如果你不调用函数，对应的 `console.log('Hello')` 就不会执行。
* 如果你不调用 `subscribe()` 进行订阅，对应的 `const.log('Hello')` 也不会执行。

此外，"调用" 和 "订阅" 是独立的操作：

* 两个函数的调用会触发两个单独的副作用。
* 两个 Observable 订阅同样会触发两个单独的副作用。

而 EventEmitter 共享副作用并且无论是否存在订阅者都会尽早执行，Observables 与之相反，不会共享副作用且是延迟执行。


> Observables 的值传递可以是同步地，也可以是异步的。

举个例子：

```js
import { Observable } from 'rxjs';

console.log('before')

Observable.create(observer => {
  observer.next('Hello World')
}).subscribe(str => console.log(str))

console.log('afrer')

// before
// Hello World
// afrer
```

由输出日志来看，上述的订阅操作完全是同步的。

既然如此，Observables 和函数又有什么区别呢？

**Observable 可以随着时间的推移返回多个值，这是函数所做不到的。**    

```js
function foo() {
  console.log('Hello');
  return 1;
  return 2; // 死代码，永远不会执行
}
foo()
```

```js
import { Observable } from 'rxjs';

// 同步返回多个值
Observable.create(observer => {
  observer.next('Hello')
  observer.next('1')
  observer.next('2')
}).subscribe(str => console.log(str))

// 异步返回多个值
Observable.create(observer => {
  observer.next('Hello')
  observer.next('1')
  setTimeout(() => {
    observer.next('2')
  }, 1000)
}).subscribe(str => console.log(str))
```


### Observable 剖析

Observables 可通过 `Observable.create()` 或其它创建操作符 (`of`、`from`、`interval` 等) 进行创建，然后可使用 Observer 进行订阅，执行时它会发送 `next` / `complete` / `error` 通知给对应的 Observer。当然，执行时发送也可能会被取消掉。

Observable 的四个核心在于：

* 创建
* 订阅
* 执行
* 取消

#### 创建 Observables

`Observable.create()` 时 `Observable` 构造函数的别名，它接收一个 `subscribe` 函数参数。

下面的示例创建的 Observables 每隔一秒会向观察者发送自已字符串 `'hi'`：

```js
import { Observable } from 'rxjs';

let observable = Observable.create(observer => {
  let id = setInterval(() => {
    observer.next('hi')
  }, 1000)
})
```

#### 订阅 Observables

可对上述示例中创建的 `observable` 进行订阅：

```js
observable.subscribe(str => console.log(str))
```

`observable.subscribe` 和 `Observable.create(function subscribe(observer) { ... })` 中的 `subscribe` 名字是相同的，但在 RxJS 库中是不同的。从实际出发，我们可以在概念上认为是等同的。

这表明 `subscribe` 的调用在同一 Observable 的多个观察者之间是不共享的。
* 当同一个观察者调用 `observable.subscribe` 时，`Observable.create(function subscribe(observer) { ... })` 中的 `subscribe` 函数只服务于给定的观察者。
* 对 `observable.subscribe` 的每次调用都会触发对应观察者的独立设置。

订阅 Observable 像是调用函数，并提供接收数据的回调函数：
* 这与 `addEventListener` / `removeEventListener` 这样的处理事件方法 API 是完全不同的。
* 使用 `observable.subscribe` 时，在 Observable 中不会将给定的观察者注册为监听器，Observable 也不会去维护一个附加的观察者列表。
* `subscribe` 调用时启动「Observable 执行」的一种简单方式，并将值或事件传递给本次执行的观察者。


#### 执行 Observables

`Observable.create(function subscribe(observer) {...})` 中 `...` 的代码表示「Observable 执行」，它是惰性运算，只有在每个观察者订阅后才会执行。随着时间的推移，执行可能会以同步或异步的方式产生多个值。

Observable 执行时可以值传递的三种类型：

* Next 通知：发送一个值，比如数字、字符串、对象等。
* Error 通知：发送一个 JavaScript 错误或异常。
* Complete 通知：发送一个完成通知，然后不再发送任何值。

Next 通知是最常见的类型，它表示传递给观察者的实际数据，而 Error 和 Complete 通知只会在 Observable 执行期间发送一次，并且只会执行其中的一个。

> 在 Observable 执行期间, 可能会发送零到无穷多个 "Next" 通知。如果发送了 "Error" 或 "Complete" 通知，之后就不会再发送任何通知了。

示例如下：

```js
Observable.create(observer => {
  try {
    observer.next(1);
    observer.next(2);
    observer.next(3);
    observer.complete();
    // 后面的代码不会执行
    observer.next(4);
    throw new Error('there is something wrong')
  } catch (err) {
    observer.error(err);
  }
}).subscribe(num => console.log(num))

// 1
// 2
// 3
```


#### 取消 Observable

Observable 的执行可能是无限的，观察者通常希望能在有限的事件内终止执行，所以需要一个 API 来取消执行。因为每个执行都是其对应的观察者专属的，一旦观察者完成接手值，它必须要一种方法来停止执行，以免浪费计算或资源。

调用了 `observable.subscribe` 后，观察者会附加到新创建的 Observable 执行中。这个调用会返回一个对象，即 `Subscription`。

通过 `Subscription` 的 `unsubscribe()` 方法可以取消执行。示例如下:

```js
import { Observable } from 'rxjs';

let observable = Observable.create(observer => {
  setInterval(() => {
    console.log('interval is running')
    observer.next('hi')
  }, 1000)
})

let subscription = observable.subscribe(x => console.log(x))
setTimeout(() => subscription.unsubscribe(), 3600)

// interval is running
// hi
// interval is running
// hi
// interval is running
// hi
// interval is running
// interval is running
// interval is running
// ...
```

订阅是取消了，好像 `setInterval` 资源还没取消...

优化下构建 Observables 时的代码，如下：

```js
let observable = Observable.create(observer => {
  // interval 资源 id
  let intervalId = setInterval(() => {
    console.log('interval is running')
    observer.next('hi')
  }, 1000)

  // 提供取消和清理 interval 资源的方法
  return function unsubscribe() {
    clearInterval(intervalId)
  }
})

// interval is running
// hi
// interval is running
// hi
// interval is running
// hi
```

## 观察者

什么是观察者？

观察者是 Observable 发送的值的消费者，是一组回调函数的集合。每个回调函数对应一种 Observable 发送的通知的类型：

* `next`
* `error`
* `complete`

下面的示例是一个典型的观察者对象：

```js
let observer = {
  next: x => console.log(`Observer got a next value: ${x}`),
  error: err => console.error(`Observer got an error: ${err}`),
  complete: () => console.log('Observer got a complete notification'),
}
```

要使用观察者，需要把它提供给 Observable 的 `subscribe` 方法：

```js
observable.subscribe(observer)
```

> 可以将观察者视为有三个对应于 Observable 通知类型的回调函数的对象。

在 RxJS 中，观察者的回调函数是可缺省的。也就是说，如果你没有提供某个回调函数，Observable 也是会正常执行相关操作的，只是缺省回调函数的通知类型会被忽略而已。

下面的示例是没有 `complete` 回调函数的观察者：

```js
let observer = {
  next: x => console.log(`Observer got a next value: ${x}`),
  error: err => console.error(`Observer got an error: ${err}`),
}
```

当订阅 Observable 时，你可能没有将其附加到观察者对象上，而是仅仅提供一个回调函数作为参数，比如：

```js
observable.subscribe(
  x => console.log(`Observer got a next value: ${x}`)
)
```

在这种情况下，`observable.subscribe` 的内部会创建一个观察者对象并使用第一个回调函数参数作为 `next` 的处理方法。当然，三种类型的回调函数都可以直接作为参数来提供：

```js
observable.subscribe(
  x => console.log(`Observer got a next value: ${x}`),
  err => console.error(`Observer got an error: ${err}`),
  () => console.log('Observer got a complete notification'),
)
```


## 订阅

什么是订阅 (Subscription) ?

Subscription 是表示可清理资源的对象，它有一个不需要任何参数的 `unsubscribe` 方法，用来清理由 Subscription 占用的资源。

举个例子：

```js
import { interval } from 'rxjs';

let observable = interval(1000)
let subscription = observable.subscribe(x => console.log(x))
// 间隔 1 秒，分别在控制台输出 0，1，2，3，....

// 8 秒后取消 observable 的执行
setTimeout(() => {
  subscription.unsubscribe()
}, 8000)
```

Subscription 是可以结合在一起的，这样当其中一个 Subscription 调用 `unsubscribe()` 方法时，可能会有多个 Subscription 取消订阅。示例如下：

```js
import { interval } from 'rxjs';

let observable = interval(1000)
let subscription = observable.subscribe(x => console.log(`A: ${x}`))

let childObservable = interval(1000)
let childSubscription = childObservable.subscribe(x => console.log(`B: ${x}`))

subscription.add(childSubscription)

setTimeout(() => {
  subscription.unsubscribe()
}, 3000)

// A: 0
// B: 0
// A: 1
// B：1
// A: 2
// B: 2
```

Subscription 还有一个 `remove(otherSubscription)` 方法，用来撤销一个已添加的子 Subscription 。


