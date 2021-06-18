---
title: "js定义类或对象"
categories: ["默认分类"]
tags: ["javascript"]
draft: false
slug: "31"
date: "2015-12-06 22:49:00"
---

如果你没有其他编程语言的基础，你可以把类看成图纸，对象看成通过图纸制造出来的产品。而在一个图纸上增加一些内容，形成一张新的图纸，这就叫继承，即新的图纸继承了之前图纸的一些内容。通过图纸制造产品的过程就是实例化。
首先我们如何创造类即我们的图纸，在w3c上有好几种创造类的方式，但是其中的一些或多或少都有一些副作用，所以我们只要最好的。如果你想看其他方式可以去看这篇文章[ECMAScript 定义类或对象][1]

**混合的构造函数/原型方式**
说多少也不如先来一段代码看看

    function Car(sColor,iDoors,iMpg) {
      this.color = sColor;
      this.doors = iDoors;
      this.mpg = iMpg;
      this.drivers = new Array("Mike","John");
    }
    
    Car.prototype.showColor = function() {
      alert(this.color);
    };
    
    var oCar1 = new Car("red",4,23);
    var oCar2 = new Car("blue",3,25);
    
    oCar1.drivers.push("Bill");
    
    alert(oCar1.drivers);	//输出 "Mike,John,Bill"
    alert(oCar2.drivers);	//输出 "Mike,John"

Car和Car.prototype.showColor里面就是我们的图纸，不过我们把图纸中的数据和方法分开，因为如果我们把他们放到一起，那么每一个产品都将创建Car.prototype.showColor方法，这显然是没必要的，只要使用时去调用他就够了。
new Car()就是图纸Car实例化了一个产品，最后我们通过把这个对象赋值给一个变量，来给他起个名字。

另外，你还会发先构造函数名一般都是首字母大写，这样做的目的是使它与首字母通常是小写的变量名分开。

这样来理解起来应该形象一些了。


  [1]: http://www.w3school.com.cn/js/pro_js_object_defining.asp
