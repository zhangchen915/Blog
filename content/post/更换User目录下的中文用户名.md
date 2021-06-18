---
title: "更换User目录下的中文用户名"
categories: ["默认分类"]
tags: []
draft: false
slug: "102"
date: "2016-02-24 22:39:00"
---

win+x---计算机管理---本地用户和组---用户---右键administrator属性---去掉禁用选项。
启用并登陆Administrator这个帐号，把用户文件夹给改成英文，用户名可以之后去自己的微软账户改。
然后找到注册表HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\ProfileList\位置，在子目录的SID中，找到ProfileImagePath键，把值为`C:\Users\中文用户名` 的地方改为 `C:\Users\英文用户名`。
