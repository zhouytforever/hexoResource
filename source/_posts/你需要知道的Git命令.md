---
title: 你需要知道的Git命令
date: 2017-09-26 18:12:12
tags: 
- Git
categories: 工具
---
对于初来项目的同学可能对于git 操作有些不知所措，但是git 却那么重要。

## 莫怕英文message

尽量读懂每个遇到的命令行反馈

## 最常用的命令：git status

新手建议：每个操作之前先用一次status

## 单个分支

### 简单操作

- git add 
	- `git add .`  // 添加所有的
	- `git add ./src/view/***` // 添加某个目录下所有的
	- `git add ./index.js` // 添加某个具体文件
	- 可以不用add ，用`commit`加参数的形式，如`git commit -a[其他参数，如m]`
- git commit
	- 写好每个log message
		- good English，use it
		- poor English, try it more
		- 如果一次提交比较多，尽量不用 -m参数，而是直接用系统推荐编辑器编辑完整log
			- 一个完整的log如是说：首行概要，次行空着，余下细节，尽量编号
>如：
Fix bugs in opening MeasureLocale from RecordBusinessCircle  
===================这里是一个空行=========================
1\. Add flagName as param in opening MeasureLocale from RecordBusinessCircle  
2\. Adjust the switch of logs in FormPage  
3\. Remove the cacheCheck logic in FormPage instead of bringing flagName to MeasureLocale
	- —amend // 修改已经提交过的commit
	- -a `git commit -a`就是两个命令的集合：`git add .`+`git commit`略有不同的是git add .会把git 尚未追踪的文件加上, `git commit -a`不会
	- -m
- git pull [—rebase]
- [Fix all conflicts]
	- git add
	- git rebase —continue
	- 没有什么是一个continue解决不了的。如果有，那就两个
- git push

### 中级操作
- git stash
	- 情景1：别人有新代码，你想立刻拿来看，但你不想提交
	- 情景2：突然来了个支线任务，要你去打野，但你不想提交
	- 总结：任性的你就是不想提交，那么请用stash任性
	- 操作
		- git stash [save [message]]
		- git stash list
		- git stash pop
		- git stash drop
		- git stash apply
		- git stash clear
- git reset 
	- —soft
	- —hard
	- &lt;commit id&gt;
	- HEAD ^
	- HEAD~3
- git revert
	- 情景1：对于所有已经push的commit
	- 情景2：对于所有已经push的commit
- git checkout &lt;file&gt;
- git log
	- &lt;commit id&gt;
	- HEAD ^
	- HEAD~3
- git rebase -i

## 多个分支

### git branch [-a]

### git checkout &lt;branch name&gt;
- 切换已有分支
- -b 从某个分支新建一个分支

### git merge &lt;branch name&gt;

### git rebase &lt;branch name&gt;

### git cherry-pick
- 情景：在branch A里有一个commit ,想在branch B里应用
- 操作
	- git cherry-pick &lt;commit id&gt;
		- 036c69d47207fb200713010f512e7316422e12c6
	- git cherry-pick  -x &lt;commit id&gt;
	- git cherry-pick --continue

## 起死回生的仙丹

git reflog + git reset &lt;reflog short SHA&gt;

## 陷阱

### git 默认对文件名大小写不敏感
- 怎么破：`git config [—global] core.ignorecase false`

## 看男人

### man git

### git —help

### git XXX —help

### /word 搜索你想看的部分

## 更容易理解的脑图

![](/images/git-you-gotta-to-know.png "脑图git-you-gotta-to-know")
