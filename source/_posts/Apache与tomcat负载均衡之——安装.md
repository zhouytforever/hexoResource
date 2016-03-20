---
title: Apache与tomcat负载均衡之——安装
date: 2016-03-20 11:02:44
tags:
- Apache
- Tomcat
categories: Apache与tomcat
---
在做项目的时候遇到了系统反应慢，并发量大的时候就会产生事务阻塞的情况。此时除了tomcat和jvm调优动静分离以及数据库分表等措施以外就要考虑做负载均衡与tomcat集群了。

## 环境
- Windows（我用的是win10_x64当然其他的也差不多，只是下载安装包的时候注意选择就好）
- ApacheServer2.4.x
- Tomcat 7
- Jdk 1.8

## 下载Jdk
什么？ 不会？？ 那么我只能生气的说：只给你参考网址了：[官网下载页面](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)、[下载方法](http://jingyan.baidu.com/article/9989c746064d46f648ecfe9a.html)

## 下载tomcat
这只小猫对搞java ee开发的程序员来说真心太熟悉了，只要去[官网](https://tomcat.apache.org/download-70.cgi)下载对应系统的包，解压就能用。

## 下载Apache
1.	[Apache官网](http://httpd.apache.org/download.cgi)。他的windows版不在首页，需要选择下边的“Files for Microsoft Windows”连接。
2.	选择第一项ApacheHaus（这是个第三方下载平台，在它的网站下载独立的Apache会是一个压缩包。另外四个中，第二个也是独立的Apache下载地址，另外三个是集成开发环境。）。
3.	在新的界面中，会发现VC9和VC11字样，通过阅读相关内容得知，VC9是指用VS2008编译的代码，而VC11是用VS2012编译的，而用VS2012编译的无法在windows xp和server 2003中使用。算是为了兼容性好点吧，我选的用VC9编译的64位Apache，选择对应系统的下载。至此，下载过程完成！

*参照[Apache服务器最新版下载、安装及配置（win版）](http://jingyan.baidu.com/article/d8072ac47baf0eec95cefdca.html)中的“Apache下载”小节*

## 安装（配置）jdk
环境变量JAVA_HOME、CLASSPATH、Path

## 配置tomcat
tomcat的环境变量目前来看一共四种情况：
1.	需要配置环境变量Path、CATALINA_HOME、CATALINA_BASE
	老版本的tomcat是需要配置，但是新版本的tomcat就可以不用配置环境变量了（前提是JAVA_HOME得正确配置）。至于是哪个新版本，网上有说是4以后就不用了，也有说是6的。不过目前来看使用tomcat至少也是6了吧！
2.	需要配置环境变量Path、CATALINA_HOME
	需要在终端可以直接通过`startup`命令运行的服务器，且一台机器只有一个tomcat
3.	不需要配置环境变量
	作为开发环境，且tomcat版本不太古董
4.	不需要配置环境变量
	作为一个集群服务器且一台机器上有多个tomcat

ps：我是把已经有的开发环境的tomcat复制了两份。所以在我的电脑中有三个tomcat，路径如下：
- D:\Program Files\apache-tomcat-7.0.67
- D:\Program Files\apache-tomcat-7.0.67-1
- D:\Program Files\apache-tomcat-7.0.67-2
- 我且暂时把它们三个记为A、B、C。
- 环境变量里的路径是A（生产环境）。B和C是我要做负载均衡用的。

我所遇到的问题：
我已经修改了B和C的server.xml配置文件，保证已经没有端口冲突了，但是只可以单独启动一个B或者C（我都是在各自的bin目录下双击startup.bat），当要启动第二个的时候就会弹出tomcat的起动黑窗口，然后明显看到里边有报错，但是它不会停下让我看清是什么错误就闪退了。我这个郁闷，查看两个tomcat目录里边的东西都是正确的，没有别的可以配置的了。

在我痛苦的百度一番后依然无果，冥思苦想，在一个不经意间想到了启动文件startup.bat。打开一看，里边确实有那么一段代码是查找环境变量的，根据环境变量启动相应的tomcat，于是我猜想可能是两个tomcat运行的都是一个程序：A。两次运行B的startup.bat，dos窗口中出现了一些路径，一看还真是，D:\Program Files\apache-tomcat-7.0.67。
于是，我把关于tomcat的所有环境变量去掉，再来一遍，两个tomcat   B和C正常并行。

*至后来人，少绕路。另外，搞技术的人能用google是最好了，尽量不要用百度了。虽然上美国的google需要翻墙，但是搜索引擎不光只有百度和google啊，我们中国的老大哥的搜索引擎也被墙了吗？？你试试就知道了。不知道？？在我的浏览器书签里有那么一个网址[俄罗斯最大的搜索引擎](https://www.yandex.ru/)。虽然首页的文字几乎不懂，但是有两个还是明白的，一个输入框，一个搜索按钮。。。。。。*

## 配置Apache
我把刚刚下载来的Apache解压到了D:\Program Files下，所以它的路径是D:\Program Files\Apache24
1.	找到D:\Program Files\Apache24\conf\httpd.conf，用记事本编辑
2.	找到：Define SRVROOT 这一项，将其右方的值改为D:\Program Files\Apache24
3.	继续找，找到：Listene 80，若你的80端口被占用（可在cmd下用命令netstat -a查看），则将80端口改为别的
4.	保存httpd.conf文件
5.	接下来需要配置安装Apache的主服务，有了它，Apache才可启动：打开CMD窗口，输入：`"D:\application_software\Apache\bin\httpd.exe" -k install -n apache`切记，包含引号。该命令的意思是，安装apache服务，并将该服务名称命名为apache(你也可以改成别的)，回车。
6.	服务安装完毕，完毕后，会自动测试，若有问题，窗口会提示错误，此时，请根据错误自行排查。正常安装完毕如下图所示：其中，Errors reported here must be corrected before the service can be started.意思是，若该句话后面有错误信息，则表示服务安装失败，需要先改正错误。若没有，则成功。
![图片](/images/20160320apache_service.png "apache_service参照图")
7.	在安装目录中，找到D:\Program Files\Apache24\bin\ApacheMonitor.exe可执行文件，双击运行，桌面右下角会出现图标，双击打开窗口界面，会看到如图所示：
![图片](/images/QQ截图20160320145500.png "apache_service参照图2")
8.	点击右侧start，启动apache服务。补充句，从该界面可看出，其可以手动控制服务的开启与关闭，为了节省资源，关闭Apache服务器的时候，请先点击“Stop”关闭apache服务。当然，该服务也可以在windows系统服务中关闭（建议设置成手动）下图显示的是该服务成功显示的状态：
![图片](/images/QQ截图20160320145713.png "apache_service参照图3")
9.	打开浏览器，输入访问http://localhost 若出现如下图所示界面，则Apache服务器的基本配置完毕，此时apache服务器已经可以运行。
![图片](/images/20160320apache_service3.png "apache_service参照图2")

*参照[Apache服务器最新版下载、安装及配置（win版）](http://jingyan.baidu.com/article/d8072ac47baf0eec95cefdca.html)中的“配置过程”小节*

*ps：此时的运行环境只是单独的Apache，下面进入重中之重，参见Apache与tomcat负载均衡之——负载*

