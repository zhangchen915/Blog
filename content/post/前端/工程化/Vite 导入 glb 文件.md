---
title: "Vite 导入 glb 文件"
categories: ["前端"]
tags: ["工程化"]
draft: false
slug: "vite-glb"
date: "2023-10-13 15:00:10"
---

由于glb文件是二进制文件，所以需要在`vite.config.js`中配置`assetsInclude`选项，来指定需要解析的资源。

```js
defineConfig({
    base: process.env.VITE_BASE_URL,
    assetsInclude: ['**/*.glb'],
```

然后导入glb文件。

```js
import glb from 'xxx.glb
```

之后加载glb文件。

```js
const loader = new GLTFLoader()
loader.load(
   glb,
    (gltf) => {
        const scene = gltf.scene
        scene.scale.set(0.01, 0.01, 0.01)
        scene.position.set(0, 0, 0)
        scene.rotation.set(0, 0, 0)
    })
```

注意 ts 类型定义文件。

```ts
declare module '*.glb' {
  const src: string
  export default src
}
```

这样就可以在`vite`中使用glb文件了。
