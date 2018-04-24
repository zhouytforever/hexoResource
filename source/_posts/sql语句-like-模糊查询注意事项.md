---
title: Sql语句 like %?%模糊查询注意事项
date: 2014-04-05 11:34:00
tags: 
- Sql
categories: Oracle
---

```sql
String sql = "select * from users where LNAME like '%+"+lname+"%'";
--此处不可用PreparedStatement的setParam，一定要字符串拼接，否则会报错。
```


