---
title: "Why Angular 2?"
categories: ["前端"]
tags: ["Angular"]
draft: false
slug: "Why-Angular-2"
date: "2017-02-09 23:26:00"
---

现在有许多前端JavaScript框架可供选择，每个都有自己的权衡。 许多人对Angular 1.x提供的功能感到满意。 Angular 2改进了该功能，使其更快，更可扩展和更现代。 在Angular 1.x中发现价值的组织将在Angular 2中找到更多的价值。

## 数据说话⚡️

这小节内容是译者加的，因为我认为对于新手而言，学习一个框架是有成本的，特别是对于一个不算简单的技术来说，我希望这篇教程是对新手**友好的**，所以我首先要让你放心的将时间和精力投入到Angular2 中。那我们先不谈技术细节，先用数据说话。

### [谷歌趋势](https://www.google.co.jp/trends/explore?cat=31&q=angular%202,vue%202,react,angular,vue)

这里我多说一句，最近看一些文章中谷歌趋势截图，大都没有把范围限定在“**编程**”上。图中可以看出Vue2非常少，所以在下面比较中不再单独统计。

![trends.png][1]

### 教程数量

这里我选取的主要是付费课程，因为付费，所以在一定程度上反应了这个框架的市场需求。教程多意味着社区庞大，企业对这方面人才有需求。如果框架冷门用的人不多，那自然不会有人去为其开发教程。

|         | Angular 2 | React | Vue  |
| ------- | --------- | ----- | ---- |
| udemy   | 12*5      | 12*4  | 5    |
| egghead | 10        | 10    | 无    |
| YouTube | 99k+      | 240k+ | 39k+ |

另外还有一些独立的教程网站

[angularclass](https://angularclass.com/)

### 社区

|               | Angular 2 | React | Vue  |
| ------------- | --------- | ----- | ---- |
| stackoverflow | 66k+      | 80k+  | 4k+  |
| GitHub        | 26k+      | 153k+ | 16k+ |
| Quora         | 500+      | 1.4k+ | 30   |
| stackshare    | *4.7k+*   | 2.5k+ | 189  |

相信随着部分angular1.x项目迁移到2.x，应该数量会更多。

注意stackshare取的是stacks数量，但是由于其并不区分Angular版本，所以用相当一部分属于1.x版本。

### 性能

[性能测试数据](https://rawgit.com/krausest/js-framework-benchmark/master/webdriver-ts/table.html)

| angular v2.2.1 | angular v2.2.1-indexkey | react v15.4.0 | react v15.4.0-indexkey | vue v2.1.3 | vue v1.0.26 |
| -------------- | ----------------------- | ------------- | ---------------------- | ---------- | ----------- |
| 1.43           | 1.23                    | 1.39          | 1.43                   | 1.37       | 1.7         |

对于前端项目而言，一般几千个数据更新基本上不会涉及到性能问题，而且移动端来说，那么小的屏幕，数据量会更少。

所以个人认为，性能并不会成为主要的选择依据，况且性能数据大家都比较接近。

最后，我想你可以放心的开始学习 Angular2 了


  [1]: http://img.bi-bo.cn/2017/02/1248850310.png
