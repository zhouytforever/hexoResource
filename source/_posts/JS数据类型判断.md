---
title: JS数据类型判断
date: 2018-03-22 10:37:06
tags:
- JS
- 技巧
- 数据类型判断
categories: JS
---


数据类型判断有多种方式，各有自己的适用情景

## typeof

用于区分基本类型，结果可能为：
- 'number'
- 'boolean'
- 'string'
- 'undefined'
- 'object'
- 'function'

> 注意：
> - typeof null === 'object'，因为null被认为是一个空的对象引用。
> - typeof /正则/ === 'object'，但是Safari<=5和Chrome<=7下是'function'

## instanceof

instanceof 运算符用来测试一个对象在其原型链中是否存在一个构造函数的 prototype 属性。

```js
object instanceof constructor
```

> instanceof 运算符用来检测 constructor.prototype 是否存在于参数 object 的原型链上。

## Object.prototype.toString

`toString()`是`Object`的原型方法，调用该方法，默认返回当前对象的`[[Class]] `。这是一个内部属性，其格式为`[object Xxx]`，其中`Xxx`就是对象的类型。

对于`Object`对象，直接调用`toString()`就能返回`[object Object]`。而对于其他对象，则需要通过`call / apply`来调用才能返回正确的类型信息。

**一个对应表:**

```js
Object.prototype.toString.call('');// [object String]

Object.prototype.toString.call(1);// [object Number]
Object.prototype.toString.call(NaN);// [object Number]
Object.prototype.toString.call(Infinity);// [object Number]
Object.prototype.toString.call(Number.NEGATIVE_INFINITY);// [object Number]
Object.prototype.toString.call(Number.POSITIVE_INFINITY);// [object Number]

Object.prototype.toString.call(true);// [object Boolean]
Object.prototype.toString.call(Symbol());// [object Symbol]
Object.prototype.toString.call(undefined);// [object Undefined]

Object.prototype.toString.call(new RegExp());// [object RegExp]
Object.prototype.toString.call(/a/);// [object RegExp]

Object.prototype.toString.call(null);// [object Null]
Object.prototype.toString.call(new Function());// [object Function]
Object.prototype.toString.call(new Date());// [object Date]

Object.prototype.toString.call([]);// [object Array]
Object.prototype.toString.call(new Error());// [object Error]
Object.prototype.toString.call(document);// [object HTMLDocument]
Object.prototype.toString.call(window);// [object global] window是全局对象 global 的引用
```
