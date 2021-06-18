---
title: "MVC,MVP,MVVM的理解"
categories: ["默认分类"]
tags: []
draft: false
slug: "133"
date: "2016-03-30 22:46:17"
---

React我不喜欢，Angular2有没正式发布文档也很少，Angular1性能有不咋地，想学点框架真愁人。
所以只能先拿Vue来学习学习了，不过这个小框架大家的评价都不错，前景也很受大家看好，学习起来也挺容易的。

学习框架先要了解一下框架的几种类型：MVC,MVP,MVVM

**一、MVC**
视图（View）：用户界面传送指令到控制器
控制器（Controller）：进行业务逻辑，完成后改变数据
模型（Model）：将数据反馈到视图

**二、MVP**
C变成了P，这个P又是什么？
呈现者（Presenter）：根据这个名字就理解的差不多了，view 抛出一个事件时, 作为响应, Presenter 对 View 进行更新.
业务逻辑存在于 Presenter，也就相当于控制器，只是Modle和View隔离开了，剩下的各部分之间的通信，都变成双向。

**三、MVVM**
又多了一个V，这个V的意思是ViewModel，View的变化会直接影响ViewModel，ViewModel的变化或者内容也会直接体现在View上，这就是所谓的双向数据绑定。Vue和Angular这两种框架就是属于这种模式，不过Vue默认是单相数据绑定。

**其实框架就是帮我们做了些事，让我们实现复杂的交互更加方便，能把精力都放在算法和业务逻辑上。**
