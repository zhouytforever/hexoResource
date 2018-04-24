---
title: Oracle数据库各种命令
date: 2014-07-25 16:24:00
tags: 
- Oracle
categories: Oracle
---

## 基本命令

```sql
sqlplus / as sysdba --命令行打开oracle（以dba身份登录）

drop user myusers cascade;--删除用户（级联）

create user myusers identified by 123;--创建用户（用户名：myusers。密码：123）

grant connect,resource to myusers;--授权

grant dba to myusers;--授权dba权限

connect myusers/zyt;--连接到刚才创建的用户
```

## oracle导入dmp

1. 首先进入cmd命令窗口
2. 执行命令：`imp userid=用户名/密码@orcl file=d:\nc60.dmp full=y` 

解释:
- 用户名：你的目标用户
- 密码：你的目标用户登录密码
- orcl:是实例名称，就是数据库名
- file：要导入的dmp文件绝对路径
- full=y 是否全部导入只有当前用户是dba的时候才能用此选项

## 导出数据库dmp文件命令

```
exp system/manager@TEST file=d:/daochu.dmp owner=(system,sys)
```
## 创建序列

```
CREATE SEQUENCE HIBERNATE_SEQUENCE MINVALUE 1 MAXVALUE 999999999999999999999999
```

## 添加外键

```sql
alter table EMP_JOBMSG
   add constraint FK_EMP foreign key (dep_id)
      references department (dep_id) on delete set null;
```

## 创建表空间

```sql
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
```

## 清空oracle回收站的垃圾

```sql
purge recyclebin;
```

回收站垃圾：![垃圾](http://images.cnitblog.com/blog/620450/201410/121531214997723.jpg)
