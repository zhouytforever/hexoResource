---
title: svn异常：svn cleanup failed–previous operation has not finished; run cleanup if it was interrupted
tags: 
- svn
- 杂项
date: 2015-05-28 09:46:00
---

今天svn同步时，

<div>出现异常：</div>
<div><span style="color: #ff0000;">svn cleanup failed&ndash;previous operation has not finished; run cleanup if it was interrupted</span></div>
<div>百度后，大多解决办法：</div>
<div>team--&gt; Refresh/Cleanup</div>
<div>但是这种方法貌似也不可用，也会出错的。</div>
<div>还好用另外一种更加直接的办法。</div>
<div>＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝＝</div>
<div>

解决方法：清空svn的队列

1.下载<span style="color: #0000ff;">[<span style="color: #0000ff;">sqlite3.exe</span>](http://pan.baidu.com/s/1gdjb9U7 "sqlite3.exe")</span>

2.找到你项目的.svn文件，查看是否存在wc.db。如果存在，那么执行下边的步骤

3.将sqlite3.exe放到.svn的同级目录

4.启动cmd打开sqlite3.exe所在目录，执行sqlite3 .svn/wc.db&nbsp;"select * from work_queue"【回车】（注意，不用加分号）

5.看到很多记录，下一步执行sqlite3 .svn/wc.db&nbsp;"delete from work_queue"

6.ok了，现在在到项目里面，执行cleanup，完全没问题了，图标状态也已经恢复了。

&nbsp;

说的不够直接，上图：

![](http://images0.cnblogs.com/blog2015/620450/201505/280943216106462.png)

当然，这个方法不是出自于我。来源：http://blog.csdn.net/luojian520025/article/details/22196865

我只是将其说的更加详细一点。

&nbsp;

</div>