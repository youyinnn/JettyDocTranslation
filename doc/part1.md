### Ⅰ.开始使用Jetty
<span id="top"></span>
  - 1. [关于Jetty的介绍](#1关于jetty的介绍)
    - [什么是Jetty](#11什么是jetty)
    - [我应该使用哪个版本](#12我应该使用哪个版本)
    - [Jetty和JavaEE的Web规范](#13jetty和javaee的web规范)
    - [在Maven里面找到Jetty](#14在maven里面找到jetty)
  - 2. [使用Jetty](#2使用jetty)
    - [下载Jetty](#21下载jetty)
    - [运行Jetty](#22运行jetty)
      - [Demo Base](#221demo-base)
      - [创建Jetty Base](#222创建jetty-base)
      - [更改Jetty的端口号](#223更改jetty的端口号)
      - [为HTTPS和HTTP2添加SSL](#224为https和http2添加ssl)
      - [获取更多的start.jar选项](#225获取更多的startjar选项)
    - [部署Web应用](#23部署web应用)
  - 3. [关于Jetty Configuration的介绍](#3关于jetty-configuration的介绍)
    - [如何配置Jetty](#31如何配置jetty)
    - [可以在Jetty里面配置什么](#32可以在jetty里面配置什么)

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

[回到顶部](#top)
- - -
<span id="12我应该使用哪个版本"></span>
#### 1.2、我应该使用哪个版本

> *这里使用表格介绍了Jetty版本对应的发布年、持有者、支持的JDK版本、支持的协议、servlet规范版本、JSP规范版本、以及是否是稳定还是已经被遗弃。*
>
> *Markdown作表格实在是太蛋疼了我就不搞了，大家可以在[这里](http://www.eclipse.org/jetty/documentation/9.4.6.v20170531/what-jetty-version.html)去参照。*

> *大致说一下，Jetty8之前的版本都已经被弃用（Deprecated）了，更不要说是远古时期甚至是神话时期了（官网就这么描述的，好中二），从9.2开始就是稳定版了。92是jdk7、93以上就是jdk8了。*

[回到顶部](#top)
- - -
<span id="13jetty和javaee的web规范"></span>
#### 1.3、Jetty和JavaEE的Web规范

> *这里介绍的也是Jetty对JavaEE规范的支持程度，值得一提的是，Jetty并没有实现完整的规范，部分规范需要以Jetty插件的形式去支持。*
>
> *表格我也不搞了，大家可以在[这里](http://www.eclipse.org/jetty/documentation/9.4.6.v20170531/jetty-javaee.html)去参照。*

[回到顶部](#top)
- - -
<span id="14在maven里面找到jetty"></span>
#### 1.4、在Maven里面找到Jetty

> *就是告诉你Jetty的maven坐标，但是官网在这里比较坑，文档中说，Jetty是从项目成立初始就把项目发布到maven上了，由于历史原因，它的坐标变动了很多次，但是文档给出的示例代码也太不负责任了：*
```
<dependency>
  <groupId>org.eclipse.jetty</groupId>
  <artifactId>jetty-project</artifactId>
  <version>${project.version}</version>
</dependency>
```
> *如果是`jetty-project`这个A值你能找到东西就有鬼了。*

> *我在README中给出了依赖的模板，大家去参照那个。*

> *然后还给出了一个Changelogs的坐标，这个没啥卵用。*

[回到顶部](#top)
- - -
<span id="2使用jetty"></span>
### 2、使用Jetty
<span id="21下载jetty"></span>
#### 2.1、下载Jetty

##### 独立版本：

独立的Jetty版本你可以在eclipse公司主页上下载：

**Jetty** https://www.eclipse.org/jetty/download.html

它包含了zip和gzip两种格式，去下载一个最适合你系统的版本吧。让你下载好并且解压好之后，你会得到一个`jetty-distribution-VERSION`的目录。

把这个目录放在你常用的位置，文章后面的部分会引用到这个目录，我们使用`JETTY_HOME`或者`${jetty.home}`来在环境变量和Java程序中引用它。

> **注意**
>
> 你必须要知道的一点是：只有稳定版（stable releases）才能使用在生产环境。如果是被舍弃版本（deprecated）或者是里程碑版本（Milestones）即M版，或者是候选版（Release Candidates）即RC版，并不适合在生产环境中使用，因为它们可能会造成安全隐患或者是功能不完善等问题。

目录包含的内容：

> license-eplv10-aslv20.html
>
> 这是Jetty的开源许可文件

> README.txt
>
> 包含一些快速开始的指南

> VERSION.txt
>
> 版本信息

> bin/
>
> 帮助Jetty运行的shell程序

> demo-base/
>
> Jetty Base的演示目录

> etc/
>
> Jetty的XML配置文件目录

> lib/
>
> Jetty运行所必须jar包

> logs/
>
> 请求日志目录

> modules/
>
> 模块定义的目录

> notice.html
>
> 开源许可介绍和注意事项

> resources/
>
> 包含额外的资源、配置

> start.ini
>
> 包含有效的命令行参数的启动文件（模块、配置、xml配置文件）

> start.jar
>
> 运行Jetty的jar文件

> webapps/
>
> 包含在Jetty默认配置下运行的web项目

##### 家用版

> *这个我也不知道怎么介绍，官网的意思是说，这个是较小的Jetty版本，适合对Jetty比较熟悉了的高级的Jetty用户使用，下载地址和目录说明我就不写了，大家有能力自己去了解。*

[回到顶部](#top)
- - -
<span id="22运行jetty"></span>
#### 2.2、运行Jetty
- [2.2.1、Demo Base](#221demo-base)
- [2.2.2、创建Jetty Base](#222创建jetty-base)
- [2.2.3、更改Jetty的端口号](#223更改jetty的端口号)
- [2.2.4、为HTTPS和HTTP2添加SSL](#224为https和http2添加ssl)
- [2.2.5、获取更多的start.jar选项](#225获取更多的startjar选项)

##### 2.2.0、一个HelloWorld：

你可以使用一下命令来启动Jetty，默认使用8080端口，在这之前你得把Jetty的根目录配置到环境变量中：
```
> cd $JETTY_HOME
> java -jar start.jar

2015-06-04 10:50:44.806:INFO::main: Logging initialized @334ms
2015-06-04 10:50:44.858:WARN:oejs.HomeBaseWarning:main: This instance of Jetty is not running from a separate {jetty.base} directory, this is not recommended.  See documentation at http://www.eclipse.org/jetty/documentation/current/startup.html
2015-06-04 10:50:44.995:INFO:oejs.Server:main: jetty-9.3.0.v20150601
2015-06-04 10:50:45.012:INFO:oejdp.ScanningAppProvider:main: Deployment monitor [file:///opt/jetty-distribution-9.3.0.v20150601/webapps/] at interval 1
2015-06-04 10:50:45.030:INFO:oejs.ServerConnector:main: Started ServerConnector@19dfb72a{HTTP/1.1,[http/1.1]}{0.0.0.0:8080}
2015-06-04 10:50:45.030:INFO:oejs.Server:main: Started @558ms
```
然后你可以在浏览器地址栏上输入 http://localhost:8080 。当然，当前目录下没有任何有效的web项目，所以你访问任何url都是404页面

从警告信息中我们可以看到，它告诉我们，我们的命令并不是执行在`{jetty.base}`目录中，这样的做法是不被推荐的。

[下面](#创建jetty-base)会告诉你如何创建Jetty Base目录。

<span id="221demo-base"></span>
##### 2.2.1、Demo Base

在标准Jetty目录中，有一个名为`demo-base`的目录，它演示了不在`JETTY_HOME`目录下运行Jetty Base项目的推荐做法：
```
> cd $JETTY_HOME/demo-base/
> java -jar $JETTY_HOME/start.jar

2015-06-04 10:55:24.161:INFO::main: Logging initialized @308ms
2015-06-04 10:55:24.431:WARN::main: demo test-realm is deployed. DO NOT USE IN PRODUCTION!
2015-06-04 10:55:24.434:INFO:oejs.Server:main: jetty-9.3.0.v20150601
2015-06-04 10:55:24.457:INFO:oejdp.ScanningAppProvider:main: Deployment monitor [file:///opt/jetty-distribution-9.3.0.v20150601/demo-base/webapps/] at interval 1
2015-06-04 10:55:24.826:INFO:oejsh.ContextHandler:main: Started o.e.j.w.WebAppContext@c038203{/,file:///opt/jetty-distribution-9.3.0.v20150601/demo-base/webapps/ROOT/,AVAILABLE}{/ROOT}
2015-06-04 10:55:24.929:WARN::main: test-jaas webapp is deployed. DO NOT USE IN PRODUCTION!
2015-06-04 10:55:24.978:INFO:oejsh.ContextHandler:main: Started o.e.j.w.WebAppContext@46238e3f{/test-jaas,file:///tmp/jetty-0.0.0.0-8080-test-jaas.war-_test-jaas-any-9105214562680121772.dir/webapp/,AVAILABLE}{/test-jaas.war}
2015-06-04 10:55:25.162:WARN::main: async-rest webapp is deployed. DO NOT USE IN PRODUCTION!
2015-06-04 10:55:25.208:INFO:oejsh.ContextHandler:main: Started o.e.j.w.WebAppContext@6b67034{/async-rest,[file:///tmp/jetty-0.0.0.0-8080-async-rest.war-_async-rest-any-1023939491558622183.dir/webapp/, jar:file:///tmp/jetty-0.0.0.0-8080-async-rest.war-_async-rest-any-1023939491558622183.dir/webapp/WEB-INF/lib/example-async-rest-jar-9.3.0.v20150601.jar!/META-INF/resources],AVAILABLE}{/async-rest.war}
2015-06-04 10:55:25.311:WARN::main: test-jndi webapp is deployed. DO NOT USE IN PRODUCTION!
2015-06-04 10:55:25.386:INFO:oejsh.ContextHandler:main: Started o.e.j.w.WebAppContext@8b96fde{/test-jndi,file:///tmp/jetty-0.0.0.0-8080-test-jndi.war-_test-jndi-any-1692053319754270133.dir/webapp/,AVAILABLE}{/test-jndi.war}
2015-06-04 10:55:25.508:WARN::main: test-spec webapp is deployed. DO NOT USE IN PRODUCTION!
2015-06-04 10:55:25.594:INFO:oejsh.ContextHandler:main: Started o.e.j.w.WebAppContext@69930714{/test-spec,[file:///tmp/jetty-0.0.0.0-8080-test-spec.war-_test-spec-any-5518740932795802823.dir/webapp/, jar:file:///tmp/jetty-0.0.0.0-8080-test-spec.war-_test-spec-any-5518740932795802823.dir/webapp/WEB-INF/lib/test-web-fragment-9.3.0.v20150601.jar!/META-INF/resources],AVAILABLE}{/test-spec.war}
2015-06-04 10:55:25.781:INFO:oejsh.ContextHandler:main: Started o.e.j.w.WebAppContext@3eb7fc54{/proxy,file:///tmp/jetty-0.0.0.0-8080-xref-proxy.war-_xref-proxy-any-3068657547009829038.dir/webapp/,AVAILABLE}{/xref-proxy.war}
2015-06-04 10:55:25.786:INFO:oejsh.ContextHandler:main: Started o.e.j.s.h.MovedContextHandler@59662a0b{/oldContextPath,null,AVAILABLE}
2015-06-04 10:55:25.951:WARN::main: test webapp is deployed. DO NOT USE IN PRODUCTION!
2015-06-04 10:55:26.248:INFO:oejsh.ContextHandler:main: Started o.e.j.w.WebAppContext@4f83df68{/test,file:///tmp/jetty-0.0.0.0-8080-test.war-_test-any-5238659347611323540.dir/webapp/,AVAILABLE}{/test.war}
2015-06-04 10:55:26.255:INFO:oejs.ServerConnector:main: Started ServerConnector@5a9c4ad9{HTTP/1.1,[http/1.1]}{0.0.0.0:8080}
2015-06-04 10:55:26.259:INFO:oejus.SslContextFactory:main: x509={jetty.eclipse.org=jetty} wild={} alias=null for SslContextFactory@23941fb4(file:///opt/jetty-distribution-9.3.0.v20150601/demo-base/etc/keystore,file:///opt/jetty-distribution-9.3.0.v20150601/demo-base/etc/keystore)
2015-06-04 10:55:26.269:INFO:oejs.ServerConnector:main: Started ServerConnector@5d908d47{SSL,[ssl, http/1.1]}{0.0.0.0:8443}
2015-06-04 10:55:26.270:INFO:oejs.Server:main: Started @2417ms
```
同样，你可以访问8080网站去查看这个项目。上面部署了一些简单的页面和demo。

你还可以使用以下命令，来查看这个demo-base的运行配置：
```
> cd $JETTY_HOME/demo-base/
> java -jar $JETTY_HOME/start.jar --list-modules
...

> java -jar %JETTY_HOME/start.jar --list-config
...
```
`--list-modules`命令会返回一张完整的列表，这个列表包含了服务器可用的并且正在使用的模块，它同时还会显示模块所在的目录，以及它们是怎样、用什么命令实现的，当前项目所依赖的模块，以及相关的jar包等。

`--list-config`命令会显示一些关于服务器重要信息。包含Java和Jetty和环境变量、配置命令、任何的JVM参数或者系统变量设置、基础服务器配置、全列的Jetty服务器的类路径、被激活使用的JettyXML配置文件。

<span id="222创建jetty-base"></span>
##### 2.2.2、创建Jetty Base

上述demo-bases目录给我们展示了Jetty的Jetty Base机制。一个Jetty Base目录允许使用单独的配置并且可以在Jetty目录以外的地方建立一个Jetty服务器实例，这样一来可以减小升级带来的代价。Jetty的默认配置是基于以下两个配置项：

**jetty.home**：用来定义Jetty根目录的位置的配置项，根目录包含：libs、默认模块、默认XML文件（具体的如 start.jar、lib、etc）。
**jetty.base**：用来定义一个Jetty服务器的特殊实现，这个目录同时包含：它的配置文件、日志文件、web项目（具体如 start.d/*.ini文件，logs、webapps）。

> **注意**
>
> 你必须认真对待JettyHome目录和保持它不变。如果你需要变动一些目录或者配置，你应该在JettyBase目录中进行。

`jetty.home`和`jetty.base`可以使用命令行去显示的配置，或者它们可以从环境变量中判断该使用什么配置，你可以这样写命令：
```
> cd $JETTY_BASE
> java -jar $JETTY_HOME/start.jar
```
下面的命令会创建一个base目录，这个目录支持HTTP连接和web项目的部署模块，然后把demo项目复制到我们创建的这个目录下部署：
```
> JETTY_BASE=/tmp/mybase
> mkdir $JETTY_BASE
> cd $JETTY_BASE
> java -jar $JETTY_HOME/start.jar

WARNING: Nothing to start, exiting ...

Usage: java -jar start.jar [options] [properties] [configs]
       java -jar start.jar --help  # for more information

> java -jar $JETTY_HOME/start.jar --create-startd
INFO : Base directory was modified
> java -jar $JETTY_HOME/start.jar --add-to-start=http,deploy

INFO: server          initialised (transitively) in ${jetty.base}/start.d/server.ini
INFO: http            initialised in ${jetty.base}/start.d/http.ini
INFO: security        initialised (transitively) in ${jetty.base}/start.d/security.ini
INFO: servlet         initialised (transitively) in ${jetty.base}/start.d/servlet.ini
INFO: webapp          initialised (transitively) in ${jetty.base}/start.d/webapp.ini
INFO: deploy          initialised in ${jetty.base}/start.d/deploy.ini
MKDIR: ${jetty.base}/webapps
INFO: Base directory was modified

> cp $JETTY_HOME/demo-base/webapps/async-rest.war webapps/ROOT.war
> java -jar $JETTY_HOME/start.jar

2015-06-04 11:10:16.286:INFO::main: Logging initialized @274ms
2015-06-04 11:10:16.440:INFO:oejs.Server:main: jetty-9.3.0.v20150601
2015-06-04 11:10:16.460:INFO:oejdp.ScanningAppProvider:main: Deployment monitor [file:///tmp/mybase/webapps/] at interval 1
2015-06-04 11:10:16.581:WARN::main: async-rest webapp is deployed. DO NOT USE IN PRODUCTION!
2015-06-04 11:10:16.589:INFO:oejw.StandardDescriptorProcessor:main: NO JSP Support for /, did not find org.eclipse.jetty.jsp.JettyJspServlet
2015-06-04 11:10:16.628:INFO:oejsh.ContextHandler:main: Started o.e.j.w.WebAppContext@1a407d53{/,[file:///tmp/jetty-0.0.0.0-8080-ROOT.war-_-any-4510228025526425427.dir/webapp/, jar:file:///tmp/jetty-0.0.0.0-8080-ROOT.war-_-any-4510228025526425427.dir/webapp/WEB-INF/lib/example-async-rest-jar-9.3.0.v20150601.jar!/META-INF/resources],AVAILABLE}{/ROOT.war}
2015-06-04 11:10:16.645:INFO:oejs.ServerConnector:main: Started ServerConnector@3abbfa04{HTTP/1.1,[http/1.1]}{0.0.0.0:8080}
2015-06-04 11:10:16.646:INFO:oejs.Server:main: Started @634ms
```

> *译者文外补充：第一行命令是在当前运行时环境变量中添加一条环境变量，把`JETTY_BASE`目录指定为`/tmp/mybase`，第二行创建这个目录，第三行是进入到这个目录，第四行是尝试运行这个目录，但是提示失败因为目录里啥也没有。然后使用`--create-startd`命令和`--add-to-start=http,deploy`命令去初始化和添加HTTP连接和Web部署模块，然后把之前demo-base目录中的一个war包复制过来，最后启动项目*

<br>
<span id="223更改jetty的端口号"></span>
##### 2.2.3、更改Jetty服务器运行的默认端口号

```
> cd $JETTY_BASE
> java -jar $JETTY_HOME/start.jar jetty.http.port=8081
...
```
当项目运行的时候，就会在8081端口中运行，你需要注意的是，使用命令行配置只对当次的JettyBase中的web项目有效，如果你想永久地改变当前JettyBase的web项目运行的端口号，你可以修改start.d目录中http.ini文件的配置。
> *译者文外补充：前提是你为这个JettyBase添加了HTTP连接*

> **NOTE：**
>
> 配置通过以下的链来生效：
>
> - `start.d/http.ini`文件是有效执行的命令行的一部分，它还包含了`--modules-http`参数，这个参数会激活http模块。
>
> - `modules/http.mod`文件定义了http模块，这个模块规定了它所使用的etc/jetty-http.xml配置文件和template.ini文件。
>
> - `jetty.http.port`这个配置是被在`etc/jetty.http.xml`文件中的`PropertyXML`对象把端口设置注入到`ServerConnector`实例中去的。
>
> 你可以在后面的章节中学到配置的更详细的说明。
