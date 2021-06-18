---
title: "关于vps"
categories: ["默认分类"]
tags: []
draft: false
slug: "16"
date: "2015-12-02 15:49:00"
---

这个站目前是放在阿里云虚拟主机上运行的，虚拟主机说是方便，但是只能跑php，而且要是国内的话备案还要花不少功夫。所以一直有换成vps的打算，而且最近一直在用的ss越来越慢了，终于下决心还是自己租个vps，自己搭建ss，毕竟自己的就是放心，还能强迫自己学习Linux。租个vps要是只用来科学上网，那就太浪费了，作为原生前端当然不能满足于现在的php博客系统，node才是真爱。
所以，开始进阶学习Linux和node。我立志不让我的vps白租，一定要花完所有流量！
给大家分享一下我是如何搭建ss的吧。
如果你是windows用户，首先安装ssh命令，其是很多教程里用putty这个工具，但是据说这个中文版还存在后门，所以两种选择安装ssh或者使用putty英文版。
链接到服务器后，我用的是一键安装包，注意一行一行的执行哦

    wget --no-check-certificate https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-go.sh
    chmod +x shadowsocks-go.sh
    ./shadowsocks-go.sh 2>&1 | tee shadowsocks-go.log

这个是来自[秋水溢冰][1]，具体可以去看他的博客，此外还用其他版本的ss供你选择。

另外还可以安装锐速对你的vps加速，在执行下代码过程之前还要先去官网注册一个账号。

    wget http://my.serverspeeder.com/d/ls/serverSpeederInstaller.tar.gz
    tar xzvf serverSpeederInstaller.tar.gz
    bash serverSpeederInstaller.sh 

然后一路回车，是否全选是，就安装完成了。
很简单吧~

  [1]: https://teddysun.com/392.html
