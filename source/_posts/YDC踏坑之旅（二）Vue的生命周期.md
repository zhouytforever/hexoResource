---
title: YDC踏坑之旅（二）Vue render的深刻体会
date: 2018-03-21 22:04:04
tags:
- Vue
- 生命周期
- nextTick
categories: Vue
---

## 前言

开发一个Dynamic-tags组件

- 初始有个默认高度
- 如果在容器中包含很多tag，需要将容器缩回到默认高度
- 每个tag长短不一
- 容器只是block的div
- 宽度可以固定
- 当容器太高时，展示【缩回】按钮

## 思路

1. 让组件加载，默认不缩回
2. 在`mounted` 方法中去计算容器高度，若高于默认高度，则设置【缩回】按钮显示
3. 加载完成后，`watch` `value` 属性（因为是动态标签云）。每次触发`value`变动都会重新计算，若高于默认高度，则设置【缩回】按钮显示
4. 但是在计算的方法中需要先设置容器不缩回，且要拿其高度。

## 那么问题来了

设置了不缩回，就会触发render。怎么保证render之后会立即重新计算呢？

## 解

1. 开始，我用了setTimeout，可是render本身就是异步的。无法保证render完再执行setTimeout回调，会有抖动现象
2. 问了身边的大神，他说：“`Element`中`textarea`的高度设置有相关写法”
3. 于是拿来`Element`的源码看了起来、其中一个亮点就是nextTick()方法

## 什么是`nextTick`？

[Vue官方文档](https://cn.vuejs.org/v2/api/#Vue-nextTick)给了这么一个解释

> 在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

官方例子：

```js
// 修改数据
vm.msg = 'Hello'
// DOM 还没有更新
Vue.nextTick(function () {
  // DOM 更新了
})

// 作为一个 Promise 使用 (2.1.0 起新增，详见接下来的提示)
Vue.nextTick()
  .then(function () {
    // DOM 更新了
  })
```

而无论是哪种形式，回调的参数就是一个VueComponent：一个当前组件的实例

我的用法：

```js
calculateExtensible() {
  this.expended = true; //在此触发render
  this.$nextTick().then(() => { //这里已经是render之后的组件了
  	if (this.readonly) { 
      this.extensible = this.$refs.tag_read.clientHeight > 72; // 72是容器默认高度
      if (this.extensible) {
        this.expended = false;
      }
    } else {
      this.extensible = this.$refs.tag_window.clientHeight > 72;
      if (this.extensible) {
        this.expended = true;
      }
    }
  });
},
```

## 总结

Vue中设置一个属性后，会触发更新。如果要根据更新后的dom属性触发新的更新，需要用到nextTick
