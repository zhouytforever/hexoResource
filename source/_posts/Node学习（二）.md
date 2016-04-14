---
title: Node学习（二）
date: 2015-11-10 16:22:20
tags:
- NodeJS包
categories: Node
---
## package.json
package.json或main字段不存在，会尝试寻找`index.js`或`index.node`作为包的接口
## package.json 规范：
1. name：包的名称，必须是唯一的，由小写英文字母、数字和下划线组成，不能包含空格
2. description：包的简要说明
3. version：符合语义化版本识别①规范的版本字符串
4. keywords：关键字数组，通常用于搜索
5. maintainers：维护者数组，每个元素要包含name、email（可选）、web（可选）字段
6. contributors：贡献者数组，格式与maintainers相同。包的作者应该是贡献者数组的第一个元素
7. bugs：提交bug的地址，可以是网址或者电子邮件地址
8. licenses：许可证数组，每个元素要包含type（许可证的名称）和url（链接到许可证文本的地址）字段
9. repositories：仓库托管地址数组，每个元素要包含type （仓库的类型，如git）、url（仓库的地址）和path（相对于仓库的路径，可选）字段
10. dependencies：包的依赖，一个关联数组，由包名称和版本号组成。
## 下面是一个完全符合 CommonJS 规范的 package.json 示例
``` {bash}
{
  "name": "mypackage", 
  "description": "Sample package for CommonJS. This package demonstrates the required 
      elements of a CommonJS package.", 
  "version": "0.7.0", 
  "keywords": [ 
     "package", 
     "example"  
  ], 
  "maintainers": [ 
     { 
        "name": "Bill Smith", 
        "email": "bills@example.com", 
     }  
  ], 
  "contributors": [ 
     { 
        "name": "BYVoid", 
        "web": "http://www.byvoid.com/"  
     }  
  ], 
  "bugs": { 
     "mail": "dev@example.com", 
     "web": "http://www.example.com/bugs"  
  }, 
  "licenses": [ 
     { 
        "type": "GPLv2", 
        "url": "http://www.example.org/licenses/gpl.html"  
     }  
  ], 
  "repositories": [ 
     { 
        "type": "git", 
        "url": "http://github.com/BYVoid/mypackage.git"  
     }  
  ], 
  "dependencies": { 
     "webkit": "1.2", 
     "ssl": { 
        "gnutls": ["1.0", "2.0"], 
        "openssl": "0.9.8"  
     }  
  } 
}
```
## 本地模式和全局模式的特点
![这是一张图片](/images/QQ20151110205310.png "本地模式和全局模式的特点参照图")
## nmp link
看完本地模式和全局模式的特点会有疑问：那么怎么在本地require全局包呢？
在linux下有了答案。。。。。。但此命令windows下不可用。
## 包的发布
``` {bash}
$ npm init
```
此命令会产生一个“Is this ok? (yes) yes”的交互，以及附带的package.json中的对应数据（很人发的哦）
之后在package.json的同级目录中创建一个index.js作为包的接口。这样，一个简单的包就完成了。
后续工作：

1. npm adduser：根据提示创建账号以便维护包，也可以全球同步（npm whoami可以用来检测是否取得了账号）
2. npm publish：在package.json所在目录下运行。稍等片刻，发布即完成。
3. 哦，发布完后是可以看的点击我，查看你
4. 改 version 重 publish：这样就完成了版本的更新
5. npm unpublish：做包是可逆的，也可以卸载的
本书阅读到了P54，明天继续。。。应该到了调试了，带着期待进入梦乡……
