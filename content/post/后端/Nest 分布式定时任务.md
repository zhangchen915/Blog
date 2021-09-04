---
title: "Nest 自定义 swagger 装饰器"
categories: ["Nest.js"]
tags: []
slug: "nest-swagger-customer-decorate"
date: "2021-9-3"
---



[***Bull\***](https://optimalbits.github.io/bull/)*是一个 Node 库，它基于*[***Redis\***](https://redis.io/)*实现了一个快速而健壮的队列系统**。*

*尽管可以直接使用 Redis 命令实现队列，但该库提供了一个 API，该 API 负责处理所有底层细节并丰富 Redis 基本功能，以便可以轻松处理更复杂的用例。*

### 特性

- 由于无轮询设计，CPU使用率最低
- 基于Redis的稳健设计
- 延迟工作（延迟任务）
- 根据cron规范安排和重复工作（定时任务）
- 工作的速率限制器
- 重试
- 优先
- 并发
- 暂停/恢复 - 全局或本地
- 每个队列有多个作业类型
- 螺纹（沙盒）处理功能
- 从进程崩溃中自动恢复

### 生命周期

![life circle](https://optimalbits.github.io/bull/job-lifecycle.png)

### 队列

队列实例通常可以有 3 个主要的不同角色：作业生产者、作业消费者或/和事件侦听器。一个队列可以有很多生产者、许多消费者和许多侦听器；而且即使此时没有可用的消费者，生产者也可以将作业添加到队列中：队列提供异步通信，这是使它们如此强大的功能之一。

#### 生产者

```
const myFirstQueue = new Bull('my-first-queue');

const job = await myFirstQueue.add({
  foo: 'bar'
});
```

#### 消费者

```
myFirstQueue.process(async (job) => {
  let progress = 0;
  for(i = 0; i < 100; i++){
    await doSomething(job.data);
    progress += 10;
    job.progress(progress);
  }
});
```

#### 监听器

```
const myFirstQueue = new Bull('my-first-queue');

// Define a local completed event
myFirstQueue.on('completed', (job, result) => {
  console.log(`Job completed with result ${result}`);
})
```

### 定时任务

定时任务是根据 cron 规范或时间间隔无限期地重复自己或直到达到给定的最大日期或重复次数的特殊作业。

```
/ Repeat every 10 seconds for 100 times.
const myJob = await myqueue.add(
  { foo: 'bar' },
  {
    repeat: {
      every: 10000,
      limit: 100
    }
  }
);

// Repeat payment job once every day at 3:15 (am)
paymentsQueue.add(paymentsData, { repeat: { cron: '15 3 * * *' } });
```

关于定时任务有一些重要的考虑因素：

- 如果重复选项相同，Bull 足够聪明，不会添加相同的定时任务。（注意：作业 ID 是重复选项的一部分，因为：https://github.com/OptimalBits/bull/pull/603，因此传递作业 ID 将允许将具有相同 cron 的作业插入队列中）
- 如果没有worker在运行，则下次worker在线时不会累积可重复的作业。
- 可以使用[removeRepeatable](https://github.com/OptimalBits/bull/blob/master/REFERENCE.md#queueremoverepeatable)方法删除可重复的作业。