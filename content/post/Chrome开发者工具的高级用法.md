---
title: "Chrome开发者工具的高级用法"
categories: ["前端"]
tags: []
draft: false
slug: "Chrome开发者工具的高级用法"
date: "2016-01-31 21:30:00"
---

谷歌调试工具肯定都用过了，但是你可能还不知道下面的一切开发者工具的用法。
另外谷歌还有一个Chrome 金丝雀 版本的浏览器专门用于开发者调试，如果你还没听说过，那就先下载一个再来看吧~

一、遍历css
使用 Tab 键可以在 CSS 样式规则中进行遍历选定，选定的目标包括：选择器、属性和属性值。如果想跳回上一个目标，使用 Shift + Tab

二、数值调整快捷键

Up / Down，增加或减少 1 单位
Shift + Up / Down，增加或减少 10 单位
Alt + Up / Down，增加或减少 0.1 单位
鼠标滚轮

三、JS 文件打开和文件内的快速跳转

在 Sources 面板使用 Ctrl + O 快捷键打开搜索框
搜索框下会提示当前页面的涉及的 JS 文件，输入文件名即可打开
如果输入 :5:9，则表示跳转到文件的第五行第九个字符

四、条件断点
在代码的行号出点击右键，选择“Add conditional breakpoint”选项。

五、运行部分代码
在 JS 文件中选中一段代码，通过 Ctrl + Shift + E 可以在 Console 面板中运行这段代码。

六、多光标编辑
使用 Ctrl + Click 可以在文件中创建多个编辑点，使用 Ctrl + U 可以取消最后一处编辑点。

七、图片转Base64
在预览图片上右键选择 copy image as Data URI，可以将图片转换为 base64 编码。

八、矩形选择
按住Alt即可选择矩形区域内的代码。

九、展开所有子节点
选择DOM元素和在带有剪头的地点按住Alt +点击鼠标左键，可以展开所有子节点。

十、查看css媒体查询源码
点击左上角的手机图标，选择设备模式，然后在标尺的零点旁边有一个错开横线组成的按钮，点击打开("blue","green","orange")工具栏，在上面点击右键即可媒体查询源码。

十一、网络幻灯片
"Film Strip"显示页面从开始到结束渲染的截图。你可以点击截图和在timeline-style中查看视图。
选择“Network”面板，点击“录制”图标，然后重新加载页面。

十二、Device Emulation Sensors（模拟移动设备传感器）
选择“Emulation > Sensors”，其中可以模拟触摸效果，地理位置和加速计，点击“Esc”取消模拟。

十三、审查元素查看动态隐藏元素
这种情况可以先点右键，然后鼠标移到DevTools上，然后按按键盘的N键。

十四、全网页截图
ctrl+shift+P 调出命令菜单，输入Screenshot搜索到截图选项即可。

十五、Coverage
查看网页中哪些CSS和JS是未使用的，未使用的代码会有红色标记。

[Dev tips][1]这里还有更多技巧。

拓展阅读：
[Chrome 35个开发者工具的小技巧][2]
[如何更专业的使用Chrome开发者工具][3]


  [1]: https://umaar.com/dev-tips/
  [2]: http://www.w3cplus.com/tools/dev-tips.html
  [3]: http://www.w3cplus.com/tools/how-to-use-chrome-devtools-like-a-pro.html
