---
title: "Jest 模拟定时器"
categories: ["前端"]
tags: ["Jest"]
draft: false
slug: "826"
date: "2020-08-22 22:27:00"
---

因为定时器依赖于实时时间，所以在测试时不是很方便。
Jest 可以将定时器换成允许我们自己控制时间的功能。

### 定时器模拟
```js
jest.useFakeTimers();   // 使用标准计时器函数的模拟版本
jest.useRealTimers()    // 使用标准计时器函数的真实版本
jest.clearAllTimers();  // 从计时器系统中删除任何挂起的计时器
```

通常每次测试之前手动调用或者在 beforeEach 函数中调用 useFakeTimers。不这样做会导致未重置内部使用计数器。

### runAllTimers
通常在测试时我们不想等待那么长时间才真正执行回调。Jest 提供 jest.runAllTimers函数来运行所有等待中的 timer。

```js
jest.useFakeTimers();

it('closes some time after being opened.', (done) => {
  // An automatic door that fires a `closed` event.
  const autoDoor = new AutoDoor();
  autoDoor.on('closed', done);
  autoDoor.open();
  // 直接运行所有等待中的 timer
  jest.runAllTimers();
});
```

### runTimersToTime
如果不想一次性运行所有计时器怎么办？runTimersToTime 可以只模拟时间流逝。
```js
jest.useFakeTimers();

it('announces it will close before closing.', (done) => {
  const autoDoor = new AutoDoor();
  // The test passes if there's a `closing` event.
  autoDoor.on('closing', done);
  // The test fails if there's a `closed` event.
  autoDoor.on('closed', done.fail);
  autoDoor.open();
  // Only move ahead enough time so that it starts closing, but not enough that it is closed.
  jest.runTimersToTime(autoDoor.TIME_BEFORE_CLOSING);
});
```

### runOnlyPendingTimers
只执行当前挂起的宏任务(即，只包括到目前为止由setTimeout()或setInterval()排队的任务。如果当前挂起的任何宏任务调度了新的宏任务，那么这个调用将不会执行这些新任务。

例如递归地调用setTimeout()，在这个场景中使用 runOnlyPendingTimers 能够一次只向前运行一步。

### advanceTimersByTime
把计时器都提前指定的毫秒。将执行已经通过setTimeout()或setInterval()排队并且将在此时间帧期间执行所有待处理"宏任务"。此外，如果这些宏任务调度将在同一时间帧内执行的新宏任务，那么将执行这些宏任务，直到队列中不再有宏任务应该在msToRun毫秒内运行。

