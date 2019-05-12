# Docker 笔记

### 一、Docker 简介

#### 1、Docker 是什么

**Docker 为什么会出现？**

> 一款产品从开发到上线，从操作系统到运行环境，再到应用配置，开发人员在开发环境开发好了以后，提交产物到测试，因为操作系统可能不同，或者应用配置可能不对，再加上版本不停的迭代，就可能出现在开发环境好好的产品，一到测试环境就会出现问题。
>
> Docker 之所以发展的这么迅速，就是因为提供了一个标准化解决方案。
>
> 环境配置如此麻烦，换一台机器就要重来一次，费力又费时。很多人想到能不能从根本上解决问题，软件可不可以带环境安装？也就是说安装的时候，把原始环境一模一样的复制过来，开发人员利用 Docker 就可以解决 “在我的机器上可以正常工作的问题”



**一句话总结下 Docker：**

解决了运行环境和配置问题软件容器，方便做持续集成并有助于整体发布的容器虚拟化技术

**Docker 安装条件：**

> Docker 支持以下 CentOS 版本：
>
> - CentOS 7（64-bit）
> - CentOS 6.5（64-bit）或更高版本
>
> 目前 CentOS 仅发行版本中的内核支持 Docker
>
> - Docker 运行在 CentOS 7 上，要求系统为 64 位、系统内核版本为 3.10 以上
> - Docker 运行在 CentOS 6.5 或更高版本上，要求系统为 64 位、系统内核版本为 2.6.32-431 以上

**查看自己内核信息：**

`uname -r`

**查看操作系统版本：**

`cat /etc/redhat-release` 

**Docker 三要素：**

1、镜像（image）

Docker 镜像（Image） 就是一个只读的模板。镜像可以用来创建 Docker 容器，一个镜像可以创建很多容器。

2、容器（container）

Docker 利用容器（Container）独立运行一个或一组应用。==容器是用镜像创建的运行实例==。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台

3、仓库（Repository）

仓库是集中存放镜像文件的场所。

仓库（Repository）和仓库注册服务器（Registry）是有区别的。仓库注册服务器上往往存放着多个仓库，每个仓库又包含多个镜像，每个镜像有不同的标签（Tag）

仓库分为公开仓库（public）和私有仓库（private）两种形式。最大的公开仓库是 DockHub（https://hub.docker.com/）存放了数量庞大的镜像供用户下载。国内的公开仓库包括阿里云、网易云等。

#### 2、安装 Docker

**centOS 6.8 安装：**

> 1、`yum install -y epel-release`
>
> - > Docker 使用 EPEL 发布，RHEL 系的 OS 首先要确保已经持有 EPEL 仓库（类似于装 redis 首先你要安装 gcc），否则先检查 OS 的版本，然后安装相应的 EPEL 包
>
> 2、`yum install -y  docker-io`
>
> 3、安装后的配置文件：`/etc/sysconfig/docker`
>
> 4、启动 docker 后台服务：`service docker start`
>
> 5、docker version 验证

**centOS 7 以上安装：**

下面的安装步骤来自于 docker 官网：https://docs.docker.com/install/linux/docker-ce/centos/

> 1、sudo yum install -y yum-utils   device-mapper-persistent-data  lvm2
>
> 2、sudo yum-config-manager     --add-repo     https://download.docker.com/linux/centos/docker-ce.repo
>
> 3、sudo yum-config-manager --enable docker-ce-nightly
>
> 4、sudo yum-config-manager --enable docker-ce-test
>
> 5、sudo yum install docker-ce docker-ce-cli containerd.io

#### 3、阿里云镜像加速配置

你从 docker hub 上拉取镜像会非常慢（duckhub 是国外的网站），所以你可以使用阿里云，它是把整个 docker hub 上的镜像克隆了一份放在了阿里云上。

**centOS 6.8 加速器配置：**

1、从阿里云上获取镜像加速器地址（个人专属地址参考：https://cr.console.aliyun.com/cn-hangzhou/mirrors）

2、配置本机 docker 运行镜像加速器

```shell
vim /etc/sysconfig/docker
将自己账户下的阿里云地址配置进
other_args="--registry-mirror=https://e3olo4nu.mirror.aliyuncs.com"

# https://e3olo4nu.mirror.aliyuncs.com  是我个人的加速器地址
```

3、重启启动 docker 后台服务

`service docker restart`

4、配置完加速器检查是否生效

```shell
[root@hepingfly sysconfig]# ps -ef |grep docker
root      25075      1  0 02:23 pts/0    00:00:00 /usr/bin/docker -d --registry-mirror=https://e3olo4nu.mirror.aliyuncs.com   # 查进程后出现这里的镜像后缀证明生效
root      25214  18696  0 02:24 pts/0    00:00:00 grep docker
```

**centOS 7 以上加速器配置：**

1、从阿里云上获取镜像加速器地址

2、配置本机 docker 运行镜像加速器

```shell
① vim /etc/docker/daemon.json
②
{
  "registry-mirrors": ["https://e3olo4nu.mirror.aliyuncs.com"]
}
③ sudo systemctl daemon-reload
```

3、重启启动 docker 后台服务

`sudo systemctl restart docker`

4、配置完加速器检查是否生效

检查方法同上

#### 4、docker 测试运行 hello-world

使用命令 `docker run hello-world`

```shell
Unable to find image 'hello-world:latest' locally
latest: Pulling from hello-world

65b27d3bd74d: Pull complete 
9f5834b25059: Pull complete 
Digest: sha256:fb158b7ad66f4d58aa66c4455858230cd2eab4cdf29b13e5c3628a6bfc2e9f05
Status: Downloaded newer image for hello-world:latest

Hello from Docker!     # 出现这一行和下面一行证明 docker 可以正常运行
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```

**解释下 docker run hello-world 这条命令到底做了什么：**

> 首先你执行这条命令后， docker 在本机中寻找 hello-world 这个镜像，如果本机中有该镜像，就以该镜像为模板生产容器实例运行，如果本机中没有该镜像，则去 Docker Hub 中查找该镜像，如果 Docker Hub 中有这个镜像，下载这个镜像到本地，然后以该镜像为模板生产容器实例运行，如果 Docker Hub 中没有该镜像，返回失败错误，查不到该镜像。

#### 5、docker 运行原理

**1）、docker 是怎么工作的**

> Docker 是一个 client-server 结构的系统，Docker 守护进程运行在主机上，然后通过 Socker 连接从客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器。容器，是一个运行时环境，就是我们前面所说的集装箱。

**2）、为什么 docker 比虚拟机快？**

> 1、docker 有比虚拟机更少的抽象层。由于 docker 不需要 Hypervisor 实现硬件资源虚拟化，运行在 docker 容器上的程序直接使用的都是实际物理机的硬件资源。因此在 CPU、内存利用率上 docker 将会在效率上有明显的优势。
>
> 2、docker 利用的是宿主机的内核，而不需要 Guest OS。因此，当新建一个容器时，docker 不需要和虚拟机一样重新加载一个操作系统的内核。因而避免引寻、加载操作系统内核这个比较费时费资源的过程，当新建一个虚拟机时，虚拟机软件需要加载 Guest OS ，这个新建过程是分钟级别的。而 docker 由于直接利用宿主机的操作系统，则省略了这个过程，因此新建一个 docker 容器只需要几秒钟。

#### 6、docker 帮助命令

> - docker verison
> - docker info
> - docker  - -help

#### 7、docker 镜像命令

1）、`docker images`

```shell
[root@hepingfly 桌面]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
hello-world         latest              9f5834b25059        6 weeks ago         1.84 kB
```

**解释下上面各个标签的含义：**

> - REPOSITORY：表示镜像的仓库源
> - TAG：镜像的标签
> - IMAGE ID：镜像 ID
> - CREATED：镜像创建时间
> - VIRTUAL SIZE：镜像大小
>
> 同一仓库源可以有多个 TAG，代表这个仓库源的不同版本，我们使用 `REPOSITORY:TAG` 来定义不同的镜像。
>
> 如果你不指定镜像的版本标签，例如你只使用 ubuntu，docker 将默认使用 `ubuntu:lastest` 镜像

**options 说明：**

> docker images 命令可以使用的参数：
>
> - `-a`：列出本地所有的镜像（含中间镜像层）
> - `-q`：只显示镜像 ID
> - `--digests`：显示镜像的摘要信息
> - `--no-trunc`：显示完整的镜像信息

```shell
[root@hepingfly 桌面]# docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
hello-world         latest              9f5834b25059        6 weeks ago         1.84 kB
<none>              <none>              65b27d3bd74d        6 weeks ago         1.84 kB
[root@hepingfly 桌面]# docker images -q
9f5834b25059
[root@hepingfly 桌面]# docker images -qa       #这两个组合使用就是返回所有镜像的id
9f5834b25059
65b27d3bd74d
```

2）、`docker search 镜像名称`

这条命令是去 docker hub 上查找这个镜像

**options 说明：**

> `docker search 镜像名称` 命令可以使用的参数：
>
> - `-s`：列出收藏数不小于指定值的镜像
> - `--no-trunc`：显示完整的镜像描述
> - `--automated`：只列出 automated build 类型的镜像

3）、`docker pull 镜像名称[:TAG]`

这条命令是去 docker hub 或者你自己配置的镜像地址去下载镜像

 ```shell
docker pull tomcat        # 这条命令等价于  docker pull tomcat:latest
# 如果你不写后面的 TAG 默认拉下的就是 latest
 ```

4）、`docker rmi 镜像名称/镜像ID`

这条命令是用来删除本地的镜像

> - 删除单个镜像：`docker rmi -f 镜像名/镜像ID`
> - 删除多个镜像：`docker rmi -f 镜像名1:TAG 镜像名2:TAG`
> - 删除全部：`docker rmi -f  $(docker images -qa)`

```shell
[root@hepingfly 桌面]# docker rmi hello-world    # 如果你不加 -r ，这个镜像如果在后台运行就会删不掉
Error response from daemon: Conflict, cannot delete 9f5834b25059 because the container e9071180c3a5 is using it, use -f to force
Error: failed to remove images: [hello-world]
[root@hepingfly 桌面]# docker rmi -f hello-world
Untagged: hello-world:latest
Deleted: 9f5834b25059239faef06a9ba681db7b7c572fc0d87d2b140b10e90e50902b53
Deleted: 65b27d3bd74d2cf4ea3aa9e250be6c632f0a347e8abd5485345c55fa6eed0258

#----------------------------------------------------------------------------------------
[root@hepingfly 桌面]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
tomcat              latest              2084f5ff262d        8 days ago          462.5 MB
nginx               latest              905bf115f76b        10 days ago         109.2 MB
[root@hepingfly 桌面]# docker rmi -f 2084f5ff262d 905bf115f76b

```

#### 8、docker 容器命令

**有镜像才能创建容器，这是根本的前提**

下面以 centos 镜像为例：

```shell
[root@hepingfly 桌面]# docker pull centos
[root@hepingfly 桌面]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
centos              latest              ab9a80ab07d0        10 weeks ago        201.8 MB
```

1）、新建并启动容器

`docker run [options] 镜像名称/镜像ID [COMMAND] [ARG]`

**options 说明：**

> - `--name 容器的新名字`：为容器指定一个名称
> - -d：后台运行容器，并返回容器 ID ，即：启动守护式容器
> - `-i`：以交互模式运行容器，通常与 -t 同时使用
> - `-t`：为容器重新分配一个伪输入终端，通常与 -i 同时使用
> - -P：随机端口映射
> - -p：指定端口映射，有以下四种格式：
>   - ip:hostPort:containerPort
>   - ip::containerPort
>   - hostPort:containerPort
>   - containerPort

```shell
[root@hepingfly 桌面]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
centos              latest              ab9a80ab07d0        10 weeks ago        201.8 MB
[root@hepingfly 桌面]# docker run -it ab9a80ab07d0
[root@8bc02a892944 /]# pwd
/
```

> 上面的 `docker run -it ab9a80ab07d0` 意思就是以 ab9a80ab07d0 镜像为模板，生成了一个容器实例（以交互模式运行这个容器）。`[root@8bc02a892944 /]` 这个命令提示符就表示我已经在 docker 容器里面了

2）、列出当前所有正在运行的容器

`docker ps [options]`

默认显示当前正在运行的 docker 进程

**options 说明：**

> - -a：列出当前所有正在运行的容器+历史上运行过的
> - -l：显示最近创建的容器
> - -n：显示最近 n 个创建的容器
> - -q：静默模式，只显示容器编号
> - `--no-trunc`：不截断输出

3）、退出容器

两种退出方式：

> - `exit`：容器停止退出
> - `ctrl + p + q` ：容器不停止退出（你使用 docker ps 还能查到这个进程）

4）、启动容器

`docker start 容器ID/容器名`

5）、重启容器

`docker restart 容器ID/容器名`

6）、停止容器

`docker stop 容器ID/容器名`

7）、强制停止容器

`docker kill 容器ID/容器名`

8）、删除容器

`docker rm 容器ID/容器名`

删除多个容器

> - `docker rm -f $(docker ps -a -q )`
> - `docker ps -a -1 | xargs docker rm`         （xargs 在 linux 中表示可变参数）

---------------------------------重要命令------------------------------

1）、启动守护式容器

`docker run -d 容器名/容器ID`

> 使用镜像 centos:latest 以后台模式启动一个容器
>
> docker run -d centos
>
> 然后你用 docker ps 查看会发现查不到进程，因为这时候容器已经退出
>
> ==重要：docker 容器后台运行，就必须要有一个前台进程==
>
> 这个是 docker 的机制问题，比如你的 web 容器，我们以 nginx 为例，正常情况下，我们配置启动服务只需要启动相应的 service 即可。例如 ：service nginx start 。但是这样做，nginx 为后台进程模式运行，就导致 docker 前台没有运行的应用，这样的容器后台启动后，会立即自杀因为他觉得没事可做了。所以最佳的解决方案是，将你要运行的程序以前台进程的形式运行

2）、查看容器日志

`docker logs -f -t --tail 容器ID/容器名`

> - `-t`：是加入时间戳
> - `-f`：跟随最新的日志打印
> - `--tail`：数字，显示最后多少条

3）、查看容器内运行的进程

`docker top 容器ID/容器名`

4）、查看容器内部细节

`docker inspect 容器ID/容器名`

> 这个命令会把容器的详细信息以 json 串的形式返回

5）、进入正在运行的容器并以命令行方式交互

`docker exec -it 容器ID/容器名 /bin/bash`

或者

`docker attach 容器ID/容器名`

> 两者的区别：
>
> - `attach` :直接进入容器启动命令终端，不会启动新的进程
> - `exec`：是在容器中打开新的终端，并且可以启动新的进程

```shell
[root@hepingfly 桌面]# docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
6aae6329aabb        centos              "/bin/bash"         14 minutes ago      Up 14 minutes                           desperate_cori      
[root@hepingfly 桌面]# docker attach 6aae6329aabb        # 重新进入到容器中并以命令行方式交互

[root@6aae6329aabb /]# 

---------------------------------------------------------------------------------------
# 下面这条命令是使用 exec 进入到容器中并以命令行方式交互
[root@hepingfly 桌面]# docker exec -it 6aae6329aabb /bin/bash
[root@6aae6329aabb /]# 


# 下面这条使用 exec 命令直接把容器中 /tmp 目录下的东西返回出来了
[root@hepingfly 桌面]# docker exec -it 6aae6329aabb ls /tmp
ks-script-h2MyUP  yum.log
[root@hepingfly 桌面]#

```

6）、从容器内拷贝文件到主机上

`docker cp 容器ID:容器内路径 目的主机路径`

 ```shell
# 这个我就是把 docker 容器中 /tmp/yum.log 文件拷贝到宿主机上的 /root 目录下
docker cp 6aae6329aabb:/tmp/yum.log /root
 ```

####  9、docker 镜像原理

**1）、镜像是什么**

> 镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、库、环境变量、配置文件等

**2）、docker 镜像加载原理**

> docker 的镜像实际上由一层一层的文件系统组成，这种层级的文件系统叫 UnionFS。
>
> bootfs（boot file system）主要包含 bootloader 和 kernel ，bootloader 主要是引导加载 kernel，Linux 刚启动的时候会加载 bootfs 文件系统，==在 Docker 镜像的最底层是 bootfs==。这一层与我们典型的 Linux/Unix 系统是一样的，包含 boot 加载器和内核。当 boot 加载完成之后整个内核就都在内存中了，此时内存的使用权已由 bootfs 转交给内核，此时系统也会卸载 bootfs。
>
> rootfs（root file system）在 boots 之上。包含的就是典型的 Linux 系统中的 /dev、/proc、/bin、/etc等标准目录和文件。rootfs 就是不同的操作系统的发行版本，比如 Ubantu、CentOS 等。
>
> 所以平时我们安装的 centOS 都有几个 G，为什么 Docker 这里才 200M？
>
> - > 对于一个精简的 OS ，rootfs 可以很小，只需要包括最基本的命令、工具和程序库就可以了，因为底层直接用 Host 的 Kernel，自己只需要提供 rootfs 就可以了。由此可见，对于不同的 linux 发行版本，bootfs 基本是一致的，rootfs 会有差别，因此不同的发行版可以共用 bootfs

为什么 docker 镜像要采用这种分层的结构呢？

> 最大的一个好处就是-共享资源
>
> 比如：有多个镜像都是从相同的 base 镜像构建而来，那么宿主机只需要在磁盘保存一份 base 镜像，同时内存中也只需加载一份 base 镜像，就可以为所有的容器服务了。而且镜像的每一层都可以被共享。

#### 10、docker 镜像 commit

docker commit 提交容器副本使之成为一个新的镜像

> `docker commit -m "提交的描述信息" -a "作者" 容器ID 要创建的目标镜像名:[标签名]` 

案例：

> 1、从 Hub 上下载 tomcat 镜像到本地并成功运行（docker run -it -p 8888:8080 tomcat）
>
> 2、故意删除上一步镜像生成的 tomcat 容器实例中 tomcat 的文档
>
> 3、也即当前运行的 tomcat 实例是一个没有文档内容的容器实例，以它为模板 commit 一个没有 doc 的 tomcat 新镜像 hepingfly/tomcat
>
> 4、启动我们的新镜像并和原来的进行对比，发现我们新提交的 tomcat 容器是没有 doc 的，原来的镜像启动后是有 doc 的

```shell
docker run -it -p 8888:8080 hepingfly/tomcat:1.1
```

#### 11、docker 容器数据卷

1）、数据卷是什么

> docker 容器产生的数据，如果不通过 docker commit 生成新的镜像，使得数据作为镜像的一部分保存下来，那么当容器删除之后，数据自然也就没有了
>
> 为了能保存数据在 docker 中我们使用卷

2）、数据卷能干嘛

> 卷就是目录或文件，存在于一个或多个容器中，由 docker 挂载到容器，但不属于联合文件系统，因此能绕过 Union File System 提供一些用于存储或共享数据的特性
>
> 卷的设计目的就是数据的持久化，完全独立于容器的生命周期，因此 Docker 不会在容器删除时删除其挂载的数据卷
>
> 特点：
>
> 1、数据卷可在容器之间共享或重用数据
>
> 2、卷中的更改可以直接生效
>
> 3、数据卷中的更改不会包含在镜像的更新中
>
> 4、数据卷的生命周期一直持续到没有容器使用它为止

总结一下就是：

> 1、容器的持久化
>
> 2、容器间继承 + 共享数据

**3）、容器数据卷添加**

**直接命令添加：**

> `docker run -it -v /宿主机绝对路径目录:/容器内目录 镜像名`

**Dockerfile 添加：**

> - 根目录下新建 mydocker 文件夹并进入
>
> - 可在 Dockerfile 中使用 ==VOLUME== 指令来给镜像添加一个或多个数据卷
>
>   - > `VOLUME["/dataVolumeContainer","/dataVolumeContainer2","/dataVolumeContainer3"]`
>     >
>     > 由于可移植性和分享的考虑，用「-v 主机目录:容器目录 这种方法不能够直接在 Dockfile 中实现」，由于宿主机目录是依赖特定宿主机的，并不能保证在所有宿主机上都存在这样的特定目录
>
> - File 构建
>
>   - ```
>     使用 vim 命令编写如下 dockerfile
>     FROM centos
>     VOLUME ["/dataVolumeContainer","/dataVolumeContainer2"]
>     CMD echo "finished.....success1"
>     CMD /bin/bash
>     ```
>
> - docker build 新镜像
>
>   - ```shell
>     [root@hepingfly mydocker]# docker build -f /mydocker/Dockerfile -t hepingfly/centos .
>     Sending build context to Docker daemon 2.048 kB
>     Sending build context to Docker daemon 
>     Step 0 : FROM centos
>      ---> ab9a80ab07d0
>     Step 1 : VOLUME /dataVolumeContainer /dataVolumeContainer2
>      ---> Running in c6c36177a5ed
>      ---> 31e8aa5330fb
>     Removing intermediate container c6c36177a5ed
>     Step 2 : CMD echo "finished.....success1"
>      ---> Running in 5c2c3121c5d7
>      ---> 90d350ddef71
>     Removing intermediate container 5c2c3121c5d7
>     Step 3 : CMD /bin/bash
>      ---> Running in 170b593d849b
>      ---> 95f5b3dbbdcf
>     Removing intermediate container 170b593d849b
>     Successfully built 95f5b3dbbdcf
>     ```
>
> - run 容器
>
>   - 你会发现容器跑起来之后就会在根目录下，生成 `/dataVolumeContainer /dataVolumeContainer2` 两个容器卷
>
> - 通过以上步骤容器内卷目录地址已经知道，但是对应的主机目录地址在哪呢？
>
>   -  使用 `docker inspect 容器id` 命令
>
>   - ```json
>     "Volumes": {
>             "/dataVolumeContainer": "/var/lib/docker/volumes/c7b9ad9cd398da9c576ba190e46ddede46c2fdbeeaca7640b508d2286977e0fe/_data",
>             "/dataVolumeContainer2": "/var/lib/docker/volumes/8288698ab9101287e5cdb597a5c93c45644151d505293fe8ac32291c0037e97a/_data"
>         }
>
>     可以看到这个 json 串，后面长的地址就是对应的主机地址
>     ```
>
>   - ​

#### 12、数据卷容器

**1）、是什么**

命名的容器挂载数据卷，其他容器通过挂载这个（父容器）实现数据共享，挂载数据卷的容器，称之为数据卷容器。

**2）、容器间传递共享**

使用`--volumes-from` 

1、我先启动一个父容器

```shell
[root@hepingfly 桌面]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
hepingfly/centos    latest              95f5b3dbbdcf        52 minutes ago      201.8 MB
hepingfly/tomcat    1.1                 85a41e9a5c10        24 hours ago        462.6 MB
tomcat              latest              2084f5ff262d        11 days ago         462.5 MB
centos              latest              ab9a80ab07d0        10 weeks ago        201.8 MB
[root@hepingfly 桌面]# docker run -it --name doc01 hepingfly/centos
[root@3568a925d5ae /]# 
[root@3568a925d5ae /]# cd dataVolumeContainer2
[root@3568a925d5ae dataVolumeContainer2]# touch aa.txt
```

hepingfly/centos 这个容器是前面我用 dockerfile 写好的，一启动在根目录下就会有两个容器卷

```sh
在宿主机上执行以下命令：
[root@hepingfly mydocker]# docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
3568a925d5ae        hepingfly/centos    "/bin/sh -c /bin/bas   2 minutes ago       Up 2 minutes                            doc01  
```

下面我们让 doc02 和 doc03 继承自 doc01==（下面的命令重要）==

```shell
[root@hepingfly mydocker]# docker run -it --name doc02 --volumes-from doc01 hepingfly/centos
[root@194657457bc8 /]#
[root@194657457bc8 /]# cd dataVolumeContainer2
[root@194657457bc8 dataVolumeContainer2]# ll
total 0
-rw-r--r--. 1 root root 0 Feb 20 14:38 aa.txt

我们进去 dataVolumeContainer2 这个容器卷，发现也有在 doc01 容器里面创建的文件，因为 doc02 是继承自 doc01 的
```

```shell
[root@hepingfly 桌面]# docker run -it --name doc03 --volumes-from doc01 hepingfly/centos
[root@75499443f3e0 /]# cd dataVolumeContainer2
[root@75499443f3e0 dataVolumeContainer2]# ll
total 0
-rw-r--r--. 1 root root 0 Feb 20 14:38 aa.txt
```

这样不管我在 doc01 还是 doc02 还是 doc03 里面操作，数据都可以共享，甚至你把父容器，doc01 删掉，数据共享也没有问题。

==结论：容器之间配置信息的传递，数据卷的生命周期一直到没有容器使用它为止==

#### 13、Dockerfile 解析

**1）、Dockerfile 是什么**

Dockerfile 是用来构建 Docker 镜像的构建文件，是由一系列的命令和参数构成的脚本

构建的三个步骤：

> 1、编写 Dockerfile 文件
>
> 2、docker build 
>
> 3、docker run

**2）、Dockfile 构建过程解析**

① Dockfile 内容基础知识

> - 每条保留字指令都必须为大写字母且后面要跟随至少一个参数
> - 指令按照从上到下，顺序执行
> - `#` 表示注释
> - 每条指令都会创建一个新的镜像层，并对镜像进行提交

② Docker 执行 Dockerfile 的大致流程

> 1、docker 从基础镜像运行一个容器
>
> 2、执行一条指令并对容器做出修改
>
> 3、执行类似 docker commit 的操作提交一个新的镜像层
>
> 4、docker 再基于刚提交的镜像运行一个新容器
>
> 5、执行 dockerfile 中的下一条指令直到所有指令都执行完成

小总结：

> - Dockerfile，需要定义一个 Dockerfile ，Dockerfile 定义了进程需要的一切东西。Dockerfile 涉及的内容包括执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程
> - Docker 镜像，在用 Dockerfile 定义一个文件之后，docker build 时会产生一个 Docker 镜像，当运行 Docker 镜像时，会真正开始提供服务
> - Docker 容器，容器是直接提供服务的

**③ Docker 保留字指令**

> - `FROM`：基础镜像，当前新镜像时基于哪个镜像的
>
> - `MAINTAINER`：镜像维护者的姓名和邮箱地址
>
> - `RUN`：容器构建时需要运行的命令
>
> - `EXPOSE`：当前容器对外暴露出的端口
>
> - `WORKDIR`：指定在创建容器后，终端默认登录进来的工作目录，一个落脚点
>
> - `ENV`：用来在构建镜像过程中设置环境变量
>
>   - ```
>     ENV MY_PATH /usr/mytest
>     这个环境变量可以在后续的任何 RUN 指令中使用，这就如同在命令前面指定了环境变量前缀一样；也可以在其他指令中直接使用这些环境变量
>     比如：WORKDIR $MY_PATH
>     ```
>
> - `ADD`：将宿主机目录下的文件拷贝进镜像且 ADD 命令会自动处理 URL 和解压 tar 压缩包
>
> - `COPY`：类似 ADD ，拷贝文件和目录到镜像中。
>
>   - 将从构建上下文目录中（源路径）的文件或目录复制到新的一层的镜像内（目标路径）位置
>
> - `VOLUME`：容器数据卷，用于数据保存和持久化工作
>
> - `CMD`：指定一个容器启动时要运行的命令
>
>   - ```
>     CMD 指令的格式和 RUN 相似，也是有两种格式：
>     1、shell 格式：CMD<命令>
>     2、exec 格式：CMD ["可执行文件","参数1","参数2"]
>     ```
>
>   - Dockerfile 中可以有多个 CMD 指令，但只有最后一个生效，CMD 会被 docker run 之后的参数替换
>
> - `ENTRYPOINT`：指定一个容器启动时需要运行的命令
>
>   - ENTRYPOINT 的目的和 CMD 一样，都是在指定容器启动程序及参数
>
> - `ONBUILD`：当构建一个被继承的 Dockerfile 时运行命令，父镜像在被子镜像继承后，父镜像的 onbuild 被触发

#### 14、自定义镜像 mycentos

1）、编写 dockerfile

```dockerfile
FROM centos
MAINTAINER hpshen1996@163.com
ENV MYPATH /usr/local/
WORKDIR $MYPATH

RUN yum -y install vim
RUN yum -y install net-tooles

EXPOSE 80

CMD echo $PATH
CMD echo "success......ok"
CMD echo /bin/bash

```

2）、构建镜像

> `docker build -f dockerfile文件位置 -t 新镜像名字:TAG .`

```shell
[root@hepingfly mydocker]# docker build -f Dockerfile2 -t mycentos:1.3 .

# 最后面一个点表示当前目录
```

3）、运行镜像

> `docker run -it 新镜像名字:TAG`

4）、列出镜像的变更历史

> `docker history 镜像名`

#### 15、CMD 和 ENTRYPOINT

1）、**CMD**

Dockfile 中可以有多个 CMD 指令，但只有最后一个生效，CMD 会被 docker run 之后的参数替换

#### 16、自定义一个 tomcat

> 1、在宿主机上创建一个目录
>
> /mydocker/tomcat9
>
> 2、在上述目录下 touch c.txt
>
> 3、将 jdk 和 tomcat 的安装包拷贝进你建的宿主机目录
>
> 4、在 /mydocker/tomcat9 目录下新建 Dockerfile 文件
>
> ````dockerfile
> FROM centos
> MAINTAINER hpshen1996@163.com
> # 把宿主机当前上下文的 c.txt 拷贝到容器 /usr/local/ 路径下
> COPY c.txt /usr/local/cincontainer.txt
> # 把 java 与 tomcat 添加到容器中
> ADD jdk-8u201-linux-x64.tar.gz /usr/local/
> ADD apache-tomcat-8.5.38.tar.gz /usr/local/
> # 安装 vim 编辑器
> RUN yum -y install vim
> # 设置工作访问的时候 WORKDIR 路径，登录落脚点
> ENV MYPATH /usr/local
> WORKDIR MYPATH
> # 配置 java 和 tomcat 环境变量
> ENV JAVA_HOME /usr/local/jdk1.8.0_201
> ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
> ENV CATANALINA_HOME /usr/local/apache-tomcat-8.5.38
> ENV PATH $PATH:$JAVA_HOME/bin:$CATANALINA_HOME/bin
> # 容器运行时监听的端口
> EXPOSE 8080
> # 启动时运行 tomcat
> ENTRYPOINT ["/usr/local/apache-tomcat-8.5.38/bin/startup.sh"]
> ````
>
> 5、构建
>
> docker build -t tomcat9 .
>
> 如果你的 Dockerfile 名字就叫这个，那么可以不用写 -f 去指定 dockerfile 的位置，否则就要写
>
> 6、运行
>
> ```shell
> docker run -d -p 9080:8080 --name mytomcat9
> -v  /mydocker/tomcat9/test:/usr/local/apache-tomcat-8.5.38/webapps/test
> -v /mydocker/tomcat9/tomcat9logs:/usr/local/apache-tomcat-8.5.38/logs
> --privileged=true
> tomcat9
>
> # 上面 -v 是使用容器卷使宿主机和 docker 容器之间进行数据共享
> ```
>
> 

#### 17、docker 安装 mysql

> 1、从 docker hub 上查找 mysql 镜像
>
> docker search mysql
>
> 2、从 docker hub 上（阿里云加速器）拉取 mysql 镜像到本地，标签为 5.6
>
> docker pull mysql:5.6
>
> 3、使用 mysql5.6 镜像创建容器（运行镜像）
>
> ```shell
> docker run -p 3306:3306 --name mysql -v /mydocker/mysql/conf:/etc/mysql/conf.d -v /mydocker/mysql/logs:/logs -v /mydocker/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.6
> ```
>
> 4、登录 mysql 数据库
>
> ```shell
> docker exec -it 87ba6b7c3c1a /bin/bash
> mysql -uroot -p 3306
> ```
>
> 

#### 18、docker 安装 redis

> 1、从 docker hub 上（阿里云加速器）拉取 redis 镜像到本地，标签为 3.2
>
> docker pull redis:3.2
>
> 2、使用 redis3.2 镜像创建容器（运行镜像）
>
> ```shell
> docker run -p 6379:6379 -v /mydocker/myredis/data:/data -v /mydocker/myredis/conf/redis.conf:/usr/local/etc/redis/redis.conf -d redis:3.2 redis-server /usr/local/etc/redis/redis.conf --appendonly yes
> ```
>
> 3、在宿主机 /mydocker/myredis/conf/redis.conf  目录下，新建 redis.conf 文件
>
> vim /mydocker/myredis/conf/redis.conf/redis.conf     关于这个配置文件作用就是你在宿主机改了，会同步到容器中，里面的内容你可以找一个 redis 的配置文件复制过来
>
> 4、测试 redis-cli 是否可以链接
>
> docker exec -it 842772d81fd0 redis-cli
>
> 5、测试持久化文件生成
>
> 你连上客户端后往 redis 里面写几条数据，然后去宿主机目录下 /mydocker/myredis/data  看有没有appendonly.aof 文件生成，有证明成功



























































