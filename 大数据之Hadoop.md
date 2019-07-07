# 大数据之 Hadoop

## 一、大数据概论

### 1、大数据概念

大数据（Big Data）是指无法在一定时间范围内用常规软件工具进行捕捉、管理、和处理数据的集合，是需要新处理模式才能具有更强的决策力、洞察发现力和流程优化能力的海量、高增长率和多样化的信息资产。

其实大数据主要解决的就是两个事：

> 1、海量数据的存储
>
> 2、海量数据的分析计算

**注：**

> 这个常规软件工具指的就是我们之前使用的 JavaEE 技术，比如说 mysql 、 mybatis ，mysql 中大概能存 500-1000 万条数据，但是大数据一般都是亿级别的，所以这个数据量 mysql 根本存不了，就算存了，检索效率也非常低。

### 2、大数据特点

> - Volume （大量）
>   - 产生的数据量很大
> - Velocity （高速）
>   - 产生数据的速度很快
> - Variety （多样）
>   - 这种类型的多样性也让数据被分为结构化数据和非结构化数据。相对于以往便于存储的以数据库/文本为主的结构化数据，非结构化数据越来越多，包括网络日志、音频、图片、地理位置信息等，这些多类型的数据对数据的处理能力提出了更高的要求
> - Value （低价值密度）
>   - 价值密度的高低与数据总量的大小成反比。比如说警察抓小偷，他只关心监控中那几分钟的视频，如何快速对有价值数据提纯，成为目前大数据背景下待解决的难题。

### 3、大数据应用场景

> - 物流仓储：大数据分析系统助力商家精细化运营、提升销量、节约成本
> - 零售：分析用户消费习惯，为用户购买商品提供方便，从而提升商品销量
> - 旅游：深度结合大数据能力与旅游行业需求，共建旅游产业智慧管理、智慧服务和智慧营销的未来
> - 商品广告推荐：给用户推荐可能喜欢的商品

## 二、从 Hadoop 框架讨论大数据生态

#### 1、Hadoop 是什么

> - Hadoop 是一个由 Apache 基金会所开发的分布式系统基础架构
> - 主要解决海量数据的存储和海量数据的分析计算问题
> - 广义上来说，Hadoop 通常是指一个更广泛的概念-Hadoop 生态圈

#### 2、Hadoop 发展历史

> 1. Lucene 框架是 『Doug Cutting』开创的开源软件，用 Java 编写，实现与 Google 类似的全文搜索功能，它提供了全文搜索引擎
> 2. 2001 年底 Lucene 成为 Apache 基金会的一个子项目
> 3. 对于海量数据的场景，Luncene 面对与 Google 同样的困难，*存储数据困难，检索速度慢*
> 4. 学习和模仿 Google 解决这些问题的办法：微型版 Nutch
> 5. 2003-2004 年，Google 公开了部分 GFS 和 MapReduce 思想的细节，以此为基础 『Doug Cutting』等人用了两年业余时间实现了 DFS 和 MapReduce 机制，使 Nutch 性能飙升
> 6. 2005 年 Hadoop 作为 Luncene 的子项目 Nutch 的一部分正式引入 Apache 基金会
> 7. 2006 年 3 月份， Map-Reduce 和 Nutch Distributed File System（NDFS）分别被纳入成为 Hadoop 的项目中
> 8.  Hadoop 的 logo 来源于 『Doug Cutting』 儿子的玩具大象
> 9. Hadoop 就此诞生并迅速发展，标志的大数据时代来临

#### 3、Hadoop 三大发行版本

Hadoop 三大发行版本：『Apache』 、『Cloudera』 、『Hortonworks』

> - Apache 版本是最原始的版本，对于入门学习最好
> - Cloudera 在大型互联网企业中用的较多
> - Hortonworks 文档较好

#### 4、Hadoop 的优势

> - 高可靠性
>   - Hadoop 底层维护了多个数据副本，所以即使 Hadoop 某个计算元素或存储出现故障，也不会导致数据的丢失
> - 高扩展性
>   - 在集群间分配任务数据，可方便的扩展数以千计的节点
> - 高效性
>   - 在 MapReduce 思想下，Hadoop 是并行工作的，以加快任务处理速度
> - 高容错性
>   - 能够自动将失败的任务重新分配

#### 5、Hadoop1.X 和 Hadoop2.X 的区别

> 在 Hadoop1.x 时代，Hadoop 中的 MapReduce 同时处理业务逻辑运算和资源的调度，耦合性较大。在 Hadoop2.x 时代，增加了 Yarn。Yarn 只负责资源的调度，MapReduce 只负责运算。

#### 6、Hadoop 组成

1）、HDFS 架构概述

> - NameNode（nn）：存储文件的元数据。如：文件名，文件的目录结构，文件属性（生成时间、副本数、文件权限），以及每个文件的块列表和块所在的 DataNode 等
> - DataNode（dn）：在本地文件系统存储文件块数据，以及块数据的校验和
> - Secondary NameNode（2nn）：用来监控 HDFS 状态的辅助后台程序，每隔一段时间获取 HDFS 元数据的快照

2）、YARN 架构概述



![YARN架构图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/YARN%E6%9E%B6%E6%9E%84%E5%9B%BE.png?q-sign-algorithm=sha1&q-ak=AKID7zjHweurnnEHqmB67L0Ulw3tmNnc1stn&q-sign-time=1562505220;1562508820&q-key-time=1562505220;1562508820&q-header-list=&q-url-param-list=&q-signature=f3a341a0b07d3942d69726f7bce10108d802398c&x-cos-security-token=07e7a2523678a7b4827d1e88033249ac722dfdf110001)



3）、MapReduce 架构概述

> MapReduce 将计算过程分为两个阶段：Map 和 Reduce
>
> ① Map 阶段并行处理输入数据
>
> ② Reduce 阶段对 Map 结果进行汇总

#### 7、大数据生态体系

![大数据生态体系](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/%E5%A4%A7%E6%95%B0%E6%8D%AE%E6%8A%80%E6%9C%AF%E7%94%9F%E6%80%81%E4%BD%93%E7%B3%BB.png?q-sign-algorithm=sha1&q-ak=AKIDMdaImNPsQyFY1Wyji7gklhzPznStrxiD&q-sign-time=1561905423;1561909023&q-key-time=1561905423;1561909023&q-header-list=&q-url-param-list=&q-signature=6f457ff3ea0ede5559881ca238b08d715bad91ea&x-cos-security-token=882c51851a53b266d347fa480a8ca1d59ea8742f10001)

#### 8、hadoop 安装

**步骤：**

```
① 先拷贝 hadoop 的 tar 包（hadoop-2.7.2.tar.gz）至服务器指定目录下
假设把 hadoop-2.7.2.tar.gz 放在 『/usr/local/module』 目录下
② 将 tar 包解压，解压完成之后进入 hadoop 目录
cd hadoop-2.7.2
③ 获取 hadoop 的安装路径
pwd
『/usr/local/module/hadoop-2.7.2』
④ 编辑环境变量配置文件
vim /etc/profile
⑤ 配置 hadoop 环境变量
export HADOOP_HOME=/usr/local/module/hadoop-2.7.2
export PATH=$HADOOP_HOME/bin:$PATH
export PATH=$HADOOP_HOME/sbin:$PATH
⑥ 验证
使用 hadoop 命令，出现下面一大段描述字，表示环境变量配置成功
```

```shell
[root@hadoop1 hadoop-2.7.2]# hadoop

Usage: hadoop [--config confdir] [COMMAND | CLASSNAME]
  CLASSNAME            run the class named CLASSNAME
 or
  where COMMAND is one of:
  fs                   run a generic filesystem user client
  version              print the version
  jar <jar>            run a jar file
                       note: please use "yarn jar" to launch
                             YARN applications, not this command.
  checknative [-a|-h]  check native hadoop and compression libraries availability
  distcp <srcurl> <desturl> copy file or directories recursively
  archive -archiveName NAME -p <parent path> <src>* <dest> create a hadoop archive
  classpath            prints the class path needed to get the
  credential           interact with credential providers
                       Hadoop jar and the required libraries
  daemonlog            get/set the log level for each daemon
  trace                view and modify Hadoop tracing settings

Most commands print help when invoked w/o parameters.

```

**注意：**

> 在安装 hadoop 之前要配置主机名和 ip 的映射关系，否则在后面使用 hadoop 过程中会出现很多问题
>
> `vim /etc/hosts`
>
> 使用上面命令，配置好主机名和 ip 的映射关系

## 三、Hadoop 运行模式

Hadoop 运行模式包括，本地模式、伪分布式模式、完全分布式模式

#### 1、本地模式 grep 官方案例

```shell
mkdir input
cp etc/hadoop/*.xml input/
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar grep input output 'dfs[a-z.]+'
cd output
cat part-r-00000
```

#### 2、本地模式 WordCount 官方案例

**步骤：**

① 在 hadoop 目录下创建一个 `wcinput` 文件夹

```shell
[root@hadoop1 hadoop-2.7.2]# mkdir wcinput
```

② 在 wcinput 文件夹下创建一个 wc.input 文件，并在里面编辑内容

````shell
vim wc.input

heping kevin James curry Jordan
heping
````

③ 回退到 hadoop 目录下执行程序

```shell
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount wcinput/ wcoutput
```

④ 执行结果

```
[root@hadoop1 wcoutput]# cat part-r-00000 
James	1
Jordan	1
curry	1
heping	2
kevin	1
```

#### 3、伪分布式-启动 HDFS 并 运行 MR 程序

要想完成启动 HDFS 并运行 MR 程序，主要需要经过下面三个步骤：

> 1、配置集群
>
> 2、启动、测试集群增、删、查
>
> 3、执行 WordCount 案例

1）、配置集群

① 配置 `hadoop-env.sh`

> 在这个文件中主要是为了去修改 `JAVA_HOME` 
>
> ```shell
> echo $JAVA_HOME
> /usr/local/java/jdk1.8.0_144     # 先看下系统中 JAVA_HOME 的位置
>
> cd hadoop-2.7.2/etc/hadoop
> vim hadoop-env.sh
> 『
> export JAVA_HOME=${JAVA_HOME}
> # 把这里改成 export JAVA_HOME=/usr/local/java/jdk1.8.0_144 
> 』
> 然后保存退出
> ```
>
> 

② 配置 `core-site.xml`

> ```shell
> cd hadoop-2.7.2/etc/hadoop
> vim core-site.xml 
> 『
> configuration>
>         <!-- 指定HDFS中NameNode的地址 -->
>         <property>
>                 <name>fs.defaultFS</name>
>                  <value>hdfs://hadoop1:9000</value>
>         </property>
>
>         <!-- 指定Hadoop运行时产生文件的存储目录 -->
>         <property>
>                 <name>hadoop.tmp.dir</name>
>                 <value>/usr/local/module/hadoop-2.7.2/data/tmp</value>
>         </property>
> </configuration>
> 』
> ```
>
> 

③ 配置 `hdfs-site.xml`

> ```shell
> cd hadoop-2.7.2/etc/hadoop
> vim hdfs-site.xml
> 『
> <configuration>
>         <!-- 指定HDFS副本的数量 -->
>         <property>
>                 <name>dfs.replication</name>
>                 <value>1</value>
>         </property>
> </configuration>
> 』
> ```
>
> 

2）、启动集群

① 格式化 `NameNode` （第一次启动时需要格式化，以后就不需要了）

> ```shell
> cd hadoop-2.7.2/bin
> hdfs namenode -format      # 格式化 NameNode
> # 在格式化过程中，如果有任何提示，比如已经格式化过，是否重新格式化，这时候你就需要重新格式化
> ```

**注意：**

> 格式化 NameNode ，会产生新的集群 id，导致 NameNode 和 DataNode 的集群 id 不一致，集群找不到以往的数据。所以，格式化 NameNode 时，一定要先删除 data 数据和 log 日志，然后再格式化 NameNode

② 启动 `NameNode`

> ```shell
> cd hadoop-2.7.2/sbin
> hadoop-daemon.sh start namenode    # 启动 NameNode
> jps
> 9233 NameNode      # 证明启动成功
> 9321 Jps
> ```
>
> 

③ 启动 `DataNode`

> ```shell
> cd hadoop-2.7.2/sbin
> hadoop-daemon.sh start datanode      # 启动 DataNode
> jps
> 9233 NameNode
> 9540 Jps
> 9469 DataNode     # 证明启动成功
> ```
>
> 

3）、查看集群

① 查看是否启动成功

> ```shell
> jps
> 9233 NameNode
> 9540 Jps
> 9469 DataNode     # 证明启动成功
> ```
>
> 

② web 端查看 HDFS 文件系统

> `http://192.168.148.141:50070`

4）、操作集群

① 在 HDFS 文件系统上创建一个 input 文件夹

> `./bin/hdfs dfs -mkdir -p /usr/heping/input`

② 将测试文件内容上次到文件系统上

> `./bin/hdfs dfs -put wcinput/wc.input /user/heping/input`
>
> 第一个路径是测试文件在本地的路径，第二个路径是 HDFS 文件系统上的路径

③ 运行 MapReduce 程序，执行 WordCount 案例

> `./bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /user/heping/input/ /user/heping/output`
>
> 前后两个输入和输出的路径都是 HDFS 文件系统上的路径

④ 查看输出结果

> `./bin/hdfs dfs -cat /user/heping/output/p*`
> James	1
> Jordan	1
> curry	1
> heping	2
> kevin	1

DataNode 和 NameNode 进程同时只能有一个工作的问题分析：

![DataNode 和 NameNode问题分析](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/DataNode%E5%92%8CNameNode%E9%97%AE%E9%A2%98%E5%88%86%E6%9E%90.png?q-sign-algorithm=sha1&q-ak=AKIDY4ec1O3DSZtWqNFGx2voSAjJbpWGViUE&q-sign-time=1562505102;1562508702&q-key-time=1562505102;1562508702&q-header-list=&q-url-param-list=&q-signature=17e2c31f9c69751087ecb859f94ee7b2f4120f77&x-cos-security-token=c01cb0f51a31f23e3fa28e6cb6f045513fe8368810001)

#### 4、启动 YARN 并运行 MR 程序

要想完成启动 YARN 并运行 MR 程序，需要经过下面几个步骤：

> - 配置集群
> - 启动测试集群增、删、查
> - 在 YARN 上执行 WordCount 案例

1）、配置集群

① 配置 `yarn-env.sh`

```shell
echo $JAVA_HOME
/usr/local/java/jdk1.8.0_144     # 先看下系统中 JAVA_HOME 的位置

cd hadoop-2.7.2/etc/hadoop
vim yarn-env.sh
『
export JAVA_HOME=xxxxxxxxx
# 把这里改成 export JAVA_HOME=/usr/local/java/jdk1.8.0_144 
』
然后保存退出
```

② 配置 `yarn-site.xml` 

```Xml
cd hadoop-2.7.2/etc/hadoop
vim yarn-site.xml	
『
<configuration>
<!-- Site specific YARN configuration properties -->
        <!-- Reducer获取数据的方式 -->
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>

        <!-- 指定YARN的ResourceManager的地址 -->
        <property>
                <name>yarn.resourcemanager.hostname</name>
                <value>hadoop1</value>
        </property>
</configuration>

』
```

③ 配置 `mapred-env.sh`

```shell
echo $JAVA_HOME
/usr/local/java/jdk1.8.0_144     # 先看下系统中 JAVA_HOME 的位置

cd hadoop-2.7.2/etc/hadoop
vim mapred-env.sh
『
# export JAVA_HOME=/home/y/libexec/jdk1.6.0/
# 把这里改成 export JAVA_HOME=/usr/local/java/jdk1.8.0_144 
』
然后保存退出
```

④ 将 `mapred-site.xml.template` 重命名为 `mapred-site.xml`

```shell
cd hadoop-2.7.2/etc/hadoop
mv mapred-site.xml.template mapred-site.xml
vim  mapred-site.xml
『
<configuration>
        <!-- 指定MR运行在YARN上 -->
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
</configuration>
』
保存退出
```

2）、启动集群

① 启动前必须保证 NameNode 和 DataNode 已经启动

② 启动 ResourceManager 

```shell
cd hadoop-2.7.2/sbin
yarn-daemon.sh start resourcemanager  # 启动 ResourceManager
[root@hadoop1 sbin]# jps
6387 Jps
5638 NameNode
5757 DataNode
6174 ResourceManager    # 证明启动成功
```

③ 启动 NodeManager

```shell
cd hadoop-2.7.2/sbin
yarn-daemon.sh start nodemanager  # 启动 NodeManager
[root@hadoop1 sbin]# jps
5638 NameNode
6471 NodeManager    # 证明启动成功
6552 Jps
5757 DataNode
6174 ResourceManager
```

3）、查看集群

在浏览器查看 YARN ：

> `http://192.168.148.141:8088`

4）、操作集群

① 删除文件系统上的 output 文件夹

> cd hadoop-2.7.2/bin
>
> `hdfs dfs -rm -r /user/heping/output`

② 运行 MapReduce 程序，执行 WordCount 案例

> `bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /user/heping/input/ /user/heping/output`
>
> 前后两个输入输出路径都是 hdfs 文件系统上的路径

③ 查看运行结果

> `bin/hdfs dfs -cat /user/heping/output/p*`
> James	1
> Jordan	1
> curry	1
> heping	2
> kevin	1



















