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











































































