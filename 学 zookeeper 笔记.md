# 学 zookeeper 笔记 

------

### 一、Zookeeper 概述及工作机制

#### 1、Zookeeper 概述

Zookeeper 是一个开源的分布式的，为分布式应用提供协调服务的 Apache 项目

#### 2、Zookeeper 工作机制

Zookeeper 从设计模式的角度来理解：是一个基于观察者模式设计的分布式服务管理框架，它负责存储和管理大家都关心的数据，然后接受观察者的注册，一旦这些数据的状态发生变化，Zookeeper 就负责通知已经在 Zookeeper 上注册的那些观察者做出相应的反应，从而实现集群中类似 Master/Slave 管理模式

### 二、Zookeeper 特点

> 1、Zookeeper 是由一个领导者（leader） ，多个跟随者（follower）组成的集群
>
> 2、Leader 负责进行投票的发起和决议，更新系统状态
>
> 3、Follower 用于接收客户请求并向客户端返回结果，在选举 Leader 过程中参与投票
>
> 4、**集群中只要有==半数以上==节点存活，Zookeeper 集群就能正常服务**
>
> ```
> 1、假如我现在有五台 zk 集群，(半数就是 2.5) 挂了两台 zk 集群还是可以正常服务的,但是挂了三台,zk 集群就不能正常服务了
> 2、假如我现在有四台 zk 集群，(半数就是 2) 挂了一台 zk 集群还是可以正常服务的,但是挂了两台,zk 集群就不能正常服务了（必须要半数以上,等于不行）
> 3、假如我现在有三台 zk 集群，(半数就是 1.5) 挂了一台 zk 集群还是可以正常服务的,但是挂了两台,zk 集群就不能正常服务了
>
> 三台和四台 zk 集群,如果挂了结果是一样的,但是挂了以后明显是 三台集群利用率高
> ```
>
> **所以我们在部署 zk 集群的时候都是部署奇数台**
>
> 5、全局数据一致：每个 server 保存一份相同的数据副本， client 无论连接到哪个 server ，数据都是一致的
>
> 6、更新请求顺序进行，来自同一个 client 的更新请求按其发送顺序依次执行
>
> 7、数据更新原子性，一次数据更新要么成功，要么失败
>
> 8、实时性，在一定范围内，client 能读到最新的数据

### 三、Zookeeper 数据结构

Zookeeper 数据模型的结构与 Unix 文件系统很类似，整体上可以看做是一棵树，每个节点称做一个 ZNode。每一个 ZNode 默认能够存储 1MB 的数据，每个 ZNode 都可以通过其路径唯一标识

![zookeeper数据结构图](http://pd1skrfhx.bkt.clouddn.com/zk%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E5%9B%BE.png)

### 四、Zookeeper 安装

1、将从官网下载好的 tar 包拷贝到 /opt 目录下解压

2、进入 /opt/zookeeper-3.4.10/conf   这个路径下  修改 zoo_sample.cfg   为 zoo.cfg

3、在 /opt/zookeeper-3.4.10   目录下创建一个文件夹叫 zkData（这个文件夹用来存放 zk 中存储的数据的，如果你不创建这个文件夹，默认是存在 /tmp 文件夹下）

4、使用  vim zoo.cfg   修改配置文件中 dataDir 的路径为  /opt/zookeeper-3.4.10/zkData

### 五、操作 Zookeeper 

1、启动 Zookeeper

> 进入到  /opt/zookeeper-3.4.10/bin 目录下，使用命令 ./zkServer.sh start    启动 Zookeeper 服务

2、查看状态

> 使用命令 ./zkServer.sh status

3、启动客户端

> 使用命令 ./zkCli.sh 

4、退出客户端

> 使用命令 quit

5、停止 Zookeeper 服务

> 进入到  /opt/zookeeper-3.4.10/bin 目录下，使用命令 ./zkServer.sh stop    停止 Zookeeper 服务

### 六、Zookeeper 配置文件参数说明

```properties
#zookeeper 配置文件 zoo.cfg:

#通信的心跳数,就是 zookeeper server 和服务器端通信的心跳数,说白点就是多长时间访问一次，这里是 2s 访问一次
tickTime=2000 
#leader 和 follower 的初始化通信时间，这里是 10 * 2000 就是不超过 20 秒
initLimit=10
#leader 和 follower 的同步时间，这里是 5 * 2000 就是不超过 10 秒
syncLimit=5
#数据文件目录和数据持久化路径
dataDir=/opt/zookeeper-3.4.10/zkData
#客户端连接断开,比如说你用 java 客户端想要去访问 zookeeper server 那么端口号就是 2181
clientPort=2181
```



### 七、Zookeeper 内部选举机制

**说明：**

Zookeeper 虽然在配置文件中并没有指定 master 和 slave 。但是，Zookeeper 工作时，是有一个节点为 leader，其它为 follower，Leader 是通过内部的选举机制产生的。

举个例子来说明 Zookeeper 的选举过程：

> 假设现在有五台服务器组成的 Zookeeper 集群，它们的 id 从 1-5，同时他们是最新启动的，也就是没有历史数据，所以在数据存放量上，五台服务器都是一样的，假设这些服务器安装顺序启动：
>
> 第一台服务器启动以后，它会进行投票，投给它自己，然后将票数跟 5 的半数比，因为只有 1 票，没有超过 5 的半数，所以第一台服务器处于 LOOKING 状态
>
> 第二台服务器启动以后，它也会进行投票，投给它自己，然后第一台服务器也会把票投给 2 号服务器，因为在投票选举时，id 值大的获胜。所以 2 号服务器现在有两票了，但是它还是没有超过 5 的半数，所以 1 号和 2 号服务器都处于 LOOKING 状态
>
> 第三台服务器启动的时候，和前面一样，因为它的 id 值大，所以 1 号和 2 号会把票投给 3 号，加上它自己的 1 票，总共 3 票，超过 5 的半数。所以 3 号当选为 leader
>
> 第四台和第五台启动的时候，虽然他们的 id 值很大，但是集群中已经有 leader 了，所以他们只能是 follower

### 八、Zookeeper 节点类型

#### 1、Znode 有两种类型

> - 短暂（ephemeral）：客户端和服务端断开连接后，创建的节点自己删除
> - 持久（persistent）：客户端和服务端断开连接后，创建的节点不删除

#### 2、Znode 有四种形式的目录节点（默认是 persistent）

> - 持久化目录节点（PERSISTENT）
>
> 客户端与 Zookeeper 断开连接后，该节点依旧存在
>
> - 持久化顺序编号目录节点（PERSISTENT_SEQUENTIAL）
>
> 客户端与 Zookeeper 断开连接后，该节点依旧存在，只是 Zookeeper 给该节点名称进行顺序编号
>
> - 临时目录节点（EPHEMERAL）
>
> 客户端与 Zookeeper 断开连接后，该节点被删除，只是Zookeeper 给该节点名称进行顺序编号





### 九、集群安装

> - 解压安装
>
> 首先是解压安装，和单击模式一样：
>
> 1、解压 zookeeper 安装包到 /opt 目录下
>
> 2、在  /opt/zookeeper-3.4.10 这个目录下创建 zkData 目录
>
> 3、重命名 /opt/zookeeper-3.4.10/conf   这个路径下  zoo_sample.cfg   为 zoo.cfg
>
> - 配置 zoo.cfg 配置文件
>
> 使用 vim 命令编辑 zoo.cfg 配置文件：
>
> 添加以下配置：
>
> server.1=172.31.3.195:2888:3888
>
> server.2=172.31.3.196:2888:3888
>
> server.3=172.31.3.197:2888:3888
>
> ```
> 解释一下上面的配置：
> Server.A=B:C:D
>
> A 是一个数字,表示这个是第几号服务器
> B 是这个服务器的 ip 地址
> C 是这个服务器与集群中 Leader 服务器交换信息的端口
> D 是万一集群中 Leader 服务器挂了,需要一个端口来重写进行选举,选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口
>
> 集群模式下需要配置一个文件 myid 这个文件在 dataDir 目录下,这个文件里面有一个数据就是 A 的值, Zookeeper 启动时读取此文件,拿到里面的数据与 zoo.cfg 里面的配置信息比较从而判断到底是哪个 server
> ```
>
> - 启动 Zookeeper 集群
>
> 可以在几台服务器上依次执行 zkServer.sh start 命令        然后可以使用   zkServer.sh status 查看状态

**注：**

==在启动 zookeeper 集群之前一定要确保几台服务器的防火墙是关闭的==

### 十、客户端命令行操作

1、启动 Zookeeper 客户端

> 进入到 bin 目录下，执行 『 ./zkCli.sh』命令即可启动客户端

2、显示所有操作命令

> 在 zookeeper 客户端，使用 『help』命令，可以展示所有 zookeeper 的操作命令

3、查看当前 znode 中包含的内容

> 在 zookeeper 客户端，使用 『ls /』 命令，可以查看根目录下有哪些节点，或者你可以把根目录换成别的目录，就是查看指定目录下有哪些节点

4、查看当前节点数据并能看到更新次数

> 在 zookeeper 客户端，使用 『ls2 /』 命令，可以查看到根目录下有哪些节点，并把节点的详细信息给你展示出来

5、创建普通节点

==在创建节点的时候必须要往节点里面写点数据，不然节点是不会被创建成功的==

> 在 zookeeper 客户端，使用『create /family father』命令
>
> 这个命令的意思是：
>
> 我在根节点下面创建一个 family 节点，然后往 family 节点里面写一个信息，信息内容是 father
>
> 这时候如果你使用 『ls /』命令，你就会发现在根节点下面会有两个节点「zookeeper，family」

6、获得节点的值

> 在 zookeeper 客户端，使用『get /family』命令，可以获取 family 这个节点里面的值

7、创建短暂节点

> 在 zookeeper 客户端，使用『create -e /hobby basketball』命令
>
> 这时候如果你使用 『ls /』命令，你就会发现在根节点下面会有三个节点「zookeeper，family，hobby」
>
> 但是当你退出客户端，然后在进入客户端时，你使用 『ls /』命令看一下，你会发现只有 「zookeeper，family」这两个节点了，因为刚才创建的 hobby 节点是短暂节点，退出客户端后就没有了

8、创建带序号的节点

> 在 zookeeper 客户端，使用『create -s /family/father watchTV』命令
>
> 这个命令的意思是：
>
> 我在 family 节点下面再创建一个 father 节点，这个节点里面的内容是 watchTV
>
> 这时候你使用 『ls /family』命令，你会发现创建的节点带序号 [ father0000000000]

9、修改节点的数据值

> 在 zookeeper 客户端，使用『set /family/father0000000000 watchTV2』命令
>
> 这样就把原来 father0000000000 节点中的内容修改为 watchTV2

10、节点的值变化监听

> 在 zookeeper 客户端，使用『get /hobby watch』命令
>
> 这样就监听了 hobby 这个节点值得变化，假如我在集群中另一台机器的客户端修改了 hobby 这个节点的值，那么我在当前机器就可以监听到值得变化。
>
> 在当前机器的客户端会出现下面内容：
>
> WatchedEvent state:SyncConnected type:NodeDataChanged path:/hobby

注意：

==这个监听是一次性的。也就是说你第二次再去改节点的值就不会再被监听到了，如果还想被监听到，那么还需要使用『get /hobby watch』命令来再一次启动监听==

11、节点的子节点变化监听

> 在 zookeeper 客户端，使用『ls / watch』命令
>
> 这样我就监听了根节点下，所有子节点的变化，假如我在集群中另一台机器的客户端又新创建了一个节点，那么我在当前机器就可以监听到节点的变化
>
> 在当前机器的客户端会出现下面的内容：
>
> WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/

注意：

==同上。监听也是一次性的==

12、删除节点

> 在 zookeeper 客户端，使用『delete /flower』命令，可以删除根节点下面的 flower 节点

13、递归删除节点

> 在 zookeeper 客户端，使用『rmr /family』命令，如果 family 节点下面还有子节点，可以递归删除

14、查看节点状态

> 在 zookeeper 客户端，使用『stat /hobby』命令，可以查看这个节点的状态

### 十一、监听器原理

![](http://pd1skrfhx.bkt.clouddn.com/zookeeper%E7%9B%91%E5%90%AC%E5%99%A8%E5%8E%9F%E7%90%86.png)

#### 1、监听器原理详解：

1）、首先要有一个主线程main()线程

2)、在 main() 线程中创建 zookeeper 客户端，这时候就会创建两个线程，一个负责网络连接通信（connect），一个负责监听（Listener）

3）、通过 connect 线程将注册的监听事件发送给 zookeeper

4)、在 zookeeper 的注册监听器列表中将注册的监听事件添加到列表中

5）、zookeeper 监听到有数据或者路径变化，就会将这个消息发送给 listener 线程

6）、listener 线程内部调用 process() 方法

#### 2、常见的监听

1）、监听节点数据变化

get path [watch]

2)、监听子节点增减的变化

ls path [watch]

### 十二、zookeeper 写数据流程

![zookeeper 写数据流程](http://pd1skrfhx.bkt.clouddn.com/zookeeper%20%E5%86%99%E6%95%B0%E6%8D%AE%E6%B5%81%E7%A8%8B.png)



### 十三、zookeeper 初始化客户端、创建子节点、获取子节点

```java
public class TestZookeeper {


    public static void main(String[] args) throws IOException, KeeperException, InterruptedException {
        getChild();


    }
    //连接 zk server 集群的地址,这样集群中任何一个节点都可以连上
    private static String connectStr = "192.168.31.229:2181";
    //session 超时时间设置
    private static int sessionTimeout = 2000;
    //初始化客户端
    public static ZooKeeper initClient() throws IOException {
        /**
            前面我们也说了，你 new 一个 zk 客户端，它同时就会产生两个线程，一个负责连接 server
            一个负责监听，一旦你监听的事件发生了变化，就会调用 process() 方法

            而且这里你拿到的客户端其实和你在 linux 命令行使用的客户端其实是一样的,只不过一个是通过命令行
            一个是通过代码
         */
        ZooKeeper zkClient = new ZooKeeper(connectStr, sessionTimeout, new Watcher() {
            @Override
            public void process(WatchedEvent event) {
                System.out.println("节点的路径" + event.getPath() + "节点的类型" + event.getType());


            }
        });
        return  zkClient;
    }

    //创建子节点
    public static void create() throws IOException, KeeperException, InterruptedException {
        ZooKeeper zkClient = initClient();
        /**
         * path:创建子节点的路径
         * data：子节点里面存放的数据
         * acl：节点的权限
         * createMode：节点类型,是永久节点还是临时节点
         *
         */
        String path = zkClient.create("/hello","你好帅".getBytes(), ZooDefs.Ids.OPEN_ACL_UNSAFE, CreateMode.PERSISTENT);
        System.out.println("创建成功后的路径：" + path);

    }

    //获取子节点
    public static void getChild() throws IOException, KeeperException, InterruptedException {
        ZooKeeper zkClient = initClient();
        List<String> children = zkClient.getChildren("/", false);
        for (String child : children) {
            System.out.println(child);
        }
        
        //判断节点是否存在a
    public static void exist() throws IOException, KeeperException, InterruptedException {
        ZooKeeper zkclient = initClient();
        Stat stat = zkclient.exists("/hobby", false);
        System.out.println(stat == null ? "节点存在" : "节点不存在");

    }
    }
```













