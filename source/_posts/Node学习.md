---
title: Node学习
date: 2015-09-03 12:37:41
tags:
- NodeJS包
categories: Node
---
## exports
不可以通过对exports直接赋值代替对module.exports赋值。exports实际上只是一个和module.exports指向同一个对象的变量，它本身
会在模块执行结束后释放，但module 不会，因此只能通过指定module.exports 来改变访问接口

### 被调用文件
``` bash
/**
 * HelloObject.js
 */
function Hello(){
    var name;
    this.setName = function (na){
        name = na;
    }
    this.sayHello = function(){
        console.log('Hello '+name);
    }
};

module.exports = Hello; //替换exports.Hello = Hello;
```
### 调用文件
``` bash
/**
 * testHelloObject.js
 */

var Hello = require('./HelloObject');

var hello = new Hello();//此处需要new 一个女朋友了

hello.setName('Honey');//要给女朋友设置名字，并问好
hello.sayHello();
```
## Node.js包的规范
-    package.json必须在包的顶层目录下
-    二进制文件应该在bin目录下
-    JavaScript代码应该在lib目录下
-    文档应该在doc目录下
-    单元测试应该在test目录下

