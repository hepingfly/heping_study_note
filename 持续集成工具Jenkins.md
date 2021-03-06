# 持续集成工具 Jenkins

#### 1、持续集成、持续部署、持续交互概念

**持续部署：**

> 在项目开发过程中，进行单元测试能够通过，但是部署到服务器上运行却出现问题。怎么做才比较好一点呢？
>
> 所以仅仅单元测试还不够，各个模块都必须能够在服务器上运行。
>
> - 关注点
>   - 持续部署的关注点在于**项目功能部署至服务器后可运行**，为下一步测试环节或最终用户正式使用做好准备

**持续集成：**

> 在项目开发过程中，各个小组分别负责各个具体模块开发，本模块独立测试虽然能够通过，但是上线前夕将所有模块整合到一起集成测试却发现很多问题，想要解决就需要把很多代码返工重写而且仍然可能有问题，但是现在时间很可能不够了。怎么做才比较好一点呢？
>
> 经常性、频繁的把所有模块集成在一起进行测试，有问题尽早发现，这就是持续集成。
>
> - 关注点
>   - 持续集成的关注点在于尽早发现项目整体运行问题，尽早解决

**持续交付：**

> 在项目开发过程中，项目的各个升级版本之间间隔时间太长，对用户反馈感知迟钝，无法精确改善用户体验，用户流失严重。那怎么做才会好一点呢？
>
> 用小版本不断进行快速迭代，不断收集用户反馈信息，用最快的速度进行改进优化。
>
> - 关注点
>   - 持续交付的关注点在于研发团队的最新代码能够尽快让最终用户体验到

#### 2、持续集成工具

**Jenkins 和 Hudson**

> 目前最流行的一款持续集成及自动化部署工具。
>
> Jenkins 和 Hudson 之间的关系： 2009 年，甲骨文收购了 sun 并继承了 Hudson 代码库。在 2001 年初，甲骨文和开源社区之间关系破裂，该项目被分成两个独立的项目：
>
> - Jenkins：由大部分原始开发人员组成
> - Hudson：由甲骨文公司继续管理
>
> 所以 Jenkins 和 Hudson 是两款非常相似的产品

**技术组合：**

> Jenkins 可以整合 Github 或 Subversion
>
> Hudson 也可以整合 Github 或 Subversion

#### 3、手动部署和自动部署

![手动部署](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-jenkins/%E6%89%8B%E5%8A%A8%E9%83%A8%E7%BD%B2.png)

![自动部署](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-jenkins/%E8%87%AA%E5%8A%A8%E9%83%A8%E7%BD%B2.png)

#### 4、Jenkins + svn 

1、版本服务器如果你使用 svn 的话，因为 Jenkins 要去访问 svn ，所以需要配置一下版本库访问的账号和密码。

2、svnserve.conf 文件中 anon-access 一定要打开注释并设置为 none   `anon-access=none`

#### 5、应用服务器 tomcat 设置

因为 Jenkins 需要去访问 tomcat ，因此为了安全性考虑，tomcat 需要设置用户名和密码及额外配置一些角色

```xml
<!-- 编辑 apache-tomcat-8.5.49/conf/tomcat-users.xml 配置文件,增加以下配置 -->
<role rolename="manager-gui"/>
<role rolename="manager-script"/>
<role rolename="manager-jmx"/>
<role rolename="manager-status"/>
<user username="tomcat_user" password="123456" roles="manager-gui,manager-script,manager-jmx,manager-status"/>
```

#### 6、运行 Jenkins 主体程序并初始化

**Jenkins 部署：**

① 下载 jenkins.war 包，并放在 tomcat 的 webapps 目录下

② 修改 tomcat 的 server.xml 文件

```xml
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" URIEncoding="UTF-8" />
<!-- 加上 URIEncoding="UTF-8"  防止 get 请求提交时，带中文请求参数,参数乱码  -->
```

③ 启动 tomcat 并访问 `ip:port/jenkins`





























