---
title: "使用 Jest 对 Nuxt 应用进行单元测试"
categories: ["前端"]
tags: ["单元测试","Jest"]
draft: false
slug: "799"
date: "2020-04-12 16:34:00"
---

### 配置 `jest.config.js`
```js
module.exports = {
  // tell Jest to handle `*.vue` files
  moduleFileExtensions: ["js", "json", "vue"],
  watchman: false,
  moduleNameMapper: {
    "^~/(.*)$": "<rootDir>/$1",
    "^~~/(.*)$": "<rootDir>/$1",
    "^@/(.*)$": "<rootDir>/$1"
  },
  transform: {
    // process js with `babel-jest`
    "^.+\\.js$": "<rootDir>/node_modules/babel-jest",
    // process `*.vue` files with `vue-jest`
    ".*\\.(vue)$": "<rootDir>/node_modules/vue-jest"
  },
  snapshotSerializers: ["<rootDir>/node_modules/jest-serializer-vue"],
  collectCoverage: true,
  collectCoverageFrom: [
    "<rootDir>/components/**/*.vue",
    "<rootDir>/pages/*.vue"
  ]
};
```

### 配置 Babel config
```js
function isBabelLoader(caller) {
  return caller && caller.name === "babel-loader";
}

module.exports = function(api) {
  if (api.env("test") && !api.caller(isBabelLoader)) {
    return {
      presets: [
        [
          "@babel/preset-env",
          {
            targets: {
              node: "current"
            }
          }
        ]
      ]
    };
  }
  return {};
};
```

### 添加测试脚本到 package.json

`"test": "jest --colors --verbose"`

因为Jest 的测试文件中的方法是全局的，所以一般编辑器不会给提示，如果想要编辑器有提示的话可以安装 `@types/jest`

#### 参考文章
https://medium.com/@gogl.alex/nuxt-jest-setup-from-scratch-8905d3880daa
https://medium.com/@brandonaaskov/how-to-test-nuxt-stores-with-jest-9a5d55d54b28
 
