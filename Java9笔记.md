# Java9笔记

### 一、概念介绍

> - JEP 
>   - JDK Enhancement Proposals  ：jdk 改进提案，每当需要有新的设想的时候，JEP 可以在 JCP（Java Community Process）之前或者同时提出**非正式的规范**（specification），被正式认可的 JEP 正式写进 JDK 的发展路线图并分配版本号
> - JSR
>   - Java Specification Requests ：Java 规范提案。新特性的规范出现在这一阶段，是指向 JCP（Java Community Process）提出新增一个 **标准化技术规范**的正式请求。请求可以来自于小组/项目 、JEP、JCP 成员或者 Java 社区成员的提案，每个 Java 版本都由相应的 JSR 支持。

### 二、Java9 新特性

#### 1、目录结构变化

1）、Java9 之前的目录结构

![Java9 之前的目录结构](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/Java9%E4%B9%8B%E5%89%8D%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.png)



2）、Java9 之后的目录结构

![Java9 之后的目录结构](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/Java9%E4%B9%8B%E5%90%8E%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84.png)

说明：

> 在 JDK9 中
>
> - 删除了 JRE 的子目录
> - `bin` 目录包含所有的命令
> - `conf` 目录包含用户可编辑的配置文件
> - `include` 目录包含编译本地代码时使用的 C/C++头文件
> - `jmods` 目录包含 JMOD 格式的平台模块
> - `legal` 目录包含法律声明
> - `lib` 目录包含一些动态链接库

#### 2、模块化系统

1）、模块化系统产生的背景

> Java 已经发展超过 20 年（95 年发布），Java 和相关生态在不断丰富的同时也暴露出了一些问题。
>
> - Java 运行环境的膨胀和臃肿。每次 JVM 启动的时候，至少会有 30~60M 的内存加载。主要原因是 JVM 需要加载 `rt.jar` ，不管其中的类是否被 classloader 加载，第一步整个 jar 都会被 JVM 加载到内存中去，而模块化可以根据模块的需要加载程序运行需要的 class 。
> - 很难真正的对代码进行封装，而系统并没有对不同部分（也就是 jar 文件）之间的依赖关系有个明确的概念。每一个公共的类都可以被类路径之下任何其他的公共类访问到，这样就会导致无意中使用了并不想被公开访问的 API 。
> - 本质上说，模块（module）的概念，其实就是 package 外再包裹一层，也就是说，用模块来管理各个 package ，通过声明某个 package 暴露，不声明默认就是隐藏。因此模块化是的代码组织上更安全，因为它可以指定哪些部分暴露，哪些部分隐藏。

2）、代码说明

![Java9模块化](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-Java9/java9%E6%A8%A1%E5%9D%97%E5%8C%96.png)



① 创建一个 `java9demo` 的工程，然后在这个工程下面创建 2 个 module。一个 `java9A` ，一个 `java9B`。

② 在 `java9A` 中创建一个实体类 Person，然后想在 `java9B` 中引用它，正常情况下是引用不到的，因为是存在两个模块中。

③ 我们在 `java9A` 模块的 `src` 目录下新建 `module-info.java ` 文件

```
module java9A {
    // 这里 exports 后面跟具体的包名
    exports com.hepingfly.bean;
}
```

④ 我们在 `java9B` 模块的 `src` 目录下新建 `module-info.java ` 文件

```
module java9demo {
    // requires 后面跟具体要引入的 module 的名字
    requires java9A;
}
```

⑤ 这样在 `java9B` 模块中就能引用 `java9A` 模块暴露出来的实体类了。

```java
public class ModuleTest {
    // 我在 java9B 模块中想引用 java9A 模块中的 Person 类
    public static void main(String[] args) {
        Person person = new Person();
        person.setName("hepingfly");
        System.out.println(person.getName());   // 打印 hepingfly
    }
}
```

