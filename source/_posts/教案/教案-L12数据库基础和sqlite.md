## 数据库
### 主流数据库介绍
- 存储磁盘需求
- 关系型数据库
- 用excel例子引出表的概念
- 用excel和学生、班级做例子，稍微介绍一对多映射
- 主流数据库 oracle mysql pgsql sqlserver sqlite ，nosql mongo redis。

## sql
### 介绍 结构化查询语言
### 引题：结合excel讲解sql概念
2. 存储名字和分数学生表：编号 学生名 分数
3. 语句引题介绍select、 where语句
4. 概念介绍：数据库、表、字段
5. 常用数据类型 int varchar

## sqlite
### 什么是驱动
### 控制台进行练习   
语法
```sql
# 导入SQLite驱动:
>>> import sqlite3
# 连接到SQLite数据库
# 数据库文件是test.db
# 如果文件不存在，会自动在当前目录创建:
>>> conn = sqlite3.connect('test.db')
# 创建一个Cursor:
>>> cursor = conn.cursor()
# 执行一条SQL语句，创建user表:
>>> cursor.execute('create table user (id varchar(20) primary key, name varchar(20))')
<sqlite3.Cursor object at 0x10f8aa260>
# 继续执行一条SQL语句，插入一条记录:
>>> cursor.execute('insert into user (id, name) values (\'1\', \'Michael\')')
<sqlite3.Cursor object at 0x10f8aa260>
# 通过rowcount获得插入的行数:
>>> cursor.rowcount
1
# 关闭Cursor:
>>> cursor.close()
# 提交事务:
>>> conn.commit()
# 关闭Connection:
>>> conn.close()
>>> conn = sqlite3.connect('test.db')
>>> cursor = conn.cursor()
# 执行查询语句:
>>> cursor.execute('select * from user where id=?', ('1',))
<sqlite3.Cursor object at 0x10f8aa340>
# 获得查询结果集:
>>> values = cursor.fetchall()
>>> values
[('1', 'Michael')]
>>> cursor.close()
>>> conn.close()
```
概念
connect 、 cursor、 事务（原子性）、commit、 rollback

## 总结语法
1.创建新表
create table tabname(col1 type1 [not null] [primary key],col2 type2 [not null],..)

2.删除表
drop table tabname

3.查询：select * from table1 where 范围

4.插入：insert into 表名(字段名1,字段名2) values(值1,值2)

5.删除：delete from table1 where 范围

6.修改：update 表名 set 修改的字段名=修改的字段值 where 范围

7.查找：select * from table1 where field1 like ’%value1%’ N%匹配以N开头     %N匹配以N结尾   %N%匹配包含N  
	[a,b]% 以a或b开头     %[a,b]以a或b结尾

	select * from table1 where field1 like 'z_'以z开头且匹配之后一个字符

	升序输出数据记录
	select * from table_name order by field asc
	降序输出数据记录
	select * from table_name order by field desc

9.总数：select count (*) from table_name;


### 练习一
练习：
1,创建一个学生成绩表 Grades，学号  姓名  成绩  名次
CREATE TABLE Grades (num INTEGER PRIMARY KEY,name TEXT,grade INTEGER,rank INTEGER)

2，往表中插入10数据
INSERT IN TO Grades (num,name,grade,rank) VALUES (1,’zhangsan’,398,20)


3，查询成绩大于60分的人的名字
SELECT name,grade FROM grades WHERE grade > 60

4，查询成绩小于60分的人的个数
SELECT COUNT(*) FROM grades WHERE grade < 60

5，把成绩小于60分的人的成绩修改为60分
UPDATE grades SET grade = 60 WHERE grade < 60

6，删除所有学号小于100的人的信息
DELETE FROM grades WHERE num < 100


6，查询姓“张”的人的所有信息
SELECT * FROM grades WHERE name LIKE '张%'

7，查询所有人的成绩，按照成绩从高到低排列
SELECT grade FROM grades ORDER BY grade DESC

8，查询所有人的成绩，按照名次从小到大排列
SELECT grade FROM grades ORDER BY rank ASC




## 可视化工具
navicat     使用者较多    
(推荐)datagrip    jetbrains出品    
workbench  官方工具
 
er图UML图 文件右键show_diagram






## 安装mysql
#### 官网版本选择
5.7 8.0  zip installer

#### 安装
官方文档
https://dev.mysql.com/doc/refman/8.0/en/windows-create-option-file.html
【博客参考文章】
http://www.cnblogs.com/laumians-notes/p/9069498.html
my.ini
```
[mysqld]
# 设置3306端口
port=3306
# 设置mysql的安装目录
basedir=C:/Program Files/MySQL/mysql-8.0.12-winx64   # 双斜杠\\或/
# 设置mysql数据库的数据的存放目录
datadir=C:/Program Files/MySQL/mysql-8.0.12-winx64/Data   # 此处同上
# 允许最大连接数
# max_connections=200
# 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
# max_connect_errors=10
# 服务端使用的字符集默认为UTF8
#character-set-server=utf8mb4
# 创建新表时将使用的默认存储引擎
#default-storage-engine=INNODB
# 默认使用“mysql_native_password”插件认证
#default_authentication_plugin=mysql_native_password

[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8
[client]
# 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8
```
#### 概念
服务端和客户端

#### 登陆与调整字符集
mysql -u root -p
show variables like '%char%';

8.0已经不需要改字符集
字符集历史https://zhidao.baidu.com/question/1949879706369876188.html

### 官方基础教程 
https://dev.mysql.com/doc/refman/8.0/en/creating-tables.html
todo insert那一节准备基础数据。

#### 驱动介绍和选择
mysqldb（底层C驱动，安装可能出现问题） mysql-connector官网安装,  pymysql（纯py，兼容mysqldb)
#### 执行昨天的练习
命令行和脚本。
```sql
# 导入MySQL驱动:
>>> import mysql.connector
# 注意把password设为你的root口令:
>>> conn = mysql.connector.connect(user='root', password='password', database='test')
>>> cursor = conn.cursor()
# 创建user表:
>>> cursor.execute('create table user (id varchar(20) primary key, name varchar(20))')
# 插入一行记录，注意MySQL的占位符是%s:
>>> cursor.execute('insert into user (id, name) values (%s, %s)', ['1', 'Michael'])
>>> cursor.rowcount
1
# 提交事务:
>>> conn.commit()
>>> cursor.close()
# 运行查询:
>>> cursor = conn.cursor()
>>> cursor.execute('select * from user where id = %s', ('1',))
>>> values = cursor.fetchall()
>>> values
[('1', 'Michael')]
# 关闭Cursor和Connection:
>>> cursor.close()
True
>>> conn.close()
```

### 语法二
#### 管理
- 新建数据库 删除数据库 所有数据库
- 切换数据库
user dbname；
show databases
show tables
show columns from表
- 新建角色  赋权限grant  
- alter 修改表
    创建用户
        create user '用户名'@'IP地址' identified by '密码';
    删除用户
        drop user '用户名'@'IP地址';
    修改用户
        rename user '用户名'@'IP地址'; to '新用户名'@'IP地址';;
    修改密码
        set password for '用户名'@'IP地址' = Password('新密码')

### 创建表
- 主键、外键、唯一、约束、非空
### 清空表
drop table 表名
delete from 表名
truncate table 表名
         

####
- like
- order by 排序

####
- group by 分组
- avg max count
- having
```sql
CREATE TABLE `user_info` (
    `id` INT(11) NOT NULL AUTO_INCREMENT COMMENT '主键id',
    `user_id` VARCHAR(50) NOT NULL DEFAULT '' COMMENT '用户编号',
    `grade` VARCHAR(50) NOT NULL DEFAULT '' COMMENT '年级',
    `class` VARCHAR(50) NOT NULL DEFAULT '' COMMENT '班级',
    PRIMARY KEY (`id`),
    UNIQUE INDEX `uniq_user_id` (`user_id`)
)
ENGINE=InnoDB

INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (10, '10230', 'C', 'B');
INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (9, '10229', 'C', 'a');
INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (8, '10228', 'B', 'b');
INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (7, '10227', 'B', 'b');
INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (6, '10226', 'B', 'a');
INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (5, '10225', 'B', 'a');
INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (4, '10224', 'A', 'b');
INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (3, '10223', 'A', 'b');
INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (2, '10222', 'A', 'a');
INSERT INTO `user_info` (`id`, `user_id`, `grade`, `class`) VALUES (1, '10221', 'A', 'a');

select max(user_id),grade from user_info group by grade ;
select max(user_id),grade from user_info group by grade  having grade>'A'
```
#### 连接 
visor或pycharm插件画图？
例子一：球员 球队
球员表：编号 姓名 球队编号
球队表：编号 名称 所在地
球员对球队多对一
球队表：球队编号 名称 
地址表：球丢编号 城市 地址
球队对地址一对一 主键必须相同
介绍映射。
学生表：编号 学生名 学习科目 所属班级
班级表：编号 年级 班级名
科目表：编号 科目
  教师表：编号 教师名 科目
学生_科目表：学生id 科目id
班级对学生 一对多， 学生对科目 多对多 

- join leftjoin innerejoin


### 练习二 常见sql五十道 
https://blog.csdn.net/fashion2014/article/details/78826299
学生练习数据库版
(选做）安装postgresql

## 封装mysql工具类
```
import pymysql as ps
 2 
 3 class MysqlHelper:
 4     def __init__(self, host, user, password, database, charset):
 5         self.host = host
 6         self.user = user
 7         self.password = password
 8         self.database = database
 9         self.charset = charset
10         self.db = None
11         self.curs = None
12     # 数据库连接
13     def open(self):
14         self.db = ps.connect(host=self.host, user=self.user, password=self.password,database=self.database, charset=self.charset)
15         self.curs = self.db.cursor()
16     # 数据库关闭
17     def close(self):
18         self.curs.close()
19         self.db.close()
20     # 数据增删改
21     def cud(self, sql, params):
22         self.open()
23         try:
24             self.curs.execute(sql, params)
25             self.db.commit()
26             print("ok")
27         except :
28             print('cud出现错误')
29             self.db.rollback()
30         self.close()
31     # 数据查询
32     def find(self, sql, params):
33         self.open()
34         try:
35             result = self.curs.execute(sql, params)
36             self.close()
37             print("ok")
38             return result
39         except:
40             print('find出现错误')
复制代码
2.数据查询（引入封装类）

1 from MysqlHelper import MysqlHelper
2 
3 mh = MysqlHelper('localhost', 'root', '123456', 'test', 'utf8')
4 sql = "select * from user where name=%s"
5 print(mh.find(sql, '小明'))
3.数据修改（引入封装类）

1 from MysqlHelper import MysqlHelper
2 
3 mh = MysqlHelper('localhost', 'root', '123456', 'test', 'utf8')
4 sql = "insert into user(name,password) values(%s,%s)"
5 mh.cud(sql, ('小光', '123456'))
```

## 练习三 学生管理数据库版

## 课外参考
安装初始化mysql后，默认几个库介绍 https://blog.csdn.net/zhang123456456/article/details/53771076/
char和varchar区别 https://blog.csdn.net/qq_33412610/article/details/77962267