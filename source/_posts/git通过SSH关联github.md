---
title: git通过SSH关联github
date: 2016-03-11 23:36:51
tags:
- github
- SSH-key
categrories: git
---
今天本来想把博客弄好了，可是在git上遇到了问题，本机是ubuntu系统，虽然安装了git但是没有跟github关联。
幸好看到如下博文：[git与github在ubuntu下的使用](http://www.cnblogs.com/cocowool/archive/2010/10/19/1855616.html "小狼的世界")
我的操作步骤如下：
1.	git config --global user.name  "用户名或者用户ID"
	git config --global user.email  "邮箱"
	*<font color='red'>注：原文用的是形如 git config –global user.name = "用户名或者用户ID"</font>*
2.	因为github是通过ssh的方式访问资源库的，所以需要ssh-key（每次windows安装github客户端也都自动加上了ssh）
	使用命令：ssh-keygen -C 'you email address@gmail.com' -t rsa
	于是见到如下操作
![这是一张图片](/images/ubuntu-20160312.png "ssh-key命令截图")
	*说明:git-ssh-rsa是我自己输入进去的，下边还提示两遍输入密码。但是也可以直接回车，于是我就偷懒了。*
3.	见到那一串key我直接复制进浏览器github中的key里边，上边标题随便来一个还是不行。
	至此有的同志可能会问了，怎么看行与不行呢，命令：ssh -T git@github.com
4.	看了下github报错说明，意思是文件有可能复制了一半。。。我就纳闷了，哪来的复制文件呢？？
5.	再次看下此图，发现是保存了两个文件，但是保存在哪里了呢？
6.	linux有个强大的find命令。我用了如下命令 sudo find /home -name'git-ssh*' -ls
	命令参照：<http://man.linuxde.net/find>
7.	找到了，用vi打开后复制下来，回到浏览器github，里边的title真的就是个title，只有标记作用。
8.	添加成功之后，本来以为可以直接 git push了，可是还要创建仓库（不光在本地，也要有网端）
9.	在要创建仓库的路径下git init
10.	git add .		添加所有文件
11.	git commit -m '说明'		提交 
12.	*git remote add 本地仓库名（就是一个标识） git@github.com:github用户名/github上仓库名.git*
13.	在github上还要创建一个仓库，否则push的时候说此仓库not found
14.	这回总算可以push了	git push -u WebChartExpress master
15.	至此，一个git的仓库算是在本地上传上去了。

