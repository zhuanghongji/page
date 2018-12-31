# RxJs 基础知识：操作符

在 RxJS 中，操作符扮演着十分重要的角色。操作符作为基础的代码单元，使得我们可以轻松地将复杂的异步代码以声明式的方式进行组合。

> 这样说好像有点抽象，也没说明什么是操作符...

操作符是 `Observable` 上的方法，比如 `.map(...)`、`.filter(...)` 和 `.merge(...)` 等。当操作符被调用时，它 **不会改变** 已有的 Observable 实例，而是返回一个基于已有的 Observable 实例的 **新的** Observable 。

操作符本质上是一个纯函数 (pure function)，它接收一个 Observable 作为输入，并生成一个新的 Observable 作为输出。

> **跑个题：什么是纯函数？**  
> 纯函数的返回结果只依赖于它的参数，并且在执行过程里面没有副作用。
```js
// foo() 不是纯函数
// 它的返回结果依赖外部变量 a
const a  = 1
const foo = (b) => a + b
foo(2)  // 3
.
.
// 返回结果只依赖参数 x 和 y，foo(4, 5) 的值永远是 9
// 满足了纯函数的一个条件
const bar = (x, y) => x + y
bar(4, 5)  // 9
.
.
// baz() 是纯函数
// * 返回结果只依赖参数
// * 执行 baz() 之前和之后，obj.vlaue 的值都是 1，无副作用
const obj = { value: 1 }
const baz = (obj, z) => obj.value + z
baz(2, obj) // 3
obj.value   // 1
```

言归正传，在下面的示例中我们自定义了一个操作符函数，它将从「输入 Observable」输出的每个值都乘以 10 ：

```js
import { Observable, from } from 'rxjs';

function multiplyByTen(input) {
  let output = Observable.create(observer => {
    input.subscribe({
      next: x => observer.next(x * 10),
      error: err => observer.error(err),
      complete: () => observer.complete()
    })
  })
  return output
}

let input = from([1, 2, 3, 4])
let output = multiplyByTen(input)
output.subscribe(x => console.log(x))

// 10
// 20
// 30
// 40
```

注意：订阅 `output` 会导致 `input` 也被订阅，我们称之为「操作符订阅链」。

## 实例操作符 & 静态操作符

操作符可分为两大类：

* [实例操作符](#实例操作符)
* [静态操作符](#静态操作符)

### 实例操作符

通常提到操作符时，我们指的是示例操作符，它是 Observable 实例上的方法。举例来说，如果上面的 `multiplyByTen` 是官方提供的实例操作符的话，那么它看起来大概是酱紫的：

```js
Observable.prototype.multiplyByTen = function multiplyByTen() {
  let input = this;
  return Rx.Observable.create(function subscribe(observer) {
    input.subscribe({
      next: (x) => observer.next(x * 10),
      error: (err) => observer.error(err),
      complete: () => observer.complete()
    });
  });
}
```

要注意的是，这里的 `input` (Observable) 不再是一个函数参数，而是一个 `this` 对象，如果作为实例操作符的话，使用姿势大致是这样的：

```js
let observable = Observable.from([1, 2, 3, 4]).multiplyByTen();
observable.subscribe(x => console.log(x));
```

### 静态操作符

静态操作符是直接附加到 Observable 类上的，它在内部不使用 `this` 关键字，而是完全依赖于它的参数。

> 静态操作符是附加到 Observable 类上的纯函数，通常用来从头开始创建 Observable 。


创建类型的操作符是最常用的静态操作符。它们只接受非 Observable 参数 (比如数字)，然后再创建一个新的 Observable，而不是将一个输入 Observable 进行转换输出。

举个栗子，`create` 函数：

```js
import { Observable } from 'rxjs';

Observable.create((subscriber) => {
  subscriber.next(1)
  subscriber.next(2)
}).subscribe(x => console.log(x))

// 1
// 2
```

## 弹珠图 (Marble diagrams)

许多操作符都是时间相关的，它们可能会以不同的方式进行 `delay (延迟)`、`sample (取样)`、`throttle (节流)` 或 `debonce (去抖动值)` 等。所以说，
仅仅靠文字来解释操作符如何工作还是比较抽象的，结合图表才是王道。

弹珠图是操作符运行方式的视觉表示，其中包含 Observable (一个或多个)、操作符及其参数、输出 Observable 等。

> 在弹珠图中，时间流向右边，描述了在 Observable 执行中值 (弹珠) 是如何发出的。

一个解剖过的弹珠图如下：

![](./res/marble-diagram-anatomy.svg)

## 操作符分类

根据操作符的用途，可做如下分类：

* [创建](#创建操作符)
* [转换](#转换操作符)
* [过滤](#过滤操作符)
* [组合](#组合操作符)
* [多播](#多播操作符)
* [错误处理](#错误处理操作符)
* [工具](#工具操作符)
* [条件和布尔](#条件和布尔操作符)
* [数学和聚合](#数学和聚合操作符)

在后面的文档中，我们会结合弹珠图来详细介绍一些常见操作符的具体用法。


### 创建操作符

|  | 说明
| -- | --
| `create` | 创建一个新的 Observable，当观察者 (Observer) 订阅该 Observable 时，它会执行指定的函数。
| `defer` | 创建一个 Observable，当被订阅的时候，调用 Observable 工厂为每个观察者创建新的 Observable。
| `empty` | 创建一个什么数据都不发出并且立马完成的 Observable。
| `from` | 从一个数组、类数组对象、Promise、迭代器对象或者类 Observable 对象创建一个 Observable 。
| `fromEvent` | 创建一个 Observable，该 Observable 发出来自给定事件对象的指定类型事件。
| `fromEventPattern` | 从一个基于 addHandler/removeHandler 方法的API创建 Observable。
| `fromPromise` | 将 Promise 转化为 Observable。
| `interval` | 创建一个 Observable ，该 Observable 使用指定的 IScheduler ，并以指定时间间隔发出连续的数字。
| `never` | 创建一个不向观察者发出任何项的 Observable 。
| `of` | 创建一个 Observable，它会依次发出由你提供的参数，最后发出完成通知。
| `repeat` | 返回的 Observable 重复由源 Observable 所发出的项的流，最多可以重复 count 次。
| `repeatWhen` | 回的 Observalbe 是源 Observable 的镜像，除了 complete 。
| `range` | 创建一个 Observable ，它发出指定范围内的数字序列。
| `throw` | 创建一个不发送数据给观察者并且立马发出错误通知的 Observable。
| `timer` | 创建一个 Observable，该 Observable 在初始延时（initialDelay）之后开始发送并且在每个时间周期（ period）后发出自增的数字。


### 转换操作符

|  | 说明
| -- | --
| `buffer` | 缓冲源 Observable 的值直到 closingNotifier 发出。
| `bufferCount` | 缓冲源 Observable 的值直到缓冲数量到达设定的 bufferSize.
| `bufferTime` | 在特定时间周期内缓冲源 Observable 的值。
| `bufferToggle` | 缓冲源 Observable 的值，openings 发送的时候开始缓冲，closingSelector 发送的时候结束缓冲。
| `bufferWhen` | 缓冲源 Observable 的值, 使用关闭 Observable 的工厂函数来决定何时关闭、发出和重置缓冲区。
| `concatMap` | 将源值投射为一个合并到输出 Observable 的 Observable,以串行的方式等待前一个完成再合并下一个 Observable。
| `concatMapTo` | 将每个源值投射成同一个 Observable ，该 Observable 会以串行的方式多次合并到输出 Observable 中 。
| `exhaustMap` | 将每个源值投射成 Observable，只有当前一个投射的 Observable 已经完成， 这个 Observable 才会被合并到输出 Observable 中。
| `expand` | 递归地将每个源值投射成 Observable，这个 Observable 会被合并到输出 Observable 中。
| `groupBy` | 根据指定条件将源 Observable 发出的值进行分组，并将这些分组作为 GroupedObservables 发出，每一个分组都是一个 GroupedObservable 。
| `map` | 将给定的 project 函数应用于源 Observable 发出的每个值，并将结果值作为 Observable 发出。
| `mapTo` | 每次源 Observble 发出值时，都在输出 Observable 上发出给定的常量值。
| `mergeMap` | 将每个源值投射成 Observable ，该 Observable 会合并到输出 Observable 中。
| `mergeMapTo` | 将每个源值投射成同一个 Observable ，该 Observable 会多次合并到输出 Observable 中。
| `mergeScan` | 在源 Observable 上应用 accumulator 函数，其中 accumulator 函数本身返回 Observable ，然后每个返回的中间 Observable 会被合并到输出 Observable 中。
| `pairwise` | 将一系列连续的发送成对的组合在一起，并将这些分组作为两个值的数组发出。
| `partition` | 将源 Observable 一分为二，一个是所有满足 predicate 函数的值，另一个是所有 不满足 predicate 的值。
| `pluck` | 将每个源值(对象)映射成它指定的嵌套属性。
| `scan` | 对源 Observable 使用累加器函数， 返回生成的中间值， 可选的初始值。
| `switchMap` | 将每个源值投射成 Observable，该 Observable 会合并到输出 Observable 中， 并且只发出最新投射的 Observable 中的值。
| `switchMapTo` | 将每个源值投射成同一个 Observable ，该 Observable 会使用 switch 多次被打平 到输出 Observable 中。
| `window` | 每当 windowBoundaries 发出项时，将源 Observable 的值分支成嵌套的 Observable 。
| `windowCount` | 将源 Observable 的值分支成多个嵌套的 Observable ，每个嵌套的 Observable 最多发出 windowSize 个值。
| `windowToggle` | 将源 Observable 的值分支成嵌套的 Observable，分支策略是以 openings 发出项为起始，以 closingSelector 发出为结束。
| `windowWhen` | 将源 Observable 的值分支成嵌套的 Observable ，通过使用关闭 Observable 的工厂函数来决定何时开启新的窗口。

### 过滤操作符

|  | 说明
| -- | --
| `debounce` | 只有在另一个 Observable 决定的一段特定时间经过后并且没有发出另一个源值之后，才从源 Observable 中发出一个值。
| `debounceTime` |只有在特定的一段时间经过后并且没有发出另一个源值，才从源 Observable 中发出一个值。
| `distinct` | 返回 Observable，它发出由源 Observable 所发出的所有与之前的项都不相同的项。
| `distinctUntilChanged` | 返回 Observable，它发出源 Observable 发出的所有与前一项不相同的项。
| `distinctUntilKeyChanged` | 返回 Observable，它发出源 Observable 发出的所有与前一项不相同的项，使用通过提供的 key 访问到的属性来检查两个项是否不同。
| `elementAt` | 只发出单个值，这个值位于源 Observable 的发送序列中的指定 index 处。
| `filter` | 通过只发送源 Observable 的中满足指定 predicate 函数的项来进行过滤。
| `first` | 只发出由源 Observable 所发出的值中第一个(或第一个满足条件的值)。
| `ignoreElements` | 忽略源 Observable 所发送的所有项，只传递 complete 或 error 的调用。
| `audit` | 在另一个 Observable 决定的时间段里忽略源数据，然后发出源 Observable 中最新发出的值， 然后重复此过程。
| `auditTime` | duration 毫秒内忽略源值，然后发出源 Observable 的最新值， 并且重复此过程。
| `last` | 返回的 Observable 只发出由源 Observable 发出的最后一个值。
| `sample` | 发出源 Observable 最新发出的值当另一个 notifier Observable发送时。
| `sampleTime` | 在周期时间间隔内发出源 Observable 发出的最新值。
| `single` | 该 Observable 发出源 Observable 所发出的值中匹配指定 predicate 函数的单个项。
| `skip` | 返回一个 Observable， 该 Observable 跳过源 Observable 发出的前N个值(N = count)。
| `skipLast` | 跳过源 Observable 最后发出的的N个值 (N = count)。
| `skipUntil` | 返回一个 Observable，该 Observable 会跳过源 Observable 发出的值直到第二个 Observable 开始发送。
| `skipWhile` | 返回一个 Observable， 该 Observable 会跳过由源 Observable 发出的所有满足指定条件的数据项， 但是一旦出现了不满足条件的项，则发出在此之后的所有项。
| `take` | 只发出源 Observable 最初发出的的N个值 (N = count)。
| `takeLast` | 只发出源 Observable 最后发出的的N个值 (N = count)。
| `takeUntil` | 发出源 Observable 发出的值，直到 notifier Observable 发出值。
| `takeWhile` | 发出在源 Observable 中满足 predicate 函数的每个值，并且一旦出现不满足 predicate 的值就立即完成。
| `throttle` | 从源 Observable 中发出一个值，然后在由另一个 Observable 决定的期间内忽略 随后发出的源值，然后重复此过程。
| `throttleTime` | 从源 Observable 中发出一个值，然后在 duration 毫秒内忽略随后发出的源值， 然后重复此过程。


### 组合操作符

|  | 说明
| -- | --
| `combineAll` | 通过等待外部 Observable 完成然后应用 combineLatest ，将高阶 Observable 转化为一阶 Observable。
| `combineLatest` | 组合多个 Observables 来创建一个 Observable ，该 Observable 的值根据每个输入 Observable 的最新值计算得出的。
| `concat` | 创建一个输出 Observable，它在当前 Observable 之后顺序地发出每个给定的输入 Observable 中的所有值。
| `concatAll` | 通过顺序地连接内部 Observable，将高阶 Observable 转化为一阶 Observable 。
| `exhaust` | 当前一个内部 Observable 还未完成的情况下，通过丢弃内部 Observable 使得 高阶 Observable 转换成一阶 Observable。
| `forkJoin` | ...
| `merge` | 创建一个输出 Observable ，它可以同时发出每个给定的输入 Observable 中值。
| `mergeAll` | 将高阶 Observable 转换成一阶 Observable ，一阶 Observable 会同时发出在内部 Observables 上发出的所有值。
| `race` | 返回 Observable，该 Observable 是源 Observable 和提供的 Observables 的组合中 第一个发出项的 Observable 的镜像。
| `startWith` | 返回的 Observable 会先发出作为参数指定的项，然后再发出由源 Observable 所发出的项。
| `switch` | 通过只订阅最新发出的内部 Observable ，将高阶 Observable 转换成一阶 Observable 。
| `withLatestFrom` | 结合源 Observable 和另外的 Observables 以创建新的 Observable， 该 Observable 的值由每 个 Observable 最新的值计算得出，当且仅当源发出的时候。
| `zip` | 将多个 Observable 组合以创建一个 Observable，该 Observable 的值是由所有输入 Observables 的值按顺序计算而来的。
| `zipAll` | ...



### 多播操作符

|  | 说明
| -- | --
| `multicast` | 返回的 Observable 发出对 ConnectableObservable 发出的项调用一个指定的 selector 函数的结果， ConnectableObservable 可以在潜在的多个流之间共享单个 subscription 。
| `publish` | 返回 ConnectableObservable，它是 Observable 的变种，它会一直等待，直到 connnect 方法被调用才会开始把值发送给那些订阅它的观察者。
| `publishBehavior` | ...
| `publishLast` | ...
| `publishReplay` | ...
| `share` | 返回一个新的 Observable，该 Observable 多播(共享)源 Observable。 至少要有一个订阅者，该 Observable 才会被订阅并发出数据。 当所有的订阅者都取消订阅了，它会取消对源 Observable 的订阅。


### 错误处理操作符

|  | 说明
| -- | --
| `catch` | 捕获 observable 中的错误，可以通过返回一个新的 observable 或者抛出错误对象来处理。
| `retry` | 返回一个 Observable， 该 Observable 是源 Observable 不包含错误异常的镜像。
| `retryWhen` | 返回一个 Observable， 该 Observable 是源 Observable 不包含错误异常的镜像。


### 工具操作符

|  | 说明
| -- | --
| `do` | 为源 Observable 上的每次发送执行副作用，但返回的 Observable 与源 Observable 是相同的。
| `delay` | 通过给定的超时或者直到一个给定的时间来延迟源 Observable 的发送。
| `delayWhen` | 在给定的时间范围内，延迟源 Observable 所有数据项的发送，该时间段由另一个 Observable 的发送决定。
| `dematerialize` | 将 Notification 对象的 Observable 转换成它们所代表的发送。
| `materialize` | 表示源 Observable 中的所有通知，每个通知都会在 Notification 对象中标记为 它们原始的通知类型，并会作为输出 Observable 的 next 通知。
| `observeOn` | 使用指定的调度器来重新发出源 Observable 的所有通知。
| `subscribeOn` | 使用指定的 IScheduler 异步地订阅此 Observable 的观察者。
| `timeInterval` | ...
| `timestamp` | ...
| `timeout` | ...
| `timeoutWith` | ...
| `toArray` | ...
| `toPromise` | 将 Observable 序列转换为符合 ES2015 标准的 Promise 。



### 条件和布尔操作符

|  | 说明
| -- | --
| `defaultIfEmpty` | 如果源 Observable 在完成之前没有发出任何 next 值，则发出给定的值，否则返回 Observable 的镜像。
| `every` | 返回的 Observable 发出是否源 Observable 的每项都满足指定的条件。
| `find` | 只发出源 Observable 所发出的值中第一个满足条件的值。
| `findIndex` | 只发出源 Observable 所发出的值中第一个满足条件的值的索引。
| `isEmpty` | 如果源 Observable 是空的话，它返回一个发出 true 的 Observable，否则发出 false 。



### 数学和聚合操作符

|  | 说明
| -- | --
| `count` | 计算源的发送数量，并当源完成时发出该数值。
| `max` | max 操作符操作的 Observable 发出数字(或可以与提供的函数进行比较的项)并且当源 Observable 完成时它发出单一项：最大值的项。
| `min` | min 操作符操作的 Observable 发出数字(或可以使用提供函数进行比较的项)并且当源 Observable 完成时它发出单一项：最小值的项。
| `reduce` | 在源 Observalbe 上应用 accumulator (累加器) 函数，然后当源 Observable 完成时，返回 累加的结果，可以提供一个可选的 seed 值。