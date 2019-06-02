# Linux 笔记

### 一、Linux 简介

#### 1、linux 介绍

Linux 系统诞生于 1991 年，由芬兰的大学生 Lunus 和后来陆续加入的众多爱好者共同开发完成。

**Linux 版本分类：**

> - 内核版
>   - RHEL6.X
>   - RHEL7.X
> - 发行版
>   - RedHat 系列
>     - 个人版：Fedora
>     - 服务器版：RHEL（RedHat Enterprise Linux）
>   - Debian 系列

### 二、系统分区

**两种分区表形式**

> - MBR 分区表（主引导记录分区表）：最大支持 2.1TB硬盘，==最多支持 4 个分区==
> - GPT 分区表（全局唯一标识分区表）：GPT 支持 9.4ZB 硬盘（1ZB=1024PB，1PB=1024EB，1EB=1024TB）理论上支持的分区数没有限制，但 windows 限制 128 个主分区

**分区类型**

> - 主分区：最多只能有 4 个（分区表决定的）
> - 扩展分区
>   - 最多只能有 1 个
>   - 主分区加扩展分区最多只能有 4 个（意思是如果你有 1 个扩展分区，那么主分区最多只能有 3 个）
>   - 不能写入数据，只能包含逻辑分区
> - 逻辑分区

**简单说下为什么要分区：**

首先对于硬盘来说，你不分区其实也可以，你就把所有的数据都写在一个地方，这样就带来一个问题，就是你查找的时候回非常麻烦，所以我们会对硬盘进行分区，但是受 MBR分区表的限制，最多只支持 4 个分区，但是我们觉得 4 个分区太少了，想要突破 4 个分区的限制，所以我们会把主分区中的一个拿来作为扩展分区，这个扩展分区不能写数据，只能包含逻辑分区，在扩展分区里面我可以有很多的逻辑分区，这样就突破了 4 个分区的限制。

**关于分区在介绍一下：**

> - 必须分区
>   - /（根分区）
>   - swap 分区（交换分区）
>     - 如果真实内存小于 4GB，swap 为内存的两倍
>     - 如果真是内存大于 4GB，swap 和内存保持一致
> - 推荐分区
>   - /boot（启动分区，1GB）
> - 常用分区
>   - /home（用于文件服务器）
>   - /www（用于 web 服务器）

**格式化：**

> - 格式化又称逻辑格式化，它是指根据用户选定的文件系统（如，FAT16、FAT32、NTFS、EXT2、EXT3、EXT4等），在磁盘的特定区域写入特定的数据，在分区中划出一片用于存放文件分配表、目录表等用于文件管理的磁盘空间。
> - 格式化会把你这个分区格式化成一个一个小的 Block ，这个 Block 是存储数据的最小单元，数据都是存储在 Block 里面的，每个 Block 存储数据的大小是 4kb。假如你这个 Block 存储了 2kb 的数据，那剩下的 2kb 也就不能使用了，所以常常我们硬盘的实际大小和硬盘存储数据的大小有些许差别。

**注意**

==格式化的根本目的是写入文件系统，而不是将数据清除掉。==

**挂载点：**

==Linux 中使用已经存在的空目录作为挂载点==

**文件系统结构：**

在 Linux 中我们知道根目录是最顶层的目录，在根目录下面可能会有 /boot、/etc、/home等子目录。那么从硬盘上来看，如果我对 home 目录进行分区，那么它就有自己的存储空间。也就是说如果我要是给 home 单独分了区，我往 home 目录里写数据，会写在 home 自己的硬盘空间里面，而剩下的其他目录，如果没有单独分区，我往里面写数据，它就会写在 根目录的存储空间里面

**设备文件名：**

> - /dev/hda1  （IDE 硬盘接口）
> - /dev/sda1  （SCSI 硬盘接口、SATA 硬盘接口）
>
> 这里的 `dev` 是 `device` 的缩写，在这个目录下放的都是所有设备的设备文件名，「hd」 和 「sd」表示接口类型，「hd 」是比较老的接口了，已经接近淘汰。usb 在 linux 下会被识别成 「sd 」接口。「a 」表示第一个硬盘，要是第二个硬盘就是 「b」。1 表示第一个分区。

**挂载：**

我们把从设备文件名和挂载点连起来的过程叫做挂载（其实就是把设备挂载到这个目录上），挂载是动作。

### 三、Linux 安装

#### 1、配置 Ip 地址

> - `setup` 
>
> 通过 setup 工具设置 Ip 地址，注意激活 `onboot=yes`
>
> - `service network restart`
>
> 重启网络服务

#### 2、虚拟机网络配置

| 连接方式 | 连接网卡     | 是否能连接本机 | 是否能连接局域网 | 是否能连接公网 |
| -------- | ------------ | -------------- | ---------------- | -------------- |
| 桥接     | 本地真实网卡 | 可以           | 可以             | 可以           |
| NAT      | VMnet8       | 可以           | 不能             | 可以           |
| 仅主机   | VMnet1       | 可以           | 不能             | 不能           |

#### 3、安装日志

> - 使用名称 cd ~ 进入到这个目录下，然后 ls 一下，你会发现会有三个日志文件 `install.log`  `install.log.syslog`   `anaconda-ks.cfg`
> - `install.log`  
>
> 这个日志文件存储了安装在系统中的软件包及其版本信息
>
> - `install.log.syslog`  
>
> 存储安装过程中留下的事件记录（比如说你在安装过程中创建了用户，或者设置了时间都会记录在这个日志文件中）
>
> - anaconda-ks.cfg
>
> 以 kickstart 配置文件的格式记录安装过程中设置的选项信息（通常用来做服务器大批量安装的模板，如果你不用大批量安装，那就可以把它当成一个日志文件）

### 四、Linux 注意事项

#### 1、注意事项

> - Linux 严格区分大小写
> - Linux 一切皆文件
>
> linux 中所有内容都是以文件的形式保存和管理的，硬件设备也是文件。windows 是通过设备管理器来管理硬件的。linux 的设备文件保存在 /dev 目录下。
>
> - Linux 不靠扩展名区分文件类型
>
> windows 是靠扩展名区分文件类型的，但是 Linux 不是靠扩展名区分文件类型的，而是靠*权限位标识*来确定文件类型的，常见的文件类型有普通文件、目录、链接文件、块设备文件、字符设备文件等几种。但是 linux 中一些特殊文件还是要求写扩展名的，之所以要求写扩展名，是为了帮助管理员来区分不同的文件类型
>
> ```
> 1、压缩包：Linux 下常见的压缩文件名有 *.gz、*.tar.gz、*.zip、*.tgz 你写清楚扩展名可以帮助管理员更快速的判断压缩包的格式，因为不同格式的压缩包解压的命令是不一样的。另：如果你不写扩展名在 linux 下也是可以解压使用的(写扩展名只是方便判断)
> 2、二进制软件包：CentOS 中所使用的二进制安装包是 RPM 包，所有 RPM 包都用 “.rpm” 结尾，目的同样是让管理员一目了然
> 3、程序文件：Shell 脚本一般用 “.sh” 扩展名结尾
> ```
>
> - Linux 中所有存储设备都必须在挂载之后才能使用
>
> Linux 中所有的存储设备都有自己的设备文件名，这些设备文件必须在挂载之后才能使用，包括硬盘、U盘、光盘。挂载其实就是给这些存储设备分配盘符，只不过 windows 中的盘符用英文字母表示，==Linux 中的盘符是一个已经建立的空目录==。我们把这些空目录叫做挂载点。把设备文件和挂载点连接的过程叫做挂载

#### 2、Linux 目录结构

| 目录名            | 目录的作用                                                   |
| ----------------- | ------------------------------------------------------------ |
| /bin/             | 存放系统命令的目录，普通用户和超级用户都可以执行。是 /usr/bin/ 目录的软连接 |
| /sbin/            | 存放系统命令的目录，只有超级用户才可以执行。是 /usr/sbin/ 目录的软连接 |
| /usr/bin/         | 存放系统命令的目录，普通用户和超级用户都可以执行。           |
| /usr/sbin/        | 存放系统命令的目录，只有超级用户才可以执行                   |
| /boot/            | 系统启动目录，保存与系统启动相关的文件，如内核文件和启动引导程序（grub）文件等 |
| /dev/             | 设备文件保存位置                                             |
| /etc/             | 配置文件保存位置。系统内所有采用默认安装方式（rpm 安装）的服务配置文件全部保存在此目录中，如用户信息、服务的启动脚本、常用服务的配置文件等。 |
| /home/            | 普通用户的家目录。在创建用户时，每个用户要有一个默认登录和保存自己数据的位置，就是用户的家目录，所有用户的宿主目录是在 /home/ 下建立一个和用户名相同的目录。如：用户 /user1/的家目录就是 /home/user1 |
| /lib/             | 系统调用的函数库保存位置。是 /usr/lib/ 的软                  |
| /lib64/           | 64 位函数库保存位置。是 /usr/lib64/ 的软链接                 |
| /lost+found/      | 当系统意外崩溃或机器意外关机，而产生一些文件碎片放在这里。当系统启动的过程中 fsck 工具会检查这里，并修复已经损坏的文件系统。这个目录只在每个分区中出现，例如： /lost+found/ 就是根分区的备份恢复目录，/boot/lost+found/ 就是 /boot 分区的备份恢复目录 |
| /media/           | 挂载目录。系统建议用来挂载媒体设备。如软盘和光盘。           |
| /misc/            | 挂载目录。我们知道只要是一个已经建立的空目录就可以作为挂载点。系统虽然准备了三个默认的挂载目录 /media/、/misc/、/mnt/ 但是到底哪个目录中挂载什么由管理员自己决定。==我们一般是在 /mnt/ 目录下建立不同的目录挂载不同的设备==。如 /mnt/cdrom/ 挂载光盘，/mnt/usb/ 挂载 U 盘。 |
| /mnt/             | 挂载目录。早起 linux 只有这一个挂载目录。                    |
| /opt/             | 第三方安装的软件保存的位置。同样 /usr/local/ 目录也可以用来安装软件 |
| /proc/            | 虚拟文件系统。该目录中的数据并不保存在硬盘上，而是保存在内存中。主要保存系统的内核、进程、外部设备状态和网络状态等。如 /proc/cpuinfo 是保存 cpu 信息的，/proc/devices 是保存设备驱动的列表的，/proc/filesystems 是保存文件系统列表的，/proc/net 是保存网络协议信息的等等。。 |
| /sys/             | 虚拟文件系统。和 /proc/ 目录相似，该目录中的数据都保存在内存中。主要保存与内核相关的信息 |
| /root/            | root 用户的宿主目录。普通用户的宿主目录在 /home 下，root 用户的宿主目录直接在根目录下 |
| /selinux/         | 增加安全组件。是为了提升 linux 的安全性能的。这个东西是由美国国家安全局牵头开发，是为了限制 root 权限 |
| /run/             | 系统运行时产生的数据，如：ssid，pid 等相关数据。 /var/run/ 是此目录的软链接 |
| /srv/             | 服务数据目录。一些系统服务启动之后，可以在这个目录中保存所需要的数据。 |
| /tmp/             | 临时目录。系统存放临时文件的目录，在该目录下，所有用户都可以访问和写入。建议此目录中不能保存重要数据。你自己建的数据可以删，但是系统带的临时文件建议不要删。 |
| /usr/             | 系统软件资源目录。注意 usr 不是 user 的缩写，而是 「Unix Software Resource」的缩写，所以不是存储用户数据的目录，而是存放系统软件资源的目录。系统中安装的软件大多数保存在这里。 |
| /usr/lib/         | 应用程序调用的函数库保存在该位置                             |
| /usr/local/       | 手动安装的软件保存位置。建议源码包软件安装在这个位置         |
| /usr/share/       | 应用程序的资源文件保存位置，如帮助文档，说明文档和字体目录   |
| /usr/src/         | 源码包保存位置。我们手工下载的源码包和内核源码包都可以保存到这里。不过我们习惯把手工下载的源码包保存到 /usr/local/src/ 目录下，把内核源码保存到 /usr/src/kernels/ 目录中 |
| /usr/src/kernels/ | 内核源码保存位置                                             |
| /var/             | 动态数据保存位置。主要保存缓存、日志以及软件运行所产生的文件。 |
| /var/www/html/    | RMP 包安装的 Apache 的网页主目录                             |
| /var/lib/         | 程序运行中需要调用或改变的数据保存的位置。如，==MySQL 的数据库保存在 /var/lib/mysql/ 目录中== |
| /var/log/         | 系统日志保存的位置                                           |
| /var/run/         | 一些服务和程序运行后，他们的 PID（进程ID）保存位置。是 /run/ 目录的软链接 |
| /var/spool/       | 放置队列数据的目录。就是排队等待其他程序使用的数据。         |
| /var/spool/mail/  | 新收到的邮件队列保存位置。系统新收到的邮件会保存在此目录中   |
| /var/spool/cron/  | 系统的定时任务队列保存位置。系统的计划任务会保存在这里       |

**注：**

说一下 linux「~」 的作用：

在 linux 中 「~」表示当前用户的家目录。比如说你当前用户时 heping ，那么你 「cd ~」就相当于 「cd /home/heping」

比如说你当前用户是 root 用户，那么你 「cd ~」就相当于 「cd /root」因为你根目录下的 root 文件夹就是 root 用户的家目录。

#### 3、小建议

**1、服务器在重启之前，最好终止正在执行的服务**

因为计算机硬盘最怕在高速存储时断电或重启，非常容易造成硬盘损坏。但平时我们可能会发现强制关我们的个人电脑，并没有发现硬盘损坏，这是因为你的个人电脑只有你一个人在用，并没有很多人访问，强制断电时，硬盘并没有数据交换，但是服务器就不一样了，一直都会有人访问，硬盘一致在高速运转。

**2、重启命名的选用**

Linux 可以识别的重启命令有很多条，但是建议使用 `shutdown -r now` 命令重启。这条命令在重启时会正常保存和终止服务器中正在运行的程序，是安全重启命令。而且最好在重启之前执行几次 `sync` 命令，这条命令是数据同步命令，可以让暂时保存在内存中的数据同步到硬盘上。 

**3、远程配置防火墙时不要把自己踢出服务器**

首先最好的方法肯定是在本地把防火墙规则验证好了，再上传服务器，这样可以把故障降到最低。还有一个笨方法：就是写一个系统定时任务，让它每 5 分钟清空一下防火墙规则，这样就算防火墙规则写错了也有反悔的机会，等测试没有问题了再删除这个定时任务。

**4、合理分配权限**

服务器管理有个最简单的原则：给予用户最小的权限

### 五、常用命令

#### 1、命令的基本格式

1）、命令提示符

> `[root@hepingfly ~]`
>
> - `[]`：这里是提示符的分隔符号，没有特殊含义
> - `root`：显示的是当前的登录用户
> - `“@”`：分隔符号，没有特殊含义
> - `hepingfly`：当前系统的简写主机名
> - `~` ：代码用户当前所在的目录
> - `“#”` ：命令提示符。超级用户是 “#”，普通用户是 “$”



```
dr-xr-xr-x.   2 root root  4096 2月  11 14:14 bin
dr-xr-xr-x.   5 root root  4096 2月  11 07:13 boot
```

> - 第一列：权限
> - 第二列：引用计数。文件的引用计数代表该文件的硬连接个数，而目录的引用计数代表该目录有多少个一级子目录
> - 第三列：所有者。也就是这个文件属于哪个用户。默认所有者是文件的建立用户
> - 第四列：所属组。默认所属组是文件建立用户的有效组，一般情况下就是建立用户的所在组。
> - 第五列：大小。默认单位是字节
> - 第六列：文件修改时间。文件状态修改时间或文件数据修改时间都会更改这个时间，注意这个时间不是文件的创建时间。
> - 第七列：文件名

#### 2、常用命令

##### `cd`

> 切换目录

| 特殊符号 | 作用             |
| -------- | ---------------- |
| ~        | 代表用户的家目录 |
| -        | 代表上次所在目录 |
| .        | 代表当前目录     |
| ..       | 代表上级目录     |

**绝对路径和相对路径：**

绝对路径：以根目录为参照物，从根目录开始，一级一级进入目录

相对路径：以当前目录为参照物，进行目录查找

##### `mkdir`

> 创建目录
>
> 选项：
>
> ​	-p：递归建立所需目录

**注：**

任何系统中都不会存在同名文件或同名目录

==小贴士：==

Linux 中有一个 ==extundelete== 文件恢复工具，可以预先在系统中安装一个这个工具，可以在误删除操作后，有一定几率对文件进行找回。

##### `touch`

> 创建空文件或修改文件时间
>
> 如果一个已经存在的文件，你在对它使用 touch，会修改它的时间

##### `stat`

> stat 是查看文件详细信息的命令，最主要是可以看到文件的三个时间
>
> ```shell
> [hepingfly@hepingfly ~]$ stat aa
>   File: "aa"
>   Size: 2         	Blocks: 8          IO Block: 4096   普通文件
> Device: 803h/2051d	Inode: 131083      Links: 1
> Access: (0664/-rw-rw-r--)  Uid: (  500/hepingfly)   Gid: (  500/hepingfly)
> Access: 2019-02-13 22:37:25.799007773 +0800    # 最近访问时间
> Modify: 2019-02-13 22:37:22.052010708 +0800    # 最近数据修改时间
> Change: 2019-02-13 22:37:22.052010708 +0800    # 最近状态修改时间
> # 注：
> # linux 是不记录创建时间的
> ```
>
> 

##### `cat`

> cat 命令用来查看文件内容
>
> 选项：
>
> ​	-n：显示行号
>
> ​	-A：相当于 -vET 选项的整合，用于列出所有隐藏符号

```shell
[hepingfly@hepingfly ~]$ cat -A aa
123$
^Iff$
56 d$        # 把 aa 中所有隐藏符号都列出来了
```

##### `more`

> 分屏显示文件内容
>
> more 命令比较简单，一般不用什么选项，命令会打开一个交互界面，可以识别一些交互命令。常用的交互命令如下：
>
> - 空格键：向下翻页
> - b：向上翻页
> - 回车键：向下滚动一行
> - /字符串：搜索指定的字符串

##### `less`

> less 命令和 more 命令类似，只是 more 是分屏显示命令，而 less 是分行显示命令
>
> 主要作用就是分行显示文件内容

##### `tail`

> 显示文件结尾的内容
>
> 选项：
>
> ​	-f：监听文件的新增内容
>
> ​	-n 行数：从文件结尾开始，显示指定行数

##### `ln`

> 在文件之间建立链接
>
> 选项：
>
> ​	-s：建立软链接文件。如果不加 “-s” 选项，则建立硬链接文件
>
> ​	-f：强制。如果目标文件已经存在，则删除目标文件后再建立链接文件

```shell
 # 建立硬链接文件，目标文件没有写文件名，会和原名一致
 ln /home/hepingfly/aa.txt /home/hepingfly/bb
 
 #也就是 /home/hepingfly/aa.txt 和 /home/hepingfly/bb/aa.txt 这两个是硬链接文件
```

**创建软链接：**

```shell
ln -s /home/hepingfly/bb.txt /home/hepingfly/cc
```

**注意：**

==创建软链接一定要写绝对路径==

**硬链接的特征：**

> - 源文件和硬链接文件拥有相同的 Inode 和 Block
> - 修改任意一个文件，另一个都改变
> - 删除任意一个文件，另一个都能使用
> - 硬链接标记不清，很难确定硬链接文件的位置，不建议使用
> - 硬链接不能链接目录
> - 硬链接不能跨分区 

**软链接的特征：**

> - 软链接和源文件拥有不同的 Inode 和 Block
> - 两个文件修改任意一个，另一个都会改变
> - 删除软链接，源文件不受影响。删除源文件，软链接不能使用
> - 软链接没有实际数据，只保存源文件的 Inode，无论源文件有多大，软链接大小不变
> - 软链接的权限是最大权限 `lrwxrwxrwx`，但是由于没有实际数据，最终访问时需要参考源文件权限（软链接里面存的知识源文件的 Inode ，你最终访问的时候是根据这个 Inode 去找源文件，所以具体内容访问权限还要看源文件，只是说软链接里面存的 Inode 内容，所有人都可以访问）
> - 软链接可以链接目录
> - 软链接可以跨分区
> - 软链接特征明显，建议使用软链接

##### `rm`

> 删除文件或目录
>
> 选项：
>
> ​	-f：强制删除（force）
>
> ​	-r：递归删除，可以删除目录（recursive）

##### `cp`

> 复制文件或目录
>
> 选项：
>
> ​	-a：相当于 -dpr 选项的集合
>
> ​	-d：如果源文件为软链接（对硬链接无效），则复制出的目标文件也为软链接
>
> ​	-p：复制后的目标文件保留源文件属性（包括所有者、所属组、权限和时间）
>
> ​	-r：递归复制，用于复制目录

##### `mv`

> 移动文件或改名

##### `alias`

> 命令的别名，主要是用于照顾管理员的使用习惯的
>
> 查询别名：`alias`
>
> 设置别名：`alias 别名='原命令'`

**注意：**

别名的优先级是高于系统命令的，你如果设置的别名和系统命令重复了，那么系统命令就再也敲不了了，所以你设置别名的时候需要防止和系统命令重复。可以先敲一下你想设置的别名看能不能执行，或者用 whereis 命令看下

```shell
# 设置别名，注意，等于号后面的引号是需要的
[root@hepingfly 桌面]# alias grep='grep --color=auto'
[root@hepingfly 桌面]# alias
alias cp='cp -i'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias mv='mv -i'
alias rm='rm -i'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'
```

**注：**

> 用命令定义的别名，是临时生效的，要想永久生效，需要写入环境变量配置文件 `~/.bashrc`
>
> 顺便说下 `~/.bashrc` 环境变量配置文件和 `/et/profile` 环境变量配置文件的区别：
>
> 答：
>
> 在 `/et/profile` 环境变量配置文件中的配置是对所有用户都生效，而 `~/.bashrc` 环境变量配置文件只对当前用户生效，而我们说别名就是为了照顾管理员使用习惯的，所以把它配置在  `~/.bashrc` 环境变量配置文件中正好

##### 常用快捷键

| 快捷键   | 作用                                                         |
| -------- | ------------------------------------------------------------ |
| Tab 键   | 把命令或文件补全                                             |
| ctrl + A | 把光标移动到命令行的开头。如果我们输入的命令过长，想要把光标移动到命令行的开头使用 |
| ctrl + E | 把光标移动到命令行的结尾。                                   |
| ctrl + L | 清屏。相当于 clear 命令                                      |
| ctrl + U | 删除或剪切光标之前的命令。我输入了一行很长的命令，不用使用退格键一个一个字符的删除，使用这个快捷键会更加方便 |
| ctrl + Y | 粘贴 ctrl + U 的内容                                         |
| ctrl + C | 强制终止当前命令                                             |



### 六、Linux 权限管理

##### 1、权限介绍

```shell
[hepingfly@hepingfly ~]$ ll
总用量 40
-rw-rw-r--. 2 hepingfly hepingfly    0 2月  14 00:23 aa.txt
```

> - 第 1 位代表文件类型
>   - “-” 表示文件
>   - “d” 表示目录
>   - “l” 表示软链接
> - 第 2~4 位表示文件所有者的权限
>   - r：代表 read ，是读权限
>   - w：代表 write，是写权限
>   - x：代表 excute，是执行权限
> - 第 5~7 位表示文件所属组的权限
> - 第 8~10位表示其他人的权限

注：

在权限位后面还有一个 “.” 这个点表示该文件受 SELinux 的保护，SELinux 是美国国家安全局开发的一个增强 linux 安全组件

##### 2、权限相关命令

`chmod`

> 修改文件权限模式
>
> 选项：
>
> ​	-R：递归设置权限，也就是给子目录中的所有文件设置权限

chmod 命令的权限模式的格式是 `[ugoa][+-=][perms]`，也就是 `[用户身份][赋予方式][权限]` 的格式

> - 用户身份
>   - u：代表所有者（user）
>   - g：代表所属组（group）
>   - o：代表其他人（other）
>   - a：代表全部身份（all）
> - 赋予方式
>   - +：加入权限
>   - -：减去权限
>   - =：设置权限
> - 权限
>   - r：读取权限（read）
>   - w：写入权限（write）
>   - x：执行权限（execute）

```shell
chmod u+r,u-x,g+w,o+x aa.txt       # 可以使用逗号分隔，把命令连起来写

# 或者你可以使用"=" 来写
chmod u=rw-,g=rwx,o=rw- aa.txt    # 这样你就可以不用管这个文件原来是什么权限了
```

**数字权限：**

> - 4：代表 “r” 权限
> - 2：代表 “w” 权限
> - 1：代表 “x” 权限

**常用权限：**

> - 644：这是文件的基本权限，代表所有者拥有读、写权限，而所属组和其他人拥有只读权限
> - 755：这是文件的执行权限和目录的基本权限，代表所有者拥有读、写和执行权限，而所属组和其他人拥有读和执行的权限
> - 777：这是最大权限。在实际生产服务器中，要尽量避免给文件或目录赋予这样的权限，这会造成一定的安全隐患

##### 3、所有者所属组命令

`chown`

> 修改文件和目录的所有者和所属组
>
> chown [选项] 所有者:所属组 文件或目录
>
> 选项：
> 	-R：递归设置权限，也就是给子目录中的所有文件设置权限

```shell
chown root aa.txt   # 把 aa.txt 这个文件的所有者改成 root

chown root:root aa.txt  # 把 aa.txt 这个文件的所有者改成 root 所属组改成 root
```

**注：**

1、普通用户不能修改文件的所有者，哪怕自己是这个文件的所有者也不行

2、普通用户可以修改所有者是自己的文件的权限

`chgrp`

> 修改文件和目录的所属组

```shell
chgrp hepingfly aa.txt   # 把 aa.txt 这个文件的所属组改为 hepingfly
```

##### 4、基本权限的作用

首先 读、写、执行 权限对文件和目录的作用是不同的

> - 权限对文件的作用
>   - 读（r）：对文件有读（r）权限，代表可以读取文件中的数据。如果把权限对应到命令上，那么一旦对文件有读（r）权限，就可以对文件执行 cat 、more 、less 、head、tail 等文件查看命令
>   - 写（w）：对文件有写（w）权限，代表可以修改文件中的数据。如果把权限对应到命令上，那么一旦对文件有 写（w）权限，就可以**对文件执行 vim 、echo 等修改文件数据的命令**。==注意：对文件有写（w）权限，是不能删除文件本身的，只能修改文件中的数据。如果想要删除文件，则需要对文件的上级目录拥有 写（w）权限==
>   - 执行（x）：对文件有执行（x）权限，代表了文件拥有的执行权限，可以运行。在 Linux 中，只要文件有执行（x）权限，这个文件就是执行文件了。只是这个文件到底能不能正确的执行，不仅需要执行（x）权限，还要看文件中的代码是不是正确。对文件来说，执行（x）权限是最高权限。
> - 权限对目录的作用
>   - 读（r）：对目录有读（r）权限，代表可以查看目录下的内容，也就是可以查看目录下有哪些子文件和子目录。如果把权限对应到命令上，那么一旦对目录拥有了读（r）权限，就可以在目录下执行 ls 命令，查看目录下的内容。
>   - 写（w）：对目录有写（w）权限，代表可以修改目录下的数据，也就是可以在目录中新建、删除、复制、剪切子文件或子目录。如果把权限对应到命令上，那么一旦对目录拥有了写（w）权限，就可以在目录下执行 touch、rm、cp、mv 命令。对目录来说，写（w）权限是最高权限。
>   - 执行（x）：目录是不能运行的，那么对目录拥有执行（x）权限，就可以对目录执行 cd 命令，进入目录

**目录的可用权限：**

> 目录的可用权限其实只有以下几个：
>
> - 0：任何权限都不赋予
> - 5：基本的目录浏览和进入权限
> - 7：完全权限

##### 5、umask 默认权限

**① 查看系统的 umask 权限**

```shell
[hepingfly@hepingfly aa]$ umask
0002       # 这里是用八进制数值显示 umask 权限
[hepingfly@hepingfly aa]$ umask -S
u=rwx,g=rwx,o=rx   # 这里是用字母表示文件和目录的初始权限
```

**② umask 权限的计算方法**

先说下新建文件和目录的默认最大权限

> - 对文件来说，新建文件的默认最大权限是 666，没有执行（x）权限。这是因为执行权限对文件来说比较危险，不能在新建文件的时候默认赋予，而必须通过用户手工赋予。（对文件来说，创建的时候不允许默认有执行权限）
> - 对目录来说，新建目录的默认最大权限是 777。这是因为对目录而言，执行（x）权限仅仅代表进入目录，所以即使建立新文件时直接默认赋予，也没有什么危险

**计算方法：**

我们按照默认 umask 值是 022 来计算下新建文件和目录的默认权限：

> - 文件的默认权限最大只能是 666，而 umask 的值是 022
>
>   - ```
>     "-rw-rw-rw-"  减去 "-----w--w-"  等于 "-rw-r--r--"
>     ```
>
> - 目录的默认权限最大可以是 777，而 umask 的值是 022
>
>   - ```
>     "drwxrwxrwx"  减去  "d----w--w-"  等于 "drwxr-xr-x"
>     ```
>
>   ​

**修改 umask 默认值：**

首先说普通用户的 umask 默认值是 002，root 用户的 umask 默认值是 022（具体怎么来的？系统默认在环境变量中配置好的。）

使用命令 `vim /etc/profile`

```shell
if [ $UID -gt 199 ] && [ "`id -gn`" = "`id -un`" ]; then
    umask 002
else
    umask 022
fi
```

直接改上面的 umask 值即可。

注：

这个 umask 值，系统都默认给我们配置好了，我们其实不需要动，你只需要知道有 umask 这回事就可以了。

### 七、搜索命令

#### 1、`whereis`

> whereis 是搜索系统命令的命令，也就是说 whereis 不能搜索普通文件，只能搜索系统命令
>

#### 2、`which`

> which 也是搜索系统命令的命令，和 whereis 命令区别在于，如果这个命令有别名，则还可以找到别名

#### 3、`locate`

> 可以按照文件名搜索文件
>
> - 优点：按照数据库搜索，搜索速度快，消耗资源小。数据库位置：`/var/lib/mlocate/mlocate.db`
> - 缺点：只能按照文件名来搜索文件，而不能执行更复杂的搜索，比如按照 权限、大小、修改时间等搜索文件

说明1：

````
关于执行locate时报错“locate: can not stat () `/var/lib/mlocate/mlocate.db': No such file or directory”的处理

答：
locate是通过生成一个文件和文件夹的索引数据库，当用户在执行loacte命令查找文件时，它会直接在索引数据库里查找，若该数据库太久没更新或不存在，此时执行 updatedb ，更新下数据库即可
````

说明2：

你在 /tmp 下建一个文件，你用 locate 命令是搜不到的，原因是它在配置文件里面配置了禁止搜索 /tmp 目录下的文件

```shell
vim /etc/updatedb.conf
PRUNE_BIND_MOUNTS="yes"    #开启搜索限制，也就是让这个配置文件生效
PRUNEFS="......"  # 在 locate 执行搜索时，禁止搜索这些文件系统类型
PRUNENAMES="......"  # 在 locate 执行搜索时，禁止搜索带这些扩展名的文件
PRUNEPATHES="......"  # 在 locate 执行搜索时，禁止搜索这些系统目录
```

#### 4、`find`

**① 按照文件名搜索**

> `find 搜索路径 [选项] 搜索内容`
>
> 选项：
>
> ​	`-name`：按照文件名搜索
>
> ​	`-iname`：按照文件名搜索，不区分文件名大小写
>
> ​	`-inum`：按照 inode 号搜索

对于使用 inode 号搜索，这里说一下：

```shell
[hepingfly@hepingfly ~]$ touch abc.txt
[hepingfly@hepingfly ~]$ ll
总用量 0
-rw-rw-r--. 1 hepingfly hepingfly 0 2月  20 23:25 abc.txt
[hepingfly@hepingfly ~]$ ls -i abc.txt   # 使用这个命令可以根据文件名搜出这个文件名对应的 inode 号
131081 abc.txt
[hepingfly@hepingfly ~]$ find /home -inum 131081 
find: “/home/lost+found”: 权限不够
/home/hepingfly/abc.txt

#有了 inode 号，就可以使用 find 命令查找文件的位置了

```

**② 按照文件大小搜索**

> `find 搜索路径 [选项] 搜索内容`
>
> 选项：
>
> ​	`-size [+|-]大小`：按照指定大小搜索文件
>
> "+" 的意思是搜索比指定大小还要大的文件，"-" 的意思是搜索比指定文件还要小的文件

find 命令的单位：

```shell
 -size n[cwbkMG]
              File uses n units of space.  The following suffixes can be used:

              ‘b’    for  512-byte blocks (this is the default if no suffix is
                     used)  # 这是默认单位，如果单位为 b 或者不写单位，默认按照 512 字节搜索

              ‘c’    for bytes   # 搜索单位是 c ，按照字节搜索

              ‘w’    for two-byte words  # 搜索单位是 w ，按照双字节(中文)搜索

              ‘k’    for Kilobytes (units of 1024 bytes) # 按照 kb 单位搜索，必须是小写 k

              ‘M’    for Megabytes (units of 1048576 bytes)  # 按照 MB 单位搜索，必须是大写 M

              ‘G’    for Gigabytes (units of 1073741824 bytesM  # 按照 GB 单位搜索，必须是大写 G
```

例子：

```shell
# 下面这条命令的意思是在 /home 目录下搜索 5 * 512 字节的文件
[root@hepingfly hepingfly]# find /home/ -size 5   

#下面这条命令的意思是在 /home 目录下搜索大于 10M 的文件
[root@hepingfly hepingfly]# find /home/ -size +10M
```

**③ 按照修改时间搜索**

Linux 中的文件有访问时间（atime）、数据修改时间（mtime）、状态修改时间（ctime）这三个时间，我们也可以按照时间来搜索文件

> `find 搜索路径 [选项] 搜索内容`
>
> 选项：
>
> ​	`-atime [+|-]时间`：按照文件访问时间搜索 
>
> ​	`-mtime [+|-]时间`：按照文件数据修改时间搜索 
>
> ​	`-ctime [+|-]时间`：按照文件状态修改时间搜索 

**注：time 的单位是 “天”，min 单位是分钟**

重点说一下 「+|-」的含义：

> - `-5` ：代表 5 天内修改的文件
> - `5` ：代表前 5 ~ 6 天那一天修改的文件
> - `+5`：代表 6 天前修改的文件

```sh
# 在 /home 目录下搜索 5 天内修改过的文件
[root@hepingfly hepingfly]# find /home -mtime -5
/home
/home/lost+found
/home/hepingfly
/home/hepingfly/.lesshst
/home/hepingfly/abc.txt
/home/hepingfly/.mozilla
```

**④ 按照权限搜索**

> `find 搜索路径 [选项] 搜索内容`
>
> 选项：
>
> ​	`-perm 权限模式`：查找文件权限刚好等于 "权限模式" 的文件
>
> ​	`-perm -权限模式`：查找文件权限全部包含 "权限模式" 的文件
>
> ​	`-perm +权限模式`：查找文件权限全部包含 "权限模式" 的任意一个权限的文件

```shell
# 查找 /home 目录下权限是 644 的文件
[root@hepingfly hepingfly]# find /home -perm 644

[root@hepingfly hepingfly]# ll
总用量 0
-rw-rw-r--. 1 hepingfly hepingfly 0 2月  20 23:25 abc.txt      # 权限是 664
-rw-------. 1 hepingfly hepingfly 0 2月  21 00:37 bcd.txt      # 权限是 600

# "+" 表示 600 三组权限只要有一组比 444 三组权限中的一组大，就会搜索出来
[root@hepingfly hepingfly]# find /home -perm +444
/home/hepingfly/bcd.txt
/home/hepingfly/abc.txt

# "-" 表示 600 三组权限都要比 444 三组权限大或者等于，才会搜索出来
[root@hepingfly hepingfly]# find /home -perm +444
/home/hepingfly/abc.txt
```

**⑤ 按照所有者和所属组搜索**

> `find 搜索路径 [选项] 搜索内容`
>
> 选项：
>
> ​	`-uid 用户ID`：按照用户 ID 查找所有者是指定 ID 的文件
>
> ​	`-gid 组ID`： 按照用户组 ID 查找所属组是指定 ID 的文件
>
> ​	`-user 用户名`：按照用户名查找所有者是指定用户的文件
>
> ​	`-group 组名`：按照组名查找所属组是指定用户组的文件
>
> ​	`-nouser` ：查找没有所有者的文件

按照所有者和所属组搜索时，"-nouser" 选项比较常用，主要用于查找垃圾文件。只有一种情况例外，那就是外来文件。比如，光盘和 U盘中的文件如果是由 Windows 复制的，在 Linux 查看就是没有所有者的文件，再比如手工源码包安装的文件，也有可能没有所有者

```shell
# 在 /home 目录下搜索用户名是 hepingfly 的文件
[root@hepingfly hepingfly]# find /home -user hepingfly
```

**⑥ 按照文件类型搜索**

> `find 搜索路径 [选项] 搜索内容`
>
> 选项：
>
> ​	`-type d`：查找目录
>
> ​	`-type f`：查找普通文件
>
> ​	`-type l`：查找软链接文件

**⑦ 逻辑运算符**

> `find 搜索路径 [选项] 搜索内容`
>
> 选项：
>
> ​	`-a`：and 逻辑与
>
> ​	`-o`：or 逻辑或
>
> ​	`-not`：not 逻辑非

1、逻辑与

find 命令也支持逻辑运算符选项，其中 -a 代表逻辑与运算，也就是 -a 的两个条件都成立，find 搜索的结果才成立

```shell
[root@hepingfly 桌面]# find /home/ -size -20M -a -type d

# 上面这条命令的意思就是：在 /home 目录下搜索小于 20M ，并且文件类型是目录
```

2、逻辑或

-o 代表逻辑或运算，也就是 -o 的两个条件只要其中一个成立，find 命令就可以找到结果

```shell
# 在 /home 目录下搜索名称是 abc.txt 的或者名称是 bcd.txt 的
[root@hepingfly hepingfly]# find /home -name abc.txt -o -name bcd.txt 
/home/hepingfly/bcd.txt
/home/hepingfly/abc.txt
```

3、逻辑非

-not 是逻辑非，也就是取反的意思

```shell
# 在 /home/hepingfly 目录下搜索名称不是 abc.txt 的文件
[root@hepingfly hepingfly]# find /home/hepingfly/ -not -name abc.txt

#或者上面的命令你这么写也可以：
find /home/hepingfly/ ! -name abc.txt       # 注意  ！ 左右两侧一定要有空格
```

**⑧ 其他选项**

1、`-exec 选项`

> `find 搜索路径 [选项] 搜索内容 -exec 命令2 {} \;`
>
> **注：**
>
> 只要你写了 exec 命令，这个 `\;`就是必须要写的，这是格式

上面命令的作用就是把 find 命令查出来的结果放到大括号里面，交给由 -exec 调用的命令2 来处理。"{}" 就代表 find 命令的查找结果

```shell
# 这条命令的意思就是，把 /home 目录下小于 20M 的文件查出来的结果放到大括号里，然后交给 ls -l 命令去执行
find /home -size -20M -exec ls -l {} \;
```

2、`-ok 选项`

> -ok 选项和 -exec 选项的作用基本一致，区别在于：-exec 的命令2 会直接处理，而不询问。-ok 的命令2 在处理前会先询问用户是否这样处理，在得到确认命令后，才会执行

#### 5、`grep`

> grep 的作用是在文件中提取和匹配符合条件的字符串行
>
> `grep [选项] "搜索内容" 文件名`
>
> 选项：
>
> ​	`-i`：忽略大小写 
>
> ​	`-n`：输出行号
>
> ​	`-v`：反向查找（找不包含搜索内容的）
>
> ​	`--color=auto`：搜出的关键字用颜色显示

```shell
# 这条命令会把 abc.txt 文件中有关 heping 的行搜索出来
[root@hepingfly hepingfly]# grep --color=auto heping abc.txt 
hepingfly
heping hello
```

下面说下 find 命令和 grep 命令的区别：

> 1）、find 命令
>
> find 命令用于在系统中搜索符合条件的文件名，如果需要模糊查询，则使用通配符进行匹配。搜索时文件名是完全匹配的。
>
> 2）、grep 命令
>
> grep 命令用于在文件中搜索符合条件的字符串，如果需要模糊查询，则使用正则表达式进行匹配。搜索时字符串时包含匹配的。

**通配符和正则表达式的区别：**

通配符：用于匹配文件名，完全匹配

| 通配符 | 作用                                                         |
| :----- | :----------------------------------------------------------- |
| `?`    | 匹配一个任意字符                                             |
| `*`    | 匹配 0 个或多个任意字符，也就是可以匹配任何内容              |
| `[]`   | 匹配括号中任意一个字符。例如 [abc] 代表一定匹配一个字符，或者是 a ，或者是 b ，或者是 c |
| `[-]`  | 匹配括号中任意一个字符。- 代表一份范围。例如，[a-z] 代表匹配一个小写字母 |
| `[^]`  | 逻辑非，表示匹配不是中括号内的一个字符。例如，`[^0-9]` 代表匹配一个不是数字的字符 |

正则表达式：用于匹配字符串，包含匹配

| 正则符 | 作用                                                         |
| ------ | ------------------------------------------------------------ |
| `?`    | 匹配前一个字符重复 0 次，或 1 次                             |
| `*`    | 匹配前一个字符重复 0 次，或任意多次                          |
| `[]`   | 匹配括号中任意一个字符。例如 [abc] 代表一定匹配一个字符，或者是 a ，或者是 b ，或者是 c |
| `[-]`  | 匹配括号中任意一个字符。- 代表一份范围。例如，[a-z] 代表匹配一个小写字母 |
| `[^]`  | 逻辑非，表示匹配不是中括号内的一个字符。例如，`[^0-9]` 代表匹配一个不是数字的字符 |
| ^      | 匹配行首                                                     |
| $      | 匹配行尾                                                     |

#### 7、管道符

> 命令格式：
> 	`命令1|命令2`
>
> 命令1 的输出作为命令2 的输入

```shell
# 将 ll 命令的结果，作为 more 命令的输入
[root@hepingfly 桌面]# ll /etc/ |more
```

你会发现管道符的作用和前面说的 find 中  exec 命令很像，都是把一个命令的输出作为另一个命令的输入。（可以看前面 find 命令）

> 其实可以说管道符和 exec 命令基本作用一致，可以为什么基本作用一样还要弄两个命令呢？
>
> 我们把含有管道符的操作叫做文本流操作，但是 find 命令不支持文本流操作，但是管道符又很有用，所以在 find 命令中使用 exec 选项来模拟管道符

```shell
# 管道符的操作时文本流操作，不论前一个命令的结果是什么，最终都是以字符串形式呈现，grep 是搜索符合条件的字符串(相当于我把前一个命令的结果保存在一个临时文件中，然后后一个命令去操作它)
[root@hepingfly 桌面]# ll /etc/ | grep yum
```

#### 8、`netstat`

`netstat` 在 CentOS7 中，需要安装 `net-snmp.x86_64`，`net-tools.x86_64` 两个包才有此命令。7.5 系统中已经自动安装 

> `netstat [选项]`
>
> 选项：
>
> ​	`-a`：列出所有网络状态，包括 Socket 程序
>
> ​	`-c 秒数`：指定每隔几秒刷新一次网络状态
>
> ​	`-n`：使用 IP 地址和端口号显示，不使用域名和服务名
>
> ​	`-p`：显示 PID 和程序名
>
> ​	`-t`：显示使用 TCP 协议端口的连接状况
>
> ​	`-u`：显示使用 UDP 协议端口的连接状况
>
> ​	`-l`：仅显示监听状态的连接
>
> ​	`-r`：显示路由表	 

````shell
# 这条命令的意思就是查询系统中所有开启的端口
[root@hepingfly 桌面]# netstat -tuln
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address               Foreign Address             State      
tcp        0      0 0.0.0.0:38238               0.0.0.0:*                   LISTEN      
tcp        0      0 0.0.0.0:111                 0.0.0.0:*                   LISTEN      
tcp        0      0 0.0.0.0:22                  0.0.0.0:*                   LISTEN 
````

```sh
[root@hepingfly 桌面]# netstat -anp |more
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address               Foreign Address             State       PID/Program name   
tcp        0      0 0.0.0.0:38238               0.0.0.0:*                   LISTEN      2139/rpc.statd             
tcp        0      0 192.168.31.240:22           192.168.31.90:50135         ESTABLISHED 7199/sshd 


# ESTABLISHED 这个状态表示有人正在远程登录我的服务器，可以通过  Foreign Address 来看是谁在登录

# 那么想一下我可不可以判断，当前系统到底有多少在正在访问？
[root@hepingfly 桌面]# netstat -anp | grep ESTABLISHED | wc -l
2

# 通过上面这个命令我就可以知道具体的网络连接数量
```

```sh
[root@hepingfly 桌面]# netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
192.168.31.0    0.0.0.0         255.255.255.0   U         0 0          0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U         0 0          0 docker0
0.0.0.0         192.168.31.1    0.0.0.0         UG        0 0          0 eth0
# 最后一个就是网关，可以使用 netstat -rn 去查看网关
```



### 八、压缩和解压缩命令

在 Linux 中可以识别的常见压缩格式有十几种，比如：".zip"、".gz"、".bz2"、“tar”、".tar.gz"、".tar.bz2"等

#### 1、".zip" 格式

".zip" 是 Windows 中最常用的压缩格式，Linux 也可正确识别 ".zip" 格式，这可以方便的和 Windows 系统通用压缩文件。

**".zip" 格式压缩命令：**

> `zip [选项] 压缩包名 源文件或源目录`
>
> 选项：
>
> ​	`-r`：压缩目录 

```shell
[root@hepingfly hepingfly]# zip mytxt.zip abc.txt abd.txt bcd.txt 
  adding: abc.txt (deflated 6%)
  adding: abd.txt (stored 0%)
  adding: bcd.txt (stored 0%)
  
  # 把三个 txt 文件压缩成一个 zip 文件
```

**".zip" 格式解压缩命令：**

> `unzip [选项] 压缩包名`
>
> 选项：
>
> ​	`-d`：指定解压缩位置

```shell
[root@hepingfly hepingfly]# unzip -d /home/hepingfly/abc/ mytxt.zip 
Archive:  mytxt.zip
  inflating: /home/hepingfly/abc/abc.txt  
 extracting: /home/hepingfly/abc/abd.txt  
 extracting: /home/hepingfly/abc/bcd.txt  
 
 # 如果不指定 -d 参数，默认解压到当前目录下
```

#### 2、".gz" 格式（不能打包）

**".gz" 格式压缩命令：**

> `gzip [选项] 源文件`
>
> 选项：
>
> ​	`-c`：将压缩数据输出到标准输出中，可以用于保留源文件
>
> ​	`-d`：解压缩
>
> ​	-r：压缩目录
>
> gzip 压缩文件后会把源文件删除掉，它是不支持保留源文件的 

```shell
[root@hepingfly hepingfly]# gzip abc.txt 
[root@hepingfly hepingfly]# ll
总用量 12
-rw-rw-r--. 1 hepingfly hepingfly   60 2月  22 01:33 abc.txt.gz
# 压缩完之后源文件也会被删除掉

----------------------------------------------------------------------------
[root@hepingfly hepingfly]# gzip -c bcd.txt > bcd.txt.gz
[root@hepingfly hepingfly]# ll
总用量 16
-rw-------. 1 hepingfly hepingfly    0 2月  21 00:37 bcd.txt
-rw-r--r--. 1 root      root        28 2月  25 00:55 bcd.txt.gz

# 你使用 gzip -c bcd.txt 命令，会把压缩数据输出到屏幕上，现在我不让它输出到屏幕上，而是重定向到一个压缩文件中，这样就实现了在压缩你文件的同时不删除源文件

----------------------------------------------------------------------------
[root@hepingfly hepingfly]# gzip -r aaaa/
[root@hepingfly hepingfly]# ll aaaa/
总用量 8
-rw-r--r--. 1 root root 26 2月  25 01:02 a.txt.gz
-rw-r--r--. 1 root root 26 2月  25 01:02 b.txt.gz
# 你使用 gzip -r 命令，它是把这个目录下的所有文件都压缩，而不会把这个目录压缩(不能打包)

```

**".gz" 格式解压缩命令：**

> `gzip -d 压缩包名`
>
> `gunzip 压缩包名`
>
> 上面两个命令都可以

#### 3、".bz2 格式（不能压缩目录）"

".bz2" 格式，是 Linux 的另一种压缩格式，从理论上来讲，".bz2" 格式的算法更先进、压缩比更好；而 ".gz" 格式相对来讲压缩时间更快

**".bz2" 格式压缩命令：**

> `bzip2 [选项] 源文件`
>
> 选项：
>
> ​	`-d`：解压缩
>
> ​	`-k`：压缩时保留源文件
>
> ​	`-v`：显示压缩的详细信息

```shell
[root@hepingfly hepingfly]# bzip2 -k bcd.txt 
[root@hepingfly hepingfly]# ll
总用量 20
-rw-------. 1 hepingfly hepingfly    0 2月  21 00:37 bcd.txt
-rw-------. 1 hepingfly hepingfly   14 2月  21 00:37 bcd.txt.bz2

# 保留源文件压缩
```

**注：**

这条命令不能压缩目录，压缩目录就会报错。前面的 gzip 命令是不能打包，你用 gzip 命令压缩目录，它会把你目录下的文件进行压缩，不会把你这个目录进行压缩

**".bz2" 格式解压缩命令：**

> `bzip2 -d 压缩包`
>
> `bunzip2 压缩包`
>
> 上面两个命令都可以

#### 4、".tar " 格式（打包不会压缩）

".tar" 格式的打包和解打包都使用 tar 命令，区别只是选项不同。

**".tar" 格式打包命令：**

> `tar [选项] [-f 压缩包名] 源文件或目录`
>
> 选项：
>
> ​	`-c`：打包
>
> ​	`-f`：指定压缩包的文件名。压缩包的扩展名是用来给管理员识别格式的，所以一定要正确指定扩展名。
>
> ​	`-v`：显示打包文件过程

```shell
[root@hepingfly hepingfly]# tar -cvf test.tar abd.txt bcd.txt 
abd.txt
bcd.txt
```

**".tar" 格式解打包命令：**

> `tar [选项] 压缩包`
>
> 选项：
>
> ​	`-x`：解打包
>
> ​	`-f`：指定压缩包的文件名
>
> ​	`-v`：显示解打包文件过程

```shell
[root@hepingfly hepingfly]# tar -xvf test.tar 
abd.txt
bcd.txt
```

#### 5、".tar.gz" 和 ".tar.bz2" 格式

使用 tar 命令直接打包压缩

> `tar [选项] 压缩包 源文件或目录`
>
> 选项：
>
> ​	`-z`：压缩和解压缩 ".tar.gz" 格式
>
> ​	`-j`： 压缩和解压缩 ".tar.bz2" 格式

```shell
# 压缩
[root@hepingfly hepingfly]# tar -jcvf test.gz.bz2 abd.txt bcd.txt 
abd.txt
bcd.txt
[root@hepingfly hepingfly]# ll
总用量 24
-rw-r--r--. 1 root      root        160 2月  25 23:11 test.gz.bz2
---------------------------------------------------------------
# 解压缩
[root@hepingfly hepingfly]# tar -jxvf test.gz.bz2 
abd.txt
bcd.txt
----------------------------------------------------------------
# 压缩
[root@hepingfly hepingfly]# tar -zcvf test.tar.gz abd.txt bcd.txt 
abd.txt
bcd.txt
[root@hepingfly hepingfly]# ll
总用量 12
-rw-r--r--. 1 root      root         0 2月  22 02:09 abd.txt
-rw-------. 1 hepingfly hepingfly    0 2月  21 00:37 bcd.txt
-rw-r--r--. 1 root      root       154 2月  25 23:14 test.tar.gz
----------------------------------------------------------------
# 解压缩
[root@hepingfly hepingfly]# tar -zxvf test.tar.gz 
abd.txt
bcd.txt

--------------------------------------------------------------
# 只查看不解压
[root@hepingfly hepingfly]# tar -ztvf test.tar.gz 
-rw-r--r-- root/root         0 2019-02-22 02:09 abd.txt
-rw------- hepingfly/hepingfly 0 2019-02-21 00:37 bcd.txt

--------------------------------------------------------------
# 解压到指定目录(-C 一定要跟在压缩包后面)
[root@hepingfly hepingfly]# tar -zxvf test.tar.gz -C aaaa/
abd.txt
bcd.txt

--------------------------------------------------------------
# 解压指定文件
[root@hepingfly hepingfly]# tar -ztvf test.tar.gz         # 我先查看这个压缩包里面有哪些文件
-rw-r--r-- root/root         0 2019-02-22 02:09 abd.txt
-rw------- hepingfly/hepingfly 0 2019-02-21 00:37 bcd.txt
[root@hepingfly hepingfly]# tar -zxvf test.tar.gz -C aaaa/ abd.txt 
abd.txt
# 我只把 abd.txt 文件解压到 aaaa 目录下
```

### 九、关机和重启命令

#### 1、sync 数据同步

> 由于服务器不像我们的个人电脑，它往硬盘中读写请求可能会非常多，所以会把一部分数据先放在内存缓冲区中，使用  sync 命令就是把缓冲区中的内容往硬盘上写。刷新文件系统缓冲区。一般在重启前会多敲几次这个命令（至于有没有用，官方说有用，你只能选择相信）

#### 2、shutdown 命令

> 关机和重启
>
> `shutdown [选项] 时间 [警告信息]`
>
> 选项：
>
> ​	`-c`：取消已经执行的 shutdown 命令
>
> ​	`-h`：关机
>
> ​	`-r`：重启 

#### 3、reboot 命令

重启命令，敲完这个命令系统重启

#### 4、halt 和 poweroff 命令

这两个都是关机命令，直接执行即可。这两个命令不会完整关闭和保存系统的服务，不建议使用。

### 十、常用网络命令

#### 1、配置 ip 地址

**配置 ip 地址有两种方法：**

> 1）、`setup 工具`
>
> 2）、`vim /etc/sysconfig/network-scripts/ifcfg-eth0  手工修改配置文件`
>
> **注：**
>
> 配置完 ip 地址后，需要你手动重启网络服务
>
> `service network restart`

**说一个问题：**

> 我们使用 `service network restart` 重启网络服务
>
> ```
> [root@hepingfly 桌面]# service network restart
> 正在关闭接口 eth0： 设备状态：3 (断开连接)
>                                                            [确定]
> 关闭环回接口：                                             [确定]
> 弹出环回接口：                                             [确定]
> 弹出界面 eth0： Determining if ip address 192.168.44.2 is already use for device eth0...													 [确定]
> ```
>
> 有时候最后一步可能不是「确定」而是 「失败」，原因就是 ip 地址冲突了，你换个 ip 就可以解决。但如果你换了很多个 ip 还是有问题，那么就可能不是 Ip 冲突的问题。可能是 UUID 重复了。解决方法：
>
> ````
> ① vim /etc/sysconfig/network-scripts/ifcfg-eth0
> 编辑这个文件，然后删除 MAC 地址行
> ② rm -rf /etc/udev/rules.d/70-persistent-net.rules
> 删除 UUID 和 MAC 地址绑定文件
> ③reboot
> 重启 linux
> ````
>
> 

#### 2、ifconfig 命令

> 最主要的作用就是查看 ip 地址

```shell
eth0      Link encap:Ethernet  HWaddr 00:0C:29:4B:40:17  
          inet addr:192.168.31.240  Bcast:192.168.31.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe4b:4017/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:9405 errors:0 dropped:0 overruns:0 frame:0
          TX packets:333 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:568906 (555.5 KiB)  TX bytes:23748 (23.1 KiB)
 
# HWaddr：表示 MAC 地址
# inet addr：表示 Ip 地址
# Mask：表示子网掩码
```

#### 3、write 命令

> 向其他用户发送信息
>
> `write 用户名 终端号`

```shell
[root@hepingfly 桌面]# write root pts/1
hello

#上面命令意思就是，向 pts/1(远程终端1) 登录的 root 用户发送信息，使用 "Ctrl + D" 保存发送的数据
```

#### 4、wall 命令

> write 命令用于给指定用户发送信息，而 wall 命令用于给所有登录用户发送信息包括你自己，执行时在 wall 命令后加入需要发送的信息即可 

```shell
 wall "hello hi"     # 给所有用户发送这个信息
```

#### 5、mail 命令

mail 是 Linux 的邮件客户端命令，可以利用这个命令给其他用户发送邮件。

**1）、发送邮件**

如果我们想给其他用户发送邮件，可以使用如下命令：

```shell
[root@hepingfly 桌面]# mail hepingfly            # mail 收件人用户名
Subject: hello							# 邮件标题
i am root how are you					# 邮件具体内容
.										# 使用 "." 来结束输入
EOT

# 结果就是发送邮件给 hepingfly 用户
```

我们收到的邮件都保存在 `/var/spool/mail/用户名` 中，每个用户都有一个以自己用户命名的邮箱

**2）、发送文件内容**

如果我们想把某个文件的内容发送给指定用户，可以执行如下命令：

```shell
[root@hepingfly mail]# mail -s "from root" hepingfly < hello.txt

# 使用 -s 指定邮件标题
# 上面命令的结果就是把 hello.txt 的内容发送给 hepingfly 用户
```

**3）、查看已经接收的文件**

我们可以直接在命令行执行 mail 命令，进入 mail 的交互命令中，可以在这里查看到已经接收到的邮件。

```shell
[root@hepingfly 桌面]# mail
Heirloom Mail version 12.4 7/29/08.  Type ? for help.
"/var/spool/mail/root": 2 messages
>   1 Anacron               Wed Feb 20 21:23  18/669   "Anacron job 'cron.daily' on hepingfly"
    2 Anacron               Sat Feb 23 19:12  18/669   "Anacron job 'cron.daily' on hepingfly"
& 

# 通过数字编号来操作邮件，输入对应的数字编号即可查看对应的邮件
```

### 十一、系统痕迹命令

系统中有一些重要的痕迹日志文件，如 `/var/log/wtmp`、`/var/run/utmp`、`/var/log/btmp`、`/var/log/lastlog`

等日志文件，如果你用 vim 打开这些日志文件，你会发现这些文件是二进制乱码。这是由于这些日志中保存的是系统的重要登录痕迹，包括某个用户何时登录的系统，何时退出了系统，错误登录等重要的系统信息。这些信息要是可以通过 vim 打开，就能编辑，这样痕迹信息就不准确，所以这些重要的痕迹日志，只能通过对应的命令来查看。

#### 1、w 命令

> w 命令是显示系统中正在登录的用户信息的命令，这个命令查看的痕迹日志是 `/var/run/utmp`

```shell
[root@hepingfly bin]# w
#系统时间    # 持续开机时间  #登录用户   #系统在 1 分钟，5 分钟，15 分钟前的平均负载
 00:21:28 up 18 min,  2 users,  load average: 0.46, 0.24, 0.17
USER     TTY      FROM              LOGIN@   IDLE   JCPU   PCPU WHAT
root     tty1     :0               00:03   18:14   2.77s  2.77s /usr/bin/Xorg :0 -br -verbose -audit 4 -auth /var/run/gdm/auth-for-gdm-vzfS4H/database -
root     pts/0    :0.0             00:17    0.00s  0.05s  0.04s w
```

**第一行信息内容如下：**

| 内容                           | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| 00:21:28                       | 系统当前时间                                                 |
| up 18 min                      | 系统的运行时间，本机已经运行了 18 min 没有关机               |
| 2 users                        | 当前登录了两个用户                                           |
| load average: 0.46, 0.24, 0.17 | 系统在之前 1 分钟，5 分钟，15 分钟的平均负载。如果 CPU 是单核的，则这个数值超过 1 就是高负载；如果 CPU 是 四核的，则这个数值超过 4 就是高负载（这个平均负载完全是按照个人经验来判断的，一般认为不应该超过 CPU 的核数） |

**第二行信息内容如下：**

| 内容   | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| USER   | 当前登录的用户                                               |
| TTY    | 登录的终端：<br />tty1-6：本地字符终端（alt + F1~F6 切换）<br />tty7：本地图形终端（ctrl + alt + F7切换，必须按照启动图形界面）<br />pts/0-255：远程终端 |
| FROM   | 登录的 IP 地址，如果是本地终端，则是空的                     |
| LOGIN@ | 登录时间                                                     |
| IDLE   | 用户闲置时间                                                 |
| JCPU   | 所有的进程占用 CPU 的时间                                    |
| PCPU   | 当前进程占用 CPU 的时间                                      |
| WHAT   | 用户正在进行的操作                                           |

#### 2、who 命令

who 命令 和 w 命令类似，用于查看正在登录的用户，但是显示的内容更加简单，也是查看 `/var/run/utmp` 的日志

```sh
[root@hepingfly bin]# who
root     tty1         2019-02-28 00:03 (:0)
root     pts/0        2019-02-28 00:17 (:0.0)
#用户名    #登录终端      登录时间(来源 ip)
```

#### 3、last 命令

last 命令是查看系统所有登录过的用户信息，包括正在登录的用户和之前登录过的用户。这个命令查看的是 /var/log/wtmp 痕迹日志文件

```shell
[root@hepingfly bin]# last
root     pts/0        :0.0             Thu Feb 28 00:17   still logged in   
root     tty1         :0               Thu Feb 28 00:03   still logged in   
reboot   system boot  2.6.32-642.el6.x Thu Feb 28 00:03 - 00:52  (00:49)  
#用户名    #终端号      #来源 IP 地址			#登录时间			# 退出时间
```

#### 4、lastlog 命令

lastlog 命令是查看系统中所有用户最后一次的登录时间的命令，他查看的日志是 `/var/log/lastlog` 文件

#### 5、lastb 命令

lastb 是查看错误登录信息的，查看的是 `/var/log/btmp` 痕迹日志

```shell
[root@hepingfly bin]# lastb
hepingfl tty1         :0               Wed Feb 27 02:16 - 02:16  (00:00)    
hepingfl tty1         :0               Sat Feb 23 18:50 - 18:50  (00:00)    

btmp begins Sat Feb 23 18:50:09 2019
# 错误登录用户    # 终端       #尝试登录时间
```

### 十二、挂载命令

#### 1、挂载命令

linux 所有存储设备都必须挂载使用，包括硬盘

> `mount [-t 文件系统] [-L 卷标名] [-o 特殊选项] 设备文件名 挂载点`
>
> 选项：
>
> ​	`-t 文件系统`：加入文件系统类型来指定挂载的类型，可以是 ext3 、 ext4 、iso9660 等文件系统
>
> ​	`-L 卷标名`：  挂载指定卷标的分区，而不是按照设备文件名挂载（现在基本不用这个参数）
>
> ​	-o 特殊选项：可以指定挂载的额外选项，比如读写权限、同步异步等，如果不指定则默认值生效。

```shell
# 查询系统中已经挂载的设备， -l 会显示卷标名
[root@hepingfly 桌面]# mount [-l]

------------------------------------------------
[root@hepingfly 桌面]# mount -a
# 上面命令的意思是，根据配置文件 /etc/fstab 的内容，自动挂载。它还可以用来检测下 /etc/fstab 这个文件有没有问题，如果这个文件有问题的话，使用 mount -a 命令会报错，方式系统重启后崩溃。因为系统重启会去扫描 /etc/fstab 这个文件
```

```shell
mount -o remount,noexec /boot
# 重新挂载 /boot 分区，并使用 noexec 权限      这样整个 boot 分区下所有的文件都没有了执行权限
# remount 用于重新挂载已经挂载的文件系统，一般用于指定修改特殊权限
```



#### 2、光盘挂载

> 光盘挂载的前提依然是指定光盘的设备文件名，不同版本的 Linux ，设备文件名并不相同
>
> - CentOS 5.X 以前的系统，光盘设备文件名是 `/dev/hdc`
> - CentOS 6.X 以后的系统，光盘的设备文件名是 `/dev/sr0`

```shell
[root@hepingfly 桌面]# mount /dev/sr0 /mnt/cdrom/
mount: you must specify the filesystem type

# 挂载的时候可能会出现上面的错误，原因是因为你没有插入光盘，光驱是空的

[root@hepingfly 桌面]# mount /dev/sr0 /mnt/cdrom/
mount: block device /dev/sr0 is write-protected, mounting read-only
# 上面的意思就是 sr0 想被挂为读写权限，但是其实只有读权限。光盘我们都知道他是只读的，所以这个信息你恰恰可以认为是挂载正确提示

-----------------------------------------------------------------------------------
光盘用完之后记得卸载：
[root@hepingfly 桌面]# umount /dev/sr0
[root@hepingfly 桌面]# umount /mnt/cdrom
# 上面两条命令都可以，因为设备文件名和挂载点已经连接到一起，卸载哪一个都可以

#注：
卸载的时候需要退出光盘目录才能正常卸载
```

**解释下挂载的时候挂载点为什么一定要是空目录？**

> 首先说挂载点如果不是空目录的话，挂载也是可以成功的。但是这个目录下的文件就看不到了，但是文件并没有被删除，这个文件处于既不能被删除也不能被访问的状态，等你卸载的时候发现这个目录下的文件又可以访问了。

#### 3、U 盘挂载

U 盘会和硬盘共用设备文件名，所以 U 盘的设备文件名不是固定的，需要手工查询

```shell
[root@hepingfly 桌面]# fdisk -l                     # fdisk 是分区工具，fdisk -l 是查看磁盘分区信息
# 如果你插入 U 盘的话，你可以从这个信息里面看到 U 盘的设备文件名
```

解释一下上面的内容：

> 假设你现在只有一块硬盘，那么硬盘的设备文件名应该就是 sda ，那么此时你插入  U 盘，U 盘的设备文件名应该就是 sdb ，但是如果你有两块硬盘，那么第二块硬盘的设备文件名应该就是 sdb ，如果此时你插入 U 盘，U 盘的设备文件名就是会是 sdc。所以说每一个人的系统中，你 U 盘的设备文件名都不是固定的。

**再说一遍：**

==挂载就是把设备文件名和空目录连起来==

**挂载 U 盘：**

```shell
[root@hepingfly 桌面]# mount -t vfat /dev/sdb1 /mnt/usb/

解释下上面的命令：
u 盘是 windows 下为 FAT32 文件系统，在 linux 下识别为 vfat ，所以使用 「-t vfat 」，至于 「/dev/sdb1」为 u 盘被 linux 系统识别的设备文件名，不同操作系统识别的可能不一样，按照上面说的方法查询一下 u 盘的设备文件名即可
```

如果 U 盘中有中文，会发现是中文乱码。Linux 要想正常显示中文，需要两个条件：

> - 安装了中文编码和中文字体
> - 操作终端需要支持中文显示（纯字符终端，是不支持中文编码的）

而我们当前系统是安装了中文编码和字体的，而 xshell 远程终端是 windows 下的程序，当然是支持中文显示的。那之所以挂载 U 盘还出现乱码，是需要在挂载的时候，手工指定中文编码。

```shell
[root@hepingfly 桌面]# mount -t vfat -o iocharset=utf8 /dev/sdb1 /mnt/usb/
# 挂载 U 盘，指定中文编码格式为 utf-8
```

如果需要卸载：

```shell
umount /mnt/usb/
```

### 十三、vim 编辑器

#### 1、vim 编辑器简介

vim 是一个全屏幕纯文本编辑器，是 vi 编辑器的增强版。你可以利用别名让输入 vi 命令的时候，实际上执行 vim 编辑器

```shell
[root@hepingfly ~]# alias vi='vim'
但是这样定义别名是临时生效，如果需要永久生效，请放置环境变量配置文件    ~/.bashrc   中
```

#### 2、vim 基本使用

**1）、vim 的工作模式**

![vim 工作模式](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-linux/vim%E5%B7%A5%E4%BD%9C%E6%A8%A1%E5%BC%8F.png?q-sign-algorithm=sha1&q-ak=AKIDovIurWu9ROHEuplJkECZ154Zlgx3qyTi&q-sign-time=1551718389;1551720189&q-key-time=1551718389;1551720189&q-header-list=&q-url-param-list=&q-signature=4ce6a8ce215b6aea5dd8ffe4922e87d2e70554df&x-cos-security-token=515a769776822921dcff94ac2e859f584df148ea10001)

命令模式：是主要输入快捷键的模式。命名模式要想进入输入模式，可以使用 a i o 等快捷键来进入。

**插入命令：**

| 命令 | 作用                   |
| ---- | ---------------------- |
| `a`  | 在光标所在的字符后插入 |
| `A`  | 在光标所在的行尾插入   |
| `i`  | 在光标所在的字符前插入 |
| `I`  | 在光标所在的行首插入   |
| `o`  | 在光标下插入新行       |
| `O`  | 在光标上插入新行       |

输入模式：主要用于文本编辑，和记事本类似，输入数据就好。

末行模式（编辑模式）：

> :w  保存不退出
>
> :w 新文件名        把文件另存为新文件
>
> :q   不保存退出
>
> :wq   保存退出
>
> :!    强制
>
> :q!  强制不保存退出，用于修改文件之后，不保存数据退出
>
> :wq!   强制保存退出，当文件的所有者或 root 用户，对文件没有写权限的时候，强制写入数据使用

#### 3、命令模式操作

**1、移动光标**

1）、上下左右移动光标

> 上、下、左、右方向键		移动光标
>
> h（左）     j（下）      k（上）      l（右）           		移动光标

2）、光标移动到文件头或文件尾

> gg  			移动到文件头
>
> G			移动到文件尾（shift + g）

3）、光标移动到行首或行尾

> ^			移动到行首
>
> $			移动到行尾

4）、移动到指定行

> :n			移动到第几行（这里的 n 是数字）

**2、删除或剪切**

1）、删除字母

> x  			删除单个字母
>
> nx			删除 n 个字母（n 是数字，如果打算从光标位置连续删除 10 个字母，可以使用 10x 即可）

2）、删除整行或剪切

> dd 			删除单行
>
> ndd			删除多行
>
> :n1,n2d		删除指定范围的行

删除行或多行，是比较常用的删除方法。这里的 dd 快捷键既是删除也是剪切。删除内容放入了剪切板，如果不粘贴就是删除，粘贴就是剪切。粘贴方法：

> p			粘贴到光标下面一行
>
> P			粘贴到光标上面一行

3）、从光标所在行删除到文件尾

> dG			从光标所在行删除到文件尾（d 是删除行，G 是文件尾，连起来就是从光标行删除到文件尾）

**3、复制**

> yy			复制单行
>
> nyy			复制多行

复制之后的粘贴依然可以使用 p 键或 P 键

**4、撤销**

> u			撤销
>
> ctrl + r              反撤销

u 键能一直撤销到文件打开时的状态，ctrl + r 能一直反撤销到最后一次操作状态

**5、替换**

> r			替换光标所在处的字符
>
> R			从光标所在处开始替换字符，按 esc 键结束

#### 4、末行模式操作

**1、一些设置操作**

| 设置参数                               | 含义                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| `:set nu`<br />`:set nonu`             | 显示与取消行号                                               |
| `:syntax on`<br />`:syntax off`        | 是否依据语法显示相关颜色帮助。默认会显示相关颜色来帮助排错   |
| `:set hlsearch`<br />`:set nohlsearch` | 设置是否将查找的字符串高亮显示。默认是高亮显示               |
| `:set showmode`<br />`:set noshowmode` | 设置是否在左下角显示如“-INSERT-” 之类的状态栏。默认是开启的  |
| `:set list`<br />`:set nolist`         | 设置是否显示隐藏字符。（tab 键用 "^I" 表示，回车符用 "$" 键表示） |

**注：**

> - 上面设置的参数都只是临时生效，一旦关闭文件再打开，又需要重新输入。如果想要永久生效，需要手动建立 vim 的配置文件 ==“~/vimrc”== ，把你需要的参数写入配置文件就永久生效了。
> - Windows 下回车符在 Linux 中是用 `“^M$”` 显示的，而不是   `“$”` 。这样会导致 Windows 下编辑的程序脚本（如 shell 脚本），无法在 Linux 中执行。这时可以通过命令 ==「dos2unix」==，把 windows 格式转换为 Linux 格式。同样可以使用 ==「unix2dos」== ，把 Linux 格式转换为 Windows 格式。这两个命令默认没有安装，需要手动安装才能使用。

**2、查找**

> /查找内容			从光标所在行向下查找
>
> ?查找内容			从光标所在行向上查找
>
> n					下一个
>
> N					上一个

**3、替换**

> :1,10s/old/new/g			替换 1 到 10 行所有 old 为 new（s 表示替换）
>
> :%s/old/new/g			替换整个文件的 old 为 new（% 是一个变量，在这里代表整篇文档）

在 shell 中 "#" 开头是注释，假如说我想注释文件前 10 行，如果手工一个一个注释会很麻烦，可以使用替换来解决。

> :1,10s/^/#/g				注释 1 到 10 行
>
> :1,10s/^#//g				取消 1 到 10 行的注释

#### 5、vim 使用技巧

**1、导入其他文件的内容**

> :r 文件名  			把文件内容导入到光标位置

```shell
:r /root/aa.txt              #末行模式下，输入这个会把 /root/aa.txt 文件的内容加到你光标所在处
```

**2、在 vim 中执行系统命令**

> :!命令				在 vim 中执行系统命令
>

这里只是在 vim 中执行系统命令，但并不把系统命令的结果写入到文件中。主要用于在文件编辑中，查看系统信息，如时间。 

**3、导入命令结果**

> :r  !命令				在 vim 中执行系统命令，并把命令结果导入光标所在行

**4、多文件打开**

在 vim 中可以同时打开两个文件

```shell
[root@hepingfly ~]# vim -o aa.txt abcd               上下分屏打开两个文件(小 o)
[root@hepingfly ~]# vim -O aa.txt abcd               左右分屏打开两个文件(大 O)
```

如果是上下打开两个文件，可以通过先按 「ctrl + w」再按上下箭头的方式在两个文件之间切换。

如果是左右打开两个文件，可以通过先按 「ctrl + w」再按左右箭头的方式在两个文件之间切换。

### 十四、软件包安装

#### 1、软件包分类

1）、源码包

**特点：**

> - 优点
>   - 开源，如果有足够的能力，可以修改源代码 
>   - 可以自由选择所需要的功能
>   - 软件是编译安装，所以更加适合自己的系统，更加稳定效率也更高
>   - 卸载方便
> - 缺点
>   - 安装步骤多
>   - 编译时间长，安装比二进制安装时间长 

2）、二进制包

**二进制包分类：**

> - DPKG 包：是由 Debian Linux 所开发出来的包管理机制，通过 DPKG 包， Debian Linux 就可以进行软件包管理。主要应用在 Debian 和 unbuntu 中
> - RPM 包：是由 Red Hat 公司所开发的包管理系统。功能强大，安装、升级、查询和卸载都非常方便。目前很多 Linux 都在使用这种包管理方式，包括 Fedora、CentOS、SuSE等

**特点：**

> - 优点
>   - 包管理系统简单，只通过几个命令就可以实现包的安装、升级、查询和卸载
>   - 安装速度比源码包安装快的多
> - 缺点
>   - 经过了编译，看不到源代码
>   - 功能选择不如源码包灵活
>   - 依赖性。有时候我们会发现需要安装软件包 a 时，需要先安装 b 和 c  ，而安装 b 时，需要安装 d 和 e。也就是说需要先安装 d 和 e，再安装 b 和 c ，最后才能安装 a 包

#### 2、rpm 依赖性

这里主要说一个模块依赖：

什么是模块依赖，举个例子，尝试安装以下文件：

```shell
[root@hepingfly Packages]# rpm -ivh mysql-connector-odbc-5.1.5r1144-7.el6.x86_64.rpm 
error: Failed dependencies:
	libodbcinst.so.2()(64bit) is needed by mysql-connector-odbc-5.1.5r1144-7.el6.x86_64
	unixODBC is needed by mysql-connector-odbc-5.1.5r1144-7.el6.x86_64
```

发现报错，需要安装 `libodbcinst.so.2` 函数库文件，这时候会发现光盘中根本找不到这个文件。那是因为函数库没有单独成包，是包含在某一个软件包中的。而如果要知道在哪个软件包中，需要查询网站 `http://rpmfind.net/` （或者直接百度搜索 rpm 包查找）

#### 3、rpm 包安装

**1）、rpm 包命名规则**

`httpd-2.2.15-15.el6.centos.1.i686.rpm`

> - httpd
>   - 软件包名
> - 2.2.15
>   - 软件版本
> - 15
>   - 软件发布次数
> - el6
>   - 软件发行商。el6 是 Red Hat 公司发布。适合 RHEL6.X  和 CentOS6.X 下使用
> - i686
>   - 适合的硬件平台。RPM 包可以在不同的硬件平台安装，所以出现了所谓的 i386（386 以上的计算机都可以安装）、i586（586 以上的计算机都可以安装）、i686（奔腾 II 以上的计算机都可以安装）、x86_64（64 位以上 CPU 都可以安装）和 noarch（没有硬件限制）

**这里简单说两个概念：**

> 包全名：`httpd-2.2.15-15.el6.centos.1.i686.rpm` 这叫包全名。如果操作的是未安装软件包，则使用包全名，而且需要注意使用绝对路径。
>
> 包名：如果操作的是已经安装的软件包，则使用包名即可，系统会生成 rpm 包数据库（/var/lib/rpm/ 下面）

**2）、rpm 包手工命令安装**

① 默认安装位置



② rpm 包安装

**安装命令：**

> `rpm -ivh 包全名`
>
> 选项：
>
> ​	`-i`：install 安装
>
> ​	`-v`：显示详细信息
>
> ​	`-h`：显示安装进度（hash）
>
> ​	`--nodeps`：不检测依赖性安装。安装时会检测依赖性，确定底层所需的软件是否安装。如果没有安装则会报错。如果我不管依赖性，想强行安装，可以使用这个选项。注意：这样不检测依赖性安装的软件基本是不能使用的，所以不建议这样做
>
> ​	`--force`：强制安装。不管是否已经安装，都重新安装。

rpm 安装的服务启动命令：

> - `service httpd restart`
> - `/etc/rc.d/init.d/httpd start|stop|restart`
>
> 其实第一条命令 service 去启动或者停止服务，它本质上也是会去 /etc/rc.d/init.d/  这个目录下找 httpd 然后进行启动，只不过把命令简化了。假如你把 /etc/rc.d/init.d/httpd  给它重命名一下，你会发现用 service httpd restart 启动不起来了，原因就是它找不到这个文件了。

#### 4、rpm 包升级和卸载

1）、rpm 包升级

> `rpm -Uvh 包全名`
>
> 选项：
>
> ​	`-U`：升级安装，如果没有安装过，系统直接安装。如果安装过的版本较旧，则升级到新版本

2）、卸载

> `rpm -e 包名`
>
> 选项：
>
> ​	`--nodeps`：不检查依赖性
>
> ​	`-e`：卸载

#### 5、rpm 包查询

**1）、查询软件包是否安装**

> `rpm -q 包名`
>
> 选项：
>
> ​	`-q`：查询（query）

**2）、查询系统中所有已安装的软件包**

> `rpm -qa`
>
> 选项：
>
> ​	`-a`：所有（all）
>
> 也可以使用：
>
> `rpm -qa | grep httpd`

**3）、查询软件包中的文件列表**

> 可以查询已经安装的软件包中的文件列表和安装的完整目录
>
> `rpm -ql 包名`
>
> 选项：
>
> ​	`l`：列出软件包中所有的文件列表和软件所安装的目录（list）

查询没有安装的软件包中的文件列表和打算安装的位置：

> `rpm -qlp 包全名`
>
> 选项：
>
> ​	`p`：没有安装的软件包信息（package）

**4）、查询系统文件属于哪个 rpm 包**

> 既然可以知道每个 rpm 包中的文件的安装位置，那么可以查询系统文件属于哪个 rpm 包吗？当然可以。但是要注意，手工建立的文件是不能查询的，因为这些文件不是通过 rpm 包安装的，当然不能反向查询它属于哪个 rpm 包。
>
> `rpm -qf 系统文件名`
>
> 选项：
>
> ​	`-f`：查询系统文件属于哪个软件包（file）

#### 6、rpm 包文件提取

> `rpm2cpio 包全名 |cpio -idv . 文件绝对路径`
>
> ​	`rpm2cpio` ：将 rpm 包转换为 cpio 格式的命令	
>
> ​	`cpio`：是一个标准工具，它用于创建软件档案文件和从档案文件中提取文件

```shell
rpm2cpio /mnt/cdrom/Packages/httpd-2.2.15-53.el6.centos.x86_64.rpm | cpio -idv . /etc/httpd/conf/httpd.conf

# 上面的命令意思就是从 httpd-2.2.15-53.el6.centos.x86_64.rpm 这个包中，提取出 httpd.conf 这个文件到当前目录下
```

#### 7、yum 源搭建

yum 安装又叫 rpm包 在线安装，它是 rpm 包安装的一种方式。

**1）、yum 源 文件解析**

> yum 源配置文件保存在 `/etc/yum.repos.d/` 目录中，文件的扩展名一定是 `「*.repo」`。也就是说，yum 源配置文件只要扩展名是 `「*.repo」` 就会生效

```shell
[root@hepingfly yum.repos.d]# ls
CentOS-Base.repo  CentOS-Debuginfo.repo  CentOS-fasttrack.repo  CentOS-Media.repo  CentOS-Vault.repo 
```

这个目录中有 5 个 yum 源配置文件，默认情况下 `CentOS-Base.repo` 文件生效。 

```shell
vim CentOS-Base.repo

[base]
name=CentOS-$releasever - Base
mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
#baseurl=http://mirror.centos.org/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
```

> - `[base]` ：  容器名称
> - `name` ：容器说明，可以自己随便写
> - `mirrorlist` ：镜像站点，这个可以注释掉
> - `baseurl` ：我们的 yum 源服务器地址。默认是 CentOS 官方的 yum 源服务器，是可以使用的。如果你觉得慢，是可以改成你喜欢的 yum 地址
> - `enabled` ：此容器是否生效，如果不写或者写成 enabled=1 表示此容器生效，写成 enabled=0 表示此容器不生效
> - `gpgcheck` ：如果为 1 表示 rpm 的数字证书生效，如果为 0 表示 rpm 的数字证书不生效
> - `gpgkey` ：数字证书的公钥文件保存位置。不用修改。

**2）、搭建本地 yum 源**

① 放入 CentOS 安装光盘，并挂载光盘到指定位置

```shell
mkdir /mnt/cdrom/
mount /dev/sr0/ /mnt/cdrom/
```

② 修改其他几个 yum 源配置文件的扩展名，让它们失效，因为只有扩展名是 `「*.repo」` 的文件才能作为 yum 源配置文件。当然也可以删除其它几个 yum 源配置文件，但是如果删除了，要是你又想用网络 yum 源的时候，就没有了参考文件，所以最好还是修改扩展名。

```shell
cd /etc/yum.repos.d
mv CentOS-Base.repo CentOS-Base.repo.bak
mv CentOS-Vault.repo CentOS-Vault.repo.bak
mv CentOS-Debuginfo.repo CentOS-Debuginfo.repo.bak
```

③ 修改光盘 yum 源配置文件

```shell
[root@hepingfly yum.repos.d]# vim  CentOS-Media.repo

[c6-media]
name=CentOS-$releasever - Media
baseurl=file:///mnt/cdrom/              #地址为你自己的光盘挂载地址
#        file:///media/cdrom/			注释这两个不存在的地址
#        file:///media/cdrecorder/
gpgcheck=1
enabled=1               # 把 enabled 的值从 0 改成 1 ，让这个 yum 源配置文件生效
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

```

#### 8、yum 命令

**1）、查询**

① 查询 yum 源服务器上所有可安装的软件包列表

> `yum list`

② 查询 yum 源服务器是否包含某个软件包

> `yum list 包名`

```shell
[root@hepingfly 桌面]# yum list samba
可安装的软件包
samba.x86_64                                  3.6.23-51.el6                             base
```

③ 搜索 yum 源服务器上所有和关键字相关的软件包

> `yum search 关键字`

```shell
[root@hepingfly ~]# yum search ifconfig
knemo.x86_64 : A KDE network monitoring tool
moreutils.x86_64 : Additional unix utilities
net-tools.x86_64 : Basic networking tools

# yum search 搜索可以用于确定某个软件在哪个相关包中。上面的命令可以确定 "ifconfig" 命令需要安装 net-tools.x86_64  包
```

④ 查询指定软件包的信息

> `yum info 包名`

```shell
[root@hepingfly ~]# yum info telnet

可安装的软件包								   # 还没有安装
Name        : telnet						# 包名
Arch        : x86_64						# 适合的硬件平台
Version     : 0.17							# 版本
Release     : 48.el6						# 发布版本
Size        : 58 k							# 大小
Repo        : base							# yum 源容器名
```

**注：**

> 之前我们说了一个 包名 和 包全名的概念，这个两个仅仅针对 rpm 包手动安装来说，yum 安装没有包名和包全名之分，通通都是 包名

**2）、安装**

> `yum -y install 包名`
>
> 选项：
>
> ​	`install` ：安装
>
> ​	`-y` ：自动回答 yes。如果不加 -y 那么每个安装的软件都需要手工回答 yes

```shell
[root@hepingfly ~]# yum install -y httpd-devel
# 使用 yum 自动安装 Apache

[root@hepingfly httpd-2.2.9]# yum -y install gcc
# 使用 yum 安装 gcc
```

**3）、升级**

> `yum -y update 包名`
>
> 选项：
>
> ​	`update` ：升级
>
> ​	`-y` ：自动回答 yes

注：

在进行升级操作时， yum 源服务器中软件包的版本要比本机安装的软件包版本高

```shell
[root@hepingfly ~]# yum update -y
# 升级本机中所有软件包
# 这条命令会升级系统中所有的软件包，包括内核都会升级，不过我们的生成服务器是稳定优先的，所以这种全系统升级的情况并不多见
```

**4）、卸载**

> `yum remove 包名`

注意：

> 再次强调一下，除非你确定你卸载的软件的依赖包不会对系统产生影响，否则不要执行 yum 的卸载。因为很有可能在卸载软件包的同时卸载的依赖包也是重要的系统文件，这就有可能导致系统崩溃。
>
> 比如：  a   -> b -> c  你这时候卸载 b 软件包，因为 a 软件包依赖 b 软件包，所以卸载 b 的时候会先把 a 给卸载了，但假如某个系统文件依赖了 a 包，这时候  a  包又被你卸载了，就会造成系统崩溃。

```shell
[root@hepingfly ~]# yum remove samba
# 卸载 samba 软件包
# 如果你明确你要卸载的软件包，不会对系统产生什么影响，当然可以随意卸载
```

#### 9、yum 组管理命令

**1）、查询可以安装的软件组**

> `yum grouplist`

```shell
[root@hepingfly ~]# yum grouplist
# 列出所有可用的软件组列表
```

**2）、查询软件组内包含的软件**

> `yum groupinfo 软件组名`

```shell
[root@hepingfly ~]# yum groupinfo 英国英语支持

组：英国英语支持
 Language: en_GB
 可能的软件包：
   kde-i18n-British
   kde-l10n-British
```

**3）、安装软件组**

> `yum groupinstall 软件组名`

```shell
[root@hepingfly ~]# yum -y groupinstall 英国英语支持
# 安装指定的软件组，组名可由 grouplist 查询出来
```

**4）、卸载软件组**

> `yum groupremove 软件组名`

```shell
[root@hepingfly ~]# yum -y groupremove 英国英语支持
# 卸载指定软件组
```

#### 10、源码包安装

前面我们说过了，linux 下只有两种软件包，一种是二进制包，一种是源码包。二进制包也就是 redhat 系列中的 rpm 包。其中 rpm 包分为两种安装方法，一种手工安装，一种在线安装（yum 安装）

应该选择哪种安装包：

> - 如果软件包是给大量客户提供访问，建议使用源码包安装，因为源码包效率更高
> - 如果软件包是给 Linux 底层使用，或只给少量客户访问，建议使用 rpm 包安装，因为 rpm 包在线安装简单。

**安装过程：**

> 1、下载软件包
>
> 2、解压缩
>
> 3、进入解压目录
>
> 4、`./configure`    编译前准备
>
> - 这一步主要有三个作用：
>   - 在安装之前需要检测系统环境是否符合安装要求
>   - 定义需要的功能选项。 `"./configure"` 支持的功能选项较多，可以执行 `"./configure --help"` 命令查询其支持的功能。一般会通过 `./configure --prefix=安装路径` 来指定安装路径
>   - 把系统环境的检测结果和定义好的功能选项写入 Makefile 文件，后续的编译和安装需要依赖这个文件的内容
>
> 5、`make`       编译
>
> - `make` 会调用 gcc 编译器，并读取 Makefile 文件中的信息进行系统软件编译。编译的目的就是把源码程序转变为能被 Linux 识别的可执行文件，这些可执行文件保存在当前目录下。编译过程较为耗时，需要有耐心。
>
> 6、`make clean`     清空编译内容
>
> - 如果在 `./configure` 或 `make` 编译中报错，那么我们在重新执行命令前一定要记得执行 `make clean` 命令，它会清空 Makefile 文件或编译产生的 ".o" 头文件
>
> 7、`make install`        编译安装
>
> - 这才是真正的安装过程，安装的同时会往你的安装目录写文件。所以如果这条命令执行报错，需要执行 make clean 命令，然后删除安装目录，重新安装。 

注意：

`configure` 不是系统命令，而是源码包软件自带的一个脚本程序，所以必须采用 `./configure` 方式执行

#### 11、比较两个文件不同

> diff 命令用来查看两个文件的不同
>
> `diff 选项 old new`
>
> 选项：
>
> ​	`-a`：将任何文档当做文本文档处理
>
> ​	`-b`：忽略空格造成的不同
>
> ​	`-B`：忽略空白行造成的不同
>
> ​	`-I`：忽略大小写造成的不同
>
> ​	`-N`：当比较两个目录时，如果某个文件只在一个目录中，则在另一个目录找那个视做空文件
>
> ​	`-r`：当比较目录时，递归比较子目录
>
> ​	`-u`：使用统一的输出格式

```shell
diff -Naur /root/test/old.txt /root/test/new.txt > pat.txt
# 上面命令的意思是，比较两个文件的不同，并生成补丁文件
```

==小技巧：==

> Xshell  自带了一个上传下载的工具，使用之前你需要安装以下这个软件包
>
> 方式一：
>
> 去网上搜索 `lrzsz-0.12.20-27.1.el6.x86_64.rpm`  这个软件包，然后使用 `rpm -ivh lrzsz-0.12.20-27.1.el6.x86_64.rpm` 安装该软件包
>
> 方式二：
>
> 使用 `yum install -y lrzsz` 即可
>
> 使用：
>
> rz 是上传(receive，视角都站在服务器端，服务器接收，即通常我们说的上传)
>
> sz 是下载（send，视角都站在服务器端，服务器往外发送，即通常我们说的下载）

### 十五、用户管理

#### 1、用户相关文件

**1）、用户信息文件**

文件名：『`/etc/passwd` 』

> `hepingfly:x:500:500::/home/hepingfly:/bin/bash`
>
> - 第一列：用户名
>
> - 第二列：密码位。这里的密码仅仅做个占位，存放的并不是真正的密码。真正的密码存放在『 `/etc/shadow` 』中，为什么不直接把密码存在另一个文件中呢？
>
>   - ```shell
>     [root@hepingfly 桌面]# ll /etc/passwd
>     -rw-r--r--. 1 root root 1686 2月  20 23:24 /etc/passwd
>     [root@hepingfly 桌面]# ll /etc/shadow
>     ----------. 1 root root 1062 2月  20 23:24 /etc/shadow
>     ```
>
>   - 我们会发现『 `/etc/shadow` 』 的权限位是 000 也就是说除了 root 用户，别的所有用户都没办法查看，安全性更高
>
> - 第三列：用户 id
>
>   - 0       超级用户 UID 。如果用户 UID 为 0 表示这个账号是管理员账号。那 Linux 中如何把普通用户升级为管理员呢？就是把其他用户的 UID 改为 0 就可以了，这点和 Windows 是不同的。不过不建立设立多个管理员账号。
>   - 1-499      系统用户（伪用户） UID 。这些 UID 是系统保留给系统用户的 UID。也就是说 UID 在 1-499 范围内的用户是不能登录系统的，而是用来运行系统或服务的。其中 1-99 是系统保留的账号，系统自动创建。100-499 是预留给用户创建系统账号的。
>   - 500-65535      普通用户 UID 。建立的普通用户 UID 从 500 开始，最大到 65535。这些用户已经够用了，但是如果不够也不用担心，2.6.x 内核以后的 Linux 系统用户 UID 已经可以支持 2^32^  这么多了
>
> - 第四列：  组 id      GID 添加用户时，如果不指定用户所属的初始组，那么会建立和用户名相同的组
>
> - 第五列： 用户说明
>
> - 第六列：用户家目录
>
> - 第七列：登录 shell         登录后用哪种解析器去执行

==如何把普通用户变成超级用户：把用户的 UID 改成 0==

**2）、影子文件**

文件名：『`/etc/shadow` 』

> `hepingfly:$6$qN8J4Wbo$PcGKJy3Bg0yTmj5zgozXQ7Ui2C8hFbmeuk3kPO2ux5R0LTOnj/9lXOOUIZEC4by/8WwR4A0s985NnN9qzYC5S0:17947:0:99999:7:::` 
>
> - 第一列：用户名
> - 第二列：加密密码。
>   - 我们可以在密码前人为的加入 『`!`』  或 『`*`』改变加密值让密码暂时失效，使这个用户无法登陆，达到暂时禁止用户登录的效果。（其实不一定非要加 『`!`』  或 『`*`』 加任意一个字符都行，这样它密码加密值就变了，用户肯定就登不进去了，但是我们约定俗称一般在前面加 『`!`』  或 『`*`』）
>   - 所有伪用户的密码都是 『`!!`』  或 『`**`』，代表是没有密码不能登录的。
> - 第三列：密码最近修改时间。时间戳。
> - 第四列：两次修改密码的间隔时间（以第三列时间为开始）。默认是 0 如果不是 0 ，要过了你设置的时间才能修改密码。单位是 天。比如说你设置成了 2，那么意思就是把第三列时间戳转出时间在这个时间后的两天内都不能修改密码
> - 第五列：密码有效期（以第三列时间为开始）。假如你设置成 180，那么意思就是 密码 180天内有效，相比于第三列的时间
> - 第六列：密码修改到期前的警告天数（和第五列相比）。默认是 7 。比如说你第五列设置的是 180 那么到了第 173天，你每一次登录就会提醒你密码快到期了，修改密码
> - 第七列：密码过期后的宽限天数（和第五列相比）。你可以把它写成 0 那么密码到期后这个用户就被禁用了
> - 第八列：密码失效时间。这里同样要写时间戳，如果超过失效时间，就算密码没有过期，用户也会失效无法使用了。
> - 第九列：保留。

**3）、组信息文件**

文件名：『`/etc/group` 』

> `hepingfly:x:500:`
>
> - 第一列：组名。
> - 第二列：组密码位
> - 第三列：组 ID
> - 第四列：此组中支持的其他用户。附加组是此组的用户

**初始组：**

每个用户初始组只能有一个。一般都是和用户名相同的组作为初始组。

**附加组：**

每个用户可以属于多个附加组。要把用户加入组，都是加入附加组。

**说一个目录**

> 『`/etc/skel` 』
>
> 这个是用户的模板目录。
>
> 解释下：
>
> 就是你创建一个用户，会在 home 目录下生成一个同名的目录
>
> ```shell
> ls -a
>  .bash_logout  .bash_profile  .bashrc  .gnome2  .mozilla
>  
>  # 会发现有这么多自动生成的隐藏文件
>  
>  ls -a /etc/skel/
>  .bash_logout  .bash_profile  .bashrc  .gnome2  .mozilla
>  # 发现这个目录下也有隐藏文件，上面的隐藏文件是根据这个模板来生成的，如果你在这个目录下，在加一个文件，那么你新建一个用户，在用户的家目录也会生成对应的文件 
>  
> ```
>
> 

**手工删除用户：**

> /etc/passwd
>
> /etc/shadow
>
> /etc/group
>
> /etc/gshadow
>
> /home/user1
>
> /var/spool/mail/user1
>
> 手工删除用户只要把以上六个地方的用户信息删除即可，也间接证明了创建用户其实就是往这六个地方写信息。

#### 2、添加用户

> `useradd [选项] 用户名`
>
> 选项：
>
> ​	`-g 组名`：指定初始组，这个不需要手工指定
>
> ​	`-G 组名`：指定附加组，把用户加入组，使用附加组

**注意**

==添加完用户后记得设置密码，否则这个用户是不能登录的==

**useradd 默认值**

useradd 这个命令，如果不加选项，你创建一个用户会给你指定默认值。useradd 添加用户时参考的默认值文件主要由两个，分别是 『`/etc/default/useradd`  』  和  『 `/etc/login.defs` 』

①   『`/etc/default/useradd`  』  文件

```shell
# useradd defaults file
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel
CREATE_MAIL_SPOOL=yes
```

> - GROUP=100
>   - 这个选项是建立用户的默认组，也就是说添加每个用户时，用户的初始组的 GID 就是 100 这个用户组。但是我们目前采用的是私有用户组机制，就是说你建一个用户，默认初始组就是和你用户同名的组。也就是说这个选项现在不起作用，可以不用管
> - HOME=/home
>   - 用户家目录的默认位置，所有新建用户的家目录都默认在 /home 目录下
> - INACTIVE=-1
>   - 这个选项就是密码过期后的宽限天数，也就是 `/etc/shadow` 文件的第七个字段。如果是天数，比如说是 10，代表密码过期后 10 天后失效，如果是 0 ，代表密码过期后立即失效。如果是 -1 ，代表密码永远不会失效。这里默认值是 -1 ，所以所有新建用户的密码都不会失效
> - EXPIRE
>   - 这个值默认是空，不太常用
> - SHELL
>   - 指定用户默认的 shell
> - SKEL=/etc/skel
>   - 这个选项定义用户的模板目录位置。/etc/skel 目录中的文件都会复制到新建用户的家目录中
> - CREATE_MAIL_SPOOL=yes
>   - 这个选项定义是否给新建用户建立邮箱，默认是创建，也就是说所有的新建用户系统都会新建一个邮箱，放在 /var/spool/mail 中

② 『 `/etc/login.defs` 』文件

```shell
MAIL_DIR        /var/spool/mail

PASS_MAX_DAYS   99999
PASS_MIN_DAYS   0
PASS_MIN_LEN    5
PASS_WARN_AGE   7

UID_MIN                   500
UID_MAX                 60000

GID_MIN                   500
GID_MAX                 60000

CREATE_HOME     yes
UMASK           077
USERGROUPS_ENAB yes
ENCRYPT_METHOD SHA512
```

> - MAIL_DIR
>   - 这行指定新建用户的默认邮箱位置
> - PASS_MAX_DAYS 
>   - 这行指定的是密码的有效期，也就是 /etc/shadow 文件的第五字段。代表多少天后必须修改密码，默认值是 99999
> - PASS_MIN_DAYS
>   - 这行指定的是两次密码的修改间隔时间，也就是 /etc/shadow 文件的第四字段。代表第一次修改密码之后，几天后才能再次修改密码。默认值是 0
> - PASS_MIN_LEN 
>   - 这个选项现在不生效，不用管
> - PASS_WARN_AGE
>   - 这行代表密码修改到期前的警告天数，也就是 /etc/shadow 文件的第六字段。代表密码有效期前多少天前开始警告提醒，默认值是 7 天
> - UID_MIN   UID_MAX
>   - 这个指定的是  UID 的范围
> - GID_MIN    GID_MAX
>   - 这个指定的是 GID 的范围
> - CREATE_HOME
>   - 这行指定建立用户时是否自动建立用户的家目录，默认是建立
> - UMASK  
>   - 这行指定的是建立用户家目录的默认权限，因为  umask 值是 077 所以新建的用户的家目录的权限是 700
> - USERGROUPS_ENAB 
>   - 这行指定的是使用命令 userdel 删除用户时，是否删除用户的初始组，默认是删除
> - ENCRYPT_METHOD
>   - 这行是指定 linux 用户密码的加密方式

#### 3、修改用户信息

**1）、设定密码**

> `passwd [选项] 用户名`
>
> 选项：
>
> ​	`-l`：暂时锁定用户。仅 root 用户可用
>
> ​	`-u`：解锁用户。仅 root 用户可用
>
> ​	`—-stdin` ：可以将通过管道输出的数据作为用户的密码

```shell
echo "123" | passwd --stdin hepingfly
# 更改用户 hepingfly 的密码


--------------------------------------
passwd
# passwd 直接回车代表修改当前用户的密码(普通用户用 passwd 修改自己的密码，密码不能设置的太简单，否则不生效)

---------------------------------------
passwd -l Jimmy
# 这样就把 Jimmy 这个用户给锁定起来了(这个的原理其实就是把 /etc/shadow 下这个用户的密码前面加两个感叹号，让这个用户登录不进去)
passwd -u Jimmy
# 这样就把 Jimmy 这个用户解锁 (这个的原理其实就是把 /etc/shadow 下这个用户的密码前面两个感叹号去掉，让这个用户登录进去)
```

#### 4、删除用户和切换用户

**1）、删除用户**

> `userdel [-r] 用户名`
>
> 选项：
>
> ​	`-r`：在删除用户的同时，删除用户的家目录

**2）、切换用户**

> `su [选项] 用户名`
>
> 选项：
>
> ​	`-` ：使用 "-" 表示带用户的环境变量一起切换

```shell
[root@hepingfly home]# su hepingfly
[hepingfly@hepingfly home]$ env
USERNAME=root       # root 用户切换为 hepingfly 用户，如果不加 - 你会发现 USERNAME 还是 root

su - hepingfly
# 使用这个命令就会连带环境变量一起切过来
```

#### 5、组管理命令

1）、添加用户组

> `groupadd 组名`

2）、删除用户组

> `groupdel 组名`

3）、把用户添加进组或从组中删除

> `gpasswd [选项] 组名`
>
> 选项：
>
> ​	`-a  用户名`：把用户加入组
>
> ​	`-d  用户名`：把用户从组中删除

### 十六、权限管理

#### 1、ACL 概述

ACL 用于解决用户对文件身份不足的问题。它的基本思路就是把用户拿出来，不再考虑它是文件的所有者还是所属组，直接赋予权限使用

#### 2、ACL 使用

1）、开启 ACL

> `dumpe2fs -h /dev/sda2`
>
> `dumpe2fs`  命令是查询指定分区详细文件系统信息的命令
>
> 选项：
>
> ​	`-h`：仅显示超级快中信息，而不显示磁盘块组的详细信息

```shell
Default mount options:    user_xattr acl    # 看到这个表明 ACL 是开启的
```

==ACL 默认是开启的==

如果没有开启，手动开启分区的 ACL 权限

> `mount -o remount,acl /`
>
> 重新挂载根分区，并挂载假如 ACL 权限

2）、ACL 基本命令

> `getfacl 文件名`             查询文件的 acl 权限
>
> `setfacl  选项  文件名`          设定 acl 权限
>
> 选项：
>
> ​	`-m`：设定 ACL 权限
>
> ​	`-b`：删除 ACL 权限
>
> ​	``-x：用户`        删除单个用户的 ACL 权限

> `setfacl -m u:用户名:权限 文件名`
>
> `setfacl -m g:组名:权限 文件名`

```shell
setfacl -m u:Jimmy:4 /home/hepingfly/test/     # 这个就表示给 test 目录赋予 Jimmy 这个用户读的权限

setfacl -m u:Jimmy:5 -R /home/hepingfly/test/    # 赋予递归 ACL 权限，只能赋予目录
```

**ACL 默认权限**

```shell
setfacl -m d:u:Jimmy:5 -R /home/hepingfly/test/    # 在原有命令基础上加一个 d:    表示 default
```

**注意：**

如果给目录赋予 ACL 权限，下面两条命令都要输入

```shell
# 递归与默认的区别：
setfacl -m u:Jimmy:5 -R /home/hepingfly/test/     # 这条命令只对已经存在的文件生效
setfacl -m d:u:Jimmy:5 -R /home/hepingfly/test/   # 这条命令只对以后新建的文件生效

#第一个：比如说你 test 目录下原来有一些文件，然后你执行了上面第一个命令，那么这些文件就具备的 ACL 权限，但是此时你如果新建一个文件，这个新建文件时不具备 ACL 权限的
# 第二个：比如说你 test 目录下原来有一些文件，这些文件还没有 ACL 权限，这时候你执行第二条命令，那么以后新建的文件都会具备 ACL 权限，但是之前的文件还是没有 ACL 权限
# 因此需要两个命令一起使用
```

**删除 ACL 权限：**

```shell
setfacl -x u:Jimmy /home/hepingfly/test/        # 删除指定用户的 ACL 权限
setfacl -b /home/hepingfly/test/     # 删除文件的所有 ACL 权限
```

#### 3、sudo 权限

sudo 授权

给普通用户赋予部分管理员权限

**sudo 有个原则：**

> - 赋予的权限越详细，普通用户得到的权限就越小
> - 赋予的权限越简单，普通用户得到的权限越大

使用 root 身份，执行 `visudo` 

```shell
root    ALL=(ALL)       ALL
# 用户名   被管理主机的地址=(可使用的身份)   授权命令(要写绝对路径)

# %wheel        ALL=(ALL)       ALL
# %组名   被管理主机的地址=(可使用的身份)   授权命令(要写绝对路径)
```

> - 用户名/组名：代表 root 给哪个用户或用户组赋予命令，注意组名前加 "%"
> - 被管理主机的地址：用户可以用指定的命令管理指定 ip 地址的服务器。如果写 ALL 代表可以管理任何主机。如果写固定 IP ，代表用户可以管理指定的服务器
> - 可使用的身份：就是把来源用户切换成什么用户使用，（ALL）代表可以切换成任意身份。这个字段可以省略
> - 授权命令：代表 root 把什么命令授权给普通用户。默认是 ALL ，代表任何命令，这个当然不行（这样这个用户就变成 root 用户了）。如果需要给哪个命令授权，写入命令名即可，不过需要注意，一定要写成绝对路径

```shell
visudo
Jimmy ALL=/sbin/shutdown -r now    # 授权 Jimmy 用户可以使用 shutdown -r now 命令

sudo -l     # 查看可用的授权
```

授权 Jimmy 用户可以添加其他普通用户：

```shell
Jimmy ALL=/usr/sbin/useradd       # 赋予 Jimmy 添加用户权限。命令必须写入绝对路径
Jimmy ALL=/usr/bin/passwd        # 赋予修改密码权限，取消对 root 的密码修改
Jimmy ALL=/usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd "", !/usr/bin/passwd root
```

#### 4、SUID 权限

SetUID 是什么（简称 SUID）？

> SetUID 的功能可以这么理解：
>
> - 只有可以执行的二进制程序才能设定 SUID 权限
> - 命令执行者要对该程序拥有 x（执行）权限
> - 命令执行者在执行该程序时获得该程序文件属主的身份（在执行程序的过程中灵魂附体为文件的属主）
> - SetUID 权限只在该程序执行过程中有效，也就是说身份改变只在程序执行过程中有效

```shell
[root@hepingfly 桌面]# ll /usr/bin/passwd 
-rwsr-xr-x. 1 root root 30768 11月 24 2015 /usr/bin/passwd

# 你会发现上面所属用户的最后一个权限是 s ，这就代表这个文件具有 suid 权限，假如这个 s 出现在所属组的位置，就代表这个文件具有 sgid 权限，假如 t 出现在其他用户的位置，就表示这个文件具有 sbit 权限
```



```shell
[root@hepingfly 桌面]# ll /etc/passwd
-rw-r--r--. 1 root root 1714 4月   8 23:14 /etc/passwd
[root@hepingfly 桌面]# ll /etc/shadow
----------. 1 root root 1181 4月   8 23:14 /etc/shadow
[root@hepingfly 桌面]# ll /usr/bin/passwd 
-rwsr-xr-x. 1 root root 30768 11月 24 2015 /usr/bin/passwd

# 思考一个问题，我们说普通用户是可以修改自己的密码的，修改密码最终我们说是写到 /etc/shadow 这个文件中的，但是上面这个文件的权限是 000 ，它是怎么写进去的呢？

# 原因就在于修改密码的命令 /usr/bin/passwd 这个命令，你会发现它所有者上面有个 s 就表示它具有 suid 权限，那么为什么要给 /usr/bin/passwd 这个文件 suid 权限呢？目的就是为了可以让普通用户设置密码，普通用户在使用 /usr/bin/passwd 这个命令的时候可以去操作 /etc/shadow 文件
```

解释一下上面第一句话：

给文件赋 SUID 权限：

```shell
[root@hepingfly hepingfly]# touch abc
[root@hepingfly hepingfly]# ll abc 
-rw-r--r--. 1 root root 0 4月  14 22:52 abc
[root@hepingfly hepingfly]# chmod u+s abc             直接使用 u+s 就可以
[root@hepingfly hepingfly]# ll abc
-rwSr--r--. 1 root root 0 4月  14 22:52 abc

# 但是我们会发现上面的是 S ，这个 S 表示报错，说明你这个文件的权限有问题，因为我们说只有可以执行的程序才能设置 suid 权限，前提就是这个文件要有执行权限，这个 abc 文件时没有执行权限的，你给它设置 suid 权限，所以是 S 是有问题的(也可以这么理解：如果一个文件有 s 权限，那么它肯定有 x 权限。s = S + x)

# 正确应该这么操作：
[root@hepingfly hepingfly]# chmod 755 abc 
[root@hepingfly hepingfly]# ll abc
-rwxr-xr-x. 1 root root 0 4月  14 22:52 abc
[root@hepingfly hepingfly]# chmod u+s abc 
[root@hepingfly hepingfly]# ll abc 
-rwsr-xr-x. 1 root root 0 4月  14 22:52 abc
```

解释下上面第二句话：

```shell
[root@hepingfly hepingfly]# ll /usr/bin/passwd 
-rwsr-xr-x. 1 root root 30768 11月 24 2015 /usr/bin/passwd
[root@hepingfly hepingfly]# ll /etc/shadow
----------. 1 root root 1181 4月   8 23:14 /etc/shadow

# 你会发现 /usr/bin/passwd 这个命令的所属用户和所属组都是 root ，假如这时候我 Jimmy 用户想去改自己的密码，那么就会用到 /usr/bin/passwd 这条命令，相对于这条命令，我 Jimmy 用户就是其他人，会发现这条命令其他人也有执行权限
# 总之，你命令执行者要对这条命令有执行权限 suid 权限才会生效
```

解释下上面第三句话：

```shell
# 命令执行者在执行该程序时，获得该程序文件所有者身份，意思就是：
[root@hepingfly hepingfly]# ll /usr/bin/passwd 
-rwsr-xr-x. 1 root root 30768 11月 24 2015 /usr/bin/passwd
[root@hepingfly hepingfly]# ll /etc/shadow
----------. 1 root root 1181 4月   8 23:14 /etc/shadow

# 我 Jimmy 用户本来对 /etc/shadow 这个文件是没有权限操作的，但是因为 /usr/bin/passwd 这个命令是有 suid 权限的，所以当 Jimmy 用户执行 /usr/bin/passwd 这个命令的时候，它的身份会从 Jimmy 变成 /usr/bin/passwd 这个命令的所有者，也就是 root，root 用户时对 /etc/shadow 这个文件有操作权限的，因此 Jimmy 用户对这个文件也就有了操作权限
```

**危险的 SetUID:**

```shell
[root@hepingfly 桌面]# ll /usr/bin/vim
-rwxr-xr-x. 1 root root 2283960 7月  24 2015 /usr/bin/vim
[root@hepingfly 桌面]# chmod u+s /usr/bin/vim
[root@hepingfly 桌面]# ll /usr/bin/vim
-rwsr-xr-x. 1 root root 2283960 7月  24 2015 /usr/bin/vim

# 解释一下上面的，首先我 vim 命令是有执行权限的，这时候我给 vim 命令设置 suid 权限，那么就意味着我任何用户在执行 vim 命令的时候，身份都会变成 vim 命令的所属主用户，也就是 root 用户，那这权限就大了，比如说，/etc/shadow 文件普通用户时没有权限修改的，但是你一旦给 vim 设置 suid 权限，普通用户在执行 vim 命令的时候身份就变成了 root 用户，这时候就可以随意修改  /etc/shadow 文件了
```

**注意：**

==SUID 权限只能针对执行程序，也就是说一个文件只有有执行权限才能被赋予 SUID 权限==

#### 5、SGID 权限

1）、针对文件的作用

SGID 既可以针对文件生效，也可以针对目录生效，这和 SUID 明显不同。如果针对文件，SGID 的含义如下：

> - 只有可执行的二进制程序才能设置 SGID 权限
> - 命令执行者要对该程序拥有 x （执行）权限
> - 命令在执行程序的时候，组身份升级为该程序文件的所属组
> - SGID 权限同样只在该程序执行过程中有效，也就是说组身份改变只在程序执行过程中有效

```shell
[root@hepingfly hepingfly]# locate shp
/home/hepingfly/shp
[root@hepingfly hepingfly]# ll /var/lib/mlocate/mlocate.db 
-rw-r-----. 1 root slocate 2402222 4月  15 22:56 /var/lib/mlocate/mlocate.db

# 我们知道 locate 命令，是根据文件名去搜索文件，但是它搜索的实际上是一个数据库，根据数据库索引再去找文件，数据库文件位置是 /var/lib/mlocate/mlocate.db  但是我们发现这个文件对普通用户的权限是 0(该文件其他用户的权限是 0)，但是我们发现普通用户也是可以使用 locate 命令去搜索的，那么既然可以使用 locate 命令，就必定会访问这个数据库文件去找索引，但是这个数据库文件对普通用户又没有权限，那是普通用户时怎么做到可以访问这个文件呢？

# 下面来解释下：
[root@hepingfly hepingfly]# ll /usr/bin/locate 
-rwx--s--x. 1 root slocate 35840 3月  12 2015 /usr/bin/locate

# 我们发现 /usr/bin/locate 这个命令是有 sgid 权限的，那么就意味着普通用户在执行 /usr/bin/locate 这条命令的时候，所属组就会变成 「slocate」，而 /var/lib/mlocate/mlocate.db 这个文件的所属组对这个文件是具有读权限的，因此普通用户在执行 /usr/bin/locate 这条命令的时候可以对这个数据库文件进行读取

```

2）、针对目录的作用

如果 SGID 针对目录设置，含义如下：

> - 普通用户必须对此目录拥有 r 和 x 的权限，才能进入此目录
> - 普通用户在此目录中的有效组会变成此目录的所属组
> - 若普通用户对此目录拥有 w 权限时，新建的文件的默认所属组是这个目录的所属组

```shell
[root@hepingfly hepingfly]# ll
总用量 4
drwxr-xr-x. 2 root root 4096 4月  16 21:49 test      # 有个 test 文件夹，所有者和所属组都是 root

[root@hepingfly hepingfly]# chmod 777 test/   给 test 文件夹赋 777 权限，目的是为了让普通用户(其他人)拥有在这个文件夹下的 w 权限
[root@hepingfly hepingfly]# ll
总用量 4
drwxrwxrwx. 2 root root 4096 4月  16 21:49 test

[hepingfly@hepingfly test]$ touch aa       # 我用 hepingfly 这个用户创建了一个文件 aa 
[hepingfly@hepingfly test]$ ll
总用量 0
-rw-rw-r--. 1 hepingfly hepingfly 0 4月  16 22:01 aa   # 发现这个文件的所有者和所属组都是 hepingfly,这是正常的，本来就是 hepingfly 这个用户创建的


[root@hepingfly hepingfly]# chmod g+s test/    现在我用 root 用户给 test 文件夹设置 sgid 权限
[root@hepingfly hepingfly]# ll
总用量 4
drwxrwsrwx. 2 root root 4096 4月  16 22:01 test    # 发现所属组这里有了 sgid 权限

[hepingfly@hepingfly test]$ touch bb   # 现在再使用 hepingfly 这个用户去 test 目录下创建一个 bb 文件，发现它的所属组变成了 root，因为 test 文件夹具有 sgid 权限，那么如果普通用户对这个文件夹有 w 权限，在这个文件夹下新建的文件所属组就会变成这个文件夹的所属组(test 文件夹的所属组)
[hepingfly@hepingfly test]$ ll
总用量 0
-rw-rw-r--. 1 hepingfly hepingfly 0 4月  16 22:01 aa
-rw-rw-r--. 1 hepingfly root      0 4月  16 22:05 bb

```

#### 6、Sticky BIT 权限

Sticky BIT 粘着位，也简称 SBIT。SBIT 目前仅针对目录有效，它的作用如下：

> - 粘着位目前只对目录有效
> - 普通用户对该目录拥有 w 和 x 权限，即普通用户可以在此目录拥有写入权限
> - 如果没有粘着位，因为普通用户拥有 w 权限，所以可以删除此目录下的任何文件，包括其他用户建立的文件。一旦赋予粘着位，除了 root 用户可以删除所有文件，普通用户就算拥有 w 权限，也只能删除自己建立的文件，但是不能删除其他用户建立的文件

```shell
[root@hepingfly hepingfly]# ll -d /tmp/
drwxrwxrwt. 31 root root 4096 4月  16 22:01 /tmp/      # 我们发现 /tmp 目录就具有 sbit 权限

# 结合上面的权限，我们发现 /tmp 目录普通用户(其他人权限)具有 w 的权限，就意味着普通用户可以删除这个目录下的任何文件(按照以前的知识，删文件的前提是要对这个文件所在目录有 w 权限)

[root@hepingfly tmp]# touch abc       用 root 用户新建一个文件 abc
[root@hepingfly tmp]# ll /tmp/abc 
-rw-r--r--. 1 root root 0 4月  16 22:32 /tmp/abc 

[hepingfly@hepingfly tmp]$ touch bcd     # 用普通用户新建一个 bcd 文件
[hepingfly@hepingfly tmp]$ ll /tmp/bcd 
-rw-rw-r--. 1 hepingfly hepingfly 0 4月  16 22:37 /tmp/bcd

# 现在我想用普通用户删 abc 文件，既然我普通用户对 /tmp 目录有 w 权限，就意味着我可以删这个目录下任意文件
[hepingfly@hepingfly tmp]$ rm -rf abc
rm: 无法删除"abc": 不允许的操作

# 发现删不了，因为 /tmp 目录具有 sbit 权限，普通用户就算对这个文件夹有 w 权限，也只能删除自己建立的文件而不能删除别人建立的文件(但是 root 用户可以随意删)
```

设定文件特殊权限：

> - 4 代表 SUID
> - 2 代表 SGID
> - 1 代表 SBIT
>
> 或者你可以使用 `u+s`   `g+s`   `o+t`  来设定特殊权限

```shell
chmod 4755 test   # 赋予 SUID 权限
chmod 2755 test   # 赋予 SGID 权限
mkdir test
chmod 1755 test  # SBIT 只对目录有效，建立测试目录，并赋予 SBIT 权限
```

#### 7、chattr 权限

> `chattr [+-=] [选项] 文件或目录名`
>
> 选项：
>
> ​	`+` ：       增加权限 
>
> ​	`-` ：       删除权限
>
> ​	`=` ：       等于某权限
>
> ​	`i` ：      如果对文件设置 i 属性，那么不允许对文件进行删除、改名，也不能添加和修改数据；如果对目录设置 i 属性，那么只能修改目录下文件的数据，但不允许建立和删除文件
>
> ​	`a` ：     如果对文件设置 a 属性，那么只能在文件中增加数据，但是不能删除也不能修改数据；如果对目录设置 a 属性，那么只允许在目录中建立和修改文件，但是不允许删除
>
> ​	`e` ：    linux 中绝对大多文件都默认拥有 e 属性。表示该文件是使用 ext 文件系统进行存储的。

查看文件系统属性：

> lsattr 选项 文件名
>
> 选项：
>
> ​	`-a` ：   显示所有文件和目录
>
> ​	`-d ：`  若目标是目录，仅列出目录本身的属性，而不是子文件的

```shell
[root@hepingfly hepingfly]# touch abc
[root@hepingfly hepingfly]# chattr +i abc      给 abc 这个文件加上不可改变权限，相当于把 abc 的 insert 给锁了，那么这个文件就不给修改、删除
[root@hepingfly hepingfly]# lsattr abc           查看 abc 的权限
----i--------e- abc

[root@hepingfly hepingfly]# mkdir test
[root@hepingfly hepingfly]# chattr +i test/    给目录添加一个不可改变权限，相当于把目录的 insert 权限给锁了，那么这个目录就不能新建和删除文件，但是对目录下的文件不影响，文件还是可以修改的(我们锁的是目录，不是目录下的子文件)
[root@hepingfly hepingfly]# lsattr -d test/      查看目录权限
----i--------e- test/

```

注意：

> 如果你给文件设置了  a 属性，我们说只能在文件中增加数据，但是不能修改数据。增加数据的话，你不能使用 vim 去增加，因为 vim 是一个编辑器，它能增加能修改，系统无法判断你是增加了还是修改了。可以使用 echo 往文件里面追加。

### 十七、文件系统管理

#### 1、文件系统介绍

1）、Linux 文件系统特性

> - super block（超级块）
>   - 记录整个文件系统的信息。包括 block 与 inode 的总量，已经使用的 block 和 inode 的数量，未使用的 block 和 inode 的数量，block 与 inode 的大小，文件系统的挂载时间，最近一次的写入时间等。
> - data block（数据块，也称作 block）
>   - 用来实际保存数据的（柜子的隔板），block 的大小（1kb、2kb、4kb）和数量在格式化后就已经决定，不能改变，除非重新格式化（制作柜子的时候，隔断大小就已经决定，不能更改，除非重新制作柜子）。每个 block 只能保存一个文件数据，要是文件数据小于一个 block 块，那么这个 block 的剩余空间不能被其它文件使用，要是文件数据大于一个 block 块，则占用多个 block 块。Windows 中磁盘碎片整理工具的原理就是把一个文件占用的多个 block 块尽量整理到一起，这样可以加快读写速度。
> - inode（i 节点，柜子门上的标签）
>   - 用来记录文件的权限（r、w、x），文件的所有者和所属组，文件的大小，文件的状态改变时间（ctime），文件的最近一次读取时间（atime），文件的最近一次修改时间（mtime），文件的数据真正保存的 block 编号。每个文件需要占用一个 inode 

#### 2、文件系统命令

① df 命令

> 用来查看硬盘分区空间使用情况
>
> `df -h`

```shell
[root@hepingfly 桌面]# df -hT       加个 -T 参数，结果会多出 Type 这一列，显示分区的文件系统类型
Filesystem     Type     Size  Used Avail Use% Mounted on
/dev/sda2      ext4     9.8G  3.0G  6.3G  33% /
tmpfs          tmpfs    491M   80K  491M   1% /dev/shm
/dev/sda1      ext4     976M   34M  892M   4% /boot
/dev/sda3      ext4     4.8G   11M  4.6G   1% /home
/dev/sr0       iso9660  3.7G  3.7G     0 100% /media/CentOS_6.8_Final
```

② du 命令

> 我们经常用这个命令来查看目录大小，因为用 ll 去查看目录大小不准确，使用 ll 去查看目录带下一般都是 4k 或者 4k 的倍数，因为使用 ll 它统计出来的实际上是，目录中存的所有文件名的大小，一个 block 占 4kb 如果一个 block 存不下就用两个 block 去存，占 8kb。
>
> `du [选项] [目录或文件名]`
>
> 选项：
>
> ​	`-a`：显示每个子文件的磁盘占用量。默认只统计子目录的磁盘占用量。
>
> ​	-h ：人性化显示
>
> ​	-s ：统计总占用量，而不列出子目录和子文件占用量

```shell
[root@hepingfly init]# du -hs /home/
104K	/home/
```

说明：

> du 与 df 的区别：du 是用于统计文件大小的，统计文件大小是准确的，df 是用于统计空间大小的，统计的剩余空间是准确的。
>
> 有时候会存在这两个统计不一致的情况，原因是 df 统计的是空间，临时文件、垃圾文件等都会占用空间，df 就光统计文件大小，所以这两个会存在差异情况。

③ dumpe2fs 命令

显示磁盘状态

> `dumpe2fs /dev/sda3`    

④ 判断文件类型

> file 文件名        判断文件类型
>
> type 命令名       判断命令类型

```shell
[root@hepingfly hgfs]# type mkdir      这个命令是外部命令
mkdir is /bin/mkdir 
[root@hepingfly hgfs]# type cd         这个命令是内置命令
cd is a shell builtin
[root@hepingfly hgfs]# cd /home/hepingfly/
[root@hepingfly hepingfly]# ll
总用量 8
-rw-r--r--. 1 root root    4 4月  18 00:07 abc
-rw-r--r--. 1 root root    0 4月  18 00:06 abc~
drwxr-xr-x. 2 root root 4096 4月  17 23:59 test
[root@hepingfly hepingfly]# file test           表示这是一个目录
test: directory
[root@hepingfly hepingfly]# file abc            表示这是一个文件
abc: ASCII text


# 上面命令，在你写 shell 脚本的时候可能会用到
```

#### 3、手工分区

fdisk 命令

> `fdisk -l`
>
> 查看系统所有硬盘及分区

**使用如下命令进行分区：**

①『  `fdisk /dev/sdb 』` 进行磁盘分区（在开始分区的时候还没有分区号）

> sdb 表示第二块硬盘，我之前只有一块硬盘，现在我新添加一块硬盘，要对这块硬盘进行分区，所以写 /dev/sdb
>
> 注意这个 sdb 后面不能跟数字，因为数字是表示分区号，我现在就是没有分区才要去分区的，如果写分区号，代表都有分区了，那我还分什么区。
>
> 假如你原来有两块硬盘，现在新添加一块硬盘，要对这个硬盘进行分区，那么这里就要写 /dev/sdc 了

你输入上面的命令就会开始分区了，分区的时候会与系统进行交互。

**fdisk 交互指定说明：**

| 命令 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| a    | 设置可引导标记                                               |
| b    | 编辑 bsd 磁盘标签                                            |
| c    | 设置 DOS 操作系统兼容标记                                    |
| d    | **删除一个分区**                                             |
| l    | **显示已知的文件系统类型。82 为 Linux swap 分区，83 为 Linux 分区** |
| m    | 显示帮助菜单                                                 |
| n    | **新建分区**                                                 |
| o    | 建立空白 DOS 分区表                                          |
| p    | **打印分区列表**                                             |
| q    | 不保存退出                                                   |
| s    | 新建空白 SUN 磁盘标签                                        |
| t    | **改变一个分区的系统 ID**                                    |
| u    | 改变显示记录单位                                             |
| v    | 验证分区表                                                   |
| w    | 保存退出                                                     |
| x    | 附加功能                                                     |

②具体分区步骤见：

> <https://console.cloud.tencent.com/cos5/bucket/setting?type=filelist&bucketName=shp-notes-1257820375&path=%252Fshp-linux%252F®ion=ap-chengdu>
>
> 这个里面 1~ 18 个截图，建议把截图弄出来放到 word 中，看更方便

③分区完成之后要进行格式化：

格式化，建立文件系统：

> `mkfs -t ext4 /dev/sdb1`
>
> `mkfs -t ext4 /dev/sdb5`
>
> 说明：
>
> ext4 表示  linux 的文件系统，上面我在分区的时候分了一个主分区，一个扩展分区，一个逻辑分区，扩展分区不能存储数据，也不能格式化，逻辑分区，分区号是从 5 开始的，所以把 sdb1 和  sdb5 格式化后，建立文件系统才能使用

④ 建立挂载点

> `mkdir /disk1`
>
> `mkdir /disk5`
>
> 所有的分区都是要挂载之后才能使用的

⑤ 挂载

> `mount /dev/sdb1 /disk1`
>
> `mount /dev/sdb5 /disk5`

⑥ 查看

> mount        查看所有已挂载的分区和光盘
>
> fdisk -l       查看系统分区
>
> df -h         查看磁盘空间使用情况

#### 4、自动挂载

我们只允许对固定设备做自动挂载，不允许对光盘或者 U 盘等移动硬盘做自动挂载。因为你如果对移动设备做了自动挂载，但是系统启动的时候，你如果忘了插入光盘或者 U 盘，那么系统就会直接崩溃，因为它找不到这个设备。

**如何自动挂载？**

通过修改分区自动挂载文件实现。

> `vim /etc/fstab`        注意：这个文件直接参与系统启动，如果修改错误，系统启动报错 

```shell
vim /etc/fstab
UUID=adec8353-015d-4846-9f00-d4f16630ec3a /                       ext4    defaults        1 1
UUID=1b8318b7-046d-4694-b723-afa92e96888e /boot                   ext4    defaults        1 2
UUID=f20a6373-1d80-4fcc-9d8d-a7c2be91d11f /home                   ext4    defaults        1 2
UUID=cc8c66fd-21d7-42ee-8a6f-3d62c71e09b0 swap                    swap    defaults        0 0
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
```

解释下上面的内容：

> - 第一列：设备文件名
> - 第二列：挂载点
> - 第三列：文件系统
> - 第四列：挂载选项
> - 第五列：是否可以被备份   0 ：不备份   1：每天备份   2：不定期备份（建议写 1）
> - 第六列：是否检测磁盘 fsck     0 : 不检测    1：启动时检测    2：启动后检测（建议写 2）

自动挂载示例：

```shell
vim /etc/fstab
/dev/sdb1               /disk1                  ext4    defaults        1 2
/dev/sdb5               /disk5                  ext4    defaults        1 2


# 这样我就是实现了 sdb1 和 sdb5 这两个分区开机自动挂载
```

**说明：**

> 其实在上面我们也看到了，第一列有 UUID ，其实也可以使用 UUID 去进行挂载。其实使用 UUID 去挂载还会有好处，好处就是当硬盘增加了新的分区，或者分区的顺序改变，再或者内核升级后，仍然能够保证分区能够正确的加载，而不至于造成启动障碍。
>
> 使用 UUID 挂载，只需要把第一列的设备文件名换成查询到的分区 UUID 即可，其他列和使用设备文件名挂载一样。

如何查询分区对应的 UUID：

> `ls -l /dev/disk/by-uuid/`

```shell
[root@hepingfly disk1]# ls -l /dev/disk/by-uuid/
总用量 0
lrwxrwxrwx. 1 root root 10 4月  23 21:30 1b8318b7-046d-4694-b723-afa92e96888e -> ../../sda1
lrwxrwxrwx. 1 root root 10 4月  23 21:30 3731f19b-ecd4-4ac5-ad41-303c894ed742 -> ../../sdb5
lrwxrwxrwx. 1 root root 10 4月  23 21:30 6aedc6ac-d2e1-465e-a824-07b4b9d10ae5 -> ../../sdb1
lrwxrwxrwx. 1 root root 10 4月  23 21:30 adec8353-015d-4846-9f00-d4f16630ec3a -> ../../sda2
lrwxrwxrwx. 1 root root 10 4月  23 21:30 cc8c66fd-21d7-42ee-8a6f-3d62c71e09b0 -> ../../sda5
lrwxrwxrwx. 1 root root 10 4月  23 21:30 f20a6373-1d80-4fcc-9d8d-a7c2be91d11f -> ../../sda3

# 使用上面的命令可以看到每个分区对应的 UUID
```

**测试：**

> `mount -a`        重新挂载所有内容
>
> 再输入 mount 命令，查看是否挂载成功。或者重启也行。

#### 5、parted 命令分区

我们 Linux 系统中有两种常见的分区表，MBR 分区表和 GPT 分区表。

> - MBR 分区表 ：支持最大分区是 2TB ，最多支持 4 个主分区，或 3 个主分区 1 个扩展分区
> - GPT 分区表：支持最大 18EB 的分区（1EB = 1024PB = 1024 * 1024TB），最多支持 128 个分区，其中 1 个系统保留分区，127 个用户自定义分区

一般我们使用 fdisk 命令进行分区的时候，都是对 MBR 分区表进行分区，但是这种分区表，支持的硬盘最大 2TB，也就是说你硬盘容量如果大于 2TB ，使用 MBR 分区表后面的都不会识别了。所以如果你硬盘容量超过 2TB，就需要使用 parted 命令进行分区。另外，你使用 parted 命令分完区之后，就不会存在什么主分区、扩展分区、逻辑分区了，因为我们说之所以存在主分区、扩展分区、逻辑分区，就是因为 MBR 分区表，最多支持 4 个主分区，所以我们想突破 4 个主分区的限制，就弄出了扩展分区和逻辑分区，那么现在 GPT 分区表，已经支持 128 个分区了，所以就不需要这些主分区、扩展分区、逻辑分区了，都是主分区。

### 十八、高级文件系统管理

#### 1、磁盘配额

1）、磁盘配额概念

它是用来限制用户或用户组来使用磁盘空间或文件个数的。

2）、磁盘配额条件

> - 内核必须支持磁盘配额
>
>   - ```shell
>     # 查询内核支不支持磁盘配额方法：
>     [root@hepingfly 桌面]# grep QUOTA /boot/config-2.6.32-642.el6.x86_64 
>     CONFIG_NETFILTER_XT_MATCH_QUOTA=m
>     CONFIG_XFS_QUOTA=y
>     CONFIG_QUOTA=y         # y 说明是支持的
>     CONFIG_QUOTA_NETLINK_INTERFACE=y
>     CONFIG_PRINT_QUOTA_WARNING=y
>     # CONFIG_QUOTA_DEBUG is not set
>     CONFIG_QUOTA_TREE=y
>     CONFIG_QUOTACTL=y
>     # 一般默认情况下都是支持的
>     ```
>
> - 系统中必须安装了 quota 工具，我们的 linux 是默认安装了 quota 工具的
>
>   - ```shell
>     [root@hepingfly 桌面]# rpm -qa |grep quota
>     quota-3.17-23.el6.x86_64
>     ```
>
>   -  如果发现没有安装直接 yum 安装 quota 包就可以了

### 十九、shell 基础

#### 1、shell 概述

1）、什么是 shell

![什么是 shell](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-linux/%E4%BB%80%E4%B9%88%E6%98%AFshell.png?q-sign-algorithm=sha1&q-ak=AKIDlZ0os8J68CnoxEOo5L4E0Si5LczzTuOx&q-sign-time=1556374265;1556376065&q-key-time=1556374265;1556376065&q-header-list=&q-url-param-list=&q-signature=0fc48b56591a7ad5c1a8e068d101f6e71bbdb511&x-cos-security-token=364af8503e4d12734fbfad9cc65dbb7cb019ba8b10001)

你可以把 shell 理解为一个命令解释器，比如说你在命令行输入 cd 命令，linux 怎么知道你输入的 cd 命令是什么意思，所以这时候就需要 shell，它会把你输入的命令翻译成 linux 可以认识的命令，交给 linux 内核去执行，并把内核执行结果翻译成我们可以理解的语言展现给我们。

我们可以通过 `cat /etc/shells` 来查看 linux 支持的 shell

#### 2、echo 命令

> `echo [选项][输出内容]`
>
> 选项：
>
> ​	`-e` ：支持反斜线控制的字符转换
>
> ​	`-n` ：取消输出后行末的换行符号

```shell
[root@hepingfly 桌面]# echo -e "a\tb"
a	b
```

**小知识点：**

echo 输出字符串中含有感叹号会报错，解决方法就是在感叹号后面加一个空格

```shell
[root@hepingfly hepingfly]# echo "aaaa!"        在后面直接写 ! 会报错
bash: !": event not found

# 解决方法：在感叹号后加一个空格即可解决
[root@hepingfly hepingfly]# echo "aaa! "
aaa!
```

#### 3、脚本执行方法

```shell
[root@hepingfly hepingfly]# ll
总用量 8
-rwxr-xr-x. 1 root root   29 4月  28 22:02 hello.sh
drwxr-xr-x. 2 root root 4096 4月  17 23:59 test
[root@hepingfly hepingfly]# ./hello.sh              使用相对路径
hello shp
[root@hepingfly hepingfly]# /home/hepingfly/hello.sh     使用绝对路径
hello shp
[root@hepingfly hepingfly]# bash hello.sh      直接使用 bash 去执行
hello shp

```

直接使用 bash 命令去执行 shell 脚本，甚至都不需要这个脚本有执行权限。

#### 4、Bash 的基本功能

1）、历史命令

> `history [选项] [历史命令保存文件]`
>
> 选项：
>
> ​	`-c` ：清空历史命令
>
> ​	`-w` ：把缓存中的历史命令写入历史命令保存文件。如果不手工指定历史命令保存文件，则放入默认历史命令保存文件 `~/.bash_history` 中 

注：

> 我们使用 history 命令查看历史命令和 `~/.bash_history` 文件中保存的历史命令是不同的。那是因为当前登录操作的命令并没有直接写入 `~/.bash_history` 文件，而是保存在缓存中。需要等当前用户注销之后，缓存中的命令才会写入 `~/.bash_history` 文件。如果我们需要把内存中的命令直接写入 `~/.bash_history` 文件，而不等用户注销才写入，可以使用 `history -w` 命令

设置历史命令保存条数：

> /etc/profile 文件
>
> HISTSIZE=1000           这个值可以调大

历史命令调用:

> - 使用 『`!n`』  重复执行第 n 条历史命令
> - 使用 『`!!`』  重复执行上一条命令

2）、输入输出重定向

① Bash 的标准输入输出

| 设备   | 设备文件名  | 文件描述符 | 类型         |
| ------ | ----------- | ---------- | ------------ |
| 键盘   | /dev/stdin  | 0          | 标准输入     |
| 显示器 | /dev/stdout | 1          | 标准输出     |
| 显示器 | /dev/stderr | 2          | 标准错误输出 |

② 输出重定向

![bash输入输出](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-linux/bash%E8%BE%93%E5%85%A5%E8%BE%93%E5%87%BA.png?q-sign-algorithm=sha1&q-ak=AKIDfwRnYUH0nWEjSVn1PDuKm0lnkpPGuyLW&q-sign-time=1556544279;1556546079&q-key-time=1556544279;1556546079&q-header-list=&q-url-param-list=&q-signature=ed2124a9bbb2234f9f3b56fa99fce8db6799a931&x-cos-security-token=cd2fba9569d6e104fb6c59fddc01ce4b7c6319c510001)

解释下什么叫输出重定向：

命令的输出本身是应该输出给标准输出设备的，也就是输出到显示器上，重定向就是把它的输出方向改变，让它不在输出到显示器上，而是让它输出到文件中，这就是输出重定向。

标准输出重定向：

```shell
[root@hepingfly hepingfly]# ll > abc

# 这样就会把 ll 的结果输出到 abc 这个文件中
```

标准错误输出重定向：

```shell
[root@hepingfly hepingfly]# lljfkdf 2>abc

# 上面我乱写一个命令，显然会输出错误信息，这时候我把标准错误输出重定向到 abc 文件中
# 你直接写 lljfkdf >abc  这样肯定是不行的，因为这是一条错误命令，但是你加一个 2，表示错误输出，就可以了
```

正确输出和错误输出同时保存：

```shell
[root@hepingfly hepingfly]# ll > abc 2>&1
[root@hepingfly hepingfly]# llfsdfdsfsd > abc 2>&1

# 上面的意思就是把正确命令和错误命令都输出到 abc 这个文件，这样写的好处就是不管你命令写的正不正确都会把结果输出到 abc 这个文件

# 解释下 ll > abc 2>&1 这个命令：
#  ll > abc 表示把命令结果输出到 abc 这个文件， 2>&1 表示把错误输出结果输出到标准输出，前面又是把标准输出输出到 abc 这个文件，所以不就是把标准输出和标准错误输出都输出到 abc 这个文件了嘛，至于 & 起一个标识作用，如果你不写 & ，就是 2>1   这个就表示把错误输出结果输出到 1 这个文件中
```

3）、多命令顺序执行

| 多命令执行符 | 格式                    | 作用                                                         |
| ------------ | ----------------------- | ------------------------------------------------------------ |
| `;`          | `命令1 ; 命令2 ; 命令3` | 多个命令顺序执行，命令之间没有任何逻辑联系                   |
| `&&`         | `命令1 && 命令2`        | 当命令1 正确执行，则命令2 才会执行<br />当命令1 执行不正确，则命令2 不会执行 |
| `||`         | `命令1 || 命令2`        | 当命令1 执行不正确，则命令2 才会执行<br />当命令1 正确执行，则命令2 不会执行 |

```shell
[root@hepingfly 桌面]# ll;date;cd /home/      这三条命令会按照顺序执行，不管前面命令执行成功还是失败，后面的命令都会照常执行

[root@hepingfly home]# ls && echo yes    如果第一个命令正确执行了，我就输出一个 yes。如果第一个命令报错，第二个命令是不会执行的。

[root@hepingfly home]# lsfaa || echo no    如果第一个命令没有正确执行，我就输出一个 no。如果第一个命令正确执行，第二个命令是不会执行的

[root@hepingfly home]# ll && echo yes || echo no  这条命令的意思是，如果第一个命令正确执行，则输出 yes，否则输出 no

```

#### 5、shell 特殊符号

| 符号  | 作用                                                         |
| ----- | ------------------------------------------------------------ |
| `''`  | 单引号。在单引号中所有的特殊符号，如 "$"  和 "`"（反引号）都没有特殊含义。 |
| `""`  | 双引号。在双引号中特殊符号都没有特殊含义。但是 "$"  "`"   "\"  是例外，拥有调用变量的值，引用命令，和转义符的特殊含义。 |
| ``    | 反引号。反引号括起来的内容是系统命令，在 Bash 中会先执行它。和 `$()` 作用一样，不过推荐使用 `$()` , 因为反引号非常容易看错 |
| `$()` | 和反引号作用一样，用来引用系统命令                           |
| `[]`  | 用于变量的测试                                               |
| `#`   | 在 shell 脚本中，# 开头的行代表注释                          |
| `$`   | 用于调用变量的值。如，需要调用变量 name 的值时，需要使用 $name 的方式得到变量的值 |
| `\`   | 转义符。跟在 \ 后的特殊符号将失去特殊含义，变为普通字符。    |

```shell
[root@hepingfly home]# a=$(date)      系统命令的执行结果赋值给变量
[root@hepingfly home]# echo $a
2019年 05月 04日 星期六 21:18:46 CST
[root@hepingfly home]# b=$(ll /home/)      理论上只要命令有输出，就可以赋值给变量
[root@hepingfly home]# echo $b
```

#### 6、变量命名规则

在定义变量时，需要遵守一些规则：

> - 变量名称可以由字母、数字、下划线组成，但是不能以数字开头
>
> - 在 Bash 中，变量的默认类型都是字符串型，如果要进行数值运算，则必须指定变量类型为数值型
>
> - 变量用等号连接值，等号左右两侧不能有空格
>
>   - ```
>     比如你在 shell 中定义一个变量，可以这么写：
>     name=shp
>     但是不能这么写：
>     name = shp      这种写法是错误的
>     因为在 linux 中命令的格式是：   命令 空格 选项 空格 参数
>     你定义变量的时候加空格，它会把你的变量当成 linux 命令去解析，这样显然有问题
>     ```
>
> - 变量的值如果有空格，需要使用单引号或者双引号扩起来。其中双引号括起来的内容 "$" "\" 和反引号都有特殊含义，而单引号括起来的内容都是普通字符
>
> - 在变量的值中，可以使用 "\" 转义符
>
> - 如果需要增加变量的值，那么可以进行变量值的叠加。不过变量需要使用双引号引起来
>
>   - ```shell
>     test=123
>     test="$test"456
>     echo $test     # 结果是123456
>     # 其实很好理解，使用 $ 调用变量的值，然后和后面的字符串拼接起来
>     ```
>
> - 如果是要把命令的结果作为变量值赋予变量，则需要使用反引号或者 $() 把命令包起来
>
>   - ```shell
>     [root@hepingfly home]# test=$(date)    把 date 命令的执行结果赋值给变量
>     [root@hepingfly home]# echo $test
>     2019年 05月 05日 星期日 21:14:28 CST
>     ```
>
> - 环境变量名建议大写，用于区分

#### 7、变量的分类

> - 用户自定义变量：这种变量是最常见的变量，由用户自己定义变量名和变量值
> - 环境变量：这种变量主要保存的是和系统环境相关的数据。
> - 位置参数变量：这种变量主要是用来向脚本当中传递参数或数据的，变量名不能自定义，变量作用是固定的
> - 预定义变量：在 Bash 中已经定义好的变量，变量名不能自定义，变量作用也是固定的

#### 8、用户自定义变量

```shell
[root@hepingfly 桌面]# name=shp        定义变量
[root@hepingfly 桌面]# echo $name		 调用变量
shp

```

变量查看：

> `set [选项]`
>
> 选项：
>
> ​	`-u` ：如果设定此选项，调用未声明变量时会报错

```shell
[root@hepingfly 桌面]# echo $qqq    在 shell 中调用一个不存在的变量，输出结果是空

[root@hepingfly 桌面]# qqq=""    
[root@hepingfly 桌面]# echo $qqq      给变量赋一个空值，输出结果也是空

# 因此根据输出结果我们无法分辨变量时不存在还是空值

[root@hepingfly 桌面]# set -u
[root@hepingfly 桌面]# echo $bbb    执行完 set -u  后再输出一个不存在的变量，就会报错
bash: bbb: unbound variable
# 当然上面执行 set -u 是临时生效的，需要永久生效的话要写到配置文件中
```

注：

你直接敲 set 是可以看到所有的用户自定义变量和环境变量

小说明:

> 在 shell 中你直接使用 $ 去调用一个变量，如果这个变量不存在，不会报错，会返回空。例如 
>
> `echo $aaa` 会返回一个空 

**删除变量：**

> `unset 变量名`

**总结一下：**

```
增：
变量名=变量值
删:
unset 变量名
改:
变量名=变量值     会直接覆盖
查:
set             可以查看所有自定义的用户变量
调用变量:
$变量名
```

#### 9、环境变量

环境变量设置：

> `export 变量名=变量值`

```shell
[root@hepingfly 桌面]# export age=100           使用 export 声明的变量即是环境变量
```

环境变量删除：

> `unset 变量名`

```shell
[root@hepingfly 桌面]# unset age       删除环境变量
```

环境变量查询：

> `env`

```shell
[root@hepingfly 桌面]# export man=shp
[root@hepingfly 桌面]# env|grep man         env 可以查看环境变量
man=shp
```

**PATH 变量：**

系统查找命令的路径

```shell
[root@hepingfly bin]# echo $PATH
/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/root/bin
```

PATH 变量的值是用 『`:`』分割的路径，这些路径就是系统查找命令的路径。也就是说当我们输入一个程序名，如果没有写入路径，系统就会到 PATH 变量定义的路径去找是否有可以执行的程序。如果找到则执行，否则会报 “命令没有发现的错误”。

现在假如说我们有一个自己的脚本，我不想输入路径就直接运行，有什么方法？

方法一：

把你自己写的脚本放到环境变量的路径下，这样就可以直接执行了。例如，把脚本放到 /bin 目录下

方法二：

利用变量叠加去修改环境变量的值

```shell
[root@hepingfly hepingfly]# PATH="$PATH":/home/hepingfly/
[root@hepingfly hepingfly]# echo $PATH
/usr/lib64/qt-3.3/bin:/usr/local/sbin:/usr/sbin:/sbin:/usr/local/bin:/usr/bin:/bin:/root/bin:/home/hepingfly/  # 发现 /home/hepingfly/ 这个路径就被加到环境变量中去了
```

**PS1 变量：**

命令提示符设置。PS1 变量是用来定义命令行提示符的，可以按照我们的需求来定义自己喜欢的提示符

```shell
[root@hepingfly hepingfly]# echo $PS1
[\u@\h \W]\$

[root@hepingfly hepingfly]# PS1='[\u@\t \W]\$'    修改 PS1 变量(PS1 变量的值要用单引号包含,因为单引号中所有的特殊字符都当成普通字符对待，前面说过)
[root@21:43:44 hepingfly]#      发现这里的提示符已经变了
```

```
\d:   显示日期，格式为 "星期 月 日"
\H：  显示完整的主机名
\h：  显示简写的主机名
\t：  显示 24 小时制时间，格式为 "HH:MM:SS"
\T：  显示 12 小时制时间，格式为 "HH:MM:SS"
\A：  显示 24 小时制时间，格式为 "HH:MM"
\@：  显示 12 小时制时间，格式为 "HH:MM am/pm"
\u：  显示当前用户名
\v：  显示 Bash 的版本信息
\w：  显示当前所在目录的完整名称
\W：  显示当前所在目录的最后一个目录
\#：  执行的第几个命令
\$：  提示符。如果是 root 用户会显示提示符为 "#" 如果是普通用户会显示提示符为 "$"
```

#### 10、位置参数变量

| 位置参数变量 | 作用                                                         |
| ------------ | ------------------------------------------------------------ |
| `$n`         | n 为数字，`$0` 代表命令本身，`$1-$9` 代表第一个到第九个参数，十以上的参数需要用大括号包含 `${10}` |
| `$*`         | 这个变量代表命令行中所有的参数， `$*` 把所有的参数看成一个整体 |
| `$@`         | 这个变量也代表命令行中所有的参数，不过 `$@` 把每个参数区分对待 |
| `$#`         | 这个变量代表命令行中所有参数的个数                           |

```shell
count.sh 脚本:
#!/bin/bash
a=$1   # 这里拿到命令行中第一个参数
b=$2   # 这里拿到命令行中的第二个参数
sum=$(($a+$b))     # 在 shell 当中如果想要进行数值运算，需要使用 $(()) 把你要运算的算式给括起来,它才知道这是数值运算, $() 是用来括系统命令的,你括起来之后它才知道这是系统命令，然后执行，把结果赋值给变量
echo $sum
echo $0     # 这个就是输出命令行本身


[root@hepingfly hepingfly]# ./count.sh 22 11
33
./count.sh
```

**小贴士**

> 在 shell 当中如果想要进行数值运算，需要使用 `$(()) ` 把你要运算的算式给括起来,它才知道这是数值运算, `$() ` 是用来括系统命令的,你括起来之后它才知道这是系统命令，然后执行，把结果赋值给变量。 

```shell
param.sh 脚本：
#!/bin/bash
for i in "$*"     # $* 是把所有参数当成一个整体
        do
                echo $i    # 这里我有多少个参数，就会一把输出出来

        done
echo "============"
for y in "$@"        #  $@ 是把参数区别对待
        do
                echo $y   # 有多少个参数，循环多少次，依次输出出来

        done

echo "============"
echo "$#"       # 这里会输出所有参数的个数


[root@hepingfly hepingfly]# ./param.sh 22 33 44
22 33 44            #   $*  把所有参数当成一个整体，所以 3 个参数一起输出出来了
============
22
33                 #    $@  把每个参数区分对待，因此 3 个参数分别输出出来
44
============
3                  #   $#   输出参数的个数

```

#### 11、预定义变量

| 预定义变量 | 作用                                                         |
| ---------- | ------------------------------------------------------------ |
| `$?`       | 最后一次执行的命令的返回状态。如果这个变量的值为 0，证明上一个命令正确执行。如果这个变量的值为非 0（具体是哪个数，由命令自己来决定），则证明上一个命令执行不正确。 |
| `$$`       | 当前进程的进程号（PID）                                      |
| `$!`       | 后台运行的最后一个进程的进程号（PID）                        |

```shell
[root@hepingfly 桌面]# fdfda           随便乱输入一个命令
bash: fdfda: command not found
[root@hepingfly 桌面]# echo $?       输出最后一次命令执行状态
127									# 非 0 表示命令未正确执行
[root@hepingfly 桌面]# ll
总用量 28
-rw-r--r--. 1 root root 25872 3月  28 22:28 mysql80-community-release-el6-2.noarch.rpm
[root@hepingfly 桌面]# echo $?
0                                 # 0 表示命令正确执行
```

#### 12、接收键盘输入

> `read [选项] [变量名]`
>
> 选项：
>
> ​	`-p "提示信息"`  ：在等待 read 输入时，输出的提示信息
>
> ​	`-t 秒数`   ：  read  命令会一直等待用户输入，使用此选项可以指定等待的时间
>
> ​	`-n 字符数`   ：read 命令接收到指定的字符数，就会执行
>
> ​	`-s`      ：     隐藏输入的数据，适用于机密信息的输入

```shell
[root@hepingfly hepingfly]# read -p "please input num: " -t 10 num
please input num: 12
[root@hepingfly hepingfly]# echo $num
12
```

#### 13、declare 声明变量类型

linux 中所有变量的默认类型都是字符串类型，我们需要把变量声明为整数类型才可以进行运算。使用 declare 命令就可以实现声明变量的类型。

> `declare [+/-] [选项] 变量名` 
>
> 选项：
>
> ​	`-` ：给变量设定类型属性
>
> ​	`+` ：取消变量的类型属性
>
> ​	`-a` ：将变量设定为数组类型
>
> ​	`-i` ：将变量声明为整数类型
>
> ​	`-r` ：将变量声明为只读变量。注意：一旦设定为只读变量，既不能修改变量的值，也不能删除变量，设置不能通过 `+r` 取消只读属性。
>
> ​	`-x` ：将变量声明为环境变量
>
> ​	`-p` ：显示指定变量的被声明的类型

变量设定为整数类型：

```shell
[root@hepingfly hepingfly]# a=1
[root@hepingfly hepingfly]# b=2
[root@hepingfly hepingfly]# c=$a+$b          直接相加是两个字符串做拼接操作
[root@hepingfly hepingfly]# echo $c
1+2
[root@hepingfly hepingfly]# declare -i c=$a+$b        声明变量 c 为数值类型
[root@hepingfly hepingfly]# echo $c
3
```

变量设定为数值类型：

```shell
[root@hepingfly hepingfly]# declare -a name[0]="shp"
[root@hepingfly hepingfly]# declare -a name[1]="kimi"
[root@hepingfly hepingfly]# declare -a name[2]="durant"
[root@hepingfly hepingfly]# echo ${name[*]}           使用 ${name[*]} 获取数组中所有变量
shp kimi durant

[root@hepingfly hepingfly]# echo ${name[0]}          使用 ${name[0]}  获取单个变量的值
shp
```

注：

我们前面说过使用 `export 变量名`   使用 export 声明的变量都是环境变量，其实它本质上就是使用 `declare -x 变量名` 将变量声明成环境变量。因为使用 `export 变量名`  比较简单，使用 `declare -x 变量名`  比较不容易记，所以我们都是用 `export 变量名`  去声明一个环境变量。

#### 14、数值运算

在 shell 中你如果要进行数值运算，推荐使用 `$((运算式))` 方式运算

```shell
[root@hepingfly 桌面]# a=2
[root@hepingfly 桌面]# b=3
[root@hepingfly 桌面]# echo $(($a+$b))
5
```

#### 15、环境变量配置文件

1）、登录时生效的环境变量配置文件

在 Linux 系统登录时主要生效的环境变量配置文件有以下五个：

> - `/etc/profile`
> - `etc/profile.d/*.sh`
> - `~/.bash_profile`
> - `~/.bashrc`
> - `/etc/bashrc`
>
> 主要区别是  /etc 下的配置文件对所有用户都生效，home 目录下只对当前用户生效

环境变量配置文件调用过程：

```
/etc/profile -->  ~/.bash_profile  -->  ~/.bashrc  -->  /etc/bashrc  --> 命令提示符
```

> 在用户登录过程中，先调用 `/etc/profile` 文件，这个环境变量配置文件中主要定义了，如： `PATH` 环境变量，`HOSTNAME` 变量，`umask` 等。
>
> 然后由 `/etc/profile` 文件调用  `~/.bash_profile` 文件，在这个文件中主要调用了  `~/.bashrc` 文件和在 `PATH` 变量后面加上 `$HOME/bin` 这个目录，也就是说如果我们在自己的家目录下建立 bin 目录，然后把自己的脚本放入 `~/bin` 目录，就可以直接执行脚本而不用通过目录执行了
>
>  `~/.bash_profile`  文件调用   `~/.bashrc` 文件，在这个文件中主要实现了定义别名，和调用 `/etc/bashrc`
>
> `/etc/bashrc` 文件实现了，定义 PS1 变量，也就是用户提示符, umask 值

**注：**

如果我们误删了这些环境变量，比如说我们删了 `/etc/bashrc` 文件，或者删除了  `~/.bashrc`  文件（这个文件会调用 `/etc/bashrc` 文件），那么我们的提示符就会变成：

```shell
-bash-4.1#

# 因为 PS1 变量是在 /etc/bashrc 文件中定义的，这个文件被删了，或者这个文件没被调用那么 PS1 变量就不会被定义，提示符自然就变成简易的 bash 版本信息。解决方法就是，从别的地方拷贝一份配置文件过来
```

2）、注销时生效的环境变量配置文件

> 在用户退出登录时，只会调用一个环境变量配置文件，就是 `~/.bash_logout` 。这个文件默认没有写入任何内容，如果我们希望在退出登录时执行一些操作，就可以把命令写入这个文件。

3）、其他配置文件

> 最常见的就是 `~/.bash_history` 文件，历史命令保存文件

**Tips ：**

> **/etc/motd 文件**
>
> `/etc/motd` 文件是在用户正确输入用户名和密码，正确登录之后显示欢迎信息。在 `/etc/motd` 文件中的欢迎信息，不论是本地登录还是远程登录都可以显示。

### 二十、shell 编程

#### 1、基础正则1

| 元字符    | 作用                                                         |
| --------- | ------------------------------------------------------------ |
| `*`       | 前一个字符匹配 0 次或任意多次                                |
| `.`       | 匹配除了换行符外任意一个字符                                 |
| `^`       | 匹配行首。例如： `^hello` 会匹配以 `hello` 开头的行          |
| $         | 匹配行尾。例如： `hello$` 会匹配以 `hello` 结尾的行          |
| `[]`      | 匹配中括号中指定的任意一个字符，只匹配一个字符。例如：`[aiou]` 匹配这四个字母中的任意一个字母。 `[0-9]` 匹配任意一个数字    `[a-z][0-9]` 匹配小写字母和一位数字构成的两位字符 |
| `[^]`     | 匹配除中括号的字符以外的任意一个字符。例如：  `[^0-9]`  匹配任意一位非数字字符    `[^a-z]` 表示任意一位非小写字母 |
| `\`       | 转义符。用于将特殊符号的含义取消。                           |
| `\{n\}`   | 表示前面的字符恰好出现 n 次。例如：`[0-9]\{4\}` 匹配 4 位数字。 `[1][3-8][0-9]\{9\}` 匹配手机号码。 |
| `\{n,\}`  | 表示前面的字符出现不小于 n 次。例如： `[0-9]\{2,\}` 表示两位及其以上的数字。 |
| `\{n,m\}` | 表示前面的字符至少出现 n 次，最多出现 m 次。例如： `[a-z]\{6,8\}` 匹配 6 到 8 位的小写字母。 |

```shell
[root@hepingfly hepingfly]# grep --color=auto "a*" bb.txt 
afss
fd           # 我想在 bb.txt 中匹配包含 a 字符的行，这种写法是由问题的，下面的写法才是正确的
bsd			 # 这种写法会把所有的都匹配到
cdsa
[root@hepingfly hepingfly]# grep --color=auto "aa*" bb.txt 
afss
cdsa     # 匹配含有一个 a 或多个 a

[root@hepingfly hepingfly]# grep --color=auto "afs*s" bb.txt 
afss   # af 和 s 之间 s 出现 0 次或多次(afs、afsss、afssss 等会匹配到)

[root@hepingfly hepingfly]# grep "c..a" bb.txt 
cdsa     # 匹配 c 和 a 中间有两个字符
```

**小说明：**

> 在正则中，任何字符后面加 `*` 去匹配，其实是不起作用的，它会匹配所有的内容。比如说：`a*`  『*』在正则中表示前一个字符匹配 0 次或多次，也就是说 a 可以出现 0 次，那么就把所有的内容都匹配到了。应该使用 
>
> 『aa*』 去匹配 a 字符。
>
> 在正则中 『.*』 表示匹配所有内容

#### 2、字符串处理之 cut

前面我们说了 grep 这个命令，这个命令是把你要查找的字符串所在行都返回出来。

cut 为列提取命令：

> `cut [选项] 文件名`
>
> 选项：
>
> ​	`-f 列号`  ：  提取第几列
>
> ​	`-d 分隔符` ： 按照指定分隔符分割列
>
> ​	`-c 字符范围`  ： 不依赖分隔符来区分列。而是通过字符范围（行首为 0） 来进行字段提取。『n- 』表示从第 n 个字符到行尾；『n-m』从第 n 个字符到第 m 个字符；『-m』 表示从第 1 个字符到第 m 个字符。

**注：**

cut 命令默认分隔符是制表符，也就是 tab 键。 cut 命令不能按照空格来进行分隔。

```shell
[root@hepingfly hepingfly]# vim student.txt
id      name    age
1       shp     18
2       jimmy   20
3       kimi    40

[root@hepingfly hepingfly]# cut -f 2 student.txt        提取第 2 列的内容
name
shp
jimmy
kimi

[root@hepingfly hepingfly]# cut -f 2,3 student.txt      提取第 2 列和第 3 列
name	age                # 如果想提取多列直接用逗号分隔即可
shp	18
jimmy	20
kimi	40

[root@hepingfly hepingfly]# vim person.txt
id,name,age
1,shp,10
2,jimmy,20
3,kimi,30

[root@hepingfly hepingfly]# cut -f 1,3 -d "," person.txt         指定按照 "," 来分割
id,age
1,10
2,20
3,30
```

#### 3、字符串处理之 printf

printf 格式化输出

> `printf '输出类型输出格式' 输出内容`
>
> 输出类型：
>
> ​	`%ns`  ：输出字符串。 n 是数字代表输出几个字符。
>
> ​	`%ni`  ：输出整数。  n 是数字指代输出几个数字。
>
> ​	`%m.nf`  ：输出浮点数。 m 和  n 是数字，代表输出的整数位数和小数位数。如 %8.2f   表示共输出 8 位数，其中 2 位是小数，6 位是整数。
>
> 输出格式：
>
> ​	`\n`  ： 换行
>
> ​	`\t`   ： tab 键

#### 4、awk 基本使用

> `awk '条件1{动作1} 条件2{动作2}' 文件名`
>
> 条件：
>
> ​	一般使用关系表达式作为条件。
>
> 动作：
>
> ​	格式化输出，流程控制语句

```shell
[root@hepingfly hepingfly]# awk '{printf $1 "\t" $3 "\n"}' student.txt 
id	age                 # $1 表示第一列，$3 表示第三列
1	18
2	20
3	40

[root@hepingfly hepingfly]# df -h | awk '{print $5}'     把第五列提取出来
Use%
34%
1%
4%
1%
1%
1%
```

#### 5、awk 条件

| 条件  | 说明                                                         |
| ----- | ------------------------------------------------------------ |
| BEGIN | 在 awk 程序一开始时，尚未读取任何数据之前执行。BEGIN 后的动作只在程序开始时执行一次 |
| END   | 在 awk 程序处理完所有的数据，即将结束时执行。 END 后的动作只在程序结束时执行一次 |
| >     | 大于                                                         |
| <     | 小于                                                         |
| >=    | 大于等于                                                     |
| <=    | 小于等于                                                     |
| ==    | 等于。用于判断两个值是否相等，如果是给变量赋值，使用 『=』号 |
| !=    | 不等于                                                       |
| A~B   | 判断字符串 A 中是否包含能匹配 B 表达式的子字符串             |
| A!~B  | 判断字符串 A 中是否不包含能匹配 B 表达式的子字符串           |

BEGIN

> BEGIN 是 awk 的保留字，是一种特殊的条件类型。BEGIN 的执行时机是 "在 awk 程序一开始时，尚未读取任何数据之前执行"。一旦 BEGIN 后的动作执行一次，当 awk 开始从文件中读取数据，BEGIN 的条件就不再成立，所以 BEGIN 定义的动作只能被执行一次。

```shell
[root@hepingfly hepingfly]# awk 'BEGIN{print "hello"} {print $2 "\t" $3}' student.txt 
hello
name	age
shp	18
jimmy	20
kimi	40
```

EDN

> END 也是 awk 的保留字，不过刚好和 BEGIN 相反。END 是在 awk 程序处理完所有程序，即将结束时执行。END 后的动作只在程序结束时执行一次。

```shell
[root@hepingfly hepingfly]# awk 'END{print "hello"} {print $2 "\t" $3}' student.txt 
name	age
shp	18
jimmy	20
kimi	40
hello
```

awk 使用举例：

```shell
[root@hepingfly hepingfly]# grep -v "age" student.txt      目的是把标题行去掉
1	shp	18
2	jimmy	20
3	kimi	40
[root@hepingfly hepingfly]# grep -v "age" student.txt |awk '$3>18{print $3}'
20											# 找出第三列大于 18 的然后大于出来
40
```



### 二十一、启动引导与修复

#### 1、系统运行级别

Linux 默认有 7 个运行级别

| 运行级别 | 含义                               |
| -------- | ---------------------------------- |
| 0        | 关机                               |
| 1        | 单用户模式                         |
| 2        | 不完全的命令行模式，不含 NFS 服务  |
| 3        | 完全的命令行模式，就是标准字符界面 |
| 4        | 系统保留                           |
| 5        | 图形模式                           |
| 6        | 重启动                             |

**runlevel 命令**

> 在 linux 系统中可以使用 runlevel 命令来查看系统的运行级别

```shell
[root@hepingfly hepingfly]# runlevel
N 5    # N 代表进入这个级别前，上一个是哪个级别。 5 代表当前级别

# 如果是由字符界面进入图形界面，查询结果应该是这样
[root@hepingfly hepingfly]# runlevel
3 5     #代表是由 3 级别进入 5 级别
```

**手动修改运行级别**

> `init 5`                 把当前运行级别改成 5

**系统默认运行级别**

> `/etc/inittab`      默认级别的配置文件，进去就可以修改默认级别
>

```shell
[root@hepingfly hepingfly]# vim /etc/inittab
id:5:initdefault:
```

**/etc/rc.d/rc.local 文件**

> 这个配置文件会在用户登录之前读取，这个文件中写入什么命令，在每次系统启动时都会执行一次。也就是说，如果有任何需要在系统启动就运行的工作，只需要写入 `/etc/rc.d/rc.local` 这个配置文件即可



### 二十二、服务管理

#### 1、服务分类

> - RPM 包安装的服务
>   - 独立的服务
>     - 就是独立启动的意思，这类型的服务可以自行启动，而不用依赖其他的管理服务。不依赖其他的管理服务，那么当客户端请求访问时，独立的服务响应请求更快速。Linux 中大多数服务都是独立服务。
>   - 基于 xinetd 的服务
>     - 这种服务就不能独立启动了，而是要依靠管理服务来调用这种服务。这个负责管理的服务就是 xinetd 服务，xinetd 服务时系统的超级守护进程。xinetd 服务的作用就是管理不能独立启动的服务。当有客户端请求时，会先请求 xinetd 服务，由 xinetd 服务区唤醒相应的服务。当客户端请求结束时，被唤醒的服务会关闭并释放资源。这样做的好处就是只需要持续启动 xinetd 服务，而其它基于 xinetd 服务只有需要时才会启动，不会占用过多的服务器资源。但是这种服务由于在有客户端请求时才会被唤醒，所以响应时间相对较慢。
> - 源码包安装的服务

#### 2、独立服务管理

1）、独立服务的**启动管理**

> ① 使用 /etc/init.d/目录中的启动脚本启动服务
>
> ```shell
> /etc/init.d/network start
> ```
>
> ② 使用 service 命令来启动服务
>
> ```shell
> service 独立服务名 start|stop|restart
>
> # service 命令也是去调用 /etc/init.d/ 下的服务去启动的
> ```
>
> 

2）、独立服务的**自启动管理**

1️⃣ 使用 `chkconfig` 自启动管理命令

> `chkconfig [--level 运行级别] [独立服务名] [on|off]` 
>
> 选项：
>
> ​	`--levle` ：设定在哪个运行级别中开机自启动（on），或者关闭自启动（off）

```shell
[root@hepingfly init.d]# chkconfig --level 2345 network on

# --level 2345 可以省略不写，默认就是 2345
```

2️⃣  修改 `/etc/rc.d/rc.local` 文件，设置服务自启动

```shell
vim /etc/rc.d/rc.local

#!/bin/sh
#
# This script will be executed *after* all the other init scripts.
# You can put your own initialization stuff in here if you don't
# want to do the full Sys V style init stuff.

touch /var/lock/subsys/local
# 设置服务启动命令，这样开机的时候就会自动执行这条命令
/etc/init.d/network start
```

3️⃣ 使用 `ntsysv` 命令管理自启动

> 你敲完这个命令，会出来一个图形界面，通过这个图形界面你可以控制服务的自启动。

































































































































