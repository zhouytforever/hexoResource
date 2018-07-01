---
title: YDC踏坑之旅（五）markdonw-loader
date: 2018-07-01 21:37:57
tags:
- Vue
- webpack
- markdown
- loader
categories: Vue
---

时隔写markdown-loader已经三个月了，现在回想起来仍是记忆犹新。。。

## 背景

YDC开发进程已经到了文档阶段，需要制作一个官网给用户（小白鼠）参考我们写的组件用法。

根据已有的现成例子：antdesign、element等，我们需要在官网上注入demo代码及其render之后的组件样子。
那么问题来了：需要一个工具解析出我们的代码，并输出为可执行的js语句。
于是就有了我们自己的markdown-loader……

## 思路

1. 参考已有markdown-loader的代码
2. 根据我们需要的输出写解析过程
3. 组装出原markdown解析出来的HTML讲法树（AST）和可执行的es5语句

## 实现

- 在各种开源工具中摸索、查找，终于确定用markdown-it来做markdown的AST解析工具，原因如下：
  1. 其下载量大，评分高
  2. 有正在维护的[文档](https://markdown-it.github.io/markdown-it/#/)
  3. 文档虽然英文，但易读、结构清晰
- webpack-loader是什么
  1. 输入：文件内容string或者buffer
  2. 输出：经过处理的文件内容string或者buffer
  3. 串行调用
- 只差真正的实现了

## 问题

1. 我实现了一版，但按照下游同事的说法（参考antdesign：一个loader出多个子demo.md）却不是他要的，
他要的是一个loader出一个子demo.md。这里引申一个开发原则：接口事先要明确。
2. 好吧，我重写。输出后他看了下，说：这个AST不是他要的。又是接口没有事先明确。

## 后记

1. 模块（或者软件）开发原则：一定一定要先定好接口，并且是经过深思熟虑过的有扩展性的。
