---
title: "JS读取剪切板中的文字与图片"
categories: ["前端"]
tags: ["js"]
draft: false
slug: "503"
date: "2018-01-10 15:12:00"
---

玩过知乎应该都知道，你在网页中复制一段文字知乎会给你加上一段版权信息。

黏贴事件中的`clipboardData`对象，其实和拖动时产生的`DataTransfer`对象是一样的。我们可以利用其中的`getData`和`setData`方法获取或修改剪贴板中的数据。

##一、监听复制黏贴事件
我们可以直接监听`copy`，`cut`和`paste`这三种类型事件。
```js
document.addEventListener('paste', function(e){
    const clipboard = e.clipboardData;
    
    if(!clipboard.items || !clipboard.items.length) return;
}
```
注意：Chrome下`execCommand`不会触发黏贴事件。

##二、复制时修改剪贴板
```js
document.addEventListener('copy', function(e){
     var clipboard = e.clipboardData;
     clipboard.setData('text/plain','123');
     e.preventDefault();
})
```
`setData`会覆盖同种类型的数据，所以可以通过`window.getSelection().toString()`先获取选中的文字，在加上一些自定义的文字，就能达到知乎的效果。

##三、复制图片
我们可以复制网页或截图(截图工具截的图片，如QQ截图)。网页中的图片须要右键点击`复制图片`，用`ctrl+c`也可以的，不过这样实际上是获得的一段MIME类型为`text/html`的字符串，所以我们还需处理一下才能获得图片的URL。
操作系统中的图片则须用`ctrl+c`，但在火狐下无法复制操作系统的图片。

`clipboardData.items`中的对象有type和kind属性。

- kind属性取值是`file`或`string`。
- type属性是`MIME`类型。

你可以在下面的输入框中黏贴图片。

<iframe height='265' scrolling='no' title='JS copy img' src='//codepen.io/zhangchen915/embed/vpjPoN/?height=265&theme-id=light&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/zhangchen915/pen/vpjPoN/'>JS copy img</a> by zhangchen (<a href='https://codepen.io/zhangchen915'>@zhangchen915</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>
