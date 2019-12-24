# SpringCloud 笔记

### 微服务概述

#### 1、什么是微服务

微服务最早是由 Martin Fowler 在 2014 年在他的个人博客中提出来的。  博客地址：    `<https://martinfowler.com/articles/microservices.html>`

**是什么：**

> - 就目前而言，对于微服务业界并没有一个统一的、标准的定义（While there is no precise definition of this architectural style）为什么这么说？ 因为其中有一个很重要的原因就是拆分的维度，是按照技术维度去拆还是按照业务逻辑去拆还是按照领域模型去拆。这个仁者见仁智者见智，没有达成统一意见。
> - 但通常而言，微服务架构是一种架构模式，或者说一种架构风格，**它提倡将单一应用程序划分成一组小的服务**，每个服务运行在其独立的自己的进程中，服务之间互相协调，互相配合，为用户提供最终的价值。服务之间采用轻量级的通信机制互相沟通（通常是基于 http 的 RESTful API）。每个服务都围绕着具体业务进行构建，并且能够被独立的部署到生产环境、类生产环境等。另外，应尽量避免统一的、集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言、工具对其进行构建，可以有一个非常轻量级的集中式管理来协调这些服务，可以使用不同的语言来编写服务，也可以使用不同的数据存储。

我们从技术的维度去理解就是：

> 微服务化的核心就是将传统的一站式应用，根据业务拆分成一个一个的服务，彻底的去耦合，每一个微服务提供单个业务功能的服务，一个服务做一件事，从技术角度去看就是一种小而独立的处理过程，类似进程的概念，能够自行单独启动或销毁，拥有自己独立的数据库。

#### 2、微服务与微服务架构

**微服务：**

> 强调的是服务的大小，它关注的是某一个点，是具体解决某一个问题的一个服务应用。强调的是一个个的个体，每个个体完成一个具体的任务或功能。
>
> 狭义的看，可以看做 eclipse 里面一个个微服务工程或者 moudle

**微服务架构：**

> 微服务架构是一种架构模式，**它提倡将单一应用程序划分成一组小的服务**，每个服务运行在其独立的自己的进程中，服务之间互相协调，互相配合，为用户提供最终的价值。服务之间采用轻量级的通信机制互相沟通（通常是基于 http 的 RESTful API）。每个服务都围绕着具体业务进行构建，并且能够被独立的部署到生产环境、类生产环境等。另外，应尽量避免统一的、集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言、工具对其进行构建

#### 3、微服务优缺点

**优点：**

> 每个服务足够内聚，足够小，代码容易理解，这样就能聚焦一个指定的业务功能或业务需求
>
> 开发简单，开发效率提高，一个服务可能就是专一的只干一件事。
>
> 微服务能够被小团队单独开发，这个小团队可以是2-5个人的开发人员
>
> 微服务是松耦合的，是有功能意义的服务，无论是在开发阶段还是部署阶段都是独立的。
>
> 微服务能够使用不同的语言开发
>
> 易于和第三方集成，微服务允许容易且灵活的方式集成自动部署，通过持续集成工具，如：Jenkins
>
> 微服务只是业务逻辑的代码，不会和 HTML CSS 或其他界面组件混合
>
> 每个微服务都有自己的存储能力，可以有自己的数据库。也可以有统一的数据库。

**缺点：**

> 开发人员要处理分布式系统的复杂性
>
> 多服务运维难度，随着服务的增加，运维的压力也在增大
>
> 系统部署依赖
>
> 数据一致性
>
> 系统集成测试
>
> 性能监控

### SpringCloud 入门

#### 1、SpringCloud 是什么

SpringCloud 基于 SpringBoot 提供了一整套微服务解决方案，包括服务注册于发现、配置中心、全链路监控、服务网关、负载均衡、熔断器等组件，除了基于 NetFlix 的开源组件做高度抽象封装之外，还有一些选型中立的开源组件。

**注：**

> SpringCloud = 分布式微服务架构下的一站式解决方案，是各个微服务架构落地技术的集合体，俗称微服务全家桶。

#### 2、SpringCloud 和 SpringBoot 是什么关系

> SpringBoot 专注于快速方便的开发单个个体服务。
>
> SpringCloud 是关注全局的微服务协调治理框架，它将 SpringBoot 开发的一个个单体微服务整合并管理起来，为各个微服务之间提供配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等集成服务。
>
> SpringBoot 可以离开 SpringCloud 独立开发项目，但是 SpringCloud 离不开 SpringBoot ，属于依赖关系。
>
> **总结：**
>
> SpringBoot 专注于快速、方便的开发单个微服务个体，SpringCloud 关注全局的服务治理框架。

#### 3、SpringCloud 相关网址资料

① `https://www.springcloud.cc/spring-cloud-netflix.html`

② `https://www.springcloud.cc/spring-cloud-dalston.html`

③ SpringCloud 中国社区 `http://www.springcloud.cn`

④ SpringCloud 中文网  `https://www.springcloud.cc`



### Rest 微服务构建案例工程模块

#### 1、父工程构建步骤

①新建父工程 microservicecloud ,切记 packaging 是 pom 模式

② 定义 pom 文件，将后续各个子模块公用的 jar 包等统一提取出来，类似一个抽象父类。

③ pom 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.hepingfly.springcloud</groupId>
    <artifactId>microservicecloud</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <log4j.version>1.2.17</log4j.version>
        <lombok.version>1.16.18</lombok.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Dalston.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>1.5.9.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>

            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>5.0.4</version>
            </dependency>

            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>1.0.31</version>
            </dependency>

            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>1.3.0</version>
            </dependency>

            <dependency>
                <groupId>ch.qos.logback</groupId>
                <artifactId>logback-core</artifactId>
                <version>1.2.3</version>
            </dependency>

            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
                <scope>test</scope>
            </dependency>

            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

#### 2、api 公共模块和部门 entiey 创建步骤

① 新建 microservicecloud-api 工程

> 创建方式是在父工程下面 new 一个 moudule
>
> 创建完成之后，你再去父工程的 pom 文件看，发现会多了
>
> ```xml
> <modules>
>     <module>microservicecloudapi</module>
> </modules>
> ```
>
> 说明子工程被聚合到了父工程中

②修改子工程 pom 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>microservicecloud</artifactId>
        <groupId>com.hepingfly.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>microservicecloud-api</artifactId>

    <!--当前 module 需要用到的 jar 包，按照自己的需求添加，如果父类已经包含了，可以不用写版本号-->
    <dependencies>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>
</project>
```

③新建部门实体类并配合 lombok 使用

```java
@SuppressWarnings("serial")
@AllArgsConstructor
@NoArgsConstructor
@Data
@Accessors(chain = true)
public class Dept implements Serializable{

    /**
     * 主键
     */
    private Long deptNum;

    /**
     * 部门名称
     */
    private String deptName;

    /**
     * 来自哪个数据库，因为微服务架构可以一个服务对应一个数据库，同一个信息被存储到不同数据库
     */
    private String dbSource;

}
```

#### 3、部门服务提供者

① 新建 microservicecloud-provider-dept 工程

> 创建方式是在父工程下面 new 一个 moudule
>
> 创建完成之后，你再去父工程的 pom 文件看，发现会多了
>
> ```Xml
> <modules>
>     <module>microservicecloudapi</module>
>     <module>microservicecloud-provider-dept</module>
> </modules>
> ```
>
> 说明子工程被聚合到了父工程中

② 修改子工程 pom 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>microservicecloud</artifactId>
        <groupId>com.hepingfly.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>microservicecloud-provider-dept</artifactId>
    <dependencies>
        <!-- 引入自己定义的 api 通用包，这样就可以使用 Dept 部门 Entity -->
        <dependency>
            <groupId>com.hepingfly.springcloud</groupId>
            <artifactId>microservicecloud-api</artifactId>
            <version>${project.version}</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
        </dependency>

        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-core</artifactId>
        </dependency>

        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jetty</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>

        <!--修改后立即生效，热部署-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>springloaded</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>
    </dependencies>
</project>
```

③ 新建 yml 配置文件

```yaml
server:
  port: 8001

mybatis:
  config-location: classpath:mybatis/mybatis.cfg.xml   # mybatis 配置文件路径
  type-aliases-package: com.hepingfly.springcloud.entites
  mapper-loacations:
   - classpath:mybatis/mapper/**/*.xml   # mapper 映射文件

spring:
  application:
    name: microservicecloud-dept
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource   # 当前数据源操作类型
    driver-class-name: org.gjt.mm.mysql.Driver
    url: jdbc:mysql://localhost:3306/springcloud
    username: root
    password: 123456
```

④ 编写部门 dao

```java
@Mapper
public interface DeptDao {

    public boolean addDept(Dept dept);

    public Dept findById(Long id);

    public List<Dept> findAll();
}
```

⑤ 编写 mapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.hepingfly.springcloud.DeptDao">
   <select id="findById" resultType="Dept">
      SELECT * FROM  dept WHERE deptNum = #{id}
   </select>

    <select id="findAll">
        SELECT * FROM dept;
    </select>
    
    <insert id="addDept">
        INSERT INTO dept(deptName, db_source) VALUES(#{deptName},database())
    </insert>
</mapper>
```

⑥ 编写 service 及实现类

```java
public interface DeptService {

    public boolean add(Dept dept);

    public Dept get(Long id);

    public List<Dept> list();
}

//----------------------------------------
@Service
public class DeptServiceImpl implements DeptService {

    @Autowired
    private DeptDao deptDao;
    @Override
    public boolean add(Dept dept) {
        return deptDao.addDept(dept);
    }

    @Override
    public Dept get(Long id) {
        return deptDao.findById(id);
    }

    @Override
    public List<Dept> list() {
        return deptDao.findAll();
    }
}
```

⑦ 编写 controller

```java
@RestController
public class DeptController {
    private DeptService deptService;

    @RequestMapping(value = "/dept/add",method = RequestMethod.POST)
    public boolean add(@RequestBody Dept dept) {
        return deptService.add(dept);
    }

    @RequestMapping(value = "/dept/get/{id}",method = RequestMethod.GET)
    public Dept get(@PathVariable("id") Long id) {
        return deptService.get(id);
    }


    @RequestMapping(value = "/dept/list",method = RequestMethod.GET)
    public List<Dept> list() {
        return deptService.list();
    }
}
```

⑧ 编写 springboot 主启动类

```java
@SpringBootApplication
public class DeptProviderApp {
    public static void main(String[] args) {
        SpringApplication.run(DeptProviderApp.class,args);
    }
}
```

#### 4、部门服务消费者

① 新建 microservicecloud-consumer-dept 工程

> 创建方式是在父工程下面 new 一个 moudule
>
> 创建完成之后，你再去父工程的 pom 文件看，发现会多了
>
> ```xml
> <modules>
>     <module>microservicecloudapi</module>
>     <module>microservicecloud-consumer-dept</module>
> </modules>
> ```
>
> 说明子工程被聚合到了父工程中

② 修改子工程 pom 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>microservicecloud</artifactId>
        <groupId>com.hepingfly.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>
    <description>部门微服务消费者</description>

    <artifactId>microservicecloud-consumer-dept</artifactId>
    <dependencies>
        <!-- 引入自己定义的 api 通用包，这样就可以使用 Dept 部门 Entity -->
        <dependency>
            <groupId>com.hepingfly.springcloud</groupId>
            <artifactId>microservicecloud-api</artifactId>
            <version>${project.version}</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!--修改后立即生效，热部署-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>springloaded</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>

    </dependencies>
</project>
```

③ 新建 yml 配置文件

```yaml
server:
  port: 80
```

④ 编写配置 bean 

```java
@Configuration
public class ConfigBean {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

⑤ 编写 controller

```java
@RestController
public class DeptController_Consumer {

    public static final String REST_URL_PREFIX = "http://localhost:8001";

    /**
     * RestTemplate 提供了多种便捷访问远程 http 服务的方法
     * 是一种简单便捷的访问 restful 服务模板类，是 spring 提供的
     * 用于访问 rest 服务的客户端模板工具类
     *
     * 使用方式：
     * 使用 restTemplate 访问 restful 接口非常简单粗暴无脑
     * (url,requestMap,ResponseBean.class)
     * 这三个参数分别代表：
     * REST 请求地址、请求参数、HTTP 响应转换成被转换的对象类型
     */
    @Autowired
    private RestTemplate restTemplate;

    @RequestMapping(value = "/consumer/dept/add",method = RequestMethod.POST)
    public boolean add(Dept dept) {
        return restTemplate.postForObject(REST_URL_PREFIX + "/dept/add",dept,Boolean.class);
    }

    @RequestMapping(value = "/consumer/dept/get/{id}",method = RequestMethod.GET)
    public Dept get(@PathVariable("id") Long id) {
        return restTemplate.getForObject(REST_URL_PREFIX + "/dept/get/" + id,Dept.class);
    }

    @RequestMapping(value = "/consumer/dept/list",method = RequestMethod.GET)
    public List<Dept> list() {
        return restTemplate.getForObject(REST_URL_PREFIX + "/dept/list",List.class);
    }
}
```

⑥ 编写主启动类

```java
@SpringBootApplication
public class DeptConsumerApp {
    public static void main(String[] args) {
        SpringApplication.run(DeptConsumerApp.class,args);
    }
}
```

### Eureka 服务注册与发现

#### 1、Eureka 是什么

Netflix 在设计 Eureka 时遵守的就是 AP 原则

**Eureka 是什么？**

> Eureka 是 Netflix 的一个子模块，也是核心模块之一。Eureka 是一个基于 REST 的服务，用于定位服务，以实现云端中间层服务发现和故障转移。服务注册与发现对于微服务架构来说是非常重要的，有了服务注册与发现，只需要使用服务的标识符，就可以访问到服务，而不需要修改服务调用的配置文件了。功能类似于 dubbo 的注册中心，比如 Zookeeper

**Eureka 的基本架构**

> Spring Cloud 封装了 Netflix 公司开发的 Eureka 模块来实现服务注册与发现
>
> Eureka 采用了 C-S 的设计架构架构。Eureka Server 作为服务注册功能的服务器，它是服务注册中心。
>
> 系统中其他的微服务，使用 Eureka 的客户端连接到 Eureka Server  并维持心跳连接。这样系统的维护人员就可以通过 Eureka Server 来监控系统中各个微服务是否正常运行。SpringCloud 的一些其他模块（比如 Zuul）就可以通过 Eureka Server 来发现系统中其他微服务，并执行相关逻辑

![Eureka架构](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/Eureka%E6%9E%B6%E6%9E%84.png)

**说明：**

> Eureka 包含两个组件：Eureka Server 和 Eureka Client
>
> - Eureka Server 提供服务注册服务。各个节点启动后，会在 EurekaServer 中进行注册，这样 EurekaServer 中的服务注册表中将会存储所有可用服务节点信息，服务节点的信息可以在界面中直观的看到
> - Eureka Client 是一个 Java 客户端，用于简化 Eureka Server 的交互，客户端同时也具备一个内置的、使用轮询（round-robin）负载算法的负载均衡器。在应用启动后，将会向 Eureka Server 发送心跳（默认周期为 30秒）。如果 Eureka Server 在多个心跳周期内没有接收到某个节点的心跳，EurekaServer 将会从服务注册表中把这个服务节点移除（默认 90 秒）

**Eureka 三大角色：**

> - Eureka Server 提供服务注册与发现
> - Service Provider 服务提供方将自身服务注册到 Eureka，从而使服务消费方能够找到
> - Service Consumer 服务消费方从 Eureka 获取注册服务列表，从而能够消费服务

#### 2、Eureka Server 服务注册中心建立

① 新建 microservicecloud-eureka 工程

> 创建方式是在父工程下面 new 一个 moudule
>
> 创建完成之后，你再去父工程的 pom 文件看，发现会多了
>
> ```xml
> <modules>
>     <module>microservicecloudapi</module>
>     <module>microservicecloud-provider-dept</module>
>     <module>microservicecloudconsumer-dept</module>
>     <module>microservicecloud-eureka</module>
> </modules>
> ```
>
> 说明子工程被聚合到了父工程中

② 修改子工程 pom 文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>microservicecloud</artifactId>
        <groupId>com.hepingfly.springcloud</groupId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>microservicecloud-eureka</artifactId>

    <dependencies>
        <!--eureka-server 服务端-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka-server</artifactId>
        </dependency>

        <!--修改后立即生效，热部署-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>springloaded</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>
    </dependencies>
</project>
```

③ 新建 yml 配置文件

```yaml
server:
  port: 7001
eureka:
  instance:
    hostname: localhost   # eureka服务端的实例名称
  client:
    register-with-eureka: false  # false 表示不向注册中心注册自己
    fetch-registry: false  # false 表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/  # 设置与 Eureka Server 交互的地址查询服务和注册服务都需要依赖这个地址
```

④ 编写 springboot 主启动类

```java
@SpringBootApplication
@EnableEurekaServer   // 开启 EurekaServer,接受其他微服务注册进来
public class EurekaServerApp {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApp.class,args);
    }
}
```

⑤ 测试是否成功

启动 SpringBoot 主启动类，然后在浏览器端访问 `localhost:7001`,  如果出现 Eureka 页面证明服务启动成功。

#### 3、将已有的部门微服务注册进 Eureka 服务注册中心

 修改 microservicecloud-provider-dept 工程

① 修改 pom 文件

```xml
<!--将微服务 provider 注册进 eureka-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>


<!-- 在 pom 文件中添加上面两个依赖 -->
```

② 修改 yml 配置文件

```yaml
eureka:
  client:   # 将客户端注册进 eureka 服务列表内
    service-url:
      defaultZone: http://localhost:7001/eureka
      
# 在原有 yml 配置文件中新增上面的配置
```

③ 修改主启动类

```java
@SpringBootApplication
@EnableEurekaClient      // 加上这个注解后，本服务启动后会自动注册进 eureka 服务中
public class DeptProviderApp {
    public static void main(String[] args) {
        SpringApplication.run(DeptProviderApp.class,args);
    }
}
```

④ 测试

先启动 eureka 服务端，microservicecloud-eureka 这个服务，然后再启动 microservicecloud-provider-dept 这个服务。然后访问 `localhost:7001` ，在页面可以看到应用被注册进了 eureka 服务。

**这里要注意下微服务的注册名：**

```yaml
spring:
  application:
    name: microservicecloud-dept
    
# 就是你在配置文件中配置的应用名称
```

#### 4、微服务信息完善

1）、主机映射名称修改

当你在页面上访问 eureka 服务 `localhost:7001` 时，会看到下面的信息，其中 Status 一栏展示的信息，有点复杂，我们可以定制这个展示名称

```
Application	AMIs	Availability   Zones	   Status
MICROSERVICECLOUD-DEPT	n/a (1)	  (1)	   UP (1) - 192.168.31.90:microservicecloud-dept:8001
```

**修改方式：**

通过修改 microservicecloud-provider-dept 这个服务的 yml 配置文件即可

```yaml
eureka:
  client:   # 将客户端注册进 eureka 服务列表内
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    instance-id: microservicespringcloud-dept  # 增加这个配置即可，指定注册进 eureka 的实例名称
```

这样在页面上看到的结果就是：

```
Application	AMIs	 Availability             Zones	     Status
MICROSERVICECLOUD-DEPT	   n/a (1)	         (1)	      UP (1) -microservicespringcloud-dept
```

2）、主机 ip 信息提示

当我们把鼠标悬停在上面 Status 栏下面的服务实例名称上时，在浏览器左下角可以显示 `ip:port/info`,这样可以很快知道这个微服务在哪台机器上。

**修改方式：**

通过修改 microservicecloud-provider-dept 这个服务的 yml 配置文件即可。

```yaml
eureka:
  client:   # 将客户端注册进 eureka 服务列表内
    service-url:
      defaultZone: http://localhost:7001/eureka
  instance:
    prefer-ip-address: true    #让访问路径可以显示 ip 地址
```

3）、info 内容构建

希望点击 Status 栏下面具体微服务实例名称，显示的是该微服务相关信息，而不是 404 页面

① 修改  microservicecloud-provider-dept 这个服务的 pom 文件

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<!-- 在 pom 文件中增加上面的依赖 -->
```

② 修改总的父工程，修改 pom 文件，添加构建 build 信息

```xml
<build>
    <finalName>microservicecloud</finalName>
    <resources>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
        </resource>
    </resources>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-resources-plugin</artifactId>
            <configuration>
                <delimiters>
                    <delimit>$</delimit>
                </delimiters>
            </configuration>
        </plugin>
    </plugins>
</build>
```

③ 修改  microservicecloud-provider-dept 这个服务的 yml 文件

```yaml
info:
  app.name: hepingfly-microservicecloud
  company.name: www.hepingfly.com
  build.artifactId: $project.artifactId$
  build.version: $project.version$

# yml 配置文件中添加以上配置
```

这样你在页面上点击 Status 栏中具体的某个服务，超链接点进去就不会是 404 而是你在配置文件中配置的信息

#### 5、Eureka 自我保护机制

如果你这个服务长时间不与 Eureka 建立连接，在页面就会报如下错误：

```
EMERGENCY! EUREKA MAY BE INCORRECTLY CLAIMING INSTANCES ARE UP WHEN THEY'RE NOT. RENEWALS ARE LESSER THAN THRESHOLD AND HENCE THE INSTANCES ARE NOT BEING EXPIRED JUST TO BE SAFE.
```

**什么是自我保护机制？**

> 默认情况下，如果 Eureka Server 在一定时间内没有接收到某个微服务实例的心跳，EurekaServer 将会注销该实例（默认 90 秒）。但是当网络分区故障发生时，微服务与 Eureka Server 之间无法正常通信，以上行为可能就变的非常危险了。（因为微服务本身其实是健康的，此时不应该注销这个微服务）。Eureka 通过 “自我保护模式”来解决这个问题。当 EurekaServer 在短时间丢失过多客户端时（可能发生了网络分区故障），那么这个节点就会进入自我保护模式。一旦进入该模式，EurekaServer 就会保护服务注册表中的信息，不再删除服务注册表中的数据（也就是不会注销任何微服务）。当网络故障恢复后，该 Eureka Server 节点会自动退出自我保护模式。
>
> 在自我保护模式中，Eureka Server 会保护服务注册表中的信息，不再注销任何服务实例。当它收到的心跳数重新恢复到阈值以上时，该 Eureka Server 节点就会自动退出自我保护模式。它的设计哲学就是宁可保留错误的服务注册信息，也不盲目注销任何可能健康的服务实例。
>
> 综上，自我保护模式是一种应对网络异常的安全保护措施。它的架构哲学是宁可同时保留所有微服务（健康的微服务和不健康的微服务都会保留），也不盲目注销任何健康的微服务。使用自我保护模式，可以让 Eureka 更加健壮、稳定。
>
> 在 SpringCloud 中，可以通过下面的配置来禁用自我保护模式，但是不推荐禁用。
>
> ```
> eureka.server.enable-self-preservation=false
> ```

**一句话总结一下就是：**

> 某时刻某个微服务不可用了，eureka 不会立刻清理，依旧会对该微服务的信息进行保存。

#### 6、Eureka 服务发现

对于注册进 eureka 里面的微服务，可以通过服务发现来获得该服务的信息。

① 修改 microservicecloud-provider-dept 工程的 DeptController

```java
@RestController
public class DeptController {

    @Autowired
    private DiscoveryClient client;

    @RequestMapping(value = "/dept/discovery",method = RequestMethod.GET)
    public Object discovery() {
        // 获取 eureka 中所有的微服务
        List<String> services = client.getServices();
        List<ServiceInstance> instances = client.getInstances("MICROSERVICECLOUD-DEPT");
        for (ServiceInstance instance : instances) {
            System.out.println(instance.getServiceId() + "\t" + instance.getHost() + "\t" + instance.getPort() + "\t" + instance.getUri());
        }
        return this.client;

    }
}

// 在原有代码的基础上，增加以上代码
```

② 修改microservicecloud-provider-dept 工程的主启动类

```java
@SpringBootApplication
@EnableEurekaClient      // 加上这个注解后，本服务启动后会自动注册进 eureka 服务中
@EnableDiscoveryClient   // 服务发现
public class DeptProviderApp {
    public static void main(String[] args) {
        SpringApplication.run(DeptProviderApp.class,args);
    }
}
```

③ 测试

访问 `http://localhost:8001/dept/discovery` 

```json
{
    "localServiceInstance": {
        "host": "192.168.31.90",
        "port": 8001,
        "uri": "http://192.168.31.90:8001",
        "serviceId": "microservicecloud-dept",
        "metadata": {},
        "secure": false
    },
    "services": [
        "microservicecloud-dept"
    ]
}
```

#### 7、Eureka 集群配置

① 新建 microservicecloudeureka-b 和 microservicecloudeureka-c 工程

② 修改 pom 文件，可以按照 microservicecloud-eureka 去粘贴 pom 文件

③ 修改 b 和 c 两个工程的主启动类

```java
@SpringBootApplication
@EnableEurekaServer   // 开启 EurekaServer,接受其他微服务注册进来
public class EurekaServerApp {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApp.class,args);
    }
}

// 主启动类和 microservicecloud-eureka  保持一致
```

④ 修改三台 eureka 服务器的 yml 配置文件

```yaml
server:
  port: 7001
eureka:
  instance:
    hostname: eureka7001.com   # eureka服务端的实例名称
  client:
    register-with-eureka: false  # false 表示不向注册中心注册自己
    fetch-registry: false  # false 表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:   # 单机版  http://${eureka.instance.hostname}:${server.port}/eureka/
      defaultZone: http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/    # 设置与 Eureka Server 交互的地址查询服务和注册服务都需要依赖这个地址
      
 --------------------------------------------------------------------------------------
 server:
  port: 7002
eureka:
  instance:
    hostname: eureka7002.com   # eureka服务端的实例名称
  client:
    register-with-eureka: false  # false 表示不向注册中心注册自己
    fetch-registry: false  # false 表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:   # 单机版  http://${eureka.instance.hostname}:${server.port}/eureka/
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7003.com:7003/eureka/    # 设置与 Eureka Server 交互的地址查询服务和注册服务都需要依赖这个地址
      
--------------------------------------------------------------------------------------
 server:
  port: 7003
eureka:
  instance:
    hostname: eureka7003.com   # eureka服务端的实例名称
  client:
    register-with-eureka: false  # false 表示不向注册中心注册自己
    fetch-registry: false  # false 表示自己端就是注册中心，我的职责就是维护服务实例，并不需要去检索服务
    service-url:   # 单机版  http://${eureka.instance.hostname}:${server.port}/eureka/
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/    # 设置与 Eureka Server 交互的地址查询服务和注册服务都需要依赖这个地址
```

⑤ 修改 microservicecloud-provider-dept 工程 yml 配置文件，将微服务发布到 3 台 eureka 集群配置中

```yaml
eureka:
  client:   # 将客户端注册进 eureka 服务列表内
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
  instance:
    instance-id: microservicespringcloud-dept
    prefer-ip-address: false
```

注：

> 记得修改下 host 文件，做下域名映射。把 eureka7001.com 和 eureka7002.com 和 eureka7003.com 都和 127.0.0.1 做映射

#### 8、Eureka 比 zookerper 好在哪里

首先说下 CAP 理论：

> CAP 理论的核心是：一个分布式系统不可能同时很好的满足一致性，可用性和分区容错性。
>
> CAP 理论就是说在分布式存储系统中，最多只能实现上面的两点。而由于当前的网络硬件肯定会出现延迟丢包等问题，**所以分区容错性是我们必须要实现的**。
>
> 所以我们只能在一致性和可用性之间进行权衡，没有 NOSQL 系统能同时保证这三点。
>
> C ：强一致性   A：高可用性    P：分布式容忍性
>
> - CA ：传统 Oracle 数据库
> - AP ：大多数网站架构的选择
> - CP ：Redis  Mongodb 

**Eureka 和 Zookeeper 比较**

> 作为服务注册中心，Eureka 和 Zookeeper 相比：
>
> 著名的 CAP 理论指出，一个分布式系统不可能同时满足 C（一致性）、A（可用性）和 P（分区容错性）。由于分区容错性 P 在分布式系统中是必须要保证的，因此我们只能在 A 和 C 之间权衡。
>
> **Zookeeper 保证的是 CP**
>
> - 当向注册中心查询服务列表时，我们可以容忍注册中心返回的是几分钟以前的注册信息，但不能接受服务直接 down 掉不可用。也就是说，服务注册功能对于可用性的要求要高于一致性。但是 zk 会出现这样一种情况，当 master 节点因为网络故障与其他节点失去联系时，剩余节点会重新进行 leader 选举。问题在于，选举 leader 的时间太长，大概 30 ~ 120 秒，且选举期间整个 zk 集群都是不可用的，这就导致了在选举期间注册服务瘫痪。在云部署环境下，因网络问题使得 zk 集群失去 master 节点是较大概率会发生的事件，虽然服务能最终恢复，但是漫长的选举时间导致的注册长期不可用是不能容忍的。
>
> **Eureka 保证的是 AP** 
>
> - Eureka 看明白了这一点，因此在设计时就优先保证可用性。Eureka 各个节点都是平等的，几个节点挂掉不会影响正常节点的工作，剩余的节点依然可以提供注册和查询服务。而 Eureka 的客户端在向某个 Eureka 注册时，如果发现连接失败，则会自动切换至其他节点，只要有一台 Eureka 还在就能保证服务可用（保证可用性），只不过查到的信息可能不是最新的（不保证强一致性）。除此之外，Eureka 还有一种自我保护机制，如果在 15 分钟内超过 85%的节点都没有正常的心跳，那么 Eureka 就认为客户端与注册中心出现了网络故障，此时会出现以下几种情况：
>   - Eureka 不再从注册列表中移除因为长时间没收到心跳而应该过期的服务
>   - Eureka 仍然能够接受新服务的注册和查询请求，但是不会被同步到其他节点上（即保证当前节点依然可用）
>   - 当网络稳定时，当前实例新的注册信息会被同步到其他节点中
>
> 因此，Eureka 可以很好的应对因网络故障导致部分节点失去联系的情况，而不会像 zookeeper 那样使整个注册服务瘫痪

### Ribbon 负载均衡

#### 1、Ribbon 是什么

> Spring Cloud Ribbon 是基于 Netflix Ribbon 实现的一套客户端负载均衡的工具。
>
> 简单的说，Ribbon 是 Netflix 发布的开源项目，主要功能是提供客户端软件负载均衡算法，将 Netflix 的中间层服务连接在一起。Ribbon 客户端组件提供一系列完善的配置项如连接超时，重试等。简单的说，就是在配置文件中列出 Load Balancer（LB）后面所有的机器，Ribbon 会自动帮助你基于某种规则（如简单轮询，随机连接等）去连接这些机器。我们也很容易使用 Ribbon 实现自定义的负载均衡算法。

**负载均衡：**

> LB，即负载均衡（Load Balance），在微服务或分布式集群中经常使用的一种应用。
>
> 负载均衡简单的说就是将用户的请求平摊的分配到多个服务上，从而达到系统的 HA
>
> 常见的负载均衡有 NGINX 、	LVS、F5等
>
> 相应的在中间件，例如：dubbo和 springCloud 中均给我提供了负载均衡，Springcloud 的负载均衡算法可以自定义。
>
> LB 又可以分为：
>
> - 集中式 LB
>   - 即在服务的消费方和提供方之间使用独立的 LB 设施（可以是硬件，如F5，也可以是软件，如 nginx）,由该设施负责把访问请求通过某种策略转发至服务的提供方
> - 进程内 LB
>   - 将 LB 逻辑集成到消费方，消费方从服务注册中心获知有哪些地址可用，然后自己再从这些地址中选择出一个合适的服务器。
>   - Ribbon 就属于进程内 LB，它只是一个类库，集成于消费方进程，消费方通过它来获取到服务提供方地址。

#### 2、Ribbon 配置

① 修改 microservicecloudconsumer-dept 工程

② 修改 该工程 pom 文件

```xml
<!--Ribbon 相关-->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-ribbon</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>

<!-- 在原有基础上增加以上依赖 -->
```

③ 修改该工程 application.yml 配置文件，追加 eureka 的服务注册地址

```yaml
eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
```

④ 开启负载均衡

```java
@Configuration
public class ConfigBean {

    @Bean
    @LoadBalanced   // 加上这个注解
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

// 前面我们说了 springcloud 带有负载均衡，然后在 springcloud 中我们是通过 restTemplate 来调用 rest 服务的，所以只要你在创建这个 bean 的时候加上 @LoadBalanced 注解即可开启负载均衡
```

⑤ 修改该工程主启动类

```java
@SpringBootApplication
@EnableEurekaClient     // 添加这个注解
public class DeptConsumerApp {
    public static void main(String[] args) {
        SpringApplication.run(DeptConsumerApp.class,args);
    }
}
```

⑥ 修改该工程的 controller

```java
@RestController
public class DeptController_Consumer {

    //public static final String REST_URL_PREFIX = "http://localhost:8001";
    
    // 这里修改为由原来的 ip 和端口去访问服务提供者，改为通过服务提供者注册进 eureka 的应用名称，通过 eureka 去访问服务提供者
    public static final String REST_URL_PREFIX = "http://microservicecloud-dept";
}
```

⑦ 先启动三个 eureka 集群，再启动 microservicecloud-provider-dept  把服务注册进 eureka

⑧ 再启动 microservicecloudconsumer-dept ，这个工程启动后，之前 controller 里面写了接口，测试一下接口是否能访问成功

**总结：**

> Ribbon 和 Eureka 整合后 Consumer 可以直接调用服务而不用再关心地址和端口号（直接通过注册进 eureka 的服务名称进行调用）

#### 3、Ribbon 负载均衡

![eureka和ribbon架构图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springcloud/eureka%E5%92%8Cribbon%E6%9E%B6%E6%9E%84%E5%9B%BE.png)

> Ribbon 在工作时分成两步：
>
> 第一步先选择 EurekaServer，它优先选择在同一个区域内负载较少的 server
>
> 第二步再根据用户指定的策略，在从 server 取到的服务注册列表中选择一个地址
>
> 其中 Ribbon 提供了多种策略：比如轮询、随机和根据响应时间加权。

**下面说一下步骤：**

① 参考 microservicecloud-provider-dept 工程，再新建两份，分别修改端口号为 8002，8003（新建的这两份工程 pom 文件的依赖和 microservicecloud-provider-dept 一样，代码什么的也和这个工程一样，就等于把这个工程拷贝两份，然后修改下 yml 配置文件）

② 新建 8002 工程数据库，8003 工程数据库，各自微服务分别连各自的数据库

③ 修改 8002 和 8003 各自的 yml 配置文件

```yaml
server:
  port: 8002    # 主要修改端口号

mybatis:
  config-location: classpath:mybatis/mybatis.cfg.xml   
  type-aliases-package: com.hepingfly.springcloud.entites
  mapper-locations: classpath:mybatis/mapper/*.xml  

spring:
  application:
    name: microservicecloud-dept    # 这个名字千万不能改，几个服务提供者通过统一的名称注册到 eureka 中对外提供服务
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource  
    driver-class-name: org.gjt.mm.mysql.Driver
    url: jdbc:mysql://192.168.31.128:3306/springcloud02    # 修改数据库名字，一个微服务连一个数据库
    username: root
    password: 123
eureka:
  client:   
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
  instance:
    instance-id: microservicespringcloud-dept
    prefer-ip-address: false

info:
  app.name: hepingfly-microservicecloud
  company.name: www.hepingfly.com
  build.artifactId: $project.artifactId$
  build.version: $project.version$
  
  #-------------------------------------------------------------------------------------
  server:
  port: 8003    # 主要修改端口号

mybatis:
  config-location: classpath:mybatis/mybatis.cfg.xml   
  type-aliases-package: com.hepingfly.springcloud.entites
  mapper-locations: classpath:mybatis/mapper/*.xml  

spring:
  application:
    name: microservicecloud-dept   # 这个名字千万不能改，几个服务提供者通过统一的名称注册到 eureka 中对外提供服务
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource   
    driver-class-name: org.gjt.mm.mysql.Driver
    url: jdbc:mysql://192.168.31.128:3306/springcloud03  # 修改数据库名字，一个微服务连一个数据库
    username: root
    password: 123
eureka:
  client:   
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
  instance:
    instance-id: microservicespringcloud-dept
    prefer-ip-address: false

info:
  app.name: hepingfly-microservicecloud
  company.name: www.hepingfly.com
  build.artifactId: $project.artifactId$
  build.version: $project.version$
```

总结一下修改注意点：

> 需要修改的：
>
> 1、端口号
>
> 2、连接的数据库名
>
> 不能修改的：
>
> 对外暴露的统一的服务实例名

④ 先启动 3 个 eureka 集群

⑤ 再启动 3 个 microservicecloud-provider-dept 微服务

⑥ 启动 microservicecloudconsumer-dept ，这样客户端通过 Ribbon 完成负载均衡并访问服务提供者提供的服务

> 这里出现的现象就是：
>
> microservicecloudconsumer-dept   这个服务是消费者，3 个 microservicecloud-provider-dept  是服务提供者，这三个服务注册到了 eureka server 中，这时候我服务消费者就会去 eureka server 中获取可用的服务列表，然后我这个服务消费者集成了 Ribbon，我又拿到了可用的服务列表，那么 Ribbon 就可以根据自己的负载均衡算法去调用服务提供者。所以这时候你去访问 consumer 中的接口时，consumer 去调用 provider，那么可能调用到 provider1 也可能调用到 provider2，也可能调用到 provider3，这个是由 Ribbon 的负载均衡算法决定的。

**总结：**

> Ribbon 其实就是一个软负载均衡的客户端组件，它可以和其他所需请求的客户端结合使用（比如这里的 consumer），和 eureka 结合只是其中一个实例。

#### 4、Ribbon 的核心组件 IRule

IRule ：根据特定算法从服务列表中选取一个要访问的服务

> - RoundRobinRule
>   - 轮询
> - RandomRule
>   - 随机
> - AvailabilityFilteringRule
>   - 会先过滤由于多次访问故障而处于断路器跳闸状态的服务，还有并发的连接数量超过阈值的服务，然后对剩余的服务列表按照轮询策略进行访问。
> - WeightedResponseTimeRule
>   - 根据平均响应时间计算所有服务的权重，响应时间越快服务权重越大被选中的概率越高。刚启动时如果统计信息不足，则使用 RoundRobinRule 策略，等统计信息足够，会切换到 WeightedResponseTimeRule
> - RetryRule
>   - 先按照 RoundRobinRule 的策略获取服务，如果获取服务失败则在指定时间内会进行重试，获取可用的服务
> - BestAvailableRule
>   - 会先过滤掉由于多次访问故障而处于断路器跳闸状态的服务，然后选择一个并发量最小的服务
> - ZoneAvoidanceRule
>   - 默认规则，复合判断 server 所在区域的性能和 server 的可用性选择服务器

```java
@Configuration
public class ConfigBean {

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

    @Bean
    public IRule myRule() {  
        return new RandomRule();// 如果想切换负载均衡算法，直接把这个 bean 加入到 ioc 容器中即可
    }

}
```

#### 5、自定义 Ribbon 的负载均衡策略

① 需要修改 microservicecloudconsumer-dept 工程，因为这个工程集成了 Ribbon

② 在主启动类上添加 @RibbonClient 注解

> 在启动该微服务的时候就能去加载我们的自定义 Ribbon 配置类，从而使配置生效

```java
@SpringBootApplication
@EnableEurekaClient
// 我在 MyselfRule.class 里面去自定义自己的负载均衡策略
// 这样在启动该微服务的时候就能去加载我们自定义的类(这个类里面自定义了负载均衡策略)
@RibbonClient(name = "MICROSERVICECLOUD-DEPT", configuration = MyRule.class)
public class DeptConsumerApp {
    public static void main(String[] args) {
        SpringApplication.run(DeptConsumerApp.class,args);
    }
}
```

**注意事项，注意事项，注意事项：**

> 官方文档给出了明确警告：
>
> 就是你自定义的负载均衡策略类不能放在 @ComponentScan 所扫描的当前包下及其子包下，否则我们这个自定义的负载均衡策略类就会被所有的 Ribbon 客户端所共享，也就是说我们达不到特殊化定制的目的了。

```java
/**
 * 自定义负载均衡策略配置类
 */
@Configuration
public class MyRule {

    @Bean
    public IRule myRule() {
        return new RandomRule();  // Ribbon 默认是轮询，我自定义这个类为随机 
    }
}

// 我这个 myRule 类和 SpringBoot 的主启动类，不在同一个包下,因为 @SpringBootApplication 这个注解点进去就是 @ComponentScan
```

假如我现在有个需求：依旧是使用轮询策略，但是要求每个服务器调用 5 次。就是说以前轮询是每个机器一次，现在是每台机器调用 5 次。

### Feign 负载均衡

#### 1、Feign 是什么

> 概述：
>
> Feign 是一个声明式的 WebService 客户端。使用 Feign 能让编写 WebService 客户端更加简单，它的使用方法是定义一个接口，然后在上面添加注解，同时也支持 JAX-RS 标准的注解。Feign 也支持可拔插式的编码器和解码器。SpringCloud 对 Feign 进行了封装，使其支持了 SpringMVC 标准注解和 HttpMessageConversters。Feign 可以与 Eureka 和 Ribbon 组合使用以支持负载均衡。
>
> ---------------------------------------------------------------------------------------------
>
> 说的直白一点就是：
>
> Feign 是一个声明式的 Web 服务客户端，使得编写 Web 服务客户端变得非常容易，只需要创建一个接口，然后在上面添加注解即可。

**Feign 能干什么？**

> Feign 旨在使编写 Java Http 客户端变得更容易。
>
> 前面在使用 Ribbon + RestTemplate 时，利用 RestTemplate 对 http 请求的封装处理，形成了一套模板化的调用方法。但是在实际开发中，由于对服务依赖的调用可能不止一处，往往一个接口会被多处调用，所以通常都会针对每个微服务自行封装一些客户端类来包装这些依赖服务的调用。所以，Feign 在此基础上做了进一步的封装，由它来帮助我们定义和实现依赖服务接口的定义。在 Feign 的实现下，我们只需要创建一个接口并使用注解的方式来配置它（以前是 DAO 接口上面标注 Mapper 注解，现在是一个微服务接口上面标注一个 Feign 注解即可），即可完成对服务提供方的接口绑定，简化了使用 SpringCloud Ribbon 时，自动封装服务调用客户端的开发量。

#### 2、Feign 工程构建

步骤：

① 参考 microservicecloudconsumer-dept 工程，新建 microservicecloudconsumer-dept-feign 工程

② microservicecloudconsumer-dept-feign 工程 pom 文件修改，主要添加对 feign 的支持

```xml
<!-- 在原有依赖的基础上，增加以下依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```

③ 修改  microservicecloudapi 工程，通用的公共的放在这个工程里面

④ 修改  microservicecloudapi 工程 pom 文件，增加 feign 依赖

```xml
<!-- 在原有依赖的基础上，增加以下依赖 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```

⑤ 新建 DeptClientService 接口，并新增注解 @FeignClient

```java
@FeignClient(value = "MICROSERVICECLOUD-DEPT")
public interface DeptClientService {

    @RequestMapping(value = "/dept/get/{id}",method = RequestMethod.GET)
    public Dept get(@PathVariable("id") long id);

    @RequestMapping(value = "/dept/list", method = RequestMethod.GET)
    public List<Dept> list();

    @RequestMapping(value = "/dept/add", method = RequestMethod.POST)
    public boolean add(Dept dept);
}
```

⑥ 执行 maven 的 clean install，因为 microservicecloudapi 工程是公共部分，这样保证其他服务都能用到

⑦ microservicecloudconsumer-dept-feign 工程 修改 controller , 添加上一步新建的 DeptClientService 接口

```java
@RestController
public class DeptController_Consumer {
	
    // 因为我在这个工程的 pom 中已经引入了 microservicecloudapi 的依赖，所以这里我可以直接使用这个接口
    @Autowired(required = false) // 加个 required 是因为接口没有实现类, idea 中会报错,加 required 解决
    private DeptClientService clientService;


    @RequestMapping(value = "/consumer/dept/add",method = RequestMethod.POST)
    public boolean add(Dept dept) {
        return clientService.add(dept);
    }

    @RequestMapping(value = "/consumer/dept/get/{id}",method = RequestMethod.GET)
    public Dept get(@PathVariable("id") Long id) {
        return clientService.get(id);
    }

    @RequestMapping(value = "/consumer/dept/list",method = RequestMethod.GET)
    public List<Dept> list() {
        return clientService.list();
    }
}
```

⑧ microservicecloudconsumer-dept-feign 工程 修改主启动类

```java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients(basePackages = {"com.hepingfly.springcloud"})   // 开启 feign
@ComponentScan(basePackages = {"com.hepingfly.springcloud"})
public class DeptConsumerApp {
    public static void main(String[] args) {
        SpringApplication.run(DeptConsumerApp.class,args);
    }
}
```

⑨ 测试

> 1）、启动 3 个 eureka 集群
>
> 2）、启动 3 个 部门服务提供者微服务，这样这 3 个服务会注册进 eureka 集群
>
> 3）、启动 Feign 所在的工程 microservicecloudconsumer-dept-feign
>
> 4）、访问接口 `http://localhost:8001/consumer/dept/list`

**小总结：**

> Feign 通过接口的方法调用 Rest 服务（之前我们是通过 Ribbon + RestTemplate）
>
> 该请求直接发送给 Eureka 服务器（`http://MICROSERVICECLOUD-DEPT/dept/list`），通过 Feign 直接找到服务接口，由于在进行服务调用的时候融合了 Ribbon 技术，所以也支持负载均衡作用。

### Hystrix 断路器

#### 1、Hystrix 断路器是什么

分布式系统面临的问题：

> 复杂分布式体系结构中的应用程序有数十个依赖关系，每个依赖关系在某些时候将不可避免的失败。

服务雪崩：

> 多个微服务之间调用的时候，假设微服务A 调用微服务B 和 微服务C ，微服务B 和微服务C 又调用其他的微服务，这就是所谓的「扇出」。如果扇出的链路上某个微服务的调用响应时间过长或者不可用，对微服务A 的调用就会占用越来越多的系统资源，进而引起系统崩溃，所谓的「雪崩效应」。
>
> 对于高流量的应用来说，单一的后端依赖可能会导致所有服务器上的所有资源都在几秒钟内饱和。比失败更糟糕的是，这些应用程序还可能导致服务之间的延迟增加，备份队列，线程和其他系统资源紧张，导致整个系统发生更多的级联故障。这些都表示需要对故障和延迟进行隔离和管理，以便单个依赖关系的失败，不能取消整个应用程序或系统。

**Hystrix 是什么？**

> Hystrix 是一个用于处理分布式系统的延迟和容错的开源库，在分布式系统里，许多依赖不可避免的会调用失败，比如超时、异常等。Hystrix 能够保证在一个依赖出问题的情况下，不会导致整体服务失败，避免级联故障，以提高分布式系统的弹性。
>
> 断路器本身是一种开关装置，当某个服务单元发生故障之后，通过断路器的故障监控，向调用方返回一个符合预期的、可处理的备选响应（FallBack），而不是长时间的等待或者抛出调用方无法处理的异常，这样就保证了服务调用方的线程不会被长时间不必要的占用，从而避免了故障在分布式系统中的蔓延，乃至雪崩。

#### 2、服务熔断

**服务熔断是什么**

> 熔断机制是应对雪崩效应的一种微服务链路保护机制。
>
> 当扇出链路的某个微服务不可用或者响应时间太长时，会进行服务的降级，进而熔断该节点微服务的调用，快速返回错误的响应信息。当检测到该节点微服务调用响应正常后恢复调用链路。在 Springcloud框架里熔断机制通过 Hystrix实现。 Hystrix 会监控微服务间的调用情况，当失败的调用到一定的阈值，缺省是 5 秒内 20 次调用失败就会启动熔断机制。熔断机制的注解是 `@HystrixCommand`

步骤：

① 参考 microservicecloud-provider-dept 工程，新建 microservicecloud-provider-dept-hystrix 工程（大致上是把原有工程复制一遍然后修改 pom 和 yml 配置文件）

② 修改 pom 文件，增加 hystrix 依赖

```xml
<dependencies>
        <!-- 引入自己定义的 api 通用包，这样就可以使用 Dept 部门 Entity -->
        <dependency>
            <groupId>com.hepingfly.springcloud</groupId>
            <artifactId>microservicecloud-api</artifactId>
            <version>${project.version}</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>

        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
        </dependency>

        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-core</artifactId>
        </dependency>

        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jetty</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>springloaded</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
	
    	<!-- 增加 hystrix 依赖 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-hystrix</artifactId>
        </dependency>
    </dependencies>
```

③ 修改 yml 配置文件

```yaml
server:
  port: 8001

mybatis:
  config-location: classpath:mybatis/mybatis.cfg.xml   
  type-aliases-package: com.hepingfly.springcloud.entites
  mapper-locations: classpath:mybatis/mapper/*.xml  

spring:
  application:
    name: microservicecloud-dept
  datasource:
    type: com.alibaba.druid.pool.DruidDataSource   
    driver-class-name: org.gjt.mm.mysql.Driver
    url: jdbc:mysql://192.168.31.128:3306/springcloud
    username: root
    password: 123
eureka:
  client:   
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
  instance:
    instance-id: microservicespringcloud-dept-hystrix     # 服务名称修改了一下，表明这个微服务是带熔断器的微服务
    prefer-ip-address: false

info:
  app.name: hepingfly-microservicecloud
  company.name: www.hepingfly.com
  build.artifactId: $project.artifactId$
  build.version: $project.version$
```

④ 修改 DeptController

```java
@RestController
public class DeptController {

    @Autowired
    private DeptService deptService;
    // 一旦调用服务方法失败并抛出了错误信息后，会自动调用 @HystrixCommand 标注好的 fallbackMethod 调用类中的指定方法
    @HystrixCommand(fallbackMethod = "processHystrixGet")
    @RequestMapping(value = "/dept/get/{id}",method = RequestMethod.GET)
    public Dept get(@PathVariable("id") Long id) {
        return deptService.get(id);
    }

    public Dept processHystrixGet(@PathVariable("id") Long id) {
        // 这里可以定义自己需要返回的内容
        return new Dept();
    }


}
```

⑤ 修改主启动类

```java
@SpringBootApplication
@EnableEurekaClient      // 加上这个注解后，本服务启动后会自动注册进 eureka 服务中
@EnableDiscoveryClient   // 服务发现
@EnableCircuitBreaker  // 对 hystrixR熔断机制的支持
public class DeptProviderApp {
    public static void main(String[] args) {
        SpringApplication.run(DeptProviderApp.class,args);
    }
}
```

⑥ 测试

> 1、先启动 3 个 eureka server
>
> 2、启动 DeptProviderApp  主启动类
>
> 3、启动一个消费者，访问消费者，消费者会去调用加了 hystrix 了服务提供者，一旦报错对执行对应的方法

#### 3、服务降级

**服务降级是什么**

> 整体资源块不够了，忍痛将某些服务先关掉，待渡过难关，再开启回来

**注：**

服务降级处理是在客户端实现完成的，与服务端没有关系

**服务熔断和服务降级：**

> 服务熔断：
>
> 服务熔断的作用类似于我们家用保险丝，当某服务出现不可用或响应超时情况时，为了防止整个系统雪崩，暂时停止对该服务的调用。
>
> 服务降级：
>
> 服务降级是从整个系统的负荷情况出发和考虑的，对某些负荷会比较高的情况，为了预防某些功能（业务场景）出现负荷过载或者响应慢的情况，在其内部暂时舍弃对一些非核心的接口和数据的请求，而直接返回一个提前准备好的 fallback（退路）错误处理信息。这样虽然提供的是一个有损服务，但却保证了整个系统的稳定性和可靠性。
>
> 相同点：
>
> - 都是从系统的可用性和可靠性出发，为了防止系统崩溃
> - 最终让用户体验到的都是某些功能暂时不可用
>
> 不同点：
>
> - 触发的原因不同，服务熔断一般是某个服务（下游服务）不可用引起的，而服务降级一般是从整体负荷考虑。

上面服务熔断的方式：

```java
@RestController
public class DeptController {

    @Autowired
    private DeptService deptService;
    @HystrixCommand(fallbackMethod = "processHystrixGet")
    @RequestMapping(value = "/dept/get/{id}",method = RequestMethod.GET)
    public Dept get(@PathVariable("id") Long id) {
        return deptService.get(id);
    }

    public Dept processHystrixGet(@PathVariable("id") Long id) {
        return new Dept();
    }
}
// 这种方式其实有问题，因为我每写一个方法就要有一个 fallbackMethod 与之对应，这样方法多了以后，就很容易造成方法膨胀。并且处理异常的方法和你业务逻辑方法绑定在一起,造成高耦合,程序可维护性不好。
```

步骤：

① 修改 microservicecloudapi 工程，根据已有的 DeptClientService 接口，新建一个实现了 FallbackFactory 接口的类 DeptClientServiceFallbackFactory 

```java
/**
 * 这就相当于把 DeptClientService 里面每一个方法的 fallback 方法统一
 * 写在这个类里面,在这个类里面处理,这样就可以和业务方法进行解耦
 */
@Component  // 这个注解千万不能忘记添加
public class DepartClientServiceFallbackFactory implements FallbackFactory<DeptClientService> {
    @Override
    public DeptClientService create(Throwable throwable) {
        return new DeptClientService() {
            @Override
            public Dept get(long id) {
                return new Dept();
            }

            @Override
            public List<Dept> list() {
                return null;
            }

            @Override
            public boolean add(Dept dept) {
                return false;
            }
        };
    }
}
```

② 修改 microservicecloudapi 工程，DeptClientService 接口，在注解 @FeignClient 中添加 fallbackFactory 属性

```java
@FeignClient(value = "MICROSERVICECLOUD-DEPT",fallbackFactory = DepartClientServiceFallbackFactory.class)  // 添加 fallbackFactory 属性,去指定统一处理熔断的类
public interface DeptClientService {

    @RequestMapping(value = "/dept/get/{id}",method = RequestMethod.GET)
    public Dept get(@PathVariable("id") long id);

    @RequestMapping(value = "/dept/list", method = RequestMethod.GET)
    public List<Dept> list();

    @RequestMapping(value = "/dept/add", method = RequestMethod.POST)
    public boolean add(Dept dept);
}

// 假如 DeptClientService 中的方法,调用出异常了,那么就会去找 fallbackFactory, fallbackFactory 指定了统一处理熔断的类 DepartClientServiceFallbackFactory,这个类中的方法会帮忙兜底。
```

③ 把  microservicecloudapi 工程进行重新 clean install，这样别的工程就可以进行调用到最新的包

④ 修改 microservicecloudconsumer-dept-feign 工程，修改 yml 配置文件

```yaml
server:
  port: 8002
eureka:
  client:
    register-with-eureka: false
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka/,http://eureka7002.com:7002/eureka/,http://eureka7003.com:7003/eureka/
feign:         # 添加这个注解,开启 hystrix
  hystrix:
    enabled: true
```

⑤ 测试

> 1）、先启动 3 个 eureka 服务
>
> 2）、启动 microservicecloud-provider-dept 微服务
>
> 3）、启动 microservicecloudconsumer-dept-feign 微服务
>
> 4）、正常访问测试
>
> 5）、故意关闭微服务  microservicecloud-provider-dept
>
> 6）、会发现客户端会有自己调用提示（此时服务端 provider 已经 down 了，但是我们做了服务降级处理，让客户端在服务端不可用时也会获得提示信息而不会挂起耗死服务器）

### 服务监控

#### 1、hystrixDashboard

**概述：**

> 除了隔离依赖服务的调用以外，Hystrix 还提供了准实时的调用监控（Hystrix Dashboard），Hystrix 会持续的记录所有通过 Hystrix 发起的请求的执行信息，并以统计报表和图形的形式展示给用户，包括每秒执行多少请求，多少成功多少失败等。Netflix 通过 hystrix-metrics-event-stream 项目实现了对以上指标的监控。SpringCloud 也提供了 Hystrix Dashboard 的整合，对监控内容转换成可视化界面。

步骤：

① 新建工程 microservicecloudconsumer-hystrix-dashboard 工程

② pom 文件

```xml
<!-- 加上以下注解 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
</dependency>
<!-- 监控系统健康情况 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

③ 主启动类

```java
@SpringBootApplication
@EnableHystrixDashboard
public class DeptConsumer_DashBoardApp {
    public static void main(String[] args) {
        SpringApplication.run(DeptConsumer_DashBoardApp.class,args);
    }
}
```

**我这个微服务是进行服务监控功能的，所以所有 Provider 微服务提供类，都需要监控依赖配置，就是 `spring-boot-starter-actuator` 这个 pom 依赖，所有的 provider 微服务提供类都要有。**

④ 启动 microservicecloudconsumer-hystrix-dashboard 。然后访问 `http://localhost:9001/hystrix` 出现豪猪界面，就证明服务启动成功。

⑤ 启动 3 个 eureka 集群

⑥ 启动 microservicecloud-provider-dept-hystrix（端口号是 8001） ，启动这个带熔断服务的服务提供者。所以现在我们一共启动了 5 个微服务，3 个eureka微服务，一个带熔断服务的服务提供者，还有一个  hystrix-dashboard进行服务监控，9001 端口现在去监控 8001 服务

⑦ 服务提供者微服务是去访问了数据库，所以你可以访问这个接口 `http://localhost:8001/dept/get/1` （这个是服务提供者 controller 里面的接口），观察监控窗口。

> 在豪猪监控页面你可以填写监控地址：
>
> 如 ：   `http://localhost:8001/hystrix.stream`    其中 ip 和端口是你要监控的微服务的  ip 和端口
>
> 监控结果的图形化展示：
>
> - 实心圆
>   - 共有两种含义。它通过颜色的变化代表了实例的健康程度，它的健康度从 绿色 < 黄色 < 橙色 < 红色 递减。该实心圆除了颜色变化之外，它的大小也会根据实例的请求流量发生变化，流量越大该实心圆就越大。所以通过实心圆的展示，就可以在大量实例中快速的发现故障实例和高压力实例。
> - 曲线：
>   - 用来记录 2 分钟内流量的相对变化，可以通过它来观察到流量的上升和下降趋势。

### zuul 路由网关

#### 1、zuul 是什么

> Zuul 包含了对请求的路由和过滤两个最主要的功能：
>
> 其中路由功能负责将外部请求转发到具体的微服务实例上，是实现外部访问统一入口的基础，而过滤器功能则负责对请求的处理过程进行干预，是实现请求校验、服务聚合等功能的基础。zuul 和 eureka 进行整合，需要将 zuul 自身注册为 eureka 服务治理下的应用，同时从 eureka 中获得其他微服务的消息，也即以后的访问微服务都是通过 zuul 跳转后获得。
>
> **注：**
>
> zuul 服务最终还是会注册进 Eureka

#### 2、zuul 路由基本配置

步骤：

① 新建工程 microservicecloud-zuul-gateway 

② pom 文件修改

```xml
<dependencies>
        <!--zuul 路由网关-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-zuul</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
        </dependency>

        <!--修改后立即生效，热部署-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>springloaded</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
        </dependency>

        <!--将微服务 provider 注册进 eureka-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-eureka</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-config</artifactId>
        </dependency>

        <!--actuator 监控-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <!--hystrix 容错-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-hystrix</artifactId>
        </dependency>
    </dependencies>
```

③ yml 配置文件

```yaml
server:
  port: 9527
spring:
  application:
    name: microservicecloud-zuul-gateway  # 注册进 eureka 中唯一微服务名字
eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
  instance:
    instance-id: myzuul
    prefer-ip-address: true
```

**注：**

这里需要配置一下 host 文件     127.0.0.1    myzuul

④ 主启动类

```java
@SpringBootApplication
@EnableZuulProxy   // 开启 zuul 代理
public class ZuulSpringCloudApp {
    public static void main(String[] args) {
        SpringApplication.run(ZuulSpringCloudApp.class, args);
    }
}
```

⑤ 先启动 3 个 eureka 集群，一个服务提供类 microservicecloud-provider-dept， 一个路由服务 microservicecloud-zuul-gateway 

⑥ 测试

> 不用路由：
>
> 访问 `http://localhost:8001/dept/get/2`   相当于直接访问服务提供者
>
> 启用路由：
>
> 访问 `http://myzuul:9527/microservicecloud-dept/dept/get/2`
>
> myzuul 是 zuul 注册到 eureka 中的实例名，9521 是 zuul 微服务端口号，microservicecloud-dept 是服务提供者注册到 eureka 中的实例名，/dept/get/2   是 restful 风格的地址。也就是说你去访问 zuul 地址，然后 zuul 在 eureka 中找到了一个叫 microservicecloud-dept 的微服务，然后去访问它 dept/get/2   restful 地址。
>
> 所以可以分为三段：
>
> `myzuul:9527`  ：网关地址
>
> `microservicecloud-dept` ：真实微服务地址
>
> `dept/get/2`：具体 rest 风格地址

#### 3、zuul 路由访问映射规则

> 使用 zuul 后，我们访问服务，可以这么访问：`http://myzuul:9527/microservicecloud-dept/dept/get/2`
>
> 但是 microservicecloud-dept 是真实的微服务地址，我们不想把它暴露在外面，所以我们可以配置一个映射规则。

方法就是修改 zuul 所在微服务的 yml 配置文件，增加路由映射规则：

```yaml
server:
  port: 9527
spring:
  application:
    name: microservicecloud-zuul-gateway  # 注册进 eureka 中唯一微服务名字
eureka:
  client:
    service-url:
      defaultZone: http://eureka7001.com:7001/eureka,http://eureka7002.com:7002/eureka,http://eureka7003.com:7003/eureka
  instance:
    instance-id: gateway.com
    prefer-ip-address: true

# 配置 zuul 路由映射规则
zuul:
  routes:
    mydept.serviceId: microservicecloud-dept
    mydept.path: /mydept/**
```

> 之前我们是这么访问接口的：
>
> `http://myzuul:9527/microservicecloud-dept/dept/get/2`
>
> 配置完路由映射规则就可以这么访问：
>
> `http://myzuul:9527/mydept/dept/get/2`
>
> 但是这样也会存在问题：
>
> 就是通过配置的路由映射规则地址可以访问服务，但是原有通过具体微服务名字（`http://myzuul:9527/microservicecloud-dept/dept/get/2`） 这种也还可以访问。如果希望只能通过配置的路由映射地址去访问，那么需要加一下配置：
>
> ```yaml
> # 配置 zuul 路由映射规则
> zuul:
>   routes:
>     mydept.serviceId: microservicecloud-dept  # 配置具体微服务名称和路径的映射关系
>     mydept.path: /mydept/**
>   ignored-services: microservicecloud-dept   # 加了这个就不能通过具体的微服务名字 microservicecloud-dept  去访问了(忽略原真实服务名)
>   
> #------------------------------------------------------------------------------------
> zuul:
>   routes:
>     mydept.serviceId: microservicecloud-dept  
>     mydept.path: /mydept/**
>   ignored-services: "*"     # 假如你想忽略多个,就可以这么写
> ```
>
> 另外访问地址可以**设置统一前缀**：
>
> 比如我这几个微服务都部署在北京，那么我想用 beijing 作为前缀，那么久可以这么配置：
>
> ```yaml
> zuul:
>   routes:
>     mydept.serviceId: microservicecloud-dept
>     mydept.path: /mydept/**
>   ignored-services: microservicecloud-dept
>   prefix: /beijing      # 这个配置就表示访问的时候 url 都要加上 /beijing 作为前缀
>   
>   # 如原来是这么访问的：http://myzuul:9527/mydept/dept/get/2
>   # 现在访问方式就变成：http://myzuul:9527/beijing/mydept/dept/get/2
> ```
>
> 

































