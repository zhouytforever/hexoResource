---
title: oracle数据库各种命令
tags: 
- oracle
categories: Oracle
date: 2014-07-25 16:24:00
---

<span style="background-color: #00ff00; font-size: 16px;">-------------------基本命令----------------------------</span>

<span style="font-size: 16px;">sqlplus / as sysdba--命令行打开oracle（以dba身份登录）</span>

<span style="font-size: 16px;">drop user myusers cascade;--删除用户（级联）</span>

<span style="font-size: 16px;">create user myusers identified by 123;--创建用户（用户名：myusers。密码：123）</span>

<span style="font-size: 16px;">grant connect,resource to myusers;--授权</span>

<span style="font-size: 16px;">grant dba to myusers;--授权dba权限</span>

<span style="font-size: 16px;">connect myusers/zyt;--连接到刚才创建的用户</span>

&nbsp;

<span style="font-size: 16px; background-color: #00ff00;">-------------------oracle导入dmp文件命令------------</span>
<span style="font-size: 16px;">1.首先进入cmd命令窗口</span>

<span style="font-size: 16px;">2.执行命令：imp userid=用户名/密码@orcl file=d:\nc60.dmp full=y </span>
<span style="font-size: 16px;">解释</span>
<span style="font-size: 16px;">用户名：就是你的目标用户 。</span>
<span style="font-size: 16px;">密码：就是你的目标用户登录密码。</span>
<span style="font-size: 16px;">orcl:是实例名称 就是数据库名。</span>
<span style="font-size: 16px;">file：就是你要导入的dmp文件全路径。</span>
<span style="font-size: 16px;">full=y 是否全部导入只有当前用户是dba的时候才能用此选项 。</span>

&nbsp;

<span style="font-size: 16px; background-color: #00ff00;">--------------------导出数据库dmp文件命令-----------</span>

<span style="font-size: 16px;">exp system/manager@TEST file=d:/daochu.dmp owner=(system,sys)</span>

&nbsp;

<span style="background-color: #00ff00;">-----------------------创建序列--------------------------------</span>

CREATE SEQUENCE HIBERNATE_SEQUENCE MINVALUE 1 MAXVALUE 999999999999999999999999

&nbsp;

<span style="background-color: #00ff00;">-----------------------添加外键--------------------------------</span>

alter table EMP_JOBMSG
   add constraint FK_EMP foreign key (dep_id)
      references department (dep_id) on delete set null;

&nbsp;

<span style="background-color: #00ff00;">-----------------------创建表空间------------------------------</span>

/*第1步：创建临时表空间 */
create temporary tablespace user_temp  
tempfile 'D:\user_temp.dbf' 
size 50m  
autoextend on  
next 50m maxsize 20480m  
extent management local;  

/*第2步：创建数据表空间  */
create tablespace user_data  
logging  
datafile 'D:\user_data.dbf' 
size 50m  
autoextend on  
next 50m maxsize 20480m  
extent management local;  

/*第3步：创建用户并指定表空间  */
create user username identified by password  
default tablespace user_data  
temporary tablespace user_temp;  

/*第4步：给用户授予权限  */
grant connect,resource,dba to username;

&nbsp;

<span style="background-color: #00ff00;">-----------------------清空oracle回收站的垃圾-------------------</span>

purge recyclebin;

回收站垃圾：

![](http://images.cnitblog.com/blog/620450/201410/121531214997723.jpg)

&nbsp;