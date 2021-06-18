---
title: "【译】使用 WebStorm 调试 Nuxt.js"
categories: ["前端"]
tags: []
draft: false
slug: "609"
date: "2019-07-10 23:58:00"
---

你之前一直只用控制台调试吗？你是否对其输出的顺序头疼不已？用很长时间才发现丢失了对象？让我们面对现实...几乎每个人都不得不重复这种方法，包括我自己。

> `console.log` 不是且永远不是调试的银弹

调试器是一个伴随我们多年但因为种种原因人们不再Node世界中使用。我们来自NodeJS，VSCode和Jetbrains的朋友创建了大量工具，帮助我们“停止”应用程序，并在那个时刻获得应用程序的当前状态。NuxtJS，另一方面，尝试启动和运行调试器一直很痛苦，难以搞定，因此人们就倾向于放弃并开始使用console.log。

好吧，恐惧不是我的朋友。事实上我有一个快速，安全并且好用的方案来解决你所有的问题！其实，NuxtJS的调试比大家想象中的更容易，我想让你了解它，因为几乎没有关于这个主题的文档，希望这让你的生活更轻松。

## 项目配置
打开你的`nuxt.config.js`并转到build属性，因为我们要修改`extend`方法。

```js
extend(config, ctx) {
      // Added Line
      config.devtool = ctx.isClient ? 'eval-source-map' : 'inline-source-map'

      // Run ESLint on save
      if (ctx.isDev && ctx.isClient) {
        config.module.rules.push({
          enforce: 'pre',
          test: /\.(js|vue)$/,
          loader: 'eslint-loader',
          exclude: /(node_modules)/
        })
      }
}
```

这行什么意思？

`config.devtool`是Webpack的一个属性，它允许我们配置如何生成JS的SourceMap（[参考][1]）

`eval-source-map`是一个与行号完全匹配的SourceMap，这有助于我们在客户端进行调试。（[更多信息][2]）

`inline-source-map`与上一个非常相似，但有一个例外，即在bundle中添加为**DataUrl**。 它帮助我们在服务器中调试我们的NuxtJS应用程序。 （[更多信息][3]）

*注意：判断开发环境，不建议在生产中使用它。*

## 使用nodemon运行NodeJS调试器
我们将使用一个名为nodemon的优秀开发工具，它基本上让我们可以观察项目中的任何变化并自动重启服务器。

要使用nodemon运行NodeJS调试器，只需添加标志`--inspector`即可！

```json
{
  "scripts": {
    "dev": "nodemon --inspect node_modules/.bin/nuxt",
  }
}
```

## WebStorm

### 配置服务端
正常配置npm run dev项目即可

### 配置客户端
添加 `Javascript Debug`，URL应该是Nuxt将运行的URL，但我建议使用Chrome并启用“确保在加载脚本时检测到断点”选项并保存！

### 如何同时运行
首先以调试模式?运行服务端，项目正确加载后，选择客户端运行配置并单击相同的图标。它应该打开一个新的chrome实例。

现在我们完全准备好调试我们的程序了！?

原文地址：[Nuxt.js Debugging with WebStorm][4]


  [1]: https://webpack.js.org/configuration/devtool/
  [2]: https://webpack.js.org/configuration/devtool/#development
  [3]: https://webpack.js.org/configuration/devtool/#special-cases
  [4]: https://medium.com/@fernalvarez/nuxt-js-debugging-for-webstorm-9b4ef5415a5
