# SpringCloud笔记2

------

### 一、SpringCloud 相关介绍

#### 1、SpringCloud 和 SpringBoot 版本选型

1）、Springboot 版本选择

SpringBoot 官方建议升级到 2.X 以上版本，并且提供了迁移指南。

![springboot推荐版本](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/springboot%E6%8E%A8%E8%8D%90%E7%89%88%E6%9C%AC.png)



2）、SpringCloud 版本选择 

> - SpringCloud 的版本是以伦敦地铁站英文首字母来作为版本号的命名。由地铁站名称字母 A-Z 依次类推的形式来发布迭代版本。
> - 因为 SpringCloud 是一个由许多子项目组成的综合项目，各子项目有不同的发布节奏。为了管理 SpringCloud 与各个子项目的版本依赖关系，发布了一个清单，其中包括了某个 SpringCloud 版本对应的子项目版本。为了避免 SpringCloud 版本号与子项目版本版本号混淆，SpringCloud 采用了名称而非版本号的命名，这些版本的名字采用了伦敦地铁站的名字，根据字母表的顺序来对应版本时间顺序。
> - 当 SpringCloud 的发布内容积累到临界点或者一个重大 bug 被解决后，会发布一个 "service releases" 版本，简称 SRX 版本。比如 Greenwich.SRX2 就是 SpringCloud 发布的 Greenwich 版本的第 2 个 SRX 版本。

![springcloud版本号命名方式](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/springcloud%E7%89%88%E6%9C%AC%E5%8F%B7%E5%91%BD%E5%90%8D.png)



3）、SpringBoot 和 SpringCloud 版本对应关系

① 版本对应关系

![SpringBoot 和 SpringCloud 版本对应关系](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/springboot%E5%92%8Cspringcloud%E7%89%88%E6%9C%AC%E5%AF%B9%E5%BA%94%E5%85%B3%E7%B3%BB.png) 

② 更详细的版本对应查看方法

可以通过这个网址去进行查看：

`https://start.spring.io/actuator/info`

![springcloud和springboot版本对应关系2](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/springcloud%E4%B8%8E%20springboot%E7%89%88%E6%9C%AC%E5%AF%B9%E5%BA%94%E5%85%B3%E7%B3%BB2.png)

③ 官网版本查看

进入 springcloud 的官网，选择一个你想用的 springcloud 的版本，然后点进去会发现在官网中会推荐你使用一个相应的 springboot 版本。

![springboot和 springcloud 版本对应关系3](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/springcloud%E5%92%8C%20springboot%E7%89%88%E6%9C%AC%E5%AF%B9%E5%BA%94%E5%85%B3%E7%B3%BB3.png)

**注：**

> - 如果只用 springboot 那么直接使用最小版本即可。
> - 如果同时使用 springboot 和 springcloud ，需要照顾 springcloud ，由 springcloud 决定 springboot 的版本。

### 二、服务工程构建

#### 1、创建父工程

Pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.example</groupId>
    <artifactId>springcloud2020</artifactId>
    <version>1.0-SNAPSHOT</version>
    <!-- 父工程，打包方式为 pom,不再是默认的 jar -->
    <packaging>pom</packaging>

    <!-- 统一管理 jar 包版本 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.16.18</lombok.version>
        <mysql.version>5.1.47</mysql.version>
        <druid.version>1.1.16</druid.version>
        <mybatis.spring.boot.version>1.3.0</mybatis.spring.boot.version>
    </properties>
    
    <!--子模块继承之后，提供作用：锁定版本 + 子module 不用写 groupId 和 version-->
    <dependencyManagement>

    </dependencyManagement>

    <dependencies>
        <!--SpringBoot 版本-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.2.2.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <!--Springcloud 版本-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>Hoxton.SR1</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <!--Springcloud alibaba 版本-->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-alibaba-dependencies</artifactId>
            <version>2.1.0.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>${mysql.version}</version>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>${druid.version}</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>${mybatis.spring.boot.version}</version>
        </dependency>
    </dependencies>


</project>
```

**小知识：**

> Maven 依赖中 `<dependencyManagement></dependencyManagement>`  的作用：
>
> 答：Maven 中使用 `dependencyManagement` 元素提供了一种管理依赖版本号的方式。通常会在一个组织或者项目的最顶层父 POM 中看到  `dependencyManagement` 元素。
>
> 使用  `dependencyManagement` 元素能让所有在子项目中引用一个依赖而不用显示的列出版本号，Maven 会沿着父子层次向上走，直到找到一个拥有   `dependencyManagement` 元素的项目，然后它就会使用这个   `dependencyManagement` 元素中指定的版本号。
>
> 注意：
>
> 1、`dependencyManagement` 里面只是声明依赖，并不实现引入，因此子项目需要显示的声明需要用的依赖
>
> 2、如果不在子项目中声明依赖，是不会从父项目中继承下来的，只有在子项目中写了该依赖项，并且没有指定具体版本，才会从父项目中继承该项，并且 version 和 scope 都读取自父 pom
>
> 3、如果子项目中指定了版本号，那会使用子项目中指定的版本



#### 2、支付模块构建

1）、创建一个服务提供者 `springcloud-provider-payment8001`

① pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloud2020</artifactId>
        <groupId>com.hepingfly.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.hepingfly.springcloud</groupId>
    <artifactId>springcloud-provider-payment8001</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
            <version>1.5.9.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>1.3.0</version>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid-spring-boot-starter</artifactId>
            <version>1.1.10</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.44</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
        </dependency>
    </dependencies>
</project>
```

② 相关 Java 代码

```java
// controller 代码-----------------------------------------------------------------------------
/**
 * @auther hepingfly
 * @date 2020/11/8 9:00 下午
 */
@RestController
public class PaymentController {

    @Resource
    private PaymentService paymentService;

    @PostMapping("/payment/save")
    public Result save(Payment payment) {
        int result = paymentService.save(payment);
        if (result > 0) {
            return new Result(200,"插入成功");
        } else {
            return new Result(444, "插入失败");
        }
    }

    @GetMapping("/payment/get/{id}")
    public Result getPaymentById(@PathVariable("id") Long id) {
        Payment payment = paymentService.getPaymentById(id);
        if (payment != null) {
            return new Result(200, "查询成功", payment);
        } else {
            return new Result(444, "没有对应的记录", null);
        }
    }
}

// service 代码--------------------------------------------------------------------------------

/**
 * @auther hepingfly
 * @date 2020/11/5 9:33 下午
 */
@Service
public class PaymentServiceImpl implements PaymentService {

    @Resource
    private PaymentDAO paymentDAO;
    @Override
    public int save(Payment payment) {
        return paymentDAO.save(payment);
    }

    @Override
    public Payment getPaymentById(Long id) {
        return paymentDAO.getPaymentById(id);
    }
}

```

#### 3、热部署使用

代码改动之后可以自动重启生效，而不需要你手动去进行重启。

① 引入 maven 依赖到 pom 文件中

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <version>1.5.9.RELEASE</version>
    <scope>runtime</scope>
    <optional>true</optional>
</dependency>
```

② 添加 maven 插件到 pom 文件中

```xml
<build>
    <finalName>springcloud2020</finalName>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <version>2.3.4.RELEASE</version>
            <configuration>
                <fork>true</fork>
                <addResources>true</addResources>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**注：**

这个可以放在父工程中。

③ 开启 idea 自动编译

![开启idea自动编译](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/idea%E5%BC%80%E5%90%AF%E8%87%AA%E5%8A%A8%E7%BC%96%E8%AF%91.png)

④ 使用快捷键修改 idea 中 registry 值

windows 下使用： `ctrl + shift + alt + /`    然后选 Registry

mac 下使用 ： `ctrl + shift + option + / `     然后选 Registry

![修改 idea 中 registry](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/%E4%BF%AE%E6%94%B9%20idea%20%E4%B8%ADregistry.png)

⑤ 重启 idea 



#### 4、订单模块构建

1）、创建一个服务消费者 `springcloud-consumer-order80`

① pom.xml 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>springcloud2020</artifactId>
        <groupId>com.hepingfly.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>springcloud-consumer-order80</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
            <version>1.5.9.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

② 相关 Java 代码

```java
// controller 代码-----------------------------------------------------------------------------
/**
 * @auther hepingfly
 * @date 2020/12/7 10:42 下午
 */
@RestController
@Slf4j
public class OrderController {

    /**
     * 1、RestTemplate 是什么？
     * RestTemplate 提供了多种便捷访问远程 Http 服务的方法
     * 是一种简单便捷的访问 restful 服务模板类，是 Spring 提供的用于访问
     * Rest 服务的客户端模板工具类
     *
     * 2、如何使用
     * 使用 restTemplate 访问 restful 接口非常的简单粗暴
     * (url,requestMap,ResponseBean.class) 三个参数分别表示：
     * Rest请求地址，请求参数，HTTP 响应被转换的对象类型
     */
    @Resource
    private RestTemplate restTemplate;

    public static final String PAYMENT_URL = "http://localhost:8001";

    @GetMapping("/consumer/payment/save")
    public Result<Payment> save(Payment payment) {
        return restTemplate.postForObject(PAYMENT_URL + "/payment/save", payment, Result.class);
    }

    @GetMapping("/consumer/payment/get/{id}")
    public Result<Payment> getPayment(@PathVariable("id") Long id) {
        return restTemplate.getForObject(PAYMENT_URL + "/payment/get/" + id, Result.class);
    }
}
```



### 三、Eureka 相关

#### 1、服务治理

什么是服务治理？

SpringCloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务治理。在传统的 rpc 远程调用框架中，管理每个服务与服务之间依赖关系比较复杂，所以需要使用服务治理，管理服务与服务之间依赖关系，可以实现服务调用、负载均衡、容错等，实现服务注册与发现。

#### 2、服务注册与发现

1）、服务注册与发现

- Eureka 采用了 CS 架构，Eureka Server 作为服务注册功能的服务器，它是服务注册中心。系统中其他的微服务，使用 Eureka 的客户端连接到 Eureka Server 并维持心跳连接。这样系统维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。
- 服务注册与发现中有一个注册中心。当服务器启动的时候，会把当前自己服务器的信息，如：服务地址、通信地址等以别名的方式注册到注册中心上。服务消费者以该别名的方式去注册中心获取到实际的服务通信地址，然后再实现 rpc 调用。
- rpc 框架核心设计思想在于注册中心，因为使用注册中心管理每个服务与服务之间的一个依赖关系（服务治理概念）。在任何 rpc 框架中，都会有一个注册中心，存放服务地址相关信息（接口地址）

#### 3、Eureka 组件

Eureka 包含两个组件：Eureka Server 和 Eureka Client

> - Eureka Server 提供服务注册服务
>   - 各个微服务节点通过配置启动后，会在 EurekaServer 中进行注册，这样 Eureka Server服务注册表中将会存储所有可用服务节点信息，并且服务节点的信息可以在界面中直观的看到。
> - Eureka Client 通过注册中心进行访问
>   - 是一个 Java 客户端，用于简化 EurekaServer 的交互，客户端同时也具备一个内置的、使用轮询负载算法的负载均衡器。应用启动后，将会向 EurekaServer 发送心跳（默认周期是 30s）。如果 Eureka Server 在多个心跳周期内没有接收到某个节点的心跳，Eureka Server 将会从服务注册表中把这个服务节点移除（默认 90s）

![Eureka服务注册与发现](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/Eureka%20%E6%9C%8D%E5%8A%A1%E6%B3%A8%E5%86%8C%E4%B8%8E%E5%8F%91%E7%8E%B0.png)

#### 4、编码实现

1）、创建一个 Eureka Server  `springcloud-eureka-server7001`

① pom.xml

```xml
<parent>
        <artifactId>springcloud2020</artifactId>
        <groupId>com.hepingfly.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>springcloud-eureka-server7001</artifactId>

    <dependencies>
        <!--eureka server-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
            <version>2.2.9.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>com.hepingfly.springcloud</groupId>
            <artifactId>springcloud-api-commons</artifactId>
            <version>1.0-SNAPSHOT</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <version>2.2.0.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
            <version>1.5.9.RELEASE</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.10</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

**这里需要注意的是：**

> Eureka Server1.X 和 2.X 版本依赖的区别：
>
> 旧版本：
>
> ```xml
> <dependency>
>     <groupId>org.springframework.cloud</groupId>
>     <artifactId>spring-cloud-starter-eureka</artifactId>
>     <version>2.2.9.RELEASE</version>
> </dependency>
> ```
>
> 新版本：
>
> ```xml
> <dependency>
>     <groupId>org.springframework.cloud</groupId>
>     <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
>     <version>2.2.9.RELEASE</version>
> </dependency>
> ```
>
> 

② application.yml

```yaml
server:
  port: 7001
eureka:
  instance:
    hostname: localhost   #eureka 服务端实例名称
  client:
    # flase 表示不向注册中心注册自己
    register-with-eureka: false
    # false 表示自己就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与 eureka server 交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

2）、把服务提供者注册进 Eureka

① 修改 pom 文件

旧版本：

```xml
<dependency>
 <groupId>org.springframework.cloud</groupId>
 <artifactId>spring-cloud-starter-eureka</artifactId>
 <version>2.2.9.RELEASE</version>
</dependency>
```

新版本：

```xml
<dependency>
 <groupId>org.springframework.cloud</groupId>
 <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
 <version>2.2.9.RELEASE</version>
</dependency>
```

② 修改 application.yml 配置文件，添加下面 eureka 的配置信息

```yaml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
    #表示是否将自己注册进 eureka
    register-with-eureka: true
    # 是否从 eurekaserver 抓取已有的注册信息。单节点无所谓，集群必须设置为 true才能配合 ribbon 使用负载均衡
    fetch-registry: true
```

③ 修改主启动类，添加 eureka 客户端注解

```java
@SpringBootApplication
@EnableEurekaClient       // Eureka 客户端注解
public class Application {
    public static void main(String[] args) throws ParserConfigurationException {
        DocumentBuilderFactory documentBuilderFactory = DocumentBuilderFactory.newInstance();
        documentBuilderFactory.setFeature("http://apache.org/xml/features/disallow-doctype-decl", true);
        SpringApplication.run(Application.class, args);
    }
}
```

#### 5、集群 eureka 搭建

**1）、搭建步骤**

新建另 一个 Eureka Server  `springcloud-eureka-server7002`

① pom 文件和  `springcloud-eureka-server7001` 保持一致

② application.yml 文件

```yaml
----------------------------先看单机版配置文件-----------------------
eureka:
  instance:
    hostname: localhost   #eureka 服务端实例名称
  client:
    # flase 表示不向注册中心注册自己
    register-with-eureka: false
    # false 表示自己就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与 eureka server 交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
      
--------------------------server7001 配置文件 -------------------------
server:
  port: 7001
eureka:
  instance:
    hostname: eureka7001.com   #eureka 服务端实例名称
  client:
    # flase 表示不向注册中心注册自己
    register-with-eureka: false
    # false 表示自己就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与 eureka server 交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7002.com:7002/eureka/  #7001 这台 server 需要把服务注册进 7002 中，7002 也要把服务注册进7001 中，相互注册，相互守望

--------------------------server7002 配置文件 -------------------------

server:
  port: 7002
eureka:
  instance:
    hostname: eureka7002.com   #eureka 服务端实例名称
  client:
    # flase 表示不向注册中心注册自己
    register-with-eureka: false
    # false 表示自己就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    fetch-registry: false
    service-url:
      #设置与 eureka server 交互的地址查询服务和注册服务都需要依赖这个地址
      defaultZone: http://eureka7001.com:7001/eureka/  #7002 需要把服务注册进 7001 中

```

**2）、将支付服务发布到 eureka 集群中**

`springcloud-provider-payment8001`  服务发布到 eureka 集群中

① 修改 application.yml 文件

```yaml
# ---------------------单机版配置--------------------
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
    #表示是否将自己注册进 eureka
    register-with-eureka: true
    # 是否从 eurekaserver 抓取已有的注册信息。单节点无所谓，集群必须设置为 true才能配合 ribbon 使用负载均衡
    fetch-registry: true
 
# ---------------------集群版配置--------------------
eureka:
  client:
    service-url:
      # 集群版 eureka 配置
    defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
    #表示是否将自己注册进 eureka
    register-with-eureka: true
    # 是否从 eurekaserver 抓取已有的注册信息。单节点无所谓，集群必须设置为 true才能配合 ribbon 使用负载均衡
    fetch-registry: true
```

**3）、将订单服务发布到 eureka 集群中**

`springcloud-consumer-order80` 服务发布到 eureka 集群中

① 修改 application.yml 文件

```yaml
# ---------------------单机版配置--------------------
eureka:
  client:
    service-url:
      defaultZone: http://localhost:7001/eureka
    #表示是否将自己注册进 eureka
    register-with-eureka: true
    # 是否从 eurekaserver 抓取已有的注册信息。单节点无所谓，集群必须设置为 true才能配合 ribbon 使用负载均衡
    fetch-registry: true
    
# ---------------------集群版配置--------------------
eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka
    #表示是否将自己注册进 eureka
    register-with-eureka: true
    # 是否从 eurekaserver 抓取已有的注册信息。单节点无所谓，集群必须设置为 true才能配合 ribbon 使用负载均衡
    fetch-registry: true
```

6、支付服务集群搭建











