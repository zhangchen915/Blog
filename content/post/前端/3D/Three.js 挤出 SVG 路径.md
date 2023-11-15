---
title: "Three.js 挤出 SVG 路径"
categories: ["前端"]
tags: ["Three.js"]
draft: false
slug: "Three.js-svg-Extrude"
date: "2023-10-15 11:56:10"
---

导入SVGLoader `import { SVGLoader } from "three/examples/jsm/loaders/SVGLoader";`
 
SVGLoader扩展了基本Loader类，其中包含.parse()方法。 

```js
const svgMarkup = document.querySelector('svg').outerHTML;

const loader = new THREE.SVGLoader();
const svgData = loader.parse(svgMarkup);
```
为了得到我们可以挤出的形状，我们需要解析SVG。通过调用获取路径的数据.paths()方法。它将返回一个数组形状路径.每一个都有.toShapes(true)方法。
然后创建一个 ExtrudeGeometry 对象并传入 SVG 路径数据。

ExtrudeGeometry 是 Three.js 库中的一种三维图形工具，用于将二维图形沿着某个轴线进行拉伸，从而实现从二维到三维的转换。它可以将简单的形状，例如多边形、路径或点云，转换为具有深度和厚度的实体几何体。
ExtrudeGeometry 提供了许多可配置的选项来控制挤出的效果，包括顶点的数量、高度、偏移量、旋转角度等。此外，还可以通过添加多个路径或形状来创建复杂的几何体。
在使用 ExtrudeGeometry 时，通常需要配合着其他几何体和材质来进行渲染。它可以用于制作各种三维模型，例如建筑模型、机械部件、游戏角色等。它是一种非常实用的工具，可以帮助开发者快速地创建出逼真的三维场景。

```js
// Group that will contain all of our paths
const svgGroup = new THREE.Group();

const material = new THREE.MeshNormalMaterial();

// Loop through all of the parsed paths
svgData.paths.forEach((path, i) => {
  const shapes = path.toShapes(true);

  // Each path has array of shapes
  shapes.forEach((shape, j) => {
    // Finally we can take each shape and extrude it
    const geometry = new THREE.ExtrudeGeometry(shape, {
      depth: 20,
      bevelEnabled: false
    });

    // Create a mesh and add it to the group
    const mesh = new THREE.Mesh(geometry, material);

    svgGroup.add(mesh);
  });
});
```

<iframe height="300" style="width: 100%;" scrolling="no" title="Three.js SVG extruder" src="https://codepen.io/areknawo/embed/abWzjGO?default-tab=result&theme-id=light" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
  See the Pen <a href="https://codepen.io/areknawo/pen/abWzjGO">
  Three.js SVG extruder</a> by Arek Nawo (<a href="https://codepen.io/areknawo">@areknawo</a>)
  on <a href="https://codepen.io">CodePen</a>.
</iframe>
