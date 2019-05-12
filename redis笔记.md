# Redis 笔记

### 一、NoSql 入门和概述

#### 1、入门概述

##### 1.1、互联网时代背景下大机遇，为什么用 nosql

**1、单机 mysql 架构模式**

如果一个网站的访问量不大，用单个数据库完全可以轻松应付。也就是我们传统的架构模式，jsp 去找 controller 然后 controller 去找 service ，service 再去找 dao ，dao 最后去访问数据库实例。抽象来看的话，这种架构可以分为三层：

![](http://pdcp5zn89.bkt.clouddn.com/%E5%8D%95%E6%9C%BAmysql%E6%9E%B6%E6%9E%84.png)

下面说下这种架构的瓶颈：

1️⃣  当数据量的总大小一台机器放不下时

2️⃣  数据的索引（B+ Tree）一个机器的内存放不下时

3️⃣访问量（读写混合）一个数据库实例不能

**2、Memcached（缓存）+ MySQL + 垂直拆分**

![](http://pdcp5zn89.bkt.clouddn.com/%E7%BC%93%E5%AD%98+%E5%9E%82%E7%9B%B4%E6%8B%86%E5%88%86.png)

说一下上面的这种架构模式：

上面单机模式的架构师 dao 层直接访问数据库，现在我在数据库前面加上一个缓存，让 dao 层访问缓存。所以对数据库来说，如果你频繁的查询数据库中的一些固定数据，那么我们就可以把这些固定数据拿出来放缓存里面，减轻数据库被访问的压力。

**3、主从读写分离**

![](http://pdcp5zn89.bkt.clouddn.com/%E4%B8%BB%E4%BB%8E%E8%AF%BB%E5%86%99%E5%88%86%E7%A6%BB.png)

说明：

由于数据库写入压力增加，Memcached 只能缓解数据库的读取压力。读写集中在一个数据库让数据库不堪重负，大部分网站开始使用主从复制技术来达到读写分离，以提高读写性能和读库的可扩展性。mysql 的 master-slave 模式成为网站的标配。我读操作都在 msater 库上进行，写操作在 slave 库上。master 库写入数据后会复制到 slave 库。

**4、为什么要使用 NoSQL ？**

国内的 NoSQL 数据库起源于国外，国外随着 facebook 和 twiter 的兴起，传统的关系型数据库，一行一列，不足以来支撑这么复杂的关系网络

##### 1.2、NoSQL 是什么

NoSQL = Not Only SQL 指的是不仅仅是 SQL，泛指非关系型数据库。这些类型的数据存储不需要固定的模式，无需多余的操作就可以横向扩展。 

##### 1.3、NoSQL 能干什么

1）、易扩展

NoSQL 数据库种类繁多，但是有一个共同特点就是去掉关系数据库的关系型特性。数据之间无关系，这就非常容易扩展。也无形间在架构层面上带来了可扩展的能力。

2）、大数据量高性能

NoSQL 数据库都具有非常高的读写性能，尤其在大数据量下，同样表现优秀。这得益于它的无关系性，数据库的结构简单。

3）、多样灵活的数据模型

NoSQL 无需事先为要存储的数据建立字段，随时可以存储自定义的数据格式。而在关系型数据里，增删字段是一件非常麻烦的事。如果是非常大数据量的表，增加字段简直就是一个噩梦。

#### 2、CAP 定理

> C    Consistency（强一致性）
>
> A    Availability（可用性）
>
> P   Partition tolerance（分区容错性）

CAP 理论的核心是：一个分布式系统不可能很好的满足一致性，可用性，和分区容错性这三个需求，==最多只能同时较好的满足其中两个==

### 二、Redis 入门概述

#### 1、redis 是什么

redis : REmote DIctionary Server（远程字典服务器）是完全开源免费的，用 C 语言编写的，遵守 BSD 协议，是一个高性能的（key-value）分布式内存数据库，基于内存运行，并支持持久化的 NoSQL 数据库，是当前最热门的 NoSQL 数据库之一，也被人们称为数据结构服务器。

**redis 与其他 key-value 缓存产品有以下三个特点：**

> 1、redis 支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用
>
> 2、redis 不仅仅支持简单的 key-value 类型的数据，同时还提供 list ，set ，zset，hash 等数据结构的存储
>
> 3、redis 支持数据的备份，即 master-slave 模式的数据备份

#### 2、redis 安装

以 redis-3.0.4 为例：

1、使用 tar -xzvf redis-3.0.4.tar.gz 命令，将 redis 解压到 /opt 目录下，出现文件夹：redis-3.0.4

2、进入目录 ：cd /opt/redis-3.0.4

3、在 redis-3.0.4 目录下执行 『make』 命令

```
你执行 make 命令的时候，可能会出现 gcc 命令未找到，这时候就需要先安装 gcc
gcc 简单的来说就是 c 程序的编译器

安装 gcc:
如果可以上网的话：
使用命令：『yum install gcc-c++』安装即可
装完之后使用命令：『gcc -v 』可以查看到 gcc 的版本信息
```

4、再次 执行 『make』命令（ 上一次 『make』因为没有安装 gcc 所以失败了，因此需要再次 make 一下）

```
你再次 make 的时候可能还会出现错误『Jemalloc/jemalloc.h:没有那个文件或目录』，这是因为你上次 make 不成功残留的一些文件。
执行命令 『make distclean』
最后再一次 make 就可以成功了
```

5、安装完成之后，会提示你执行 『make test』，建议不要执行

6、在 redis-3.0.4 目录下执行 『make install』，这样会把 redis 的一些常用命令默认安装到 /usr/local/bin 目录下

7、在 redis-3.0.4 目录下，将  redis.conf 文件拷贝一份做个备份，例如我在根目录下建立一个文件夹 myredis 把 redis.conf 拷贝一份到这个文件夹下，然后启动的时候带上这个配置文件即可

8、修改 redis.conf 配置文件。将配置文件里面 daemonize no 改成 yes，让服务在后台启动

9、启动 redis。

```
先去到 /usr/local/bin 目录下
然后使用命令：
./redis-server /myredis/redis.conf                这里后面跟的是 redis 的配置文件
启动完之后，使用命令：
./redis-cli -p 6379             6379是默认端口号
然后在客户端输入 ping 来判断服务是否启动成功，如果得到的响应的 PONG 证明服务启动成功，或者你也可以直接查进程
```

### 三、redis 基础知识

1、redis 默认有 16 个数据库，数据库下标从 0 开始，默认使用 0 号库

2、可以使用 『select』 命令切换数据库

3、可以使用 『dbsize』 命令查看当前数据库的 key 的数量，使用 『keys *』 命令可以查看出当前库所有的 key

4、可以使用 flushdb 清空当前库，使用 flushall 命令清空所有库，16 个库都会被清空

### 四、redis 数据类型

#### 1、redis 五大数据类型

1️⃣ String（字符串）

String 类型是 redis 最基本的数据类型。string 类型是二进制安全的，意思是 redis 的 string 可以包含任何数据，比如 Jpg 图片或者序列号对象。一个 redis 中字符串 value 最多可以是 512 M

2️⃣ Hash（哈希，类似 java 里面的 map）

Redis hash 是一个键值对集合，是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象

3️⃣ List（列表）

redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或尾部（右边）它的底层实际是个链表

4️⃣ Set（集合）

redis 的 set 是 string 类型的无序集合

5️⃣ Zset（sorted set 有序的集合）

redis zset 和 set 一样也是 string 类型元素的集合，且里面元素不允许重复。不同的是每个元素都会关联一个 double 类型的分数。redis 正是通过分数来为集合中的成员进行从小到大的排序。zset 的成员是唯一的，但分数却可以重复。

#### 2、redis 命令文档

http://redisdoc.com/

#### 3、redis 键（key）

```
1、keys *    查看当前库中所有的 key
2、exists keyname     判断某个 key 是否存在
3、move keyname dbnum      把当前库中的 keyname 这个 key 移动到 dbnum 这个库里面
4、expire keyname 秒钟      为给定的 key 设置过期时间
5、ttl keyname         查看还有多少秒过期，-1 表示永不过期    -2 表示已过期
6、type keyname         查看你的 key 是什么类型
```

#### 4、redis 字符串（String）

```
set k1 v1      设置 key 为 k1 值为 v1
get k1        获取 k1 的值
del k1        删除 k1
append k1 abc       在 k1 的值后面追加 abc(得到的结果就是 v1abc)
strlen k1          返回 k1 值的长度
incr k1           假设 k1 的值是 2，那么你执行这个命令就会把 k1 的值递增 1，再执行一次再递增 1
decr k1            执行这个命令会把 k1 的值递减 1
incrby k1 3         执行这个命令会把 k1 的值递增 3，执行一次递增 3
decrby k1 3         执行这个命令会把 k1 的值递减 3，执行一次递减 3
getrange k1 0 3      范围内取值，假设我现在 k1 的值为 14abc，执行命令后的结果就是 14ab
setrange k1 1 mm     范围内设值，假设我现在 k1 的值为 14abc，执行命令后的结果就是 1mmbc
setex k4 10 v4       设置 k4 的值为 v4并且过期时间为 10 秒
mset k1 v1 k2 v2      同时设置多个key 和 value ，体现到 java 代码中就是你直接把 map 的key-value往redis 塞就可以了
mget k1 k2           同时获取多个 value 值
```

#### 5、redis 列表（List）

```
lpush list01 1 2 3 4 5       设置 key 为 list01 value 为一个集合，值为 1 2 3 4 5
lrange list01 0 -1           返回 list01 中所有的值,结果为 5 4 3 2 1
rpush list02 1 2 3 4 5         设置 key 为 list01 value 为一个集合，值为 1 2 3 4 5
lrange list02 0 -1           返回 list02 中所有的值,结果为 1 2 3 4 5
```

**lpush 和 rpush 区别：**

> lpush 设置一个集合，使用 lrange 返回集合中的元素是逆序输出的
>
> rpush 设置一个集合，使用 lrange 返回集合中的元素是顺序输出，怎么设置怎么出来

 

```
现在假设我已经执行了两条命令：
lpush list01 1 2 3 4 5 
rpush list02 1 2 3 4 5 

其次 pop 是出栈的命令，lpop 表示从左边出栈，rpop 表示从右边出栈(一次只出栈一个)
lpop list01   结果是 5 出去了(因为你 lrange list01 0 -1 结果 5 是在栈顶所以先出去的是 5)
lpop list02   结果是 1 出去了(因为你 lrange list02 0 -1 结果 1 是在栈顶所以先出去的是 1)
rpop list01   结果是 1 出去了(从右边出栈，所以栈底的先出去)
rpop list02   结果是 5 出去了(从右边出栈，所以栈底的先出去)

lindex list01 3  按照索引下标获得元素(这个就是获取 key 为 list01 的 value 值中下标为 3 的元素)
llen list01      返回 list01 中元素的个数

假设我现在先执行了命令：
rpush list01 1 2 3 3 4 4 4 4 5 5
lrem list01 3 4     删除 key 为 list01 的 value 值，具体是删除 3 个 4

假设我先执行了命令：
lpush list01 1 2 3 4 5 6
ltrim list01 1 3      截取指定范围的 key 再赋值给 key(这里就是截取 list01 的 value 值，从下标 1 开始截取，到下标 3 结束，然后再把截取的结果赋值给 list01)所以结果为 5 4 3

假设我先执行了命令：
lpush list01 1 2 3 4
lpush list02 5 6 7 8
lrange list01 0 -1      结果就是 4 3 2 1
lrange list02 0 -1      结果就是 8 7 6 5
其次要知道 pop 是出栈，push 是进栈
 RPOPLPUSH lis01 list02     意思就是 list01 从右边出栈一个，进栈到 list02 中
 lrange list02 0 -1     结果就是 1 8 7 6 5
 
 『lset key index value命令』：
 假设执行命令：
 lrange list01 0 -1    结果是  4 3 2 1
lset list01 2 x     这个就是把下边为 2 的 value 值设置为了 x
lrange list01 0 -1     结果是 4 3 x 1

 『linsert key before/after value1 value2 命令』：
 假设执行命令：
 lrange list01 0 -1    结果是  4 3 2 1
 linsert list01 before 3 linux   意思就是往 key 为 list01 的集合中插入值，在 value 值为 3 之前插入一个值是 linux
  lrange list01 0 -1    结果是 4 linux 3 2 1
  linsert list01 after 2 windows   意思就是往 key 为 list01 的集合中插入值，在 value 值为 2 之后插入一个值是 windows
lrange list01 0 -1    结果是 4 linux 3 2 windows 1
```

**关于 List 总结：**

> 1、它是一个字符串链表，left 和 right 都可以插入、添加
>
> 2、如果键不存在，创建新的链表
>
> 3、如果键已存在，新增内容
>
> 4、如果值全移除，对应的键也就消失了
>
> 5、链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了

#### 6、redis 集合（Set）

```
sadd set01 1 1 2 2 3 3     往 set 集合里面插入数据，key 是 set01 ，因为 set 里面不允许重复所以你插入重复的数据，会被覆盖掉。所以 value 就是 1 2 3 三个值
smembers set01         返回 key 为 set01 集合的 value 值(结果是 1 2 3)
sismember set01 2      返回 key 为 set01 的集合中是否包含元素 2，包含返回 1 ，不包含返回 0
scard set01            获取集合中元素的个数
--------------------------------------------------------
『srem key value』 命令     删除集合中的元素
srem set01 2           这个就是把 set01 集合中的元素 2 给删除
smembers set01       结果就是 1 和 3 ，元素 2 已经被删除了
----------------------------------------------------------
『srandmember key 整数』命令     随机返回几个整数
sadd list02 1 2 3 4 5 6
srandmember list02 3        这样就会从集合 list02 中随机返回 3 个整数
----------------------------------------------------------------
『spop key』 命令        随机出栈集合中的元素
spop set02          会把 set02 中的元素随机出栈一个
-----------------------------------------------------------------
『smove key1 key2 key1里面的某个元素』命令      把 key1 中某个元素移动到 key2 中
sadd set01 1 2 3 4 5
sadd set02 x y z 
smove set01 set02 3        这样就把 set01 中的元素 3 移动到了 set02 中
smembers set02          结果就是 y 3 x z
-------------------------------------------------------------------
『sdiff key1 key2』命令      两个集合取差集，结果是返回第一个集合中有的元素，而第二个集合中没有的元素
sadd set01 1 2 3 4 5
sadd set01 1 2 3 a b
sdiff set01 set02        结果就是 4 5
『sinter key1 key2』命令     两个集合取交集
sinter set01 set02          结果是 1 2 3
『sunion key1 key2』命令     两个集合取并集
sunion set01 set02          结果是 1 2 3 4 5 a b

```

#### 7、redis 哈希（Hash）

**关于哈希可以概括为：KV 模式不变，但 V 是一个键值对**

```
hset user id 1         这里的 key 是 user 但是 value 又是一个键值对(key 为 id，value 是 1)
hset user name xy
hget user id            返回 1
hget user name          返回 xy
------------------------------------
hmset customer id 1 name zs age 18          可以一次性设置多个 key 和 value
hmget customer id name age           结果就是 1 zs 18
hgetall customer       一次性获取所有，返回 id 1 name zs age 18
-------------------------------------------------------------
hdel customer name    把 name 这个 key 给删除
hgetall customer       返回 id 1 age 18
-----------------------------------------------------------
hlen customer        查询 key 是 customer 这个哈希的 value 值中 key 的个数(结果是 2)
---------------------------------------------------------------
hexists customer id      判断 key 是 customer 这个哈希的 value 值中是否存在 key 是 id 的，返回 1 表示存在，0 表示不存在
--------------------------------------------------------------
 hkeys customer    查看 key 是 customer 这个哈希的 value 值中所有的 key (结果是 id age)
hvals customer     查看 key 是 customer 这个哈希的 value 值中所有的 value (结果是 1 18)
----------------------------------------------------------------------------------
hincrby customer age 2     key 是 customer 这个哈希的 value 值中 key 为 age 的 value 值递增 2
hset customer score 2.5
hincrbyfloat customer score 0.5 key 是 customer 这个哈希的 value 值中 key 为 age 的 value 值递增 0.5  (结果就是 3)
```

#### 8、redis 有序集合 Zset（sorted set）

Zset 其实就是在 set 基础上加了一个 score 值。之前 set 是 k1 v1 v2 v3  ，那么现在 zset 是 k1 score1 v1 score2 v2，相当于它的 value 值又是一个 k-v 键值对

```
zadd zset01 60 v1 70 v2 80 v3 90 v4 100 v5     向 zset 中插入数据，key 是 zset01 value 值又是一个 k-v 键值对,其中 key 是 score 值
zrange zset01 0 -1    显示 zset 中的元素(结果为 v1 v2 v3 v4 v5)
zrange zset01 0 -1 withscores  显示 zset 中的元素，连同 score 一起显示出来(结果为v1 70 v2 80 v3 90 v4 100 v5)
------------------------------------------------------------------------
『zrangebyscore key 开始score 结束score』命令
zrangebyscore zset01 60 80        返回 score 值在 60 到 80 之间的(结果就是 v1 v2 v3)
zrangebyscore zset01 （60 （80      "("  表示不包含(结果是 v2)
zrangebyscore zset01 60 90 limit 2 2   在返回结果上进行筛选，从下标为 2 的元素开始取，取 2 个(结果是 v3 v4)
-------------------------------------------------------
zrem zset01 v5   删除元素
--------------------------------------------
zcard zset01    返回元素个数，返回 4(因为上面删除了一个)
---------------------------------------------------
zcount zset01 60 80      统计 key 为 zset01 score 值在 60-80之间的个数(结果返回 3)
```

### 五、redis 配置文件

![](http://pdcp5zn89.bkt.clouddn.com/redis-unit.png)

1、redis 配置文件中 1k 和 1kb 表示的大小是不一样的

2、单位大小写不敏感。 1GB 和 1Gb 是一样的

### 六、redis 持久化

#### 1、RDB

##### 1.1、RDB 是什么

在指定时间间隔内将内存中的数据集快照写入磁盘，也就是行话说的 Snapshot 快照，它恢复时是将快照文件直接读到内存里。

Redis 会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何 IO 操作的，这就确保了极高的性能。如果需要进行大规模的数据恢复，且对于数据恢复的完整性不是非常敏感，那 RDB 方式要比 AOF 方式更加的高效。RDB 的缺点是最后一次持久化后的数据可能会丢失。

##### 1.2、FORK

Fork 的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程。

**注：**

**RDB 保存的是 dump.rdb 文件** 

![](http://pdcp5zn89.bkt.clouddn.com/redis-rdb%E9%85%8D%E7%BD%AE.png)

RDB 是整个内存压缩过的 Snapshot。默认：

15 分钟改了 1次

5 分钟改了 10 次

1 分钟改了 1 万次

上面三中情况都会生成一个 dump.rdb 文件

#### 2、AOF

我们知道 RDB 最大的缺陷就是会丢失最后一次备份的数据。其实这个影响也不是很大，假设你 15 分钟备份一次，那么最后 15 分钟的数据丢失，你要说近一年的数据丢失了，运维工程师恢复不了，最近 15 分钟数据丢失了，运维工程师还是可以恢复的。

##### 2.1 AOF 是什么

AOF ——> Append Only File 

==以日志的形式来记录每个写操作==，将 redis 执行过的所有写指令记录下来（读操作不记录），只许追加文件但不可以改写文件， redis 启动之初会读取该文件重新构建数据。换言之，redis 重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

**注：**

**AOF保存的是 appendonly.aof 文件** 

##### 2.2、 Rewrite

AOF 采用文件追加的方式，文件会越来越大为了避免出现此类情况，新增了重新机制。当 AOF 文件的大小超过所设定的阈值时，Redis 就会启动 AOF 文件的内容压缩，只保留可以恢复数据的最小指令集。可以使用命令 ==bgrewriteaof==

**重写的原理：**

> 当 AOF 文件持续增长过大时，会 fork 出一条新进程来将文件重写（也是先写临时文件最后再 rename）遍历新进程内存中的数据，每条记录都会有一条 set 语句。重写 AOF 文件的操作，并没有读取旧的 AOF 文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的 AOF 文件。这点和快照有点类似。

**触发机制**

> Redis 会记录上次重写时的 AOF 大小，默认配置是当 AOF 文件大小是上次 rewrite 后大小的一倍且文件大于 64M 时触发

**优势：**

> 每修改同步：appendfsync always  同步持久化，每次发生数据变更会被立即记录到磁盘（只要修改了就同步），这种方式性能较差，但数据完整性比较好。
>
> 每秒同步：appendfsync everysec    异步操作，每秒同步，如果一秒内宕机，有数据丢失
>
> 不同步：appendfsync no   从不同步

**劣势：**

> 1、对于相同数据集的数据而言 AOF 文件要远大于 RDB 文件，恢复速度慢于 RDB 文件
>
> 2、AOF 的运行效率要慢于 RDB ，每秒同步测量效率较好，不同步效率和 RDB 相同

**总结：**

![](http://pdcp5zn89.bkt.clouddn.com/redis-aof%E6%80%BB%E7%BB%93.png)

### 七、redis 事务

#### 1、redis 事务是什么

可以一次执行多个命令，本质是一组命令的集合。一个事务中的所有命令都会序列化，按顺序的串行化执行而不会被其它命令插入，不许加塞。

#### 2、redis 事务常用命令

> 1、discard     取消事务，放弃执行事务块内所有的命令
>
> 2、exec     执行所有事务块内的命令
>
> 3、multi     标记一个事务块的开始
>
> 4、unwatch    取消 watch 命令对所有 key 的监视
>
> 5、watch key[key….]     监视一个（或多个） key ，如果在事务执行之前这个（或这些）key 被其它命令所改动，那么事务将被打断。

#### 3、redis 事务正常执行

![redis事务正常执行](http://pdcp5zn89.bkt.clouddn.com/redis%E4%BA%8B%E5%8A%A1%E6%AD%A3%E5%B8%B8%E6%89%A7%E8%A1%8C.png)

```
127.0.0.1:6379> MULTI          开启一个事务
OK
127.0.0.1:6379> set k1 v1       在事务中需要执行的 redis 执行
QUEUED                           返回的这个表示执行在队列中，还没有执行
127.0.0.1:6379> set k2 v2
QUEUED
127.0.0.1:6379> get k1     在事务中可以写任何 redis 指令，等你使用 exec 命令执行的时候，会一起返回结果
QUEUED
127.0.0.1:6379> set k3 v3
QUEUED
127.0.0.1:6379> EXEC           相当于提交事务执行
1) OK
2) OK
3) "v1"
4) OK
```

#### 4、redis 放弃事务

```
127.0.0.1:6379> MULTI           在这里我开启了事务
OK
127.0.0.1:6379> set k1 11      在事务中执行 redis 命令
QUEUED
127.0.0.1:6379> set k2 22
QUEUED
127.0.0.1:6379> DISCARD      在这里我突然不想执行本次操作了，那么就可以使用这个命令来取消事务
OK
127.0.0.1:6379> get k1     在这里发现 get k1 获取到的值，还是原来的值。因为你取消事务了
"v1"
```

#### 5、redis 事务一个失败整体失败

```
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> set k1 a
QUEUED
127.0.0.1:6379> set k2 b
QUEUED
127.0.0.1:6379> set k3 c
QUEUED
127.0.0.1:6379> getsetfds             这里随便乱写了一个命令，只要有一条命令执行失败，整个事务就失败
(error) ERR unknown command 'getsetfds'
127.0.0.1:6379> set k4 d
QUEUED
127.0.0.1:6379> EXEC               这里提交事务发现是失败的
(error) EXECABORT Transaction discarded because of previous errors.
127.0.0.1:6379>
```

#### 6、redis 事务一个失败部分失败

```
127.0.0.1:6379> get k1        我们首先 get k1 得到它的值是 aa
"aa"
127.0.0.1:6379> MULTI         开启事务
OK
127.0.0.1:6379> incr k1     这里我给 k1 的值递增一个，这里的 redis 命令是完全没有问题的，所以把命令加入
QUEUED                      了队列里面，等到你执行的时候才会发现，它的值不能递增，但命令在这是没有问题的
127.0.0.1:6379> set k2 33
QUEUED
127.0.0.1:6379> set k3 44
QUEUED
127.0.0.1:6379> EXEC           这里提交事务的时候，因为 k1 的值不能递增，所以第一条命令执行失败
1) (error) ERR value is not an integer or out of range
2) OK                           其它命令是执行成功的
3) OK
127.0.0.1:6379> get k2   
"33"                              这里获取到的值，是刚提交事务后的值。
127.0.0.1:6379> get k3
"44"
127.0.0.1:6379>
```

**注：**

> 这里的第六点和上面的第五点做下区分，上面第五点是你开启事务后，执行 redis 命令直接就报错了，根本就没有这个 redis 命令，这个命令根本没有加入到事务要执行的队列中，所以你提交事务后，整个事务直接失败。而第六点是，你开启事务后，执行的 redis 命令是正确的，而这条命令也被加入到队列中了，所以当你提交事务执行的时候，才会发现哪条命令执行有问题，有问题的命令执行不成功，每有问题的命令执行成功。
>
> 这就相当于 java 里面的 IO ，你在写的时候就要给它抛出一个异常，但是想 2/0 这种，只有等运行的时候才会发现异常。

所以关于 redis 是否支持事务？这个问题应该这么来回答：

> redis 是部分支持事务，它不像 mysql 和 oracle 那样是绝对强一致性。它可能在执行事务过程中，出现对的放心，错的就不成功

#### 7、wacth 监控

##### 1、悲观锁、乐观锁、CAS（Check And Set）

**1.1 悲观锁**

> 悲观锁，顾名思义就是我对这个事情的发展很悲观，认为一定会有问题产生，这其实就是一个悲观情绪。所以这时候我为了不出事，把整张表都给你锁了。
>
> 其实悲观锁和乐观锁有点类似于 mysql 里面的行锁和表锁。这里我们先说下 mysql 里面的行锁和表锁。
>
> 表锁意思就是我对整张表进行加锁。言下之意就是这张表此时此刻只能允许我一个人改。假设这张表只有两条记录，那么你把表锁了，问题不大。但是假设这张表有 20 万条记录，你操作的是第 163 条记录，这时候你把表锁了，但是别人想操作的是第 226 条记录，和你操作的记录不冲突，但是因为你把表锁了，另一个线程就只能等待。这样会出现的情况就是等待的线程越来越多。
>
> ==所以表锁最大的特点就是并发性极差，但是一致性很好==
>
> 行锁就是对你要操作的那一行进行加锁，这样别的行每有加锁，别的线程也就可以同时进行操作了，这样并发性比较好。
>
> 悲观锁的应用：
>
> 就是你在进行数据库备份的时候，你肯定希望这时候不要再有人往数据库里面写东西了，所以就可以加一把悲观锁



**1.2 乐观锁**

> 乐观锁，顾名思义就是对事情的发展很乐观。就认为我的数据一定没有人进行修改，这其实就是一个乐观的情绪。但是假设真有人对记录进行修改怎么办呢？乐观锁想了这么一种办法，既可以保证并发性又可以保证不把整张表锁住，它是这么做的：
>
> ==就是在每条记录后面加一个版本号字段==，假设现在张三和李四都去修改同一条记录，数据库中的这条记录的版本号是 1，这时候李四先修改完了，提交了事务，这时候版本号就会加 1，这条记录的版本号变成了 2，那么等张三再提交了时候，发现版本号不对（张三是在 1 的基础上改的，但是现在版本号已经变成了 2），就会报异常，事务提交失败。需要获取最新版本号对应的记录，在此基础上修改才能提交成功。这样张三提交后也不会影响李四提交的内容。
>
> **乐观锁策略：**
>
> ==提交版本必须大于记录当前版本才能执行更新==

```
初始化信用卡余额和欠额
127.0.0.1:6379> set balance 100
OK
127.0.0.1:6379> set debt 0
OK
无加塞篡改，先监控再开启 multi，保证两笔金额变动在同一事务内
127.0.0.1:6379>  watch balance 
OK
127.0.0.1:6379> MULTI
OK
127.0.0.1:6379> decrby balance 20        这里吧余额减 20
QUEUED
127.0.0.1:6379> incrby debt 20            同时欠额加 20
QUEUED
127.0.0.1:6379> EXEC
1) (integer) 80
2) (integer) 20

第一个窗口先执行 watch
127.0.0.1:6379>  watch balance 
OK
再另开一个窗口
127.0.0.1:6379> get balance
"80"
127.0.0.1:6379> set balance 800      这里我直接把 余额改成了 800
OK
第一个窗口执行下面命令
127.0.0.1:6379> MULTI      开启事务
OK
127.0.0.1:6379> decrby balance 20   这里我又想把余额给减 20，但是我不知道余额已经被改了
QUEUED
127.0.0.1:6379> incrby debt 20
QUEUED
127.0.0.1:6379> EXEC              所以这里提交事务就没有成功
(nil)
```

**注：**

一旦执行了 unwatch 和 exec 提交事务后，之前加的监控锁都会被取消掉

**小结：**

watch 指令类似于乐观锁，事务提交时如果 key 的值已经被别的客户端改变，那么整个事务队列都不会被执行。通过 watch 命令，在事务执行之前监控了多个 keys ，倘若在 watch 之后有任何 key 的值发生了变化，EXEC 命令执行的事务都将会被放弃，同时返回 NUllmuti-bulk 应答以通知调用者事务执行失败。

#### 8、redis 事务特性

> 1、单独的隔离操作：
>
> 事务中所有的命令都会序列化、按顺序的执行。事务在执行的过程中，不会被其它客户端发送来的命令请求打断
>
> 2、没有隔离级别的概念：
>
> 队列中的命令没有提交之前都不会被实际的执行，因为事务提交前任何指令都不会被实际的执行，也就不存在“事务内的查询要看到事务里的更新，在事务外的查询不能看到”这个让人万分头痛的问题。
>
> 3、不保证原子性：
>
> redis 同一个事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚。

### 八、Redis 发布和订阅

#### 1、redis 发布订阅是什么

它是进程间的一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息

**注：**

这个了解一下就可以了，真正企业中消息中间件肯定不会用 redis 去做的

### 九、redis 的复制（Master/Slave）

#### 1、redis 的复制是什么

也就是我们说的主从复制，主机数据更新后根据配置和策略，自动同步到备机的 master/slaver 机制，Master 以写为主，Slave 以读为主

#### 2、redis 主从复制相关配置

假设你部署 3 台 redis ,当你把 3 台 redis 都启动起来之后，会发现 3 台都是 Master。这时候你可以通过命令：

==slaveof 主库ip 主库端口==     来配置这台机器成为 slave ，但是这样做会有问题，就是你 slave 一断开再次连接就不是 slave 了，除非你把配置写到配置文件中，redis 一启动就知道这台 redis 的 master 是谁

在部署主从复制 redis 时，我们通常需要修改以下配置

```
port 6379     redis 连接的端口号，默认是 6379
logfile "6379.log"   日志文件的名称
dbfilename dump6379.rdb      生成的 rdb 文件名称
appendfilename "appendonly6379.aof"    生成的 aof 文件名称，你如果没有开启 aof 这个可以不改
```

### 十、jedis 操作 redis

#### 1、测试 jedis 是否连接成功

```java
public class TestPing {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.31.6",6379);
        System.out.println(jedis.ping());     //打印 PONG 证明连接成功
    }
}
```

#### 2、常用的 API

```java
public class TestAPI {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.31.6",6379);
        jedis.set("k1","v1");
        jedis.set("k2","v2");
        jedis.set("k3","v3");
        System.out.println(jedis.get("k2"));   //打印 v2
        Set<String> keys = jedis.keys("*");
        for (String set : keys) {
            System.out.println(set);
        }
    }
}
//这些 API 和你在 redis 命令行使用的 API 一样，只不过换成了用 Java 代码去调用
```

#### 3、事务简单使用

```java
public class TestTracsaction {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.31.6",6379);
        Transaction tracsaction = jedis.multi();     //开启事务
        tracsaction.set("k1","v11");
        tracsaction.set("k2","v22");
        tracsaction.set("k3","v33");
        tracsaction.exec();     //提交事务
        //tracsaction.discard();   //放弃提交事务（回滚）
    }
}
```

#### 4、事务使用案例

```java
public class TestTransaction2 {

    public static boolean transMethod() {
        Jedis jedis = new Jedis("192.168.31.6",6379);
        int balance;    //可用余额
        int debt;      //欠额
        int amtToSubstract = 5;   //实刷额度
        jedis.watch("balance");
        balance = Integer.parseInt(jedis.get("balance"));
        if (balance < amtToSubstract) {
            jedis.unwatch();
            System.out.println("已经被别人修改过了");
            return false;
        } else {
            System.out.println("*********开启事务");
            Transaction transaction = jedis.multi();
            transaction.decrBy("balance",20);
            transaction.incrBy("debt",20);
            transaction.exec();
            System.out.println("*********提交事务");
            balance = Integer.parseInt(jedis.get("balance"));
            debt = Integer.parseInt(jedis.get("debt"));
            return true;
        }
    }

    /**
     * 通俗点讲,watch 命令就是标记一个键,如果标记了一个键,在提交事务前如果该键被别人
     * 修改过,那事务就会失败。这种情况通常可以在程序中再尝试一次。
     * 首先标记了键 balance,然后检查余额是否足够,不足就取消标记,并不做扣减
     * 足够的话，就启动事务进行更新操作
     * 如果在此期间,键 balance 被其它人修改过,那再提交事务(执行 exec()方法)就会报错
     * 程序中通常可以捕获这类错误再重新执行一次,直到成功
     */
    public static void main(String[] args) {
        boolean retValue = transMethod();
        System.out.println("事务提交结果" + retValue);
    }
}
```





















