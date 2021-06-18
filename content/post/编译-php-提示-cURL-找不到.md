---
title: "编译 php 提示 cURL 找不到"
categories: ["后端"]
tags: ["php"]
draft: false
slug: "573"
date: "2018-07-24 19:03:00"
---

**问题：**
确定机器已经安装curl，而且通过`--with-curl=`指定路径也不管用。错误信息如下：
```
checking for cURL 7.10.5 or greater... configure: error: cURL version 7.10.5 or later is required to compile php with cURL support
```

**解决方法：**
这些都不允许您在启用cURL的情况下编译PHP。

要使用cURL进行编译，需要libcurl头文件（.h文件）。 它们通常位于`/usr/include/curl`中。 它们通常捆绑在一个单独的开发包中。

例如，在Ubuntu中安装`libcurl`：

`sudo apt-get install libcurl4-gnutls-dev`
或CentOS：

`sudo yum install curl-devel`
然后你可以这样做：

`./configure --with-curl ＃其他选项......`
如果手动编译cURL，则可以指定不带lib或include后缀的文件的路径。 （例如：/usr/local如果cURL标题位于`/usr/local/include/curl`中）。

原文地址：[Compiling php with curl, where is curl installed?][1]


  [1]: https://stackoverflow.com/questions/4976971/compiling-php-with-curl-where-is-curl-installed
