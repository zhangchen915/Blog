---
title: "Angular 条件事件绑定"
categories: ["前端"]
tags: ["Angular"]
draft: false
slug: "487"
date: "2017-11-16 15:58:00"
---

有时为了减少事件监听的数量，我们不想用`@HostListener`装饰器为每个组件实例都绑定事件。
这时需要我们用`Renderer2`的`listen`方法在构造函数或`ngOnInit`中手动绑定事件。

```js
constructor(renderer: Renderer2, elementRef: ElementRef) {
 if(myCondition) {
       let even=renderer.listen(elementRef.nativeElement, 'click', () => {
          console.log('Callback');
       });
  }
}

//移除绑定
even();
```

**参考阅读：**
[Dynamically add event listener in Angular 2][1]


  [1]: https://stackoverflow.com/questions/35080387/dynamically-add-event-listener-in-angular-2/35082441
