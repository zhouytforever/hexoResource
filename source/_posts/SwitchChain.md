---
title: Switch的链式调用实现
date: 2018-11-10 17:17:00
tags: 
- 面试
- this
- 链式调用
categories:
- 面试
- Javascript
---

## 前言

前不久，面试了下某在线文档公司，前面都比较顺利。第二面的时候，面试官给了这样一个题目

```
switch(flag) {
  case 1: do some thing; break;
  case 2: do some thing; break;
  default: do default;
}

// 请实现可以链式调用的Switch
用法如 switch(flag).match(case, () => {}).match(case2, () => {}).default(() => {})
```

## 我的实现

当时他把他的Mac pro给了我，因我平时用Vim居多，也没用他的IDE。直接打开终端，上了vi

## 思路

```
// 毕竟最近总写functional，干脆给他来一个高级一点的吧，不用class也不用this那套
// 上代码，怎一个行云流水
const Switch = (aim) {
  let found = false
  return { // 注意，当时就想直接返回一个对象好了
    match: (a, cb) => {
      if(!found && a === aim) {
        found = true
        cb()
      }
      // 问题来了，怎么把这个对象传递下去，当时不知怎么了，居然还想炫技，想到用callee
      // 于是把Switch 方法的aguments.callee拿来了。又突然想到arguments不能在箭头函数中用。有点方，最后timeout。面试官收回了笔记本
    },
    default: (cb) => {
      if(!found) cb()
    },
  }
}
```

## 反思

悔不该如此装X，结果一不小心给自己挖个大坑。后来我想，干脆，给你来个多种实现方式

### ES5的类写法

```
function Switch (flag) {
  this.flag = flag
  this.found = false
}
Switch.prototype.match = function (arg, cb) {
  if (!this.found && arg === this.flag) {
    this.found = true
    cb()
  }
  // 当时的无法返回那个对象的问题就用this解决。
  // 可是js中的this是很突变（mutation）的
  // 能不用则尽量不用，要不函数式怎么那么亮
  return this 
}
Switch.prototype.default = function (cb) {
  if (!this.found) {
    cb()
  }
}
module.exports = Switch
```

### ES6的class写法

```
module.exports = class Switch {
  constructor (flag) {
    this.flag = flag
    this.found = false
  }
  match (a, cb) {
    if (!this.found && a === this.flag) {
      this.found = true
      cb()
    }
    return this
  }
  default (cb) {
    if (!this.found) {
      cb()
    }
  }
}
```

### Function写法

```
module.exports = (f) => {
  let flag = f
  let found = false
  let result = { 
    match: (a, cb) => {
      if (!found && flag === a) {
        found = true
        cb()
      }
      // 当时那个无法返回该对象的问题，就这样轻松解决
      return result 
    },
    default: (cb) => {
      if (!found) {
        cb()
      }
    }
  }
  return result
}
```
