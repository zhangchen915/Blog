---
title: "Js 中的 CSP（Communicating Sequential Processes）"
categories: ["默认分类"]
tags: ["CSP"]
draft: false
slug: "637"
date: "2019-09-01 04:38:00"
---

![dd839ff45691d9737ce18a35a1f27f5c.jpg][1]
## *什么是 CSP（Communicating Sequential Processes）？*

CSP是由托尼霍尔在1978年发表的一种形式语言规范，中文翻译为通信顺序进程，用于描述并发系统中的通信模式。Go语言原生支持，Clojure有core.async。



## 并发的系统的实现

为了设计并发系统，并发进程/任务/作业/应用程序必须能够进行通信。广泛采用的方法是使用公共存储（数据源）。但是，如果数据源以不需要的顺序写入和读取，则问题是竞争条件和不可靠性。

- 公共存储
  - Threads
  - Locks
  - 互斥 Mutexes
- 消息传递 (CSP and Actor Model)
  - Processes
  - Messages
  - No shared data

## 并发不是并行
例程A将消息放入某个通道。然后停止，直到例程B准备好接收该消息。在服用它之后，B运行直到它接下来*接受*指令并且退后，因此常规A可以再次运行并且它可以无休止地运行。

**没有并行性的并发性：不是操作系统的线程，而是将一个操作系统线程分段使用。**

![csp_illustration2.png][2]

下面使用js-csp中的一个例子，两个process之间打乒乓球：
```js
import {
 go,
 chan,
 take,
 put,
} from 'js-csp'
const ping = chan()
const pong = chan()
go(function * () {
 yield take(ping)
 console.log('ping')
 yield put(pong, true)
})
go(function * () {
 yield take(pong)
 console.log('pong')
 yield put(ping, true)
})
put(ping, true)
// > 'ping'
// > 'pong'
// > 'ping'
// > 'pong'
// > 'ping'
// > 'pong'
...
```


## CSP与 Actor 模型

Actor模型，又叫参与者模型，其”一切皆参与者(actor)”的理念与面向对象编程的“一切皆是对象”类似，但是面向对象编程中对象的交互通常是顺序执行的(占用的是调用方的时间片，是否并发由调用方决定)，而Actor模型中actor的交互是并行执行的(不占用调用方的时间片，是否并发由自己决定)。

从actor自身来说，它的行为模式可简化为:

- 发送消息给其它的actor
- 接收并处理消息，更新自己的状态
- 创建其它的actor

### 区别

- Actor中实体和通信介质是紧耦合的，一个Actor持有一个Mailbox，而CSP中process和channel是解耦的，没有从属关系。从这一层来说，CSP更加灵活

- Actor模型中actor是主体，mailbox是匿名的，CSP模型中channel是主体，process是匿名的。从这一层来说，由于Actor不关心通信介质，底层通信对应用层是透明的。因此在分布式和容错方面更有优势。


## 关键概念与实现
- 顺序处理（processes）
- 通过通道（channel）进行同步通信
- 通道的多路复用与交替

channel 是一个队列，Processes 通过channel进行通信，Processes 可以暂停来等待 channel 中的值。
processer 是一个`Pull`模型的迭代器，它不断从 channel 取出最新的值，如果没有值就暂停执行。

### Processes 
```js
async function process (inChannel, outChannel) {
  while (true) {
    const msg = await inChannel.take();
    // do stuff with msg
    await outChannel.put(res);
  }
};
```
我们使用`[Symbol.asyncIterator]`让`channer`成为可迭代对象，然后使用`for-await-of`语法会更加简洁，同时通过这个`while(true)`不难理解 process 一直从channer拉取数据：
```ts
public async *[Symbol.asyncIterator](): AsyncIterableIterator<T> {
    while (true) {
        yield await this.take();
    }
}
```

```js
async function process (inChannel, outChannel) {
  for await(const msg of inChannel) {
    // do stuff with msg
    await outChannel.put(res);
  }
};
```

### Channel
在Channel中实现了messages putters takers racers 这几个队列。
put 操作返回一个Promise，在该Promise中 首先将传入的参数放到messages，然后将resolve函数放到putters，然后判断takers队列是否有值。

- 如果已经有taker 或racers等待消息，则返回的承诺将立即得到解决
- 如果taker 和racers都在等待消息，则优先权将给予将检索该消息的接受者

可以对channel队列进行操作，例如过滤，遍历等等，多个channel之间还可以广播，或者合并。



!!!
<iframe src="//www.slideshare.net/slideshow/embed_code/key/eM3Mo66CiN9SmV" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/XebiaFrance/xebicon17-communicating-sequential-processes-diana-ortega" title="XebiCon&#x27;17 : Communicating sequential processes - Diana Ortega" target="_blank">XebiCon&#x27;17 : Communicating sequential processes - Diana Ortega</a> </strong> from <strong><a href="https://www.slideshare.net/XebiaFrance" target="_blank">Xebia France</a></strong> </div>
!!!

### 更多资料
https://arild.github.io/csp-presentation/
https://github.com/jfet97/csp
https://pusher.com/sessions/meetup/the-js-roundabout/csp-in-js
[Javascript中的CSP快速入门][3]


  [1]: https://zhangchen915.com/usr/uploads/2019/08/2488025574.jpg
  [2]: https://zhangchen915.com/usr/uploads/2019/09/1414704761.png
  [3]: https://lucasmreis.github.io/blog/quick-introduction-to-csp-in-javascript/
