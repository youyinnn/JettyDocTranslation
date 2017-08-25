### Ⅰ.开始使用Jetty
<span id="开始使用jetty"></span>
  - 1. [关于Jetty的介绍](#1关于jetty的介绍)
    - [什么是Jetty](#11什么是jetty)
    - [我应该使用哪个版本](#12我应该使用哪个版本)
    - [Jetty和JavaEE的Web规范](#13jetty和javaee的web规范)
    - [在Maven里面找到Jetty](#14在maven里面找到jetty)
  - 2. [使用Jetty]
    - [下载Jetty]
    - [运行Jetty]
    - [部署Web应用]
  - 3. [关于Jetty Configuration的介绍]
    - [如何配置Jetty]
    - [可以在Jetty里面配置什么]

- - -
<span id="1关于jetty的介绍"></span>
### 1、关于Jetty的介绍
<span id="11什么是jetty"></span>
#### 1.1、什么是Jetty
Jetty是一个开源的项目，它可以作为HTTP服务器，HTTP客户端，和Servlet容器来使用。

这个指南总共分为以下5个部分：
> - 第一部分，告诉你怎么开始使用Jetty。 它提供了Jetty的相关信息以及你可以在哪里下载到它，以及你可以在哪些仓库源中找到它，比如Maven的中央仓库。它还提供了一个Quick Start，告诉你如何启动和运行Jetty，以及一个关于如何配置Jetty和Jetty可以配置些什么的概述。
> - 第二部分，更加详细的给予你配置Jetty的指南。它解释了Jetty如何去部署一个Web应用，如何配置上下文和连接，以及如何实现SSL和其他的安全措施。
> - 第三部分，这是如何管理Jetty的重点指南。它从服务器的启动到session的管理，日志记录，HTTP/2的支持，再到Jetty的优化，这些章节会帮助管理者学到很多Jetty实例以外的知识。这个部分当然也会告诉你怎么配置和其他servlet容器一样都会有的许多配置，比如JNDI和JMX。
> - 第四部分，这个部分注重的是Jetty的开发，是给更高级的Jetty用户准备的。里面很大一部分是告诉你如何在一个现有项目里面去使用嵌入式Jetty。它还包含了几个栗子，里面讲述了许多Jetty架构之外的东西。这个部分也会告诉你如何使用Maven中的Jetty插件，同时也会介绍如何debugJetty。
> - 第五部分，这部分是一些参考内容，包括一些Jetty架构的指南、JettyXML的语法、Jetty的分布式指南和一些常见问题的排查。同样会告诉你怎么才可以参与到Jetty社区中，参与贡献。

[回到顶部](#开始使用jetty)
- - -
<span id="12我应该使用哪个版本"></span>
#### 1.2、我应该使用哪个版本

> *这里使用表格介绍了Jetty版本对应的发布年、持有者、支持的JDK版本、支持的协议、servlet规范版本、JSP规范版本、以及是否是稳定还是已经被遗弃。*
>
> *Markdown作表格实在是太蛋疼了我就不搞了，大家可以在[这里](http://www.eclipse.org/jetty/documentation/9.4.6.v20170531/what-jetty-version.html)去参照。*

> *大致说一下，Jetty8之前的版本都已经被弃用（Deprecated）了，更不要说是远古时期甚至是神话时期了（官网就这么描述的，好中二），从9.2开始就是稳定版了。92是jdk7、93以上就是jdk8了。*

[回到顶部](#开始使用jetty)
- - -
<span id="13jetty和javaee的web规范"></span>
#### 1.3、Jetty和JavaEE的Web规范

> *这里介绍的也是Jetty对JavaEE规范的支持程度，值得一提的是，Jetty并没有实现完整的规范，部分规范需要以Jetty插件的形式去支持。*
>
> *表格我也不搞了，大家可以在[这里](http://www.eclipse.org/jetty/documentation/9.4.6.v20170531/jetty-javaee.html)去参照。*

[回到顶部](#开始使用jetty)
- - -
