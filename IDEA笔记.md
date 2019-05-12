# IDEA 笔记

### 一、IntelliJ IDEA 介绍

#### 1、JetBrains 公司介绍

IDEA （https://www.jetbrains.com/）是 JetBrains 公司的产品，公司旗下还有其他产品，比如：

> - WebStorm  用于开发前端
> - PyCharm   用于开发 python

注：

==我们之前用的 Eclipse 是 IBM 公司开发的==

#### 2、IntelliJ IDEA 介绍

IDEA 全称 IntelliJ IEDA,是 Java 语言的集成开发环境，IDEA 在业界被公认为是最好的 java 开发工具之一，尤其在智能代码助手、代码自动提示、重构、J2EE支持等方面的功能可以说是超常的。

#### 3、IDEA 的主要优势

1️⃣ 强大的整合能力。比如：Git 、Maven、Spring 等

2️⃣ 提示功能的快速、便捷

3️⃣ 提示功能的范围广

4️⃣ 好用的快捷键和代码模板

5️⃣精准搜索

#### 4、IDEA 安装目录

windows 版本的 IDEA ，找到安装目录，安装目录下面有个 bin 目录

![idea 安装目录下虚拟机配置信息](http://pfwju0tz1.bkt.clouddn.com/idea%E5%AE%89%E8%A3%85%E7%9B%AE%E5%BD%95%E4%B8%8B%E8%99%9A%E6%8B%9F%E6%9C%BA%E9%85%8D%E7%BD%AE%E4%BF%A1%E6%81%AF.png)

在 Mac 系统中对应着 「idea.vmoptions」这个文件可以修改虚拟机配置

```
idea.vmoptions:

-Xms128m           初始内存大小，设置这个值可以提高 idea 启动的速度
-Xmx750m           最大内存大小，把这个值设置大点，可以降低垃圾回收的频率
-XX:ReservedCodeCacheSize=240m    保留代码的缓存大小，把这个值调大，可以降低无用代码回收的频率，进而提高程序性能
-XX:+UseCompressedOops
```

#### 5、IDEA 的设置目录

![idea各种配置保存目录](http://pfwju0tz1.bkt.clouddn.com/idea%E5%90%84%E7%A7%8D%E9%85%8D%E7%BD%AE%E4%BF%9D%E5%AD%98%E7%9B%AE%E5%BD%95.png)

在 windows 下你 idea 启动之后会生成这两个文件夹，这是idea 的各种配置的保存目录。这个设置目录有一个特性，就是你删除掉整个目录之后，重新启动 idea 会自动帮你生成一个全新的默认配置，所以很多时候如果你把 idea 的配置改坏了，只要删除掉该目录一切都会还原到默认配置。

#### 6、创建模块（Module）

1️⃣ 在Eclipse 中我们有 Workspace（工作空间）和 Project（工程）的概念，在 IDEA 中只有 Project（工程）和 Module（模块）的概念。这里的对应关系为：

![idea中的project和module对比eclipse](http://pfwju0tz1.bkt.clouddn.com/idea%E4%B8%AD%E7%9A%84project%E5%92%8Cmodule.png)



2️⃣从 Eclipse 转过来的人总是下意识的要在同一个窗口管理 n 个项目，这在 IntelliJ IDEA 是无法做到的。idea 提供的方案是打开多个项目实例，即打开多个项目窗口。即：一个 Project 打开一个 Window窗口

3️⃣ 在 idea 中 Project 是最顶级的级别，次级别是 Module 。一个 Project 可以有多个 Module。idea 采用这种一个 Project 多个 Module 的方式，目前主流的大型项目都是分布式部署的，结构都是类似这种多 Module 结构：

![主流大型项目结构](http://pfwju0tz1.bkt.clouddn.com/%E4%B8%BB%E6%B5%81%E5%A4%A7%E5%9E%8B%E9%A1%B9%E7%9B%AE%E7%BB%93%E6%9E%84.png)

#### 7、设置代码模板

在 idea 中主要有两处可以设置代码模板

> - Preferences  ——> Editor  ———> Postfix Completion
> - Preferences  ———>  Live Templates
>
> 这两种的区别就是，第一种是预先设定好的不能修改，第二种支持修改和自定义。
>
> 代码模板的主要作用就是，我们可以通过预先设定好的代码模板，快速敲出我们想要的代码。





