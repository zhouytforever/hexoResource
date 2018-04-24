---
title: YDC踏坑之旅（三）Vue组件的引用方式
date: 2018-04-19 10:24:33
tags:
- Vue
- 组件引入
categories: Vue
---

##  前言

一次开会，YDC的技术评审和初用反馈。

## 会议内容

**A：** 每次要用一个组件的时候都要`import XXX from '@xxxx/YDC'`太麻烦了。
> `Vue.use()`可以解决每个业务view或者业务组件下需要单独引入外部组件的的问题
> [用法与描述](https://cn.vuejs.org/v2/api/#Vue-use-plugin)

**YDC主导开发：** 全局Vue.use使每个使用方组件多引入其他的components。

**老大：** 引入了其他多余的components没有必要。

**我（想）：** 为什么官方都推荐use使用插件呢？真的不好吗？

## 会议后的调查

**我：** 现在你在用YDC的时候，没有用全局use。是不是会产生很多`import XXX from '@xxxx/YDC'`呢？有没有很麻烦？

**B：** 是啊，每个业务页面都要写好多import。代码不优雅、写起来也很麻烦。

**我（想）：** 如果为了防止多引入东西而带来很多额外的使用困难，那这个组件库就是还有很大的优化空间的。
况且Vue.use真的会带来很大的影响吗？

实际止，在[awsome-vue](https://github.com/vuejs/awesome-vue)中有很多Plugin，这样的设计并不会对全局Vue产生性能的影响或者全局污染。因此为了写业务方便，善用Vue.use可以减少很多代码量，是一种有效提高效率的方式

那么，怎么写一个可以在Vue.use中生效的插件呢？

>[官网的示例](https://cn.vuejs.org/v2/guide/plugins.html)


