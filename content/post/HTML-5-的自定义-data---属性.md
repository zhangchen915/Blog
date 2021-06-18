---
title: "HTML 5 的自定义 data-* 属性"
categories: ["默认分类"]
tags: []
draft: false
slug: "97"
date: "2016-02-14 14:15:00"
---

使用 data-* 属性保存数据
----------------

    <div data-key="value"></div>  //一般数据
    <div data-JSON='{"game":"on"}'></div>

**1.使用原生 JS 操作 data-* 属性**

    <div id = "data" key = "value" ></div>
    <script>

    // 使用getAttribute获取 data- 属性
    var data = document.getElementById ( 'data' ) ;
    var key = data.getAttribute ( 'key' ) ;
    // 使用setAttribute设置 data- 属性
    user.setAttribute ( 'key' , 'new_value' ) ;
    
    //使用 dataset
    var DOMStringMap = el.datase //返回DOMStringMap对象
    key = el.dataset.key;
    el.dataset.value = null;  //删除 data-*属性
    </script>
注意:最新的 dataset 属性只支持IE11+，所以还是老老实实用 getAttribute 吧。

**2.使用 JQuery 操作 data-* 属性**

    $().data('key');  //读取
    $().data(key,value);  //赋值

因为"data-*"是HTML5才出现的属性，所以在非HTML5的页面或浏览器里可以使用.data(obj)方法来操作。

注意：data- 属性名如果包含了连字符，例如：data-date-of-birth ，连字符将被去掉，并转换为驼峰式的命名，前面的属性名转换后应该是： dateOfBirth
