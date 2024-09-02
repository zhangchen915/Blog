---
title: "前端状态管理——Redux与有限状态机（FSM）"
categories: ["前端"]
tags: []
draft: false
slug: "618"
date: "2019-08-04 23:29:00"
---

## Redux
Redux和Vuex都是基于Flux的思想实现的，这里我们以Redux为例。
###核心概念
- 所有的状态存放在 Store。组件每次重新渲染，都必须由状态变化引起。
- 用户在 UI 上发出 action。
- 纯函数 reducer 接收 action，然后根据当前的 state，计算出新的 state。

### 特点
数据的单一来源：即整个APP尽量不使用内部state，所有的状态全部源自于Redux
纯函数reducer：处理action并生成新的state，其形式为 newState = f(state，action)，不允许任何副作用
不可变state：只有当state对象被整体替换时候，才会触发view层的更新。

### 副作用
在函数式编程中，最大的特点就是纯函数，一个输入一个结果，这个过程想数学函数一样，透明可控，但是异步操作等给应用程序带来了不确定性和可变性，这种被称为副作用。直接用Redux处理这些副作用比较复杂也难以测试，所以出现了redux-saga，mobx等工具。

##有限状态机（FSM）
有限状态机（finite-state machine），是表示有限个状态以及在这些状态之间的转移和动作等行为的数学模型。
比如TCP连接，Promise都可以用状态机来表示他们工作方式。
![捕获.PNG][1]
###特点
- 状态总数是有限的。
- 任一时刻，只处在一种状态之中。
- 某种条件下，会从一种状态转变（transition）到另一种状态。

###与有限状态机差异
- 都是一个状态容器，但FSM将将有限状态与无限状态或者上下文状态区分开，也正因如此有限状态机可以可视化状态之间的转换
- redux 的 reducer 就是一个函数，而状态机是“带规则的reducer” ，你可以定义由于事件导致的有限状态之间的合法转换，以及应该在转换中（或在状态的进入/退出时）执行哪些操作
- redux 没有内置处理副作用
- Redux 需要使用单一的全局原子性的 Store，状态机使用类似`角色模型`的方式，其中可以存在许多彼此通信的分层状态图。
- Redux 单向数据流动，状态机并没约束数据流。
- 状态机可以适应任何框架
###如何选择
关键要看你主要关注应用的最终状态还是**约束状态变化**和**状态转换的过程**。

参考文章：

[What is an actual difference between redux and a state machine (e.g. xstate)?][2]


  [1]: https://img.bi-bo.cn/2019/08/4206290673.png
  [2]: https://stackoverflow.com/questions/54482695/what-is-an-actual-difference-between-redux-and-a-state-machine-e-g-xstate
