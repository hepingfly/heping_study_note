# JVM 性能监控与调优



### 一、概述

#### 1、为什么要调优及监控

> - 防止出现 OOM 的情况
> - 出现 OOM 了，通过 JVM 调优去解决 OOM 
> - 减少 Full GC 出现的频率



#### 2、性能优化的步骤

1）、性能监控（发现问题）

> 包括以下几个方面：
>
> - GC 频繁
> - cpu load 过高
> - OOM
> - 内存泄漏
> - 死锁
> - 程序响应时间较长

2）、性能分析（排查问题）

> - 打印 GC 日志，通过 GCviewer 或者 `http://gceasy.io` 来分析日志信息
> - 灵活运用命令行工具，jstack ，jmap ，jinfo
> - dump 出堆文件，使用内存分析工具分析文件
> - 使用阿里 Arthas，或者 jconsole，JVisualVM 来实时查看 JVM 状态
> - jstack 查看堆栈信息

3）、性能调优（解决问题）

> - 适当增加内存，根据业务背景选择垃圾回收器
> - 优化代码，控制内存使用
> - 增加机器，分散节点压力
> - 合理设置线程池数量
> - 使用中间件提高程序效率，如缓存，消息队列等。



### 二、JVM 监控及诊断工具

#### 1、概述

Java 作为最流行的编程语言之一，其应用性能诊断一直受到业界广泛关注。可能造成 Java 应用性能的问题因素非常多，例如：线程控制、磁盘读写、数据库访问、网络I/O、垃圾收集等。想要定位这些问题，一款优秀的性能诊断工具必不可少。

#### 2、jps ：查看正在运行的 Java 进程

1）、介绍

> jps ：Java Process Status 。
>
> 用于查询正在运行的虚拟机进程。

注：

对于本地虚拟机进程来说，进程的本地虚拟机ID 与操作系统的进程ID 是一致的，是唯一的。

![jps命令id 介绍](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-jvm/jps%E5%91%BD%E4%BB%A4id%20%E4%BB%8B%E7%BB%8D.png)

2）、基本语法

> `jps [options] [hostid]`
>
> 我们可以通过追加参数来打印额外信息

① options 参数

> - `-q`：仅仅显示 `LVMID（local virtual machine id）`，即 本地虚拟机唯一 ID，不显示主类的名称。
>
>   - ```
>     jps -q
>     2788
>     2808
>     3005
>     3006
>     ```
>
> - `-l` ：输出应用程序主类的全类名。或者，如果进行执行的是 jar 包，则输出 jar 的完整路径。
>
>   - ```
>     jps -l
>     2788
>     3014 org.jetbrains.jps.cmdline.Launcher
>     3015 com.hepingfly.monitor.Test
>     3016 jdk.jcmd/sun.tools.jps.Jps
>     2808 org.jetbrains.idea.maven.server.RemoteMavenServer
>     ```
>
> - `-m` ：输出虚拟机进行启动时传递给主类 `main()` 方法的参数
>
>   - ```
>     jps -m
>     2788
>     3063 Launcher /Applications/IntelliJ IDEA.app/Contents/lib/asm-all-7.0.1.jar:/Applications/IntelliJ IDEA.app/Contents/lib/commons-lang3-3.9.jar:/Applications/IntelliJ IDEA.app/Contents/lib/netty-buffer-4.1.47.Final.jar:/Applications/IntelliJ IDEA.app/Contents/plugins/java/lib/javac2.jar:/Applications/IntelliJ IDEA.app/Contents/lib/httpclient-4.5.12.jar:/Applications/IntelliJ IDEA.app/Contents/lib/plexus-component-annotations-1.7.1.jar:/Applications/IntelliJ IDEA.app/Contents/lib/maven-resolver-spi-1.3.3.jar:/Applications/IntelliJ IDEA.app/Contents/lib/util.jar:/Applications/IntelliJ IDEA.app/Contents/lib/platform-api.jar:/Applications/IntelliJ IDEA.app/Contents/lib/qdox-2.0-M10.jar:/Applications/IntelliJ IDEA.app/Contents/lib/netty-codec-4.1.47.Final.jar:/Applications/IntelliJ IDEA.app/Contents/lib/netty-common-4.1.47.Final.jar:/Applications/IntelliJ IDEA.app/Contents/lib/jna.jar:/Applications/IntelliJ IDEA.app/Contents/lib/trove4j.jar:/Applications/IntelliJ IDEA.app/Contents/lib/nanoxml-
>     3064 Test hepingfly              // hepingfly 是Java应用程序 Test main 方法传入的参数
>     2808 RemoteMavenServer
>     3065 Jps -m
>     ```
>
> - `-v` ：列出虚拟机进行启动时的 JVM 参数。比如 ： -Xms20m -Xmx50m 是启动程序指定的 JVM 参数。
>
>   - ```
>     jps -v
>     3075 Launcher -Xmx700m -Djava.awt.headless=true -Djdt.compiler.useSingleThread=true -Dpreload.project.path=/Users/shenheping/Documents/Personal-shp/2018/workspace/workspace-idea/interview3 -Dpreload.config.path=/Users/shenheping/Library/Application Support/JetBrains/IntelliJIdea2020.1/options -Dexternal.project.config=/Users/shenheping/Library/Caches/JetBrains/IntelliJIdea2020.1/external_build_system/interview3.3169c94c -Dcompile.parallel=true -Drebuild.on.dependency.change=true -Dio.netty.initialSeedUniquifier=-7562273079508223163 -Dfile.encoding=UTF-8 -Duser.language=zh -Duser.country=CN -Didea.paths.selector=IntelliJIdea2020.1 -Didea.home.path=/Applications/IntelliJ IDEA.app/Contents -Didea.config.path=/Users/shenheping/Library/Application Support/JetBrains/IntelliJIdea2020.1 -Didea.plugins.path=/Users/shenheping/Library/Application Support/JetBrains/IntelliJIdea2020.1/plugins -Djps.log.dir=/Users/shenheping/Library/Logs/JetBrains/IntelliJIdea2020.1/build-log -Djps.fallback.jdk.home=/Applications/IntelliJ IDEA.app/Conte
>     3076 Test -agentlib:jdwp=transport=dt_socket,address=127.0.0.1:51103,suspend=y,server=n -javaagent:/Users/shenheping/Library/Caches/JetBrains/IntelliJIdea2020.1/captureAgent/debugger-agent.jar -Dfile.encoding=UTF-8
>     2788  -Xms128m -Xmx1024m -XX:ReservedCodeCacheSize=240m -XX:+UseConcMarkSweepGC -XX:SoftRefLRUPolicyMSPerMB=50 -ea -XX:CICompilerCount=2 -Dsun.io.useCanonPrefixCache=false -Djdk.http.auth.tunneling.disabledSchemes="" -XX:+HeapDumpOnOutOfMemoryError -XX:-OmitStackTraceInFastThrow -Djdk.attach.allowAttachSelf=true -Dkotlinx.coroutines.debug=off -Djdk.module.illegalAccess.silent=true -XX:+UseCompressedOops -Dfile.encoding=UTF-8 -XX:ErrorFile=/Users/shenheping/java_error_in_idea_%p.log -XX:HeapDumpPath=/Users/shenheping/java_error_in_idea.hprof -javaagent:/Users/shenheping/.jetbrains/jetbrains-agent-v3.2.1.4255.9c8=51aaea47 -Djb.vmOptionsFile=/Users/shenheping/Library/Application Support/JetBrains/IntelliJIdea2020.1/idea.vmoptions -Didea.paths.selector=IntelliJIdea2020.1 -Didea.executable=idea -Didea.home.path=/Applications/IntelliJ IDEA.app/Contents -Didea.vendor.name=JetBrains
>     3077 Jps -Denv.class.path=/Users/shenheping/Software-shp/2020/Java/jdk-11/Contents/Home/lib/tools.jar:/Users/shenheping/Software-shp/2020/Java/jdk-11/Contents/Home/lib/dt.jar:. -Dapplication.home=/Users/shenheping/Software-shp/2020/Java/jdk-11/Contents/Home -Xms8m -Djdk.module.main=jdk.jcmd
>     2808 RemoteMavenServer -Djava.awt.headless=true -Dmaven.defaultProjectBuilder.disableGlobalModelCache=true -Xmx768m -Didea.maven.embedder.version=3.5.4 -Dmaven.ext.class.path=/Applications/IntelliJ IDEA.app/Contents/plugins/maven/lib/maven-event-listener.jar -Dfile.encoding=UTF-8
>     ```
>
>   

**注：**

如果某 Java 进程关闭了默认开启的 UsePerfData 参数（即使用参数 `-XX:-UsePerfData`），那么 jps 命令（以及 jstat）将无法探知该 Java 进程。

② hostid 参数

> 如果想要远程监控主机上的 java 程序，需要安装 `jstatd`
>
> 一般我们不会使用，都是在本地使用。

#### 3、jstat：查看 JVM 统计信息

1）、介绍

> jstat ：JVM Statistics Monitoring Tool 
>
> 用于监视虚拟机各种运行状态信息的命令行工具。
>
> 在没有 GUI 图形界面，只提供了纯文本控制台环境的服务器上，它将是运行期定位虚拟机性能问题的首选工具。常用于检测垃圾回收问题以及内存泄漏问题。

2）、基本语法

> - option参数
>
> - interval 参数 ：用于指定输出统计数据的周期，单位为毫秒。即，查询间隔
>
>   - ```
>     jstat -class 3245 1000            每隔 1s 钟打印一次
>     Loaded  Bytes  Unloaded  Bytes     Time
>        745  1741.7        0     0.0       0.15
>        745  1741.7        0     0.0       0.15
>        745  1741.7        0     0.0       0.15
>        745  1741.7        0     0.0       0.15
>        745  1741.7        0     0.0       0.15
>        745  1741.7        0     0.0       0.15
>     ```
>
> - count 参数：用于指定查询的总次数
>
>   - ```
>     jstat -class 3281 1000 3             每隔 1s 钟打印一次，总共打印 3 次
>     Loaded  Bytes  Unloaded  Bytes     Time
>        745  1741.7        0     0.0       0.15
>        745  1741.7        0     0.0       0.15
>        745  1741.7        0     0.0       0.15
>     ```
>
> - -t 参数 ：可以在输出信息前加上一个 Timestamp 列，显示程序的运行时间。单位：秒
>
>   - ```
>     jstat -class -t 3316 1000
>     Timestamp       Loaded  Bytes  Unloaded  Bytes     Time
>               116.9    745  1741.7        0     0.0       0.16
>               117.9    745  1741.7        0     0.0       0.16
>               118.9    745  1741.7        0     0.0       0.16
>               119.9    745  1741.7        0     0.0       0.16
>               120.9    745  1741.7        0     0.0       0.16
>               121.9    745  1741.7        0     0.0       0.16
>               122.9    745  1741.7        0     0.0       0.16
>      第一列表示程序从开始运行到现在的一个运行时间
>     ```
>
>     
>     
>   - -h 参数 ：可以在周期性数据输出时，输出多少行数据后输出一个表头信息
>
>     - ```
>       jstat -class -h3 -t 3316 1000
>       Timestamp       Loaded  Bytes  Unloaded  Bytes     Time
>                 319.1    745  1741.7        0     0.0       0.16
>                 320.2    745  1741.7        0     0.0       0.16
>                 321.2    745  1741.7        0     0.0       0.16
>       Timestamp       Loaded  Bytes  Unloaded  Bytes     Time
>                 322.2    745  1741.7        0     0.0       0.16
>                 323.2    745  1741.7        0     0.0       0.16
>                 324.2    745  1741.7        0     0.0       0.16
>       Timestamp       Loaded  Bytes  Unloaded  Bytes     Time
>                 325.2    745  1741.7        0     0.0       0.16
>                 326.2    745  1741.7        0     0.0       0.16
>                 327.2    745  1741.7        0     0.0       0.16
>       
>       -h3 表示每三行输出一个表头信息
>       ```
>
>   - 
>

**经验：**

- 我们可以比较 Java 进程的启动时间（使用 -t 参数，结果的第一列就表示程序启动时间）以及总 GC 时间（GCT列），或者两次测量的间隔时间以及总 GC 时间的增量，来得出 GC 时间占运行时间的比例。
- 如果该比例超过 20%，则说明目前堆的压力比较大；如果该比例超过 90%，则说明堆里几乎没有可用的空间，随时都可能抛出 OOM 异常。

![gc时间占比](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-jvm/gc%E6%97%B6%E9%97%B4%E5%8D%A0%E6%AF%94.png)



① option 参数

> - 类装载相关的
>
>   - -class ：显示 ClassLoader 的相关信息：类的装载、卸载数量、总空间、类装载所消耗的时间
>
>   - ```
>     jstat -class 3163
>     Loaded  Bytes  Unloaded  Bytes     Time
>        745  1741.7        0     0.0       0.19
>      	①    ②        ③      ④         ⑤
>      	
>      ① 表示程序在运行过程中需要加载的类的个数
>      ② 表示程序在运行过程中加载的类占用的字节数
>      ③ 表示程序在运行过程中卸载的类的个数
>      ④ 表示程序在运行过程中卸载的类占用的字节数
>      ⑤程序运行花费的总体时间
>     ```
>
> - 垃圾回收相关的
>
>   - -gc ：显示与 GC 相关的堆信息。包括 Eden 区，两个 Survivor 区、老年代、永久代的容量、已用空间、GC 时间合计等信息
>   - -gccapacity：显示内容与 -gc 基本相同。但是输出主要关注 Java 堆各个区域使用到的最大最小空间。
>   - -gcutil ：显示内容与 -gc 基本相同。但是输出主要关注已使用空间占总空间的百分比
>   - -gccause ：与 -gcutil 功能一样，但是会额外输出导致最后一次或当前正在发生的 GC 产生的原因
>   - -gcnew ：显示新生代 GC 状况
>   - -gcnewcapacity ：显示内容与 -gcnew 基本相同，输出主要关注使用到的最大、最小空间
>   - -gcold ：显示老年代 GC 状况
>   - -gcoldcapacity：显示内容与 -gcold 基本相同，输出主要关注使用到的最大、最小空间
>   
> - JIT相关
>
>   - -compiler ：显示 JIT 编译器编译过的方法、耗时等信息
>   - -printcompilation ：输出已经被 JIT 编译的方法

② jstat -gc 结果详解

![jstat](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-jvm/jstat.png)

> 新生代：
>
> - S0C是第一个幸存者区的大小(字节
> - S1C是第二个幸存者区的大小(字节)
> - SOU是第一个幸存者区已使用的大小(字节)
> - S1U是第二个幸存者区已使用的大小(字节)
> -  EC是Eden空间的大小(字节)
> - EU是Eden空间已使用大小(字节)
>
> 老年代：
>
> - OC是老年代的大小(字节)
> - OU是老年代已使用的大小(字节)
>
> 方法区（元空间）：
>
> - MC是方法区的大小
> - MU是方法区已使用的大小
> -  CCSC是压缩类空间的大小
> - CCSU是压缩类空间已使用的大小
>
> 其他：
>
> - YGC是指从应用程序启动到采样时young gc次数 
> - YGCT是指从应用程序启动到采样时young gc消耗的时间(秒)
> - FGC是指从应用程序启动到采样时full gc次数 
> - FGCT是指从应用程序启动到采样时full gc消耗的时间(秒)
> - GCT是指从应用程序启动到采样时gc的总时间（Full GC 和 Young GC 时间的和）

**通过 jstat 如何去判断是否出现内存泄漏？**

> 1、在长时间运行的 Java 程序中，我们可以运行 jstat 命令连续获取多行性能数据，并取这几行数据中 OU 列（即已占用的老年代内存）的最小值
>
> 2、然后我们每隔一段较长时间重复上一次操作，来获得多组 OU 最小值。如果这些值呈上涨趋势，则说明该 Java 程序的老年代内存已使用量在不断上涨，这意味着无法回收的对象在不断增加，因此很有可能存在内存泄漏。

#### 4、jmap ：导出内存映像文件和内存使用情况

1）、介绍

> jmap ：JVM Memory Map
>
> 作用一方面是获取 dump 文件，它还可以获取目标 Java 进程的内存相关信息，包括 Java 堆各区域的使用情况、堆中对象的统计信息、类加载信息

























