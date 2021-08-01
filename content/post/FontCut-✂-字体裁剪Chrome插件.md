---
title: "FontCut ✂ 字体裁剪Chrome插件"
categories: ["前端"]
tags: ["chrome插件","字体"]
draft: false
slug: "701"
date: "2019-11-08 02:45:00"
---

![FontCut.jpg][1]

本文介绍一个基于 `opentype.js` 实现的Chrome字体裁剪插件，能够实时预览字体，裁剪并导出otf和woff格式的字体子集。


## 为什么要截取字体？
相对于英文字体，中文字体文字多轮廓复杂，一个文件动辄10多MB。而一些活动页等场景必须要实现富文本。所以我们会将用到的字体裁剪出一个子集。

目前`font-carrier，fontkit，fontmin`等等字体裁剪方案只能借助服务端能力，着实不便。其实这些项目大多也是基于`opentype.js`的。`opentype.js`能够在浏览器将 ttf otf woff 三种文件格式解析为一个 font 类，其实字体裁剪在浏览器就能完成。

![插件截图][2]

## 字体那些事

### 字体格式
#### ttf 与 otf
OpenType字体的Outline描述方法主要有TrueType和Postscript，前者的字库文件后缀名一般为ttf，后者的后缀名一般为otf。
OTF通过提供许多TTF无法提供的功能来扩展TTF。例如，OTF的格式允许最多存储65,000个字符。OTF和TTF之间的主要区别是高级排版功能。OTF具有诸如连字和替代字符（也称为字形）之类的装饰。
![ligatures-640x72.png][3]


#### WOFF
摘录维基百科中的介绍：
> Web开放字体格式（Web Open Font Format，简称WOFF）是一种网页所采用的字体格式标准。此字体格式发展于2009年，由万维网联盟的Web字体工作小组标准化，现在已经是推荐标准。此字体格式不但能够有效利用压缩来减少文件大小，并且不包含加密也不受DRM（数字著作权管理）限制。

WOFF 1.0使用zlib压缩，文件大小一般比TTF小40%。而WOFF 2.0使用Brotli压缩，文件大小比上一版小30%。

WOFF 与 ttf 这种格式还是类似的，主要是压缩了而已。

#### 字体文件是如何存储的
字体文件本身是一个二进制文件，内部是由若干个表组成的。以 TrueType 为例其中最关键的是 `glyf`表，存储了字体的轮廓信息，包含了二次贝塞尔曲线的控制点；字体裁剪插件也是收集收集这些信息再次生成新的文件。
你可以在这里https://photopea.github.io/Typr.js/ 
![](https://i.loli.net/2020/10/13/OLe1V5WNEn9o6GH.png)

打开控制台看一看字体的组成，可以看到 opentype 解析出的结果。
![](https://i.loli.net/2020/10/13/LkF2ZBWsMVrnmdl.png)

### CSS `@font-face`

字体格式可以选择以下几种："woff", "woff2", "truetype", "opentype", "embedded-opentype" 和 "svg"。通常 woff + ttf 就能满组大部分浏览器的兼容（IOS 不支持 woff2）。

```css
@font-face {
  font-family:'Awesome Font';
  font-style: normal;
  font-weight: 400;
  src: local('Awesome Font'),
       url('/fonts/awesome.woff2') format('woff2'),
       url('/fonts/awesome.woff') format('woff'),
       url('/fonts/awesome.ttf') format('truetype'),
       url('/fonts/awesome.eot') format('embedded-opentype');
}
```

#### 加载顺序
**字体指定顺序很重要**，浏览器将选取其支持的第一种格式。所以如果需要兼容的浏览器使用 WOFF2，则应将 WOFF2 声明置于 WOFF 之上，依此类推。

### 字体的加载
> 字体延迟加载可能会有延迟文本渲染的问题：浏览器必须构建渲染树（它依赖 DOM 和 CSSOM 树），然后才能知道需要使用哪些字体资源来渲染文本。 因此，字体请求的处理将远远滞后于其他关键资源请求的处理，并且在提取资源之前，可能会阻止浏览器渲染文本。

![font-crp.png][4]

如果我们知道页面用了那些文字，我们还可以利用新特性：`<link rel="preload">`，在关键渲染路径中提早触发对网络字体的请求，而不必等待创建 CSSOM。

```html
<head>
  <!-- Other tags... -->
  <link rel="preload" href="/fonts/awesome-l.woff2" as="font">
</head>
```

#### 字体的展示时机

字体下载需要时间，那如果这段时间比较长页面已经渲染好了，使用这些字体的文字是如何展示的？

其实浏览器在加载字体是可以分为如下三个阶段：
- 阻塞期：如果未加载字体，则任何尝试使用它的元素都必须呈现不可见的备用字体。如果在此期间成功加载了字体，则可以正常使用。
- 交换期：如果未加载字体，则任何尝试使用它的元素都必须展示备用字体。如果在此期间成功加载了字体，则可以正常使用。
- 失效期；如果未加载字体，则视为加载失败，字体回退。

CSS 中的`font-display`属性可以让我们控制这几个阶段的策略，该属性接受五个值 `auto|block|swap|fallback|optional`（默认为 auto，行为与和 block 类似）。区别如下表：


|          |  阻塞期   | 交换期 |
| -------- | --------------- | ----------- |
| block    | 短           | ♾        |
| swap     | 无            | ♾           |
| fallback | 极短（~100ms） | 短（~3s）     |
| optional | 极短            | 无        |

### 裁剪原理
先将字体文件转换为 arrayBuffer，传递给 opentype.js 的 parse 函数得到字体文件的解析结果。
使用 stringToGlyphs(string) 方法获取需要裁剪的字形信息列表。
最后，结合字体名等信息 new 一个新的 Font 就能得到字体子集。

### 项目地址
https://github.com/zhangchen915/FontSubset 
欢迎试用，来点个 ⭐ 呀 ~


### 参考文献：
- [微软 OTF 规范](http://www.microsoft.com/typography/otspec/otff.htm "OTF")
- [苹果 Truetype 规范文档](https://developer.apple.com/fonts/TTRefMan/RM06/Chap6.html "Truetype")
- [sfnt2WOFF]( https://github.com/odemiral/woff2sfnt-sfnt2woff/blob/master/ "WOFF")
- https://www.makeuseof.com/tag/otf-vs-ttf-fonts-one-better/
- [optimize-webfonts][7]
- [字体是如何存储的？](https://zhuanlan.zhihu.com/p/53036815 "字体是如何存储的？") 


  [1]: https://img.zhangchen915.com/2019/11/1086516777.jpg
  [2]: https://img.zhangchen915.com/2019/11/2484991210.png
  [3]: https://img.zhangchen915.com/2019/11/2874624131.png
  [4]: https://img.zhangchen915.com/2019/11/4080563420.png
  [5]: https://github.com/zhangchen915/FontSubset
  [6]: https://github.com/caryll/
  [7]: https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/webfont-optimization#%E4%BC%98%E5%8C%96%E5%8A%A0%E8%BD%BD%E5%92%8C%E6%B8%B2%E6%9F%93
