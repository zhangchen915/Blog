---
title: "虚拟机与Docker 镜像、容器"
categories: ["后端"]
tags: ["Docker"]
draft: false
slug: "343"
date: "2017-07-05 21:24:00"
---

## Docker vs 虚拟机
虚拟机在软件层面上通过模拟硬件的输入和输出，让虚拟机的操作系统得以运行在没有物理硬件的环境中。
而docker和宿主机共享kernel，只需要搬运工kernel的输入输出。
所以虚拟机启动需要几十秒，而Docker容器可以在数毫秒内启动。

## 容器与镜像
容器（container）的定义和镜像（image）几乎一模一样，也是一堆层的统一视角，唯一区别在于容器的最上面那一层是可读可写的。

**容器 = 镜像 + 读写层。并且容器的定义并没有提及是否要运行容器。**
![imgserver.png][1]

## 构建镜像
1. docker commit
可以想象为git，创建一个容器，在容器中做一些修改，最后提交成为一个新的容器。

2. Dockerfile
Dockerfile的第一条指令必须是FORM
通过`docker build`执行Dockerfile中的全部命令。

推荐使用Dockerfile构建，相比第一种方式更加灵活。

## 从镜像启动容器
`docker run`
> docker run命令类似于git pull命令。git pull命令就是git fetch 和 git merge两个命令的组合，同样的，docker run就是docker create和docker start两个命令的组合。

**参考文献：**
[10张图带你深入理解Docker容器和镜像][2]


  [1]: https://img.zhangchen915.com/2017/07/121005378.png
  [2]: http://10%E5%BC%A0%E5%9B%BE%E5%B8%A6%E4%BD%A0%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3Docker%E5%AE%B9%E5%99%A8%E5%92%8C%E9%95%9C%E5%83%8F
