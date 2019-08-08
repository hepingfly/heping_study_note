# nginx 安装

安装 nginx 需要预先执行如下命令：

```shell
yum install gcc-c++
yum install -y pcre pcre-devel
yum install -y zlib zlib-devel
yum install -y openssl openssl-devel
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





















