---
title: "【译】为什么我们选择Angular 2 而不是 React用于我们的企业级应用开发"
categories: ["默认分类"]
tags: []
draft: false
slug: "Angular2-vs-React"
date: "2017-03-07 00:19:00"
---

作者：Justin Goodhew
[原文链接][1]

![1-xWwEzrjwXOsA3zZTV8ptNA@2x.jpeg][2]

由于这在开发者社区中是一个非常有争议的话题，所以随着我们更深入的理解并结合收到的反馈，我们将不断更新这篇博客。我也想推出一些限定词，所以大家都明白我们来自哪里。

### 我们的背景

我们的公司由我的联合创始人和首席技术官，瑞恩·坎贝尔领导，一位拥有18年的经验的软件架构师，过去10年在大型企业。 当我们决定使用Angular 2时，他已经熟悉Angular 1了。但是，在决定使用Angular之前，我们还在React中构建了一个应用程序。

我们也相信Angular和React都是伟大的选择，并允许一些惊人的产品（React： [Messenger](https://www.messenger.com/)和Angular： [healthcare.gov](https://www.healthcare.gov/)）在其上建立和发展。 根据具体项目，团队以及他们的经验/喜好，制定更加切合实际的决策。 本文的目的是让您了解我们的思想过程，并了解我们采用Angular的旅程中的一些发现。

### 以下是两种技术的简要概述：

**Angular 2** —由Google支持，是最新的JS（javascript）框架。

主要特点：

- 坚持己见—用“Angular 方式”写东西
- Typescript 通常能提高效率
- 有很多由Google支持的样板文件和可重复使用的组件

**React** —由Facebook支持，是一个在JS语言中的库，而不是一个框架。

主要特点：

- 开放—React允许开发人员更多的灵活性和自由度
- 更自由的选择
- 更新和频繁的更改

### 库 vs. 框架

最大的区别是它们是如何组织的。 React是一个[库](http://www.pcmag.com/encyclopedia/term/57725/code-library) ，angular是一个 [框架](http://www.pcmag.com/encyclopedia/term/37907/application-framework)。 定义如下：

- 库：我将使用React库，现在我必须决定所有其他将要用到的支持库：路由，model 管理和构建工具。
- 框架：我将使用Angular 2，让我们开始用Angular 2的方式构建一个应用程序。

因此，我们不会将Angular 2与独立的React进行比较，我们正在与React以及获得与Angular 2功能相同的所有其他软件包（类似React + Redux + React router + Babel）进行比较。

### 我们的 React 经验

当我们在React中构建一个应用程序时，我们真的喜欢这个库。我们对所有的可能性感到兴奋，前期学习远低于Angular。当我们开始构建我们的第一个React Native应用程序时，我们的团队对React的经验有限，而且生态系统频繁更新。

我们知道我们可以使用React来快速创建一个MVP，这是非常重要的，因为这个项目的排期很短。我们的第一步是获得项目设置，这意味着找到正确的配套工具进行集成。这是我们犯了重大错误的地方。我们选择使用 @exponent/ex-navigator 一个路由器库，它似乎有很好的支持，是由exponentjs开发的，他们以React生态系统中的优质工具而闻名。

我们开始整理应用程序的原型，直到我们开始尝试传递数据的应用程序，我们注意到，这个路由器是不实用和废弃的。你必须改变state ，通过导航器传递它，这是你不应该在现代React应用程序中做的第一件事情。

这个经历让我们对使用React构建应用程序感到失望。 React本身是令人惊讶的，在我们的团队中没有一个关于React本身的意见。不幸的是，我们选择的库却不可同日而语。但这是我们自己的错误，如果你对你的初始选择更加谨慎，你可以避免这些问题。然而，总是存在你正在使用的库被废弃的风险。

### 我们的 Angular 2 经验：

我们用 Angular 构建了多个应用。 Angular 2框架中的所有部分，包括高级架构都由Google提供支持。这真的帮助大家融入同一个页面，标准化大团队，但它也导致了一些问题。

对于初学者，需要很多思考才能入门Angular 2和它提供的每个模块。它感觉像一个企业开发人员会熟悉的高质量框架。它正在推动基于Web的应用程序框架中预期的界限。 我们一直在使用jQuery甚至是Backbone来开发应用程序。我们的团队喜欢Angular 2 的依赖注入，使用响应式编程可观察模式构建的内置即用型HTTP模块，一个精心设计的Angular 2 升级程序允许将大型项目逐个移动到Angular 2。

我所遇到的唯一缺点是框架的一切还没有完全被记录下来。 到目前为止，Angular的文档质量很好，但是只能涉及到许多Angular 2模块的表面。作为一个团队，我们通过深入了解Angular 2源代码并分享我们的知识，成为专家，一起工作。

一般来说，Angular 2给我们留下了深刻的印象，它是我们的首选框架。我们已经能够比过去的框架更容易完成功能。 TypeScript，记录的最佳实践，以及Angular 2架构使构建应用程序更愉快。我们能够了解OOP，设计模式和最佳实践架构，并将其应用于web。

### 为什么Angular更好？

我们的客户主要是希望从开源获益的大型企业。 当选择软件时，他们关心通过以下方式降低风险：

- 技术一致性
- 社区支持
- 清晰的最佳实践
- 最小未知

他们有数百万的用户，以及数百万或数十亿美元，受他们管理的软件影响。 这些决定是至关重要的，有时甚至关乎生死。

他们还坚持使用一种技术很长时间，需要一致性，清晰的最佳实践和最小未知。

必须降低技术风险，以允许企业利用开源的力量，并从开源社区提供的惊人的工作中获益。

### 企业如何量化风险？

企业在三个不同领域量化风险：支持，社区和风险。 当在这些领域中比较React和Angular 2时，根据企业的需求，你可以更好地了解为什么Angular 2有利于他们。

**周边的技术支持**

React —中断支持很快，保持更新是很困难的

Angular 2 —Google公开支持时间和版本迭代安排：2.0版之后没有更多突破性变化

**可雇佣**

React

- 大量社区追随者，会有大量人才储备。
- 更少标准化/更多培训 - 由于技术的开放性开发者将以自己的方式构建。

Angular

- 大量社区追随者，会有大量人才储备。
- 上手简单 - 开发人员将遵循类似的最佳实践。
- 标准化 - Google撰写了如何使用框架的最佳实践。

**被支持者/创造者遗弃的潜在风险**

React

- 支持 - 高度支持，增长的社区。
- 依赖他人 - 高。 库由Facebook支持，但其他需要的部分基本由第三方建立。

Angular

- 支持 - 高度支持，增长的社区。
- 依赖他人 - 全面支持。 最小化依赖于除Google和他们的开发团队之外的其他人。

### 结论

基于我们与React和Angular 2开发的经验以及客户的需求，我们决定使用Angular。 这并不意味着我们不能接受新的选择，但是现在，对于我们的客户，Angular符合他们的需求，我们对结果感到非常激动。

我们知道有很多关于这个问题的意见，所以请随时让我们知道你选择了什么，原因为何。 我们将深入探讨这两个框架中的调试和开发工具，以便更新。


------

[Biznas.io](https://biznas.io/)
[Biznas on Twitter](https://twitter.com/biznasapps)
[Biznas on Facebook](https://www.facebook.com/BiznasApps/)
[Biznas on LinkedIn](https://www.linkedin.com/company/10871824?trk=tyah&trkInfo=clickedVertical%3Acompany%2CclickedEntityId%3A10871824%2Cidx%3A2-1-2%2CtarId%3A1475705404220%2Ctas%3Abiznas)


  [1]: https://blog.biznas.io/why-we-chose-angular-2-over-react-for-our-enterprise-software-development-work-392e2c9e39a9
  [2]: http://img.bi-bo.cn/2017/03/3914609364.jpeg
