# nginx 笔记

### Nginx 安装

安装 nginx 需要预先执行如下命令：

```shell
yum install gcc-c++
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
yum install -y openssl openssl-devel

-------------------------------------
# 这些依赖使用 tar 包安装也可以
pcre-8.27.tar.gz
zlib-1.2.8.tar.gz
openssl-1.0.1.tar.gz
```

这四个需要安装的原因是：

> - nginx是C语言开发，建议在linux上运行。安装nginx需要先将官网下载的源码进行编译，编译依赖gcc环境，如果没有gcc环境，需要安装gcc。
> - PCRE(Perl Compatible Regular Expressions)是一个Perl库，包括 perl 兼容的正则表达式库。nginx的http模块使用pcre来解析正则表达式，所以需要在linux上安装pcre库。
> - zlib库提供了很多种压缩和解压缩的方式，nginx使用zlib对http包的内容进行gzip，所以需要在linux上安装zlib库。
> - OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及SSL协议，并提供丰富的应用程序供测试或其它目的使用。nginx不仅支持http协议，还支持https（即在ssl协议上传输http），所以需要在linux安装openssl库。

**安装步骤：**

1、准备目录

```shell
mkdir /usr/local/nginx
```

2、下载安装文件

一般是下载源代码文件包，然后自行进行编译和安装

```shell
wget http://nginx.org/download/nginx-1.12.0.tar.gz
```

3、解压缩

```shell
tar -zxvf nginx-1.12.0.tar.gz
```

4、准备安装

1）、进入解压后的目录

```shell
cd nginx-1.12.0
```

2）、执行命令

```shell
# --prefix 指定 nginx 安装位置，如果没有指定默认为 /usr/local/nginx
./configure --prefix=/usr/local/nginx
```

执行这条命令可能会出现的异常情况：

① 如下所示

```shell
[root@instance-3lm099to nginx-1.14.0]# ./configure --prefix=/usr/local/nginx
checking for OS
 + Linux 3.10.0-862.3.2.el7.x86_64 x86_64
checking for C compiler ... not found        # 出现这个证明 gcc 未安装，需要安装 gcc
```

② 如下所示

```shell
./configure: error: the HTTP rewrite module requires the PCRE library.
You can either disable the module by using --without-http_rewrite_module
option, or install the PCRE library into the system, or build the PCRE library
statically from the source with nginx by using --with-pcre=<path> option.
# 出现这个证明 pcre 或者 openssl 库未安装，检查是否安装
# 注意：pcre 和 pcre-devel 这两个都要装，使用 rpm -qa |grep pcre  查下这两个是否都装了，如果只装了一个需要把另一个也装上
```

把未安装的库安装好之后，建议执行 make clean 命令

```shell
make clean     # 如果执行 ./configure --prefix=/usr/local/nginx 出现错误需要执行 make clean 命令
```

然后再次执行

```shell
./configure --prefix=/usr/local/nginx
```

执行完成后，在最后看到如下信息，证明命令执行成功

```
Configuration summary
  + using system PCRE library
  + OpenSSL library is not used
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp"
```

3）、编译，make（make的过程是把各种语言写的源码文件，变成可执行文件和各种库文件）

```shell
[root@linux nginx-1.12.0]# make
```

4）、安装

```shell
[root@linux nginx-1.12.0]# make install
```

5、启动 nginx

```
cd /usr/local/nginx/sbin
./nginx -c /usr/local/nginx/conf/nginx.conf
```

6、查看 nginx 是否启动成功

```shell
ps -ef |grep nginx
```

查到进程号证明启动成功



------

### Nginx 简介

#### 1、什么是 Nginx

> Nginx 是一个高性能的 HTTP 和反向代理服务器，特点是占有内存少，并发能力强。Nginx 专为性能优化而开发，性能是其最重要的考量，能经受高负载的考验，有报告表明能支持高达 50000 个并发连接数

#### 2、反向代理

**正向代理：**

> 在客户端（浏览器）配置代理服务器，通过代理服务器进行互联网访问

**反向代理：**

> 反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后，再返回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器地址，隐藏了真实服务器 IP 地址。

#### 3、负载均衡和动静分离

**负载均衡：**

> 单个服务器解决不了，我们增加服务器数量，然后将请求分发到各个服务器上，将原来请求集中到单个服务器上的情况改为请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡。

**动静分离：**

> 为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速度。降低原来单个服务器的压力。

#### 4、nginx 常用命令

**查看 nginx 版本号**

```shell
 ./nginx -v
```

**启动 nginx**

```shell
./nginx
```

**关闭 nginx**

```
 ./nginx -s stop
```

**重新加载 nginx**

```shell
./nginx -s reload
```

#### 5、nginx 配置文件

nginx 配置文件有三部分组成

第一部分：全局块

> 从配置文件开始到 events 块之间的内容，主要会设置一些影响 nginx 服务器整体运行的配置指令，主要包括配置运行 nginx 服务器的用户（组）、允许生成的 worker process 数、进行 PID 存放路径、日志存放路径和类型以及配置文件的引入

第二部分：events 块

> events 块涉及的指令主要影响 Nginx 服务器与用户的网络连接，常用的设置包括是否开启对多 work process 下的网络连接进行序列化，是否允许同时接收多个网络连接，选取哪种事件驱动模型来处理连接请求，每个 work process 可以同时支持的最大连接数等。

第三部分：http 块

> 这是 nginx 配置中最频繁的部分。**http 块又包括 http 全局块、server 块** 
>
> - http 全局块
>
>   - http 全局块配置的指令包括文件引入、MIME-TYPE 定义、日志自定义、连接超时时间、单链接请求数上限等
>
> - server 块
>
>   - 这块和虚拟主机有密切关系，虚拟主机从用户角度看，和一台独立的硬件主机是一样的，该技术的产生是为了节省互联网服务器硬件成本
>
>   - 每个 http 块可以包括多个 server 块，而每个 server 块就相当于一个虚拟主机
>
>   - 每个 server 块也分为全局 server 块，以及可以同时包含多个 location 块
>
>   - ```shell
>     # location 块
>     一个 server 块可以配置多个 location 块
>     这块的主要作用是基于 Nginx 服务器接收到的请求字符串(例如：server_name/uri-string)，对虚拟主机名称(也可以是 IP 别名) 之外的字符串(例如 前面的 /uri-string) 进行匹配，对特定的请求进行处理。地址重定向、数据缓存和应答控制等功能，还有许多第三方模块的配置也在这里进行
>     ```
>     ​
>

### Nginx 配置实例

#### 1、反向代理配置

**反向代理实例1：**

实现效果：

> 打开浏览器，在浏览器地址栏输入地址 `192.168.148.145`   ,跳转到 linux 系统 tocmat 主页面中

```shell
server {
        listen       80;
        server_name  192.168.148.145;
        
        location / {
            root   html;
            # 主要是在 nginx 配置文件中加上这个   proxy_pass
            proxy_pass http://127.0.0.1:8080;
            index  index.html index.htm;
        }
}
```

**反向代理实例2：**

实现效果：

> 使用 nginx 反向代理，根据访问的路径跳转到不同端口的服务中
>
> nginx 监听端口为 9001
>
> 访问 `192.168.148.145:9001/edu/`   直接跳转到 `192.168.148.145:8080`
>
> 访问 `192.168.148.145:9001/vod/`   直接跳转到 `192.168.148.145:8081`

准备工作：

> 1、准备两个 tomcat  一个 8080 端口，一个 8081 端口
>
> 2、在 8080 tomcat 的 webapp 目录下创建 edu 目录，然后在这个目录下创建一个 a.html。在 8081 tomcat 的 webapp 目录下创建 vod 目录，然后在这个目录下也创建一个 a.html

```shell
server {
                listen  9001;
                server_name 192.168.148.145;
        location ~ /edu/ {
               proxy_pass http://127.0.0.1:8080;
         }
        location ~ /vod/ {
               proxy_pass http://127.0.0.1:8081;
         }
}

# 假如你访问 http://192.168.148.145:9001/edu/a.html   那么就会把请求转发到  http://127.0.0.1:8080/edu/a.html

# 假如你访问 http://192.168.148.145:9001/edu/c/a.html   那么就会把请求转发到  http://127.0.0.1:8080/edu/c/a.html

```

**location 指令说明：**

该指令用于匹配 URL

语法如下：

```
location [= | ~ | ~* | ^~] uri {
    
}
```

> -  `=`  ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求
> - `~`  ： 用于表示 uri 包含正则表达式，并且区分大小写
> - `~*` ：用于表示 uri 包含正则表达式，并且不区分大小写
> - `^~` ：用于不含正则表达式的 uri 前，要求 nginx 服务器找到标识 uri 和请求字符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location 块中的正则 uri 和请求字符串做匹配

**注意：**

如果 uri 包含正则表达式，则必须要有  `~` 或者 `~*` 标识

#### 2、负载均衡配置

实现效果：

> 浏览器地址栏输入地址 `192.168.148.145:9001/edu/a.html`  实现负载均衡效果，平均分配到 8080 端口和 8081 端口中

准备工作：

> 1、准备两个 tomcat  一个 8080 一个 8081 
>
> 2、在两个 tomcat  的 webapp 目录下都创建 edu 文件夹，在 edu 文件夹下创建测试页面 a.html

```shell
http {
	upstream myserver {                    #   ① 负载均衡注意配置这个地方
         server 192.168.148.146:8080;
         server 192.168.148.146:8081;
    }
    server {
    	listen       80;
        server_name  192.168.148.146;
        location / {
            root   html;
            proxy_pass http://myserver;    #  ②  负载均衡主要配置这个地方
            index  index.html index.htm;
        }
	}
}

# 当访问 http://192.168.148.146/edu/a.html     就会把请求按照负载均衡分配到 8080 端口和 8081 端口
```

**nginx 负载均衡策略：**

> - 轮询（默认）
>
>   - 每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器 down 掉，能自动剔除
>
> - weight
>
>   - weight 代表权重，默认为 1，权重越高被分配的客户端越多
>
>   - ```shell
>     upstream myserver {                  
>              server 192.168.148.146:8080 weight=10;
>              server 192.168.148.146:8081 weight=20;
>         }
>     ```
>
> - ip_hash
>
>   - 每个请求按访问 ip 的 hash 结果分配，这样每个访客固定访问一个后端服务器，可以解决 session 问题 
>
>   - ```shell
>     upstream myserver {
>     		 ip_hash;
>              server 192.168.148.146:8080;
>              server 192.168.148.146:8081;
>         }
>      # 通俗一点解释就是，比如说你第一使用 192.168.148.146 这个 ip 去访问，访问到了 8080 服务，那么以后你访问的都是 8080 服务。假如你换了一个 ip 地址，如果第一次访问到了假设192.168.148.146:8081 这个服务，那么这个 ip 之后访问的都是这个服务
>      
>      # 因为 ip 的 hash 值是唯一的，靠 ip 的 hash 值区分
>     ```
>
> - fair
>
>   - 按后端服务器的响应时间来分配请求，响应时间短的优先分配
>
>   - ```shell
>     upstream myserver {
>              server 192.168.148.146:8080;
>              server 192.168.148.146:8081;
>              fair;
>         }
>     ```
>     ​
>

#### 3、动静分离配置

什么是动静分离？

> Nginx 动静分离简单来说就是动态请求和静态请求分开，不能理解成只是单纯的把动态页面和静态页面物理分离。严格意义上说应该是动态请求和静态请求分开，可以理解成使用 Nginx 处理静态页面，tomcat 处理动态页面。动静分离从目前实现角度来讲大致分为两种：
>
> 一种是纯粹把静态文件独立成单独的域名，放在独立的服务器上，也是目前主流推崇的方案
>
> 另外一种方法就是动态和静态文件混合在一起发布，通过 nginx 分开
>
> 通过 location 指定不同的后缀名实现不同的请求转发。通过 expires 参数设置，可以使浏览器缓存过期时间，减少与服务器之间的请求和流量。具体 expires 定义：是给一个资源设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，所以不会产生额外的流量。此种方法非常适合不经常变动的资源。（如果经常更新的文件，不建议使用 expires 来缓存）。例如设置成 3d ，表示在这 3 天内，访问这个 URL ，发送一个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码 304，如果有修改，则直接从服务器重新下载，返回状态码 200。

什么是动态请求和静态请求？

> 什么是动态请求？比如说我们需要去查询数据库，从数据库中返回需要的信息，那么这种请求我们就可以使用 nginx 直接给转发到 tomcat 去处理，这种请求也叫动态请求 
>
> 什么是静态请求？比如说我们现在需要去请求得到一个图片的地址，或者得到一个 html 文件，这种请求就叫静态请求，这时候就可以使用 nginx ，把请求去指向一个静态资源服务器。 

准备工作：

> 在 linux 系统中准备静态资源，用于进行访问
>
> 创建两个文件夹 www   和 image 文件夹，一个放 a.html ，一个放 a.png

```shell
server {
		listen       80;
        server_name  192.168.148.147;
		
		# 如果你请求是 http://192.168.148.147/a/www  这里就拦截不到了
        location /www {  # 这行的意思是你访问的请求是 http://192.168.148.147/www (就会被拦截)
            root /usr/local/data;  # 就把请求转发到 192.168.148.147/usr/local/data/www/a.html
        }
        location /image {
            root /usr/local/data;
            autoindex on;     # 这个的作用就是列出访问的文件列表
        }
        
        location /a/www {   # 请求 http://192.168.148.147/a/www/a.html
            root /usr/local/data;  # 把请求转发到 192.168.148.147/usr/local/data/a/www/a.html
        }
}

# 现在假如说你去访问 http://192.168.148.147/image/     因为你直接访问的是这个文件夹，而这个文件夹下面有一个 a.png 文件，而你 nginx 里面又配置了 「autoindex on」 所以会把 a.png 以列表的方式列出来，你一点击就会把图片显示出来

# 现在说下如果你访问 http://192.168.148.147/www/a.html 发生什么事？
# 它会去 192.168.148.147/usr/local/data/www/a.html  去找这个文件加载，找不到就 404
```

#### 4、nginx 高可用配置

![nginx高可用](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-nginx/nginx%E9%AB%98%E5%8F%AF%E7%94%A8.png)

配置高可用准备工作：

> - 需要两台服务器
> - 在两台服务器上安装 nginx
> - 在两台服务器上安装 keepalived

**安装 keepalived**

> 安装方式有很多中，可以通过下载 tar 包进行安装，最常见是通过 yum 命令进行安装
>
> `yum install keepalived -y`
>
> 安装完成后可以使用 `rpm -qa | grep keepalived` 检查一下。并且在 `/etc/keepalived/keepalived.conf`  会有配置文件，我们通过修改配置文件完成 keepalived 的配置。

**主 nginx 所在服务器 keepalived 配置：**

keepalived.conf：

```sh
! Configuration File for keepalived

global_defs {
   notification_email {
     acassen@firewall.loc
     failover@firewall.loc
     sysadmin@firewall.loc
   }
   notification_email_from Alexandre.Cassen@firewall.loc
   smtp_server 192.168.200.1
   smtp_connect_timeout 30
   router_id 192.168.148.145                         # 主机 ip ，通过这个 ip 去访问你的主机
   vrrp_skip_check_adv_addr
   vrrp_strict
   vrrp_garp_interval 0
   vrrp_gna_interval 0
}

# 检测脚本的配置
vrrp_script chk_http_port {
    script "/usr/local/src/nginx_check.sh"    # 检测脚本的路径
    interval 2    # 检测脚本执行的间隔,这里每隔 2 秒执行一次 
    weight 2      # 设置当前服务器的权重，比如说这里你设置成 -20 ，当你主服务器挂了后，从服务器就变成主服务器
}

# 虚拟 ip 配置
vrrp_instance VI_1 {
    state MASTER       # 备份服务器上将 MASTER 改为 BACKUP，设置是主服务器还是备份服务器
    interface eth0     # 网卡，你在哪个网卡上绑定虚拟主机的虚拟 ip
    virtual_router_id 51   # 主、备机的 virtual_router_id 必须相同
    priority 100          # 主、备机取不同的优先级，主机值较大，备份机值较小
    advert_int 1        # 每隔 1 秒钟发送一个心跳
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {     # VRRP 虚拟 ip 地址
        192.168.200.16
    }
}
```

**nginx 检测脚本：**

```sh
#!/bin/bash
A=`ps -C nginx ¨Cno-header |wc -l`
if [ $A -eq 0 ];then
    /usr/local/nginx/sbin/nginx
    sleep 2
    if [ `ps -C nginx --no-header |wc -l` -eq 0 ];then
        killall keepalived
    fi
fi
```

**总结一下 nginx 配置高可用的步骤：**

> 1、修改  `/etc/keepalived/keepalived.conf`  配置文件
>
> 2、在 `/usr/local/src/` 下添加 nginx 检测脚本（检测 nginx 是否还活着）
>
> 3、把两台服务器上 nginx 和 keepalived 都启动起来  
>
> keepalived 启动：`service keepalived start`
>
> 4、最终测试：通过虚拟 ip 去访问 nginx，如果一切正常的话，应该访问到的是主 nginx，然后在把主 nginx 停掉及和主 nginx 在一台服务器的 keepalived 停掉，正常情况下是可以访问到备份 nginx

### Nginx 原理

#### 1、原理解释

你查 nginx 进程会发现有一个 master 进程和一个 worker 进程。当客户端发送一个请求到 nginx 中，请求会先到 master，然后 master 把请求分担给 worker，让 worker 去执行具体的任务。但是一个 master 下面可能会有很多的 worker ，这个 worker 是怎么获得任务的呢？ 这里面用到一种机制 ，就是争抢机制。

**一个 master 和多个 worker 的好处：**

> - 可以使用 `nginx -s reload` 进行热部署，利用 nginx 进行热部署操作（比如说现在 1 个 master 和 4 个 worker，有 1 个 worker 有任务正在执行，这里你进行 reload ，这个执行任务的 worker 照常执行，其他 3 个 worker 重新加载，有请求过来其他 3 个 worker 负责争抢执行，等第一个 worker 执行完之后，再重新加载新的配置，这样几个 worker 都是重新加载过的）
> - 每个 worker 是独立的进程，如果有其中的一个 worker 出现问题，其他 worker 是独立的，继续进行争抢，实现请求过程，不会造成服务中断

**一般我们需要设置多少个 worker**

> Nginx 同 redis 类似都采用了 io 多路复用机制，每个 worker 都是一个独立的进程，但每个进程里只有一个主线程，通过异步非阻塞的方式来处理请求，即使是千万个请求也不在话下。每个 worker 的线程可以把一个 cpu 的性能发挥到极致。所以 worker 数和服务器的 cpu 数相等是最为适宜的。设少了会浪费 cpu ，设多了会造成 cpu 频繁切换上下文带来的损耗。















