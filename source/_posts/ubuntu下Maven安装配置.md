---
title: Ubuntu下Maven安装配置
date: 2016-03-13 12:49:51
tags:
- Maven
- Ubuntu
categories: Maven
---

## 下载
1.	进入Maven的官网下载最新bin安装包：[Maven官网](https://maven.apache.org/configure.html "链接") [Maven官方下载页面](https://maven.apache.org/download.cgi "下载")

2.	在下载页面选择 	*apache-maven-XX.XX.XX-bin.tar.gz* 
	*<font color='red'>注意：一定要是Binary archive 不能是Source archive，也就是下载人家编译好的包</font>*
3.	解压到一个指定目录下面 tar zxvf apache-maven-XX.XX.XX-bin.tar.gz -C /usr/maven(我指定的是/usr/maven目录)

## 配置环境变量
1.	sudo gedit /etc/profile编辑环境变量文件
2.	在文件末尾加入
	export M2_HOME=/usr/maven/apache-maven-3.3.9（我的路径 /usr/maven/apache-maven-3.3.9）
	export PATH=$PATH:$M2_HOME/bin
3.	（可选）配置本地仓库位置
	ps:如果不配置本地仓库会把 ~/.m2文件夹（~ 代表操作系统的当前用户目录）作为默认本地仓库
	a. 还是回到环境变量编辑页面，接着刚才后面的位置加入
		export M2_REPO=/usr/mavenRepository（我的路径 /usr/mavenRepository）
	b. 配置settings.xml
		打开M2_HOME目录 然后cd conf  (我的路径 /usr/maven/apache-maven-3.3.9/conf)
	c. sudo gedit settings.xml 在 <settions>标签下加入<localRepository>/usr/mavenRepository</localRepository> （我的路径M2_REPO指定位置/usr/mavenRepository）,存档。
4.	注销并重新登录进来，以使环境变量生效
5.	打开终端，输入mvn -v此时可以看到如下输出
	Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
	Maven home: /usr/maven/apache-maven-3.3.9
	Java version: 1.7.0_95, vendor: Oracle Corporation
	Java home: /usr/lib/jvm/java-7-openjdk-amd64/jre
	Default locale: zh_CN, platform encoding: UTF-8
	OS name: "linux", version: "3.16.0-62-generic", arch: "amd64", family: "unix"
	那么，恭喜了,你配置完成了!

## 初始化本地仓库 
打开终端，输入sudo -i (切换到root)
mvn help:system 
然后就是
。
。
。
。
。
等
*<font color='gray'>后话：mvn help:system的作用 1.打印环境 2.（如果还是处）把远程仓库的包目录结构和一些基本的包加载到本地。</font>*


