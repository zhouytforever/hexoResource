---
title: Sql语句 like %?%模糊查询注意事项
date: 2014-04-05 11:34:00
tags: 
- Sql
categories: Oracle
---

String sql = "select * from users where LNAME like '%+"+lname+"%'";

<span style="color: #ff0000;">//此处不可用PreparedStatement的setParam。一定要字符串拼接。</span>

<span style="color: #ff0000;">否则会报错</span>