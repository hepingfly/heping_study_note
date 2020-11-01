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









