# RxJs 基础知识：主体

什么是主体 (Subject) ？

Subject 是一种特殊的 Observable，可以将值多路传播给多个观察者。普通的 Observable 是单路传播的，也就是说，每个已订阅的观察者都拥有 Observable 的独立执行。

> Subject 像是 Observable，但可以多路传播给多个观察者。  
> Subject 也像是 EventEmitter，维护着多个监听器的注册表。

**每个 Subject 都是 Observable** :

* 对于 Subject，提供一个观察者并使用 `subscribe()` 方法就可以正常接收值。
* 从观察者的角度而言，它无法判断 Observable 执行是来自 "普通 Observable" 还是 "Subject"。

**每个 Subject 都是 Observer** :

* Subject 有 `next(v)` `error(e)` 和 `complete()` 方法的对象。
* 要给 Subject 提供新值，只要调用 `next(theValue)` 方法即可，它会将值多路传播给已注册监听该 Subject 的观察者们。

> 在 Subject 的内部，`subscribe()` 方法不会调用发送值的新执行。它只是将给定的观察者注册到观察者列表中，类似其它库或语言中的 `addListener` 工作方式。

下面的示例中，我们为 Subject 提供了两个观察者，然后再给 Subject 提供一些值：

```js
import { Subject } from 'rxjs';

let subject = new Subject()
subject.subscribe(v => console.log(`ObserverA: ${v}`))
subject.subscribe(v => console.log(`ObserverB: ${v}`))
subject.next(1)
subject.next(2)

// ObserverA: 1
// ObserverB: 1
// ObserverA: 2
// ObserverB: 2
```

因为 Subject 也是观察者，这意味着你可以将 Subject 作为参数传给 Observable 的 `subscribe()` 方法，示例如下：

```js
import { Subject, from } from 'rxjs';

let subject = new Subject()
subject.subscribe(v => console.log(`ObserverA: ${v}`))
subject.subscribe(v => console.log(`ObserverB: ${v}`))

let observable = from([1, 2, 3])
observable.subscribe(subject)

// ObserverA: 1
// ObserverB: 1
// ObserverA: 2
// ObserverB: 2
// ObserverA: 3
// ObserverB: 3
```

在上面的示例中，我们基本上只是通过 Subject 将单路传播的 Observable 执行转换为多路传播的。这也说明了，Subject 是将任意 Observable 执行共享给多个观察者的唯一方式。

还有一些特殊类型的 `Subject`：

- [RxJs 基础知识：主体](#rxjs-%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86%E4%B8%BB%E4%BD%93)
  - [BehaviorSubject](#behaviorsubject)
  - [ReplaySubject](#replaysubject)
  - [AsyncSubject](#asyncsubject)


## BehaviorSubject

`BehaviorSubject` 有一个「当前值」的概念，保存了发送给消费者的最新值。当有新的观察者订阅时，会立即从 `BehaviorSubject` 那接收到「当前值」。


```js
import { BehaviorSubject } from 'rxjs';

let subject = new BehaviorSubject(0) // 0 是初始值

subject.subscribe({
  next: x => console.log(`ObserverA: ${x}`)
})
subject.next(1)
subject.next(2)

subject.subscribe({
  next: x => console.log(`ObserverB: ${x}`)
})
subject.next(3)

// ObserverA: 0    --> 注：ObserverA 订阅时会立即接收到初始值 (当前值) 0
// ObserverA: 1
// ObserverA: 2
// ObserverB: 2    --> 注：ObserverB 订阅时会立即接收到当前值
// ObserverA: 3
// ObserverB: 3
```


## ReplaySubject

`ReplaySubject` 有点类似 `BehaviorSubject`，可以发送旧值给新的订阅者。特别的，`ReplaySubject` 可以记录 Observable 执行的一部分值，而不只是当前值。

```js
import { ReplaySubject } from 'rxjs';

let subject = new ReplaySubject(3) // 为新的订阅者缓冲 (回放) 3 个值

subject.subscribe({
  next: x => console.log(`ObserverA: ${x}`)
})
subject.next(1)
subject.next(2)
subject.next(3)
subject.next(4)

subject.subscribe({
  next: x => console.log(`ObserverB: ${x}`)
})
subject.next(5)

// ObserverA: 1
// ObserverA: 2
// ObserverA: 3
// ObserverA: 4
// ObserverB: 2    --> 注：缓冲的值
// ObserverB: 3    --> 注：缓冲的值
// ObserverB: 4    --> 注：缓冲的值
// ObserverA: 5
// ObserverB: 5
```

除了可以设置缓冲数量，`ReplaySubject` 还可以指定 windowTime 来确定多久之后开始记录值，单位为毫秒。

下面的示例中，我们使用了一个较大的缓存数量 100，同时为了效果明显和便于观察，windowTime 设置为 5000 毫秒 (5 秒)。


```js
import { ReplaySubject } from 'rxjs';

let subject = new ReplaySubject(100, 5000 /** windowTime */)

subject.subscribe({
  next: x => console.log(`ObserverA: ${x}`)
})

// 每 2 秒打印出一个数字
let i = 1
setInterval(() => subject.next(i++), 2000)

setTimeout(() => {
  subject.subscribe({
    next: x => console.log(`ObserverB: ${x}`)
  })
}, 10000)

// ObserverA: 1
// ObserverA: 2
// ObserverA: 3   --> 超过了 5 秒，会作为缓冲值
// ObserverA: 4   --> 超过了 5 秒，会作为缓冲值
// ObserverA: 5   --> 超过了 5 秒，会作为缓冲值
// ObserverB: 3   --> 缓冲的值
// ObserverB: 4   --> 缓冲的值
// ObserverB: 5   --> 缓冲的值
// ObserverA: 6   --> 新值 6
// ObserverB: 6   --> 新值 6
// ObserverA: 7   --> 新值 7
// ObserverB: 7   --> 新值 7
// ObserverA: 8   --> 新值 8
// ObserverB: 8   --> 新值 8
// ...
```

## AsyncSubject

`AsyncSubject` 也是一种特殊类型的 `Subject`，它的作用在于：只有当 Observable 执行完成 (complete) 时才会将最后一个值发送给所有的观察者。

```js
import { AsyncSubject } from 'rxjs';

let subject = new AsyncSubject()

subject.subscribe({
  next: x => console.log(`ObserverA: ${x}`)
})

subject.next(1);
subject.next(2);
subject.next(3);
subject.next(4);

subject.subscribe({
  next: x => console.log(`ObserverB: ${x}`)
});

subject.next(5);
subject.complete();

// ObserverA: 5
// ObserverB: 5
```

`AsyncSubject` 和 `last()` 操作符类似，因为它也是等待 `complete` 通知，再发送单个值。
