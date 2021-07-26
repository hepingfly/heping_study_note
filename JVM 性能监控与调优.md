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

**1）、介绍**

> jmap ：JVM Memory Map
>
> 作用一方面是获取 dump 文件（堆内存快照文件,二进制文件），它还可以获取目标 Java 进程的内存相关信息，包括 Java 堆各区域的使用情况、堆中对象的统计信息、类加载信息

**2）、基本语法**

> `jmap [option] <pid>`
>
> `jmap [option] <executable> <core>`
>
> `jmap [option] [server_id@]<remote server IP or hostname>`

其中 option 参数包括一下内容：

> - -dump ：生成 Java 堆转储快照（dump 文件）
>   - `-dump:live`  只保存堆中存活的对象
> - -heap ：输出整个堆空间的详细信息，包括 GC 的使用、堆配置信息、以及内存的使用信息
> - -histo ：输出堆中对象的统计信息，包括类、实例数量和合计容量
>   - `-histo:live` 只统计堆中存活的对象
> - -permstat ：以ClassLoader为统计口径输出永久代的内存状态信息
> -  -finalizerinfo ：显示在F-Queue中等待Finalizer线程执行finalize方法的对象
> - -F ：当虚拟机进程对-dump选项没有任何响应时，可便用此选项强制执行生成dump文件
> - `-J <flag>`  ：传递参数给 jmap 启动 jvm

为什么要导出 dump 文件，dump 文件的作用是啥？

如果程序在执行过程中，出现了堆的溢出或者非堆的溢出（方法区、metaspace 也可能会出现溢出），我们就需要对数据进行分析，具体就是分析这个 dump 文件。

**3）、导出内存映像文件**

① 手动的方式

`jmap -dump:format=b,file=<fileName.hprof> <pid>`

`jmap -dump:live,format=b,file=<fileName.hprof> <pid>`

② 自动的方式

`-XX:+HeapDumpOnOutOfMemoryError`

`-XX:+HeapDumpPath=<fileName.hprof>`

**注：**

> 自动和手动对应着两种使用场景，手动导出可以在没发生 OOM 之前就可以把 dump 文件导出进行分析。
>
> 但是当程序发生 OOM 退出系统时，一些瞬时信息都随着程序的终止而消失，而重现 OOM 问题比较困难或者耗时，所以如果可以在 OOM 时，自动导出 dump 文件就显得非常迫切。
>
> 所以我们就可以采用自动的方式去获取 dump 文件：
>
> `-XX:+HeapDumpOnOutOfMemoryError`  ：当程序发生 OOM 时，导出应用程序的当前堆快照
>
> `-XX:+HeapDumpPath=<<fileName.hprof>>`  ： 指定堆快照的保存位置
>
> 例如：
>
> `-Xmx100m -XX:+HeapDumpOnOutOfMemoryError  -XX:+HeapDumpPath=D:\1.hprof`



**说明：**

1、通常在写 Heap Dump 文件前会触发一次 Full GC，所以 heap dump 文件里保存的都是 FullGC 后留下的对象信息。

2、由于生成 dump 文件比较耗时，因此需要耐心等待，尤其大内存镜像生成 dump 文件则需要耗费更长的时间来完成。



**4）、导出堆内存相关信息**

`jmap -heap pid`

`jmap -histo pid`

可以通过以上两个命令导出这个时间点，堆内存相关信息



#### 5、jhat：JDK 自带堆分析工具

1）、介绍

> jhat （JVM Heap Analysis Tool）：
>
> Sun JDK 提供的 jhat 命令与 jmap 命令搭配使用，用于分析 jmap 生成的 heap dump 文件。jhat 内置了一个微型的 HTTP/HTML 服务器，用于生成 dump 文件的分析结果后，用户可以在浏览器中查看分析结果。
>
> 使用了 jhat 命令，就启动了一个 http 服务，端口是 7000，即 `http://localhost:7000` 就可以在浏览器中分析。

**注：**

jhat 命令在 JDK9、JDK10 中已经被删除，官方建议使用 Visual VM 代替



#### 6、jstack：打印 JVM 中线程快照

**1）、介绍**

> jstack ：JVM Stack Trace
>
> 用于生成虚拟机指定进程当前时刻的线程快照。线程快照就是当前虚拟机内指定进程的每一条线程正在执行的方法堆栈的集合。
>
> 生成线程快照的作用：
>
> 可用于定位线程出现长时间停顿的原因，如线程间死锁、死循环、请求外部资源导致的长时间等待问题。这些都是导致线程长时间停顿的常见原因。当线程出现停顿时，就可以用 jstack 显示各个线程调用的堆栈情况。

在线程 dump 中，我们一般会重点留意下面几种状态：

> - 死锁， Deadlock（重点关注）
> - 等待资源，Waiting on condition（重点关注）
> - 等待获取监视器，Waiting on monitor entry（重点关注）
> - 阻塞，Blocked（重点关注）
> - 执行中，Runnable
> - 暂停，Suspended
> - 对象等待中，Object.wait()   或 TIMED_WAITING
> - 停止， Parked

2）、基本语法：

> `jstack option pid`

option 参数包括下面几个：

> - `-F` ：当正常输出的请求不被响应时，强制输出线程堆栈
> - `-l`：除堆栈外，显示关于锁的附加信息
> - `-m` ：如果调用到本地方法的话，可以显示 C/C++ 的堆栈
> -  

案例代码：

```java
/**
 * @auther hepingfly
 * @date 2021/4/5 1:27 下午
 */
public class SleepTest {
    public static void main(String[] args) {
        System.out.println("hepingfly");
        try {
            Thread.sleep(1000 * 60 * 20);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

上面这段代码处于等待中，下面用 jstack 去分析一下：

![jstack分析代码](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-jvm/jstack%E5%88%86%E6%9E%90%E4%BB%A3%E7%A0%81.png)



### 三、JVM 监控及诊断-GUI篇

#### 1、概述

使用命令行去监控或者诊断 Java 性能相关信息虽然方便，但是存在以下局限：

1、无法获取方法级别的分析数据，如方法间的调用关系、各方法的调用次数和调用时间等（这对定位应用性能瓶颈至关重要）

2、要求用户登录到目标 Java 应用所在的宿主机上，使用起来不是很方便

3、分析数据通过终端输出，结果展示不够直观

图形化诊断工具：

> - JDK 自带的工具
>   - jconsole ：JDK 自带的可视化监控工具。查看 Java 应用程序的运行概况、监控堆信息、永久区使用情况、类加载情况等
>   - Visual VM ：提供了一个可视化界面，用于查看 Java 虚拟机上运行的基于 Java 的应用程序详细信息
>   - JMC ：能够以极低的性能开销收集 Java 虚拟机的性能数据
> - 第三方工具
>   - MAT ：Memory Analyzer Tool ，是基于 Eclipse 的内存分析工具，是一个快速、功能丰富的 Java heap 分析工具，可以帮助我们查找内存泄漏
>   - JProfiler ：商业软件，需要付费，功能强大。
>   - Arthas ：Alibaba 开源的 Java 诊断工具。深受开发者喜爱。
>   - Btrace ：Java 运行时追踪工具。可以在不停机的情况下，跟踪指定的方法调用、构造函数调用和系统内存等信息

#### 2、jConsole 使用

1、概述

> 从 Java5 开始，在 JDK 中自带的 Java 监控和管理控制台
>
> 用于对 JVM 中内存、线程和类的监控，是一个基于 JMX（Java Management extensions）的 GUI 性能监控工具

2、启动

直接去 bin 目录下启动即可。

3、连接方式

> - 本地连接
>   - 使用 jConsole 连接一个正在本地系统运行的 JVM ，并且执行程序和运行 jConsole 的需要是同一个用户。jConsole 使用文件系统的授权通过 RMI 连接器连接到平台的 MBean 服务器上。这种从本地连接的监控能力只有 Sun 的 JDK 具有。
> - 远程
>   - 使用下面的 URL 通过 RMI 连接器连接到一个 JMX 代理， `service:jmx:rmi:///jndi/rmi://hostname:port/jmxmi`  jConsole 为建立连接，需要在环境变量中设置 mx.remote.credentials 来指定用户名和密码，从而进行授权。
> - 高级
>   - 使用一个特殊的 URL 连接 JMX 代理。一般情况使用自己定制的连接器而不是 RMI 提供的连接器来连接 JMX 代理，或者是一个使用 JDK1.4 的实现了 JMX 和 JMX Remote 的应用。



#### 3、Visual VM

**1、概述**

> - Visual VM 是一个功能强大的多合一故障诊断和性能监控的可视化工具。
> - 它集成了多个 JDK 命令行工具，使用 Visual VM 可用于显示虚拟机进程以及虚拟机进程的配置和环境信息（类似于命令行使用 jps ，jinfo）监视应用程序的 CPU、GC、堆、方法区及线程的信息（jstat ， jstack）等，甚至代替 JConsole。
> - 在 JDK6 Update JDK7 以后，Visual VM 便作为 JDK 的一部分发布（在 JDK 的 bin 目录下）。即：它完全免费
> - 此外，Visual VM 也可以作为独立的软件安装。

**注：**

JDK9 以后 visual VM 不再集成在 Oracle JDK 中，需要单独下载安装。

下载地址：`https://visualvm.github.io/download.html`

下载之后解压到 JDK 目录下即可。



**2、插件的安装**

- Visual VM 的一大特点就是支持插件扩展，并且插件安装非常方便。我们既可以通过离线下载插件文件 `*.nbm` ,然后在 Plugin 对话框的已下载页面下，添加已下载的插件。也可以在可用插件页面下，在线安装插件。
- 插件地址：`https://visualvm.github.io/pluginscenters.html`
- IDEA 中安装 Visual VM
  - Preferences --  Plugins -- 搜索 Visual VM Launcher ，安装重启即可

















