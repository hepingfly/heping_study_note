# MySQL 高级笔记

### 一、mysql 架构介绍

#### 1、MySQL简介

**1）、概述**

> - mysql 是一个关系型数据库管理系统（RDBMS），由瑞典 MYSQL AB 公司开发，目前属于 oracle 公司
> - mysql 是开源的，所以你不需要支付额外的费用
> - mysql 支持大型的数据库。可以处理有上千万条记录的大型数据库
> - mysql 支持大型数据库，支持 5000 万条记录的数据仓库，32 位系统表文件最大可支持 4GB，64 为系统支持最大表文件为 8TB
> - Mysql 是可以定制的，采用了 GPL 协议，你可以修改源码来开发自己的 mysql 系统

#### 2、MySQL Linux 版安装

**1）、下载**

去 mysql 官网下载 rpm 包

**2）、检查当前系统是否安装过 mysql** 

> 查询命令：
>
> `rpm -qa | grep mysql`      如果有查出来安装包，需要将已安装的删除
>
> 删除命令：
>
> rpm -e 包名

**3）、安装 MySQL** 

> **注意：**在新版本的CentOS7中，默认的数据库已更新为了Mariadb，而非 MySQL，所以执行 yum install mysql 命令只是更新Mariadb数据库，并不会安装 MySQL 。
>
> 1、查看已安装的 Mariadb 数据库版本
>
> ```shell
> rpm -qa|grep -i mariadb
> ```
>
> 2、卸载已安装的 Mariadb 数据库
>
> ```shell
> rpm -qa|grep mariadb|xargs rpm -e --nodeps
> ```
>
> 3、再次查看已安装的 Mariadb 数据库版本，确认是否卸载完成
>
> ```shell
> rpm -qa|grep -i mariadb
> ```
>
> 4、下载安装包文件
>
> ```shell
> wget https://dev.mysql.com/get/mysql80-community-release-el6-2.noarch.rpm
> ```
>
> 5、安装 mysql80-community-release-el6-2.noarch.rpm 包
>
> ```shell
> rpm -ivh mysql-community-release-el7-5.noarch.rpm
> ```
>
> 安装完成之后，会在 /etc/yum.repos.d/ 目录下新增 mysql-community.repo 、mysql-community-source.repo 两个 yum 源文件
>
> 6、安装 mysql
>
> ```shell
> yum install mysql-server
> ```
>
> 7、检查 mysql 是否安装成功
>
> ```shell
> rpm -qa | grep mysql
> ```
>
> 8、启动 mysql 服务
>
> ```shell
> systemctl start mysqld.service #启动 mysql
> systemctl restart mysqld.service #重启 mysql
> systemctl stop mysqld.service #停止 mysql
> systemctl enable mysqld.service #设置 mysql 开机启动
> ----------------------------------------------------
> centos6 使用：
> service mysqld start
> ```
>
> 9、设置密码
>
> mysql5.6 安装完成后，它的 [root](https://www.baidu.com/s?wd=root&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd) 用户的密码默认是空的，我们需要及时用 mysql 的 root 用户登录（第一次直接回车，不用输入密码），并修改密码。
>
> ```shell
> mysql -u root
> mysql> use mysql;
> mysql> update user set password=PASSWORD("这里输入root用户密码") where User='root';
> mysql> flush privileges;
> ```
>
> 10、设置远程主机登录
>
> ```shell
> mysql> GRANT ALL PRIVILEGES ON *.* TO 'your username'@'%' IDENTIFIED BY 'your password';
> ```
>
> 执行以下命令，为root 用户添加远程登录的能力:
>
> ```shell
> mysql> GRANT ALL PRIVILEGES ON *.* TO root@"%" IDENTIFIED BY "123456";
> ```
>
> 

**注：**

> `ntsysv`
>
> 使用 ntsysv 命令后看到  `[*] mysql` 表示 mysql 会开机自启

####  3、MySQL 数据文件存放位置

> mysql 数据文件存放位置为 /var/lib/mysql  目录下，你新建的数据库都会存放在这个目录下

#### 4、修改 MySQL 默认字符集

1、先看下 /etc 目录下有没有 my.cnf 配置文件

```shell
ls -l /etc/my.cnf

# 如果有的话跳过第二步
```

2、拷贝一份配置文件到 /etc 目录下

> 5.5 版本：
>
> cp /usr/share/mysql/my-huge.cnf  /etc/my.cnf
>
> 5.6 版本：
>
> cp /usr/share/mysql/my-default.cnf /etc/my.cnf

3、查看 mysql 默认字符集

```shell
# 在 mysql 客户端执行这个
show variables like '%char%';

+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
+--------------------------+----------------------------+
```

4、编辑 my.cnf 配置文件

> `vim /etc/my.cnf`
>
> 添加下面几行配置后，重启 mysql

```shell
[client]
default-character-set=utf8
[mysqld]
character_set_server=utf8
character_set_client=utf8
collation-server=utf8_general_ci
```

在 mysql 客户端执行 `show variables like '%char%';` 如果发现都变成了 utf8 表示修改成功

**注意：**

> 有可能你在没有修改字符集编码之前插入数据库是乱码，修改完字符集后发现插入还是乱码。
>
> 原因是，你这个库已经是非 utf8 的，重新创建一个数据库再插入数据就不会出现乱码问题

#### 5、MySQL 配置文件

首先说下配置文件的位置：

> windows:    `my.ini 文件`
>
> linux ：`/etc/my.cnf 文件`

1）、二进制日志 log-bin

这个日志文件主要用于主从复制

```ini
[mysqld]
log-bin=D:/software/MySQLServer5.5/data/mysqlbin

# 想要配置二进制日志文件就照上面这样配
```

2）、错误日志 log-error

默认是关闭的，记录严重的警告和错误信息，每次启动和关闭的详细信息等。

```ini
[mysqld]
log-err=D:/software/MySQLServer5.5/data/mysqlerr

# 想要配置错误日志文件就照上面这样配
```

3）、查询日志log

默认关闭，记录查询的 sql 语句，如果开启会降低 mysql 的整体性能，因为记录日志也是需要消耗系统资源的

4）、数据文件

① mysql 数据文件存放位置

> - windows
>   - `D:/software/MySQLServer5.5/data/`
>   - data 下面会有很多很多你建立的数据库
> - linux 
>   - 默认路径：`/var/lib/mysql`
>   - 你进入到这个目录下使用命令  `ls -l |grep ^d`  把所有的目录都查出来，这样你就可以看到一个一个的数据库

② frm 文件

 存放的是表结构

![frm 文件](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-mysql_advance/frm%20%E6%96%87%E4%BB%B6.png?q-sign-algorithm=sha1&q-ak=AKIDmidkOwgrc22Q08N8r2UT0uAD1ThpcL8n&q-sign-time=1554044602;1554046402&q-key-time=1554044602;1554046402&q-header-list=&q-url-param-list=&q-signature=297340e94e0b9ad555fa451add372ae240c93508&x-cos-security-token=cec877d2cc07180a741dfb2811e515b093963eda10001)

你创建一个表，就会有对应的 frm 文件，存放表结构

③ myd 文件

存放的是表数据

④ myi 文件

存放的是表索引

![myd 和 myi 文件](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-mysql_advance/myd%20%E5%92%8C%20myi%20%E6%96%87%E4%BB%B6.png?q-sign-algorithm=sha1&q-ak=AKIDrKJiegRVePTOqcyPJpTOACYInFiHK72w&q-sign-time=1554044667;1554046467&q-key-time=1554044667;1554046467&q-header-list=&q-url-param-list=&q-signature=77f2ce56e688f8fdbb40715906304c01acf1c05b&x-cos-security-token=0632e4b411db434c1658d8266902badce5c8fc8110001)

#### 6、MySQL 逻辑架构介绍

![mysql 逻辑架构图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-mysql_advance/mysql%20%E9%80%BB%E8%BE%91%E6%9E%B6%E6%9E%84%E5%9B%BE.jpg?q-sign-algorithm=sha1&q-ak=AKIDie5z7XhioiSJ6KNJXlsy410n3H9hdPcd&q-sign-time=1554044547;1554046347&q-key-time=1554044547;1554046347&q-header-list=&q-url-param-list=&q-signature=003654a9acc01a6d52b0353d606c636cad5d56f9&x-cos-security-token=e44c7d40166bb8f7b8d1234a1b97f809eff5e6ce10001)



① 连接层

> 最上层是一些客户端和连接服务，包含本地scoket通信和大多数基于客户端/服务端工具实现的类似于tcp/ip的通信，主要完成一些类似连接处理，授权认证，及相关的安全方案，在该层引入线程池的概念，为通过认证安全接入的客户端提供线程，同样在该层上可以实现基于SSL的安全链接，[服务器](https://www.baidu.com/s?wd=%E6%9C%8D%E5%8A%A1%E5%99%A8&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)也会为安全接入的每个客户端验证它所具备的操作权限

② 服务层

> 第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存查询，SQL的分析和优化及部分内置函数的执行，所有跨存储[引擎](https://www.baidu.com/s?wd=%E5%BC%95%E6%93%8E&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)的功能也在这一层实现，如过程，函数等，在该层，服务层会解析查询并创建相应的内部解析树，并对其完成响应的优化确认查询表的顺序，是否利用索引等，最后生成相应的执行操作，如果是select语句，服务器还会查询内部的缓存，如果缓存空间足够大，这样就解决大量读操作的环境中能够很好的提供系统性能。

③ 引擎层

> 存储引擎层，存储引擎真正负责了MySQL[中数据](https://www.baidu.com/s?wd=%E4%B8%AD%E6%95%B0%E6%8D%AE&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)的存储和提取，服务器通过API与存储引擎进行通信，不同的存储引擎具有的功能不同，这样我们可以根据自己的实际需要进行选取，例如：MYISAM和InnoDB

④ 存储层

> 数据存储层，主要讲数据存储在运行于裸设备的文件系统之上。并完成存储引擎的交互(文件系统)

#### 7、存储引擎介绍

**1）、查看存储引擎**

> 在 mysql 客户端执行：
>
> show engines;        查看你的 MySQL 现在提供什么存储引擎
>
> show variables like '%storage_engine%'         查看 MySQL 默认的存储引擎

**2）、MyISAM 和 InnoDB**

| 对比项   | MyISAM                                                 | InnoDB                                                       |
| -------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 主外键   | 不支持                                                 | 支持                                                         |
| 事务     | 不支持                                                 | 支持                                                         |
| 行表锁   | 表锁，即使操作一条记录也会锁住整个表，不适合高并发操作 | 行锁，操作时只锁一行，不对其他行有影响，适合高并发操作       |
| 缓存     | 只缓存索引，不缓存真实数据                             | 不仅缓存索引还缓存真实数据，对内存要求较高，而且内存大小对性能有决定性的影响 |
| 表空间   | 小                                                     | 大                                                           |
| 关注点   | 性能                                                   | 事务                                                         |
| 默认安装 | Y                                                      | Y                                                            |

### 二、索引优化分析

#### 1、索引简介

1）、索引是什么

> 首先别人问你索引是什么，你回答索引就像新华字典前面的目录，根据这个目录可以很快找到需要的内容，这个回答是欠妥的。
>
> - MySQL 官方对索引的定义为：索引（Index）是帮助 MySQL 高效获取数据的数据结构。所以我们可以得到索引的本质：==索引是数据结构==
> - 你可以简单理解为排好序的快速查找数据结构
>   - 在数据之外，**数据库系统还维护满足特定查找算法的数据结构**，这些数据结构以某种方式引用（指向）数据，这样就可以在这些数据结构上实现高级查找算法。这种数据结构，就是索引。下图就是一种可能的索引方式示例（配图说明：）
>   - 总结一下：数据本身之外，数据库还维护这一个满足特定查找算法的数据结构，这些数据结构以某种方式指向数据，这样就可以在这些数据结构的基础上实现高级查找算法，这种数据结构就是索引。
> - 一般来说索引本身也很大，不可能全部存在在内存中，因此索引往往以索引文件的形式存储在磁盘上
> - 我们平常所说的索引，如果没有特别指明，都是指 B 树（多路搜索树，并不一定是二叉的）结构组织的索引。其中聚集索引、复合索引、前缀索引默认都是使用 B+ 树索引，统称索引。当然除了 B+ 树这种索引之外，还有哈希索引。

![mysql 索引方式](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-mysql_advance/mysql%20%E7%B4%A2%E5%BC%95%E6%96%B9%E5%BC%8F.png?q-sign-algorithm=sha1&q-ak=AKID1gXcScRS59kpVSY5gC5Id0tN836SJNDM&q-sign-time=1554053118;1554054918&q-key-time=1554053118;1554054918&q-header-list=&q-url-param-list=&q-signature=8de4bf7ef5fe321b1fc5e1e819aa82ce5b489772&x-cos-security-token=6eacb680bb5b2c5425b4d4fc585df45d9f21bba110001)

> 为了加快 Col2 的查找，可以维护一个右边所示的二叉查找树，每个节点分别包含索引键值和一个指向对应数据记录物理地址的指针，这样就可以运用二叉查找在一定的复杂度内获取到相应的数据，从而快速的检索出符合条件的记录。

**注：**

索引会影响到你 where 条件后面的查找和 order by 后面的排序。索引的两大功能，查找和排序。

2）、索引优势劣势

优势：

> - 类似大学图书馆建书目索引，提高数据检索的效率，降低数据库的 IO 成本
> - 通过索引对数据进行排序，降低数据排序的成本，降低了 CPU 的消耗

劣势：

> - 实际上索引也是一张表，该表保存了主键和索引字段，并指向实体表的记录，所以索引列也是要占空间的
> - 虽然索引大大提高了查询速度，同时却会降低更新表的速度，如对表进行 insert update delete 。因为更新表时，mysql 不仅要保存数据，还要保存一下索引文件每次更新添加了索引列的字段，都会调整因为更新所带来的键值变化后的索引信息

#### 2、索引分类

> - 单值索引：即一个索引只包含单个列，一个表可以有多个单列索引
> - 唯一索引：索引列的值必须唯一，单允许有空值
> - 复合索引：即一个索引包含多个列

#### 3、哪些情况需要建索引

> 1、主键自动建立唯一索引
>
> 2、频繁作为查询条件的字段应该建立索引
>
> 3、查询中与其他表关联的字段，外键关系需要建立索引
>
> 4、频繁更新的字段不适合创建索引
>
> 5、where 条件里用不到的字段不创建索引
>
> 6、查询中排序的字段，排序字段若通过索引去访问将大大提高排序速度
>
> 7、查询中统计或者分组字段

#### 4、哪些情况不需要建索引

> 1. 表记录太少
> 2. 经常增删改的表
>    - 提高了查询速度，但同时会降低更新表的速度，如对表进行 insert update 和 delete 因为更新表时，Mysql 不仅要保存数据，还要保存一下索引文件
> 3. 如果某个数据列包含许多重复的内容，为它建立索引就没有太大的实际效果。比如说有一个数据列是性别，不是男就是女，那么你为这一列建立索引就没有太大的必要。
>    - 说一个索引选择性，索引选择性是指索引列中不同值的数目与表中记录数的比。如果一个表中有 2000 条记录，表索引列有 1980 个不同的值，那么这个索引的选择性就是 1980/2000 = 0.99  一个索引的选择性越接近于  1 ，这个索引的效率就越高

### 三、性能分析

#### 1、MySQL Query Optimizer

> 1、MySQL 中有专门负责优化 select 语句的优化器模块，主要功能：通过计算分析系统中收集到的统计信息，为客户端请求的 Query 提供它认为最优的执行计划（MySQL 认为最有的检索方式，不一定是 DBA 认为最优的，这部分最耗时间）
>
> 2、当客户端向 MySQL 请求一条 Query ，命令解析器模块完成请求分类，区别出是 select 并转发给 MySQL Query Optimizer 时，MySQL Query Optimizer 首先会对整条 Query 进行优化，处理掉一些常量表达式的运算，直接换算成常量值。并对 Query 中的查询条件进行简化和转换，如去掉一些无用或显而易见的条件、结构调整等。然后分析 Query 中的 Hint 信息（如果有），看显示 Hint 信息是否可以完全确定该 Query 的执行计划。如果没有 Hint 或者 Hint 信息还不足以完全确定执行计划，则会读取所涉及对象的统计信息，根据 Query 进行写相应的计算分析，然后再得出最后的执行计划

#### 2、MySQL 常见瓶颈

> - CPU ：CPU 在饱和的时候一般发生在数据装入内存或从磁盘上读取数据的时候
> - IO：磁盘 I/O 瓶颈发生在装入数据远大于内存容量的时候

#### 3、Explain 使用简介

1）、explain 是什么（查看执行计划）

> 使用 explain 关键字可以模拟优化器执行 sql 查询语句，从而知道 mysql 是如何处理你的 sql 语句的。分析你的查询语句或是表结构的性能瓶颈。

2）、explain 能干嘛

> - 表的读取顺序
> - 数据读取操作的操作类型
> - 哪些索引可以使用
> - 哪些索引被实际使用
> - 表之间的使用
> - 每张表有多少行被优化器查询

3）、explain 怎么用

> **用法：explain + sql 语句**

```mysql
mysql>  explain select * from book;
+----+-------------+-------+-------+---------------+---------+---------+------+------+-------------+
| id | select_type | table | type  | possible_keys | key     | key_len | ref  | rows | Extra       |
+----+-------------+-------+-------+---------------+---------+---------+------+------+-------------+
|  1 | SIMPLE      | book  | index | NULL          | PRIMARY | 4       | NULL |    1 | Using index |
+----+-------------+-------+-------+---------------+---------+---------+------+------+-------------+

```

#### 4、Explain 各字段解释

1）、explain 之 id 介绍

> select 查询的序列号，包含一组数字，表示查询中执行 select 子句或操作表的顺序
>
> 它包括三种情况：
>
> - id 相同，执行顺序由上至下
> - id 不同，如果是子查询，id 序号会递增，id 值越大优先级越高，越先被执行
> - id 相同不相同同时存在。id 如果相同可以认为是一组，从上往下顺序执行，在所有组中 id 值越大，优先级越高，越先执行

上面第一种情况：

```mysql
mysql> explain select t2.* from t1,t2,t3 where t1.id = t2.id and t1.id = t3.id and t1.other_column = '';

+----+-------------+-------+--------+---------------+---------+---------+---------------+------+-------------+
| id | select_type | table | type   | possible_keys | key     | key_len | ref           | rows | Extra       |
+----+-------------+-------+--------+---------------+---------+---------+---------------+------+-------------+
|  1 | SIMPLE      | t1    | ALL    | PRIMARY       | NULL    | NULL    | NULL          |    1 | Using where |
|  1 | SIMPLE      | t2    | eq_ref | PRIMARY       | PRIMARY | 4       | student.t1.id |    1 |             |
|  1 | SIMPLE      | t3    | eq_ref | PRIMARY       | PRIMARY | 4       | student.t1.id |    1 | Using index |
+----+-------------+-------+--------+---------------+---------+---------+---------------+------+-------------+
```

![explain_id 相同](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-mysql_advance/explain_id%E7%9B%B8%E5%90%8C.png?q-sign-algorithm=sha1&q-ak=AKIDIenXiMouF0fop6ufW3zY7N48z7A6487T&q-sign-time=1553609453;1553611253&q-key-time=1553609453;1553611253&q-header-list=&q-url-param-list=&q-signature=d2b9523765b258d584f4eb79d37623a5be761da9&x-cos-security-token=e00f86859f393f79d4abda1af8d92f48451b2a8010001)

上面的第二种情况：

```mysql
mysql> explain select t2.* from t2 where id = (select id from t1 where id = (select t3.id from t3 where t3.other_column = ''));
+----+-------------+-------+------+---------------+------+---------+------+------+-----------------------------------------------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra                                               |
+----+-------------+-------+------+---------------+------+---------+------+------+-----------------------------------------------------+
|  1 | PRIMARY     | NULL  | NULL | NULL          | NULL | NULL    | NULL | NULL | Impossible WHERE noticed after reading const tables |
|  2 | SUBQUERY    | NULL  | NULL | NULL          | NULL | NULL    | NULL | NULL | no matching row in const table                      |
|  3 | SUBQUERY    | t3    | ALL  | NULL          | NULL | NULL    | NULL |    1 | Using where                                         |
+----+-------------+-------+------+---------------+------+---------+------+------+------------------------------------
```

上面第三种情况：

```mysql
mysql> explain select t2.* from (select t3.id from t3 where t3.other_column='') s1,t2 where s1.id = t2.id;
+----+-------------+-------+------+---------------+------+---------+------+------+-----------------------------------------------------+
| id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra                                               |
+----+-------------+-------+------+---------------+------+---------+------+------+-----------------------------------------------------+
|  1 | PRIMARY     | NULL  | NULL | NULL          | NULL | NULL    | NULL | NULL | Impossible WHERE noticed after reading const tables |
|  2 | DERIVED     | t3    | ALL  | NULL          | NULL | NULL    | NULL |    1 | Using where                                         |
+----+-------------+-------+------+---------------+------+---------+------+------+------------------------------------
```

2）、explain 之 select_type 和 table 介绍

`select_type`

> 这个 `select_type` 表示的就是查询类型，主要用于区别普通查询、联合查询、子查询等的复杂查询。
>
> 主要有下面这几种类型：
>
> - SIMPLE
>   - 简单的 select 查询，查询中不包含子查询或者 union
> - PRIMARY
>   - 查询中若包含任何复杂的子部分，最外层查询则被标记为 PRIMARY
> - SUBQUERY
>   - 在 select 或 where 列表中包含了子查询，该子查询被标记为 SUBQUERY
> - DERIVED
>   - 在 from 列表中包含的子查询被标记为 DERIVED（衍生）
> - UNION
>   - 若第二个 select 出现在 union 之后，则被标记为 union。如果 union 包含在 from 子句的子查询中，外层 select 将被标记为 DERIVED
> - UNION RESULT
>   - 从 union 表获取结果的 select 被标记为 UNION RESULT

`table` :

> 显示这一行的数据是关于哪张表的

3）、explain 之 type 介绍

一共有下面几种类型：

![explain_type 类型](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-mysql_advance/explain_type%20%E7%B1%BB%E5%9E%8B.png?q-sign-algorithm=sha1&q-ak=AKIDEuiIejuqy6ubPhnW1hMNIuYhc7ZLUJ1A&q-sign-time=1554057852;1554059652&q-key-time=1554057852;1554059652&q-header-list=&q-url-param-list=&q-signature=dfedfc6a47f1facee4eb8255e0d636b515be37ae&x-cos-security-token=d9edcdae3af398e6a4bc32370211b46c7f7dd74410001)



> type 表示的是访问类型，显示查询用了何种类型，结果值从好到坏依次为：
>
> system > const > eq_ref > ref > range > index > ALL
>
> 一般来说得保证查询至少达到 range 级别，最好能达到 ref 
>
> - system
>
>   - 表只有一行记录，这是 const 类型的特例，平时不会出现，这个可以忽略不计。
>
> - const
>
>   - 表示通过索引一次就找到了， const 用于比较 primary key 或者 unique 索引。因为只匹配一行数据，所以会很快。比如将主键置于 where 列表中，mysql 就能将该查询转换成一个常量
>
>   - ```mysql
>     mysql> explain select * from (select * from t1 where id =1)d1;
>     +----+-------------+------------+--------+----------------+---------+---------+------+------+-------+
>     | id | select_type | table      | type   | possible_keys  | key     | key_len | ref  | rows | Extra |
>     +----+-------------+------------+--------+----------------+---------+---------+------+------+-------+
>     |  1 | PRIMARY     | <derived2> | system | NULL           | NULL    | NULL    | NULL |    1 |       |
>     |  2 | DERIVED     | t1         | const  | PRIMARY,idx_t1 | PRIMARY | 4       |      |    1 |       |
>     +----+-------------+------------+--------+----------------+---------+---------+------+------+-------+
>
>     ```
>
> - eq_ref
>
>   - 唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描
>
>   - ```mysql
>     mysql> explain select * from t1,t2 where t1.id = t2.id;
>     +----+-------------+-------+--------+----------------+---------+---------+---------------+------+-------+
>     | id | select_type | table | type   | possible_keys  | key     | key_len | ref           | rows | Extra |
>     +----+-------------+-------+--------+----------------+---------+---------+---------------+------+-------+
>     |  1 | SIMPLE      | t1    | ALL    | PRIMARY,idx_t1 | NULL    | NULL    | NULL          |    1 |       |
>     |  1 | SIMPLE      | t2    | eq_ref | PRIMARY        | PRIMARY | 4       | student.t1.id |    1 |       |
>     +----+-------------+-------+--------+----------------+---------+---------+---------------+------+-------+
>
>     ```
>
> - ref
>
>   - 非唯一性索引扫描，返回匹配某个单独值的所有行。本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而他可能会找到多个符合条件的行，所以它应该属于查找和扫描的混合体。
>
>   - ```mysql
>     mysql> explain select * from t1 where col1='hi';
>     +----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
>     | id | select_type | table | type | possible_keys | key           | key_len | ref   | rows | Extra       |
>     +----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
>     |  1 | SIMPLE      | t1    | ref  | idx_col1_col2 | idx_col1_col2 | 768     | const |    2 | Using where |
>     +----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
>     ```
>
> - range
>
>   - 只检索给定范围的行，使用一个索引来选择行。key 列显示使用了哪个索引，一般就是在你的 where 语句中出现了 between  < > in 等查询，这种范围扫描索引比全表扫描要好，因为它只需要开始于索引的某一点，而结束语另一点，不用扫描全部索引。
>
>   - ```mysql
>     mysql> explain select * from t1 where id between 20 and 30;
>     +----+-------------+-------+-------+----------------+---------+---------+------+------+-------------+
>     | id | select_type | table | type  | possible_keys  | key     | key_len | ref  | rows | Extra       |
>     +----+-------------+-------+-------+----------------+---------+---------+------+------+-------------+
>     |  1 | SIMPLE      | t1    | range | PRIMARY,idx_t1 | PRIMARY | 4       | NULL |    1 | Using where |
>     +----+-------------+-------+-------+----------------+---------+---------+------+------+-------------+
>
>     ```
>
> - index
>
>   - Full Index Scan ，index 与 All 区别为 index 类型只遍历索引树。这通常比 ALL 快，因为索引文件通常比数据文件小。（也就是说，虽然 index 和 all 都是读全表，但 index 是从索引中读取的，而 all 是从硬盘中读取的）
>
>   - ```mysql
>     mysql> explain select id from t1;
>     +----+-------------+-------+-------+---------------+--------+---------+------+------+-------------+
>     | id | select_type | table | type  | possible_keys | key    | key_len | ref  | rows | Extra       |
>     +----+-------------+-------+-------+---------------+--------+---------+------+------+-------------+
>     |  1 | SIMPLE      | t1    | index | NULL          | idx_t1 | 4       | NULL |    2 | Using index |
>     +----+-------------+-------+-------+---------------+--------+---------+------+------+-------------+
>     ```
>
> - all
>
>   - Full Table Scan ，将遍历全表以找到匹配的行
>
>   - ```mysql
>     mysql> explain select * from t1;
>     +----+-------------+-------+------+---------------+------+---------+------+------+-------+
>     | id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra |
>     +----+-------------+-------+------+---------------+------+---------+------+------+-------+
>     |  1 | SIMPLE      | t1    | ALL  | NULL          | NULL | NULL    | NULL |    2 |       |
>     +----+-------------+-------+------+---------------+------+---------+------+------+-------+
>
>     ```
>
>   ​

4）、explain 之 possible_keys 和 key 介绍

> `possible_keys`
>
> 显示可能应用在这张表中的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询实际使用
>
> `key`
>
> 实际使用的索引，如果为 NULL，则没有使用索引

5）、explain 之 key_len 介绍

> 表示索引中使用的字节数，可通过该列计算查询中使用的索引长度。在不损失精确性的情况下，长度越短越好。
>
> key_len 显示的值为索引字段的最大可能长度，并非实际使用长度，即 key_len 是根据表定义计算所得，不是通过表内检索出的。

5）、explain 之 ref 介绍

> 显示索引的那一列被使用了，如果可能的话，是一个常数。哪些列或常量被用于查找索引列上的值。

```mysql
mysql> explain select * from t1,t2 where t1.col1 = t2.col1 and t2.col1 = 'hi';
+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
| id | select_type | table | type | possible_keys | key           | key_len | ref   | rows | Extra       |
+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
|  1 | SIMPLE      | t2    | ALL  | NULL          | NULL          | NULL    | NULL  |    1 | Using where |
|  1 | SIMPLE      | t1    | ref  | idx_col1_col2 | idx_col1_col2 | 768     | const |    2 | Using where |
+----+-------------+-------+------+---------------+---------------+---------+-------+------+-------------+
2 rows in set (0.00 sec)

mysql> explain select * from t1,t2 where t1.col1 = t2.col1;
+----+-------------+-------+------+---------------+---------------+---------+-----------------+------+-------------+
| id | select_type | table | type | possible_keys | key           | key_len | ref             | rows | Extra       |
+----+-------------+-------+------+---------------+---------------+---------+-----------------+------+-------------+
|  1 | SIMPLE      | t2    | ALL  | NULL          | NULL          | NULL    | NULL            |    1 |             |
|  1 | SIMPLE      | t1    | ref  | idx_col1_col2 | idx_col1_col2 | 768     | student.t2.col1 |    1 | Using where |
+----+-------------+-------+------+---------------+---------------+---------+-----------------+------+-------------+

```

5）、explain 之 rows 介绍

> 根据表统计信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数

6）、explain 之 Extra 介绍

> 包含不适合在其他列中显示但十分重要的额外信息

#### 5、索引优化单表案例

① 建表 sql

```sql
create table if not exists article (
	id int(10) unsigned not null primary key auto_increment,
	author_id int(10) unsigned not null,
	category_id int(10) unsigned not null,
	views int(10) unsigned not null,
	comments int(10) unsigned not null,
	title varbinary(255) not null,
	content text not null

);
insert into article (author_id,category_id,views,comments,title,content) values(1,1,1,1,'1','1'),(2,2,1,2,'2','2'),(3,3,1,3,'3','3');
```

② 案例

```sql
-- 查询 category_id 为 1 且 commonts 大于 1 的情况下，views 最多的 author_id
mysql> explain select author_id from article where category_id =1 and comments > 1 order by views desc limit 1;
+----+-------------+---------+------+---------------+------+---------+------+------+-----------------------------+
| id | select_type | table   | type | possible_keys | key  | key_len | ref  | rows | Extra                       |
+----+-------------+---------+------+---------------+------+---------+------+------+-----------------------------+
|  1 | SIMPLE      | article | ALL  | NULL          | NULL | NULL    | NULL |    3 | Using where; Using filesort |
+----+-------------+---------+------+---------------+------+---------+------+------+-----------------------------+

-- 你会发现上面查出来的 type 是 all 并且有 filesort 的问题，所以必须优化



-- 所以我们尝试建立索引去解决问题
mysql> show index from article;         --  发现只有一个主键索引
+---------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table   | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+---------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| article |          0 | PRIMARY  |            1 | id          | A         |           3 |     NULL | NULL   |      | BTREE      |         |               |
+---------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+

-- 建立索引(一般where 后面的查询条件需要建立索引)
create index idx_article_ccv on article(category_id,comments,views)
show index from article;   -- 你就会发现你建的三个索引

-- 建完索引后再次分析
mysql> explain select author_id from article where category_id =1 and comments > 1 order by views desc limit 1;
+----+-------------+---------+-------+-----------------+-----------------+---------+------+------+-----------------------------+
| id | select_type | table   | type  | possible_keys   | key             | key_len | ref  | rows | Extra                       |
+----+-------------+---------+-------+-----------------+-----------------+---------+------+------+-----------------------------+
|  1 | SIMPLE      | article | range | idx_article_ccv | idx_article_ccv | 8       | NULL |    2 | Using where; Using filesort |
+----+-------------+---------+-------+-----------------+-----------------+---------+------+------+-----------------------------+
-- 发现我们建立的索引起作用了， type 也变成了 range, 但是还存在 filesort 的情况

```

单独拎出来说一下：

```sql
-- 上一步，我们建完索引之后发现全表扫描的问题解决了，但是还存在 filesort 的问题
mysql> explain select author_id from article where category_id =1 and comments > 1 order by views desc limit 1;
+----+-------------+---------+-------+-----------------+-----------------+---------+------+------+-----------------------------+
| id | select_type | table   | type  | possible_keys   | key             | key_len | ref  | rows | Extra                       |
+----+-------------+---------+-------+-----------------+-----------------+---------+------+------+-----------------------------+
|  1 | SIMPLE      | article | range | idx_article_ccv | idx_article_ccv | 8       | NULL |    2 | Using where; Using filesort |
+----+-------------+---------+-------+-----------------+-----------------+---------+------+------+-----------------------------+

-- 现在假设我们把 comments = 1，看出现什么情况
mysql> explain select author_id from article where category_id =1 and comments = 1 order by views desc limit 1;
+----+-------------+---------+------+-----------------+-----------------+---------+-------------+------+-------------+
| id | select_type | table   | type | possible_keys   | key             | key_len | ref         | rows | Extra       |
+----+-------------+---------+------+-----------------+-----------------+---------+-------------+------+-------------+
|  1 | SIMPLE      | article | ref  | idx_article_ccv | idx_article_ccv | 8       | const,const |    1 | Using where |
+----+-------------+---------+------+-----------------+-----------------+---------+-------------+------+-------------+
-- 通过对比我们会发现改成等于 1 后，type 是 range ，filesort 的问题也解决了，并且 ref 还指向两个常量，进一步解释：
-- 上面 commonts > 1 我们虽然建了索引 key 显示也用到了索引，但是到了 commonts > 1 这里它表示一个范围，就会导致索引失效，举个简单的例子，category_id=1 我们可以把它看成假如我要去 2 楼找 201 ,你明确告诉我找 201 那是不是我一下就找到了，但是 commonts > 1 就好像你去 3 楼，找一个不是 301 的房间，那我是不是还是要一个一个的去找，所以就会产生 mysql filesort 按照 mysql 自己的方式去找，但是我把 commonts 改成 =1 就不一样了，三个索引都生效，一下子就找到了。因此别看就改了一个等于号，在性能上就有很大的差别。



-- 虽然把 comments 改成 1 解决了问题，但是原 sql 不是要求这么写的，所以还要想办法
-- 我们考虑跳过 comments 重建索引，把原来的索引删了
drop index idx_article_ccv on article;
create index idx_article_cv on article(category_id,views)

-- 然后再执行 sql 语句
mysql> explain select author_id from article where category_id =1 and comments > 1 order by views desc limit 1;
+----+-------------+---------+-------+----------------+----------------+---------+------+------+-------------+
| id | select_type | table   | type  | possible_keys  | key            | key_len | ref  | rows | Extra       |
+----+-------------+---------+-------+----------------+----------------+---------+------+------+-------------+
|  1 | SIMPLE      | article | range | idx_article_cv | idx_article_cv | 4       | NULL |    3 | Using where |
+----+-------------+---------+-------+----------------+----------------+---------+------+------+-------------+
-- 发现解决问题
```

### 四、索引优化



### 五、Mysql 锁机制

#### 1、mysql 锁理论概述

思考一下，数据库有锁，是好事还是坏事？

> 答：有利有弊。先说弊的一面，假如你把整张表都锁了，不能查询不能更新，那么在高并发的环境下，就玩完了。好处的一面，假如说 dba 需要进行数据备份，那么这时候它肯定不会希望再由数据库往数据库写了，所以这时候就可以把整张表锁起来。

锁的定义：

> 锁是计算机协调多个进程或线程并发访问某一资源的机制。
>
> 在数据库中除了传统的计算资源（如：cpu ram i/o 等）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素，因此锁对数据库是非常重要的。

#### 2、锁的分类：

从对数据操作的类型（读\写）分：

> - 读锁（共享锁）：针对同一份数据，多个读操作可以同时进行而不会互相影响
> - 写锁（排他锁）：当前操作没有完成之前，它会阻断其他写锁和读锁。

从对数据操作的粒度分：

> - 表锁
> - 行锁

#### 3、读锁讲解

**表锁特点：**

偏向 MyISAM 存储引擎，开销小，加锁块，无死锁，锁定粒度大，发生锁冲突的概率最高，并发度最低

```sql
create table mylock(
	id int not null primary key auto_increment,
	name varchar(20)
)engine myisam;
insert into mylock(name) values('a');
insert into mylock(name) values('b');
insert into mylock(name) values('c');
insert into mylock(name) values('d');
insert into mylock(name) values('e');
```

**手动增加表锁：**

`lock table 表名字 read(write),表名字2 read(write)`

**查看表上加过的锁：**

`show open tables;`

**释放表锁：**

`unlock tables;`

为 mylock 表加 read 锁

| session1                                                     | session2                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| lock table mylock read       获得表 mylock 的 read 锁定      | 连接终端                                                     |
| select * from  mylock      发现当前 session 可以查询该表记录 | select * from mylock   其他 session 也可以查询该表记录       |
| select * from user     发现当前 session 不能查询其他没有锁定的表（因为你加了一把锁，得把锁释放了，才能查看别的表） | 其他 session 可以查询或更新未锁定的表                        |
| insert into mylock(name) values('e')   发现当前 session 中插入或者更新锁定的表都会提示错误 | 其他 session 插入或者更新锁定表会一直等待获得锁（这就是阻塞等待，你一阻塞等待系统性能是不是就下降了） |
| unlock tables     释放锁                                     | Session2 获得锁，插入操作完成                                |

为 mylock 表加 write 锁

| session1                                                  | session2                                                     |
| --------------------------------------------------------- | ------------------------------------------------------------ |
| lock table mylock write       获得表 mylock 的 write 锁定 | 待 session1 开启写锁后，session2 再连接终端                  |
| 发现当前 session 对锁定表的查询、更新、插入操作都可以执行 | 其他 session 对锁定表的查询被阻塞，需要等待锁的释放\n如果可以，换个别的 id 试下，因为 mysql 会有缓存，第一次查过了，下一次会从缓存中拿，影响锁效果展示 |
| unlock tables     释放锁                                  | Session2 获得锁，插入操作完成                                |

结论：

> MyISAM 在执行查询（select）语句前，会自动给涉及的所有表加读锁，在执行增删改操作前，会自动给涉及的表加写锁。
>
> 所以对 MyISAM 表进行操作，会有以下情况：
>
> 1、对 MyISAM 表的读操作（加读锁），不会阻塞其他进程对同一表的读请求，但会阻塞对同一表的写请求。只有当读锁释放后，才会执行其他进程的写操作
>
> 2、对 MyISAM 表的写操作（加写锁），会阻塞其他进程对同一表的读和写操作，只有当写锁释放后，才会执行其他进程的读写操作
>
> 简言之：
>
> 读锁会阻塞写，但是不会阻塞读。而写锁会把读和写都阻塞

**表锁分析：**

可以通过检查 `Table_locks_immediate` 和 `Table_locks_waited` 状态变量来分析系统上的表锁定：

> show status like 'table%'
>
> +-----------------------+-------+
> | Variable_name         | Value |
> +-----------------------+-------+
> | Table_locks_immediate | 86    |
> | Table_locks_waited    | 0     |
> +-----------------------+-------+
>
> 这两个状态变量记录 MySQL 内部表级锁定的情况：
>
> - Table_locks_immediate：产生表级锁定的次数，表示可以立即获取锁的查询次数，每立即获取锁值加 1
> - Table_locks_waited ：出现表级锁定争用而发生等待的次数（每等待一次，锁值加 1），此值高说明存在着较严重的表级锁争用情况
>
> myisam 的读写锁调度是写优先，这也是 Myisam 不适合做写为主表的引擎。因为写锁后，其他线程不能做任何操作，大量的更新会使查询很难得到锁，从而造成永远阻塞。

#### 4、行锁讲解

行锁的特点：

偏向 innoDB 引擎，开销大，加锁慢；会出现死锁，锁定粒度最小，发生锁冲突的概率最低，并发度也最高。

InnoDB 与 MyISAM 最大不同：一是支持事务，而是采用行锁

行锁案例：

```sql
-- 建表语句
create table test_innodb_lock(a int(11),b varchar(16))engine=innodb;

insert into test_innodb_lock values(1,'b2');
insert into test_innodb_lock values(3,'3');
insert into test_innodb_lock values(4,'4000');
insert into test_innodb_lock values(5,'5000');
insert into test_innodb_lock values(6,'6000');
insert into test_innodb_lock values(7,'7000');
insert into test_innodb_lock values(8,'8000');
insert into test_innodb_lock values(9,'9000');
insert into test_innodb_lock values(1,'b1');

create index idx_a on test_innodb_lock(a);
create index idx_b on test_innodb_lock(b);


```

行锁定演示：

| session1                                                     | session2                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| set autocommit=0;    把 mysql 自动提交关掉<br />select @@autocommit;   发现值是 0，证明已经关掉 | set autocommit=0;    把 mysql 自动提交关掉<br />select @@autocommit;   发现值是 0，证明已经关掉 |
| update test_innodb_lock set b = '4002' where a = 4;<br />更新但不提交，没有手动写 commit | update test_innodb_lock set b = '4003' where a = 4;<br /> 发现 session2 被阻塞只能等待 |
| commit; <br />提交更新                                       | 会发现解除阻塞，更新正常执行                                 |
|                                                              | commit;   <br />把 session2 刚刚的更新提交                   |
| update test_innodb_lock set b = '4002' where a = 4;<br />更新但不提交，没有手动写 commit | update test_innodb_lock set b = '9001' where a = 9;<br /> 操作的不是同一条数据，发现可以正常执行不会阻塞 |

说一个问题：

> 假设你现在开两个 session 并且把两个 session 中 都设置了  set autocommit=0 自动提交都关了，然后现在我在 session1 中做一个 update 操作，然后 commit ，按道理说我 commit 之后别的 session 也就可以看到结果了，但是 session2 中你会发现还是原来的值，这是因为 session2 你设置了 autocommit=0 ，这时候你需要在  session2 中再 commit 一次，才会看到结果。但是如果你再开一个 session3 ，默认 autocommit=1 是可以直接看到结果的，不需要 commit

**索引失效行锁变表锁：**

| session1                                                     | session2                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 创建表的时候给字段创建了索引，b 字段是 varchar 类型<br />现在我执行 update test_innodb_lock set a = 55 where b = 5000; | update test_innodb_lock set a = 66 where b = '6001'   按道理说操作的不是同一条记录，session2 的这个操作应该也是成功的，但发现阻塞了，原因就是 session 中 b 为 varchar 类型，但是操作的时候使用的是 int 类型，虽说 mysql 底层会做类型转换，可以更新成功，但是会造成索引失效，索引失效后行锁变表锁，造成阻塞 |

**间隙锁危害：**

| session1                                                     | session2                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 我原始表中没有 2 这条记录<br />update test_innodb_lock set b = '200' where a > 1 and a <5; | insert into test_innodb_lock values(2,'2000');<br />按道理说 innodb 是行锁，我操作一个根本就不存在的数据，应该是可以成功的，但发现阻塞了。 |
| commit;                                                      | 阻塞解除，完成插入                                           |

意思就是在这个范围内不管三七二十都会给你锁住。

间隙锁：

> 当我们用范围条件而不是相等条件检索数据， innodb 会给符合条件的已有数据记录的索引项加锁，对于键值在条件范围内，但并不存在的记录叫间隙。
>
> innodb 也会对这个间隙加锁，这种锁机制就是所谓的间隙锁

危害：

> 检索锁一个比较致命的危害，就是当锁定一个范围的键值以后，即使某些不存在的键值也会被无辜锁定，而造成在锁定时无法插入锁定范围内的任何数据。某些场景下会对性能造成影响。

#### 5、如何手动锁定一行

```sql
-- session1
begin;
select * from test_innodb_lock where a = 6 for update;    -- 关键要加 for update

--session2
update test_innodb_lock set b = '6004' where a = 6;  -- 这时候我操作这行记录就阻塞了，必须要等 session1 commit 之后才会执行这条 sql 语句

-- session2 阻塞期间 session1 可以做自己需要做的事情，可以保证 a = 6 这条记录不会被别人修改
```

**行锁分析：**

> mysql> `show status like '%innodb_row_lock%';`
> +-------------------------------+--------+
> | Variable_name                 | Value  |
> +-------------------------------+--------+
> | Innodb_row_lock_current_waits | 0      |
> | Innodb_row_lock_time          | 184164 |
> | Innodb_row_lock_time_avg      | 30694  |
> | Innodb_row_lock_time_max      | 51156  |
> | Innodb_row_lock_waits         | 6      |
> +-------------------------------+--------+
>
> - Innodb_row_lock_current_waits ：当前正在等待锁定的数量
> - Innodb_row_lock_time：从系统启动到现在锁定总时间长度
> - Innodb_row_lock_time_avg ：每次等待所话平均时间
> - Innodb_row_lock_time_max：从系统启动到等待最长的一次所花时间
> - Innodb_row_lock_waits：系统启动后到现在总共等待的次数













































































