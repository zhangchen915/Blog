---
title: "webp实践"
categories: ["前端"]
tags: ["性能优化"]
draft: false
slug: "webp实践"
date: "2016-10-04 20:48:00"
---

现在网站消耗流量最大的也就是图片了，而解决图片的体积过大的终极解决方案就是Webp图片了，虽然目前浏览器中只有chrome内核支持webp，但是考虑到chrome的用户量，我们也值得单独为chrome提供webp图片。

由于我们使用的是又拍云存储图片，所以最简单的办法就是使用**又拍云**提供的图片处理功能，直接在链接后加上 `!/format/webp`，又拍云就会提供转换后的图片。还有一个支持让浏览器支持webp的插件[webpjs][1]，但是这个插件性能很差，所以并不推荐使用。

所以我写了一个简单的指令替换图片的src属性

    app.directive('imgWebp', function () {
        return {
            restrict: 'AE',
            link: function ($scope, $element, $attrs) {
                $attrs.$observe('imgWebp', function (value) {
                    if (supportWebP) {
                        $element[0].setAttribute('src', value + '!/format/webp');
                    } else {
                        $element[0].setAttribute('src', value);
                    }
                });
            }
        };
    });

关键点在于如何检测浏览器对webp的支持，简单的方法可以直接根据浏览器的UV来判断，但是最稳妥的办法还是对浏览器做特征检测。

    var WebP = new Image();
            WebP.onload = WebP.onerror = function(){
                window.supportWebP = WebP.height == 1;
            };
    WebP.src = "data:image/webp;base64,UklGRiYAAABXRUJQVlA4IBoAAAAwAQCdASoBAAEAAAAMJaQAA3AA/v89WAAAAA==";

原理也很简单加载一个1×1的wenp图片，然后在加载完成时检测图盘的高度是否为1.

  [1]: http://webpjs.appspot.com
