---
title: "使用SVG Patterns作为平铺背景"
categories: ["前端"]
tags: ["SVG"]
draft: false
slug: "638"
date: "2019-08-29 23:11:00"
---

```html
<svg width="100%" height="100%">
    <defs>
        <pattern id="polka-dots" x="0" y="0" width="100" height="100" patternUnits="userSpaceOnUse">
             
            <circle fill="#bee9e8" cx="50" cy="50" r="25">
            </circle>
             
        </pattern>
    </defs>
     
    <rect x="0" y="0" width="100%" height="100%" fill="url(#polka-dots)"></rect>
</svg>
```
defs标签内定义一个pattern并提供一个id，让后将fill属性指向该ID的URL : fill="url(#polka-dots)"。
效果如下：

!!!
<svg width="100%" height="100%">
    <defs>
        <pattern id="polka-dots" x="0" y="0" width="100" height="100" patternUnits="userSpaceOnUse">
             
            <circle fill="#bee9e8" cx="50" cy="50" r="25">
            </circle>
             
        </pattern>
    </defs>
     
    <rect x="0" y="0" width="100%" height="100%" fill="url(#polka-dots)"></rect>
</svg>
!!!

更多的属性可以在这里尝试

<iframe height="360" style="width: 100%;" scrolling="no" title="Messing with SVG &lt;pattern&gt; Attributes" src="//codepen.io/chriscoyier/embed/MYmbwv/?height=360&theme-id=light&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href='https://codepen.io/chriscoyier/pen/MYmbwv/'>Messing with SVG &lt;pattern&gt; Attributes</a> by Chris Coyier 
  (<a href='https://codepen.io/chriscoyier'>@chriscoyier</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

### 对比传统的css平铺

?CSS平铺缺点：
- 与位图一起使用时，它不可扩展
- 性能较低
- 更难以定制
- 仅限于矩形重复

?SVG模式专业人士：
- 轻量级
- 从CSS自定义
- 可扩展
- 能够创建复杂的模式

http://www.heropatterns.com/
