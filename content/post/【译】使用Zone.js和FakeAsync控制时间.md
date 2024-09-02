---
title: "【译】使用Zone.js和FakeAsync控制时间"
categories: ["默认分类"]
tags: []
draft: false
slug: "265"
date: "2017-02-21 13:52:00"
---

JavaScript应用程序充满了异步代码：我们侦听事件，使它们去抖，与web workers交互，向后端发送消息。测试这样的代码是困难的，并且在一些情况下几乎不可能。 在这篇博文中，我将展示测试异步代码的传统方式，以及如何通过使用Zone.js和fakeAsync，以及通过控制时间本身，我们可以使异步测试更直接和可靠。

#案例分析

让我们来看看这段代码示例：
```js
interface PurchaseBackend {
  checkAvailability(sku: string, qty: number): Promise<boolean>;
  order(sku: string, qty: number): Promise<boolean>;
}

interface Messages {
  success(message: string): void;
  error(message: string): void;
}

class PurchaseService {
  inProgress = false;

  constructor(private backend: PurchaseBackend, private messages: Messages) {}

  order(sku: string, qty: number): void {
    if (this.inProgress) {
      this.messages.error("Already purchasing an item");
      return;
    }

    this.backend.checkAvailability(sku, qty).then((enoughQuantity: boolean) => {
      if (enoughQuantity) {
        this.inProgress = true;
        return this.backend.order(sku, qty);
      } else {
        return false;
      }

    }).then((success: boolean) => {
      if (success) {
        this.messages.success("Your purchase succeeded!");
      } else {
        this.messages.error("Your purchase failed!");
      }

    }).then(() => {
      this.inProgress = false;
    }, () => {
      this.inProgress = false;
    });
  }
}
```
- PurchaseService协调checkAvailability和订单调用。 它询问是否有足够数量的特定sku可用，如果可用，请订购。 此外，它在字段中跟踪订单的状态。
- 消息表示一些抽象的UI，这里是使框架独立的示例。 如果它是一个Angular应用程序，这可以是一个Angular组件。
- 不出所料，PurchaseBackend代表后端。

#测试
我们要编写一个测试PurchaseService验证该INPROGRESS字段设置为真后，我们检查其可用性，而且它仍然是真实的，直到我们完成订单。
这是一种方法。

#分析测试
这里有很多东西要解开。
首先，我们有承诺的一个链条，我们需要插入的状态PurchaseService在这条产业链的中间，也就是说，没有承诺，我们可以等待运行断言。为了能够做到这一点，我们协调两个promise的完成，并使用两个嵌套的setTimeout调用。
我们能够避免一些吧，使测试通过每一个中间结果存储在的某些属性更好PurchaseService，但是这将让服务更加有状态的，复杂的。使代码可测试应该改善其设计，而不是其他方式！
注意，测试中的服务实际上相当简单，很容易想象一个协调多个异步进程，其中一些将被表示为observables和超时。测试这样的服务将更加困难，并且测试将更加难以遵循。
但等待 - 它变得更糟。如果抛出异常，并且其中一个promise被拒绝，Jasmine将等待测试超时，从而导致糟糕的开发体验。如果我们把一些去抖动混入，我们实际上必须等待几百毫秒的测试完成。
最后，在运行任何断言之前，验证所有计划的异步函数是否完成通常是一个好主意。这是不可能做到的，至少在一般情况下，这种风格的测试。

#解决方案：Zone.js
解决方案是控制时间本身！

许多图书馆试图这样做。例如，我们可以为RxJS提供一个自定义调度程序。然而，这样的解决方案不是全面的：它们针对图书馆，因此是过高的级别。

该Zone.js库采用不同的方法：它会低一个级别和补丁浏览器。这是为了使所有异步原语（例如，setTimeout，Promise）通过一个单独的地方，这允许我们使用一个机制来控制时间，而不管我们使用的库。

![0-QDXohxNhXdtH4Px--.png][1]

我们开发了Zone.js作为Angular项目的一部分。最初的目标不是控制时间，而是知道何时异步操作完成，所以我们可以运行Angular的变化检测和更新视图。这就是为什么新版本的Angular不再需要$ scope.apply  -  Zone.js告诉Angular何时检查更改，它为用户透明地执行。事实上，Zone.js也可以用来控制测试中的时间是一个副产品，但是一个非常好的。

#FakeAsync
添加Zone.js到页面补丁的浏览器，并创建一个全局 Zone 对象，我们可以用它来与库进行交互。的默认实现园区包装浏览器异步原语：因此调用包裹的setTimeout会做一些区相关的工作，但最终将调用浏览器的setTimeout的。

但是 Zone 有一个不同的实现称为FakeAsync。它保存所有的调用到一个数组，而不是调用浏览器。所以所有的计划异步工作都存储在这个数组中。然后它可以通过遍历该数组并调用存储的函数来模拟时间的流逝。

![0-SW-FFXeQs_B8JTjL-.png][2]

所以当使用FakeAsync时，浏览器的setTimeout永远不会被调用。这意味着使用的测试FakeAsync将始终在单个microtask执行。这样的测试感觉同步或阻塞，即使在技术上不是这样。

#辅助函数：fakeAsync，tick，flushMicrotasks。
即使FakeAsync不绑定到Angular，框架提供了一些帮助函数，使使用这个 zone 更容易：fakeAsync，tick和flushMicrotasks。

我将在下面的例子中使用这些辅助函数，所以我将从Angular包中导入它们。但是如果你是另一个UI框架的愉快用户，你总是可以移植它们（大约100行代码）。

```js
import {fakeAsync, flushMicrotasks} from '@angular/core/testing';

describe("PurchaseService", () => {
  it("fakeAsync test", fakeAsync(() => {
    const b = jasmine.createSpyObj("PurchaseBackend", ['checkAvailability', 'order']);
    const m = jasmine.createSpyObj("Messages", ['success', 'error']);

    b.checkAvailability.and.returnValue(Promise.resolve(true));

    let orderResolve = null;
    const orderResult = new Promise(r => orderResolve = r);
    b.order.and.returnValue(orderResult);

    const p = new PurchaseService(b, m);

    p.order("Sku123", 10);
    flushMicrotasks();
    expect(p.inProgress).toBe(true);

    orderResolve(true);
    flushMicrotasks();

    expect(p.inProgress).toBe(false);
    expect(m.success).toHaveBeenCalledWith("Your purchase succeeded!");
  }));
});
```

让我们逐步理解这个测试。

我们将测试包装到fakeAsync调用中，以确保在测试完成后没有异步工作未完成。

然后，如上面的测试，我们调用p.order。但在这种情况下，它不计划真正的微任务，而是将其添加到FakeAsync的微任务队列。

在这一点上，我们基本上被阻塞在：
```js
this.backend.checkAvailability(sku, qty).then((enoughQuantity: boolean) => {
  if (enoughQuantity) {
     this.inProgress = true;
     return this.backend.order(sku, qty);
  } else {
     return false;
  }
  //…
```
调用flushMicrotasks运行传入的函数，然后将inProgress设置为true，并将另一个微任务添加到队列中。第一个微任务现在完成，并从队列中删除。

虽然被阻塞，我们正在按顺序完成promise。调用flushMicrotasks第二次运行调度的微任务，这完成了整个操作。

如果我们在测试完成后在队列中有微任务，我们会得到一个错误。例如，以下测试会报错：
```js
import {fakeAsync, flushMicrotasks} from '@angular/core/testing';

describe("PurchaseService", () => {
  it("fakeAsync test", fakeAsync(() => {
    const b = jasmine.createSpyObj("PurchaseBackend", ['checkAvailability', 'order']);
    const m = jasmine.createSpyObj("Messages", ['success', 'error']);

    b.checkAvailability.and.returnValue(Promise.resolve(true));

    let orderResolve = null;
    const orderResult = new Promise(r => orderResolve = r);
    b.order.and.returnValue(orderResult);

    const p = new PurchaseService(b, m);

    p.order("Sku123", 10);
    flushMicrotasks();
    expect(p.inProgress).toBe(true);

    orderResolve(true);
  }));
});
```

#Analyzing the Test
First, the test is flat: no setTimeout calls required. Flat tests are easier to follow, refactor, and debug.
Second, if one of the promises threw an exception, the corresponding flushMicrotasks call would throw that exception instantly, which results in a better dev experience.
Third, fakeAsync verifies that no pending microtasks or timeouts left after the test is complete, again resulting in a better dev experience.
And, finally, testing debouncing and other time-related operations is just as easy. Instead of calling flushMicrotasks, we can call tick and pass the number of milliseconds we want to skip.

#Zone.js in the Browser
This is not the only application of Zone.js. As I mentioned, the new versions of Angular use it to trigger change detection. It can also be used to enhance error reporting, implement long stack traces, etc.. Since it is such a useful primitive, it is slowly making its way into the browser (see here https://gist.github.com/mhevery/63fdcdf7c65886051d55). So think of Zone.js as a prollyfill.

#Summary
As JavaScript developers we write a lot of asynchronous code. In this article I showed that such code is hard to test, and testing even a fairly simple service requires promise coordination and nested setTimeout calls. The solution is to take control of time with the help of Zone.js. This results in flat tests that run in a single microtask. Such tests are easier to follow, change, and debug.



  [1]: http://img.bi-bo.cn/2017/02/1971631523.png
  [2]: http://img.bi-bo.cn/2017/02/4253397062.png
