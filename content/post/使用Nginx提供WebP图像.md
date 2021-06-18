---
title: "使用Nginx提供WebP图像"
categories: ["后端"]
tags: ["nginx"]
draft: false
slug: "362"
date: "2017-07-29 11:57:00"
---

用JS判断浏览器类型再替换图片链接终究不是一个好的选择，还是需要一个更底层的方法，本文就介绍了用Nginx提供WebP图像，当然你应该提前准备好相应webP图片。

**1.** 添加 `image/webp webp` 到 `/etc/nginx/mime.types` 从而让 Nginx 识别 MIME 类型。

**2.** 在 `/etc/nginx/nginx.conf` 中的 http 配置段添加如下：
```
map $http_accept $webp_suffix {
    default "";
    "~*webp" ".webp";
}
```

**3.** 这里以WordPress为例，添加如下规则到`/etc/nginx/sites-enabled/example.com`：
```
location ~* ^/wp-content/.+\.(png|jpe?g)$ {
    add_header Vary Accept;
    try_files $uri$webp_suffix $uri =404;
}
```

**原理：**
浏览器的每个请求头中都带有"Accept"字段,例如`Accept:image/webp,image/apng,image/*,*/*;q=0.8`；第二步中，我们通过$http_accept拿到请求头中的Accept，`~*webp`是正则，如果含有webp 那么 $webp_suffix 的值就为 `.webp`；第三部我们通过try_files 先给uri加上`.webp`后缀，如果没有找到则使用原有地址，最后如果都匹配不到返回404。

**参考文章：**
[Recipe: serve WebP with nginx conditionally][1]


  [1]: https://github.com/uhop/grunt-tight-sprite/wiki/Recipe:-serve-WebP-with-nginx-conditionally
