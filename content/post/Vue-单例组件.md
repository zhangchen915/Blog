---
title: "Vue 单例组件"
categories: ["前端"]
tags: []
draft: false
slug: "667"
date: "2019-09-05 01:35:00"
---

类似弹窗等组件我们希望是全局单例的，下面是 Vue 中的几种实现方式。

## Vue 实例
使用Vue.extend方法得到一个组件的实例，挂载之后插入到DOM中。

```js
var MyComponent = Vue.extend({
  template: '<div v-on:click="world">Hello {{msg}}!</div>',
  props: {
    msg: {
      type: String,
      required: true
    }
  }
  methods : {
    world : function() {
      console.log('world')
    }
  }
})
 
var component = new MyComponent(propsData: {msg: 'world'}).$mount()
document.getElementById('app').appendChild(component.$el)
```

传入prop需要在 new 组件时传入一个名为`propsData`的对象。组件内的`data`可以直接以`component.data['name']`这种形式修改。

Vue实例组件生命周期还有`$forceUpdate()`，`$nextTick()`和`$destroy()`方法。
注意：`$forceUpdate()`不会影响所有子组件，只影响实例本身和插入插槽内容的子组件。


## Vue 全局 mixin
```js
Vue.mixin({
	mounted:function(){
		if(this.$root===this){
			var ne=document.createElement("div");
			ne.id="placeholderGLOBAL";
			this.$el.appendChild(ne);
			var dp=Vue.component("global-components");
			var idp=new dp({parent:this,el:"#placeholderGLOBAL"});
			idp.$mount();
		}
	}
});
```

https://forum.vuejs.org/t/solved-component-singleton-render-only-once-reuse-dom-elements/17799/9
