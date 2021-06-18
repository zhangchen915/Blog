---
title: "CSS实验：搜索框"
categories: ["默认分类"]
tags: []
draft: false
slug: "27"
date: "2015-12-05 21:43:00"
---

前端学习就要多实践，这个周末在改自己博客，准备自己做个主题，关于搜索框我看中文教程不多，找到了一篇不错的英文教程，在这个基础上对代码和描述都有改动。[原文在此][1]

一、 Background Fade
------------------

首先完成最基础的搜索框的html骨架

    &lt;div class="box">
      &lt;div class="container-1">
          &lt;span class="icon"><i class="fa fa-search"></i></span>
          &lt;input type="search" id="search" placeholder="Search..." />
      &lt;/div>
    &lt;/div>

可以看出我们把搜索框分为四个部分，最外层的container-1用来确定搜索框在整个页面的位置和搜索框的大小，icon和search实现搜索框内部样式。

第一步为container-1添加样式，由于只是演示搜索框，所以我们只简单加上宽高就可以了。

    .container-1{
        margin: 100px auto;
        width: 300px;
        height: 50px;
    }

第二步我们为input添加基本样式
  

    .container-1 input{
        width: 300px;
        height: 50px;
        background: #2b303b;
        border: none;
        color: #63717f;
        padding-left: 45px;
        -webkit-border-radius: 5px;
        -moz-border-radius: 5px;
        border-radius: 5px;
       }

第三步为icon添加样式
其中line-height: 50px;为了使图标居中（这个属性设置的居中岁字体大小变化是有一点偏差的，居中只是一般我们看不出偏差）

    .container-1 .icon{
        box-sizing: border-box;
        position: absolute;
        line-height: 50px;
        margin-left: 15px;
        color: #4f5b66;
    }

到现在整个搜索框已经基本完成了，只是看起来还是还不是很炫，接下来我们再加点特效~

第四步通过伪元素:hover:focus实现鼠标经过和获得焦点是背景变化

    .container-1 input#search:hover, .container-1 input#search:focus, .container-1 input#search:active{
        outline:none;
        background: #ffffff;
    }

第五步之前的变化瞬间完成比较生硬，最后在加上过渡效果。
在input元素上加入

    -webkit-transition: background .55s ease;
    -moz-transition: background .55s ease;
    -ms-transition: background .55s ease;
    -o-transition: background .55s ease;
    transition: background .55s ease;

注意，需要要在发生变化的元素上添加，而不是在:hover:focus，可以这样理解:hover:focus存放的是结果，他告诉transtion
我要变成什么样负责触发过渡，而transtion则负责控制过渡的细节，比如时间和速度。

<iframe width="100%" height="300" src="//jsfiddle.net/zhangchen915/Lwwccxee/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

如果我们把上面第五步中的background其他属性那又是一种特效，比如我们想让鼠标悬停时搜索框自动伸长，那就换成width，当然:hover和原始宽度也要发生相应改变。

    .container-1 input#search:focus, .container-1 input#search:active,.container-1:hover input#search{
        width: 300px;
    }
    .container-1:hover .icon{
        color: #93a2ad;
    }
最后把input的宽度改成50px，看下效果是不是更炫了~

**药不能停，继续加特效。**

既然我们的图标是个放大镜，哪我们给这个图标再加个放大效果，用户体验会更好。
我们给.icon加入过渡控制：

  -webkit-transition: all .55s ease;
  -moz-transition: all .55s ease;
  -ms-transition: all .55s ease;
  -o-transition: all .55s ease;
  transition: all .55s ease;

再给.container-1:hover .icon加入过渡后的结果：

    -webkit-transform:scale(1.5); /* Safari and Chrome */
        -moz-transform:scale(1.5); /* Firefox */
        -ms-transform:scale(1.5); /* IE 9 */
        -o-transform:scale(1.5); /* Opera */
        transform:scale(1.5);

看看效果吧
<iframe width="100%" height="300" src="//jsfiddle.net/zhangchen915/v7fgr01a/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

  [1]: http://webdesign.tutsplus.com/tutorials/css-experiments-with-a-search-form-input-and-button--cms-22069
