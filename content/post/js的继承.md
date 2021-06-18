---
title: "js的继承"
categories: ["默认分类"]
tags: []
draft: false
slug: "32"
date: "2015-12-07 01:01:00"
---

在之前我们已经提到过继承，我们把类比作图纸，一个图纸继承了另一张图纸他就拥有了它的全部方法。

因为js中没有明确规定继承的实现，而是通过模仿继承效果实现的，所以继承方法也不止一种。还是那样，要用就用最好的，但是每个方法还是有他的局限性，那怎么办？其是还是像之前实现类思想一样，还是混合方式，只是我们只利用每种方法中的优点。
**组合继承**
先看代码：

    function ClassA(sColor) {
        this.color = sColor;
    }
    
    ClassA.prototype.sayColor = function () {
        alert(this.color);
    };
    //以上我们定义了ClassA类
    function ClassB(sColor, sName) {
        ClassA.call(this, sColor);//用对象冒充继承ClassA类的sColor属性，同时还传递了参数（第二次调用ClassA）
        this.name = sName;
    }
    
    ClassB.prototype = new ClassA();//用原型链继承 ClassA 类的方法（第一次调用ClassA）
    
    ClassB.prototype.sayName = function () {
        alert(this.name);
    };
先解释下啥叫对象冒充，看起来很不靠谱的样子。还是换个好听的名字吧，“借用构造函数”或者“经典继承”，是不是瞬间高大上。其实理解也很简单，函数是对象只是他能在特定环境中执行代码，所以可以通过apply()或call()在之后创建的对象执行构造函数。最说说这个方法的问题，我们说继承的目的是什么，就是模块化，是函数的复用，但是如果每一个实例的方法都要在构造函数中定义
那何谈复用，所以我们不会单独使用。

在看看啥是原型链，这个名字挺听上去就霸气，还是回到上面的代码，我们把 ClassB 的 prototype 属性设置成 ClassA 的实例。因为想要 ClassA 的所有属性和方法，还有比把 ClassA 的实例赋予 prototype 属性更好的方法吗？但是问题也随之而来，既然我们的ClassB.prototype是 ClassA的实例，那么所有的ClassB实例都会共享ClassA 中的属性，也就是说改了A后B也变了，显然不能这样。

而上面者种组合继承的方法，避免了缺陷，融合了两种方法的优点，是最常用的继承方式，而且，instanceof和isPrototypeOf()也能够识别这种方法创建的对象。为啥最常用了还不说是最好的哪？因为实现的时候调用了两次超类（父类），如果要完美的继承，我们还需要加一点特效，这个特效就是“寄生”。

“寄生”又是什么鬼，还是先看代码：

    function inheritPrototype(ClassB, ClassA){
    var prototype = object(ClassA.prototype);
    prototype.constructor = ClassB;
    ClassB.prototype = prototype;
    }
此外之前的ClassB.prototype = new ClassA();替换成了inheritPrototype(ClassB, ClassA)

在讲这段代码之前我们先要了解Object.prototype.constructor，它的作用是返回一个指向创建了该对象原型的函数引用。需要注意的是，该属性的值是那个函数本身，而不是一个包含函数名称的字符串。
接下来再深入看看这段代码，第一句通过ClassA.prototype创建了一个副本，第二句通过prototype.constructor把副本的引用指向ClassB，最后将这个副本对象赋给ClassB。
其是我们需要的就是原型的副本，所以我们找了这么一个方法，代替之前调用函数，从而提高效率。

所以这种寄生组合式继承才能说是“最”有效的方式。

