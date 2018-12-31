# RxJs 基础知识：调度器

## 什么是调度器

调度器控制着何时启动 subscription 和何时发送通知：

* **调度器是一种数据结构。**  
  它知道如何根据优先级或其它标准来存储任务和排序任务。

* **调度器是执行上下文。**  
  它表示在何时何地执行任务。

* **调度器有一个虚拟时钟。**  
  调度器功能通过它的 getter 方法 `now()` 提供了「时间」的概念，在具体调度器上安排的任务将严格遵循该时钟所表示的时间。

> 调度器可以让你规定 Observble 在什么样的执行上下文中发送通知给它的观察者。

在下面示例中，我们采用普通的 Observble 同步地发出值 `1` `2` `3` 并使用操作符 `observeOn` 来指定 `async` 调度器发送这些值。

```js
import { range, asyncScheduler } from 'rxjs'
import { observeOn } from 'rxjs/operators'

let observale = range(0, 3).pipe(
  observeOn(asyncScheduler)
)
console.log('before subscribe')
observale.subscribe(x => console.log(x))
console.log('after subscribe')

// before subscribe
// after subscribe
// 0
// 1
// 2
```

可以注意到，数字的输出是在 `after subscribe` 输出之后的，这是因为 `observeOn(asyncScheduler)` 在 `range( ... )` 和最终的观察者中间引入了一个代理观察者，使得事件的发送是在下一次事件循环迭代的最开始才执行。

## 调度器类型

`asyncScheduler` 调度器是 RxJS 提供的内置调度器中的一个，包括其它的调度器列举如下：

| 调度器 | 目的
| -- | --
| - | 不设置任何调度器的话，会以同步递归的方式发送通知，可用于定时操作或尾归操作。
| asapScheduler | 微任务的队列调度，它使用可用的最快速的传输机制，比如 Node.js 的 process.nextTick() 或 Web Worker 的 MessageChannel 或 setTimeout 或其它，用于异步转换。
| asyncScheduler | 使用 `setIntercal` 的调度，用于基于时间的操作符。
| queueScheduler | 当前事件帧中的队列调度 (蹦床调度器)，用于迭代操作。


## 使用调度器

你可能已经在你的 RxJS 代码中使用过调度器了，只是没有明确地指明要使用的调度器的类型而已。这是因为所有的 Observble 操作符处理并发性都有可选的调度器，如果没有提供调度器的话，RxJS 会通过使用「最小并发原则」选择一个默认调度器。也就是说，会选择满足操作符的需要的最小并发量的调度器。例如：

* 对于返回有限和少量消息的 observale 的操作符，RxJS 不使用调度器，即 `null` 或 `undefined`。
* 对于返回潜在大量的或无限数量的消息的操作符，使用 `queue` 调度器。
* 对于使用定时器的操作符，使用 `async` 调度器。

因为 RxJS 使用最少的并发调度器，如果你出于性能考虑要引入并发，可以使用采用调度器的操作符的指定调度器，比如 `from([40, 50, 60], asyncScheduler)`。下面我们分别指定前面提到过的三种调度器来测试一下：

asapScheduler :

```js
let observable1 = from([10, 20, 30], asapScheduler)
console.log('before subscribe')
observable1.subscribe(x => console.log(x))
console.log('after subscribe')

// before subscribe
// after subscribe
// 10
// 20
// 30
```

asyncScheduler :

```js
let observable2 = from([40, 50, 60], asyncScheduler)
console.log('before subscribe')
observable2.subscribe(x => console.log(x))
console.log('after subscribe')

// before subscribe
// after subscribe
// 40
// 50
// 60
```

queueScheduler :

```js
let observable3 = from([70, 80, 90], queueScheduler)
console.log('before subscribe')
observable3.subscribe(x => console.log(x))
console.log('after subscribe')

// before subscribe
// 70
// 80
// 90
// after subscribe
```


> 注：没找到相关官方文档，内容有待纠正和补充。