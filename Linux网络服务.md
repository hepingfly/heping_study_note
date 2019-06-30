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











