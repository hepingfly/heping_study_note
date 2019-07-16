# SpringBoot笔记

### 									SpringBoot简介

#### 一、SpringBoot介绍

##### 1、什么是SpringBoot？

SpringBoot 是 Spring 团队在 2014 年伴随 Spring4.0 版本开发出的一个框架叫 SpringBoot。

##### 2、简介

Spring Boot 用来简化 Spring 应用开发，约定大于配置，去繁从简，just run 就能创建一个独立的，产品级别的应用。

##### 3、SpringBoot 产生的背景

J2EE笨重的开发、繁多的配置、低下的开发效率、复杂的部署流程、第三方集成难度大

##### 4、解决方案

”Spring 全家桶时代“

SpringBoot  ——>  J2EE 一站式解决方案

SpringCloud  —> 分布式整体解决方案

##### 5、优点

> - 快速创建独立运行的 Spring 项目以及与主流框架集成
> - 使用嵌入式的 Servlet 容器，应用无需打成 WAR 包。可以直接打成 jar 包，然后使用 java -jar 命令运行
> - starters（启动器） 自动依赖与版本控制
> - 大量的自动配置，简化开发，也可修改默认值
> - 无需配置 XML，无代码生成，开箱即用
> - 准生产环境的运行时应用监控
> - 与云计算的天然集成

##### 6、微服务

- 其实早在 2014 年，有一个叫 「martin fowler」的人，他发表了而一篇关于微服务的博客，非常生动形象的介绍了微服务的设计思想及一些理念，然后微服务才慢慢给人们所熟知。
- 微服务：他其实是一种架构风格，它希望一个应用应该是一组小型服务，可以通过 HTTP 的方式进行互通


**总结一下：**

SpringBoot 可以说是在 Spring 的基础上，对 Spring 进行的一个再封装

#### 二、环境准备

##### 1、本地 maven 配置

在 maven 的 settings.xml 配置文件中找到 profiles 标签，加上如下内容（指定 maven 使用 jdk1.8 来编译）：

```xml
<profiles>
    <profile>
      <id>jdk-1.8</id>
      <activation>
        <activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
      </activation>
      <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
      </properties>
    </profile>
</profiles>
```

##### 2、idea 中 maven 配置

打开 idea 点击 settings ，在 Build Tools --> Maven 点击配置好 maven 的根目录、配置文件、仓库

#### 三、Spring Boot HelloWorld

##### 1、创建一个 maven 工程

使用 idea 直接 New Project 然后选择 maven 工程即可

##### 2、导入 spring boot 相关的依赖

```xml
	<!--springBoot 相关依赖 -->
	<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
    </parent>
	<!--springBoot 相关依赖 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!--这个插件,可以将应用打包成一个可执行的 jar 包-->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

```

##### 3、编写一个主程序，用来启动 spring boot 应用

```java
/**
 * @SpringBootApplication 来标注一个主程序类,说明这是一个 Spring Boot 应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {
    //快捷键psvm
    public static void main(String[] args) {
        //spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
        // 让这个 spring 应用跑起来，第一个参数把这个主程序类传进来，第二个参数把 main 方法的 args 参数传进来
    }
}
```

##### 4、编写相关的 Controller 和 Service

```java
@Controller
public class HelloController {
    @ResponseBody
    @RequestMapping("/hello")
    public String hello() {
        return "hello springBoot";
    }
}
```

##### 5、运行主程序进行测试myfirstProject-1.0-SNAPSHOT.jarmyfirstProject-1.0-SNAPSHOT.jar

运行我们刚才编写的 spring boot 主程序进行测试，直接 Run 即可

#### 四、Hello World 探究

通过上面的 HelloWorld 程序，我们发现 SpringBoot 确实比较简单好用，它只需要写一个主程序来启动 SpringBoot 的应用，然后我们就可以去写我们的业务逻辑了，像 Controller 、Service等。配置方面可以说我们基本上没有写，为什么会有这么神奇呢？我们先从它的 pom.xml 文件看一下：

##### 1、POM 文件

```xml
在 SpringBoot 的 pom 文件中我们会引入这样一个依赖：
<parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
</parent>

这个 parent 点进去之后你会发现它还会有个 parent：
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>1.5.9.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
<artifactId>spring-boot-starter-parent</artifactId>

上面这个 parent 我再点进去：
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-dependencies</artifactId>
<version>1.5.9.RELEASE</version>   在这个里面 SpringBoot 帮你管理了很多的依赖
```

所以 「spring-boot-dependencies」可以看成是 SpringBoot 的==版本仲裁中心==，以后我们导入依赖默认是不需要写版本的，因为你所有依赖的版本信息都在 「spring-boot-dependencies」中帮你管理了（当然，如果没有在 dependencies 里面管理的依赖仍然还是需要声明版本号的）

因此导入 「spring-boot-starter-parent」这个依赖，就可以把它理解为版本仲裁的作用。

##### 2、启动器

```xml
在 pom 文件中我们还引入了这么一个依赖，下面说一下这个依赖的作用： 
<dependencies>
    <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-web</artifactId>   <!--这里也没写版本号,因为springboot会自动帮我们仲裁 -->
    </dependency>
 </dependencies>
```

我们分两块来看 「spring-boot-starter-web」

> - spring-boot-starter：这个称为 springBoot 的场景启动器
> - spring-boot-starter-web：这个就是 web 模块的场景启动器，帮我们导入了 web 模块正常运行所依赖的组件（上面那个 artifactId 你点进去就会发现它帮我们导入了很多 web 相关的依赖）
>
> 总结：
>
> SpringBoot 将所有的功能场景都抽取出来，做成一个个的 starters（启动器），只需要在项目里面引入这些  starter 相关场景，所有的依赖都会导入进来，要用什么功能就导入什么场景启动器。

##### 3、主程序类

```java
/**
 * @SpringBootApplication 来标注一个主程序类,说明这是一个 Spring Boot 应用
 */
@SpringBootApplication
public class HelloWorldMainApplication {
    //快捷键psvm
    public static void main(String[] args) {
        //spring应用启动起来
        SpringApplication.run(HelloWorldMainApplication.class,args);
        //这里传入的参数一定要是 @SpringBootApplication 注解标注的类
    }
}
```

重点来说一下 **@SpringBootApplication** 这个注解：

这个注解标注在某个类上说明这个类是 SpringBoot 的主程序类，SpringBoot 就应该运行这个类的 main 方法来启动 SpringBoot 应用。

把这个注解点进去之后你会发现，它是由下面这么多注解组成的：

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration     //这个注解标注在某个类上，表明这是一个 SpringBoot 的配置类
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
   .........
}
```

> - 先来看下 **@SpringBootConfiguration** 这个注解
>
>   - ```java
>     //这个注解点进来之后你会发现它其实也就是用 @Configuration 来标识这是一个配置类
>     @Target({ElementType.TYPE})
>     @Retention(RetentionPolicy.RUNTIME)
>     @Documented
>     @Configuration    //标了这个注解表明这是一个配置类，这个注解点进去你会发现有 @Compoent 修饰，说明配置类也是容器中的一个组件
>     public @interface SpringBootConfiguration {
>     }
>     ```
>
>   - 其实 **@SpringBootConfiguration** 标识在类上就表明这是一个 SpringBoot 的配置类，用 **@Configuration** 标识在类上表明这是一个 Spring 的配置类。一个是 SpringBoot 的注解，一个是 Spring 的注解
>
> - 再来看下 **@EnableAutoConfiguration**
>
>   - 这个注解的意思是「<u>开启自动配置功能</u>」
>
>   - 以前我们需要配置的东西，SpringBoot 会自动帮我们配置，**@EnableAutoConfiguration** 告诉 SpringBoot 开启自动配置功能，这样自动配置才能生效。
>
>   - ```java
>     //这个注解点进去之后是这样的
>     @Target({ElementType.TYPE})
>     @Retention(RetentionPolicy.RUNTIME)
>     @Documented
>     @Inherited
>     @AutoConfigurationPackage
>     @Import({EnableAutoConfigurationImportSelector.class})
>     public @interface EnableAutoConfiguration {
>         .....
>     }
>     ```
>
>     - **@AutoConfigurationPackage** 
>
>       - 这个注解的意思是 「自动配置包」
>
>       - ```java
>         //点进去之后你会发现它也是用了 Spring 的底层注解 @Import，给容器导入组件
>         @Target({ElementType.TYPE})
>         @Retention(RetentionPolicy.RUNTIME)
>         @Documented
>         @Inherited
>         @Import({Registrar.class})  //这个注解的作用就是给容器中导入一个组件
>         //Registrar 这个类你点进去会发现，它会把注解标注类所在的包获取到，进而注解标注类所在包及其子包才会被扫描
>         public @interface AutoConfigurationPackage {
>         }
>         ```
>
>       - ==所以这个注解真正的作用就是将主配置类（***<u>@SpringBootConfiguration</u>***标注的类）所在包及其下面所有子包里面的所有组件扫描到 Spring 容器中==
>
>       - 所以只要你配置了 @AutoConfigurationPackage 这个注解，就会把这个类所在包及其子包下所有的组件扫描到 Spring 容器中，因为这个注解包含在   **@SpringBootApplication**  里面，所以 **@SpringBootApplication** 注解标注的类，所在的包及其子包，只要有组件就会被扫描进 spring 容器中。
>
>     - **@Import({EnableAutoConfigurationImportSelector.class})**
>
>       - ```java
>         public class AutoConfigurationImportSelector {
>             // 这个类中的 selectImports 方法会将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中
>             public String[] selectImports(AnnotationMetadata annotationMetadata)  		{
>                 ......
>               }
>         }
>         ```
>
>
>       - 我们说 **@Import** 注解是给容器导入组件用的，「EnableAutoConfigurationImportSelector」这个类就代表导入哪些组件的选择器，会将所有需要导入的组件以全类名的方式返回，这些组件就会被添加到容器中
>        
>       - 容器中会导入非常多的自动配置类（xxxAutoConfiguration），这个自动配置类意思就是，比如说你现在用的 webmvc 项目，之前一般我们都会配置一些包扫描器、视图解析器等。现在就不用你自己去配，自动配置类都帮你配置好了。再比如说你现在想用 AOP ，也有 AOP 对应的自动配置类，这样也不用你手动配置 AOP 了（有了自动配置类，免去了我们手动编写配置和注入功能组件的工作）
>        
>       - 总结一下：<u>SpringBoot 在启动的时候从类路径下的 META-INF/spring.factories 中获取 「EnableAutoConfiguration」指定的值，将这些值作为自动配置类到如到容器中，这样自动配置类就会生效，帮我们进行自动配置工作</u>
>        
>       - ```java
>         protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
>             // 这个方法会去获取 EnableAutoConfiguration 的值，最后返回
>             // 返回完之后会把值作为配置类加到容器中
>                 List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
>                 Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
>                 return configurations;
>             }
>         ```
>        
>       - 最后再说一下，上面也说了 @Import 这个注解是导入组件用的，但是具体导入哪些组件由EnableAutoConfigurationImportSelector.class 这个类决定，这个类你点进去源码发现它就是获取类路径下的 META-INF/spring.factories 文件中 「EnableAutoConfiguration」指定的值，所以这个「EnableAutoConfiguration」的值都会被扫描进 spring 容器中，这样就达到了 springboot 帮助我们配置，就不用我们手动配置了

#### 五、使用 Spring Initializer 快速创建 Spring Boot 项目

我们平时用的 IDE 像 Eclipse 或者 IDEA 都支持使用 Spring 的项目创建向导快速创建一个 Spring Boot 项目。

> 向导：
>
> 1、如果你使用的是 idea 那么可以  file  —> new —> project  —>  Spring Initializer 然后按照向导即可
>
> 2、如果你使用的是 eclipse 那么可以   new  —>  Spring start Project  然后按照向导接口

向导会连上 spring 官网帮我们创建好项目，所以如果你使用 spring Initializer 创建 springboot 项目一定要连网。

**Tips：**

使用 springboot 向导创建 springboot 项目的时候，会给你的 pom 文件导入下面这个依赖，主要是用来做单元测试的。

```Xml
<!--springboot 进行单元测试的 starter-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```



生成的 springBoot 项目，默认会有两个主要的目录结构，一个是 「src/main/java」 另一个是 「src/main/resources」

主要来说一下 「src/main/resources」这个目录结构下有哪些重要的文件夹：

1. static 文件夹：这个包下面主要保存所有的静态资源（如：js、css、images）
2. templates 文件夹：这个包下面主要用来保存所有的模板页面。因为 SpringBoot 默认是给你打 jar 包它使用的是嵌入式的 Tomcat，默认是不支持 JSP 页面的，但是我们可以使用模板引擎（freemarker、thymeleaf），这些模板引擎页面就可以放在这个文件夹下。
3. application.properties 文件：SpringBoot 应用的配置文件。我们知道 SpringBoot 都是默认配置的，但是这些默认配置我们都可以在 application.properties 文件中去修改它。


**小说明：**

```java
/**
 * @ResponseBody 这个注解如果直接写在类上,就表示这个类的所有方法返回的数据都直接写给浏览器（如果是对象还会帮你转成 json）
 * 但是：
 * @ResponseBody 注解和 @Controller 可以合在一起用 @RestController 注解
 */
@ResponseBody
@Controller
@RestController
public class HelloController {

    @RequestMapping("/hello")
    public String hello() {
        return "hello springboot quick start";
    }
}
```



------

### 										SpringBoot  配置

#### 一、配置文件

##### 1、SpringBoot 使用一个全局的配置文件

> - application.properties 
> - application.yml
>
> 注：上面两个配置文件的名字是固定的，不能随便乱改

①说明：

SpringBoot 会默认使用上面两种配置文件中的任意一种来作为配置文件，也就是说在你的类路径下，有 application.properties 文件或者 application.yml 文件，SpringBoot 就会把它当成全局配置文件。

② 配置文件的作用就是：

就是用来修改 springBoot 自动配置的默认值，因为我们都知道我们不会修改任何的配置 SpringBoot 就启动了。启动的原因就是 SpringBoot 在底层将所有的东西都给我自动配置好了。但是要是有些默认配置我们不满意，这时候就需要 SpringBoot 的配置文件了，我们可以通过修改配置文件来改相关配置。（比如说 SpringBoot 内嵌 tomcat 的端口号是 8080，我不想用这个端口号了，那我就可以通过修改配置文件来改这个端口号）

③配置文件的位置：

配置文件可以放在 「**src/main/resources** 」目录或者 「**类路径/config** 」下

④说一下 YAML 这个配置文件：

> - YAML（YAML Ain't Markup Language）
>   - YAML A Markup Language： 是一个标记语言
>   - YAML isn't Markup Language：不是一个标记语言
>
> 不管它是不是标记语言，它跟标记语言其实都脱不了关系。
>
> - 以前的配置文件，我们大多使用 xxx.xml 文件
> - YAML ：以数据为中心，比 json 、xml 等更适合做配置文件
>
> 下面来看一个 YAML 配置的例子：
>
> ```yaml
> server:
> 	port:8081
> ```
>
> XML 配置的例子：
>
> ```xml
> <server>
> 	<port>8081</port>
> </server>
> ```
>
> 通过上面的例子我们可以发现，使用 xml 作为配置文件，大量的数据都浪费在了标签的开闭上。而使用 YAML 作为配置文件，就很简洁，它是以数据为中心的。

#### 二、YMAL语法

##### 1、基本语法

①使用 YMAL 作为配置文件，我们首先要知道怎么样去表示属性和值的关系：

`使用 「key:空格value」 来表示一个键值对`（空格必须要有）

②假如你写的几个属性之间有层级关系，我们是以<u>**空格的缩进**</u>来控制层级关系的，只要是左对齐的一列数据，都是同一个层级的。例：

```yaml
server:
    port: 8081             #用空格的缩进来控制层级关系,空格多少无所谓,只要左边一列是对齐就是同一层级的
    path: /hello                        #只要左边一列是对齐的,就证明这几个属性是同一层级的
```

注：YAML 里面属性和值是大小写敏感的

##### 2、值的写法

我们看任何一种语法表达，就看它能不能写下面这几种值：

①字面量：普通的值（数字、字符串、布尔）

> 写法就是：「key:空格value」
>
> - 如果你的 value 值是字符串默认不用加上单引号或者双引号
>
> - 但是如果你写了双引号，它会有另一个作用，就是***不会*转义字符串里面的特殊字符**，这个特殊字符就会把本身想表达的意思表达出来。例：
>
>   name: "Jimmy \n kimi"    输出：   Jimmy 换一行 kimi
>
> - 但是如果你写了单引号，它会有另一个作用，就是***会*转义字符串里面的特殊字符**，这个特殊字符最终会被当成字符串输出出来。例：
>
>   name: 'Jimmy \n kimi'      输出：Jimmy \n kimi

②对象，我们要知道在 YAML 语法中怎么去表示一个对象，对象说白了就是属性和值，也就是键值对

> 写法就是：
>
> ```yaml
> 对象名:
> 	属性名: 属性值
> #下面举个例子
> friends:
>     lastName: Jimmy
>     age: 44
> ```
>
> 行内写法：
>
> ```yaml
> 对象名: {属性名: 属性值}
> #下面举个例子：
> friend: {lastName: Jimmy,age: 44}
> ```
>
> 

③数组（包括 List 和 Set）

> 用 -空格 值 表示数组中的一个元素
>
> 写法就是：
>
> ```yaml
> 数组名:
>     - 值1
>     - 值2
> #下面举个例子：
> pets:
>     - cat
>     - dog
>     - pig
> ```
>
> 行内写法：
>
> ```yaml
> 数组名: [元素1,元素2]
> #举个例子
> pets: [cat,dog,pig]
> ```
>

#### 三、YMAL 配置文件值的获取

##### 1、application.yml 配置文件

```yaml
person:
  lastName: Jimmy
  age: 18
  boss: false
  birth: 1996/02/21
  maps: {key1: value1,key2: 666}
  lists:
    - Jimmy
    - kimi
  dog:
    name: 旺财
    age: 11
```

下面看下我是怎么把配置文件中的属性值映射到对象中的：

##### 2、和配置文件相对应的 JavaBean

```java
/**
 * 将配置文件中配置的每一个属性的值,映射到这个组件中
 * @ConfigurationProperties:这个注解用来告诉 springboot 将本类中的所有属性和配置文件中相关配置进行绑定
 *      prefix = "person"  用来指定我对配置文件中哪个下面的所有属性进行一一映射
 * 另：只有这个组件是容器中的组件,才能使用容器提供的 @ConfigurationProperties 功能
 *
 * 注：***************@ConfigurationProperties 默认获取的是全局配置文件的值********************
 */
@ConfigurationProperties(prefix = "person")
@Component
public class Person {
    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;

    private List<Object> lists;

    private Dog dog;
}
```

注：

你使用 @ConfigurationProperties 这个注解标识以后，IDE 可能会提示这么一句话：

`Spring Boot Configuration Annotaion Processor not found in classpath` 这句话你只要点进去，就会进入 SpringBoot 的官方网站，会看到一个依赖，把这个依赖复制下来放到 pom 文件中即可。

##### 3、添加需要的依赖

```xml
<!--导入配置文件处理器,配置文件进行绑定就会有提示-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

导入这个依赖后，你在 application.yml 中写的时候就会有提示

##### 4、SpringBoot 单元测试

```java
/**
 * SpringBoot 单元测试
 * 		@SpringBootTest：用来标明这是 SpringBoot 的单元测试
 * 		@RunWith(SpringRunner.class)：表示用 Spring 的驱动的跑,而不是原来的 junit
 * 	我们可以在测试期间很方便的类似编码一样进行自动注入等容器功能
 */
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootHelloworldQuickApplicationTests {

	@Autowired
	Person person;    //这里可以直接注入
	@Test
	public void contextLoads() {
		System.out.println("----------------" + person);
        //打印 Person{lastName='Jimmy', age=18, boss=false, birth=Wed Feb 21 00:00:00 CST 1996, maps={key1=value1, key2=666}, lists=[Jimmy, kimi], dog=Dog{name='旺财', age=10}}
	}
}
```

#### 四、properties 配置文件值的获取

获取方式和上面的一样。但是在这里主要说一下 properties 文件的编码问题：

```properties
#idea的properties文件默认是用 UTF-8 编码的
person.last-name=Jimmy
person.age=44
person.boss=false
person.birth=1996/02/21
person.maps.k1=v1
person.maps.k2=50
person.lists=Jimmy,kelly,kimi
person.dog.name=旺财
person.dog.age=8
```

在 idea 中 properties 文件默认使用 UTF-8 进行编码的，你在里面输入汉字，到时候获取到会乱码。解决方法就是你使用 IDEA 的时候设置  「File Encodings」的时候，勾选一下 「Transparent native-to-ascii conversion」这个选项，作用就是：一方面你在 properties 文件中可以看见你输入的汉字，另一方面它会把你输入的汉字转换成 ascii 码，这样获取的时候就不会乱码了。

#### 五、另外一种获取配置文件值的方式

```java
/**
 * 除了用 @ConfigurationProperties 注解从配置文件中获取 person 的所有属性值,还有另外一种方式：
 * 就是用 @Value() 注解,这是我们 Spring 的一个底层注解
 *
 */
@Component
public class Person {

    /**
     * 这个 @Value 注解就类似于这个功能：
     * 我们以前通过配置 bean 来给一个对象的属性赋值：
     * <bean>
     *     <property name="属性名" value=""></property>       这里的 value 就相当于 @Value 注解
     * </bean>
     * 因为我们在类的上面标注了 @Component 注解,就相当于把这个对象加入到容器中,所以现在的情况
     * 其实和上面配置 bean 的情况时一模一样的。上面配置 bean 用 value 属性来赋值,我们这里就用
     * @Value 注解来赋值。
     *
     * 配置 bean 的 value 属性值可以写 「字面量、${这个就是从环境变量,配置文件中取值}、#{spEL}」
     * 因此我们 @Value 注解也支持上面那三种写法
     */
    @Value("${person.last-name}")   //这里就是我们想要获取配置文件中 person.last-name 的属性值
    private String lastName;
    @Value("#{11*2}")   //这里我们就用了 spEL 表达式
    private Integer age;
    @Value("true")      //这里我就写一个字面量
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;

    private List<Object> lists;

    private Dog dog;
}
//通过 @Value 注解,我们是可以把配置文件的属性值读取到,然后注入到类的属性上的
```

但是这个时候我们就要想了，它和我们上面说的 @ConfigurationProperties 注解有没有区别呢？首先我们可以感觉到的就是，我们使用 @ConfigurationProperties 注解，只需要这一个注解它就可以批量把我们所有的属性都注入进来，而使用 @Value 注解则需要一个属性一个属性的写，手动注入值。下面就来说一下这两个注解的区别：

|                      |   @ConfigurationProperties   |      @Value      |
| :------------------: | :--------------------------: | :--------------: |
|        功能上        | 可以批量注入配置文件中的属性 | 必须一个个的指定 |
| 松散绑定（松散语法） |             支持             |      不支持      |
|         spEL         |            不支持            |       支持       |
|    JSR303数据校验    |             支持             |      不支持      |
|     复杂类型封装     |             支持             |      不支持      |

> 解释一下松散绑定（Relaxed binding）：
>
> 比如说我在配置文件中配置了一个属性和值，person.lastName=Jimmy
>
> 那么如果支持松散绑定的话，我使用下面几种方式都可以获取到配置文件的值：
>
> - person.lastName      这个就是标准的获取方式
> - person.last-name     遇到大写用 - 代替
> - person.last_name      遇到大写用 _ 代替
> - PERSON_LAST_NAME        这种就是推荐系统属性使用这种写法

```java
关于松散绑定案例：
//假设我配置文件配的是 person.last-name=Jimmy
@Component
public class Person {
    @Value("${person.lastName}")   //因为@Value注解不支持松散绑定,所以我通过${person.lastName}是获取不到的,是能通过${person.last-name}获取
    private String lastName;
}
---------------------------------------------------------------------------------------------------
@ConfigurationProperties(prefix = "person")
@Component
public class Person {  
    private String lastName;    //但是如果我用@ConfigurationProperties注解，这里 lastName 就可以获取到值
}
```

```java
关于 JSR303 数据校验案例：

/**
 * 也就是说我们从配置文件获取值进行注入的时候,还可以对配置文件的值进行校验一下,使用 @Validated 注解
 */
@ConfigurationProperties(prefix = "person")
@Component
@Validated
public class Person {  
    @Email      //比如说我在这里给一个校验规则叫 @Email 意思就是你这个 lastName 的值必须要是邮箱格式
    private String lastName;    //如果配置文件的lastName的值不是邮箱格式就会报错
}
-------------------------------------------------------------------------------------------------
@Component
@Validated
public class Person {
    @Value("${person.lastName}") 
    @Email      //在这里写你会发现是无效的,配置文件的 lastName 值不是邮箱格式照样可以通过,因为 @Value 注解不支持 JSR303 验证
    private String lastName;
}
```

```java
关于复杂类型封装：
/*
 * 假设配置文件中配置了，
 * 	person.maps.k1=v1
 *	person.maps.k2=50
*/
@ConfigurationProperties(prefix = "person")
@Component
public class Person {  
    private Map<String,Object> maps;    //在这里我是可以获取这个 map 值的
}
--------------------------------------------------------------------------------------------------
@Component
public class Person {
    @Value("${person.maps}") 
    private Map<String,Object> maps;    //这里就会报错,@Value注解只支持获取简单类型的值
}
```

所以总结一下什么时候用 @ConfigurationProperties 注解，什么时候用  @Value 注解：

> 如果说我们只是在某个业务逻辑中需要获取一下配置文件中的某项值，就使用 @Value；如果说我们专门写了一个 javaBean来和配置文件进行映射，那么我们就使用 @ConfigurationProperties

```java
@RestController
public class HelloController {

    @Value("${person.lastName}")
    private String name;    //比如我只是临时在某个业务逻辑需要用下配置文件某项值,那么我就可以使用@Value注解把值注入下

    @RequestMapping("/hello")
    public String hello() {
        return "hello" + name;
    }
}
```

#### 六、@PropertySource、@ImportResource 等注解

前面我们介绍了使用 @ConfigurationProperties 注解和 @Value 注解来获取配置文件的值，但是注意，==@ConfigurationProperties 注解默认获取的是全局配置文件的值==，我们要是把所有的配置都写在全局配置文件里面，显然是不可能的。所以假如我们自己去新建一个 properties 文件，该怎么去加载它呢？就是使用 @PropertySource 注解。

##### 1、使用 @PropertySource 加载指定配置文件

```properties
#我在类路径下新建了一个 person.properties 文件
person.lastName=Jimmy的大哥
person.age=44
person.boss=false
person.birth=1996/02/21
person.maps.k1=v1
person.maps.k2=50
person.lists=Jimmy,kelly,kimi
person.dog.name=旺财
person.dog.age=8
```

```java
/*
* 用 @PropertySource 注解来指定去加载哪个配置文件,它的 value 属性值是一个数组,可以同时指定多个配置文件
*/
@PropertySource(value = {"classpath:person.properties"})
@ConfigurationProperties(prefix = "person")
//******************************************************************************************
在这里我 @PropertySource 和 @ConfigurationProperties 两个注解都标了，因为我们知道 @ConfigurationProperties 默认是去加载全局配置文件的，但是在这里我使用 @PropertySource 指定加载了类路径下我自己新建的一个配置文件，但是要注意的是：你新建的配置文件和全局配置文件中都存在的属性用全局配置文件的，只有你新建的配置文件中的属性全局配置文件中没有，才会用你新建的配置文件中的属性值。
//******************************************************************************************
@Component
public class Person {
    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;

    private List<Object> lists;

    private Dog dog;
}
```

##### 2、@ImportResource 注解

这个注解的作用是：导入 Spring 的配置文件，让配置文件里面的内容生效。

①现在我在 service 包下，有个 HelloService 类

```java
public class HelloService {
}
```

②我在类路径下新建一个 spring 的配置文件 beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
	<!--在 xml 里面配置了一下 service 的 bean -->
    <bean id="helloService" class="com.iflytek.service.HelloService"></bean>
</beans>
```

③测试，现在我想知道 IOC 容器里面到底有没有这个 bean？

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootHelloworldQuickApplicationTests {
	/**
	 * 在这里我直接把 IOC 容器给注入进来
	 */
	@Autowired
	ApplicationContext ioc;

	@Test
	public void testService() {
		boolean b = ioc.containsBean("helloService");
		System.out.println("--------------" + b);  //发现打印的是 false,证明 IOC 容器中根本就没有这个bean
	}
}
```

所以证明 spring 的配置文件根本没有生效。<u>所以说如果你想要 Spring 的配置文件生效或者说加载进来，这时候就要使用 @ImportResource 注解，你可以把这个注解标注在一个配置类上</u>。

我现在把 @ImportResource 这个注解标注在主程序类上：

```java
/*
* @ImportResource 注解：作用就是导入 spring 的配置文件
* 它的 locations 属性值也是一个数组,可以加载多个 spring 的配置文件
*/
@ImportResource(locations = {"classpath:beans.xml"})
@SpringBootApplication
public class SpringbootHelloworldQuickApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootHelloworldQuickApplication.class, args);
    }
}
```

再执行上面第③步测试，发现打印 true ，证明 spring 的配置文件生效了。

但是在我们开发中，我们想给容器中加组件，不可能说先去写一个 spring 配置文件，然后在配置文件中把 bean 写好，然后在把这个 spring 的配置文件加载进来，这样做太麻烦了。

**下面说一下 SpringBoot 推荐给容器中添加组件的方式：**

①写一个配置类，这个配置类就类似于我们以前的 spring 配置文件

```java
/**
 * 这个类要成为一个配置类,就要加上 @Configuration 这个注解
 * @Configuration：指明当前类是一个配置类,这里就是来替代之前的 spring 配置文件
 */
@Configuration
public class MyAppConfig {

    /**
     * 之前我们在 spring 的配置文件中是使用 <bean></bean>来添加组件的
     * 在配置类中,我们就使用 @Bean 注解来添加组件
     *
     * @Bean 这个注解会将方法的返回值添加到容器中,容器中这个组件默认的 id 就是方法名
     */
    @Bean
    public HelloService helloService() {
		return new HelloService();
    }
}
```

再次执行测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootHelloworldQuickApplicationTests {
	/**
	 * 在这里我直接把 IOC 容器给注入进来
	 */
	@Autowired
	ApplicationContext ioc;

	@Test
	public void testService() {
		boolean b = ioc.containsBean("helloService");
		System.out.println("--------------" + b);  //发现打印的是true，但是要是把上面配置类中方法名改成 helloService1,这里就是 false了。因为默认方法名就是组件的 id
	}
}
```

#### 七、配置文件占位符

在配置文件中写值的时候，你可以使用 SpringBoot 给你提供的一些占位符来写该值。

①配置文件中可以写随机数

```java
${random.value}、${random.int}、${randow.long}、${random.int(10)}、${random.int[1024,65536]}
```

②占位符获取之前配置的值，如果没有可以使用「冒号」指定默认值

例：

```properties
#配置文件的值可以写一个随机数
person.lastName=Jimmy${random.uuid}
#年龄是一个随机的整数
person.age=${random.int}
person.boss=false
person.birth=1996/02/21
person.maps.k1=v1
person.maps.k2=50
person.lists=Jimmy,kelly,kimi
#我想取出person.lastName的值作为 dog 的前缀
person.dog.name=${person.lastName}_dog
#peson.dog.name=${person.hello:kelly}_dog        我前面的配置是没有person.hello这个属性的,那我就给它给默认值叫kelly（使用冒号，然后在后面写默认值）
person.dog.age=8
```

将上面配置文件的值映射到下面的类中：

```java
@PropertySource(value = {"classpath:person.properties"})
@ConfigurationProperties(prefix = "person")
@Component
public class Person {
    private String lastName;
    private Integer age;
    private Boolean boss;
    private Date birth;

    private Map<String,Object> maps;

    private List<Object> lists;

    private Dog dog;
}
```

测试类：

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class SpringbootHelloworldQuickApplicationTests {

	@Autowired
	Person person;
	@Test
	public void contextLoads() {
		System.out.println("----------------" + person);
        //打印 Person{lastName='Jimmy561edc4d-263a-401e-b822-a3934423f268', age=-1106585214, boss=false, birth=Wed Feb 21 00:00:00 CST 1996, maps={k1=v1, k2=50}, lists=[Jimmy, kelly, kimi], dog=Dog{name='Jimmy3f67302e-122c-416d-8cc5-b93fe63cfc93_dog', age=8}}
	}
}
```

#### 八、Profile 多环境支持

Profile 是 Spring 对不同环境提供不同配置功能的支持，可以通过激活、指定参数等方式快速切换环境。（简单的说就是 Spring 用来做多环境支持的）

##### 1、多 Profile 文件

我们在编写主配置文件的时候，文件名可以是 「application-{profile}.properties」或者 「application-{profile}.yml」

你可以写多个配置文件，默认使用 application.properties 的配置

```properties
#配置文件1（主配置文件）：application.properties
server.port=8088
spring.profiles.active=dev
#---------------------------------------------------------------------------------
#配置文件2：application-dev.properties
server.port=8082
#---------------------------------------------------------------------------------
#配置文件3：application-product.properties
server.port=8083
```

这个时候我启动 SpringBoot 的主程序类，看控制台日志，发现用的端口号是 8082。因为我在 application.properties 中

使用 「spring.profiles.active」指定激活 dev 环境的配置文件，所以端口号就是 8082。你要是不写 「spring.profiles.active」这个配置，那默认就使用 application.properties 文件里面的配置

##### 2、YML 支持多文档块方式

```yaml
server:
  port: 8081
spring:           #激活 dev 环境的配置
  profiles:     
    active: dev
---
server:
  port: 8083
spring:
  profiles: dev

---

server:
  port: 8084
spring:
  profiles: product     #指定属于哪个环境


#YML文件有一种语法,叫文档块。就是说你在 YML 配置文件中使用三个横线,就会把这个而配置文件隔成几个文档
```

##### 3、激活指定 profile

①像上面一样在配置文件中指定 spring.profile.active=XXX

②命令行参数方式：

打开你的 IDE 在里面找到 「Run/Debug Configurations」然后你会看到一个 「Program arguments」在这个里面你就可以写命令行参数来运行程序。（输入 「--spring.profiles.active=product」就会带上你这个参数来运行程序）

或者，你可以把 SpringBoot 程序打成一个 jar 包，然后在黑窗口命令行执行 j**ava -jar xxx.jar --spring.profiles.active=product** 这样也可以运行这个程序

PS：你在写代码测试的时候，就可以直接把参数写到 IDE 里面的 「Program arguments」中，这样更方便。

③虚拟机参数方式：

打开你的 IDE 在里面找到 「Run/Debug Configurations」然后你会看到一个 「VM options」在这个里面你就可以写虚拟机参数来运行程序。（输入 「-Dspring.profiles.active=product」就会带上你这个参数来运行程序）

#### 九、配置文件加载位置

SpringBoot 启动会扫描以下位置的 application.properties 或者 application.yml 文件作为 Spring Boot 的默认配置文件

> - file:./config/          意思是配置文件可以放在当前项目的 config 目录下
> - file:./                     意思是配置文件可以放在当前项目的根目录下
> - classpath:/config/       意思是配置文件可以放在类路径下的 config 目录下
> - classpath:/                  意思是配置文件可以放在类路径下
>
> 也就是说你把配置文件放在上面四个位置，SpringBoot 都可以加载到
>
> 注：
>
> ①上面是按照**优先级从高到低的**顺序去加载配置文件的，**高优先级配置的内容会覆盖低优先级配置的内容**
>
> ②**我们可以通过配置 spring.config.location 来改变默认配置**
>
> - 这句话的意思其实是，项目打包好以后，我们可以使用命令行参数的形式，在启动项目的时候来指定配置文件的新位置。而且指定配置文件和默认加载的配置文件共同起作用形成互补配置。
>
> ```properties
> #--------------项目中的配置文件 applicaiton.properites:------------------------------
> server.port=8082
> #配置项目的访问路径
> server.servlet.context-path=/boot
> spring.config.location=F:/application.properties   
> #在这里我想指定它去加载 f 盘下面的 application.properties 文件中的配置,但是启动项目后你发现，端口号还是 8082，因为在这里写 「spring.config.location」这个配置是没有作用的，需要你把项目打成 jar 然后在命令行执行的时候才会起作用，例如：
> java -jar xxxx.jar --spring.config.loaction=F:/application.properties
> #通过上面的命令来启动项目，你会发现项目的端口号变成了 8083，但是项目的访问路径还是 /boot 所以指定加载的配置文件和项目已有的配置文件之间也是互补配置(其实在这个在运维中运用的最多最方便，就是在不改变原有配置的情况下，我指定去加载一个外部的配置文件，这个指定的外部配置文件的优先级是最高的，和已有的配置文件之间是互补关系)
>
> #---------f 盘下面的 application.properties---------------------------------------
> server.port=8083
> ```
>
> ③虽然我们上面说可以把配置文件写在项目的根路径下，和项目的类路径下，但是你使用 maven 打包的时候，配置文件写在项目的根路径下，因为不符合 maven 的结构，所以打包的时候并不会把根路径下的配置文件打进去，只会把类路径下的配置文件打进去。（这里稍微注意下即可）

```properties
#配置文件1路径：   springboot-02/config/application.properties
server.port=8081
#----------------------------------------------------------------------------
#配置文件2路径：   springboot-02/application.properties
server.port=8082
#----------------------------------------------------------------------------
#配置文件3路径：   resources/config/application.properties     类路径下的 config目录下
server.port=8083
#----------------------------------------------------------------------------
#配置文件4路径：   resources/application.properties     类路径下
server.port=8084
```

上面的情况就是 SpringBoot 会先去加载 配置文件1，然后到 配置文件2，然后到 配置文件3，然后到 配置文件4，也就是说上面四个位置的配置文件 SpringBoot 都会去加载，而且他们是**互补配置**（你在低优先的配置文件里面配置的内容如果没有被高优先级覆盖，也是生效的）

#### 十、外部配置加载顺序

SpringBoot 除了可以加载 application.properties/application.yml 配置文件外，也可以从以下位置加载配置文件：

> 1. 命令行参数
>
>    - 我们可以用命令行来修改 SpringBoot 里面的所有配置，例如：「java -jar springboot-02-config-0.0.1-SNAPSHOT.jar --server.port=8083」这样我就可以在启动的时候指定它的端口号，而 jar 包里面的配置还是生效的。它们是互补配置。
>    - 如果你想写多个参数的话，中间用空格隔开。如：「java -jar springboot-02-config-0.0.1-SNAPSHOT.jar --server.port=8083 --server.context-path=/abc」
>
> 2. 来自 java:comp/env 的 NDI 属性
>
> 3. java 系统属性（System.getProperties()）
>
> 4. 操作系统环境变量
>
> 5. RandomValuePropertySource 配置的 random.* 属性值
>
>    **由 jar 包外向 jar 包内进行寻找，优先加载带 profile（跟 jar 包在同一级目录下，优先加载 jar 包外部的配置文件，在同一级目录下不需要用命令去指定配置文件路径，它自动会去找）**
>
> 6. jar 包外部的 application-{profile}.properties 或 application.yml（带 spring.profile）配置文件
>
> 7. jar 包内部的 application-{profile}.properties 或 application.yml（带 spring.profile）配置文件
>
>    **再来加载不带 profile**
>
> 8. jar 包外部的 application.properties 或 application.yml（不带 spring.profile）配置文件
>
> 9. jar 包内部的 application.properties 或 application.yml（不带 spring.profile）配置文件
>
> 10. @Configuration 注解类上的 @PropertySource
>
> 11. 通过 SpringApplication.setDefaultProperties 指定的默认属性

***上面都是按照优先级从高到底加载的，高优先级的配置覆盖低优先级的配置，所有的配置会形成互补配置***

#### 十一、自动配置原理

以前我们写 springBoot 配置文件的时候，比如说在写 application.properties 文件：

```properties
server.port=8081
```

这个 properties 文件里面为什么可以这么写？其实是有依据的。还有除了写这个属性还能写别的什么属性？

①一种方法就是我们可以查官方文档，看配置文件里面可以写哪些配置。https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#common-application-properties

②我们了解它自动配置的原理，然后就知道在配置文件里面该怎么写了。

**自动配置原理：**

> 1. 当我们 springBoot  应用启动的时候，我们是从 SpringBoot 的主配置类开始启动的。上面标注了 @SpringBootApplication 注解，这个注解点进去会有一个 @EnableAutoConfiguration 注解，开启了自动配置功能。
>
> 2. 具体说一下 @EnableAutoConfiguration 注解的作用：
>
>    - 这个注解你点进去之后会发现有一个 @Import({EnableAutoConfigurationImportSelector.class}) 注解，这个注解会利用 EnableAutoConfigurationImportSelector 给容器中导入一些组件，但是导入了哪些组件呢？我们把这个类点进去，发现它是继承一个父类的（AutoConfigurationImportSelector），我们点进父类，里面有一个「selectImports」方法 。这个方法里面有下面一行代码：
>
>      ```java
>      //获取候选的配置
>      List<String> configurations = this.getCandidateConfigurations(annotationMetadata, attributes);
>      //这个configurations最终会被返回
>      ```
>
>      进去这个方法你会发现：
>
>      ```java
>      protected List<String> getCandidateConfigurations(AnnotationMetadata metadata, AnnotationAttributes attributes) {
>              List<String> configurations = SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
>              Assert.notEmpty(configurations, "No auto configuration classes found in META-INF/spring.factories. If you are using a custom packaging, make sure that file is correct.");
>              return configurations;
>          }
>      ```
>
>      然后再点进去你会发现：
>
>      ```java
>      public static List<String> loadFactoryNames(Class<?> factoryClass, ClassLoader classLoader) {
>              String factoryClassName = factoryClass.getName();
>
>              try {
>                  //-----------------------重要代码---------------------------
>                  Enumeration<URL> urls = classLoader != null?classLoader.getResources("META-INF/spring.factories"):ClassLoader.getSystemResources("META-INF/spring.factories");
>                  //-----------------------重要代码--------------------------------
>                  
>       //上面的代码你会发现,它是使用类加载器去加载 「META-INF/spring.factories」 这个文件（扫描所有 jar 包类路径下的 「META-INF/spring.factories」这个文件）。那么加载这个文件的作用是什么呢？
>      //它先是加载 spring.factoties 文件,得到一个个url,然后遍历这些url,最终给它整成一个 properteis 文件（把文件的内容包装成 Properties 对象）
>                  ArrayList result = new ArrayList();
>
>                  while(urls.hasMoreElements()) {
>                      URL url = (URL)urls.nextElement();
>                      Properties properties = PropertiesLoaderUtils.loadProperties(new UrlResource(url));
>                      //这里的factoryClassName你要是往回找的话,它其实就是 EnableAutoConfiguration 
>                      String factoryClassNames = properties.getProperty(factoryClassName);
>      //那这里想表达的意思就是,从properties中取到 EnableAutoConfiguration.class 类(类名)对应的值,然后把它们添加到 result 中,这个 result 最终是要返回出去的,所以也就是添加到容器中
>                      result.addAll(Arrays.asList(StringUtils.commaDelimitedListToStringArray(factoryClassNames)));
>                  }
>
>                  return result;
>              } catch (IOException var8) {
>                  throw new IllegalArgumentException("Unable to load [" + factoryClass.getName() + "] factories from location [" + "META-INF/spring.factories" + "]", var8);
>              }
>          }
>      ```
>
>      比如说我们找一个 jar 包：「spring-boot-autoconfigure-1.5.12.RELEASE.jar」，**会把这个 jar 包类路径下，「META-INF/spring.factories」里面配置的所有 「EnableAutoConfiguration」的值加入到容器中**。
>
>      ```properties
>      # Auto Configure
>      org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
>      org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
>      org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
>      org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
>      org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
>      org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
>      org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
>      org.springframework.boot.autoconfigure.cloud.CloudAutoConfiguration,\
>      org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
>      org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
>      org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
>      org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.ldap.LdapDataAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
>      org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
>      org.springframework.boot.autoconfigure.elasticsearch.jest.JestAutoConfiguration,\
>      org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
>      org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
>      org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
>      org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
>      org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
>      org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
>      org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
>      org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
>      org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
>      org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
>      org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
>      org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
>      org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
>      org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
>      org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
>      org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
>      org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
>      org.springframework.boot.autoconfigure.mobile.DeviceResolverAutoConfiguration,\
>      org.springframework.boot.autoconfigure.mobile.DeviceDelegatingViewResolverAutoConfiguration,\
>      org.springframework.boot.autoconfigure.mobile.SitePreferenceAutoConfiguration,\
>      org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
>      org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
>      org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
>      org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
>      org.springframework.boot.autoconfigure.reactor.ReactorAutoConfiguration,\
>      org.springframework.boot.autoconfigure.security.SecurityAutoConfiguration,\
>      org.springframework.boot.autoconfigure.security.SecurityFilterAutoConfiguration,\
>      org.springframework.boot.autoconfigure.security.FallbackWebSecurityAutoConfiguration,\
>      org.springframework.boot.autoconfigure.security.oauth2.OAuth2AutoConfiguration,\
>      org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
>      org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
>      org.springframework.boot.autoconfigure.social.SocialWebAutoConfiguration,\
>      org.springframework.boot.autoconfigure.social.FacebookAutoConfiguration,\
>      org.springframework.boot.autoconfigure.social.LinkedInAutoConfiguration,\
>      org.springframework.boot.autoconfigure.social.TwitterAutoConfiguration,\
>      org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
>      org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
>      org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
>      org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
>      org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
>      org.springframework.boot.autoconfigure.web.DispatcherServletAutoConfiguration,\
>      org.springframework.boot.autoconfigure.web.EmbeddedServletContainerAutoConfiguration,\
>      org.springframework.boot.autoconfigure.web.ErrorMvcAutoConfiguration,\
>      org.springframework.boot.autoconfigure.web.HttpEncodingAutoConfiguration,\
>      org.springframework.boot.autoconfigure.web.HttpMessageConvertersAutoConfiguration,\
>      org.springframework.boot.autoconfigure.web.MultipartAutoConfiguration,\
>      org.springframework.boot.autoconfigure.web.ServerPropertiesAutoConfiguration,\
>      org.springframework.boot.autoconfigure.web.WebClientAutoConfiguration,\
>      org.springframework.boot.autoconfigure.web.WebMvcAutoConfiguration,\
>      org.springframework.boot.autoconfigure.websocket.WebSocketAutoConfiguration,\
>      org.springframework.boot.autoconfigure.websocket.WebSocketMessagingAutoConfiguration,\
>      org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration
>      ```
>
>      上面的每一个这样的 xxxAutoConfiguration 类都是容器中的一个组件，都加入到容器中。用它们来做自动配置。（只有上面的这些自动配置类被加到容器中，才会进行自动配置功能）
>
> 3. 每一个自动配置类进行自动配置功能
>
> 4. 下面以 **「HttpEncodingAutoConfiguration」** 为例解释下自动配置原理：
>
>    - ```java
>      @Configuration   //表示这是一个配置类
>      @EnableConfigurationProperties({HttpEncodingProperties.class}) //启动指定类的ConfigurationProperties功能,将配置文件中对应的值和 HttpEncodingProperties 绑定起来,并把 HttpEncodingProperties 加入到 IOC 容器中,这里指定类就是 「HttpEncodingProperties」类,你点进去这个类会发现，下面一个框的代码：
>      @ConditionalOnWebApplication  //在 Spring 的底层有一个 @Conditional 注解,根据不同的条件,如果满足指定的条件,整个配置类里面的配置就会生效。翻译一下 @ConditionalOnWebApplication 这个注解的意思就是,判断当前应用是否是 web 应用,如果是,当前配置类生效
>      @ConditionalOnClass({CharacterEncodingFilter.class})    //判断当前项目有没有 CharacterEncodingFilter 这个类（这个类其实就是 SpringMVC 中字符编码过滤器）
>      @ConditionalOnProperty(
>          prefix = "spring.http.encoding",
>          value = {"enabled"},
>          matchIfMissing = true
>      )  //判断配置文件中是否存在某个配置,这里就是判断配置文件中是否存在 「spring.http.encoding.enabled」这个属性, 「matchIfMissing = true」表示如果不存在这个配置判断也是成立的。就是说即使我们配置文件中没有配置 spring.http.encoding.enabled=true,也是默认生效的
>      public class HttpEncodingAutoConfiguration {
>          private final HttpEncodingProperties properties;
>           @Bean
>          @ConditionalOnMissingBean({CharacterEncodingFilter.class}) //判断容器中是不是没有括号里面的这个 bean ,满足没有括号里面这个bean 这个条件,我才走下面的方法,否则方法就不执行了
>          public CharacterEncodingFilter characterEncodingFilter() {
>              CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
>              filter.setEncoding(this.properties.getCharset().name());
>              filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
>              filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
>              return filter;
>          }
>
>          @Bean
>          public HttpEncodingAutoConfiguration.LocaleCharsetMappingsCustomizer localeCharsetMappingsCustomizer() {
>              return new HttpEncodingAutoConfiguration.LocaleCharsetMappingsCustomizer(this.properties);
>          }
>      }
>      ```
>
>      - ```java
>        @ConfigurationProperties(  //我们都知道这个注解是从配置文件中获取指定的值和 bean 的属性进行绑定
>            prefix = "spring.http.encoding"
>        )
>        public class HttpEncodingProperties {
>            public static final Charset DEFAULT_CHARSET = Charset.forName("UTF-8");
>            private Charset charset;
>            private Boolean force;
>            private Boolean forceRequest;
>            private Boolean forceResponse;
>            private Map<Locale, Charset> mapping;
>        }
>        ```
>
>    - 一句话总结一下上面 **HttpEncodingAutoConfiguration** 这个自动配置类就是：根据当前不同的条件（@ConditionalXXX 这个注解判断结果），决定这个配置类是否生效。**一旦这个配置类生效，这个配置类就会给容器中添加各种组件（bean），而这些组件的属性是从 对应的 properties 类中获取的，而properties 里面的每一个属性又是和配置文件相绑定的。**
>
> 5. 所以所有在配置文件中能配置的属性都是在 xxx.properties 中封装好的，配置文件能配置什么就可以参照某个功能对应的这个属性类

##### 1、SpringBoot 精髓：

①SpringBoot 启动时会加载大量的自动配置类

②我们看我们需要的功能有没有 SpringBoot 默认写好的自动配置类

③如果有，我们再来看这个自动配置类中到底配置了哪些组件（这些组件中有没有我们需要用的，如果有就不需要我们额外配置了，如果没有那就需要你自己写个配置类额外配置一下）

④给容器中自动配置类添加组件的时候，会从 properties 中获取某些属性，我们可以在配置文件中指定这些属性的值。

##### 2、相关说明

SpringBoot 中：

会有： XXXAutoConfiguration：这个就是自动配置类，它会给容器中添加组件。

也会有：对应的XXXProperties：封装配置文件中相关的属性

#### 十二、@Conditional 自动配置报告

在上面我们主要介绍了 SpringBoot 的自动配置，在自动配置的时候有个地方要注意，就是你要使自动配置生效，那么你标 @Conditional XXX 注解，判断成功了以后，你注解下面的方法或者类才能生效。

##### 1、@Conditional  派生注解

**作用：** 必须是 @Conditional  指定的条件成立，才给容器中添加组件，配置类里面的内容才生效。

|      @Conditional 扩展注解      |         作用（判断是否满足当前指定条件）         |
| :-----------------------------: | :----------------------------------------------: |
|       @ConditionalOnJava        |            系统的java版本是否符合要求            |
|       @ConditionalOnBean        |                容器中存在指定Bean                |
|    @ConditionalOnMissingBean    |               容器中不存在指定Bean               |
|    @ConditionalOnExpression     |                满足SpEL表达式指定                |
|       @ConditionalOnClass       |                 系统中有指定的类                 |
|   @ConditionalOnMissingClass    |                系统中没有指定的类                |
|  @ConditionalOnSingleCandidate  | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
|     @ConditionalOnProperty      |          系统中指定的属性是否有指定的值          |
|     @ConditionalOnResource      |           类路径下是否存在指定资源文件           |
|  @ConditionalOnWebApplication   |                  当前是web环境                   |
| @ConditionalOnNotWebApplication |                 当前不是web环境                  |
|       @ConditionalOnJndi        |                  JNDI存在指定项                  |

**自动配置类必须满足一定条件才能生效** ，举个例子：「AopAutoConfiguration」这个类，

```java
@Configuration
@ConditionalOnClass({EnableAspectJAutoProxy.class, Aspect.class, Advice.class})
@ConditionalOnProperty(
    prefix = "spring.aop",
    name = {"auto"},
    havingValue = "true",
    matchIfMissing = true
)
public class AopAutoConfiguration {
}
```

要是向让这个类可以用，你必须要满足 「@ConditionalOnClass」 和 「@ConditionalOnProperty」 这两个条件，这个类才会生效。就拿 @ConditionalOnClass 来说，你项目里面必须要有 EnableAspectJAutoProxy、Aspect、Advice这三个类，这个 aop 自动配置类才会生效，你项目里面要是没有这三个类，这个 aop 自动配置类就不会生效。

##### 2、自动配置报告

所以我们就想知道，SpringBoot 自动配置类中有哪些类是生效了的。

方法：

在 application.properties 中配置 「debug=true」

```properties
#开启 springBoot 的 debug 模式
debug=true
```

这样在你启动项目的时候就会在控制台就会打印自动配置报告，这样我们就可以很方便的知道哪些类是生效了的，哪些类是没有生效的。

```
=========================
AUTO-CONFIGURATION REPORT
=========================


Positive matches:        哪些类匹配上的（已经启用的类）
-----------------

   DispatcherServletAutoConfiguration matched:
      - @ConditionalOnClass found required class 'org.springframework.web.servlet.DispatcherServlet'; @ConditionalOnMissingClass did not find unwanted class (OnClassCondition)
      - @ConditionalOnWebApplication (required) found StandardServletEnvironment (OnWebApplicationCondition)

Negative matches:       哪些类没有匹配上的（没有启用的类）
-----------------

   ActiveMQAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'javax.jms.ConnectionFactory', 'org.apache.activemq.ActiveMQConnectionFactory' (OnClassCondition)

   AopAutoConfiguration:
      Did not match:
         - @ConditionalOnClass did not find required classes 'org.aspectj.lang.annotation.Aspect', 'org.aspectj.lang.reflect.Advice' (OnClassCondition)
```

------

### SpringBoot 与日志

#### 一、日志框架

假设现在有个程序员小张，小张现在在开发一个大型系统。那么为了方便我们排查问题，我们经常会在控制台把相关信息打印出来，使用 「System.out.println」。但是等到有一天，老板看你的项目，发现使用 「System.out.println」有点多，而且没什么用，所以让你把它给去掉，那么你只有一个一个把它去掉。等过一段时间，老板觉得在控制台打印日志还是有必要的，那么你就要重新把它给加上。或者说让你把日志写到一个文件中，方便监控，那你就又需要去修改写日志的代码。来来回回很麻烦。所以小张就想，我不如写一个日志框架来记录系统的一些运行信息，那么这个日志框架就写成了，假设叫 zhang-logging.jar。在以后的项目中小张用的都是这个日志框架，但是用着用着，小张发现这个框架可能比较简陋，比如说输出日志的时候我想实现自动归档（例如每天的日志我都给你压缩起来放在一个文件里面，我以填来进行分类），或者说异步模式等等一些高级功能。原来的框架就不适合了。那么这时候小张又写了一个日志框架，假设叫 zhang-logging-good.jar。那么这就会有一个问题，就是之前的项目我们用的都是 zhang-logging.jar 这个框架，那么现在我要是用新的框架，就只有把原来的框架卸下来，然后再换新的框架。用新的框架之前的 API 可能就会要改，那假如我以后再要搞个框架，那到时候就又要改代码。所以小张又想：

他为所有的日志框架写了一个统一的接口层：日志门面（日志抽象层）    -------logging-abstract.jar

以后我们在进行编码的时候，都面向 logging-abstract.jar  里面的接口进行编码。然后你想用哪个日志，你导入具体的日志实现就可以了。

| 日志门面（日志的抽象层）                                     | 日志实现                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| JCL（Jakarta Commons Logging）<br />**SLF4j（Simple Logging Facade for Java）**<br />jboss-logging | Log4j<br />JUL（java.util.logging）<br />Log4j2<br />**Logback** |

我们说 SpringBoot 的底层是 Spring，而 Spring 框架默认是用的 JCL，SpringBoot 在此基础上进行了封装用的是 **SLF4J 和 logback**

**小说明：**

> 1、上面的日志门面，JCL 其实就是 Apache 的 commons loggings 包，但是为什么不叫 ACL 而叫 JCL ，是因为这个日志抽象层是由 Jakata 小组开发的。
>
> 2、上面我们列举了三个日志门面，所以我们现在就来权衡下用哪个比较好。首先，我们说 「jboss-logging」这个用的场景是在是太少了，生来就不太适合给我们用作日志门面；其次我们说 JCL ，这个 JCL 最后一次更新是 2014 年更新，可以说比较老了，用了也不太合适。所以说日志门面我们就用 SLF4J。
>
> 3、关于日志实现，我们也列举了好几个，也来比较下用哪个比较好。Log4j 和 Logback 其实都是同一个人写的，当时把 Log4j 写出来后，发现 Log4j 有性能问题，就准备对 Log4j 进行升级，但是发现升级的代价太大了，所以那个人就重新写了一个日志框架叫 Logback，写完之后发现万一以后还有别的日志框架，那么替换起来就会很麻烦，所以这个人又写了一个日志门面，后面的框架都是基于这个门面去实现。所以说你要是选择 SLF4j 做日志门面，那么理所应当使用 log4j 或者 logback 作为实现。log4j 没有 logback 先进，所以我们选用 logback

#### 二、SLF4J 使用原理

##### 1、如何在系统中使用 SLF4J

在我们以后开发的过程中，日志记录方法的调用，不应该是直接调用日志的实现，而是调用日志抽象层里面的方法（那么它实际上调用的就是日志实现类的方法）。

关于如何使用 SLF4J，在 SLF4J 官网中有一个 HelloWorld 示例：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    //这里用的是 slf4j 的 LoggerFactory
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
//用的时候需要导入 slf4j 的jar 和 logback 的实现jar
```

##### 2、SLF4J 原理图

![slf4j原理图](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/slf4j%E5%8E%9F%E7%90%86%E5%9B%BE.png)

下面来具体分析一下上面这张原理图：

①左一：

意思是在我们的应用中，我只导入了 SLF4J 的jar，也就是 「slf4j-api jar」它是一个日志的抽象层，那我们要进行日志的记录，它就会把你的日志输出到 /dev/null。

②左二：

意思就是我的应用程序面向 SLF4J 编程，调用它里面的方法，对日志进行记录。但是在我们的应用程序中我同时也导入了 logback 相关的 jar 包，也就是 「logback-classic.jar」和 「logback-core.jar」。所以在程序中我们虽然调用的是 SLF4J 抽象层方法，但是 logback 对它进行了实现。调用的其实还是 logback 里面的方法。

③左三：

SLF4J 想要绑定 log4j的话应该怎么做？首先我们说 log4j、logbak 都是  slf4j 的实现，而且这三个都是同一个人或者同一个团队写的。其次是 log4j 出现的比较早，在 log4j 出现的时候还没有 slf4j ，当时根本就没有想到有 slf4j 这一回事，只是在后面不断扩展中，才想到了开发一个 slf4j 作为统一抽象层。所以按照原理，我们的应用程序还是应该调用 slf4j 统一抽象层，然后在 slf4j 和 log4j 之间做一个适配。所以最后调用的还是 log4j 的方法。

④⑤⑥这几个和前面都一样就不在赘述。

**注：**

`每一个日志的实现框架都有自己的配置文件，使用 SLF4J 以后，配置文件还是做成日志实现框架的配置文件`

#### 三、其它日志框架统一转换为  slf4j

假设我现在要开发一个系统，用了 slf4j + logback 框架，但是这个系统他可能用了 Spring 、Hibernate 、Mybatis等等框架，这些框架底层也用了不同的日志框架。像 Spring 底层是用的 commons-logging 来做日志记录的，Hibernate 底层是用 jboss-logging 做日志记录的。那么这就会带来一个问题，这个系统里面用了很多的日志框架来记录日志。所以我们现在想做的就是：

统一日志记录，别的框架的日志也让它和我一起使用 slf4j 进行输出。

下面一张图就解释了统一使用 slf4j 应该怎么办？

![其他日志框架统一转换为slf4j](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/%E5%85%B6%E4%BB%96%E6%97%A5%E5%BF%97%E6%A1%86%E6%9E%B6%E7%BB%9F%E4%B8%80%E8%BD%AC%E6%8D%A2%E4%B8%BAslf4j.png)

①先看图片左上角：

左上角的图片先竖着看，我们应用程序面向 slf4j 编程，调用日志抽象层接口，其实真正实现的是 logback。但是事情往往不是那么美好，我们应用程序往往会用到别的框架，像 Spring 、Hibernate 它们底层用的也是不同的日志框架。从图上来看就是，我们应用程序中还有用到 Commons-logging、log4j、java.util.logging，现在我想让这些日志框架都使用 slf4j 。要怎么做呢？比如说你 Spring 框架用到了 Commons-logging 日志包，那么我就把 Spring 中的日志包排除掉，然后用 jcl-over-slf4j.jar 替换。先说排除掉，因为你 Spring 是依赖 commons-logging 包的，你把它排除了，Spring 启动肯定会报错，但是这时候你用 jcl-over-slf4j.jar 替换掉就可以了。也就是说 commongs-logging 包里面用到的类，在 jcl-over-slf4j.jar 包里面都有，这样替换掉才不会报错。然后 jcl-over-slf4j.jar 这个包调用 slf4j ，slf4j 再调用真正的实现 logback。

②再看图片右上角：

假设我现在想用 slf4j + log4j 的组合。图片右上角先竖着看，我应用程序面向 slf4j 编程，因为 log4j 出现的比较早，log4j 出现的时候 slf4j 还没出现，所以 slf4j 和 log4j 之间需要有一个适配，真正的实现还是 log4j。但是往往我们应用程序中继承别的框架在用别的日志。从图上看就是用到了 commons-logging、java.util.logging。那么我要是想让这些日志框架都使用 slf4j 。拿 commons-logging举例，那我就先把 commons-logging 给排除掉，然后再替换成 jcl-over-slf4j.jar。这个 jar 会去调用 slf4j ，然后 slf4j 会去调用适配 log4j 的 jar，最终调用 log4j。

所以总结一下：

**如何让系统中所有的日志都统一到 slf4j:**

1. 将系统中其他日志框架先排除出去
2. 用中间包来替换原有的日志框架
3. 导入 slf4j 其他的实现


#### 四、SpringBoot 日志关系

springBoot 使用 「spring-boot-starter-logging」来做日志功能：

```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-logging</artifactId>
      <version>2.0.2.RELEASE</version>
      <scope>compile</scope>
 </dependency>
每一个 starter 都是一个场景启动器,这个 starter 就是相当于来封装我们日志场景的启动器
```

springboot 日志底层的依赖关系：

![springboot日志](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/springboot%E6%97%A5%E5%BF%97.png)

**总结：**

1）、Springboot 底层也是使用 slf4j + logback 的方式进行日志记录

2）、Springboot 也把其他的日志都转换成了 slf4j 

3）、*Springboot 能自动适配所有的日志，而且底层使用的是 slf4j + logback 的方式记录日志，所以你引入其他框架的时候，只需要把这个框架的日志框架给排除掉即可，springboot 就能自动适配。*

#### 五、SpringBoot日志 默认配置

我们启动项目的时候，发现控制台会打印日志，其实 springboot 已经默认帮我们配置了日志

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class LoggingApplicationTests {

	/**
	 * 日志记录器
	 */
	private Logger logger = LoggerFactory.getLogger(getClass());

	@Test
	public void contextLoads() {

		//日志级别
		logger.trace("这是 trace 日志");
		logger.debug("这是 debug 级别日志");
		//springboot 默认给我输出的是 info 级别的日志
		logger.info("这是 info 级别日志");
		logger.warn("这是 warn 级别日志");
		logger.error("这是 error 级别日志");
		//如果想要修改日志级别的话,可以在 application.properties 中通过 logging.level 修改
	}

}
```

可以在 application.properties 中修改 springboot 日志的默认配置：

```properties
#指定哪个包下的日志级别(logging.level后面跟给具体哪个包下面设置日志级别)
logging.level.com.iflytek=debug
#这个属性值如果你不指定路径,只写一个文件名的话就在当前项目下生成 spring.log 日志
logging.file=F:/spring.log
#在当前磁盘的根路径下创建 spring 文件夹和里面的 log 文件夹,使用 spring.log 作为默认日志输出文件
logging.path=/spring/log
#在控制台输出的日志格式
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
#在文件中输出的日志格式
logging.pattern.file=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
```

```
<!--
        日志输出格式：
			%d表示日期时间，
			%thread表示线程名，
			%-5level：级别从左显示5个字符宽度
			%logger{50} 表示logger名字最长50个字符，否则按照句点分割。 
			%msg：日志消息，
			%n是换行符
 -->
```

#### 六、指定日志文件和日志 profile 功能

在你启动项目的时候，其实 SpringBoot 已经帮我们默认配置了日志，就像上面描述的。但是如果你不想使用 SpringBoot 默认的日志配置，你只需要在类路径下放上每个日志框架自己的配置文件即可，这样 Springboot 就不会使用它自己的默认配置了。比如我要使用 Logback ，那我就把 logback.xml 放到类路径下，这样 springboot 就会自动去加载这个配置文件。

| Logging System          | Customization                                                |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml`, or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |

logback.xml ：这个文件可以直接被日志框架识别

logback-spring.xml：这个文件不可以被日志框架直接识别，所以日志框架不直接加载里面的日志配置项，而是由 SpringBoot 去加载。如果由 springBoot 去加载的话就可以用 springboot 一个比较高级的功能，就是 profile 功能，来指定在哪个环境下生效。

```xml
<springProfile name="dev">
    <!--这个就表示在开发环境下,我使用下面的格式来打印日志信息 -->
	<layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
     </layout>
</springProfile>
<springProfile name="!dev">
    <!--这个就表示在非开发环境下,我使用下面的格式来打印日志信息 -->
	<layout class="ch.qos.logback.classic.PatternLayout">
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS}==== [%thread] %-5level===== %logger{50} - %msg%n</pattern>
     </layout>
</springProfile>

上面配置完成以后你可以在 application.properties 中指定需要激活哪个环境下的日志配置
application.properties:
#激活开发环境
spring.profiles.active=dev
```

#### 七、切换日志框架

现在 Springboot 默认给我们用的是 Slf4J + Logback 的方式，这时候如果我们想要用 Slf4j + log4J 的方式，应该如何切换呢？当然这种切换时没有意义的，因为 log4J 本来就是因为作者觉得写得太不好了，所以才重新写了一个 logback 去跟 Slf4J 进行适配，但是现在我们就以 log4J 为例说一下该如何切换：

![切换日志框架为 springBoot](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/%E5%88%87%E6%8D%A2%E6%97%A5%E5%BF%97%E6%A1%86%E6%9E%B6%E4%B8%BAlog4j.png)

首先我们看 slf4j 官网这张经典的图，在前面我们也说过。你想要使用 log4j 作为日志实现，首先应用是面对 slf4j 编程的，你需要先把项目中别的日志框架给排除掉，比如说你项目中使用了 commons-logging 和 java.util.logging 两个日志框架，那么你就需要先把这两个 jar 包排除掉，然后使用 jcl-over-slf4j.jar 和 jul-to-slf4j.jar 去替换刚才的 jar 包。同时 slf4j 和 log4j 之间需要一个适配包，所以你也需要引入一下这个适配包。下面说一下具体细节：

![切换日志框架为slf4j2](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/%E5%88%87%E6%8D%A2%E6%97%A5%E5%BF%97%E6%A1%86%E6%9E%B6%E4%B8%BAlog4j-2.png)

首先 spring-boot-starter 这个 jar 包会依赖 spring-boot-starter-logging 这个 jar，而这个 jar 包又会依赖上图中的三个 jar 包，现在我想用 log4j 作为日志框架的实现，那么 上图 logback-classic 这个 jar 我肯定要排除（它是 logback 的 jar 包），其次 log4j-to-slf4j 这个 jar 包是把 log4j 转成 slf4j 的，我们现在就想使用 log4j ，所以这个转换的 jar 也不能要，需要排除掉，因为项目中使用了 java.util.logging 这个日志框架，所以我们需要把这个 jar 包转成 slf4j ，所以 jul-to-slf4j 这个 jar 留着，体现在 pom 文件中就是：

![切换日志框架为log4j-3](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/%E5%88%87%E6%8D%A2%E6%97%A5%E5%BF%97%E6%A1%86%E6%9E%B6%E4%B8%BAlog4j-3.png)

其次是，刚才我们也说了，slf4j 和 log4j 之间需要一个适配，所以需要在 pom 文件中引入一个适配的依赖

```xml
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-log4j12</artifactId>
</dependency>
```

而 slf4j-log4j12 这个 jar 也依赖 log4j 这个 jar 包，所以在导入 slf4j-log4j12 这个 jar 的时候，log4j 的 jar 包也被一起导入进来了，对照上面的第一张图：1、slf4j-api 这个 jar 我们已经有了，我们是面向 slf4j 编程的。2、项目中用到了 java.util.logging 这个 jar ，我们已经使用 jul-to-slf4j 替换了这个 jar。3、我们也引入了 slf4j-log4j12 这个适配包，引入这个适配包的同时把 log4j 的 jar 包也引入了，然后你再在类路径下引入 log4j.properties 配置文件，启动项目就算切换成功了。

假如这个时候我想使用 log4j2 应该怎么做呢？

![springboot日志选择](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/springboot%E6%97%A5%E5%BF%97%E9%80%89%E6%8B%A9.png)

springboot 提供了启动器，默认使用的是 spring-boot-starter-logging，这个启动器底层是用 slf4j + logback 实现了，我们也可以使用 spring-boot-starter-log4j2 这个启动器，这两个启动器二选一即可，所以我们就需要在 pom 文件中把 spring-boot-starter-logging 这个 jar 排除掉，然后在 引入 spring-boot-starter-log4j2 的 jar 包即可，最后在类路径下引入 log4j2.xml 文件即可

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <!-- 排除 spring-boot-starter-logging 的 jar 包-->
            <artifactId>spring-boot-starter-logging</artifactId>
            <groupId>org.springframework.boot</groupId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <!-- 引入 spring-boot-starter-log4j2 的 jar 包 -->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```



### SpringBoot 与 Web 开发

#### 一、springBoot web 开发简介

##### 1、使用 SpringBoot 进行 web 开发

> 1）、创建 SpringBoot 应用，在创建应用的同时选中我们需要的模块，比如你需要使用 web 你就勾选 web 模块，你需要 mybatis 你就勾选 mybatis 模块。
>
> 2）、SpringBoot 已经默认将这些场景配置好了，只需要在配置文件中指定少量的配置就可以运行起来
>
> 3）、编写自己需要的业务代码

如果想要明明白白的用 SpringBoot 进行开发，你需要把自动配置原理弄清楚。就是说你每引入一个场景，你都需要知道 SpringBoot 帮我们配置了什么，这些配置我们能不能修改 ？能修改的话我们可以修改哪些配置？了解清楚以后才能进行熟练开发。

```
xxxAutoConfiguration   这个类是帮我们给容器中自动配置组件
xxxProperties    这是一个配置类，来封装我们配置文件中的内容
```



#### 二、webjars 和静态资源映射规则

##### 1、在 SpringBoot 中能否写页面？

现在我新建了一个 SpringBoot 项目，我想进行 web 开发，然后我有一些 js 和 css 文件需要引用，如果是之前的 web 应用，我们直接把这些 js 和 css 文件放到 src/main/webapp 下面即可。但是现在我们 SpringBoot 项目，看 pom.xml 文件，打的包时一个 jar 包，那现在这种方式我们可以写页面吗？答案肯定是可以的。只是这个页面放在哪，SpringBoot 有规定。

##### 2、SpringBoot 对静态资源的映射规则

在 SpringBoot 中，你使用的 SpringMVC 的相关配置，都在 「WebMvcAutoConfiguration」这个类里面，下面看下这个类里面的部分代码：

```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
            if(!this.resourceProperties.isAddMappings()) {
                logger.debug("Default resource handling disabled");
            } else {
                Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
                CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
                if(!registry.hasMappingForPattern("/webjars/**")) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

                String staticPathPattern = this.mvcProperties.getStaticPathPattern();
                if(!registry.hasMappingForPattern(staticPathPattern)) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

            }
        }
```

1）、所有 「/webjars/**」下面的请求，都去 「classpath:/META-INF/resources/webjars/」这个下面找资源。

> 介绍一下 webjars：
>
> 就是以 jar 包的方式引入静态资源
>
> 可以参考下 https://www.webjars.org/  这个网站，里面把我们常用的前端框架如，jquery bootstrap 等以 maven 依赖的形式展现给我们，我们就可以把依赖拿到加到 pom 文件中

```xml
<!--引入 jquery-webjar -->
		<dependency>
			<groupId>org.webjars</groupId>
			<artifactId>jquery</artifactId>
			<version>3.3.1-1</version>
		</dependency>
```

![webjars](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/webjars.png)

假如我现在有一个请求是： localhost:8080/webjars/abc，那么按照我们上面说的，「/webjars/**」webjars 下面的任意请求，这个资源去哪找呢？是去 「classpath:/META-INF/resources/webjars/」下面找 abc 这个资源，看上面的图是没有的，所以 404。那假设现在我的请求是：localhost:8080/webjars/jquery/3.3.1-1/jquery.js，那么它就会去 classpath:/META-INF/resources/webjars/query/3.3.1-1/jquery.js 是可以找到这个资源的，所以我在浏览器就可以访问到这个资源。

2）、「/**」 访问当前项目的任何资源（静态资源文件夹）

```
下面的这几个都是静态资源文件夹,如果请求没有被处理都会到这几个文件夹下面来找资源
"classpath:/META-INF/resources/"
"classpath:/resources/"
"classpath:/static/"
"classpath:/public/"
"/"   当前项目的根路径
```

我访问 localhost:8080/abc 意思就是去静态文件夹里面找 abc

3)、欢迎页：静态资源文件夹下的所有 index.html 页面，被 「/**」映射

我访问 localhost:8080/     意思就是去找 index.html页面

4）、所有的 **/favicon.ico 都是在静态资源文件下找

这个意思就是我访问 localhost:8080/favicon.ico 那么它就会去静态资源文件夹下找 favicon.ico 这个文件。

#### 三、模板引擎

##### 1、模板引擎介绍

现在我们想要开发一个包括前端在内的 CRUD 功能，而这时候假设前端提供的是一些模板是 html 页面，按照我们以前的开发习惯，是用 jsp 进行开发，用 jsp 的好处就是，当我们查询出一些数据转发到 jsp 页面以后，我们可以使用 <c:foreach> >进行遍历，<c:if>  进行判断，等使用这些比较好用的 JSTL 标签。但是我们现在使用的是 SpringBoot 项目，使用的是内置的 tomcat 不支持使用 JSP 页面，所以如果你要使用纯 html 静态页面的方式，会给开发带来很大的麻烦。所以 Springboot 就推荐你使用模板引擎。下面来说一下模板引擎：

例如：JSP   Velocity   Freemarker   Thymeleaf    这些都是模板殷勤

模板引擎虽然很多种，但是他们的思想都是一样的，看下面一张图：

![模板引擎](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/template-engine.png)

上面这张图意思就是，我先来写一个页面模板，但是有些值是动态的，我们用表达式写在上面（对应上图绿色部分），但是这个表达式的值从哪来呢？就是我们查询到一些数据（对应上图黄色部分），然后把这些数据和页面模板都交给模板引擎，模板引擎把数据填充到指定的位置，最后生成一个我们想要的内容。不管是 JSP 还是其他模板引擎都是这个思想，只不过不同模板引擎之间语法可能不一样。

##### 2、Thymeleaf 模板引擎

下面主要来介绍一下 SpringBoot 推荐的模板引擎 thymeleaf。

**①引入 thymeleaf**

```xml
<!-- 有对应的 thymeleaf 场景启动器 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<!-- 通过这个可以来切换 thymeleaf 的版本 -->
<properties>
		<!--thymeleaf 的版本号-->
		<thymeleaf.version>3.0.9.RELEASE</thymeleaf.version>
		<!--
			这个 thymeleaf-layout 是一个布局功能的支持程序
			如果你使用的是 thymeleaf3 主程序，那么就需要使用 layout2 以上版本
			如果你使用的是 thymeleaf2 主程序，那么就需要使用 layout1 以上版本
		-->
		<thymeleaf-layout-dialect.version>2.2.2</thymeleaf-layout-dialect.version>
</properties>
```

##### ② thymeleaf 的语法和使用

```java
@ConfigurationProperties(
    prefix = "spring.thymeleaf"
)
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING;
    //默认前缀
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    //默认后缀
    public static final String DEFAULT_SUFFIX = ".html";
}
//根据上面 thymeleaf 的配置文件配置,也就是说我们只要把 html 页面放在 classpath:/templates/ 下面,thymeleaf 就能自动渲染了
```

举个例子：

现在我在类路径下，新建了一个文件夹叫 templates ，在这个文件夹下新建了一个 success.html 页面，下面我在 controller里面编写代码

```java
@Controller
public class HelloController {
    
    /**
     * 如果是以前,springmvc 会帮我们拼串，拼前后缀，然后到 jsp 页面
     * 现在我们引入了模板引擎 thymeleaf ,你发一个 success 请求后
     * thymeleaf 会帮你解析到  classpath:/templates/success.html 页面
     * @return
     */
    @RequestMapping("/success")
    public String success() {
        //结果拼一个前缀：classpath:/templates/
        //拼一个后缀：.html
        //最终就是去 classpath:/templates/success.html
        return "success";
    }
}


//当你访问 localhost:8080/success 时，它就会去找 classpath:/templates/success.html 页面
```

总结：

所以总结一下就是，只要你把 html 页面放在 classpath:/templates/，thymeleaf 就能自动帮我们渲染了。

**③ thymeleaf** **的使用**

使用之前需要在 html 页面的 html 标签上导入名称空间，这样你写的时候就会有语法提示，不导入也不影响功能。

```xml
<html lang="en" xmlns:th="http://www.thymeleaf.org">
</html>
```

举个简单的例子：

我在 controller 里面往请求与放了数据，然后想在 html 页面中拿出来

```java
@Controller
public class HelloController {
    @RequestMapping("/success")
    public String success(Map<String,String> map) {
        //往请求域里面放入一些数据,去success.html
        map.put("hello","你好");
        return "success";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!--
        如果这是 jsp 页面,我从controller转发过来的请求,直接用 ${hello} 就可以拿到请求域里面的内容
        但是现在是 html 页面,我们要按照 thymeleaf 的语法来
        首先你要用 thymeleaf 的话,你就要在 html 标签上导一个 thymeleaf 的命名空间
        导入这个命名空间的作用就是可以有语法提示,不导的话在功能上也完全没有影响
    -->
    <h1>这是成功页面</h1>
    <!-- th:text就是将 div 里面的文本内容设置为我们指定的值 -->
    <div th:text="${hello}"></div>
</body>
</html>s
```

**④thymeleaf 的语法规则**

1. 「th:text 」   改变当前元素里面的文本内容。除了使用 「th:text 」外，你还可以使用 「th:任意 html 属性」来替换原生属性的值。

2. ![thymeleaf语法](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/thymeleaf%E8%AF%AD%E6%B3%95.png)

   ​

3. 表达式语法

```properties
Simple expressions(表达式语法):
        Variable Expressions: ${...} 可以用来获取变量值
        	1).获取对象的属性,调用方法
        		如：${person.father.name}、${person.createCompleteName()}
        	2).使用内置的基本对象
        		#ctx : the context object.
                #vars: the context variables.
                #locale : the context locale.
                #request : (only in Web Contexts) the HttpServletRequest object.
                #response : (only in Web Contexts) the HttpServletResponse object.
                #session : (only in Web Contexts) the HttpSession object.
                #servletContext : (only in Web Contexts) the ServletContext object.
                如：获取请求域中的 foo值：${param.foo}、获取 session 中 foo 的值：${session.foo}、获取当前应用中 foo 的值 ：${application.foo}
             3).使用内置的一些工具对象
        		#execInfo : information about the template being processed.
                #messages : methods for obtaining externalized messages inside variables expressions, in the same way as they would be obtained using #{…} syntax.
                #uris : methods for escaping parts of URLs/URIs
                #conversions : methods for executing the configured conversion service (if any).
                #dates : methods for java.util.Date objects: formatting, component extraction, etc.
                #calendars : analogous to #dates , but for java.util.Calendar objects.
                #numbers : methods for formatting numeric objects.
                #strings : methods for String objects: contains, startsWith, prepending/appending, etc.
                #objects : methods for objects in general.
                #bools : methods for boolean evaluation.
                #arrays : methods for arrays.
                #lists : methods for lists.
                #sets : methods for sets.
                #maps : methods for maps.
                #aggregates : methods for creating aggregates on arrays or collections.
                #ids : methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).
                如：${#strings.isEmpty(name)}、${#arrays.toArray(object)}
        
        
        Selection Variable Expressions: *{...}  选择表达式,在功能上和 ${} 是一样的,不过它有一个补充功能就是可以配合 「th:object="${session.user}"」一起使用
        如：见下框案例
        
        Message Expressions: #{...}    用来获取国际化内容的
        Link URL Expressions: @{...}   用来定义 URL
        如：
        我想去 http://localhost:8080/gtvg/order/details?orderId=3'这个 url，那么就可以这么写：
<a href="details.html" th:href="@{http://localhost:8080/gtvg/order/details(orderId=${o.id})}">view</a>

        Fragment Expressions: ~{...}   片段引用表达式
Literals(字面量)
        Text literals: 'one text' , 'Another one!' ,…
        Number literals: 0 , 34 , 3.0 , 12.3 ,…
        Boolean literals: true , false
        Null literal: null
        Literal tokens: one , sometext , main ,…
Text operations(文本操作):
        String concatenation: +
        Literal substitutions: |The name is ${name}|
Arithmetic operations(数学运算):
        Binary operators: + , - , * , / , %
        Minus sign (unary operator): -
Boolean operations(布尔运算):
        Binary operators: and , or
        Boolean negation (unary operator): ! , not
Comparisons and equality(比较运算):
        Comparators: > , < , >= , <= ( gt , lt , ge , le )
        Equality operators: == , != ( eq , ne )
Conditional operators(条件运算):
        If-then: (if) ? (then)
        If-then-else: (if) ? (then) : (else)
        Default: (value) ?: (defaultvalue)
Special tokens:
No-Operation:
```

案例：

```html
Selection Variable Expressions
<!-- 如果你使用 ${} 去获取一个对象里面的属性,你可能需要这么写 -->
<div>
    <p>Name: <span th:text="${session.user.firstName}">Sebastian</span>.</p>
    <p>Surname: <span th:text="${session.user.lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="${session.user.nationality}">Saturn</span>.</p>
</div>
<!-- 但是你要使用 *{} 就会方便一点 -->
<div th:object="${session.user}">     <!-- 这里我使用 th:object 相当于已经获取到 user 对象了-->
    <p>Name: <span th:text="*{firstName}">Sebastian</span>.</p>
    <!-- 我在这里就可以直接使用 *{} 去拿这个对象里面的属性,就不用每次都 ${session.user.XXX} -->
    <p>Surname: <span th:text="*{lastName}">Pepper</span>.</p>
    <p>Nationality: <span th:text="*{nationality}">Saturn</span>.</p>
</div>
```

循环遍历取数据小案例：

```java
@Controller
public class HelloController {
    @RequestMapping("/success")
    public String success(Map<String,String> map) {
        //往请求域里面放入一些数据,去success.html
        map.put("hello","你好");
        map.put("users", Arrays.asList("Jimmy","kelly","kimi"));
        return "success";
    }
}
```

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <!-- th:each 写在哪个标签上,每次遍历都会生成这个标签 -->
    <!--th:each 每次遍历都会生成当前这个标签-->
    <h4 th:text="${user}" th:each="user:${users}"></h4> <!--也就是说这里遍历完之后,会生成3个h4标签,因为 users 里面有三个值-->
    <h4>
        <!--
            span 标签的文本,一种方法就是我使用 th:text="${user}"
            另一种方法我就直接在 span 标签的中间,使用两个中括号[[]] 然后在里面用${}获取值
            两个中括号[[]]的意思其实就表示,th:text 只不过它可以写在标签中间
			中括号小括号[()]的意思其实就表示,th:utext 它也可以写在标签中间
        -->
        <span th:text="${user}" th:each="user:${users}">[[${user}]]</span><!-- 标签中间是不能直接写 ${} 的,要使用符合 thymeleaf 语法的方式获取值，使用 [[]] 或 [()] -->
        <!--这里就相当于我一个 h4 里面会有三个 span 标签-->
    </h4>
</body>
</html>
```

#### 四、SpringMVC 自动配置

##### 1、以下从 Spring 官网中复制相关内容：

Spring Boot 已经把Spring MVC 配置好了，以下是 SpringBoot 对 SpringMVC 的默认配置：

The auto-configuration adds the following features on top of Spring’s defaults:

- Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.

  - 意思就是自动配置了 ViewResolver（视图解析器：根据方法的返回值得到视图对象（View）由视图对象决定如何渲染，转发还是重定向）
  - ContentNegotiatingViewResolver 用来组合所有视图解析器的

  ```java
  //ContentNegotiatingViewResolver.java :
    protected void initServletContext(ServletContext servletContext) {

        //看源码你会发现，它在初始化 servletContext 的时候，利用 BeanFactory 工具类，把所有的 ViewResolver(视图解析器都获取到了)

        Collection<ViewResolver> matchingBeans = BeanFactoryUtils.beansOfTypeIncludingAncestors(this.obtainApplicationContext(), ViewResolver.class).values();

        //所以如果你要自定义视图解析器，只要实现了 ViewResolver 接口，在这里它也会把你自定义的视图解析器获取到

    }

  ```


==如何定制自己的视图解析器==
  ```

  	@Bean
  	public ViewResolver myViewResolver() {
   		return new MyViewResolver();
  	}

  	private static class MyViewResolver implements ViewResolver {

  		@Nullable
  		@Override
  		public View resolveViewName(String s, Locale locale) throws Exception {
  			return null;
  		}
  	}

  //我们可以自己给容器中添加一个视图解析器，ContentNegotiatingViewResolver 就会自动把你的视图解析器组合进来 
  ```

  ​

- Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-spring-mvc-static-content))).

  静态资源文件夹路径

- Automatic registration of `Converter`, `GenericConverter`, and `Formatter` beans.

  > - Converter（转换器）
  >
  > 比如说现在有个方法，public String hello(User user){}，假入现在页面传过来的数据和 User 对象的属性一样，那么 SpringMVC 就会自动封装，但是页面传的一般是字符串，所以这里就存在类型转换问题，比如说传过来一个 String 类型 true 那么就可以使用 Converter 把它转成布尔类型。
  >
  > - `Formatter` 格式化器
  >
  > 如果说我页面带过来一个日期 2018/6/13 那么我就可以使用格式化器把它格式化成我需要的格式

  - ==如何定制自己的格式化器==
    - 自己写一个 Formatter，然后把它加入到容器中即可。

    ```java
    //WebMvcAutoConfiguration.java:
    ```


```java
public void addFormatters(FormatterRegistry registry) {
    		// getBeansOfType 根据类型获取所有的 bean
            Iterator var2 = this.getBeansOfType(Converter.class).iterator();
			//所有类型为 Converter 的 bean 获取到了之后,一个一个遍历把它加到 registry 中
            while(var2.hasNext()) {
                Converter<?, ?> converter = (Converter)var2.next();
                registry.addConverter(converter);
            }
    
    //所以，如果你想自定义 Converter 或者 Formater ，只要实现接口然后把它加到容器中即可，然后 springboot 就会自动从容器中把所有类型是 Converter,Formatter 的 bean 获取到

            var2 = this.getBeansOfType(GenericConverter.class).iterator();

            while(var2.hasNext()) {
                GenericConverter converter = (GenericConverter)var2.next();
                registry.addConverter(converter);
            }

            var2 = this.getBeansOfType(Formatter.class).iterator();

            while(var2.hasNext()) {
                Formatter<?> formatter = (Formatter)var2.next();
                registry.addFormatter(formatter);
            }

        }

private <T> Collection<T> getBeansOfType(Class<T> type) {
    		// 从 beanFactory 中也就是容器中，获取所有类型为 Converter,Formatter,GenericConverter 的 bean
            return this.beanFactory.getBeansOfType(type).values();
        }
```

- Support for `HttpMessageConverters` (covered [later in this document](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-spring-mvc-message-converters)).

  > - SpringMVC 用来转换 Http 请求和响应的，比如说我方法返回了一个 user 对象，我想把这个 user 对象以 json 的格式返回出去，那么就会用到 `HttpMessageConverters` 这个对象
  > - HttpMessageConverters 是从容器中确定，获取所有的 HttpMessageConverter

  - ==如何定制自己的 HttpMessageConverter==
    - 自己写一个 HttpMessageConverter 然后把它加到容器中即可

- Automatic registration of `MessageCodesResolver` (covered [later in this document](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-spring-message-codes)).

  - 这个是用来定义错误代码生成规则的

- Static `index.html` support.  静态首页访问

- Custom `Favicon` support (covered [later in this document](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-spring-mvc-favicon)).

- Automatic use of a `ConfigurableWebBindingInitializer` bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#boot-features-spring-mvc-web-binding-initializer)).

  - 这个的作用就是用来初始化 web 数据邦定器的，这个 web 数据邦定器的作用就是将请求数据绑定到 JavaBean 中
  - ==如何定制自己的 ConfigurableWebBindingInitializer==
    - 自己可以写一个 `ConfigurableWebBindingInitializer` 然后把它加到容器中

```java
//Inclusion of ContentNegotiatingViewResolver and BeanNameViewResolver beans.
/*1.先来分析上面第一个小点的内容,它表达的意思是 SpringBoot 默认帮我们配置好了 ContentNegotiatingViewResolver 和 BeanNameViewResolver 这两个视图解析器,而视图解析器在 SpringMVC 中的作用就是根据方法的返回值得到视图对象(也就是 View 对象)视图对象来决定是转发还是重定向到页面

ContentNegotiatingViewResolver：这个解析器的作用就是用来组合所有的视图解析器的,比如说你可以自己写一个视图解析器,实现 ViewResolver 接口，你把这个视图解析器加到容器中,然后它就会把你写的视图解析器自动的组合进来

*/
@SpringBootApplication
public class Springboot04WebRestfulcrudApplication {

	public static void main(String[] args) {
		SpringApplication.run(Springboot04WebRestfulcrudApplication.class, args);
		
	}

	@Bean
	public ViewResolver myViewResolver() {
		return new MyViewResolver();  //new 一个视图解析器,并把这个视图解析器加入到容器中
	}
	//在这里我自己定义了一个视图解析器
	private static class MyViewResolver implements ViewResolver {

		@Nullable
		@Override
		public View resolveViewName(String s, Locale locale) throws Exception {
			return null;
		}
	}
}
//现在当我访问任意一个请求，它会走到 DispatcherServlet 里面的 doDispatcher 方法,你看它 request 对象里面 ViewResolver 里面就有我自己定义的视图解析器

```

##### 2、扩展 SpringMVC 

前面我们说了 SpringBoot 对 SpringMVC 自动配置的一些功能，但是在实际开发中仅仅靠 SpringBoot 的这点自动配置是不够用的，我们以前写 SpringMVC 的时候，会有一个 springmvc.xml 配置文件，在这个配置文件里面我们可以写跟 springMVC 相关的配置，但是要是没了这个配置文件，还能写吗？答案是可以的，Springboot 提供了扩展。

```xml
<!-- 下面是我们之前在 spingmvc.xml 中写的相关配置 -->
<mvc:view-controller path="/hello" view-name="success"></mvc:view-controller>
    <mvc:interceptors>
        <mvc:interceptor>
            <mvc:mapping path="/hello"/>
            <bean></bean>
        </mvc:interceptor>
  </mvc:interceptors>
```

==想要扩展 springMVC ，方法就是：==

**①编写一个配置类（@Configuration注解修饰），②这个配置类是 WebMvcConfigurerAdapter 类型③不能标注@EnableWebMvc**

```java
/**
 * 使用 WebMvcConfigurerAdapter 可以扩展 SpringMVC 的功能
 */
@Configuration
public class MyMVCConfig extends WebMvcConfigurerAdapter {
    //这个 WebMvcConfigurerAdapter 里面有很多空的抽象方法，你要完成哪一个功能，只需要重写对应的抽象方法即可

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
//        super.addViewControllers(registry);
        //浏览器发送 /hello 请求，就会来到 success 页面
        registry.addViewController("/hello").setViewName("success");
    }
}
```

这种方式既保留了所有的自动配置，也能用我们扩展的配置。

下面来看一下原理：

1）WebMvcAutoConfiguration 是 SpringMVC 的自动配置类

2）在做其他自动配置时会导入 `@Import({WebMvcAutoConfiguration.EnableWebMvcConfiguration.class})`

```java
    //这个类还是在 WebMvcAutoConfiguration 类里面
	@Configuration
    @Import({WebMvcAutoConfiguration.EnableWebMvcConfiguration.class})
    @EnableConfigurationProperties({WebMvcProperties.class, ResourceProperties.class})
    @Order(0)
    public static class WebMvcAutoConfigurationAdapter implements WebMvcConfigurer, ResourceLoaderAware {
    }
```

> 这个 EnableWebMvcConfiguration 你点进去发现它继承了 DelegatingWebMvcConfiguration
>
> - ```java
>   @Configuration
>   public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration {
>    }
>   ```
>
>   - ```java
>     @Configuration
>     public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
>         private final WebMvcConfigurerComposite configurers = new WebMvcConfigurerComposite();
>
>         public DelegatingWebMvcConfiguration() {
>         }
>     	
>         //这里自动装配一旦标注在方法上，方法的参数就要从容器中获取
>         //那么这里的意思其实就是，从容器中获取所有的 WebMvcConfigurer 集合
>         @Autowired(
>             required = false
>         )
>         public void setConfigurers(List<WebMvcConfigurer> configurers) {
>             if(!CollectionUtils.isEmpty(configurers)) {
>                 //这里就是把从容器中拿到的 configurers 放到上面定义的变量 configurers里面
>                 this.configurers.addWebMvcConfigurers(configurers);
>                 //那么都放到上面定义的变量 configurers 里面有什么意义呢？我们继续看源码
>             }
>         }
>     ```
>
>   - ```java
>     //还是这个类
>     @Configuration
>     public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {   //你会发现这个类里面的很多方法都是调用 this.configurers 里面的方法
>         protected void addCorsMappings(CorsRegistry registry) {
>             this.configurers.addCorsMappings(registry);
>         }
>
>         protected void addViewControllers(ViewControllerRegistry registry) {
>             this.configurers.addViewControllers(registry);
>         }
>
>         protected void configureViewResolvers(ViewResolverRegistry registry) {
>             this.configurers.configureViewResolvers(registry);
>         }
>     }
>     ```
>
>     - ```java
>       //下面以 this.configurers.addViewControllers(registry); 为例
>       class WebMvcConfigurerComposite implements WebMvcConfigurer {
>           public void addViewControllers(ViewControllerRegistry registry) {
>               Iterator var2 = this.delegates.iterator();
>
>               while(var2.hasNext()) {
>                   WebMvcConfigurer delegate = (WebMvcConfigurer)var2.next();
>                   delegate.addViewControllers(registry);
>               }
>               // 它其实是吧容器中所有的 WebMvcConfigurer 都遍历一遍，然后给每个 WebMvcConfigurer 都调用一遍 addViewControllers 方法
>           }
>           //所以说你容器中所有的 WebMvcConfigurer 都会一起起作用，因此我们自己写的继承了WebMvcConfigurerAdapter 的配置类也会起作用
>       }
>       ```
>
>       ​

3）容器中所有的 WebMvcConfigurer 都会起作用

4）我们自己的配置类也会起作用。达到的效果就是：SpringMVC的自动配置和我们的扩展配置都会起作用

##### 3、全面接管 SpringMVC 

所谓全面接管 SpringMVC 的意思就是，SpringBoot 对 SpringMVC 的自动配置不需要了，所有的配置都是由我们自己来配。

假如你不需要 SpringBoot 对 SpringMVC 的自动配置，你都想用自己的配置，你只需要在**配置类中添加 @EnableWebMvc即可**

```java
@Configuration
@EnableWebMvc   //加了这个注解以后 springboot 对 springmvc 的自动配置就全都不生效了，全靠你自己在配置文件中配置了
public class MyMVCConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
//        super.addViewControllers(registry);
        //浏览器发送 /hello 请求，就会来到 success 页面
        registry.addViewController("/hello").setViewName("success");
    }
}
```

下面说一下为什么加一个 @EnableWebMvc 注解， SpringBoot 对 SpringMVC 的自动配置就失效了：

```java
//@EnableWebMvc 这个注解你点进去会发现它导入了 DelegatingWebMvcConfiguration 这个类
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE})
@Documented
@Import({DelegatingWebMvcConfiguration.class})
public @interface EnableWebMvc {
}
```

- ```java
  // DelegatingWebMvcConfiguration 这个类点进来发现是这样的
  @Configuration
  public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
  }
  ```

- 然后我们再来看一下 WebMvcAutoConfiguration 这个类（SpringMVC 自动配置的类）

- ```java
  @Configuration
  @ConditionalOnWebApplication(
      type = Type.SERVLET
  )
  @ConditionalOnClass({Servlet.class, DispatcherServlet.class, WebMvcConfigurer.class})
  //这里意思是当 WebMvcConfigurationSupport 这个类不存在时，我下面的所有自动配置才会生效
  @ConditionalOnMissingBean({WebMvcConfigurationSupport.class})
  @AutoConfigureOrder(-2147483638)
  @AutoConfigureAfter({DispatcherServletAutoConfiguration.class, ValidationAutoConfiguration.class})
  public class WebMvcAutoConfiguration {
  }

  // 而上面用 @EnableWebMvc 标注的配置类，点进去发现 DelegatingWebMvcConfiguration 这个类正好是 WebMvcConfigurationSupport 类型，所以 Springboot 对 SpringMVC 的自动配置就全都不生效了
  ```

**如何修改 SpringBoot 的默认配置**

> 1、SpringBoot 在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean  @Component）如果有就用用户配置的，如果没有，才自动配置。如果有些组件可以有多个（比如 ViewResolver）那么就将用户配置的和自己默认配置的组合起来
>
> 2、在 SpringBoot 中会有非常多的 xxxConfiguer 帮助我们进行扩展配置

#### 五、错误处理机制

##### 1、SpringBoot 默认的错误处理机制

1）、当你用浏览器去访问一个不存在的 url 时，SpringBoot 会默认给我们返回一个错误页面

![springboot 默认错误页面](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/springboot%E9%BB%98%E8%AE%A4%E9%94%99%E8%AF%AF%E9%A1%B5%E9%9D%A2.png)

2)、如果不是通过浏览器发送请求，而是通过其他客户端或者一些第三方接口工具去访问一个不存在的 url ，Springboot 会默认给我们相应一个 json 数据

```json
{
    "timestamp": "2018-11-12T15:30:35.342+0000",
    "status": 404,
    "error": "Not Found",
    "message": "No message available",
    "path": "/abc"
}
```

**下面来说一下原理：**

首先我们来看一下 `ErrorMvcAutoConfiguration` 这个类，这个类是错误处理的自动配置类。在这个类里面我们发现它给容器添加了以下组件：

① `DefaultErrorAttributes`

② `BasicErrorController`

③ `ErrorPageCustomizer`

④ `DefaultErrorViewResolver`

我们说一旦系统出现 4xx 和 5xx 之类的错误，`ErrorPageCustomizer` 就会生效（定制错误的响应规则），就会来到 /error 请求，既然来到了这个请求，那么肯定需要有人去处理这个请求，所以这时候   `BasicErrorController` 就起作用了，这个 controller 默认处理 /error 请求，具体怎么处理的看下面分析（其实这个 controller 里面有两个重要的方法，一个是返回 html 类型数据，一个是返回 json 类型数据）

```java
//ErrorMvcAutoConfiguration 这个类：   

private static class ErrorPageCustomizer implements ErrorPageRegistrar, Ordered {
        private final ServerProperties properties;
        private final DispatcherServletPath dispatcherServletPath;

        protected ErrorPageCustomizer(ServerProperties properties, DispatcherServletPath dispatcherServletPath) {
            this.properties = properties;
            this.dispatcherServletPath = dispatcherServletPath;
        }
    //这里它会去注册一个错误页面
        public void registerErrorPages(ErrorPageRegistry errorPageRegistry) {
            ErrorPage errorPage = new ErrorPage(this.dispatcherServletPath.getRelativePath(this.properties.getError().getPath()));
            errorPageRegistry.addErrorPages(new ErrorPage[]{errorPage});
        }

        public int getOrder() {
            return 0;
        }
    }

//-----------------------------------------------------------------------------
//这里重点来看一下，上面的 this.properties.getError().getPath() ，你点进去之后你会发现：
public class ErrorProperties {
    @Value("${error.path:/error}")  //这里的意思就是从配置文件中获取 error.path 的值，如果没有就用 /error 作为值
    private String path = "/error";
    
    public String getPath() {  //点进去实际上是到这个方法里面来了，这里的 path 就是上面的 path
        return this.path;
    }
    
}
```

```java
//BasicErrorController   这个类

//我们可以看到这个类它实际上就是一个 controller
@Controller
//这个@RequestMapping就表示它处理的请求，这个请求的值是从配置文件中获取的，先从 server.error.path 获取，如果没有获取到值就从 error.path 获取值，如果都没获取到就默认处理 /error 请求
@RequestMapping({"${server.error.path:${error.path:/error}}"})
public class BasicErrorController extends AbstractErrorController {
    private final ErrorProperties errorProperties;
    
    @RequestMapping(produces = {"text/html"})    //产生 html 类型的数据，浏览器发送的请求来到这个方法处理
    public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
        HttpStatus status = this.getStatus(request);
        Map<String, Object> model = Collections.unmodifiableMap(this.getErrorAttributes(request, this.isIncludeStackTrace(request, MediaType.TEXT_HTML)));
        response.setStatus(status.value());
        //去哪个页面作为错误页面，包含页面地址和页面内容
        ModelAndView modelAndView = this.resolveErrorView(request, response, status, model);
        return modelAndView != null?modelAndView:new ModelAndView("error", model);
    }

    @RequestMapping
    @ResponseBody      //产生 json 类型的数据，其他客户端发送的请求来到这个方法处理
    public ResponseEntity<Map<String, Object>> error(HttpServletRequest request) {
        Map<String, Object> body = this.getErrorAttributes(request, this.isIncludeStackTrace(request, MediaType.ALL));
        HttpStatus status = this.getStatus(request);
        return new ResponseEntity(body, status);
    }
}
```

**说一下上面的 controller 中，两个处理请求的方法，发送的都是 /error 请求，怎么判断用哪个方法来处理请求？**

![springboot-error请求](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/springboot-error%E8%AF%B7%E6%B1%82.png)

浏览器在发送请求去请求服务端的时候，会在请求头中加一个 Accept，告诉服务端它要接收 text/html 类型的数据，所以进到 controller 方法里面，自然会去找那个产生 html 类型的方法。

![springboot-error请求2](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-springboot/springboot-error%E8%AF%B7%E6%B1%822.png)

而你用别的客户端去请求的是，请求头中的 Accept 告诉服务端客户端换接收的是任意，所以进到 controller 中就会进入到产生 json 数据的那个方法

```java
//AbstractErrorController  

// BasicErrorController 中，产生 html 类型数据方法，点进来之后就会变成这样：
public abstract class AbstractErrorController implements ErrorController {
    //主要是这个方法
    protected ModelAndView resolveErrorView(HttpServletRequest request, HttpServletResponse response, HttpStatus status, Map<String, Object> model) {
        private final List<ErrorViewResolver> errorViewResolvers;
        //这里就是遍历所有 errorViewResolvers，得到 modelAndView 就返回，没有就返回 null
        Iterator var5 = this.errorViewResolvers.iterator();

        ModelAndView modelAndView;
        do {
            if(!var5.hasNext()) {
                return null;
            }

            ErrorViewResolver resolver = (ErrorViewResolver)var5.next();
            modelAndView = resolver.resolveErrorView(request, status, model);
        } while(modelAndView == null);

        return modelAndView;
    }
}
```

上面的那个方法就是解析错误页面，具体的响应页面，去哪个页面是由 `DefaultErrorViewResolver`决定的。

```java
//DefaultErrorViewResolver类，下面我们来看下这个类中是怎么得到 ModelAndView 的

public class DefaultErrorViewResolver implements ErrorViewResolver, Ordered {
        public ModelAndView resolveErrorView(HttpServletRequest request, HttpStatus status, Map<String, Object> model) {
        ModelAndView modelAndView = this.resolve(String.valueOf(status), model);
        if(modelAndView == null && SERIES_VIEWS.containsKey(status.series())) {
            modelAndView = this.resolve((String)SERIES_VIEWS.get(status.series()), model);
        }
        return modelAndView;
    }

    private ModelAndView resolve(String viewName, Map<String, Object> model) {
        // 默认 springboot 可以去找到一个页面？  比如：error/404
        String errorViewName = "error/" + viewName;    //这个 viewName 就是传过来的 http 状态码
        //(存在模板引擎的话)模板引擎可以解析这个页面地址就用模板引擎解析
        TemplateAvailabilityProvider provider = this.templateAvailabilityProviders.getProvider(errorViewName, this.applicationContext);
        //模板引擎可用的情况下，返回到 errorViewName 可用的视图地址
        return provider != null?new ModelAndView(errorViewName, model):this.resolveResource(errorViewName, model);
        //模板引擎不可用的情况下，就在静态资源文件夹下 errorViewName 对应的页面(比如：error/404.html)
    }
    private ModelAndView resolveResource(String viewName, Map<String, Object> model) {
        String[] var3 = this.resourceProperties.getStaticLocations();   //静态资源文件夹
        int var4 = var3.length;

        for(int var5 = 0; var5 < var4; ++var5) {
            String location = var3[var5];
            try {
                Resource resource = this.applicationContext.getResource(location);
                resource = resource.createRelative(viewName + ".html");
                if(resource.exists()) {
                    return new ModelAndView(new DefaultErrorViewResolver.HtmlResourceView(resource), model);
                }
            } catch (Exception var8) {
                ;
            }
        }
        return null;
    }
    
}
```

下面我们再来看一下默认的错误处理 Controller ，BasicErrorController，当我们要响应页面的时候会返回 ModelAndView ，而视图名解析就是我们上面分析的逻辑，下面我们主要来看下 ModelAndView 中的 Model 数据是怎么来的

```java
@Controller
@RequestMapping({"${server.error.path:${error.path:/error}}"})
public class BasicErrorController extends AbstractErrorController {
    
		@RequestMapping(produces = {"text/html"})
        public ModelAndView errorHtml(HttpServletRequest request, HttpServletResponse response) {
            HttpStatus status = this.getStatus(request);
            // 从这里我们可以看到这个 model 它是怎么来的，其实它是调用了getErrorAttributes 方法
            Map<String, Object> model = Collections.unmodifiableMap(this.getErrorAttributes(request, this.isIncludeStackTrace(request, MediaType.TEXT_HTML)));
            response.setStatus(status.value());
            ModelAndView modelAndView = this.resolveErrorView(request, response, status, model);
            return modelAndView != null?modelAndView:new ModelAndView("error", model);
        }
    }

//--------------------------------------------------------------
//上面的 getErrorAttributes 方法点进去之后，发现进入到是这个类：
//AbstractErrorController.java
public abstract class AbstractErrorController implements ErrorController {
    protected Map<String, Object> getErrorAttributes(HttpServletRequest request, boolean includeStackTrace) {
        WebRequest webRequest = new ServletWebRequest(request);
        return this.errorAttributes.getErrorAttributes(webRequest, includeStackTrace);
    }
}
//--------------------------------------------------------------
//getErrorAttributes 方法再点进去，发现它是一个接口
public interface ErrorAttributes {
    Map<String, Object> getErrorAttributes(WebRequest var1, boolean var2);

    Throwable getError(WebRequest var1);
}

//而这个接口的实现类就是 ------DefaultErrorAttributes--------


```



##### 2、如何去定制错误响应

1️⃣ 如何定制错误的页面

① 有模板引擎的情况下，将错误页面命名为 「错误状态码.html」放在模板引擎文件夹下的 error 文件夹下，发生此状态码的错误就会来到对应的页面

② 我们可以使用 4XX 和 5XX 作为错误页面的文件名来匹配这种类型的所有错误，但是会优先匹配 精确的状态码.html

③ 来到错误页面后，我们在错误页面能获取到的信息有：

> timestamp : 时间戳
>
> status : 状态码
>
> error ：错误提示
>
> exception ：异常对象
>
> message ：异常消息
>
> errors ：JSR303 数据校验的错误都在这里 

④ 没有模板引擎（模板引擎找不到这个页面），静态资源文件夹下找

⑤ 以上都没有错误页面，就是默认来到 SpringBoot 默认的错误提示页面

#### 六、配置嵌入式 Servlet 容器

Springboot 默认使用嵌入式的 Servlet 容器





------

### SpringBoot 与 Docker

#### 一、Docker 简介

1、Docker 是一个 开源的应用容器引擎。基于 Go 语言并遵从 Apache2.0 协议开源。Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上。容器是完全使用沙箱机制，相互之间不会有任何接口，更重要的是容器性能开销极低。

2、Docker 支持将软件编译成一个镜像，然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像。运行中的这个镜像称为容器，容器启动是非常快速的。类似 windows 里面的 ghost 操作系统，安装好以后什么东西都有了。

举个例子：

> 假如我们现在要装 windows 操作系统，张三从微软的官网下载了一个 windows 的原生操作系统，这个原生的操作系统没有常用的驱动也没有常用的软件，那张三就把常用的驱动和常用的软件安装好，这样系统就可以使用了。可是等下次李四再装系统的时候，也是装的原生的操作系统，那么李四又要安装一遍这些常用的驱动的软件，就很麻烦。所以张三就可以把他安装好的系统以及常用的驱动、常用的软件一起做成一个镜像文件，这样下次再有人装系统的时候，直接拿这个镜像文件去安装就可以了，安装完以后，系统装好了，常用软件还有常用驱动也装好了。
>
> 而 Docker 也和上面的原理类似，我们在开发和运维的过程中，经常需要给服务器安装各种环境，比说我我需要在服务器上安装 mysql 安装 redis ，那么在一台服务器上我把 mysql 安装好配置好，把 redis 安装好配置好，环境算是搭好了，但是如果我在另一台服务器上，又需要安装 mysql 和 redis 那只有再按照再配置一遍了。这样就太麻烦了，我们就可以使用 Docker技术，Docker 支持你安装配置好的软件可以打包成一个镜像文件，比如说我现在在一台服务器上安装配置好了 mysql 那么，那么我就可以把安装配置好的 mysql 打包成一个镜像文件，下次别的服务器要用的话，只要这台服务器安装了 Docker ，那么我就可以直接运行之前打包好的镜像文件就可以了，只要这个镜像一运行起来，我们就称这个镜像为容器，比如之前打包好的 mysql 镜像，那么这个镜像运行起来就是 mysql 容器，我们就说 mysql 运行在 mysql 容器里面。所以说如果我们想在多台机器上都安装同一个软件的话，那么我在一条服务器上安装配置好就行了，然后把这个软件做成镜像文件，在别的服务器上运行这个镜像文件即可。

#### 二、 Docker 核心概念

> docker 主机（Host）：安装了 Docker 程序的机器（Docker 直接安装在操作系统之上）
>
> docker 客户端（client）：连接 docker 主机进行操作
>
> docker 仓库（Registry）：用来保存各种打包好的软件镜像
>
> docker 镜像（Images）：软件打包好的镜像，放在 docker 仓库中。可以说 Docker 镜像是用于创建 Docker 容器的模板。
>
> docker 容器（Container）：镜像启动后的实例称为一个容器。容器是独立运行的一个或一组应用

使用 Docker 的步骤：

1、安装 Docker 

2、去 Docker 仓库找到这个软件对应的镜像

3、使用 Docker 运行这个镜像，这个镜像就会生成一个 Docker 容器

4、对容器的启动和停止就是对软件的启动和停止

#### 三、Docker 安装

> - 查看 centos 版本（使用 uname -r）
>
> Docker 要求 CentOS 系统的内核版本高于 3.10 
>
> - 升级软件包及内核（选做，使用 yum update）
>
> 如果你的 centos 内核版本低了，可以使用上面的命令升级
>
> - 安装 docker（使用 yum install docker）
> - 启动 docker（使用 systemctl start docker）
> - 将 docker 服务设为开机启动（使用 system enable docker）

#### 四、Docker 中常用操作

##### 1、镜像操作

| 操作 | 命令                                                         | 说明                                                     |
| ---- | ------------------------------------------------------------ | -------------------------------------------------------- |
| 检索 | docker search 关键字。eg:docker search mysql   意思就是在 docker hub 上搜索 mysql 这个镜像的信息 | 使用这个命令就是去 docker hub 上搜索镜像的详细信息       |
| 拉取 | docker pull 镜像名:tag    意思就是在 docker hub 上下载相关镜像 | :tag 时可选的，tag 表示标签，多维软件版本，默认是 latest |
| 列表 | docker images                                                | 查看所有本地镜像                                         |
| 删除 | docker rmi image-id                                          | 删除指定的本地镜像                                       |

##### 2、容器操作

| 操作     | 命令                                         | 说明                                                         |
| -------- | -------------------------------------------- | ------------------------------------------------------------ |
| 运行     | docker run --name 自定义容器名 -d image-name | --name :自定义容器名               -d：表示在后台运行      image-name：指定你要运行的镜像名称 |
| 列表     | docker ps （查看运行中的容器）               | 加上 -a 可以查看所有的容器                                   |
| 停止     | docker stop container-name/container-id      | 停止当前你运行的容器，这里可以写你自定义的容器名或者容器 id  |
| 启动     | docker start container -name/container-id    | 启动容器                                                     |
| 删除     | docker rm container-id                       | 删除指定容器                                                 |
| 端口映射 | -p:6379:6379                                 | -p：主机端口映射到容器内部端口                               |
| 容器日志 | docker logs container-name/container-id      |                                                              |

```shell
1、搜索镜像
docker search tomcat
2、拉取镜像
docker pull tomcat
3、根据镜像启动容器(镜像启动起来之后它就变成了一个容器)
docker run --name mytomcat -d tomcat:latest
4、查看运行中的容器
docker ps
5、停止运行中的容器
docker stop 容器id
6、查看所有容器
docker ps -a
7、启动容器
docker start 容器id
8、删除一个容器
docker rm 容器id
9、启动一个做了端口映射的 tomcat
docker run -d -p 8888:8080 tomcat
-d ：后台运行
-p：将主机的端口映射到容器的一个端口      主机端口：容器内部的端口
10、查看防火墙状态
service firewalld status
11、查看容器的日志
docker logs container-name/container-id
```

##### 3、docker 安装 mysql

```shell
1、首先在 dockerhub 中搜有 mysql 相关的镜像,确定号要下载哪一个镜像
2、你也可以使用 docker search mysql 查看在 dockerhub 中所有的 mysql 镜像
3、使用 docker pull mysql 把你需要的 mysql 镜像下载下来（pull 后面也可以跟别的版本的镜像,如果你只写 mysql 就下载默认 lastest 版本的）
4、下载完成后使用 docker images 命令查询一下本地是否有刚才下载的镜像
5、如果有,我们就把这个镜像运行起来,使用命令 docker run --name mysql01 -d mysql
6、上面步骤执行完之后,你使用 docker ps 查看一下容器是否启动了,发现并没有,你再使用 docker ps -a 查看一下,发现有 msyql 只不过它不是启动状态,是因为它启动的时候因为异常退出了
7、如果我们想找一下启动退出的原因,可以查看一下它的日志,使用命令 docker logs mysql01 你就可以看到错误原因了
8、很明显我们第 5 步启动 mysql 的命令是有问题的,你在 dockerhub 搜索 mysql 这个镜像的时候,下面会有对这个镜像的说明，及启动方式，可以看一下。
9、上面的步骤说了,我们刚启动的 mysql 容器是有问题的,所以我们使用命令 docker rm 42f09819908b 把这个容器删掉,然后使用 docker ps -a 查看一下，发现没有 mysql 这个容器了
10、所以正确的启动命令应该是：docker run --name mysql01 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
11、虽然上面的命令启动成功了,但是不能使用,因为没有做端口映射,外部访问不到容器内部
12、所以我们把容器停掉 docker stop mysql01
13、所以我们来启动一个做了端口映射的 mysql 容器 docker run -p 3306:3306 --name mysql02 -e MYSQL_ROOT_PASSWORD=123456 -d mysql   将本机的 3306 端口映射到容器的 3306 端口

指定创建数据库是 utf8 编码
docker run -p 3306:3306 --name mysql02 -e MYSQL_ROOT_PASSWORD=123456 -d mysql --character-set-server-utf8mb4 --collation-server-utf8mb4_unicode_ci
```









