---
title: Oracle主键自动增加的方法与总结
date: 2014-04-05 23:06:00
tags: 
- Oracle
categories: Oracle
---

## 第一步：添加主键（如果已有且是自己命名的可略过）

一个表中要有主键，我们建主键的时候要自己命名。如果不是可能在后边遇到，主键唯一不能自动添加的问题。

```sql
alter table "表的名字" add constraint "主键名字" primary key(id);--设置主键
```

## 第二步：创建序列

在Oracle中，可以为每张表的主键创建一个单独的序列，然后从这个序列中获取自动增加的标识符，把它赋值给主键。
例如以下语句创建了一个名为`user_id_seq`的序列，这个序列的起始值为1，增量为1。

```sql
create sequence user_id_seq increment by 1 start with 1;
create sequence user_id_seq
minvalue 1
maxvalue 99999999999
start with 1
increment by 1
cache / nocache --其中n代表一个整数，默认值为20 
```

如果指定CACHE值，Oracle就可以预先在内存里面放置一些Sequence，这样存取的快些。cache里面的取完后，Oracle自动再取一组到cache。使用cache或许会跳号， 比如数据库突然不正常down掉（shutdown abort),cache中的Sequence就会丢失。举个例子：比如你的sequence中cache 100，那当你sequence取到90时突然断电，那么在你重启数据库后，sequence的值将从101开始。

## 第三步：创建触发器

如果每次都要插入user_id_seq.nextval的值会非常累赘与麻烦，因此可以考虑使用触发器来完成这一步工作。创建触发器trg_user_id_seq

```sql
create or replace trigger trg_user_id_seq  BEFORE   
insert ON  users FOR EACH ROW WHEN (new.id is null)
begin select user_id_seq.nextval into:new.id from dual;
end trg_user_id_seq; 
--编译 trigger 语句如下:
--ALTER TRIGGER trg_user_id_seq COMPILE;
--commit;
```
## 第四步：enjoy it !!!

```sql
insert into your_table_name --your_table_name: 你的表，可以不用设置主键！！
```
