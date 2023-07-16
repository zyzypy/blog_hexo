---
title: L8.数据库基础
comments: true
toc: true
date: 2018-06-11 15:01:13
updated:
tags:
categories:
- flask
banner:
---
## Postgresql
### 数据库选择
- sqlite 轻量数据库
    优点python内置，适合入门学习，缺点太轻不适合生产环境。
- mysql 广告语：使用人数最多的数据库
    mysql随互联网热兴起，很多公司在用，要求课下掌握。
- postgreSQL 广告语：最先进的关系型数据库
    postgre(大象)数据库目前使用人数不算多但市场份额一直在增加，技术先进，高负载下表现良好，django 、odoo等框架指定首选数据库。
    跟mysql同为关系型数据库操作非常相似，相信学会postgre后，mysql、oracle、sqlserver等关系型数据库都不在话下。  
- nosql类 例如MongoDB，redis
    nosql兴起过一段时间，现在热度不如刚出来那会儿，个人理解是拿空间（成本比硬盘高的内存）换时间，文档和key-value存储本身并不复杂，可以课下学习。  
结论：本教程将以**PostgreSQL**为主，进行后面的知识和项目。
### 下载postgre数据库
可以直接看第4步。  
1. [postgresql官网](https://www.postgresql.org/) ,点击DOWNLOAD按钮。
![](install1.png)
2. [postgresql官网下载页](https://www.postgresql.org/download/)，选择对应的平台。本文照顾大多数人以windows平台为例。
3. [windows installers](https://www.postgresql.org/download/windows/)，官方提供了exe, zip包或可以安装多版本的图形化引导工具这三种方式。我们这里选择的是第一个普通的installer  
![](install2.png)
4. [PostgresSQLdownload](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads), 
版本选择，此时11beta版本已出，所以选择最新稳定版10.4。这里选择的是windows 64位，10.4版本的数据库，点击下载。  
大约150mb大小，网速困难的可以在国内软件站下载或联系我索要。
![](install3.png)
### 安装数据库
1. 打开准备好的安装包
![](install4.png)
2. 系统可能会自动安装vc++runtime，安装好后显示postgre的安装向导，跟普通软件一样next下一步
3. 软件路径，默认即可 C:/ProgramsFiles/Postgres/10 。
4. 安装组件。server必备，pgadmin是一个数据库连接工具类似navicat，命令行工具偶尔验证想法，stacktool没什么用可以不装。这里保持默认。
![](install5.png)
5. 选择data存储位置，默认即可，默认路径为 安装路径/data。
6. (重要)设置 超级管理员**postgres**的密码，这里我设置密码为**postgres**，因为本地测试方便和这是linux版本默认的密码。生产环境请不要起这个密码。
![](install6.png)
7. 端口号，保持默认5432
8. locale，保持默认
9. 确认安装概览，next开始安装
![](install7.png)
10. 安装完成
不勾选stacktools，finish完成。
![](install8.png)
![](install9.png)
可以看到数据库服务已启动。
### 命令行测试连接
先熟悉下命令行工具。测试下上面安装的数据库是否正常工作。未来linux部署上会用到。图形化工具也是在命令行工具的基础上运行的。  
打开powershell，cd到数据库安装目录bin目录下，执行psql命令：
```powershell
PS C:\Users\admin> cd 'C:\Program Files\PostgreSQL\10\bin'
PS C:\Program Files\PostgreSQL\10\bin> ./psql -U postgres
用户 postgres 的口令：
psql (10.4)
输入 "help" 来获取帮助信息.

postgres=# select * from pg_tables;
```
使用超级管理员账户**postgres**和之前设置的密码**postgres**登陆，登陆成功后前缀符号变为"postgres=#",可以输入一条select语句测试。
### 与安装图形化界面工具
#### 工具介绍与选择
为了更高效地工作，我们使用图形化数据库管理工具。
常用数据库图形化管理工具有
- Navicat：使用人数众多，包括mysql等其它种类数据库。要求课下试一试。缺点界面和速度略差。
- pgAdmin：安装postgre数据库自带的，缺点比较轻比太适合工作中使用。
- datagrip：出品pycharm的公司jetbrain出品的工具，已在pycharm中集成，不过有独立与ide的安装程序操作更方便。
结论：图形化工具非常相似，连接过程也一样，上面几种工具任选，下面以datagrip为例。不用纠结工具重点是后面sql的学习。
#### datagrip安装与连接
1. 官网介绍页[datagrip](https://www.jetbrains.com/datagrip/)
2. 点击download，选择合适平台，与pycharm下载、安装、激活过程类似。
![](connect2.png)
![](connect3.png)
3. 安装完成，启动
![](connect4.png)
4. 配置数据源
下载驱动：datagrip需要支持多种数据库，但为了节省空间没有内置驱动。
datagrip软件基于java开发页面，所有需要连接postgre数据库的jdbc驱动，就好比python连接mysql的pymysql驱动一样。
![](connect5.png)
![](connect6.png)
配置数据源：新建postgre数据源，填入超级管理员账户**postgres**和之前设置的密码**postgres**，点击test connection，连接成功。
![](connect7.png)
![](connect8.png)
5. 已连接默认的postgre数据库
![](connect9.png)
可以看到左侧是数据库目录，中间是编辑框写sql的地方，下部是结果集。
### 添加新用户和新建数据库
为了避免直接使用超级管理员postgres存在安全问题和不污染数据库本身的表，新建用户"test"和database"test"，然后以test用户登陆test数据库进行后面的各项学习试验。
#### 命令行方式
将会在部署部分linux系统上详细讲解。同学可以配置psql的环境变量先课下练习。
1. 创建新用户"test"
```sql
postgres=# CREATE USER dbuser WITH PASSWORD 'password';
```
2. 然后我们可以为这个新用户创建一个数据库

```sql
postgres=# CREATE DATABASE exampledb OWNER dbuser;
```
3. 接下来我们就可以使用刚才创建的用户登录控制台并连接到创建的数据库中来进行一系列的操作了
```powershell
psql -U dbuser -d exampledb
```
#### 图形工具方式
datagrip没找到创建用户的图形界面，这点不如navicat，可以在编辑器中输入上面讲到的sql语句执行。
其它的创建数据库和表操作可以用图形界面操作，一些步骤如图
![](connect10.png)
![](connect12.png)
![](connect13.png)
![](connect14.png)


## SQL
### 介绍和学习意义
> 结构化查询语言(Structured Query Language)简称SQL，是一种特殊目的的编程语言，是一种数据库查询和程序设计语言，用于存取数据以及查询、更新和管理关系数据库系统

编程，本质就是收集数据，自动化处理，传递数据或渲染到界面。操作数据库的语言是sql，它有自己的语法。不管做web还是爬虫或科学分析。
都将经常与数据库打交道。面试笔试中sql必考。工作中如果从事web开发每天必接触。所以童鞋们一定要重视这部分学习。
### 语法基础
（todo 展开写一些常用语法和重点）
回顾基础教程里语法基础
课下参考：[SQL总结（一）基本查询](https://www.cnblogs.com/yank/p/3672478.html)
### 强化训练
（todo 选几个问题）
课下参考：[经典SQL练习](https://blog.csdn.net/mrbcy/article/details/68965271)

## 参考阅读
[SQL总结（一）基本查询](https://www.cnblogs.com/yank/p/3672478.html)
[经典SQL练习](https://blog.csdn.net/mrbcy/article/details/68965271)
[PostgreSQL新手入门- 阮一峰](http://www.ruanyifeng.com/blog/2013/12/getting_started_with_postgresql.html)
[PostgreSQL表空间、数据库、模式、表、用户/角色之间的关系](https://blog.csdn.net/kanon_lgt/article/details/5931522)

## license
🚫禁止商用  转载注明作者
