---
title: "关于js精确计算"
categories: ["默认分类"]
tags: ["javascript"]
draft: false
slug: "30"
date: "2015-12-06 15:34:00"
---

如果你有其他编程语言的经验，你就会知道计算机算不准很正常，先想一想咱们用十进制算1/3，没法精确表示，计算机用二进制当然也会有一些数无法精确表示（比如1/10）。
作为新手，而且是学前端的同学，我想目前还不用去深究其中的原理，重要的是我们是如何解决这个问题。
如果计算机算不准那只有换一个思路来解决问题了，我们把数值计算模拟为字符串操作。

下面是一个轮子：
如果以上还不能满足你的需求，可以选择更强的大的[mathjs][1]插件。

    //js 加法计算
    //调用：accAdd(arg1,arg2)
    //返回值：arg1加arg2的精确结果 
    function accAdd(arg1,arg2){ 
      var r1,r2,m; 
      try{r1=arg1.toString().split(".")[1].length}catch(e){r1=0} 
      try{r2=arg2.toString().split(".")[1].length}catch(e){r2=0} 
      m=Math.pow(10,Math.max(r1,r2)) 
      return ((arg1*m+arg2*m)/m).toFixed(2); 
    } 
    //js 减法计算
    //调用：Subtr(arg1,arg2)
    //返回值：arg1减arg2的精确结果 
    function Subtr(arg1,arg2){
         var r1,r2,m,n;
         try{r1=arg1.toString().split(".")[1].length}catch(e){r1=0}
         try{r2=arg2.toString().split(".")[1].length}catch(e){r2=0}
         m=Math.pow(10,Math.max(r1,r2));
         //last modify by deeka
         //动态控制精度长度
         n=(r1>=r2)?r1:r2;
         return ((arg1*m-arg2*m)/m).toFixed(2);
    } 
    //js 除法函数
    //调用：accDiv(arg1,arg2)
    //返回值：arg1除以arg2的精确结果 
    function accDiv(arg1,arg2){ 
      var t1=0,t2=0,r1,r2; 
      try{t1=arg1.toString().split(".")[1].length}catch(e){} 
      try{t2=arg2.toString().split(".")[1].length}catch(e){} 
      with(Math){ 
        r1=Number(arg1.toString().replace(".","")) 
        r2=Number(arg2.toString().replace(".","")) 
        return (r1/r2)*pow(10,t2-t1); 
      } 
    } 
    
    //js 乘法函数
    //调用：accMul(arg1,arg2) 
    //返回值：arg1乘以arg2的精确结果 
    function accMul(arg1,arg2) 
    { 
      var m=0,s1=arg1.toString(),s2=arg2.toString(); 
      try{m+=s1.split(".")[1].length}catch(e){} 
      try{m+=s2.split(".")[1].length}catch(e){} 
      return Number(s1.replace(".",""))*Number(s2.replace(".",""))/Math.pow(10,m) 
    } 



  [1]: http://mathjs.org
