---
title: YDC踏坑之旅（一）functional 组件
date: 2018-03-21 21:14:49
tags:
- Vue
- Element
- 组件
categories: Vue
---

看了这个标题，你可能会有疑问：什么是YDC
答：YDC是我们公司基于`Element`封装的自定义组件库+样式库。作为其开发成员之一，我亦步亦趋于大神之后，学习。学习。学习。

## 前言

在封装一个Vue组件的时候，可能需要这样一个组件：
- 只做分发，根据props中的属性来选择子组件
- 属性透传本身无状态

那么[函数式组件](https://cn.vuejs.org/v2/guide/render-function.html#%E5%87%BD%E6%95%B0%E5%BC%8F%E7%BB%84%E4%BB%B6)就是你的选择

## 例子

```js
Vue.component('my-component', {
  functional: true,
  // 为了弥补缺少的实例
  // 提供第二个参数作为上下文
  render: function (createElement, context) {
    // ...
  },
  // Props 可选
  props: {
    // ...
  }
})
```

## 限制

- 没有管理或者监听任何传递给他的状态
- 无法访问任何有状态组件的上下文属性
- 只能通过props接收父组件给的参数
- 没有自己的`data`
- 没有`this`
- 没有实例
- 没有生命周期
