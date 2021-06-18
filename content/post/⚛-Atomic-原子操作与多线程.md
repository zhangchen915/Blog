---
title: "⚛ Atomic 原子操作与多线程"
categories: ["前端","Node.js","后端"]
tags: ["并发编程","Atomic"]
draft: false
slug: "828"
date: "2020-08-30 15:54:00"
---

## 何谓原子操作？
所谓原子操作，就是多线程程序中“最小的且不可并行化的”操作。对于在多个线程间共享的一个资源而言，这意味着同一时刻，多个线程中有且仅有一个线程在对这个资源进行操作，即互斥访问。

## JS 中的 Atomic

Chrome 67+实现了SharedArrayBuffer和Atomics。

共享阵列缓冲区的概念是，您将消息发布给工作线程，但不是复制数组的内容，而是发送对它的引用，以便多个工作线程可以共享内存块。

原子的概念是它们提供可以“一次”发生的操作。

这是为什么原子如此重要的一个示例：执行加法时，您将发出多个操作。

```javascript
a = a + 3;
// ↑ is equivalent to the following:
let reg = a;
reg += 3;
a = reg;
```
在普通的JS中这不是问题，因为您被授予在每个给定时间仅执行一个函数。

引入Workers和共享内存会打破这种假设，因此a在您递增时reg，有人可能会更改的值，最终您会覆盖其值。要解决此问题，可以使用Atomic.add。

Atomic 的 wait() 和 notify() 方法采用的是 Linux 上的 futexes 模型（“快速用户空间互斥量”），可以让进程一直等待直到某个特定的条件为真，主要用于实现阻塞。


### sleep 问题
如果我们想让一段程序休眠一段时间该怎么做？
也许会想到 setTimeout 但是他只会暂停回调函数，不会阻止事件循环的进行，所以其他异步函数还是会执行的。

可以利用类似自旋锁的方式，用一段空转函数占用CPU运行，来实现暂停：
> 自旋锁不会让出CPU，是一种忙等待状态，自旋锁其实就是：死循环等待锁被释放

```js
function sleep(ms) {
  const target = Date.now() + Number(ms);
  while (target > Date.now()) {}
}

```


Atomics.wait 方法，该方法会监听一个Int32Array 对象的给定下标下的值，若值未发生改变，则一直等待(阻塞event loop)，直到发生超时(由ms参数决定定)：
```js
const nil = new Int32Array(new SharedArrayBuffer(4))

function sleep (ms) {

  // 参数校验相关代码略去

  Atomics.wait(nil, 0, 0, Number(ms))
}
```
另外，模块还对低版本的 js 运行时做了兼容，如果不支持 Atomics，则改用一个占用CPU运行的方式：
```js
function sleep(ms) {

  // 参数校验相关代码略去

  const target = Date.now() + Number(ms);
  while (target > Date.now()) {}
}
```

尽管Atomic有很强，但它们从来都不容易使用。此外，原子是非常低级的API，并且要表达高级行为，通常需要使多个Atomic操作相互交互。

## 线程同步

### 互斥体
互斥体是允许只有一个线程可以同时进入关键部分的原语。关键部分是 lock 和 unlock调用之间的代码。

详细说明：

- 互斥锁有2种状态：解锁和锁定。
- 调用lock()互斥锁应将其转换为锁定状态。
- lock()如果该互斥锁已被其他人锁定，则调用该互斥锁应该会阻塞并等待。
- 调用unlock()互斥锁应将其转换为解锁状态。
- 调用unlock()未锁定的互斥锁不是有效操作。

为此，我们需要以下原子方法：

- Atomics.compareExchange
- Atomics.wait
- Atomics.notify


```javascript
const locked = 1;
const unlocked = 0;

class Mutex {
  /**
   * Instantiate Mutex.
   * If opt_sab is provided, the mutex will use it as a backing array.
   * @param {SharedArrayBuffer} opt_sab Optional SharedArrayBuffer.
   */
  constructor(opt_sab) {
    this._sab = opt_sab || new SharedArrayBuffer(4);
    this._mu = new Int32Array(this._sab);
  }

  /**
   * Instantiate a Mutex connected to the given one.
   * @param {Mutex} mu the other Mutex.
   */
  static connect(mu) {
    return new Mutex(mu._sab);
  }

  lock() {
    for(;;) {
      if (Atomics.compareExchange(this._mu, 0, unlocked, locked) == unlocked) {
        return;
      }
      Atomics.wait(this._mu, 0, locked);
    }
  }

  unlock() {
    if (Atomics.compareExchange(this._mu, 0, locked, unlocked) != locked) {
      throw new Error("Mutex is in inconsistent state: unlock on unlocked Mutex.");
    }
    Atomics.notify(this._mu, 0, 1);
  }
}
```
当lock被调用时，它将尝试将互斥锁转换为locked状态并检查是否成功。这是通过来完成的compareExchange，这意味着：“加载值并locked在且仅当为时才与之交换unlocked”。compareExchange返回变量的旧值，可用来检查是否成功获取了互斥量。


### WaitGroup
WaitGroup通常用于等待所有Worker完成其工作，然后读取累积输出。

- 调用add(n)WaitGroup应该通过添加给定值来更改计数器
- 调用doneWaitGroup等效于调用add(-1)
- 调用waitWaitGroup应该暂停执行，直到计数器等于0
- 当心在工作开始之前add应该始终调用它，这就是为什么我在构造函数中添加了初始值。召集呼叫add的工作人员done会导致比赛条件，并可能导致意外的早退wait。

为了实现它，我使用了以下原语：

- Atomics.add
- Atomics.load
- Atomics.wait
- Atomics.notify

这是代码：
```javascript
class WaitGroup {
  constructor(initial, opt_sab) {
    this._sab = opt_sab || new SharedArrayBuffer(4);
    this._wg = new Int32Array(this._sab);
    this.add(initial);
  }

  static connect(wg) {
    return new WaitGroup(0, wg._sab);
  }

  add(n) {
    let current = n + Atomics.add(this._wg, 0, n);
    if (current < 0) {
      throw new Error('WaitGroup is in inconsistent state: negative count.');
    }
    if (current > 0){
      return;
    }
    Atomics.notify(this._wg, 0);
  }

  done() {
    this.add(-1);
  }

  wait() {
    for (;;) {
      let count = Atomics.load(this._wg, 0);
      if (count == 0){
        return;
      }
      if (Atomics.wait(this._wg, 0, count) == 'ok') {
        return;
      }
    }
  }
}
```

首先，add实现将计数器增加给定值。由于add返回了旧值，因此我们将其递增n，然后对其进行推理，就好像它被及时冻结了一样。我们只加载一次计数器，这使该调用保持一致性。

在notify被称为与服务员的默认量，这是无穷大。

我们不检查计数器是否0处于等待唤醒状态的原因是，仅在添加期间计数器达到0的情况下才调用通知。


### 参考文章：
https://zhuanlan.zhihu.com/p/112126898
https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Atomics
https://blogtitle.github.io/using-javascript-sharedarraybuffers-and-atomics/
https://www.cnblogs.com/accordion/p/12533305.html
