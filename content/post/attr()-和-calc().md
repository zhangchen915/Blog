---
title: "attr() 和 calc()"
categories: ["默认分类"]
tags: ["CSS"]
draft: false
slug: "attr-和calc"
date: "2016-05-23 18:12:00"
---

一、attr()

> attr() 用来获取选择到的元素的某一HTML属性值，并用于其样式。它也可以用于伪元素，属性值采用伪元素所依附的元素。
注意：目前在所有浏览器中，只有content属性实现了对attr()的支持。

    .test::before {
      content: '我的类是' attr(class) '我的ID是' attr(id);
    }

二、calc()

> calc 其实就是 calculate
> 的简写，它能够动态的计算长度值，而且不用考虑单位问题。其实浏览器对这个属性的兼容性也很好，IE9以上的浏览器都支持，所以放心地用吧。
> 注意：clac 支持加减乘除，但是表达式中有“+”和“-”时，其前后必须要有空格。

    .test{
          width: calc(100% - 10em);
     }


**相关阅读：**
[1.CSS3的calc()使用][1]


  [1]: http://www.w3cplus.com/css3/how-to-use-css3-calc-function.html
