---
title: "Typescript - 面向接口编程"
categories: ["前端"]
tags: ["Typescript"]
draft: false
slug: "264"
date: "2017-02-21 11:09:00"
---

接口应有两类：第一类是对一个体的抽象，它可对应为一个抽象体(abstract class)；
第二类是对一个体某一方面的抽象，即形成一个抽象面（interface）；
一个体有可能有多个抽象面。

#所谓“接口”

面向接口编程中的"接口"二字具体到语言中（Java）不仅仅是interface关键字这么简单。可以理解为接口是对具体实现的抽象。试想一下，团队协同以及代码健壮可维护性的需求日益增强的趋势下，通过暴露接口来提供服务本身是一件非常愉悦的事情。A需要调用B中的服务，A却不需要去仔细阅读B写的代码，通过接口文档就可以看出对应业务的方法和参数类型，进而使用RMI或者RPC等相关技术实现模块化调用。而这一切本身就是面向接口编程。

#abstract class 和 interface

interface、abstract class以及普通的class都能成为所谓的接口，甚至abstract class的功能可以更加强大。那么问题来了，interface和abstract class分别对应于什么场景下使用更合适一些？
interface和abstract class区别在于interface约定的是务必要实现的方法和参数，强调规则的制定；abstract class则在抽象的同时允许提供一些默认的行为，以达到代码复用的效果。例如定义一些基础、初始化方法等。另外，还有一个常识性的区别，一个实现类（相对于抽象而言）可以实现多个interface，而只能继承一个abstract class，在代码设计的过程中务必注意。

|| 在接口和抽象类的选择上，必须遵守这样一个原则：行为模型应该总是通过接口而不是抽象类定义。
