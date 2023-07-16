---
title: 数据库-PostgreSQL删除数据库关闭session
comments: true
toc: false
date: 2018-06-15 10:04:59
updated:
tags:
categories:
- 数据库
banner:
---
### 版本
PostgreSQL 10.4

### 问题
删除数据库`drop database testdb;`
提示还有其它用户连接，需要关闭session
问题产生原因：最后发现是ide的debug没关。跟图形工具的连接无关，session指正在执行的sql会话，可能慢查询也会导致此问题。
```bash
postgres=# drop database testdb;
ERROR:  database "testdb" is being accessed by other users
DETAIL:  There is 1 other session using the database.
```
### 解决办法
执行下面语句
```bash
postgres=# SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE datname='testdb' AND pid<>pg_backend_pid();
```
结果类似
```bash
 pg_terminate_backend 
----------------------
 t
 t
(2 rows)
```
然后再drop
```bash
DROP DATABASE testdb;
```

其它解决办法，原理一致：
1. 文章中看到另一种方法把上面的语句可以拆成两句话，找状态为 t（执行中的sql）的pid 终止掉，没有成功。
2. 查询到相应pid再执行回滚操作
