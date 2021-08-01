---
title: "关于js阻塞"
categories: ["默认分类"]
tags: []
draft: false
slug: "70"
date: "2015-12-22 17:28:00"
---

先看下面这段代码，是不是很熟悉这样的写法啦，但是这样做存在一个很大的性能问题，为什么这么说，因为浏览器在下载js文件时会阻止一切其他活动，比如图片的加载等等，只有到js文件下载、解析、执行都完毕后才开始继续并行下载其他资源并呈现内容。

    <html>    
        <head>
        <meta charset="UTF-8">
        <title>Demo</title>
        <script src="js/file1.js"></script>
        <script src="js/file2.js"></script>
        <link rel="stylesheet" href="style.css">
    </head>
    <body>
    </body>
    </html>

![js阻塞.png][1]

虽然现在的浏览器都能支持js文件并行下载，但是js文件下载时仍会阻塞其他资源。
所以一个基本的方法是吧scrip标签放到body标签的底部，或者在标签中加入defer（定义该脚本是否会延迟到文档解析完毕后才执行）。
关于延迟加载，在HTML5中引入了async属性，async与defer都是采用并行下载，，却别在于执行时机，async在js文件下载完成后就会执行，而defer要等到页面加载完成才会执行。**因为async下载完就会执行，所以如果你的js代码有依赖关系的化，那么可能就会出现问题。**

JavaScript动态脚本元素
--------------------

我们把js文件加载放到底部看起来不错，但是治标不治本js文件仍然是阻塞加载，对于一些一般的网页也许到这里也就够了，但事实上仍有优化的空间。
因为js可以创建任何HTML内容，所以我们也能借助一段js来动态地加载其他所有的js文件。

    var myScript= document.createElement("script");
    myScript.type = "text/javascript";
    myScript.src="";
    document.head.appendChild(myScript);
用这种方式来加载js文件就再也阻塞其他资源下载了。

使用RequireJS
------------
**了解RequireJS**
RequireJS的目标是鼓励代码的**模块化**，它使用了不同于传统script标签的脚本加载步骤。可以用它来加速、优化代码，但其主要目的还是为了代码的模块化。它鼓励在使用脚本时以module ID替代URL地址。

首先啥叫模块化？先看看我们之前写的代码
function fun1() {
  ... }
function fun2() {
  ... }
这样做没什么问题，但是如果你的代码很多那就会出现命名冲突等问题，而且不利于维护。所以我们采用模块化来组织代码，其实就是吧各个功能放到一个模块里。模块有什么好处那：

 - 模块作用域自成一体，不会污染全局空间
 - 模块明确依赖关系，依赖通过参数传递进入模块作用域，不需要引用全局变量

**定义模块**

**1.无依赖的模块**
假如一个模块不依赖其他模块，那么定义起来很简单：

    define(function() {
        // some code here
      return {
        // some public api
      };
    });

**2.有依赖的模块**
如果模块存在依赖：则第一个参数是依赖的名称数组；第二个参数是函数，在模块的所有依赖加载完毕后，该函数会被调用来定义该模块，因此该模块应该返回一个定义了本模块的object。依赖关系会以参数的形式注入到该函数上，参数列表与依赖名称列表一一对应，最常见的是依赖jQuery：

    define(['jquery'], function($) { 
        // 比如这个模块，代码依赖 jQuery，require.js 会先加载并执行 `jquery` 模块代码，
        // 然后将依赖模块以 `$` 的参数形式传入回调函数中，回调函数将执行结果注册为模块
      return {
        // some public api
      };
    });

**模块命名**
define 函数共有三个参数，第一个参数 id 定义模块名称，这个名称的格式即 baseUrl 的路径除去文件格式，比如 baseUrl 为 js 目录，一个模块放在 js/libs/file1.js 里，则模块名称：

`define('libs/file1', ['jquery'], function($){......});`
这些常由优化工具生成。你也可以自己显式指定模块名称，但这使模块更不具备移植性——就是说若你将文件移动到其他目录下，你就得重命名。

**使用模块**
讲了那么多概念不如举个具体的例子：
假如我们的js文件夹下有这么几个文件
| js/
|—main.js
|—require.js
|—jquery.js

其中myjs依赖jquer，main.js为入口文件

在HTML中只需要像这样引用require.js
　
    <script src="js/require.js" data-main="js/main"></script> 

require.js就是用来加载js的所以文件后缀当然默认的就是.js，其中的main.js是整个网页的入口代码，意思就是C语言的main()函数，所有代码都从这儿开始运行。

main.js：

    require(['jquery'], function($) {
        // you code
    });

拓展阅读：
[浏览器阻塞探究][2]——一个实验直观地展示浏览器阻塞
[js动态加载脚本][3]——如果你对其他动态加载方法感兴趣可以看这篇文章 
[Google Develop 优化性能][4]
[RequireJS API][5]
[Require.js 教程][6]
[使用 RequireJS 优化 Web 应用前端][7]


  [1]: http://www.img.zhangchen915.com/2015/12/3844294820.png
  [2]: https://github.com/ericdum/mujiang.info/issues/2
  [3]: http://www.cnblogs.com/zhuimengdeyuanyuan/archive/2013/03/06/2946277.html
  [4]: https://developers.google.com/web/fundamentals/performance/?hl=zh
  [5]: http://www.requirejs.cn/home.html
  [6]: https://www.zfanw.com/blog/require-js.html
  [7]: https://www.ibm.com/developerworks/cn/web/1209_shiwei_requirejs/
