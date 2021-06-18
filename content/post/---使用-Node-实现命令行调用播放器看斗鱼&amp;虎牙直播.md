---
title: "📺 使用 Node 实现命令行调用播放器看斗鱼&amp;虎牙直播"
categories: ["默认分类"]
tags: ["折腾"]
draft: false
slug: "870"
date: "2020-11-05 12:59:00"
---

仓库地址 
https://github.com/zhangchen915/live-url

### 斗鱼
斗鱼的解析
```js
async function getUrl(roomId = 3484) {
	try	{
		const res = await fetch(`https://www.douyu.com/${roomId}`)
		const body = await res.text();
		const id =body.match(/live\/(\S*)_/)[1]
		return `http://tx2play1.douyucdn.cn/live/${id}.flv?uuid=`
	} catch (e) {
	}
}
```

### 调起 PotPlayer

PotPlayer播放器所在路径通常如 `C:\\Program Files\\DAUM\\PotPlayer\\PotPlayerMini64.exe`，通过 exe 加入真实直播 url 参数即可播放直播。

```js
const player = cp.spawn(PotPlayer, [url],{
		detached: true,
		stdio: 'ignore'
	});
	player.unref();
```
