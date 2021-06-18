---
title: "html-webpack-plugin 多页面应用打包"
categories: ["前端"]
tags: ["webpack"]
draft: false
slug: "381"
date: "2017-08-22 16:13:00"
---

有些时候我们并不想用框架，就是传统的多页面应用，而`html-webpack-plugin`对此的支持并不好，所以需要我们多做一些工作。

## 1.构造webpack的entry对象
首先`public/pages`下每个子目录的都包含一个js、html、scss文件，其中所有js文件都是入口，所以我们使`glob`模块，找到所有js文件的路径和名称，进而构造出webpack的`entry`对象。
```js
const glob  = require('glob');

let entry = function (){
    let entry = {};
    glob.sync('./public/pages/**/*.js').map(e=>{
        const name=e.split('/')[3].replace('.js','');
        if(name)entry[name]=e;
    });
    return entry;
}();
```

## 2.构造HtmlWebpackPlugin对象
由于`HtmlWebpackPlugin`不支持类似`[name]`的语法，所以我们必须为每个页面都构造一个`HtmlWebpackPlugin`对象。这里我们仍然利用entry对象获得文件名称和路径。最后我们把得到的结果合并到plugins数组中。
```js
let HtmlWebpack=[];
Object.entries(entry).forEach(e=> {
    if(e[0]!=='global'){
        HtmlWebpack.push(new HtmlWebpackPlugin({
            chunks: ['global', e[0]],
            filename: `${e[0]}.html`,
            template: e[1].replace('.js','.html')
        }));
    }
});

plugins:[].concat(HtmlWebpack);
```

此外html-webpack-plugin也能够解析ejs等模版文件，所以我们也可以把公共模块单独抽出来，然后以模版形式导入，便于维护。
