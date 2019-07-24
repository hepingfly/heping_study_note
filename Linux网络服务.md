# Linux 网络服务

## 一、网络服务基础

### 1、什么是网络服务

网络服务：是指一些在网络上运行的、面向服务的软件模块

网络服务的标准：可以使用各种协议通过网络进行远程访问的软件模块（常见协议：http/ftp/telnet/smtp 等）

### 2、CentOS6 和 CentOS7 对比

|            | CentOS6.x                | CentOS7.x                                                    |
| ---------- | ------------------------ | ------------------------------------------------------------ |
| 文件系统   | Ext4                     | XFS                                                          |
| 防火墙     | iptables                 | firewalld                                                    |
| 默认数据库 | MySQL                    | MariaDB                                                      |
| 时间同步   | `ntpq -p`                | `chronyc sources`                                            |
| 修改时区   | `/etc/sysconfig/clock`   | `timedatectl set-timezone Asia/Shanghai`                     |
| 修改语言   | `/etc/sysconfig/i18n`    | `localectl set-locale LANG=zh_CN.UTF-8`                      |
| 主机名     | `/etc/sysconfig/network` | `/etc/hostname` <br />可以使用 `hostnamectl set-hostname heping` 来设置主机名 |

### 3、CentOS6 和 CentOS7 对比2

| 操作行为               | CentOS6.X                | CentOS7.X                   |
| ---------------------- | ------------------------ | --------------------------- |
| 启动指定服务           | `service 服务名 start`   | `systemctl start 服务名`    |
| 关闭指定服务           | `service 服务名 stop`    | `systemctl stop 服务名`     |
| 重启指定服务           | `service 服务名 restart` | `systemctl restart 服务名`  |
| 查看指定服务状态       | `service 服务名 status`  | `systemctl status 服务名`   |
| 查看所有服务状态       | `service --status-all`   | `systemctl list-units`      |
| 设置服务自启动         | `chkconfig 服务名 on`    | `systemctl enable 服务名`   |
| 设置服务不自启动       | `chkconfig 服务名 off`   | `systemctl disable 服务名`  |
| 查看所有服务自启动状态 | `chkconfig --list`       | `systemctl list-unit-files` |

### 4、CentOS6 和 CentOS7 对比3

|              | CentOS6.X                   | CentOS7.X                    |
| ------------ | --------------------------- | ---------------------------- |
| 网卡名       | CentOS6.X 网卡名是： `eth0` | CentOS7.X 网卡名：`ens33`    |
| 网络配置命令 | `ifconfig/setup`            | `ip/nmtui`                   |
| 网络服务     | 默认使用 network 服务       | 默认使用 NetworkManager 服务 |

### 5、CentOS7 网卡配置

① 配置文件目录

`/etc/sysconfig/network-scripts/ifcfg-ens33`

② 配置管理命令

`ifconfig/ip address show`

③ 配置文件内容

```
DEVICE=ens33        # 设备名称
NAME=ens33          # 网卡名称
BOOTPROTO=static    # 连接方式(dhcp/static)
ONBOOT=yes			# 是否开机加载
IPADDR=192.168.141.11    # ip 地址
NETMAST=255.255.255.0    # 子网掩码(PREFIX=24)
GATEWAY=192.168.12.1     # 网关
DNS1=8.8.8.8            # DNS
```

注：

网卡的配置文件中选项要大写，小写不报错，但不生效，参数可以小写

### 6、常见协议和端口

1）、网络地址和物理地址

> - 网络地址：互联网协议地址（IP 地址）为互联网上每一个网络或主机分配一个逻辑地址，IP 地址工作在网络层
> - 物理地址：物理地址（MAC 地址）为每一个设备设置一个固定的硬件地址，MAC 地址工作在链路层。

2）、TCP/IP 五层常见协议

> - 应用层协议：FTP、HTTP、SMTP、Telnet、DNS
> - 传输层协议：TCP、UDP
> - 网络层协议：IP、ICMP、ARP
> - 数据链路层协议：PPP 协议等
> - 物理层：不常用

3）、端口配置文件

`/etc/services`

### 7、网关和路由

> `route -n` 查看系统中路由表信息

添加和删除网关：

1）、临时生效

> 网关
>
> 添加： `route add default gw ip地址`
>
> 删除：`route del default gw ip地址`

2）、永久生效

> 网关
>
> `/etc/sysconfig/network-scripts/ifcfg-eth0`

### **8、网络常见命令**



> `nslookup`
>
> 域名解析测试命令

配置 DNS

> 配置文件：
>
> 局部（如果有多块网卡这种局部配置就有问题了）：
>
> `/etc/sysconfig/network-scripts/ifcfg-eth0`
>
> DNS=ip地址
>
> 全局：
>
> `/etc/resolv.conf`
>
> nameserver ip地址

```shell
[root@hadoop1 sbin]# nslookup www.baidu.com
Server:		192.168.148.2
Address:	192.168.148.2#53

Non-authoritative answer:
www.baidu.com	canonical name = www.a.shifen.com.
Name:	www.a.shifen.com
Address: 183.232.231.174
Name:	www.a.shifen.com
Address: 183.232.231.172
```



> arp 地址解析协议，将 ip 地址解析成 MAC 地址
>
> 选项：
>
> ​	`-a` ：查看所有
>
> ​	`-d  ip地址`：删除某条 ARP 记录
>
> 使用 arp 命令，需要先 ping 一下对应的 ip 地址，进行通信一下，然后才能查看

### 9、ssh 密钥对验证模式

> 前提：客户端已经把自己的公钥放到了服务器上
>
> 1、客户端拿着自己的公钥，去请求连接服务器，服务器要求客户端接收自己的公钥文件
>
> 2、服务器接收客户端的公钥，验证本地存放的和接收到的是否一致
>
> 3、若不一致，则直接拒绝登录请求（该公钥未在本机登录注册）。若一致，则使用客户端的公钥加密一串质疑信息，发送到客户端
>
> 4、客户端收到加密内容后，使用客户端私钥进行解密，解密后，再使用服务器公钥将结果加密，然后传给服务器。
>
> 5、服务器接收到客户端发送的数据包，使用服务器私钥进行解密，若结果和当时发给客户端的质疑内容一样，则成功建立起安全远程连接
>
> PS：
>
> 客户端有三个文件：
>
> 客户端公钥、客户端私钥、服务器公钥
>
> 服务器有三个文件：
>
> 服务器公钥、服务器私钥、客户端公钥

### 10、ssh 密钥对登录

1）、用户密码验证

Linux 主机之间我们可以直接使用 ssh 进行远程登录

**格式：**

> `ssh 用户名@ip地址`
>
> **注：**
>
> 可以直接 `ssh ip地址`
>
> 如果不写用户名的话，默认使用 root 用户登录

2）、密钥对验证

Linux 主机之间密钥对登录验证

① 客户端生成密钥对文件

> `ssh-keygen -t rsa -b 2048`
>
> `-t` ：指定加密类型（rsa/dsa等）
>
> `-b` ：指定密钥对加密长度
>
> 执行上述命令过程中会有两个询问：
>
> 询问1：执行过程中会询问保存位置，一般默认保存在当前用户家目录下的 `.ssh`  目录下
>
> 询问2：是否对密钥文件进行加密
>
> 若加密，则在调用密钥文件时需要先验证密钥的密码，密码正确才能使用密钥文件
>
> 若不加密，则密钥文件可以直接被调用，整个登录验证过程无需输入任何密码，即为免密登录

② 将公钥文件上传至服务器端

> `ssh-copy-id 用户名@服务器ip地址`
>
> 这里的用户名要和用来登录服务器的用户名一致

③ 客户端尝试登录服务器

> `ssh 用户名@服务器ip 地址`















































