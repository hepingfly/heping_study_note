# Dubbo 笔记

### 一、初步了解

dubbo 是我们开发分布式应用，现在用的比较多的一个 RPC 框架。 

#### 1、分布式基础理论

##### 1.1、什么是分布式系统

《分布式系统原理与范型》这本书中是这么定义的：

`分布式系统是若干独立计算机的集合，这些计算机对于用户来说就像单个相关系统`

分布式系统（distributed system ）是建立在网络上的软件系统。说的直白一点就是很多台计算机在一起为用户提供一种系统服务，那么对于用户来说，他使用这个系统服务就像在使用单个系统，而不是一台台计算机。

举个例子：

比如我们在使用京东的时候，对于我们用户来说就是一个京东的购物系统，但是这个系统背后其实有成千上万台计算机为我们提供服务，这些所有的计算机合起来构成一个完整的京东商城购物系统。这个购物系统它就是一个分布式的系统。

##### 1.2、发展演变

1️⃣ 单一应用架构

当网站流量很小的时候，只需要一个应用，将所有功能都部署在一起，以减少部署节点和成本。

举个例子：

假如我们现在有个小型的购物网站，它只是给少量的人来提供服务，流量比较小。  网站里面有很多的功能，有和用户相关的功能，有和订单相关的功能，有和商品相关的功能。由于我们这个网站的访问量还比较小，所以没有必要把它做成一个大型的分布式应用，所以我们可以把所有的功能做在一个应用里面，然后把整个应用打包部署在一台机器上。假如有一天一台机器承受不了了，那么我们可以再扩展一台机器来分担压力。

优点：这样开发简单，部署也简单。

缺点：

1、扩展不容易。假如我们要在应用里面添加或修改一个功能，那么都要把应用在重新打包，放到服务器上，如果有多台服务器，更是麻烦。

2、协同开发不容易。因为只有一个应用，所以假如我只想改订单相关的功能，但是如果改错了，那么其他模块的功能也不能使用。

3、当我们的应用不断扩大，比如我们以前的应用只有 10 MB，但是现在应用有 500 MB，要是还是单体应用放在一台服务器上，一台服务器上跑这么大的应用程序，压力也是会很大的。 这时候光靠增加服务器已经不能带来性能上的提高了，这时候我们就需要重新考虑一下应用的架构方式。

2️⃣ 垂直的应用架构

这种应用架构我们就可以将刚才那个大的单体应用架构拆成几个互不相关的小应用，这些小应用独立部署到每一个服务器上。哪一个小应用功能需要增加或修改了，我们只需要改这个小应用就可以了。哪一个小应用流量比较大了，我再扩展一台服务器，在这台服务器上再部署一个应用就可以了。但是应用与应用之间不可能是完全隔离的，比如支付模块的功能需要用到订单模块的功能。所以你不同的小应用之间想要交互，就要使用另一种架构模式了

3️⃣ 分布式服务架构

就是我们可以把不同应用的核心业务逻辑给抽出来，但是有个问题就是这些应用可能不在一台服务器上，那么我们想要调用的话，就只能通过 RPC 进行调用

##### 1.3、RPC

**什么是 RPC ？**

> RPC（Remote Procedure Call）是指远程过程调用，是一种进程间通信方式，它是一种技术思想，而不是规范。它允许程序调用另一个地址空间（通常是共享网络的另一台机器上）的过程或函数，而不用程序员显示编码这个远程调用的细节。即程序员无论是调用本地的远程的函数，本质上编写的调用代码基本相同。
>

**RPC 基本原理**

![RPC基本原理](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/RPC基本原理.png)

> 比如说我们现在 A 服务器客户端有一个小功能想要调用 B 服务器上的一个小功能，这个 A 服务器可以先找一个小助手，这个小助手一看，A 服务器想要调用 B 服务器，那么他就会先和 B 服务器建立一个 Socket 连接，然后把调用 B 服务器方法需要的一些参数信息传递给 B 服务器，B 服务器的小助手收到这些参数信息会去调用 B 服务器上的方法，然后把调用完成后的返回信息通过 Socket 回传给 A 服务器的小助手，然后 A 服务器的小助手把返回值交给客户端的代码。

![RPC调用时序图](http://peq5n0h1s.bkt.clouddn.com/RPC%E8%B0%83%E7%94%A8%E6%97%B6%E5%BA%8F%E5%9B%BE.png)

解释一下上面这个图：

> A 服务器上有一个代码 hello() 这个方法里面想要去调用 B 服务器上的 hi() 方法，同时传入一个 user 对象。B 服务器上的方法会返回一个值，A 服务器在调用完 B 服务器的方法后会得到一个返回值，在控制台打印这个返回值。那么整个调用过程就是：
>
> 我们 A 服务器的客户端想要调用 B 服务器的代码，那么在 A 服务器上会有一个小助手，这个小助手一看 A 服务器想要调用别的服务器，那么会先跟 B 服务器建立一个 Socket 连接，建立连接以后，你要调用别人方法还要携带参数，这个参数是需要发送给 B 服务器的。参数在网络间传输，那么肯定要把它进行序列化，序列化完成以后我们就可以发送信息给 B 服务器了。这时候 B 服务器就收到了 A 服务器的信息，B 服务器也会有个小助手，这个小助手会先把发过来的对象进行反序列化，然后去调用 B 服务器上的方法，调用完成之后会有个结果返回过来，那么 B 服务器的小助手会对拿到的结果进行序列化，然后返回给 A 服务器的小助手，A 服务器的小助手对结果进行反序列化，拿到最终结果返回给 client

RPC 两个核心模块：通讯和序列化

决定 RPC 框架性能的两个因素：1、通信效率 2、序列化和反序列效率

RPC 框架有很多如：dubbo、google 的 gRPC、fascbook 的 Thrift、以及阿里在用的 HSF(High Speed Service Framework)，他们虽然用法有所不同，但是思想都是一样的，都是通过网络通信来传递数据以实现远程过程调用。

## 二、正式学习

### 1、dubbo 概念、简介

dubbo 历史和背景：

> 首先说一下 dubbo 帮我们解决了什么问题？
>
> 它帮我们解决了在分布式服务器架构里面，A 服务器上的一个程序想要调用 B 服务器上的一段代码，我们如何进行远程调用的问题，也就是不同服务之间我们如何通信，如何调用，如何传递数据的问题，dubbo 会帮我们完成。
>
> dubbo 其实是阿里巴巴企业内部使用的一个 RPC 框架，在 2011 年的时候将它托管到了 github 上，然后持续进行更新，然后在 2014 年 10 月份的时候，阿里发布了 dubbo 的 2.4.11 版本。至此之后 dubbo 便停更了，但是其他企业也在使用 dubbo，像当当网基于 dubbo 进行了扩展 dubbox，网易考拉的 dubbok也都是基于 dubbo 进行的扩展。到了 2017 年的时候 springcloud 在抢占分布式市场，springcloud 这时候非常火，这时候阿里又更新了 dubbo，到了 2018 年 1月，阿里将 dubbox 和 dubbo 进行合并，更新了 2.6 版本。到了 2018 年 2 月份，阿里最终将 dubbo 开源贡献给了 Apache 组织，所以我们现在看到的都是 Apache Dubbo。

### 2、dubbo 设计架构

![dubbo设计架构](![](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/dubbo设计架构.png)

解释一下这个图：

> dubbo 将应用分成这几个角色，『Registry』注册中心，『Provider』服务提供者，『Consumer』 服务消费者，服务提供者的作用就是提供服务的，服务消费者的作用就是消费我们提供的服务。『Container』指的是我们的 dubbo 框架容器，『Monitor』服务监控中心，可以对服务提供者和服务消费者进行监控。
>
> 整个运行流程就是：
>
> 我们的 dubbo 框架（容器）一启动，我们的服务提供者会将自己所提供的这些服务信息注册到注册中心，这样注册中心就知道我们到底有哪些服务上线了，当服务消费者启动之后他就会从注册中心去订阅他所需要的服务，当服务提供者有了变更，比如说哪台机器下线了，那么注册中心可以吧消息推送给服务消费者。服务消费者就实时的知道服务消费者的哪个服务不能调用了。当服务消费者在调用服务提供者的服务时，也会同时把调用信息发送到监控中心，这样监控中心就能统计到这些调用信息了。这个监控中心的作用其实就是可以帮助用户通过可视化的界面来管理和维护众多的服务，看到各个服务的情况。

### 3、dubbo 管理控制台安装（dubbo-admin）

> 1、先进入 Apache Dubbo 官网，点击 GitHub
>
> 2、一直往下翻，找到下图标红的地方点进去
>
> ![dubbo 监控中心管理控制台安装1](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/dubbo管理控制台安装.png)
>
> 3、把整个项目下载下来，结果是一个 zip 包，包名是 『incubator-dubbo-ops-master.zip』
>
> 4、把这个 zip 包解压，解压完之后进入到 『incubator-dubbo-ops-master/dubbo-admin/src/main/resources』这个目录下
>
> 5、修改配置文件 『application.properties』文件中 『dubbo.registry.address=zookeeper://127.0.0.1:2181』这个地址要改成你 zookeeper 的地址
>
> 6、改完之后去到 『incubator-dubbo-ops-master/dubbo-admin』这个目录下，使用 『mvn package』把这个项目打成一个 jar 包，因为只有打成 jar 包我们才能执行，不然只是从 github 上下载下来的代码我们不能执行
>
> 7、打包完成之后 『incubator-dubbo-ops-master/dubbo-admin/target』在 target 目录下你就会发现有 jar 包，可以直接使用 『java -jar』命令执行这个 jar 包
>
> 8、在浏览器访问 『localhost:7001』即可看到管理控制台（前提是 zookeeper 一定要是启动的）
>
> 9、登录的用户名和密码都是 『root』
>
> **主要作用：**
>
> 图形化的服务管理页面，安装时需要指定注册中心的地址，即可从注册中心获取到所有的提供者/消费者进行配置管理

### 4、dubbo 的 helloworld

**需求：**

假设现在有某个电商系统，订单服务需要调用用户服务获取某个用户的所有地址。所以我们现在需要创建两个服务模块进行测试：

| 模块                     | 功能         |
| ------------------------ | ------------ |
| 订单服务（web 模块）     | 创建订单等   |
| 用户服务（service 模块） | 查询用户地址 |

现在有两个模块，第一个是用户服务模块，这个模块可以查询到用户地址。第二个模块是订单服务模块，我要创建订单的话，需要知道用户地址，所以我现在订单服务模块就想远程调用用户服务模块拿到用户地址

测试预期结果：

订单服务 web 模块在 A 服务器，用户服务模块在 B 服务器，A 可以远程调用 B 的功能

#### 1）、服务提供者

```java
// javabean 
public class UserAddress {

    private Integer id;
    private String userAddress;  //用户地址
    private String userId;   //用户 id
    private String consignee;  //收货人
    private String phoneNum;
    private String isDefault;  //是否是默认地址
    
}
```

```java
// UserService.java
/**
 * 用户服务接口
 */
public interface UserService {

    /**
     * 按照用户 id 返回收货地址
     * @param userId
     * @return
     */
    public List<UserAddress> getUserAddressList(String userId);
}

//---------------------------------------------------------------------------
//UserServiceImpl.java
/**
 用户服务接口实现类
*/
public class UserServiceImpl implements UserService {
    @Override
    public List<UserAddress> getUserAddressList(String userId) {
        UserAddress address1 = new UserAddress(1,"合肥市","11","收货人1","110","0");
        UserAddress address2 = new UserAddress(2,"芜湖市","22","收货人2","110","0");
        return Arrays.asList(address1,address2);
    }
}
```

#### 2）、服务消费者

```java
// OrderService.java
/**
 * 订单服务
 */
public interface OrderService {

    /**
     * 初始化订单
     */
    public void initOrder(String userid);
}

//-------------------------------------------------------------------------
//OrderServiceImpl.java
public class OrderServiceImpl implements OrderService {
    private UserService userService;
    @Override
    public void initOrder(String userid) {
        /**
         * 1.查询用户收货地址（这里我们就需要调用用户服务了）
         * 我们需要调用 userService 但是我们的工程里面没有 userService 和它对应的 javabean
         * 所以我们可以把 userService 和 javabean 拷贝到我们工程中来，然后按照下面方式
         * 去调用，但是调用结果一定是失败的，因为我们只拷贝了接口道我们工程中来，具体实现
         * 还在别的机器上
         *
         * 这样还存在一个问题，就是我 userService 接口如果被很多工程引用，那么我是不是都要
         * 把接口和 javabean 复制一遍，所以我们考虑把接口和 javabean 抽取出来写，谁需要
         * 用就把它引用进来
         */
        List<UserAddress> userAddressList = userService.getUserAddressList("11");
    }
}
```

**优化后的方案：**

就是把 user-service-provider 和 order-service-consumer 下的接口和 javabean 都抽取出来单独放在一个工程里面，哪个要引用就直接通过依赖引用接口

```xml
<dependencies>
    <dependency>
        <groupId>com.iflytek</groupId>
        <artifactId>service-common</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </dependency>
</dependencies>

把这个抽取出来的公共组件的依赖在需要用的工程的 pom 文件中直接引入即可
```

#### 3）、服务提供者配置、测试

在上面我们已经把服务的提供者和服务消费者代码编写好了，为了能使服务消费者远程调用服务提供者代码成功，我们使用 dubbo 来改造一下。

看上面的 dubbo 架构图我们知道，第一步我们需要把服务提供者注册到注册中心，第二步服务消费者去注册中心订阅服务提供者提供的服务。下面我们先来说第一步（以下代码我们都是在服务提供者里面操作）：

1️⃣ **将服务提供者注册到注册中心（暴露服务）**

> 1、导入 dubbo 的依赖（2.6.2 版本）和操作 zookeeper 的客户端（curator）
>
> ```xml
> <!--引入 dubbo 依赖-->
> 		<dependency>
> 			<groupId>com.alibaba</groupId>
> 			<artifactId>dubbo</artifactId>
> 			<version>2.6.2</version>
> 		</dependency>
>
> 		<!--
> 			由于注册中心使用的是 zookeeper 所以需要引入操作 zookeeper 的客户端
> 			为什么 dubbo 自己没有帮我们引入呢？
> 			由于注册中心 dubbo 有很多，不一定非要用 zookeeper ，如果你自己需要
> 			用 zookeeper ，那么你就自己手动把操作 zookeeper 的客户端引过来
> 			dubbo2.6 以前的版本，引入 zkClient 操作 zookeeper
> 			dubbo2.6 及以后的版本，引入 curator 操作 zookeeper
> 		-->
> 		<dependency>
> 			<groupId>org.apache.curator</groupId>
> 			<artifactId>curator-framework</artifactId>
> 			<version>2.12.0</version>
> 		</dependency>
> ```
>
> 2、配置服务提供者
>
> 如何配置可以参考：http://dubbo.apache.org/en-us/docs/user/quick-start.html 里面的 provider.xml 
>
> 下面是我们自己写的放在类路径下的 provider.xml
>
> ```java
> <?xml version="1.0" encoding="UTF-8"?>
> <beans xmlns="http://www.springframework.org/schema/beans"
>        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
>        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd">
>
>     <!--1、指定当前服务/应用的名称（同样的服务名字相同，不要和别的服务同名）-->
>     <dubbo:application name="user-service-provider"></dubbo:application>
>
>     <!--
>         2、指定注册中心的位置（我们注册中心使用的是 zookeeper）
>         也可以使用下面那种方式去写，两种方式选一种就可以了
>     -->
>     <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry>
>     <!--<dubbo:registry protocol="zookeeper" address="127.0.0.1:2181"></dubbo:registry>-->
>
>     <!--
>         上面两步我们指定的服务提供者的名字和注册中心的位置，这样容器一启动的时候，服务
>         提供者就可以注册到注册中心中去了，但是我们还有服务消费者，服务消费者最终是要和
>         服务提供者进行通信的，所以这通信过程使用什么协议进行通信，以及通过哪个端口进行
>         通信，这些都是需要去指定的
>     -->
>     <!--
>         3、指定通信规则（包括通信协议和通信端口）
>         name 用来指定协议的名字
>         port 用来指定通信的端口，可以随便写一个
>         使用 dubbo 协议，在 20080 端口进行通信
>     -->
>     <dubbo:protocol name="dubbo" port="20080"></dubbo:protocol>
>
>     <!--
>         4、暴露服务
>         ref : 指向服务的真正实现对象
>     -->
>     <dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl"></dubbo:service>
>     <!--服务的实现-->
>     <bean id="userServiceImpl" class="com.iflytek.service.impl.UserServiceImpl"></bean>
> </beans>
> ```
>
> 3、测试
>
> ```java
> public class MainApplication {
>     public static void main(String[] args) throws IOException {
>         //这样当 ioc 容器一启动的时候就会去加载服务提供者的配置文件
>         ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("classpath:provider.xml");
>         ioc.start();
>         System.in.read();
>     }
> }
> //通过我们前面安装的管理控制台可以查询服务是否启动成功
> ```
>
> 

#### 4）、服务消费者配置、测试

> 1、导入 dubbo 的依赖（2.6.2 版本）和操作 zookeeper 的客户端（curator）
>
> ```xml
> <!--引入 dubbo 依赖-->
> 		<dependency>
> 			<groupId>com.alibaba</groupId>
> 			<artifactId>dubbo</artifactId>
> 			<version>2.6.2</version>
> 		</dependency>
>
> 		<!--
> 			由于注册中心使用的是 zookeeper 所以需要引入操作 zookeeper 的客户端
> 			为什么 dubbo 自己没有帮我们引入呢？
> 			由于注册中心 dubbo 有很多，不一定非要用 zookeeper ，如果你自己需要
> 			用 zookeeper ，那么你就自己手动把操作 zookeeper 的客户端引过来
> 			dubbo2.6 以前的版本，引入 zkClient 操作 zookeeper
> 			dubbo2.6 及以后的版本，引入 curator 操作 zookeeper
> 		-->
> 		<dependency>
> 			<groupId>org.apache.curator</groupId>
> 			<artifactId>curator-framework</artifactId>
> 			<version>2.12.0</version>
> 		</dependency>
> ```
>
>   2、配置服务消费者
>
> 如何配置可以参考：http://dubbo.apache.org/en-us/docs/user/quick-start.html 里面的 consumer.xml
>
> 下面是我们自己写的放在类路径下的 consumer.xml：
>
> ```Xml
> <?xml version="1.0" encoding="UTF-8"?>
> <beans xmlns="http://www.springframework.org/schema/beans"
>        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
>        xmlns:context="http://www.springframework.org/schema/context"
>        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
>
>     <!--指定当前服务/应用的名称-->
>     <dubbo:application name="order-service-consumer"></dubbo:application>
>     <!--指定注册中心的位置-->
>     <dubbo:registry protocol="zookeeper" address="127.0.0.1:2181"></dubbo:registry>
>
>     <!--
>         声明需要调用的远程服务的接口，生成远程服务代理
>         我们在服务提供者那里暴露了接口服务，在这里调用接口服务
>     -->
>     <dubbo:reference interface="com.iflytek.service.UserService" id="userService">      	</dubbo:reference>
>
>     <!--为了让项目中的 @Service 或者 @Autowire 注解生效，加个包扫描器-->
>     <context:component-scan base-package="com.iflytek"></context:component-scan>
> </beans>
> ```
>
> **小讲解：**
>
> 前面我们也看过服务消费者的代码，这里再看一下：
>
> ```java
> @Service
> public class OrderServiceImpl implements OrderService {
>     @Autowired
>     private UserService userService;
>     @Override
>     public void initOrder(String userid) {
>         //之前我们没有使用配置 dubbo 的时候，我们说你把 UserService 注入进来，然后调用它的方法是不行的，因为可能这两个服务都不在一台机器上。现在我们配置好 dubbo 就可以通过 dubbo 进行远程调用了。因为服务提供者已经把服务注册到注册中心，并且暴露了 UserService 这个接口，然后我服务消费者在注册中心订阅服务，调用消费者暴露的接口
>         List<UserAddress> userAddressList = userService.getUserAddressList("11");
>     }
> }
> ```
>
> 3、测试
>
> ```java
> public class MainApplication {
>     public static void main(String[] args) throws IOException {
>         ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("consumer.xml");
>         OrderService orderService = context.getBean(OrderService.class);
>         orderService.initOrder("1");
>         System.in.read();
>     }
> }
> ```
>
> 

### 5、dubbo 监控中心的安装

> 1、2、3 步参照 dubbo 管理控制台安装
>
> 4、把这个 zip 包解压，解压完之后进入到 『incubator-dubbo-ops-master/dubbo-monitor-simple』这个目录下
>
> 5、使用 『mvn package』把这个项目打成一个 jar 包
>
> 6、打包成功之后，我们需要运行，但是在运行之前我们需要修改一些配置，所以我们不要直接去运行那个 jar 包，在同一级目录下，你除了会看到 「dubbo-monitor-simple-2.0.0.jar」这个 jar 包外，你还会看到 「dubbo-monitor-simple-2.0.0-assembly.tar.gz」这个 tar 包
>
> 7、把这个 tar 包解压，你会得到 「dubbo-monitor-simple-2.0.0」这个文件夹
>
> 8、进入 dubbo-monitor-simple/target/dubbo-monitor-simple-2.0.0/conf 你会看到这个目录下面有个 dubbo.properties 文件
>
> ```properties
> dubbo.container=log4j,spring,registry,jetty-monitor
> dubbo.application.name=simple-monitor
> dubbo.application.owner=dubbo
> #注册中心的地址
> dubbo.registry.address=zookeeper://127.0.0.1:2181
> #其他服务与我们监控中心的通信接口
> dubbo.protocol.port=7070
> #监控中心 web 页面的访问端口
> dubbo.jetty.port=8080
> dubbo.jetty.directory=${user.home}/monitor
> dubbo.charts.directory=${user.home}/monitor/charts
> dubbo.statistics.directory=${user.home}/monitor/statistics
> dubbo.log4j.file=logs/dubbo-monitor-simple.log
> dubbo.log4j.level=WARN
>
> #这个配置文件主要保证注册中心的配置是正确的就可以了
> ```
>
> 9、进入 dubbo-monitor-simple/target/dubbo-monitor-simple-2.0.0/assembly.bin 目录下执行 start.sh 启动监控中心

**小讲解：**

监控中心启动成功后，要想要监控到服务的一些调用信息，还需要在服务提供者和服务消费者中配置一些信息

```xml
provider.xml
省略一大段 dubbo 配置.....
 <!--
        配置监控中心
        方式1：
        就是写 protocol="registry" 表示当前应用从注册中心去自动发现监控中心的地址（因为你监控中心也会注册到注册中心去，所以可以从注册中心发现）
        方式2：
        就是写 address  这种方式是直连监控中心，这里就是写监控中心的 ip 和端口
        两种方式选一种即可
    -->
    <!--方式1-->
    <dubbo:monitor protocol="registry"></dubbo:monitor>
     <!--方式2-->
    <!--<dubbo:monitor address="127.0.0.1:7070"></dubbo:monitor>-->

----------------------------------------------------------------------------------
consumer.xml
省略一大段 dubbo 配置.....
 <dubbo:monitor protocol="registry"></dubbo:monitor>
```

### 6、dubbo 与 SpringBoot 整合

首先服务提供者和服务消费者的 Java Bean 和 service 实现类等和之前写的都是一样，在这里我们就默认已经写好了

controller

```java
@Controller
public class OrderController {

    @Autowired
    private OrderService orderService;
    
	@RequestMapping("/initOrder")
    public void initOrder(@RequestParam("uid") String userid) {
        orderService.initOrder(userid);   //orderService 的实现类里面其实我调用了 userService,具体可以参考上面
    }
}
```

下面我们先来配置**服务提供者**

① 导入依赖

```xml
<!-- 在服务提供者 pom 文件中添加以下依赖 -->
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>0.2.0</version>
</dependency>
```

但是这里 starter 的版本选择需要小小注意一下，可以参考链接：https://github.com/apache/incubator-dubbo-spring-boot-project

![springboot 整合 dubbo启动器版本选择](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/springboot%E6%95%B4%E5%90%88%20dubbo%E5%90%AF%E5%8A%A8%E5%99%A8%E7%89%88%E6%9C%AC%E9%80%89%E6%8B%A9.png)

等你把依赖添加到 pom 文件中以后，你就会发现，这个 starter 自动帮你引入了 dubbo、curator、zookeeper 相关的 jar 包

② 配置

以前我们配置服务提供者的时候需要写一个 spring 配置文件，现在我们使用了 spring boot ，之前所有在 spring 配置文件中配置的内容现在在 application.properties 文件中都能配置

```properties
application.properties:

# 当前应用的名称
dubbo.application.name=user-service-provider
# 指定注册中心的地址
dubbo.registry.address=127.0.0.1:2181
# 注册中心的协议，我们注册中心使用的是 zookeeper
dubbo.registry.protocol=zookeeper
# dubbo 的通信协议，我们使用的是 dubbo 协议
dubbo.protocol.name=dubbo
# 通信端口
dubbo.protocol.port=20880
# 监控中心，让它去注册中心自动发现
dubbo.monitor.protocol=registry
```

上面的配置在 application.properties 配置文件中都配置完以后，还有一个就是对外暴露哪些服务没有配置，之前我们是在 spring 的配置文件中通过 

`<dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl"></dubbo:service>>`

来实现的，但是假如我们要暴露的服务有很多我就要写很多这样的标签，所以还有一种更简单的方式：

```java
//UserServiceImpl.java:
@Service
@Component
public class UserServiceImpl implements UserService {
    @Override
    public List<UserAddress> getUserAddressList(String userId) {
        UserAddress address1 = new UserAddress(1,"合肥市","11","收货人1","110","0");
        UserAddress address2 = new UserAddress(2,"芜湖市","22","收货人2","110","0");
        return Arrays.asList(address1,address2);
    }
}
//我们只需要在需要对外暴露的服务的实现上标上 @Service 注解接口，不过这个 @Service 注解是 com.alibaba.dubbo.config.annotation.Service; dubbo 家的 service 注解，之前 spring 的 @Service 注解换成 @Component 注解

//-----------------------------------------------------------------------------------
主程序类：
@SpringBootApplication
@EnableDubbo   //开启基于注解的 dubbo 功能
public class BootUserServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootUserServiceApplication.class, args);
    }
}
// 最后别忘了在主程序类上使用 @EnableDubbo 开启基于注解的 dubbo 功能
```

下面来配置**服务消费者**

① 导入依赖

```xml
<!-- 在服务提供者 pom 文件中添加以下依赖 -->
<dependency>
    <groupId>com.alibaba.boot</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>0.2.0</version>
</dependency>
```

② 配置

```properties
application.properties:

# 当前应用的名称
dubbo.application.name=order-service-consumer
# 注册中心的地址
dubbo.registry.address=zookeeper://127.0.0.1:2181
# 配置监控中心
dubbo.monitor.protocol=registry
```

上面的配置在 application.properties 配置文件中都配置完以后，还有一个就是我们还要在配置文件中配置调用哪个远程服务的接口，之前我们是在 spring 的配置文件中通过 

 `<dubbo:reference interface="com.iflytek.service.UserService" id="userService"></dubbo:reference>`

来实现的，但是之后我们调用的远程服务可能会有很多，不可能在这里一个一个配置出来，所以还有一种更简单的方式：

```java
@Service
public class OrderServiceImpl implements OrderService {
    
//    @Autowired     这里我们就不适用 @Autowired 注解来注入了
    @Reference
    private UserService userService;
    @Override
    public void initOrder(String userid) {

        List<UserAddress> userAddressList = userService.getUserAddressList("11");
        System.out.println("------" + userAddressList.get(0));
    }
}
//我们使用 dubbo 为我们提供的一个注解 @Reference，这个注解就是帮我们去远程引用 userService 这个服务(@Reference 它会在注册中心里面发现)

//-----------------------------------------------------------------------------------
主程序类：
@SpringBootApplication
@EnableDubbo
public class BootOrderServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(BootOrderServiceApplication.class, args);
	}
}
//最后别忘了使用 @EnableDubbo 开启基于注解的 dubbo 功能
```

### 7、dubbo 配置文件加载顺序

![dubbo 配置文件加载顺序](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/dubbo%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E5%8A%A0%E8%BD%BD%E9%A1%BA%E5%BA%8F.png)

如果配置了虚拟机参数，那么优先加载虚拟机参数相关的 dubbo 配置，其次加载 dubbo.xml 中相关的 dubbo 配置（如果与 springboot 整合那么加载 application.properties 中相关的 dubbo 配置，这两个优先级是一样的，只不过区别在于有没有与 springboot 整合），最后加载 dubbo.properties 中相关的配置

### 8、dubbo 启动检查

#### 1、简介

这个启动时检查在开发中也非常常用，在开发中我们会有一个服务提供者，一个服务消费者，这两个服务都会注册到注册中心去，消费者会从注册中心获取提供者的调用地址，假如在注册中心中没有提供者，你就启动了消费者，启动的时候就会报错。因为 dubbo 默认会在启动时检查依赖的服务是否可用，不可用会抛出异常，阻止 Spring 初始化完成，以便上线时，能及早发现问题。可以通过 ==check=false==关闭检查.。

如果你没有关闭检查，直接启动服务消费者，没有启动服务提供者，就会报下面的错误：

![未关闭启动检查报错信息](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/%E6%9C%AA%E5%85%B3%E9%97%AD%E5%90%AF%E5%8A%A8%E6%A3%80%E6%9F%A5%E6%8A%A5%E9%94%99%E4%BF%A1%E6%81%AF.png)

#### 2、关闭启动检查的方式

1️⃣ **通过 spring 配置文件**

```Xml
<!-- 
服务消费者的配置文件中，dubbo:reference 标签中
使用 check="false" 
 --> 
<dubbo:reference check="false" interface="com.iflytek.service.UserService" id="userService" ></dubbo:reference>

<!-- 
假如我们服务消费者的配置文件声明了很多调用远程服务的接口，那么按照上面的方法，在每一个声明的接口上都写上 check=false 这样也比较麻烦
-->
<!-- 我们可以使用这个标签来配置当前消费者的统一规则 -->
<dubbo:consumer check="false"></dubbo:consumer><!-- 这个意思就是当前消费者声明的所有调用远程服务的接口都不检查,但是如果你 dubbo:reference 中显示的配置了 check=false,以 dubbo:reference 中配置的为准，相当于这个 dubbo:consumer 标签为整个消费者提供一个默认值，如果你没配置就用 dubbo:consumer 中的，如果你在具体标签中配置了，就用你具体标签中配置的 -->

<!-- 甚至你可以配置一个下面的标签，下面的标签意思就是启动时不检查，你启动时候如果没有注册中心也不会报错 -->
<dubbo:registry check="false"></dubbo:registry>
```

2️⃣ **通过 dubbo.properties**

```properties
dubbo.reference.com.iflytek.service.UserService.check=false
dubbo.reference.check=false
dubbo.consumer.check=false
dubbo.registry.check=false
```

### 9、dubbo 超时配置和配置的覆盖关系

#### 1、配置超时基本介绍

服务消费者在调用服务提供者的时候，可能由于网络原因或者等等原因，服务提供者执行一个方法
需要很长的时间，如果很长时间都没有返回，导致大量线程都阻塞在那里，这样就可能会引起性能下降
我们为了解决这个问题，可以指定一个超时属性，只要你在指定时间内没有返回，那么我就立即终止
不让线程大量阻塞

Spring 配置文件（服务消费者 consumer.xml）：

```Xml

<?xml version="1.0" encoding="UTF-8"?>
<beans>
    <!--
        timeout:默认值是 1000 单位是 ms
        你在这里把 timeout 设置了 3000，当你在服务消费者里面调用服务提供者服务的时候
        如果超过三秒没有响应，就调用失败
    -->
    <dubbo:reference interface="com.iflytek.service.UserService"
                     id="userService" check="false" timeout="3000">
        <!--
            这个 timeout 不仅能配置在你要远程调用按个接口服务上，
            还能具体配置到你要调用的接口服务的哪个方法上
            在这里我就明确指定了调用这个方法的超时时间
        -->
        <dubbo:method name="getUserAddressList" timeout="2000"></dubbo:method>
    </dubbo:reference>
    <!--
        同样的我们也可以在 dubbo:consumer 中为消费者整体设置超时时间
    -->
    <dubbo:consumer check="false" timeout="2000"></dubbo:consumer>
    
    <!--
 		总结一下，我们看到了上面有三个地方都可以配置 timeout 属性，那么优先级是怎么样的呢？
		答：「方法级优先，接口级次之，全局配置再次之」
		1.dubbo:method   优先级最高
		2.dubbo:reference   优先级次之
		3.dubbo:consumer    优先级最低
	-->
</beans>
```

除了服务消费者可以配置超时时间外，服务提供者本身也可以配置超时时间

Spring 配置文件（服务提供者 provider.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans>
    <!--
      服务提供者配置超时时间 timeout
    -->
    <dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl" timeout="2000">
        
    </dubbo:service>
    <!--服务的实现-->
    <bean id="userServiceImpl" class="com.iflytek.service.impl.UserServiceImpl"></bean>
    
  <!--
 		总结一下，如果服务提供者和服务消费者都配置了超时时间，那么优先级是怎么样的呢？
		答：「如果级别一样，则消费方优先，提供方次之」
		也就是说，比如服务提供者和服务消费者的超时时间都配置在接口级层面，那么优先级就是，消费者优先
		但是如果级别不一样，按照之前说的，「方法级优先，接口级次之，全局配置再次之」
	-->   
</beans>
```

#### 2、配置的覆盖关系

以 timeout 为例，显示了配置的查找顺序，其它 retries, loadbalance, actives 等类似：

- 方法级优先，接口级次之，全局配置再次之。
- 如果级别一样，则消费方优先，提供方次之。

![配置覆盖关系](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/%E9%85%8D%E7%BD%AE%E8%A6%86%E7%9B%96%E5%85%B3%E7%B3%BB.png)

建议由服务提供方设置超时，因为一个方法需要执行多长时间，服务提供方更清楚，如果一个消费方同时引用多个服务，就不需要关心每个服务的超时设置。

### 10、重试次数

前面我们说的 timeout 属性一般配合 retries 来使用。当我们某一个服务因为网络不佳，或者运行缓慢导致超时时，就会远程调用失败，这时我们就可以通过重试次数让它多试几次

```xml
    <!--
        retries：重试次数，不包含第一次调用（比如说你设置 3 那么第一次调用失败后它还会重试 3 次）
		配置成 0 就表示不重试
    -->
    <dubbo:reference interface="com.iflytek.service.UserService"
                     id="userService" check="false" timeout="3000" retries="3">
        <dubbo:method name="getUserAddressList" timeout="2000"></dubbo:method>
    </dubbo:reference>
```

但是注意下，这个重试次数，一般我们在幂等操作时设置重试次数，在非幂等操作不设置重试次数（可以把方法分为迷幂等操作和非幂等操作）。对于幂等操作，意思就是方法不管调用多少次它最终运行都是一个效果，比如说我们对数据库的查询、删除、修改操作。对于非幂等操作，意思就是方法每一次运行都会产生新的效果，比如数据库的新增操作

### 11、多版本

这个多版本它的使用场景就是，当我们的某一个接口，它出现了不兼容的升级，而我们系统里面不可能刚写完的新功能就立马给他全用上，有可能新开发的功能不稳定导致系统不稳定。所以我们可以在系统里面先让一部分人先用我们开发的新功能，另一部分的人继续用我们老版本的功能，等新功能都稳定了，我们再把老版本都替换成新版本。

服务提供者：

```java
// 我现在一个接口实现类，可能会有两个版本，一个是老版本，一个是新开发的版本
// UserServiceImpl.java
@Service
@Component
public class UserServiceImpl implements UserService {
    @Override
    public List<UserAddress> getUserAddressList(String userId) {
        System.out.println("old");
        UserAddress address1 = new UserAddress(1,"合肥市","11","收货人1","110","0");
        UserAddress address2 = new UserAddress(2,"芜湖市","22","收货人2","110","0");
        return Arrays.asList(address1,address2);
    }
}

// -------------------------------------------------------------------------------
// UserServiceImpl2.java
@Service
@Component
public class UserServiceImpl implements UserService {
    @Override
    public List<UserAddress> getUserAddressList(String userId) {
        System.out.println("new");
        UserAddress address1 = new UserAddress(1,"合肥市","11","收货人1","110","0");
        UserAddress address2 = new UserAddress(2,"芜湖市","22","收货人2","110","0");
        return Arrays.asList(address1,address2);
    }
}

```

provider.xml:

```xml
	<!--
        4、暴露服务
        ref : 指向服务的真正实现对象
    -->
    <dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl" version="1.0.0"> </dubbo:service>
	<!--服务的实现-->
    <bean id="userServiceImpl" class="com.iflytek.service.impl.UserServiceImpl"></bean>

<!-- 这里我配置了我们新开发的版本，版本号记为 2.0.0 -->
 <dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl2" version="2.0.0"> </dubbo:service>
	<!--服务的实现-->
    <bean id="userServiceImpl2" class="com.iflytek.service.impl.UserServiceImpl2"></bean>

上面两个新旧版本同时存在
```

consumer.xml:

```xml
这样我们在服务消费方调用的时候，就可以通过版本号来决定调用哪个版本的服务
<dubbo:reference interface="com.iflytek.service.UserService"
                     id="userService" version="1.0.0">  <!-- 这里就表示调用老版本的服务 -->
 </dubbo:reference>

<!-- 如果你不区分版本的话，version 可以配置成 * ，这样它就会随机调用(新版本，老版本随机调用) -->
<dubbo:reference interface="com.iflytek.service.UserService" id="userService" version="*"> 
 </dubbo:reference>
```

这样我们就可以平滑的从旧版本升级到新版本，其实也可以理解为类似灰度发布的功能

### 12、本地存根

我们的服务消费者要调用服务提供者，是通过远程调用的，功能的实现都是在服务提供者那端。服务消费者相当于只有服务的接口，每次要调用时，我们都是通过 dubbo 连向远程的 provider 来调用这些实现类。但是，有时候我们在调用真正的实现之前可能需要做一些参数校验或者缓存，这个时候我们就可以编写一个**本地存根**。这个本地存根代码我们可以在调用真正的远程服务之前做一些判断或者小功能，如果满足我们的要求，我再利用这个远程代理对象去调用远程服务，如果不满足，那我就不用调用了。

在消费方写一个远程接口的本地存根实现：

（必须要有一个有参构造器，这个有参构造器传入的是我们远程接口的代理实现）

```java
// UserServiceImplStub.java
public class UserServiceImplStub implements UserService {

    private final UserService userService;

    /**
     * 这个有参构造器是必须要的
     * 这样 dubbo 就会自动创建这个 UserServiceImplStub 对象
     * 并且传入 UserService 的远程代理对象
     */
    public UserServiceImplStub(UserService userService) {
        this.userService = userService;
    }

    @Override
    public List<UserAddress> getUserAddressList(String userId) {
        // 本地存根代码中对参数先进行判断，满足要求我在调用远程服务
        if (!StringUtils.isEmpty(userId)) {
            List<UserAddress> list = userService.getUserAddressList(userId);
        }
        return null;
    }
}
```

写好之后怎么配置呢？

consumer.xml:

```xml
<!-- 这样消费者在远程调用之前就会先来调用本地存根的代码 -->
<!--配置本地存根-->
    <dubbo:reference interface="com.iflytek.service.UserService" stub="com.iflytek.service.impl.UserServiceImplStub"></dubbo:reference>
<!-- stub 配置本地存根的全类名即可 -->
```

### 13、dubbo 与 springBoot 整合的三种方式

1、导入 dubbo-starter，在 application.properties 中配置属性，使用 @Service 暴露服务，@Reference 引用服务

> - 使用这种方式一定要注意，一定要加上 `@EnableDubbo` 注解（开启基于 dubbo 注解功能），现在我们使用这个注解主要作用就是包扫描，你点进去这个注解会发现它里面有个 `@DubboComponentScan`注解，用来进行包扫描。或者你也可以直接在 springboot 的配置文件 application.properties 中，配置：
>
> ```properties
> # 用来扫描 @Service 或 @Reference 注解所在类
> dubbo.scan.base-packages=com.iflytek.service
> ```
>
> 这样你就可以不用写  `@EnableDubbo` 注解了，你写了也会被配置文件中的这个配置给覆盖
>
> - 我们在 provider.xml 中配置的相关配置，在 @Service 注解中其实都有对应的属性
>
> ```xml
> <dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl" version="1.0.0">
> </dubbo:service>
> ```
>
> ```java
> @Service(timeout = 5,version = "1.0.0")
> @Component
> public class UserServiceImpl implements UserService {
>     @Override
>     public List<UserAddress> getUserAddressList(String userId) {
>         System.out.println("new");
>         UserAddress address1 = new UserAddress(1,"合肥市","11","收货人1","110","0");
>         UserAddress address2 = new UserAddress(2,"芜湖市","22","收货人2","110","0");
>         return Arrays.asList(address1,address2);
>     }
> }
> ```
>
> - 但是这种方式不能做到方法级别的精确配置
>
> ```xml
> <!-- 意思就是说我在 dubbo:service 里面还能配置具体的方法(通过配置 xml 的方式)，但是通过注解的方式做不到，所以我们就可以采用第二种方法 -->
> <dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl" version="1.0.0">
>     <!-- 通过 xml 的方式可以精确到具体方法的配置 -->
>     <dubbo:method name="getUserAddressList" timeout="2"></dubbo:method>
> </dubbo:service>
> ```
>
> 

2、保留 dubbo 的 xml 配置文件

> - 就是 dubbo 与 springboot 集成的时候，我们依然保留 dubbo.xml 的配置，所有关于 dubbo 的配置我们还在这个 xml 中去配置，这样在 application.properties 中我们就不需要去配置 dubbo 了
>
> 假设现在有一个 springboot 的服务提供者，你保留了 provider.xml 这个 dubbo 配置文件，并在里面配置好了 dubbo 的所有配置，那么下一步要做的就是：
>
> ```java
> @SpringBootApplication
> // 加上这个注解之后，它就会去加载你类路径下的 provider.xml 配置文件
> @ImportResource(locations = "classpath:provider.xml")
> public class BootUserServiceApplication {
>
>     public static void main(String[] args) {
>         SpringApplication.run(BootUserServiceApplication.class, args);
>     }
> }
>
>
> //-------------------------------------------------------------------------------
> UserServiceImpl.java:
> // 这时候，你 @Service 注解就可以不用写了，因为你在 provider.xml 中已经配置了
> //@Service(timeout = 5,version = "1.0.0")
> @Component
> public class UserServiceImpl implements UserService {
>     @Override
>     public List<UserAddress> getUserAddressList(String userId) {
>         System.out.println("new");
>         UserAddress address1 = new UserAddress(1,"合肥市","11","收货人1","110","0");
>         UserAddress address2 = new UserAddress(2,"芜湖市","22","收货人2","110","0");
>         return Arrays.asList(address1,address2);
>     }
> }
> ```
>
> 

**总结一下使用第二种方式的步骤：**

① 导入 dubbo-starter

② 保留 dubbo 的 xml 配置文件，继续在 xml 配置文件中配置 dubbo 相关信息

③ 在 springboot 主配置类上使用 `@ImportResource(locations = "classpath:provider.xml")`去加载配置文件

3、使用注解 API 的方式

> - 将每一个组件手动创建到容器中，让 dubbo 来扫描你配置的组件即可
> - 这种方式其实就是我一个 springboot 项目，不保留 dubbo.xml 这个文件，而是把这个文件中的配置改用 java 代码来实现

```java
@Configuration
public class MyDubboConfig {

    /**
     * <dubbo:application name="user-service-provider"></dubbo:application>
     * dubbo 的每一个标签都有对应着的 xxxConfig 类
     * 相当于我们用这个 bean 替代了 dubbo:application 标签
     */
    @Bean
    public ApplicationConfig applicationConfig() {
        ApplicationConfig applicationConfig = new ApplicationConfig();
        applicationConfig.setName("user-service-provider");
        return applicationConfig;
    }

    /**
     * <dubbo:registry address="zookeeper://127.0.0.1:2181"></dubbo:registry>
     * @return
     */
    @Bean
    public RegistryConfig registryConfig() {
        RegistryConfig registryConfig = new RegistryConfig();
        registryConfig.setAddress("zookeeper://127.0.0.1:2181");
        return registryConfig;
    }

    /**
     * <dubbo:protocol name="dubbo" port="20080"></dubbo:protocol>
     * @return
     */
    @Bean
    public ProtocolConfig protocolConfig() {
        ProtocolConfig protocolConfig = new ProtocolConfig();
        protocolConfig.setName("dubbo");
        protocolConfig.setPort(20080);
        return protocolConfig;
    }

    /**
     *  <dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl" version="1.0.0">
            <dubbo:method name="getUserAddressList" timeout="2"></dubbo:method>
        </dubbo:service>

       说一下这个参数 userService ，这个参数的实现类 userServiceImpl 我们在写的时候已经
        把它加入到 IOC 容器中了，所以你这里传入 userService ，springboot 会自自动从 IOC
        容器中获取他的实现类
     * @param userService
     * @return
     */
    @Bean
    public ServiceConfig<UserService> userServiceConfig (UserService userService) {
        ServiceConfig<UserService> userServiceConfig = new ServiceConfig<>();
        userServiceConfig.setInterface("com.iflytek.service.UserService");
        // springboot 会自动去 IOC 容器中找他的实现类
        userServiceConfig.setRef(userService);
        userServiceConfig.setVersion("1.0.0");

        List<MethodConfig>methods = new ArrayList<MethodConfig>();
        //配置每一个 method 的信息
        MethodConfig methodConfig = new MethodConfig();
        methodConfig.setName("getUserAddressList");
        methodConfig.setTimeout(2);
        methods.add(methodConfig);
        userServiceConfig.setMethods(methods);
        return userServiceConfig;
    }

}
```

```java
@SpringBootApplication
// 这里指定 dubbo 要扫描的组件，包括 @Service 注解和刚写的配置类
@EnableDubbo(scanBasePackages = "com.iflytek")
public class BootUserServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(BootUserServiceApplication.class, args);
    }
}
```

```java
// 这里的 @Service 注解要写上，因为没有使用 provider.xml 配置文件
@Service(timeout = 5,version = "1.0.0")
@Component
public class UserServiceImpl implements UserService {
    @Override
    public List<UserAddress> getUserAddressList(String userId) {
        System.out.println("new");
        UserAddress address1 = new UserAddress(1,"合肥市","11","收货人1","110","0");
        UserAddress address2 = new UserAddress(2,"芜湖市","22","收货人2","110","0");
        return Arrays.asList(address1,address2);
    }
}
```

**总结一下使用第三种方式的步骤：**

①在 springboot 项目中不保留 provider.xml 配置文件了，新建一个使用 @Configuration 修饰的自定义 dubbo 配置类，provider.xml 配置文件中的配置都可以在你自定义的配置类中通过 java 代码实现。在 provider.xml 中每一个标签在你定义的配置类中都有一个 xxxConfig 与之对应。

② 在 springboot 的主程序上指定要扫描的包（需要扫描到 @Service 注解和你刚创建的 dubbo 配置类）

## 三、dubbo 高可用

#### 1、zookeeper 宕机与 dubbo 直连

**现象：**

zookeeper 注册中心宕机，还可以消费 dubbo 暴露的服务

**说明：**

==注册中心全部宕掉以后，服务提供者和服务消费者扔能通过本地缓存通讯==，因为这时候你注册中心虽然宕机，但是服务提供者和服务消费者已经通过信，在本地有缓存，即使服务宕掉，也能通过本地缓存进行通讯。

**dubbo 直连：** 

其实我们仔细想一下，这个注册中心的作用不外乎就是去记录服务提供者的位置信息的，所以我们完全可以绕过注册中心，使用 dubbo 直接的方式

```java
@Service
public class OrderServiceImpl implements OrderService {
    //采用 dubbo 直连的方式，在 url 中直接指出服务提供者的 ip 和端口
    @Reference(url = "127.0.0.1:21882")
    private UserService userService;
    @Override
    public void initOrder(String userid) {
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        List<UserAddress> userAddressList = userService.getUserAddressList("11");
        System.out.println("------" + userAddressList.get(0));
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

// 这样即使注册中心宕掉，使用上面 dubbo 直连的方式，消费者和提供者之间也是可以通信的
```

#### 2、集群下 dubbo 负载均衡配置

在集群负载均衡时，dubbo 提供了多种负载均衡策略，缺省为 `random` 随机调用

##### 1、基于权重的随机负载均衡机制

![基于权重的随机负载均衡机制](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/%E5%9F%BA%E4%BA%8E%E6%9D%83%E9%87%8D%E7%9A%84%E9%9A%8F%E6%9C%BA%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E6%9C%BA%E5%88%B6.png)

比如这时候我们 OrderService 想要调用 UserService，UserService 现在在三台机器上，我们可以为每一台机器上的 UserService 设置权重，那么根据图上设置的权重，假如这时候我有非常多的请求过来了，那么大约会有 2/7 的请求进入到 1 号机器，大约会有 4/7 的请求会进入到 2 号机器，大约会有 1/7 的请求会进入到 3 号机器。再说一个，假如我现在一个请求过来了，调用的是 1号机器，那么下一次请求不一定是调用 2 号机器或者 3 号机器，有可能又是 1 号机器，只能说在非常多请求的情况下， 1 号机器被调用的概率是 2/ 7。

##### 2、基于权重的轮询负载均衡机制

![基于权重的轮询负载均衡机制](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/%E5%9F%BA%E4%BA%8E%E6%9D%83%E9%87%8D%E7%9A%84%E8%BD%AE%E8%AF%A2%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E6%9C%BA%E5%88%B6.png)

我们首先忽略掉权重，只看轮询，假设它就是轮询机制，那么假如我一个请求过来了，先调用 1 号机器，再由一个请求过来就调用 2 号机器，再有一个请求过来就调用 3 号机器，再有一个请求过来就调用 1 号机器，如此循环往复，这就是轮询机制。那么现在加上基于权重的轮询负载均衡机制，假如我现在有 7 次请求，对照着上图，现在我第一次请求过来，按照轮询机制，调用 1 号机器，第二次请求过来，按照轮询机制调用 2 号机器，第三次请求过来，按照轮询机制，调用 3 号机器，第四次请求过来，按照轮询机制，调用 1 号机器，第五次请求过来，按照轮询机制，调用 2 号机器，**第 6 次请求过来，这时候注意了，我们是又权重的，按照轮询机制，是应该调用 3 号机器，但是 3 号机器只占 1/7 在前面已经轮询过了，所以跳过 3 号机器，按照轮询，这时候应该调用 1 号机器，但是 1 号机器只占 2/7，在前面也已经轮询过了，所以这个时候会去调用 2 号机器，同理第七次请求也会去调用 2 号机器**

##### 3、最少活跃数负载均衡机制

![最好活跃数负载均衡机制](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/%E6%9C%80%E5%B0%91%E6%B4%BB%E8%B7%83%E6%95%B0%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E6%9C%BA%E5%88%B6.png)

还是 OrderService 去调用 UserService ，在这种机制下，它每次去调用的时候都会去问一下，这台机器上次处理请求花了多长时间，比如说 1 号机器处理上次请求花了 100 ms ， 2 号机器处理上次请求花了 1000ms ，3 号机器处理上次请求花了 300ms 那么说明 1 号机器处理请求快（响应速度快），所以这次请求我就交个 1 号机器来处理，等到下次请求再来的时候，同样会看下 3 台机器上次处理请求的时间，把请求交个时间短的机器。

##### 4、一致性 hash 负载均衡机制

![一致性 hash 负载均衡机制](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/%E4%B8%80%E8%87%B4%E6%80%A7%20hash%20%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1%E7%AD%96%E7%95%A5.png)

还是 OrderService 去调用 UserService，假如我调用的是 getUser 方法，id=1 的我都来到 1 号机器，id=2 的我都来到 2 号机器，id=3 的我都来到 3 号机器，即使很多次请求都是 id=1 的那么我这些请求也都是来到 UserService

**下面说一下如何配置负载均衡：** 

你可以在服务提供者端进行配置，也可以在服务消费者端进行配置

**服务提供者：**

```xml
<!-- 使用 loadbalace 进行配置 -->
<dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl" loadbalance="roundrobin">
 </dubbo:service>
```

**服务消费者：**

```xml
<!-- 使用 loadbalace 进行配置 -->
<dubbo:reference  interface="com.iflytek.service.UserService"
                    loadbalance="roundrobin" id="userService">   
 </dubbo:reference>
```

**服务提供者方法级别：**

```xml
<dubbo:service interface="com.iflytek.service.UserService" ref="userServiceImpl">
    	<!-- 使用 loadbalace 进行配置 -->
        <dubbo:method name="getUserAddressList" loadbalance="roundrobin"></dubbo:method>
</dubbo:service>
```

**服务消费者方法级别：**

```xml
<dubbo:reference  interface="com.iflytek.service.UserService"id="userService">
   	<!-- 使用 loadbalace 进行配置 -->
 	<dubbo:method name="getUserAddressList" loadbalance="roundrobin"></dubbo:method>
</dubbo:reference>
```

![loadbalance 实现类](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/loadbalance%E5%AE%9E%E7%8E%B0%E7%B1%BB.png)

在配置负载均衡的时候，你如果不知道 loadbalace 的值该写什么，上面四个实现类点进去看一下就知道了

**注：**

如果你使用注解的方式配置 dubbo ，在 @Service 注解或者 @Reference 注解里面也会有 loadbalance 属性，正常来配置就可以了。

#### 3、服务降级

这个服务降级也是在分布式系统里面来提升服务高可用的常用手段之一

下面说下什么是服务降级？

当服务器压力剧增的情况下，根据实际业务情况和流量，对一些服务和页面有策略的不处理或换种简单的方式处理，从而释放服务器资源以保证核心交易正常运作或高效运作。

dubbo 支持两种服务降级：

> - `mock=force:return+null`  表示消费方对该服务的方法调用都直接返回 null 值，==不发起远程调用==。用来屏蔽不重要服务不可用时对调用方的影响
> - `mock=fail:return+null`   表示消费方对该服务的方法调用在失败后，再返回 null 值，不抛异常。用来容忍不重要服务不稳定时对调用方的影响

那么这个设置怎么设置呢？

其实非常简单，我们可以直接通过 dubbo-admin 控制台，在消费者层面进行控制

![服务降级-不进行远程调用](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/%E6%9C%8D%E5%8A%A1%E9%99%8D%E7%BA%A7-%E4%B8%8D%E5%8F%91%E8%B5%B7%E8%BF%9C%E7%A8%8B%E8%B0%83%E7%94%A8.png?q-sign-algorithm=sha1&q-ak=AKIDgxatzcj7mFbibIeZzdQlnxJtVXwDeGgr&q-sign-time=1547393178;1547394978&q-key-time=1547393178;1547394978&q-header-list=&q-url-param-list=&q-signature=2f2d86e2f7d882ec1da2de24ccda22bbb2ce8e73&x-cos-security-token=30cfe19f31a766ebe13b13d28a5f72a3933aad2c10001)

我们可以直接在消费者层面，在页面上点击「屏蔽」按钮，这个意思就是消费者将不会发起远程调用，直接在页面上返回空对象（对应上面我们说的第一种服务降级）

![服务降级-进行远程调用](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/%E6%9C%8D%E5%8A%A1%E9%99%8D%E7%BA%A7-%E8%BF%9B%E8%A1%8C%E8%BF%9C%E7%A8%8B%E8%B0%83%E7%94%A8.png?q-sign-algorithm=sha1&q-ak=AKIDUJS51ouJ5vA8L3MSaWtMEDVQRkjqiqVy&q-sign-time=1547393135;1547394935&q-key-time=1547393135;1547394935&q-header-list=&q-url-param-list=&q-signature=640a593987ea72ca1a463db9b9da109985d359f9&x-cos-security-token=1723dd0749addb0be222c5694f6763e846ec7c9710001)

我们可以直接在消费者层面，在页面上点击「容错」按钮，这个意思就是消费者调用提供者，当调用失败后，返回空的对象（对应我们上面说的第二种服务降级）

#### 4、集群容错

在集群调用失败时，Dubbo 提供了多种容错方案，缺省为 `failover` 重试

集群容错模式：

> **Failover Cluster**
>
> 失败自动切换，当出现失败，重试其他服务器。通常用于读操作，但重试会带来更长时间延迟。可通过 retries = "2" 来设置重试次数（不含第一次）
>
> ```xml
> 重试次数配置如下：
> <dubbo:service interface="com.iflytek.service.UserService" retries="2">       
>  </dubbo:service>
> 或者
> <dubbo:reference  interface="com.iflytek.service.UserService" retries="2">      
> </dubbo:reference>
> ```
>
> **Failfast Cluster**
>
> 快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的操作，比如新增记录
>
> **Failsafe Cluster**
>
> 失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作
>
> **Failback Cluster**
>
> 失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。这个简单解释下，就是比如 A 服务去调用 B 服务，这时候调用失败了，我后台记录失败日志，然后过一段时间重新再调用一下 B 服务。
>
> **Forking Cluster**
>
> 并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过 `forks="2"` 来设置最大并行数。这个也简单解释下，比如说我 A 服务现在调用 B 服务，B 服务现在在四五台服务器上都有，那么我就可以并行调用者四五台机器上的服务，只要有一个成功即返回。
>
> **Broadcast Cluster**
>
> 广播调用所有提供者，逐个调用，任意一台报错则报错。通常用于通知所有提供者更新缓存或日志等本地资源信息。

集群模式的配置：

```xml
<dubbo:service interface="com.iflytek.service.UserService" cluster="fallback"></dubbo:service>
或者
<dubbo:reference  interface="com.iflytek.service.UserService" cluster="fallback">
 </dubbo:reference>

如果你不配置这个，默认使用 Failover(失败自动切换)
```

#### 5、整合 hystrix

1、配置 `spring-cloud-starter-netflix-hystrix`

springboot 官方提供了对 hystrix 的集成，直接在 pom.xml 中添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>
```

然后在 Application 类上增加 @EnableHystrix 来启用 hystrix starter

```java
@EnableHystrix    // 开启服务容错
@SpringBootApplication
public class UserServiceProviderApplication {

	public static void main(String[] args) {
		SpringApplication.run(UserServiceProviderApplication.class, args);
	}
}
```

2、配置 provider 端

```java
@Service
public class UserServiceImpl implements UserService {

    //加了这个注解之后，这个方法就会被 hystrix 代理，出了异常以后就可以进行容错
    @HystrixCommand
    @Override
    public List<UserAddress> getUserAddressList(String userId) {
        UserAddress address1 = new UserAddress(1,"合肥市","11","收货人1","110","0");
        UserAddress address2 = new UserAddress(2,"芜湖市","22","收货人2","110","0");

        // 在这我来模拟一个异常
        if (Math.random() > 0.5) {
            throw new RuntimeException();
        }
        return Arrays.asList(address1,address2);
    }
}
```

3、配置 consumer 端

消费端，同样你需要在 pom 文件中添加 hystrix starter 依赖，然后在主配置类中使用 @EnableHystrix 注解来开启容错

其次是：

```java
@Service
public class OrderServiceImpl implements OrderService {
    @Autowired
    private UserService userService;
	
    // 在这里同样使用这个注解标记，代表把这个方法交给 hystrix 代理，一旦方法里面远程调用失败或者出现异常，我就调用 hello 方法
    @HystrixCommand(fallbackMethod = "hello")
    @Override
    public void initOrder(String userid) {
        List<UserAddress> userAddressList = userService.getUserAddressList("11");
        System.out.println("------" + userAddressList.get(0));
    }

    public String hello() {
        return "调用出错了，使用这个方法进行容错";
    }
}
```

## 四、dubbo 原理

#### 1、RPC 原理

![RPC原理图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-dubbo/rpc%E5%8E%9F%E7%90%86%E5%9B%BE.png?q-sign-algorithm=sha1&q-ak=AKID8uRBWdxd8towtr1yHDJP039vjmicZeo3&q-sign-time=1547483096;1547484896&q-key-time=1547483096;1547484896&q-header-list=&q-url-param-list=&q-signature=62379fc0a606a8d1b83720d8395d377256aa58c2&x-cos-security-token=37d659c860fb13f641de5d39b84dcbfee5f3ff3910001)

**一次完整的 RPC 调用流程如下：**

> 1. **服务消费方（client）调用，以本地调用方式调用服务**
> 2. client stub 接收到调用后，负责将方法、参数等组装成能够进行网络传输的消息体
> 3. client stub 找到服务地址，并将消息发送到服务端
> 4. sever stub 收到消息后进行解码
> 5. server stub 根据解码后的结果调用本地的服务
> 6. 本地服务执行并将结果返回给 server stub
> 7. server stub 将返回结果打包成消息体并发送至消费方
> 8. client stub 接收到消息，并进行解码
> 9. **服务消费方得到最终结果**

RPC 框架的目标就是要把 2~8 这些步骤都封装起来，这些细节对用户来说是不可见的。

对照着上面的步骤，我们使用 dubbo 的时候，其实只涉及到了上面的第一步和第九步，我们使用面向接口的方式调用一个远程方法，然后我们直接获取到结果就可以了。2~8 步骤 dubbo 都帮我们封装好了。







