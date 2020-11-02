# SpringCloud笔记2

------

### 一、SpringCloud 相关介绍

#### 1、SpringCloud 和 SpringBoot 版本选型

1）、Springboot 版本选择

SpringBoot 官方强烈建议升级到 2.X 以上版本

2）、SpringCloud 版本选择

> - SpringCloud 的版本是以伦敦地铁站英文首字母来作为版本号的命名。由地铁站名称字母 A-Z 依次类推的形式来发布迭代版本。
> - 因为 SpringCloud 是一个由许多子项目组成的综合项目，各子项目有不同的发布节奏。为了管理 SpringCloud 与各个子项目的版本依赖关系，发布了一个清单，其中包括了某个 SpringCloud 版本对应的子项目版本。为了避免 SpringCloud 版本号与子项目版本版本号混淆，SpringCloud 采用了名称而非版本号的命名，这些版本的名字采用了伦敦地铁站的名字，根据字母表的顺序来对应版本时间顺序。
> - 当 SpringCloud 的发布内容积累到临界点或者一个重大 bug 被解决后，会发布一个 "service releases" 版本，简称 SRX 版本。比如 Greenwich.SRX2 就是 SpringCloud 发布的 Greenwich 版本的第 2 个 SRX 版本。

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









