---
title: Apache与tomcat负载均衡之——负载
date: 2016-03-20 15:13:13
tags:
- Apache
- Tomcat
categories: Apache与tomcat
---
上篇文章说到Apache与tomcat负载均衡（集群），下面我们来配置一下。
坑爹的百度，每次搜索“Apache与tomcat集群”都是出来那几个国内比较烂的网址或者博客，看了诸多文章总结了以下几点：
1.	Apache与tomcat做负载主要有三种方式jk、ajp_proxy、http_proxy
2.	jk用到mod_jk.so，此模块在Apache中一般没有，需要单独下载并配置，过时了，且麻烦
3.	ajp_proxy是Apache公司针对其服务器研发的基于ajp定向包协议的代理模块
4.	AJP是定向包协议。因为性能原因，使用二进制格式来传输可读性文本。WEB服务器通过TCP连接 和SERVLET容器 连接。为了减少进程生成socket的花费

本文使用mod_proxy_ajp的方式
## 配置Apache
1.	修改Apache配置文件（Apache24\conf\httpd.conf），加载以下模块
```{bash}
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_ajp_module modules/mod_proxy_ajp.so
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so
LoadModule proxy_connect_module modules/mod_proxy_connect.so
LoadModule proxy_http_module modules/mod_proxy_http.so
LoadModule slotmem_shm_module modules/mod_slotmem_shm.so
LoadModule lbmethod_bybusyness_module modules/mod_lbmethod_bybusyness.so
LoadModule lbmethod_byrequests_module modules/mod_lbmethod_byrequests.so
LoadModule lbmethod_bytraffic_module modules/mod_lbmethod_bytraffic.so
LoadModule status_module modules/mod_status.so
```
2.	配置（Apache24\conf\httpd.conf）
参照github上一篇文章：[Apache、Tomcat7集群session共享及负载均衡](https://github.com/sxyx2008/apache2-tomcat7-cluster)中2.2小节“ 配置（Apache24\conf\httpd.conf）”
-	使用mod_proxy_ajp
```{bash}
ProxyPass "/" "balancer://ajpproxy/"
<Proxy "balancer://ajpproxy/">
  BalancerMember "ajp://127.0.0.1:8009/" loadfactor=1 route=tomcat1
  BalancerMember "ajp://127.0.0.1:9009/" loadfactor=1 route=tomcat2
</Proxy>

<Location "/balancer-manager">
  SetHandler balancer-manager
  Allow from 127.0.0.1
</Location>
```

3.	踩坑：我在这样配置之后无法启动Apache，每次启动都报错`the requested operation has failed`。于是我又去百度了，得到以下方法[apache启动报错：the requested operation has failed解决办法](http://www.jb51.net/article/21004.htm)。参照此方法我在dos下cd 到Apache24\bin下然后 运行命令：`httpd.exe -w -n "Apache" -k start` 得到如下效果
![](/images/QQ截图20160320164915.png)
看这意思应该是没有Allow模块所至，参照：[apache错误Invalid command 'Order'处理](http://blog.sina.com.cn/s/blog_6dc4dbed0100zat9.html)。所以又百度了其他配置httpd.conf的方法：[apache2.4+tomcat集群](http://www.cnblogs.com/stay-sober/p/4207312.html)，此文有下边说
```{bash}
末尾添加：
ProxyRequests Off
ProxyPass / balancer://myCluster/
<Proxy balancer://myCluster/>
BalancerMember ajp://localhost:10009 route=Tomcat1
BalancerMember ajp://localhost:20009 route=Tomcat2
</Proxy>
说明：其中" myCluster "是集群的名称，" ajp://localhost:10009 route=Tomcat1 " 对应 Tomcat 集群中的 TC6_A 实例，" ajp://localhost:20009 route=Tomcat2 " 对应 Tomcat 集群中的 TC6_B 实例。
```
*在此文的启发下，我把tomcat1和tomcat2，也就是上篇文章里的B和C里边的server.xml中的`<Context docBase="D:\Program Files\apache-tomcat-7.0.67\webapps\ROOT" path="" reloadable="false"/>`改成`<Context docBase="D:\wwwroot1" path="" reloadable="false"/>`和`<Context docBase="D:\wwwroot2" path="" reloadable="false"/>`，同时在两个目录D:\wwwroot1和D:\wwwroot2都放了个index.jsp，且两个jsp内容分别为tomcat1、tomcat2这样也就方便我做负载均衡的测试了*

4.	综合以上两篇文章，我的配置如下：
```{bash}
ProxyRequests Off
ProxyPass / balancer://myCluster/ 
<Proxy "balancer://myCluster/">
    BalancerMember "ajp://127.0.0.1:8009/" loadfactor=1 route=tomcat1
    BalancerMember "ajp://127.0.0.1:9009/" loadfactor=1 route=tomcat2
</Proxy>
```

##  修改tomcat/conf/server.xml
1.	tomcat1，也就是我的B：
```{bash}
<Server port="8005" shutdown="SHUTDOWN">
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
<Engine name="Catalina" defaultHost="localhost" jvmRoute="tomcat1">
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
            channelSendOptions="8"> 
            <Manager className="org.apache.catalina.ha.session.BackupManager"
              expireSessionsOnShutdown="false"
              notifyListenersOnReplication="true"
              mapSendOptions="6"/> 
            <Channel className="org.apache.catalina.tribes.group.GroupChannel"> 
            <Membership className="org.apache.catalina.tribes.membership.McastService"
                        address="228.0.0.4"
                        port="45564"
                        frequency="500"
                        dropTime="3000"/> 
            <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                      address="127.0.0.1"
                      port="4000"
                      autoBind="100"
                      selectorTimeout="5000"
                      maxThreads="6"/> 
            <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter"> 
              <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/> 
            </Sender> 
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/> 
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor"/> 
            </Channel> 
            <Valve className="org.apache.catalina.ha.tcp.ReplicationValve" filter=""/> 
            <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/> 
            <ClusterListener className="org.apache.catalina.ha.session.JvmRouteSessionIDBinderListener"/> 
            <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/> 
</Cluster>
```

2.	tomcat2，即C：
```{bash}
<Server port="9005" shutdown="SHUTDOWN">
<Connector port="9080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
<Connector port="9009" protocol="AJP/1.3" redirectPort="8443" />
<Engine name="Catalina" defaultHost="localhost" jvmRoute="tomcat2">
<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
            channelSendOptions="8"> 
            <Manager className="org.apache.catalina.ha.session.BackupManager"
              expireSessionsOnShutdown="false"
              notifyListenersOnReplication="true"
              mapSendOptions="6"/> 
            <Channel className="org.apache.catalina.tribes.group.GroupChannel"> 
            <Membership className="org.apache.catalina.tribes.membership.McastService"
                        address="228.0.0.4"
                        port="45564"
                        frequency="500"
                        dropTime="3000"/> 
            <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                      address="127.0.0.1"
                      port="5000"
                      autoBind="100"
                      selectorTimeout="5000"
                      maxThreads="6"/> 
            <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter"> 
              <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/> 
            </Sender> 
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/> 
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor"/> 
            </Channel> 
            <Valve className="org.apache.catalina.ha.tcp.ReplicationValve" filter=""/> 
            <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/> 
            <ClusterListener className="org.apache.catalina.ha.session.JvmRouteSessionIDBinderListener"/> 
            <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/> 
</Cluster>
```以上两个tomcat就把端口冲突分开了的。
3.	我修改之后的server.xml配置文件（去掉注释后效果）：
```{bash}
<?xml version="1.0" encoding="UTF-8"?>
<Server port="8005" shutdown="SHUTDOWN">

  
  <Listener SSLEngine="on" className="org.apache.catalina.core.AprLifecycleListener"/>
  
  <Listener className="org.apache.catalina.core.JasperListener"/>
  
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener"/>
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener"/>
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener"/>
  <GlobalNamingResources>
  
    <Resource auth="Container" description="User database that can be updated and saved" factory="org.apache.catalina.users.MemoryUserDatabaseFactory" name="UserDatabase" pathname="conf/tomcat-users.xml" type="org.apache.catalina.UserDatabase"/>
  </GlobalNamingResources>

 
  <Service name="Catalina">
    <Connector connectionTimeout="20000" port="8080" protocol="HTTP/1.1" redirectPort="8443"/>
    <Connector port="8009" protocol="AJP/1.3" redirectPort="8443"/>
    <Engine defaultHost="localhost" name="Catalina" jvmRoute="tomcat1">
	<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster"
            channelSendOptions="8"> 
            <Manager className="org.apache.catalina.ha.session.BackupManager"
              expireSessionsOnShutdown="false"
              notifyListenersOnReplication="true"
              mapSendOptions="6"/> 
            <Channel className="org.apache.catalina.tribes.group.GroupChannel"> 
            <Membership className="org.apache.catalina.tribes.membership.McastService"
                        address="228.0.0.4"
                        port="45564"
                        frequency="500"
                        dropTime="3000"/> 
            <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                      address="127.0.0.1"
                      port="4000"
                      autoBind="100"
                      selectorTimeout="5000"
                      maxThreads="6"/> 
            <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter"> 
              <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/> 
            </Sender> 
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/> 
            <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor"/> 
            </Channel> 
            <Valve className="org.apache.catalina.ha.tcp.ReplicationValve" filter=""/> 
            <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/> 
            <ClusterListener className="org.apache.catalina.ha.session.JvmRouteSessionIDBinderListener"/> 
            <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/> 
	</Cluster>
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm" resourceName="UserDatabase"/>
      </Realm>

      <Host appBase="webapps" autoDeploy="true" name="localhost" unpackWARs="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs" pattern="%h %l %u %t &quot;%r&quot; %s %b" prefix="localhost_access_log." suffix=".txt"/>
		<Context docBase="D:\wwwroot1" path="" reloadable="false"/>
      </Host>
    </Engine>
  </Service>
</Server>
```

4.	分别去两个tomcat的bin目录下运行startup.bat启动
5.	启动Apache
6.	在浏览器中输入localhost会出现`tomcat1你好`和`tomcat2你好`
7.	踩坑：
	-	如果只启动了Apache而没有起动tomcat，在浏览器中输入`localhost`会出现以下错误![](/images/QQ截图20160320174254.png)
	-	接着启动B无论浏览器怎么刷新都会出现`tomcat1你好`
	-	于是，我就启动C，可是刷新页面10次，期望的`tomcat2你好`却没有出现。
	-	那么问题来了，难道不能热插拔吗？（ps:开着apache和B、C后，可以热拔C的，所以不能热插入。。。无耐百度上没有搜到相关资料）
	-	幸亏我没就这样下定结论，当我再次尝试上边的操作的时候，我刷新了将近1分钟，奇迹出现了，那个久违的`tomcat2你好`出来了。
	-	总结：Apache集群中插入结点的时候是有一定的缓冲时间的，也可能跟Apache的分发算法有关。还有不同的分发算法，本文只是用的轮循，还有按权重等算法。参考[Apache负载均衡的三大规则(一)](http://www.educity.cn/help/701922.html)