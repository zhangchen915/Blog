---
title: "git push -f 的找回"
categories: ["默认分类"]
tags: []
draft: false
slug: "694"
date: "2019-11-01 13:29:00"
---

首先使用 Github’s Events API 来拿到丢失提交的 SHA。如果是 Private 项目需要配置 token 等。
`curl https://api.github.com/repos/<user>/<repo>/events`

然后用 Github’s Refs API 用这个提交的 SHA 新建一个 recover 分支.

`curl -i -H "Accept: application/json" -H "Content-Type: application/json" -X POST -d '{"ref":"refs/heads/recover", "sha":"<orphan-commit-id>"}' https://api.github.com/repos/<user>/<repo>/git/refs`

这一步如果遇到 `"message": "Not Found"` 错误，则需要到` Developer settings >  Personal access tokens > 勾选repo` 生成一个 token，然后用查询字符串拼在URL后面 `?access_token=`

返回 201 则表示成功， pull一下就会出现 recover 分支。

参考文章：https://stackoverflow.com/questions/10098095/git-can-i-view-the-reflog-of-a-remote/35273807#35273807

