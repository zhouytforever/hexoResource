---
title: Oracle主键自动增加的方法与总结
date: 2014-04-05 23:06:00
tags: 
- Oracle
categories: Oracle
---

## <span style="font-size: 19px; line-height: 27px; background-color: #ffff00;">第一步：添加主键（如果已有且是自己命名的可略过）</span>

<span style="font-size: 19px; line-height: 27px;">　　　　一个表中要有主键，我们建主键的时候要自己命名，如果不是可能在后边遇到&ldquo;主键唯一，不能自动添加&rdquo;的问题。</span>

<div class="line number23 index22 alt2">`　　设置主键：`</div>
<div class="line number24 index23 alt1">`　　　　　　alter`&nbsp;`table`&nbsp;"表的名字"&nbsp;`add`&nbsp;`constraint`&nbsp;"主键名字"`&nbsp;``primary`&nbsp;`key``(id);&nbsp;`</div>

&nbsp;

**<span style="background-color: #ffff00; font-size: 14pt;">第二步：创建序列</span>**

<span style="font-size: 14pt;">/*在Oracle中，可以为每张表的主键创建一个单独的序列，</span>
<span style="font-size: 14pt;">然后从这个序列中获取自动增加的标识符，把它赋值给主键。</span>
<span style="font-size: 14pt;">例如一下语句创建了一个名为<span>user_id_seq&nbsp;</span>的序列，</span>
<span style="font-size: 14pt;">这个序列的起始值为1，增量为1。*/</span>

&nbsp;

<span style="font-size: 14pt;">create sequence user_id_seq increment by 1 start with 1;</span>
<span style="font-size: 14pt;">create sequence user_id_seq</span>
<span style="font-size: 14pt;">minvalue 1</span>
<span style="font-size: 14pt;">maxvalue 99999999999</span>
<span style="font-size: 14pt;">start with 1</span>
<span style="font-size: 14pt;">increment by 1</span>
<span style="font-size: 14pt;"><span>cache n &nbsp;/ nocache &nbsp;<span style="background-color: #00ff00;">--其中n代表一个整数，默认值为20</span></span></span>

<span style="font-size: 14pt;">;&nbsp;</span>

<span style="font-size: 14pt;">/*</span>

<span style="font-size: 14pt;">如果指定CACHE值，Oracle就可以预先在内存里面放置一些Sequence，这样存取的快些。cache里面的取完后，Oracle自动再取一组到cache。使用cache或许会跳号， 比如数据库突然不正常down掉（shutdown abort),cache中的Sequence就会丢失。举个例子：比如你的sequence中cache 100，那当你sequence取到90时突然断电，那么在你重启数据库后，sequence的值将从101开始。</span>

<span style="font-size: 14pt;">*/</span>

## <span style="background-color: #ffff00;">第三步：创建触发器</span>

<span style="font-size: 14pt;">/*如果每次都要插入user_id_seq.nextval的值会非常累赘与麻烦，</span>
<span style="font-size: 14pt;">因此可以考虑使用触发器来完成这一步工作。</span>
<span style="font-size: 14pt;">创建触发器trg_user_id_seq*/</span>
<span style="font-size: 14pt;">create or replace trigger trg_user_id_seq  BEFORE   </span>
<span style="font-size: 14pt;">insert ON  users FOR EACH ROW WHEN (new.id is null)</span>
<span style="font-size: 14pt;">	begin   select  user_id_seq.nextval into:new.id from dual;</span>
<span style="font-size: 14pt;">	end  trg_user_id_seq ; </span>

<span style="font-size: 14pt;">--编译 trigger 语句如下:</span>
<span style="font-size: 14pt;">--ALTER TRIGGER trg_user_id_seq COMPILE;</span>
<span style="font-size: 14pt;">--commit;</span>

&nbsp;

## <span style="background-color: #ffff00;">第四步：enjoy it !!!</span>

<span style="font-size: 14pt;">insert into 你的表，可以不用设置主键！！</span>