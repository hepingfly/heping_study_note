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

> 格式化 NameNode ，会产生新的集群 id，导致 NameNode 和 DataNode 的集群 id 不一致，集群找不到以往的数据。所以，格式化 NameNode 时，一定要先删除 data 数据和 log 日志，然后再格式化 NameNode（PS：格式化之前一定要先停止上次启动的所有 namenode 和 datanode 进程，然后再删除 data 和 log 数据）

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

#### 5、配置历史服务器

为了查看程序的历史运行情况，需要配置一下历史服务器。

1）、配置 `mapred-site.xml`

```xml
cd hadoop-2.7.2/etc/hadoop
vim mapred-site.xml	
『
<configuration>
        <!-- 历史服务器端地址 -->
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>hadoop1:10020</value>
        </property>
        <!-- 历史服务器web端地址 -->
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>hadoop1:19888</value>
        </property>
</configuration>
』
```

2）、启动历史服务器

```shell
cd hadoop-2.7.2/sbin
mr-jobhistory-daemon.sh start historyserver
```

3）、查看历史服务器是否启动

```shell
jps
4740 NodeManager
4965 JobHistoryServer    # 证明历史服务器启动了
4485 ResourceManager
5001 Jps
4202 NameNode
4300 DataNode
```

4）、web 端查看 JobHistory

> `http://192.168.148.141:19888`

#### 6、配置日志聚集

日志聚集概念：应用运行完成以后，将程序运行日志信息上传到 HDFS 系统上。

**注意：**

> 开启日志聚集功能，需要重新启动 `NodeManager` 、`ResourceManager` 和 `HistoryManager` 

具体步骤：

1）、配置  `yarn-site.xml` 

```shell
cd /hadoop-2.7.2/etc
vim yarn-site.xml
『
<configuration>
        <!-- 日志聚集功能使能 -->
        <property>
                <name>yarn.log-aggregation-enable</name>
                <value>true</value>
        </property>
        <!-- 日志保留时间设置7天 -->
        <property>
                <name>yarn.log-aggregation.retain-seconds</name>
                <value>604800</value>
        </property>
</configuration>
』
```

2）、关闭 NodeManager 、ResourceManager、HistoryManager

```shell
cd hadoop-2.7.2/sbin
yarn-daemon.sh stop nodemanager    # 关闭 NodeManager
yarn-daemon.sh stop resourcemanager  # 关闭 ResourceManager
mr-jobhistory-daemon.sh stop historyserver   # 关闭 Historyserver
jps
7321 Jps
4202 NameNode
4300 DataNode
```

3）、启动 NodeManager 、ResourceManager、HistoryManager

```shell
cd hadoop-2.7.2/sbin
yarn-daemon.sh start nodemanager    # 启动 NodeManager
yarn-daemon.sh start resourcemanager  # 启动 ResourceManager
mr-jobhistory-daemon.sh start historyserver   # 启动 Historyserver
```

4）、删除 hdfs 上已经存在的文件输出目录

```shell
cd hadoop-2.7.2/bin
hdfs dfs -rm -f /user/heping/output
```

5）、执行 wordCount 程序

```shell
cd hadoop-2.7.2
hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /user/heping/input /user/heping/output
```

6）、查看日志

```shell
http://192.168.148.141:19888
```

7、集群分发脚本

```bash
#!/bin/bash
#1 获取输入参数个数，如果没有参数，直接退出
pcount=$#
if((pcount==0)); then
echo no args;
exit;
fi

#2 获取文件名称
p1=$1
fname=`basename $p1`
echo fname=$fname

#3 获取上级目录到绝对路径
pdir=`cd -P $(dirname $p1); pwd`
echo pdir=$pdir

#4 获取当前用户名称
user=`whoami`

#5 循环
for((host=142; host<144; host++)); do
        echo ------------------- hadoop$host --------------
        rsync -rvl $pdir/$fname $user@192.168.148.$host:$pdir
done
```

#### 7、集群配置

1）、集群部署规划

|      | 192.168.148.141        | 192.168.148.142                  | 192.168.148.143                 |
| ---- | ---------------------- | -------------------------------- | ------------------------------- |
| HDFS | NameNode<br />DataNode | DataNode                         | SecondaryNameNode<br />DataNode |
| YARN | NodeManager            | ResourceManager<br />NodeManager | NodeManager                     |

上面表格说明：

> 首先有三台服务器（141、142、143），在这三台服务器上我们要去配置  HDFS 和 YARN。首先看 HDFS ，它包括 NameNode、DataNode、SecondaryNameNode ，其次你要知道 NameNode 和 SecondaryNameNode 它们的内存占比基本上就是 1:1 。比如说你服务器内存 128G ，你如果把 NameNode 和 SecondaryNameNode 配置在一台机器上，那么它们各自只能占 64G，这样就会严重影响集群性能。所以应该把这两个放在不同的机器上。再来看 YARN 的配置，YARN 包括 NodeManager 和 ResourceManager 。这个 ResourceManger 是整个集群资源的老大，它也非常占用内存，所以不能和 NameNode、SecondaryNameNode 在一台机器上。

2）、集群配置步骤

PS：下面所有的配置都在一台机器上配置好，然后通过集群分发脚本将所有配置同步到另外两台机器上

① 核心配置文件

配置 `core-site.xml`

```xml
vim hadoop-2.7.2/etc/hadoop/core-site.xml
<configuration>
        <!-- 指定HDFS中NameNode的地址 -->
        <property>
                <name>fs.defaultFS</name>
                 <value>hdfs://192.168.148.141:9000</value>
        </property>

        <!-- 指定Hadoop运行时产生文件的存储目录 -->
        <property>
                <name>hadoop.tmp.dir</name>
                <value>/usr/local/module/hadoop-2.7.2/data/tmp</value>
        </property>
</configuration>
```

② HDFS 的配置文件

配置 `hadoop-env.sh`

```bash
vim hadoop-2.7.2/etc/hadoop/hadoop-env.sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_144
```

配置 `hdfs-site.xml`

```xml
vim hadoop-2.7.2/etc/hadoop/hdfs-site.xml
<configuration>
        <!-- 指定HDFS副本的数量 -->
        <property>
                <name>dfs.replication</name>
                <value>3</value>
        </property>
        <!-- 指定Hadoop辅助名称节点主机配置 -->
        <property>
                <name>dfs.namenode.secondary.http-address</name>
                 <value>192.168.148.143:50090</value>
        </property>
</configuration>
```

③ YARN 的配置文件

配置 `yarn-env.sh`

```bash
vim hadoop-2.7.2/etc/hadoop/yarn-env.sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_144
```

配置 `yarn-site.xml`

```xml
vim hadoop-2.7.2/etc/hadoop/yarn-site.xml
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
                <value>192.168.148.142</value>
        </property>
        <!-- 日志聚集功能使能 -->
        <property>
                <name>yarn.log-aggregation-enable</name>
                <value>true</value>
        </property>
        <!-- 日志保留时间设置7天 -->
        <property>
                <name>yarn.log-aggregation.retain-seconds</name>
                <value>604800</value>
        </property>
</configuration>

```

④ MapReduce 配置文件

配置 `mapred-env.sh`

```bash
vim hadoop-2.7.2/etc/hadoop/mapred-env.sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_144
```

配置 `mapred-site.xml`

```xml
vim hadoop-2.7.2/etc/hadoop/mapred-site.xml
<configuration>
        <!-- 指定MR运行在YARN上 -->
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
        <!-- 历史服务器端地址 -->
        <property>
                <name>mapreduce.jobhistory.address</name>
                <value>192.168.148.141:10020</value>
        </property>
        <!-- 历史服务器web端地址 -->
        <property>
                <name>mapreduce.jobhistory.webapp.address</name>
                <value>192.168.148.141:19888</value>
        </property>
</configuration>
```

以上是集群中一台服务器节点的配置，配置完成后就可以使用集群分发脚本将配置同步到另外两台机器上。

#### 8、集群单节点启动

1）、如果集群是第一次启动，需要格式化 NameNode

注意：

格式化 NameNode 时，一定要先删除 data 数据和 log 日志

```shell
cd hadoop-2.7.2/bin
hdfs namenode -format      # 格式化 NameNode
```

2）、在 192.168.148.141 上启动 NameNode 和 DataNode

```shell
cd hadoop-2.7.2/sbin
./hadoop-daemon.sh start namenode   # 启动 namenode
./hadoop-daemon.sh start datanode   # 启动 datanode
[root@hadoop1 sbin]# jps
7893 NameNode
8008 DataNode
8107 Jps
```

3）、在 192.168.148.142 上启动 DataNode

```shell
cd hadoop-2.7.2/sbin
./hadoop-daemon.sh start datanode   # 启动 datanode
[root@hadoop2 sbin]# jps
8035 DataNode
8119 Jps
```

4）、在 192.168.148.143 上启动 DataNode

```shell
cd hadoop-2.7.2/sbin
./hadoop-daemon.sh start datanode   # 启动 datanode
[root@hadoop3 sbin]# jps
6112 Jps
6077 DataNode
```

**思考：**

如果我每次都是一个一个节点启动，如果节点数增加到 1000 个怎么办？

#### 9、集群 ssh 配置

1）、ssh 免密登录原理

![ssh免密登录原理](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/ssh%E5%85%8D%E5%AF%86%E7%99%BB%E5%BD%95%E5%8E%9F%E7%90%86.png?q-sign-algorithm=sha1&q-ak=AKID3i5rIVmNuulYgI3dTi7FXIoW8lcogEUH&q-sign-time=1563116545;1563120145&q-key-time=1563116545;1563120145&q-header-list=&q-url-param-list=&q-signature=182c626de3b2a6cf4e281ea2faeb0db763111183&x-cos-security-token=ce9cc00c31cdd2b5d019e935c23a84b08aa110f410001)

2）、生成公钥和私钥

```shell
首先进入到当前用户的家目录
cd ~
ls -la     # 这个命令可以看到在当前目录下有一个 .ssh 的文件夹
cd .ssh
ssh-keygen -t rsa   # 生成对应的公钥和私钥
# -t rsa 表示使用 rsa 加密算法
# 上面的命令输入完成之后，敲三个回车就会生成两个文件 id_rsa(私钥)    id_rsa.pub(公钥)
ll
-rw-------. 1 root root 1675 7月  14 23:15 id_rsa
-rw-r--r--. 1 root root  394 7月  14 23:15 id_rsa.pub
-rw-r--r--. 1 root root  397 7月  14 22:37 known_hosts
```

3）、将公钥拷贝到要免密登录的目标机器上

```shell
# 我现在需要把公钥拷贝到 192.168.148.142 和 192.168.148.143 这两台机器上
# 当前机器是 192.168.148.141
pwd
/root/.ssh    # 当前用户家目录的 .ssh 文件夹下
ssh-copy-id 192.168.148.141      # 自己这台机器也配置下，不然使用 ssh 自己登录自己也需要输入密码  
ssh-copy-id 192.168.148.142
ssh-copy-id 192.168.148.143

# 现在我去 192.168.148.142 这台机器的 『/root/.ssh』 目录下
[root@hadoop2 .ssh]# ll
总用量 8
-rw-------. 1 root root 394 7月  14 23:30 authorized_keys  # 会生成一个这个文件，这个文件的内容就是 192.168.148.141 机器的公钥 
-rw-r--r--. 1 root root 397 7月  14 22:37 known_hosts

```

上面的步骤展示了使用公钥和私钥实现免密登录到目标机器上，总结一下：

> 比如说我当前机器是 141 ，然后我想通过 ssh 免密登录到 142 和 143 的机器上，那么我的做法就是，先在 141 机器上生成一对公钥和私钥，然后把公钥拷贝到 142 和 143 机器上，这样就实现了 ssh 免密登录。拷贝后，142 和 143 机器都会生成一个 authorized_keys 文件，里面是 141 的公钥，141 想免密登录 142 和 143 都是通过这个文件来实现的。
>
> 那么假如我现在所在机器是 142，我想免密登录 141 和 143 ，你发现是不行的。那么做法就是现在 142 上生成一对公钥和私钥，然后把 142 的公钥拷贝到 141 和 143 上，这样才能实现免密登录。

上面我们配置免密登录时因为 192.168.148.141 上有 NameNode ，它需要和 142、143 进行通信，需要免密登录。同时 192.168.148.142 上有 ResourceManager 它也需要和另外两台机器进行通信，所以也需要免密登录，配置方式和上述步骤类似。

#### 10、集群群起

上面之所以要配置一下 ssh 其实是为我们群起集群服务的。

1）、配置 slaves 

```shell
cd hadoop-2.7.2/etc/hadoop
vim slaves           # 首先删除里面自带的 localhost
192.168.148.141      # 把 datanode 所在的节点都配置在这里
192.168.148.142
192.168.148.143
```

**注意：**

> slaves 这个文件开头和结尾不允许有空格，文件中也不允许有空行

上面的配置只是在一台服务器节点上配置，配置好之后我们就可以使用集群分发脚本同步到另外两台机器上。

2）、启动集群

①如果集群是第一次启动需要格式化 NameNode（格式化之前一定要先停止上次启动的所有 NameNode 和 DataNode 进程，然后再删除 data 和 log 数据）

> 格式化 namenode 命令前面有说过
>

② 启动 HDFS

```shell
cd hadoop-2.7.2/sbin
./start-dfs.sh
jps
4660 NameNode
7878 Jps
4794 DataNode

# 这样三台机器上的 namenode 和 datanode secondarynamenode 都会被启动起来
```

③ 启动 YARN

```shell
cd hadoop-2.7.2/sbin
./start-yarn.sh
```

**注意：**

> NameNode 和 ResourceManager 如果不是在同一台机器，不能在 NameNode 上启动 YARN，应该在 ResourceManager 上启动 YARN

#### 11、集群文件存储路径说明

我们可以使用命令向 hdfs 中上传一个文件，如：

> `bin/hdfs dfs -put ../hadoop-2.7.2.tar.gz /`

这个命令表示我把 `hadoop-2.7.2.tar.gz` 这个文件上传到 hdfs 的根目录下，但是这个文件具体的位置在哪？

> 首先我们知道他肯定是在 Linux 服务器中，但是具体位置在哪？
>
> 答：数据一般保存在我们配置的 `data` 目录下，所以我们去 data 目录下找
>
> `/usr/local/module/hadoop-2.7.2/data/tmp/dfs/data/current/BP-372575800-192.168.148.141-1563113976815/current/finalized/subdir0/subdir0`   最后在这个目录下找到我们需要的文件。（可能一个大文件它会分成好几块来存，找到后把他们拼接在一起就可以得到一个完整的文件）

#### 12、集群启动停止方式总结

1）、各个服务组件逐一启动停止

> ① 分别启动/停止 HDFS 组件
>
> `hadoop-daemon.sh start/stop  namenode/datanode/secondarynamenode`
>
> ② 启动/停止 YARN
>
> `yarn-daemon.sh start/stop resourcemanager/nodemanager`

2）、整体启动（配置 SSH 是前提）

> ① 整体启动/停止 HDFS
>
> `start-dfs.sh     stop-dfs.sh`
>
> ② 整体启动/停止 YARN
>
> start-yarn.sh          stop-yarn.sh

#### 13、crontab 定时任务调度

1）、重新启动 crond 服务

> `service crond restart`

2）、基本语法

> `crontab [选项]`
>
> 选项：
>
> ​	`-e` ： 编辑 crontab 定时任务
>
> ​	`-l` ：查询 crontab 任务
>
> ​	`-r` ：删除当前用户所有的 crontab 任务

3）、使用

`* * * * * 执行的任务`

| 项目         | 含义                 | 范围                       |
| ------------ | -------------------- | -------------------------- |
| `第一个 "*"` | 一小时当中的第几分钟 | 0-59                       |
| `第二个 "*"` | 一天当中的第几小时   | 0-23                       |
| `第三个 "*"` | 一个月当中的第几天   | 1-31                       |
| `第四个 "*"` | 一年当中的第几个月   | 1-12                       |
| `第五个 "*"` | 一周当中的星期几     | 0-7（0 和 7 都代表星期日） |

 4）、特殊符号

| 特殊符号 | 含义                                                         |
| -------- | ------------------------------------------------------------ |
| `*`      | 代表任何时间。比如第一个时间就代表一小时中每分钟都执行一次   |
| `,`      | 代表不连续的时间。比如 `"0 8,12,16 * * *"`  就代表在每天的 8 点 0 分，12 点 0 分，16 点 0 分都执行一次命令 |
| `-`      | 代表连续的时间范围。比如 `"0 5 * * 1-6"`  ,代表在周一到周六凌晨 5 点 0 分执行命令 |
| `        | 代表每隔多久执行一次。比如 `"*/10 * * * *"` 代表每隔 10 分钟就执行一遍命令 |

#### 14、集群时间同步

**先说集群时间为什么要进行同步？**

> 比如说我现在有三台服务器，假如说这三台机器时间不一致，就可能出现问题。我在这三台服务器上部署了集群，现在要在一点钟执行一个任务，但是这是哪个机器的时间都不一样，这样就出问题了。

**时间同步方式：**

> 找一个机器作为时间服务器，所有的机器与这台机器的时间进行定时的同步，比如：每隔十分钟，同步一次时间。

**配置时间同步步骤：**

1）、时间服务器配置（必须是 root 用户）

① 检查 ntp 是否安装

```shell
[root@hadoop1 桌面]# rpm -qa |grep ntp
fontpackages-filesystem-1.41-1.1.el6.noarch       # 出现下面三行，证明安装
ntpdate-4.2.6p5-10.el6.centos.x86_64
ntp-4.2.6p5-10.el6.centos.x86_64
```

② 修改 ntp 配置文件

> `vim /etc/ntp.conf`

修改内容：

a）、授权 `192.168.1.0-192.168.1.255` 网段上所有机器可以从这条机器上查询和同步时间

> 表现在配置文件中的操作就是：
>
> ```shell
> # Hosts on local network are less restricted.
> restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap    #把这行注释打开
>          192.168.148.0      # 把这里的网段改成 148 网段
> ----------------------------------------------------------------------------
> 即：
> restrict 192.168.148.0  mask 255.255.255.0 nomodify notrap 
> ```
>
> **注意：**
>
> 把原来配置文件中注释行打开后，它的网络 ip 是 `192.168.1.0`   这里需要根据你机器具体的 ip 网段修改下，比如说我机器集群的网段都在 `192.168.148.x`  那么我就把这里改成 `192.168.148.0`  开始

b）、集群在局域网中，不使用其他互联网上的时间

> 表现在配置文件中的操作就是：
>
> ```shell
> # Use public servers from the pool.ntp.org project.
> # Please consider joining the pool (http://www.pool.ntp.org/join.html).
> server 0.centos.pool.ntp.org iburst
> server 1.centos.pool.ntp.org iburst      # 需要把这四行全部注释掉
> server 2.centos.pool.ntp.org iburst
> server 3.centos.pool.ntp.org iburst
> ----------------------------------------------------------------------------
> 即：
> #server 0.centos.pool.ntp.org iburst
> #server 1.centos.pool.ntp.org iburst      
> #server 2.centos.pool.ntp.org iburst
> #server 3.centos.pool.ntp.org iburst
> ```
>
> 

c）、当该节点丢失网络连接，依然可以采用本地时间作为时间服务器为集群中其他节点提供时间同步

> ```shell
> server 127.127.1.0
> fudge 127.127.1.0 stratum 10      # 把这两行直接粘贴到配置文件最后即可
> ```
>
> 

③ 修改 `/etc/sysconfig/ntpd`

> ```shell
> # 添加如下内容
> SYNC_HWCLOCK=yes       # 让硬件时间与系统时间一起同步
> ```
>
> 其实就是读取硬件的时钟信息

④ 重新启动 ntpd 服务

> `service htpd restart`
>
> ```shell
> [root@hadoop1 桌面]# service ntpd status
> ntpd (pid  7758) 正在运行...     # 证明 ntpd 正在运行
> ```
>
> 

⑤ 设置 ntpd 服务开机启动

> `chkconfig ntpd on`        

2）、其他机器配置（必须 root 用户）

① 在其他机器配置 10 分钟与时间服务器同步一次

> `crontab -e`
>
> 编写的定时任务如下：
>
> ```
> */10 * * * * /usr/sbin/ntpdate 192.168.148.141
> ```
>
> 

## 四、HDFS

### HDFS 概述

#### 1、HDFS 产生背景

随着数据量越来越大，在一个操作系统存不下所有的数据，那么就分配到更多操作系统分配的磁盘中，但是不方便管理和维护，迫切需要一种系统来管理多台机器上的文件，这就是分布式文件管理系统。HDFS 只是分布式文件管理系统中的一种。

#### 2、HDFS 定义

HDFS（Hadoop Distributed File System）它是一个文件系统，用于存储文件，通过目录树来定位文件。其次，它是分布式的，由很多服务器联合起来实现其功能，集群中的服务器有各自的角色。

**HDFS 使用场景：**

> 适合一次写入，多次读出的场景，且不支持文件的修改。适合用来做数据分析，并不适合用来做网盘应用（网盘涉及到增删改查）。

#### 3、HDFS 优缺点

**优点：**

> 1）、高容错性
>
> ① 数据自动保存多个副本。它通过增加副本的形式，提高容错性。
>
> ②某一个副本丢失以后，它可以自动恢复
>
> 2）、适合处理大数据
>
> ①数据规模：能够处理数据规模达到 GB、TB、甚至 PB 级别的数据
>
> ② 文件规模：能够处理百万规模以上的文件数量，数量相当之大
>
> 3）、可构建在廉价机器上，通过多副本机制，提高可靠性

**缺点：**

> 1）、不适合低延时数据访问，比如毫秒级的存储数据，是做不到的
>
> 2）、无法高效的对大量小文件进行存储
>
> ①存储大量小文件的话，它会占用 NameNode 大量的内存来存储文件目录和块信息。这样是不可取的，因为 NameNode 的内存总是有限的（比如说几百万个小文件需要存储，那么 NameNode 就需要把这几百万个小文件的文件目录和块信息都存下来，内存很快就耗完了）
>
> ② 小文件存储的寻址时间会超过读取时间，它违反了 HDFS 的设计目标
>
> 3）、不支持并发写入、文件随机修改
>
> ① 一个文件只能有一个线程写，不允许多个线程同时写
>
> ② 仅支持数据 append（追加），不支持文件的随机修改

#### 4、HDFS 组成架构

![hdfs架构图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/hdfs%E6%9E%B6%E6%9E%84%E5%9B%BE.png?q-sign-algorithm=sha1&q-ak=AKIDWlm5niwkCvw9aNY6y2xp0PmJlrXqkM8w&q-sign-time=1563455506;1563459106&q-key-time=1563455506;1563459106&q-header-list=&q-url-param-list=&q-signature=54ae2ff2ddf8a197e00b72961aa56a25f56fed79&x-cos-security-token=a2ac32073e70418ea238651590546ffd3f7a34f610001)



**NameNode（nn）：**

> NameNode（nn）：就是 Master，它是一个主管、管理者
>
> - 管理 HDFS 的名称空间
> - 配置副本策略
> - 管理数据块（Block）映射信息
> - 处理客户端读写请求

**DataNode（dn）：**

> DataNode（dn）：就是 Slave，NameNode 下达命令，DataNode 执行实际的操作
>
> - 存储实际的数据块
> - 执行数据库的读/写操作

**Secondary NameNode（snn）：**

> Secondary NameNode：并非 NameNode 的热备。当 NameNode 挂掉的时候，它并不能马上替换 NameNode 并提供服务。
>
> - 辅助 NameNode,分担其工作量，比如 定期合并 Fsimage 和 Edits ，并推送给 NameNode
> - 在紧急情况下，可辅助恢复 NameNode

**Client：**

> 就是客户端
>
> - 文件切分。文件上传 HDFS 的时候，Client 将文件切分成一个一个的 Block，然后进行上传
> - 与 NameNode 交互，获取文件的位置信息
> - 与 DataNode 交互，读取或者写入数据
> - Client 提供一些命令来管理 HDFS，比如 NameNode 格式化
> - Client 可以通过一些命令来访问 HDFS，比如对 HDFS 增删改查操作

#### 5、HDFS 块的大小设置

HDFS 中的文件在物理上是分块存储（Block），块的大小可以通过配置参数（dfs.blocksize）来规定，默认大小在 Hadoop2.x 版本中是 128M ，老版本（Hadoop1.x）是 64M

> ①HDFS 中的文件存在一个一个的 block 上
>
> ② 我想往 block 里面写东西，寻址时间约为 10ms，即查找到目标 block 的时间为 10ms
>
> ③ **寻址时间为传输时间的 1% 时，为最佳状态。** 因此，传输时间 ：10ms/0.01=1000ms=1s
>
> ④ 目前磁盘的传输速率普遍为 100MB/s 
>
> ⑤ block 大小： 1s * 100MB/s = 100MB       约为 100MB 左右

**为什么块的大小不能设置太小，也不能设置太大？**

> - HDFS 的块设置太小，会增加寻址时间，程序一直在找块的开始位置
>   - 比如说你有几百万个块，那么我想找到其中一个块往里面写寻址时间就会很长
> - 如果块设置的太大，从**磁盘传输数据的时间**会明显**大于定位这个块开始位置所需时间**。导致程序在处理这块数据时，会非常慢

**总结：**

==HDFS 块的大小设置主要取决于磁盘的传输速率==

#### **6、HDFS 相关 shell命令**

1）、基本语法

> `bin/hadoop fs 具体命令`       或者      `bin/hdfs dfs 具体命令`
>
> dfs 是 fs 的实现类

2）、常用命令

① 先需要启动 hadoop 集群

> ```shell
> [root@hadoop1 sbin]# pwd
> /usr/local/module/hadoop-2.7.2/sbin
> [root@hadoop1 sbin]# ./start-dfs.sh
> -------------------------------------
> [root@hadoop2 sbin]# pwd
> /usr/local/module/hadoop-2.7.2/sbin
> [root@hadoop2 sbin]# ./start-yarn.sh
> ```
>
> 

② `-help`

> 显示这个命令的帮助信息 

```shell
[root@hadoop1 bin]# hadoop fs -help rm
-rm [-f] [-r|-R] [-skipTrash] <src> ... :
  Delete all files that match the specified file pattern. Equivalent to the Unix
  command "rm <src>"
                                                                                 
  -skipTrash  option bypasses trash, if enabled, and immediately deletes <src>   
  -f          If the file does not exist, do not display a diagnostic message or 
              modify the exit status to reflect an error.                        
  -[rR]       Recursively deletes directories 
```

③ `-ls`

> 显示目录信息 

```shell
[root@hadoop1 bin]# hadoop fs -ls /
Found 2 items
-rw-r--r--   3 root supergroup  197657687 2019-07-16 19:27 /hadoop-2.7.2.tar.gz
drwxr-xr-x   - root supergroup          0 2019-07-16 19:26 /wcinput
```

④ `-mkdir`

> 在 HDFS 上创建目录

```shell
[root@hadoop1 bin]# hadoop fs -mkdir -p /shp/heping/shuai
[root@hadoop1 bin]# hadoop fs -ls -R /shp
drwxr-xr-x   - root supergroup          0 2019-07-22 23:07 /shp/heping
drwxr-xr-x   - root supergroup          0 2019-07-22 23:07 /shp/heping/shuai
```

⑤ `-moveFromLocal`

> 从本地剪切粘贴到 HDFS

```shell
# 第一个路径是本地文件路径，第二个是 hdfs 文件路径
[root@hadoop1 bin]# hadoop fs -moveFromLocal ./shp.txt /shp/heping/shuai
[root@hadoop1 bin]# hadoop fs -ls /shp/heping/shuai
Found 1 items
-rw-r--r--   3 root supergroup          6 2019-07-22 23:14 /shp/heping/shuai/shp.txt

# 同时你会发现本地的 shp.txt 没有了，被剪切掉了
```

⑥ `-appendToFile`

> 追加一个文件到已经存在的文件末尾

```shell
# 第一个路径是本地文件路径，第二个是 hdfs 文件路径
[root@hadoop1 bin]# hadoop fs -appendToFile heping.txt /shp/heping/shuai/shp.txt
```

⑦ `-cat`

> 显示文件内容

```shell
[root@hadoop1 bin]# hadoop fs -cat /shp/heping/shuai/shp.txt
hello
```

⑧ `-chgrp 、 -chmod 、-chown` 

> 修改文件所属权限，和 Linux 中用法一样
>

```shell
[root@hadoop1 bin]# hadoop fs -ls -h /shp/
Found 1 items
drwxr-xr-x   - root supergroup          0 2019-07-22 23:07 /shp/heping
[root@hadoop1 bin]# hadoop fs -chgrp -R root /shp/heping
[root@hadoop1 bin]# hadoop fs -ls -h /shp/
Found 1 items
drwxr-xr-x   - root root          0 2019-07-22 23:07 /shp/heping
```

⑨ `-copyFromLocal`

> 从本地文件系统中拷贝文件到 HDFS 中

```shell
# 第一个路径是本地文件路径，第二个路径是 hdfs 上路径
[root@hadoop1 bin]# hadoop fs -copyFromLocal ./hello.txt /shp/heping/shuai
[root@hadoop1 bin]# hadoop fs -ls /shp/heping/shuai
Found 2 items
-rw-r--r--   3 root root          3 2019-07-22 23:41 /shp/heping/shuai/hello.txt
-rw-r--r--   3 root root          6 2019-07-22 23:26 /shp/heping/shuai/shp.txt

# 同时你会发现本地的 hello.txt 还存在
```

⑩ `-copyToLocal`

> 从 HDFS 拷贝到本地

```shell
# 第一个参数是 hdfs 中的路径,第二个参数是本地路径
[root@hadoop1 bin]# hadoop fs -copyToLocal /shp/heping/shuai/shp.txt ./
```

11 `-cp`

> 从 HDFS 的一个路径拷贝到 HDFS 的另一个路径

```shell
[root@hadoop1 bin]# hadoop fs -cp /shp/heping/shuai/shp.txt /shp/heping
[root@hadoop1 bin]# hadoop fs -ls /shp/heping
Found 2 items
-rw-r--r--   3 root root          6 2019-07-22 23:54 /shp/heping/shp.txt
drwxr-xr-x   - root root          0 2019-07-22 23:41 /shp/heping/shuai
```

12 `-mv`

> 在 HDFS 目录中移动文件
>

```shell
[root@hadoop1 bin]# hadoop fs -mv /shp/heping/shp.txt /
[root@hadoop1 bin]# hadoop fs -ls /
Found 4 items
-rw-r--r--   3 root supergroup  197657687 2019-07-16 19:27 /hadoop-2.7.2.tar.gz
drwxr-xr-x   - root supergroup          0 2019-07-22 23:07 /shp
-rw-r--r--   3 root root                6 2019-07-22 23:54 /shp.txt
drwxr-xr-x   - root supergroup          0 2019-07-16 19:26 /wcinput
```

13 `-get`

> 等同于 copyToLocal   就是从 hdfs 下载文件到本地

```shell
# 第一个参数是 hdfs 中的路径,第二个参数是本地路径
[root@hadoop1 bin]# hadoop fs -get /shp.txt ./
```

14 `-getmerge`

> 合并下载多个文件，比如 hdfs 的目录 /shp 目录下有多个文件，那么我就可以把这几个文件合并在一起下载下来

```shell
# 前面的路径是 hdfs 上文件路径，后面是本地文件路径
[root@hadoop1 bin]# hadoop fs -getmerge /heping.txt /shp.txt ./merge.txt
```

15 `-put`

> 等同于 copyFromLocal

```shell
# 第一个路径是本地文件路径，第二个是 hdfs 文件路径
[root@hadoop1 bin]# hadoop fs -put ./heping.txt /
```

16 `-tail`

> 显示文件末尾

```shell
[root@hadoop1 bin]# hadoop fs -tail /shp.txt
hello
```

17 `-rm`

> 删除文件或文件夹

```shell
[root@hadoop1 bin]# hadoop fs -rm /shp.txt
```

#### 7、HDFS 客户端环境准备

1）、首先根据自己电脑操作系统拷贝对应的编译后的 hadoop 的 jar 包到非中文路径下（区分 win10 编译过的 hadoop 中 jar 包、win7编译过的 hadoop 中 jar 包、linux 编译过的 hadoop 中 jar 包）

2）、配置 HADOOP 环境变量

3）、创建项目，添加依赖

```xml
<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>2.8.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.7.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>2.7.2</version>
        </dependency>
    </dependencies>
```

#### 8、java 代码访问 hdfs

```java
public class HDFSClient {
    public static void main(String[] args) throws URISyntaxException, IOException, InterruptedException {
        Configuration configuration = new Configuration();
        //1.获取 hdfs 客户端对象
        /**
         * 第一个参数：hdfs 中 namenode 的地址
         * 第二个参数：配置类
         * 第三个参数：用什么用户去访问 hdfs
         */
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration, "root");

        //2.在 hdfs 上创建路径
        client.mkdirs(new Path("/shp/love"));

        //3.关闭资源
        client.close();
        System.out.println("访问结束");
    }
}
```

**注：**

> 客户端去操作 HDFS 时，是有一个用户身份的。默认情况下，HDFS 客户端 API 会从 JVM 中获取一个参数来作为自己的用户身份。
>
> `-DHADOOP_USER_NAME=root`       root 为用户名称

### HDFS 的 API 操作

#### 1、文件上传

```java
@Test
    public void testCopyFromLocalFile() throws URISyntaxException, IOException, InterruptedException {
        Configuration configuration = new Configuration();
        // 1.获取 hdfs 对象
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration, "root");

        // 2.执行上传 API
        // 第一个参数是本地文件路径，第二个参数是 hdfs 上文件路径
        client.copyFromLocalFile(new Path("/Users/shenheping/Desktop/hello.txt"),new Path("/shp"));

        //3.关闭资源
        client.close();
    }
```

**参数优先级：**

① 我们将 `hdfs-site.xml` 拷贝到项目的类路径下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>

<configuration>
	<property>
		<name>dfs.replication</name>
        <value>1</value>
	</property>
</configuration>
```

这样你项目在运行的时候它会自动加载类路径下的配置文件，然后你在上传一个文件到 hdfs 上，发现副本数就变成了 1

② 直接在代码中设置副本数

```java
@Test
    public void testCopyFromLocalFile() throws URISyntaxException, IOException, InterruptedException {
        Configuration configuration = new Configuration();
        configuration.set("dfs.replication","2");
        // 1.获取 hdfs 对象
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration, "root");

        // 2.执行上传 API
        client.copyFromLocalFile(new Path("/Users/shenheping/Desktop/hello.txt"),new Path("/shp"));

        //3.关闭资源
        client.close();
    }
```

这样你再上传一个文件到 hdfs 上，发现副本数变成了 2

**参数优先级排序**

> 客户端代码中设置的值 > 类路径下用户自定义的配置文件 > 服务中配置文件配置的值

#### 2、文件下载

```java
@Test
    public void testCopyToLocalFile() throws URISyntaxException, IOException, InterruptedException {
        Configuration configuration = new Configuration();
        // 1.获取 hdfs 对象
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration, "root");

        // 2. 执行下载 API
        client.copyToLocalFile(new Path("/heping.txt"),new Path("/Users/shenheping/Desktop"));

        // 3.关闭资源
        client.close();
    }
```

#### 3、文件夹删除

```java
@Test
    public void testDelete() throws URISyntaxException, IOException, InterruptedException {
        Configuration configuration = new Configuration();
        // 1.获取 hdfs 对象
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration, "root");

        // 2. 执行删除 API
        // 第二个参数表示是否是递归删除
        client.delete(new Path("/heping.txt"),true);

        // 3.关闭资源
        client.close();
    }
```

#### 4、修改文件名称

```java
@Test
    public void testRename() throws URISyntaxException, IOException, InterruptedException {
        Configuration configuration = new Configuration();
        // 1.获取 hdfs 对象
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration, "root");

        // 2.执行修改名称 API
        client.rename(new Path("/shp"),new Path("/shp1"));

        // 3.关闭资源
        client.close();
    }
```

#### 5、查看文件详情

查看文件名称、权限、长度、块信息

```java
@Test
    public void testListFiles() throws URISyntaxException, IOException, InterruptedException {
        Configuration configuration = new Configuration();
        // 1.获取 hdfs 对象
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration, "root");

        // 2.查看文件详情
        RemoteIterator<LocatedFileStatus> listFiles = client.listFiles(new Path("/"), true);

        while (listFiles.hasNext()) {
            // 拿到这个文件的状态信息
            LocatedFileStatus fileStatus = listFiles.next();
            // 查看文件名称、权限、长度、块信息
            System.out.println(fileStatus.getPath().getName()); // 文件名称
            System.out.println(fileStatus.getPermission());  // 文件权限
            System.out.println(fileStatus.getLen());   // 文件长度

            // 返回一个数组，因为这个数据块会有多个副本
            BlockLocation[] blockLocations = fileStatus.getBlockLocations();
            for (BlockLocation blockLocation : blockLocations) {
                // 这个数据块具体在哪一台主机上
                String[] hosts = blockLocation.getHosts();
                for (String host : hosts) {
                    System.out.println(host);
                }
            }
            System.out.println("------分隔线-----");

        }
        // 3.关闭资源
        client.close();
    }
```

#### 6、判断是文件还是文件夹

```java
@Test
    public void testListStatus() throws URISyntaxException, IOException, InterruptedException {
        Configuration configuration = new Configuration();
        // 1.获取 hdfs 对象
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration, "root");

        // 2.判断操作
        FileStatus[] fileStatuses = client.listStatus(new Path("/"));
        for (FileStatus fileStatus : fileStatuses) {
            if (fileStatus.isFile()) {
                System.out.println("文件" + fileStatus.getPath().getName());
            } else if (fileStatus.isDirectory()) {
                System.out.println("目录" + fileStatus.getPath().getName());
            }
        }
    }
```

### HDFS 的 I/O 操作

前面我们都是使用 hdfs 封装好的 API 对 HDFS 进行操作，下面我们使用  IO 流对 HDFS 进行操作

#### 1、HDFS 文件上传

```java
/**
     * 把本地的文件上传到 hdfs 的根目录
     */
    @Test
    public void test() throws URISyntaxException, IOException, InterruptedException {
        // 1. 获取 hdfs 对象
        Configuration configuration = new Configuration();
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration,"root");

        // 2.获取输入流
        FileInputStream fis = new FileInputStream(new File("/Users/shenheping/Desktop/abc.txt"));

        // 3.获取输出流
        FSDataOutputStream fos = client.create(new Path("/abc.txt"));

        // 4.流的拷贝
        IOUtils.copyBytes(fis,fos,configuration);

        // 5.关闭流
        IOUtils.closeStream(fos);
        IOUtils.closeStream(fis);
        client.close();
    }
```

#### 2、HDFS 文件下载

```java
/**
     * 从 hdfs 上下载文件到本地
     */
    @Test
    public void getFileFromHDFS() throws URISyntaxException, IOException, InterruptedException {
        // 1. 获取 hdfs 对象
        Configuration configuration = new Configuration();
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration,"root");

        // 2.获取输入流
        FSDataInputStream fis = client.open(new Path("/abc.txt"));

        // 3.获取输出流
        FileOutputStream fos = new FileOutputStream(new File("/Users/shenheping/Desktop"));

        // 4.IO流对拷
        IOUtils.copyBytes(fis,fos,configuration);

        // 5.关闭资源
        IOUtils.closeStream(fos);
        IOUtils.closeStream(fis);
        client.close();
    }
```

#### 3、定位读取文件

分块读取 HDFS 上的大文件

```java
/**
     * 下载第一块
     */
    @Test
    public void readFileSeek1() throws URISyntaxException, IOException, InterruptedException {
        // 1.获取 fdfs 对象
        Configuration configuration = new Configuration();
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration,"root");

        // 2. 获取输入流
        FSDataInputStream fis = client.open(new Path("/hadoop-2.7.2.tar.gz"));

        // 3.获取输出流
        FileOutputStream fos = new FileOutputStream(new File("/Users/shenheping/Desktop/hadoop-2.7.2.tar.gz.part1"));

        // 4.流的拷贝（只拷贝 128m）
        byte[] bytes = new byte[1024];
        for (int i = 0; i < 1024 * 128; i++) {
            fis.read(bytes);
            fos.write(bytes);
        }

        // 5.关闭流
        IOUtils.closeStream(fos);
        IOUtils.closeStream(fis);
        client.close();
    }

    @Test
    public void readFileSeek2() throws URISyntaxException, IOException, InterruptedException {
        // 1.获取 fdfs 对象
        Configuration configuration = new Configuration();
        FileSystem client = FileSystem.get(new URI("hdfs://192.168.148.141:9000"), configuration,"root");

        // 2. 获取输入流
        FSDataInputStream fis = client.open(new Path("/hadoop-2.7.2.tar.gz"));

        // 3.设置指定读取的起点
        fis.seek(1024 * 1024 * 128);

        // 4.获取输出流
        FileOutputStream fos = new FileOutputStream(new File("/Users/shenheping/Desktop/hadoop-2.7.2.tar.gz.part1"));

        // 5.流的拷贝
        IOUtils.copyBytes(fis,fos,configuration);

        // 6.关闭流
        IOUtils.closeStream(fos);
        IOUtils.closeStream(fis);
        client.close();
    }
```

### HDFS 的数据流

#### 1、HDFS 写数据流程

就是如何向 HDFS 中上传数据

> 首先是有一个集群，这个集群中包括一个 NameNode 和 三个 DataNode。然后我客户端要去操作集群，我需要使用客户端把本地 abc.avi 这个文件上传到 HDFS 集群上。那么我们首先肯定需要拿到这个客户端对象，如果你是用 java 代码去操作的话，那么这个客户端对象就是 `FileSystem` 。拿到这个客户端对象之后，它会创建一个 `Distributed FileSystem`（后面的所有操作都和它有关系）。然后这个对象会向 NameNode 请求上传文件 /shp/heping/abc.avi  ，这时候 NameNode 会检查是否存在这个文件，如果存在直接报文件已存在。如果这个文件不存在，NameNode 会响应可以上传。可以上传之后，我肯定需要知道上传到哪几台 DataNode 服务器，所以我客户端会请求 NameNode 上传第一个 Block，让 NameNode 给我返回要上传到哪几个 DataNode 服务器。这时候 NameNode 经过计算，返回要上传的 DataNode 节点。现在我客户端知道往哪写了，接下来就是获取 FSDataOutputStream，建立 Block 传输通道，FSDataOutputStream 会请求 dn1 上传数据，dn1 收到请求会继续调用 dn2，然后 dn2 调用 dn3 ，将这个通信管道建立完成。在请求建立通道的时候 dn3 dn2 dn1 都会进行应答，都应答成功后这个通道就建立起来了。最后一步就是传输数据了。

![HDFS写数据流程](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/HDFS%E5%86%99%E6%95%B0%E6%8D%AE%E6%B5%81%E7%A8%8B.png)

**总结：**

> 1. 客户端通过 Distributed FileSystem 模块向 NameNode 请求上传文件，NameNode 检查目标文件是否已存在，父目录是否已存在
> 2. NameNode 返回是否可以上传
> 3. 客户端请求第一个 Block ，上传到哪几个 DataNode 服务器上
> 4. NameNode 返回 3 个 DataNode 节点，分别为 dn1、dn2、dn3
> 5. 客户端通过 FSDataOutputStream 模块请求 dn1 上传数据， dn1 收到请求会继续调用 dn2，然后 dn2 调用 dn3，将这个通信管道建立完成
> 6. dn1、dn2、dn3 逐级应答客户端
> 7. 客户端开始往 dn1 上传第一个 Block（先从磁盘读取数据放到一个本地内存缓存），以 Packet 为单位，dn1 收到一个 Packet 就会传给 dn2，dn2 传给 dn3（dn1 每传一个 Packet 会放入一个应答队列等待应答）
> 8. 当一个 Block 传输完成后，客户端再次请求 NameNode 上传第二个 Block 到服务器（重复执行 3-7 步）

#### 2、网络拓扑-节点距离计算

在 HDFS 写数据过程中，NameNode 会选择距离待上传数据最近距离的 DataNode 接收数据。那么这个最近距离是怎么计算的呢？

> **节点距离：两个节点到达最近的共同祖先的距离总和**

![节点距离](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/%E8%8A%82%E7%82%B9%E8%B7%9D%E7%A6%BB.png)



比如说上图，节点9 和节点4 的共同祖先是节点8，节点9 到节点8 的距离是 1，节点4 到节点8 的距离是 2，那么这两个节点之间的距离就是 3

#### 3、HDFS 机架感知-副本存储节点选择

比如说你现在存储了一个数据，这个数据有 3 个副本，这三个副本存在哪里？

> - 第一个副本在 client 所处的节点上，如果客户端在集群外，随机选一个
> - 第二个副本和第一个副本位于相同机架，随机节点
> - 第三个副本位于不同机架，随机节点
>
> 这样做的好处第一个是同一个机架上读写迅速，备份速度快。第二个就是如果一个机架挂了，另一个机架上还有，可靠性高。

![副本节点选择](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/%E5%89%AF%E6%9C%AC%E8%8A%82%E7%82%B9%E9%80%89%E6%8B%A9.png)

#### 4、HDFS 读数据流程

![HDFS读取数据流程](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/HDFS%E8%AF%BB%E6%95%B0%E6%8D%AE%E6%B5%81%E7%A8%8B.png)

> - 客户端通过 Distributed FileSystem 向 NameNode 请求下载文件，NameNode 通过查询元数据，找到文件块所在的 DataNode 地址
> - 挑选一台 DataNode 服务器（就近原则，然后随机），请求读取数据
> - DataNode 开始传输数据给客户端
> - 客户端以 Packet 为单位接收，先在本地缓存，然后写入目标文件

### NameNode 和 SecondaryNameNode

#### 1、NN 和 2NN 工作机制

 **首先思考 NameNode 中的元数据是存储在哪里的？**

> 首先，我们假设，如果存储在 NameNode 节点的磁盘中，因为经常需要随机访问，还有响应客户请求，必然效率会非常低。因此元数据需要放在内存中。但是如果只存在内存中，一旦断电，元数据丢失，整个集群就无法工作了。**因此产生在磁盘中备份元数据的 FsImage**
>
> 这样又会带来新的问题，当在内存中的元数据更新时，如果同时更新 `FsImage`，就会导致效率过低（牵涉到磁盘 IO），但是如果不更新，就会发生一致性问题，一旦 NameNode 节点断电，就会产生数据丢失。因此，**引入 Edits 文件（只进行追加操作，效率很高）。每当有元数据更新或者添加元数据时，修改内存中的元数据并追加到 Edits 中**。这样一旦 NameNode 节点断电，可以通过 FsImage 和 Edits 的合并，合成元数据。
>
> 但是如果长时间添加数据到 `Edits` 中，会导致该文件数据过大，效率降低。而且一旦断电，恢复元数据需要的时间会很长。因此需要定期进行 `FsImage` 和 `Edits` 的合并，如果这个操作由 NameNode 节点完成，又会效率过低。因此引入一个新的节点 SecondaryNameNode 专门用于 `FsImage` 和 `Edits` 的合并。

**总结一下：**

>  首先 NameNode 的元数据存在哪？肯定是存在内存中的，读取和响应快。但是内存有断电丢失数据的缺点，那怎么办？就是把这个数据存在磁盘中，存在磁盘的 `FsImage` 中。但是如果别人对内存中的数据有修改，你如果直接去修改 FsImage 不划算（产生大量磁盘 IO，效率就低了），这样就引入一个 `Edits` ，操作内存中的数据，同时把它写到编辑日志中去。这样 内存中的数据 = `FsImage + Edits`   。这样又会存在一个问题，就是编辑日志会越来越大，什么时候将 `FsImage` 和 `Edits` 进行合并呢？这时候就引入了 `SecondaryNameNode`

**NameNode 工作机制**

 ![NameNode工作机制](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/NameNode%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6.png)

> 第一阶段：NameNode 启动
>
> - 第一次启动 NameNode 格式化后，创建 FsImage 和 Edits 文件。如果不是第一次启动，直接加载编辑日志和镜像文件到内存
> - 客户端对元数据进行增删改请求
> - NameNode 记录操作日志，更新滚动日志
> - NameNode 在内存中对数据进行增删改
>
> 第二阶段：Secondary NameNode 工作
>
> - Secondary NameNode 询问 NameNode 是否需要 CheckPoint ，直接带回 NameNode 是否检查结果
> - Secondary NameNode 请求执行 CheckPoint
> - NameNode 滚动正在写的 Edits 日志
> - 将滚动前的编辑日志和镜像文件拷贝到 SecondaryNameNode
> - Secondary NameNode 加载编辑日志和镜像文件到内存，并合并
> - 生成新的镜像文件 fsimage.chkpoint
> - 拷贝 fsimage.chkpoint
> - NameNode 将 fsimage.chkpoint 重命名成 fsimage

**NN 和 2NN 工作机制详解**

```
Fsimage：NameNode内存中元数据序列化后形成的文件。
Edits：记录客户端更新元数据信息的每一步操作（可通过Edits运算出元数据）。
NameNode启动时，先滚动Edits并生成一个空的edits.inprogress，然后加载Edits和Fsimage到内存中，此时NameNode内存就持有最新的元数据信息。Client开始对NameNode发送元数据的增删改的请求，这些请求的操作首先会被记录到edits.inprogress中（查询元数据的操作不会被记录在Edits中，因为查询操作不会更改元数据信息），如果此时NameNode挂掉，重启后会从Edits中读取元数据的信息。然后，NameNode会在内存中执行元数据的增删改的操作。
由于Edits中记录的操作会越来越多，Edits文件会越来越大，导致NameNode在启动加载Edits时会很慢，所以需要对Edits和Fsimage进行合并（所谓合并，就是将Edits和Fsimage加载到内存中，照着Edits中的操作一步步执行，最终形成新的Fsimage）。SecondaryNameNode的作用就是帮助NameNode进行Edits和Fsimage的合并工作。
SecondaryNameNode首先会询问NameNode是否需要CheckPoint（触发CheckPoint需要满足两个条件中的任意一个，定时时间到和Edits中数据写满了）。直接带回NameNode是否检查结果。SecondaryNameNode执行CheckPoint操作，首先会让NameNode滚动Edits并生成一个空的edits.inprogress，滚动Edits的目的是给Edits打个标记，以后所有新的操作都写入edits.inprogress，其他未合并的Edits和Fsimage会拷贝到SecondaryNameNode的本地，然后将拷贝的Edits和Fsimage加载到内存中进行合并，生成fsimage.chkpoint，然后将fsimage.chkpoint拷贝给NameNode，重命名为Fsimage后替换掉原来的Fsimage。NameNode在启动时就只需要加载之前未合并的Edits和Fsimage即可，因为合并过的Edits中的元数据信息已经被记录在Fsimage中。
```

### CheckPoint 时间设置

1）、通常情况下，SecondaryNameNode 每隔一小时执行一次

`hdfs-default.xml`

```xml
<property>
  <name>dfs.namenode.checkpoint.period</name>
  <value>3600</value>
</property>
```

2）、一分钟检查一次操作数，当操作数达到一百万时， SecondaryNameNode 执行一次

```xml
<property>
  <name>dfs.namenode.checkpoint.txns</name>
  <value>1000000</value>
  <description>操作动作次数</description>
</property>

<property>
  <name>dfs.namenode.checkpoint.check.period</name>
  <value>60</value>
  <description> 1分钟检查一次操作次数</description>
</property >
```

### NameNode 故障处理

NameNode 故障后，可以采用如下两种方法恢复数据

方法一：

将 SecondaryNameNode 中数据拷贝到 NameNode 存储数据的目录

> 1、kill -9 NameNode进程
>
> 2、删除 NameNode 存储的数据
>
> `rm -rf /usr/local/module/hadoop-2.7.2/data/tmp/dfs/name/*`
>
> 3、拷贝 SecondaryNameNode 中的数据到 NameNode 存储数据目录
>
> `scp -r  root@192.168.148.143:/usr/local/module/hadoop-2.7.2/data/tmp/dfs/namesecondary/* /usr/local/module/hadoop-2.7.2/data/tmp/dfs/name`
>
> 4、重新启动 NameNode
>
> `sbin/hadoop-daemon.sh start namenode`

方法二：

使用 -importCheckpoint 选项启动 NameNode 守护进程，从而将 SecondaryNameNode 中数据拷贝到 NameNode 目录中

1、修改 hdfs-site.xml

```xml
<property>
  <name>dfs.namenode.checkpoint.period</name>
  <value>120</value>
</property>

<property>
  <name>dfs.namenode.name.dir</name>
  <value>/usr/local/module/hadoop-2.7.2/data/tmp/dfs/name</value>
</property>
```

2、kill -9 NameNode 进程

3、删除 NameNode 存储的数据

> `rm -rf /usr/local/module/hadoop-2.7.2/data/tmp/dfs/name/*`

4、如果 SecondaryNameNode 不和 NameNode 在一个主机节点上，需要将 SecondaryNameNode 存储数据的目录拷贝到 NameNode 存储数据的平级目录，并删除 in_use.lock 文件

> `scp -r  root@192.168.148.143:/usr/local/module/hadoop-2.7.2/data/tmp/dfs/namesecondary/* /usr/local/module/hadoop-2.7.2/data/tmp/dfs`
>
> `cd namesecondary`
>
> `rm -rf in_use.lock`

5、导入检查点数据

> `bin/hdfs namenode -importCheckpoint`

6、启动 NameNode

> `sbin/hadoop-daemon.sh start namenode`

### 集群安全模式

#### 安全模式：

1）、NameNode 启动

> NameNode 启动时，首先将镜像文件（Fsimage）载入内存，并执行编辑日志（Edits）中的各项操作。一旦在内存中成功建立文件系统元数据的映像，则创建一个新的 Fsimage 文件和一个空的编辑日志。此时 NameNode 开始监听 DataNode 请求。**这个过程期间，NameNode 一直运行在安全模式，即 NameNode 的文件系统对于客户端来说是只读的。**

2）、DataNode 启动

> **系统中的数据库的位置并不是由 NameNode 维护的，而是以块列表的形式存储在 DataNode 中。**在系统的正常操作期间，NameNode 会在内存中保留所有块位置的映射信息。在安全模式下，各个 DataNode 会向 NameNode 发送最新的块列表信息，NameNode 了解到足够多的块位置信息之后，即可高效运行文件系统。

3）、安全模式退出判断

> 如果满足“最小副本条件”，NameNode 会在 30 秒之后就退出安全模式。所谓的最小副本条件指的是在整个文件系统中 99.9% 的块满足最小副本级别（默认值：dfs.replication.min=1）。在启动一个刚刚格式化的 HDFS 集群时，因为系统中还没有任何块，所以 NameNode 不会进入安全模式

#### 基本语法：

集群处于安全模式，不能执行重要操作（写操作）。集群启动完成后，自动退出安全模式。

> - `bin/hdfs dfsadmin -safemode get`           查看安全模式状态
> - `bin/hdfs dfsadmin -safemode enter`        进入安全模式状态
> - `bin/hdfs dfsadmin -safemode leave`         离开安全模式状态
> - `bin/hdfs dfsadmin -safemode wait`           等待安全模式状态      

```shell
[root@hadoop1 bin]# hdfs dfsadmin -safemode get
Safe mode is OFF

[root@hadoop1 bin]# hdfs dfsadmin -safemode enter
Safe mode is ON
[root@hadoop1 bin]# hdfs dfs -put b.txt /
put: Cannot create file/b.txt._COPYING_. Name node is in safe mode.

# 进入安全模式之后，就不允许进行写操作了

#-------------------模拟等待安全模式---------------------------
1.先进入安全模式
 hdfs dfsadmin -safemode enter
2.创建并执行下面脚本
vim safemode.sh
#!/bin/bash
hdfs dfsadmin -safemode wait     # 这个表示等待安全模式退出，一退出安全模式立马执行下面的命令
hdfs dfs -put b.txt /
./safemode.sh         # 这个脚本执行的时候会是阻塞状态，等到安全模式退出，会立马执行完成
3.再打开一个窗口执行
hdfs dfsadmin -safemode leave
4.观察上一个窗口的情况，发现已经执行完成，文件上传到了 hdfs 上
```

### NameNode 多目录配置

NameNode 的本地目录可以配置成多个，且每个目录存放内容相同，增加了可靠性

具体配置如下：

① 在 hdfs-site.xml 中增加如下内容

```xml
<property>
    <name>dfs.namenode.name.dir</name>
	<value>file:///${hadoop.tmp.dir}/dfs/name1,file:///${hadoop.tmp.dir}/dfs/name2</value>
</property>

<!-- hadoop.tmp.dir 为在 core-site.xml 中配置好的路径 -->
```

② 停止集群删除 data 和 logs 中所有数据

```
rm -rf data/ logs/
```

③ 格式化集群并启动

```
bin/hdfs namenode –format
sbin/start-dfs.sh
```

④ 查看结果

```
ll
data
name1
name2
```

### DataNode 工作机制

![DataNode工作机制](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/DataNode%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6.png)

> 1. 一个数据块在 DataNode 上以文件形式存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据，包括数据块的长度，块数据的校验和，以及时间戳
> 2. DataNode 启动后，向 NameNode 注册，通过后，周期性（1h）的向 NameNode 上报所有块信息
> 3. 心跳是每 3 秒一次，心跳返回结果带有 NameNode 给该 DataNode 的命令如复制块数据到另一台机器，或删除某个数据块。如果超过 10 分钟没有收到某个 DataNode 的心跳，则认为该节点不可用
> 4. 集群运行过程中可以安全加入和退出一些机器

### 数据完整性

> 假如说电脑磁盘里面存储的数据是交通信号灯的红灯信号（1）和绿灯信号（0），但是如果存储该数据的磁盘坏了，一直是绿灯，是否很危险？同理 DataNode 节点上的数据损坏了，却没有发现，是否也很危险？
>
> 下面是 DataNode 节点保证数据完整性的方法：
>
> 1、当 DataNode 读取 Block 的时候，它会计算 CheckSum
>
> 2、如果计算后的 CheckSum ，与 Block 创建时值不一样，说明 Block 已经损坏
>
> 3、client 读取其他 DataNode 上的 Block
>
> 4、DataNode 在其文件创建后周期验证 CheckSum

### DataNode 掉线时参数设置

![DataNode 掉线时参数设置](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/Datanode%E6%8E%89%E7%BA%BF%E6%97%B6%E5%8F%82%E6%95%B0%E8%AE%BE%E7%BD%AE.png)

通过修改 hdfs-site.xml 可以去改默认配置

```xml
<property>
    <name>dfs.namenode.heartbeat.recheck-interval</name>
    <value>300000</value>
</property>
<property>
    <name>dfs.heartbeat.interval</name>
    <value>3</value>
</property>
```

### 服役新数据节点

随着业务的增长，数据量越来越大，原有的数据节点的容量已经不能满足存储数据的需求，需要在原有集群基础上动态添加新的数据节点。

具体步骤就是：

> 在新的服务器节点上配置好 hadoop 环境，然后直接启动 DataNode，即可关联到集群
>
> `sbin/hadoop-daemon.sh start datanode`
>
> `sbin/yarn-daemon.sh start nodemanager`

### 退役旧数据节点

#### 1、添加白名单

上面说了横向扩展一个数据节点很方便，只要把服务器节点的 hadoop 环境和集群保持一致，然后直接启动 DataNode 就可以关联到集群。那么这样可能就会有安全问题，比如我随便弄一个服务器然后上面的  hadoop 配置和集群一样，这样我的这个节点也加入集群了，会存在数据安全问题，我们可以通过设置白名单来解决这个问题。

添加到白名单的主机节点，都允许访问 NameNode，不在白名单的主机节点，都会被退出。

**配置白名单的具体步骤：**

① 在 NameNode  的 `/usr/local/module/hadoop-2.7.2/etc/hadoop` 的目录下创建 dfs.hosts 文件

```shell
vim dfs.hosts
# 添加以下节点
192.168.148.141
192.168.148.142
192.168.148.143
```

② 在 NameNode 的 hdfs-site.xml 中增加 dfs.hosts 属性

```xml
<property>
	<name>dfs.hosts</name>
	<value>/usr/local/module/hadoop-2.7.2/etc/hadoop/dfs.hosts</value>
</property>
```

③ 配置文件分发

```shell
./xsync hdfs-site.xml       
# xsync 是编写的集群分发脚本 
```

④ 刷新 NameNode 节点

```shell
cd /usr/local/module/hadoop-2.7.2/bin
hdfs dfsadmin -refreshNodes           # 让 NameNode 重新去读取 hdfs 节点，这样不在白名单的服务器节点就会下线
```

⑤ 更新 ResourceManager 节点

```shell
cd /usr/local/module/hadoop-2.7.2/bin
yarn rmadmin -refreshNodes
```

#### 2、添加黑名单

在黑名单上的主机都会被强制退出

**配置黑名单步骤：**

① 在 NameNode  的 `/usr/local/module/hadoop-2.7.2/etc/hadoop` 的目录下创建 dfs.hosts.exclude 文件

```shell
vim dfs.hosts.exclude
# 添加以下节点
192.168.148.144         # 这个节点是要退役的节点
```

② 在 NameNode 的 hdfs-site.xml 中增加 dfs.hosts.exclude 属性

```xml
<property>
	<name>dfs.hosts.exclude</name>
    <value>/usr/local/module/hadoop-2.7.2/etc/hadoop/dfs.hosts.exclude</value>
</property>
```

③ 刷新 NameNode 刷新 ResourceManager

```shell
cd /usr/local/module/hadoop-2.7.2/bin
hdfs dfsadmin -refreshNodes 
yarn rmadmin -refreshNodes
```

④ 检查 web 浏览器，退役节点的状态为 decommission in progress（退役中），说明数据节点正在复制块到其他节点

⑤ 等待退役节点状态为 decommissioned（所有块已经复制完成），停止该节点及节点资源管理器。注意：如果副本数是 3，服役的节点小于等于 3，是不能退役成功的，需要修改副本数后才能退役。

**注意：**

==不允许白名单和黑名单中同时出现同一个主机名称==

## 五、HDFS2.X 新特性

### 1、集群间数据拷贝

如果你想实现服务器之间的数据拷贝，使用 scp 命令就可以。但是如果想实现集群与集群之间的数据拷贝，一个场景就是在真实开发中，可能会有生产集群和测试集群。你先在测试集群操作，操作完之后想把数据拷贝到生产集群，这时候就需要集群与集群之间的数据拷贝。

使用 `distcp` 命令实现两个 hadoop 集群之间的递归数据复制

```sh
# 第一个集群 NameNode 的地址    第二个集群 NameNode 的地址
bin/hadoop distcp hdfs://192.168.148.141:9000/shp/heping/aa.txt   hdfs://192.168.148.149:9000/shp/heping/aa.txt
```

### 2、小文件存档

**HDFS 存储小文件的弊端**

> 每个文件均按块存储，每个块的元数据存储在 NameNode 的内存中，因此 HDFS 存储小文件会非常低效。**因为大量的小文件会耗尽 NameNode 中的大部分内存**。（文件已数据块存储在 DataNode 上，但是在 NameNode 上会存储数据块的索引，占 150 个字节，这个索引指向 DataNode 的数据块，假如你有几百万的小文件，那么这几百万个小文件都要有索引存储在 NameNode 中，NameNode 的内存很快就会被耗光。假设你有一个很大的而文件存储在 DataNode 上，它在 NameNode 上也有索引，也占  150 个字节，所以存储小文件会非常低效）。但注意，存储小文件所需要的磁盘容量和数据块的大小无关。例如，一个 1MB 的文件设置为 128M 的块存储，实际使用的是 1MB 的磁盘空间，而不是 128MB

**解决存储小文件的办法：**

> HDFS 存档文件或 HAR 文件，是一个更高效的文件存档工具，它将文件存入 HDFS 块，在减少 NameNode 内存使用的同时，允许对文件进行透明的访问。具体来说，HDFS 存档文件对内还是一个一个独立文件，对 NameNode 而言却是一个整体，减少了 NameNode 的内存。 

案例：

1）、需要启动 YARN 进程

```shell
./start-yarn.sh
```

2）、归档文件

把 /shp/heping 下面的所有文件归档成一个叫 input.har 的归档文件，并把归档后文件存储到 /shp/output 下面

```shell
# 第一个参数 input.har 是归档成一个叫什么名字的文件
# 第二个参数 /shp/heping 是把哪个目录下的所有文件进行归档
# 第三个参数 /shp/output 是归档成一个文件，这个文件的存放位置，这个目录一开始一定不能存在
hadoop archive -archiveName input.har -p /shp/heping/ /shp/output
```

3）、查看归档

```shell
hadoop fs -ls -R har:///shp/output/input.har
# 可以发现这个 har 文件里面是你之前进行归档的一个一个小文件
```

4）、解归档文件

```shell
hadoop fs -cp har:///shp/output/input.har/* /shp/heping
```

### 3、回收站

开启回收站功能，可以将删除的文件在不超时的情况下，恢复原数据，起到防止误删除的作用

1）、开启回收站功能参数说明

> - 默认值 `fs.trash.interval=0`， 0 表示禁用回收站；其他值表示设置文件的存活时间
> - 默认值 `fs.trash.checkpoint.interval=0`，检查回收站的间隔时间。如果该值为 0，则该值设置和 `fs.trash.interval` 的参数值相等
> - 要求 `fs.trash.checkpoint.interval` <= `fs.trash.interval`

2）、启用回收站

修改 `core-site.xml` ，配置垃圾回收时间为 1 分钟

```shell
vim core-site.xml 
<property>
   <name>fs.trash.interval</name>
	<value>1</value>
</property>
```

3）、查看回收站

回收站在集群中的路径：/shp/heping/.Trash （hdfs 上）

如果你去查看的话，会发现没有权限，因为默认用户时 `dr.who`

4）、修改访问回收站用户名称

进入回收站默认用户名称是 `dr.who` ，修改为 root 用户

```shell
vim core-site.xml 
<property>
  <name>hadoop.http.staticuser.user</name>
  <value>root</value>
</property>
```

5）、恢复回收站数据

```shell
bin/hadoop fs -mv 回收站文件目录 要恢复在哪个目录
```

6）、清空回收站

```shell
bin/hadoop fs -expunge
```

### 4、快照管理

快照相当于对目录做一个备份。并不会立即复制所有文件，而是指向同一个文件。当写入发生时，才会产生新文件。

> - `hdfs dfsadmin -allowSnapshot 路径`
>   - 开启指定目录的快照功能
> - `hdfs dfsadmin -disallowSnapshot 路径`
>   - 禁用指定目录的快照功能，默认是禁用
> - `hdfs dfs -createSnapshot 路径`
>   - 对目录创建快照
> - `hdfs dfs -createSnapshot 路径 名称`
>   - 创建一个指定名称的快照
> - `hdfs dfs -renameSnapshot 路径 旧名称 新名称`
>   - 重命名快照
> - `hdfs lsSnapshottableDir`
>   - 列出当前用户所有可快照目录
> - `hdfs snapshotDiff 路径1 路径2`
>   - 比较两个快照目录的不同之处
> - `hdfs dfs -deleteSnapshot 路径 快照名称`
>   - 删除快照

## 六、MapReduce

### MapReduce 概述

#### 1、MapReduce 定义

> MapReduce 是一个分布式运算程序的编程框架，是用户开发 “基于 Hadoop 的数据分析应用” 的核心框架。
>
> MapReduce 核心功能是将用户编写的业务逻辑代码和自带默认组件整合成一个完整的分布式运算程序，并发运行在一个  Hadoop 集群上。

#### 2、MapReduce 优缺点

1）、优点

> - MapReduce 易于编程
>   - 它简单的实现一些接口，就可以完成一个分布式程序，这个分布式程序可以分布到大量廉价的 PC 机器上运行。也就是说你写一个分布式程序，跟写一个简单的串行程序是一模一样的。就是因为这个特点使得 MapReduce 编程变得非常流行
> - 良好的扩展性
>   - 当你的计算资源不能得到满足的时候，可以通过简单的增加机器来扩展它的计算能力
> - 高容错性
>   - MapReduce 设计的初衷就是使程序能够部署在廉价的 PC 机器上，这就要求它有很高的容错性。比如其中一台机器挂了，它可以把上面的计算任务转移到另外一个节点上运行，不至于这个任务运行失败，而且这个过程不需要人工参与，而完全是由 Hadoop 内部完成的。
> - 适合 PB 级以上海量数据的离线处理
>   - 可以实现上千台服务器集群并发工作，提供数据处理能力。

2）、缺点

> - 不擅长实时计算
>   - MapReduce 无法像 MySQL 一样，在毫秒或者秒级内返回结果
> - 不擅长流式计算
>   - 流式计算的输入数据是动态的，而 MapReduce 的输入数据集是静态的，不能动态变化。这是因为 MapReduce 自身的设计特点决定了数据源必须是静态的。
> - 不擅长 DAG（有向图）计算
>   - 多个应用程序存在依赖关系，后一个应用程序的输入为前一个的输出。在这种情况下，MapReduce 并不是不能做，而是使用后每个 MapReduce 作业的输出结果都会写入到磁盘，会造成大量的磁盘 IO，导致性能非常低下

### MapReduce 核心思想

![MapReduce核心思想](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/mapReduce%E6%A0%B8%E5%BF%83%E6%80%9D%E6%83%B3.png)



### MapReduce 进程

一个完整的 MapReduce 程序在分布式运行时，有三类实例进程：

> 1）、MrAppMaster ：负责整个程序的过程调度及状态协调
>
> 2）、MapTask ：负责 Map 阶段的整个数据处理流程
>
> 3）、ReduceTask ：负责 Reduce 阶段的整个数据处理流程

### MapReduce 编程规范

用户编写的程序分成三个部分：Mapper 、Reducer  和 Driver

1）、Mapper 阶段

> 1、用户自定义的 Mapper 要继承自己的父类
>
> 2、Mapper 的输入数据是 KV 对的形式（KV 的类型可以自定义）
>
> 3、Mapper 中的业务逻辑写在 map() 方法中
>
> 4、Mapper 的输出数据是 KV 对的形式（KV 的类型可以自定义）
>
> 5、map() 方法（MapTask进程）对每一个 KV 调用一次

2）、Reducer 阶段

> 1、用户自定义的 Reducer 要继承自己的父类
>
> 2、Reducer 的输入数据类型对应 Mapper 的输出数据类型，也是 KV
>
> 3、Reducer 的业务逻辑写在 reduce() 方法中
>
> 4、ReduceTask 进程对每一组相同 k 的 KV 组调用一次 reduce() 方法

3）、Driver 阶段

> 相当于 YARN 集群的客户端，用于提交我们整个程序到 YARN 集群，提交的是封装了 MapReduce 程序相关运行参数的 job 对象



### WordCount 案例

#### 1、案例分析

按照 MapReduce 编程规范，分别编写 Mapper、Reducer、Driver

![wordCount 案例分析](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/wordCount%E6%A1%88%E4%BE%8B%E5%88%86%E6%9E%90.png)

#### 2、Mapper 编写

```java
/**
 * map 阶段
 * KEYIN 输入数据的 key 类型
 * VALUEIN 输入数据的 value 类型
 * KEYOUT 输出数据的 key 类型     java 1 html 2
 * VALUEOUT 输出数据的 value 类型
 */
public class WordCountMapper extends Mapper<LongWritable,Text,Text,IntWritable> {

    Text k = new Text();
    IntWritable v = new IntWritable();
    /**
     * 重写父类中的 map 方法
     * @param key  这个参数表示行的偏移量
     * @param value 这个参数表示这一行实实在在的内容
     * @param context
     */
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        // Java Java
        // 获取一行
        String line = value.toString();
        // 根据空格去切割单词
        String[] words = line.split(" ");
        // 循环写出
        for (String word : words) {
//            Text k = new Text();     提出去，不要在 for 循环里面创建对象
            k.set(word);
//            IntWritable v = new IntWritable();
            v.set(1);
            context.write(k,v);
        }
    }
}
```

#### 3、Reducer 编写

```java
/**
 * KEYIN, VALUEIN ：map 阶段输出的 key 和 value
 * KEYOUT, VALUEOUT ：reducer 输出的 key 和 value 类型
 *
 */
public class WordCountReducer extends Reducer<Text,IntWritable,Text,IntWritable> {
    IntWritable intWritable = new IntWritable();
    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        // map 完了之后，到 reduce 的数据：
        // Java 1
        // Java 1
        // 累加求和：
        int sum = 0;
        for (IntWritable value : values) {
            sum += value.get();
        }
        // 将结果写出
        intWritable.set(sum);  // 这里主要是把 sum 的 int 类型转换成 IntWritable 类型
        context.write(key,intWritable);
    }
}
```

#### 4、Driver 编写

```java
public class WordCountDriver {
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        Configuration conf = new Configuration();
        // 1.获取 job 对象
        Job job = Job.getInstance(conf);
        // 2.设置 jar 的存储位置
        job.setJarByClass(WordCountDriver.class);
        // 3.关联 Map 和 Reduce 类
        job.setMapperClass(WordCountMapper.class);
        job.setReducerClass(WordCountReducer.class);
        // 4.设置 Mapper 阶段输出数据的 key 和 value 类型
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);
        // 5.设置最终数据输出的 key 和 value 类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        // 6.设置输入路径和输出路径
        FileInputFormat.setInputPaths(job,new Path(args[0]));
        FileOutputFormat.setOutputPath(job,new Path(args[1]));
        // 7.提交 job
        boolean result = job.waitForCompletion(true);

        System.exit(result ? 0 : 1);
    }
}
```

## 七、Hadoop序列化

### 1、序列化概述

**什么是序列化：**

> 序列化就是把内存中的对象，转换成字节序列（或其他数据传输协议）以便于存储到磁盘（持久化）和网络传输。
>
> 反序列化就是将收到字节序列（或其他数据传输协议）或者是磁盘的持久化数据，转换成内存中的对象。

**为什么不用 Java 的序列化：**

> Java 的序列化是一个重量级序列化框架（Serializable），一个对象被序列化后，会附带很多额外的信息（各种校验信息，Header，继承体系等），不便于在网络中高效的传输。所以，Hadoop 自己开发了一套序列化机制（Writable）

**Hadoop 序列化特点：**

> - 紧凑：高效实用存储空间
> - 快速：读写数据的额外开销小
> - 可扩展：随着通信协议的升级而升级
> - 互操作：支持多语言的交互

### 2、序列化自定义步骤

> - 必须实现 Writable 接口
> - 反序列化时，需要反射调用空参构造函数，所以必须有空参构造器
> - 重写序列化方法
> - 重写反序列化方法
>   - 注意反序列化和序列化的顺序必须完全一致
> - 要想把结果显示在文件中，需要重写 toString() 方法
> - 如果需要将自定义的 bean 放在 key 中传输，则还需要实现 Comparable 接口，因为 MapReduce 框架中的 Shuffle 过程要求对 key 必须能排序

### 3、序列化案例

#### 1、案例需求说明

![bean序列化案例](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/bean%E5%BA%8F%E5%88%97%E5%8C%96%E6%A1%88%E4%BE%8B.png)

#### 2、序列化案例之 bean 

```java
/**
 * 序列化 bean 需要实现 Writable 接口
 */
public class FlowBean implements Writable {

    /**
     * 手机上行流量
     */
    private long upFlow;

    /**
     * 手机下行流量
     */
    private long downFlow;

    /**
     * 手机总流量
     */
    private long sumFlow;

    public FlowBean() {
    }

    public FlowBean(long upFlow, long downFlow) {
        this.upFlow = upFlow;
        this.downFlow = downFlow;
        this.sumFlow = upFlow + downFlow;

    }

    /**
     * 序列化方法
     * @param dataOutput
     */
    @Override
    public void write(DataOutput dataOutput) throws IOException {
        dataOutput.writeLong(upFlow);
        dataOutput.writeLong(downFlow);
        dataOutput.writeLong(sumFlow);
    }

    /**
     * 反序列化方法
     * @param dataInput
     */
    @Override
    public void readFields(DataInput dataInput) throws IOException {
        // 反序列化的顺序必须和序列化的顺序一致（序列化的时候是把序列化的对象放到队列里面，先进先出，因此反序列化也要按顺序进行）
        upFlow = dataInput.readLong();
        downFlow = dataInput.readLong();
        sumFlow = dataInput.readLong();

    }

    @Override
    public String toString() {
        return upFlow +
                "\t" + downFlow +
                "\t" + sumFlow;
    }
	// getter setter 方法...
}
```

#### 3、序列化案例之 Mapper

```java
/**
 * Mapper 代码
 * 泛型第一个参数表示偏移量
 * 第二个参数表示读取一行内容
 * 第三个参数表示 Mapper 输出的时候用手机号作为 key
 * 第四个参数表示 Mapper 输出的时候用 FlowBean 作为 value
 *
 */
public class FlowCountMapper extends Mapper<LongWritable,Text,Text,FlowBean> {

    Text k = new Text();
    FlowBean v = new FlowBean();
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        /*
         * 输入数据格式：
         * 7 18375438796 172.31.3.196 1116 954 200
         */
        // 1.获取一行
        String line = value.toString();  // value 就是这一行数据的内容,是 Text 类型，需要把它变成 String 类型

        // 2.使用 \t 切割这一行内容
        String[] fields = line.split("\t");

        // 3. 封装对象
        k.set(fields[1]);   // 封装手机号作为 key
        long upFlow = Long.parseLong(fields[fields.length - 3]);
        long downFlow = Long.parseLong(fields[fields.length - 2]);
        v.setUpFlow(upFlow);
        v.setDownFlow(downFlow);

        // 4. 写出
        context.write(k,v);
    }
}
```

#### 4、序列化之 Reducer

```java
/**
 * Reducer 代码
 * 泛型第一个和第二个参数是 Mapper 的输出参数
 * 第三个参数：输出的 key ：用手机号作为输出的 key
 * 第四个参数：输出的 value ：用 FlowBean 作为输出的 value
 */
public class FlowCountReducer extends Reducer<Text,FlowBean,Text,FlowBean> {
    FlowBean v = new FlowBean();
    /**
     *
     * @param key 传进来的 key 是手机号
     * @param values 传进来的 values 是 FlowBean 集合
     * @param context
     */
    @Override
    protected void reduce(Text key, Iterable<FlowBean> values, Context context) throws IOException, InterruptedException {
        // 手机号    上行流量 下行流量
        // 18375438907 2481 793
        // 18375438907 8976 676

        // 1.累加求和
        long sumUpflow = 0L;
        long sumDownflow = 0L;
        for (FlowBean flow : values) {
            sumUpflow += flow.getUpFlow();
            sumDownflow += flow.getDownFlow();
        }
        long sum = sumUpflow + sumDownflow;
        v.setUpFlow(sumUpflow);
        v.setDownFlow(sumDownflow);
        v.setSumFlow(sum);

        // 2.写出
        context.write(key,v);
    }
}
```

#### 5、序列化之 Driver

```java
/**
 * Driver 代码
 */
public class FlowCountDriver {
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        // 1. 获取 Job 对象
        Job job = Job.getInstance(new Configuration());
        // 2. 设置 jar 路径
        job.setJarByClass(FlowCountDriver.class);
        // 3. 关联 Mapper 和 Reducer
        job.setMapperClass(FlowCountMapper.class);
        // 4. 设置 Mapper 输出的 key 和 value 类型
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(FlowBean.class);
        // 5.设置最终输出的 key 和 value 类型
        job.setOutputKeyClass(Text.class);
        job.setMapOutputValueClass(FlowBean.class);
        // 6.设置输入输出路径
        FileInputFormat.setInputPaths(job,new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        // 7.提交 job
        boolean result = job.waitForCompletion(true);
        System.exit(result?0:1);

    }
}
```

## 八、MapReduce 框架原理

#### InputFormat数据输入

##### 1、MapReduce切片和 MapTask 并行度决定机制

MapTask 的并行度决定 Map 阶段的任务处理并发度，进而影响到整个 job 的处理速度。

**MapTask 并行度决定机制**

> 数据块：Block 是 HDFS 物理上把数据分成一块一块
>
> 数据切片：数据切片只是在逻辑上对输入进行分片，并不会在磁盘上将其切分成片进行存储

> - **一个 Job 的 Map 阶段并行度由客户端在提交 Job 时的切片数决定**
> - 每一个切片分配一个 MapTask 并行实例处理
> - 默认情况下，切片大小等于 BlockSize
> - 切片时不考虑数据集整体，而是逐个针对每一个文件单独切片

##### 2、FileInputFormat 实现类

在运行 MapReduce 程序时，输入的文件格式包括：基于行的日志文件、二进制格式文件、数据库表等。针对不同的数据类型，MapReduce 是如何读取这些数据呢？

> FileInputFormat 常见的接口实现类包括：TextInputFormat、KeyValueTextInputFormat、NLineInputFormat、CombineTextInputFormat 和自定义InputFormat

##### 3、TextInputFormat

TextInputFormat 是默认的 FileInputFormat 实现类。按行读取每条记录。**键是存储该行在整个文件中的起始字节偏移量，LongWritable 类型。值是这行的内容，不包括任何行终止符。（换行符和回车符），Text 类型。**

**示例：**

```
一个分片包含了下面 4 条文本记录：
hello hi
hello shp
heping heping
shp

每条记录表示为以下键值对：
(0,hello hi)
(10,hello shp)
(21,heping heping)
(36,shp)
```

##### 4、KeyValueTextInputFormat

每一行均为一条记录，被分隔符分割为 key 和 value。可以通过在驱动类中设置 `conf.set(KeyValueLineRecordReader.KEY_VALUE_SEPERATOR,"\t")` 来设定分隔符。默认分隔符是 tab（"\t"）。

**示例**

```
line1	shp nihao
line2	heping hi
line3	lalalaabchi

每条记录表示为以下键值对：
(line1,shp nihao)
(line2,heping hi)
(line3,lalalaabchi)
```

![KeyValueTextInputFormat案例](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-hadoop/KeyValueTextInputFormat%E6%A1%88%E4%BE%8B%E5%88%86%E6%9E%90.png)

**Mapper 代码：**

```java
/**
 * 输入数据：
 *   shp  ni hao
     heping hi
     shp lihai
     heping happy
   期望输出数据：
     shp  2
     heping 2
 */
public class KVTextMapper extends Mapper<Text,Text,Text,IntWritable> {
    IntWritable v = new IntWritable(1); // 遍历一个 key 就把它的 value 次数计为 1次
    @Override
    protected void map(Text key, Text value, Context context) throws IOException, InterruptedException {
        // 我想统计每一行第一个单词相同的次数，所以我只关心第一个单词，至于 value 是什么不重要

        // 写出   shp ni hao
        context.write(key,v);   // key 就是每一行第一个单词, v 是 1
    }
}
```

**Reducer 代码**

```java
public class KVTextReducer extends Reducer<Text,IntWritable, Text, IntWritable> {
    IntWritable v = new IntWritable();
    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        // <shp,1>
        // <shp,1>
        // 累加求和
        int sum = 0;
        for (IntWritable value : values) {
            sum += value.get();
        }
        v.set(sum);

        // 写出
        context.write(key,v);
    }
}
```

**Driver 代码：**

```java
public class KVTextDriver {
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {

        args = new String[]{"",""};
        Configuration configuration = new Configuration();
        // 设置切割符，用空格进行切割
        configuration.set(KeyValueLineRecordReader.KEY_VALUE_SEPERATOR," ");
        // 1.获取 job 对象
        Job job = Job.getInstance(configuration);
        // 2.设置 jar 存储路径
        job.setJarByClass(KVTextDriver.class);
        // 3. 关联 mapper 和 reducer 类
        job.setMapperClass(KVTextMapper.class);
        job.setReducerClass(KVTextReducer.class);
        // 4. 设置 mapper 输出的 key 和 value 类型
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);
        // 5. 设置最终输出 key 和 value 类型
        job.setOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);
        // 6. 设置输入输出路径
        FileInputFormat.setInputPaths(job, new Path(args[0]));
        FileOutputFormat.setOutputPath(job, new Path(args[1]));
        job.setInputFormatClass(KeyValueTextInputFormat.class);
        // 7. 提交 job
        job.waitForCompletion(true);
    }
```

##### 5、NLineInputFormat

如果使用 NLineInputFormat，代表每个 map 进程处理的切片不再按 Block 块去划分，而是按 NLineInputFormat 指定的行数 N 来划分。即**输入文件的总行数/N = 切片数**，如果不整除，切片数=商+1

**示例：**

```java
hello hi
hello shp
heping heping
shp

如果 N 是 2，则每个输入分片包含两行。开启 2 个 MapTask
(0,hello hi)
(10,hello shp)
另一个 mapper 则收到后两行
(21,heping heping)
(36,shp)
 
这里的键和值与 TextInputFormat 生成的一样
```

**mapper**

```java
public class NLineMapper extends Mapper<LongWritable, Text,Text,IntWritable> {
    Text k = new Text();
    IntWritable v = new IntWritable(1);
    @Override
    protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
        // hello hi
        // 1.获取一行
        String line = value.toString();
        // 2. 切割
        String[] words = line.split(" ");
        for (String word : words) {
            k.set(word);
            context.write(k,v);
        }

    }
}
```

**reducer**

```java
public class NLineReducer extends Reducer<Text,IntWritable,Text,IntWritable> {
    IntWritable v = new IntWritable();
    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
        // 1.累计求和
        int sum = 0;
        for (IntWritable value : values) {
            sum += value.get();
        }
        v.set(sum);
        // 2. 写出
        context.write(key,v);
    }
}
```

**Driver**

```java
public class NLineDriver {
    public static void main(String[] args) throws IOException, ClassNotFoundException, InterruptedException {
        args = new String[]{"/Users/shenheping/Desktop/mr","/Users/shenheping/Desktop/output"};
        Configuration configuration = new Configuration();
        // 1. 获取 job 对象
        Job job = Job.getInstance(configuration);
        // 2.设置输出的 jar 路径
        job.setJarByClass(NLineDriver.class);
        // 3.关联 Mapper 和 Reducer
        job.setMapperClass(NLineMapper.class);
        job.setReducerClass(NLineReducer.class);
        // 4.设置 Mapper 输出的 kv 类型
        job.setMapOutputKeyClass(Text.class);
        job.setMapOutputValueClass(IntWritable.class);
        // 5.设置最终输出的 kv 类型
        job.setOutputKeyClass(Text.class);
        job.setOutputValueClass(IntWritable.class);
        // 6.设置输入输出数据路径
        FileInputFormat.setInputPaths(job,new Path(args[0]));
        FileOutputFormat.setOutputPath(job,new Path(args[1]));
        // 7. 设置每个切片 InputSplit 中划分三条记录（三行一个切片）
        NLineInputFormat.setNumLinesPerSplit(job, 3);
        // 8. 使用 NLineInputFormat 处理记录数
        job.setInputFormatClass(NLineInputFormat.class);
        // 7.提交 job
        job.waitForCompletion(true);
    }
}
```

##### 6、自定义 InputFormat

在企业开发中，Hadoop 框架自带的 InputFormat 类型不能满足所有应用场景，需要自定义 InputFormat 来解决实际问题。

自定义 InputFormat 步骤：

> 1、自定义一个类继承 `FileInputFormat`
>
> 2、改写 `RecordReader` ，实现一次读取一个完整文件封装为 KV
>
> 3、在输出时使用 `SequenceFileOutPutFormat` 输出合并文件

**案例：**

无论 HDFS 还是 MapReduce ，在处理小文件时效率都非常低，但又难免面临处理小文件的场景。此时，就需要有相应的解决方案。可以自定义 InputFormat 实现小文件的合并。

**需求：**

将多个小文件合并成一个 SequenceFile 文件（SequenceFile 文件是 Hadoop 用来存储二进制形式的 key-value 对的文件格式），SequenceFile 里面存储着多个文件，存储的形式为文件路径 + 名称  为 key，文件内容为 value

**案例分析：**

> 1. 自定义一个类继承 `FileInputFormat`
>
>    1. 重新 isSplitable() 方法，返回 false 不可切割
>    2. 重写 createRecordReader() ，创建自定义的 RecordReader 对象，并初始化
>
> 2. 改写 RecordReader ，实现一次读取一个完整文件封装为 KV
>
>    1. 采用 IO 流一次读取一个文件输出到 value 中，因为设置了不可切片，最终把所有文件都封装到了 value 中
>    2. 获取文件路径信息 + 名称，并设置 key
>
> 3. 设置 Driver
>
>    - ```java
>      //1.设置输入的 InputFormat
>      job.setInputFormatClass(WholeFileFormat.class);  // 这里是你自定义的 inputFormat
>      //2.设置输出的 outputFormat
>      job.setOutputFormatClass(SequenceFileOutputFormat.class) 
>      ```
>
> 





















































































































































































































