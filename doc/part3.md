<span id="top"></span>
### Ⅲ. Jetty管理者指南
  - 略
  - 13. [注解](#13注解)
    - [快速设置](#131快速设置)
      - [Jetty发行版](#1311jetty发行版)
      - [关于Jetty Maven Plugin](#1312关于jetty-maven-plugin)
      - [关于嵌入式](#1313关于嵌入式)
    - [使用注解](#132使用注解)
    - [在Jetty嵌入式开发中使用注解](#133在jetty嵌入式开发中使用注解)
  - 略

- - -

<span id="13注解"></span>
#### 13、注解
- [快速设置](#131快速设置)
  - [Jetty发行版](#1311jetty发行版)
  - [关于Jetty Maven Plugin](#1312关于jetty-maven-plugin)
  - [关于嵌入式](#1313关于嵌入式)
- [使用注解](#132使用注解)
- [在Jetty嵌入式开发里面使用注解](#133在jetty嵌入式开发中使用注解)

Jetty支持servlet规范的annotation。但是这默认是不开启的，所以接下来的内容会向你展示如何开启这个支持，以及如何使用它。

<br>

<span id="131快速设置"></span>
##### 13.1、快速设置
- 13.1.1、[关于Jetty发行版](#1311关于jetty发行版)
- 13.1.2、[关于Jetty Maven Plugin](#1312关于jetty-maven-plugin)
- 13.1.3、[关于嵌入式](#1313关于嵌入式)
<br>

<span id="1311关于jetty发行版"></span>
###### 13.1.1、关于Jetty发行版

如果你是使用的Jetty发行版，那么注解是默认支持的。注解模块以及它所需要的过渡的依赖都是负责支持注解功能的。

你需要注意的是注解功能依赖JNDI，比如`@Resource`和`@Resources`都是通过JNDI模块来实现的，JNDI模块是注解模块的过渡依赖模块。

> *译者文外补充：Jetty发行版就是Jetty的客户端，与之相对的有Jetty嵌入式，我感觉文档是在强调这两个区别。*

<br>

<span id="1312关于jetty-maven-plugin"></span>
###### 13.1.2、关于Jetty Maven Plugin

注解和JNDI在Jetty Maven Plugin中都是预置的。

<br>

<span id="1313关于嵌入式"></span>
###### 13.1.3、关于嵌入式

为了在嵌入式Jetty场景中使用注解，你需要加入`jetty-annotations`jar包和所有它依赖的jar包到classpath下。你还需要把`org.eclipse.jetty.annotations.AnnotationConfiguration`类添加到装载`Configuration classes`的列表中，并把这个列表应用到代表你的web应用的`org.eclipse.jetty.wabapp.WebAppContext`类中。

下面这个栗子设置了一个标准的`test-spec.war`web应用，这个包你可以在我们发行版的demo-base中找到。你需要注意的是`test-spec.war`并不只运用到了注解，还运用到了JNDI，所以这个栗子还激活了其它一系列的配置（通过` org.eclipse.jetty.plus.webapp.EnvConfiguration`，`org.eclipse.jetty.plus.webapp.PlusConfiguration`和它们自身依赖的jars）。

```
package org.eclipse.jetty.embedded;

import java.io.File;

import org.eclipse.jetty.plus.jndi.EnvEntry;
import org.eclipse.jetty.plus.jndi.Resource;
import org.eclipse.jetty.plus.jndi.Transaction;
import org.eclipse.jetty.security.HashLoginService;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.webapp.Configuration;
import org.eclipse.jetty.webapp.WebAppContext;

/**
 * ServerWithAnnotations
 */
public class ServerWithAnnotations
{
    public static final void main( String args[] ) throws Exception
    {
        // Create the server
        Server server = new Server(8080);

        // Enable parsing of jndi-related parts of web.xml and jetty-env.xml
        Configuration.ClassList classlist = Configuration.ClassList
                .setServerDefault(server);
        classlist.addAfter("org.eclipse.jetty.webapp.FragmentConfiguration",
                "org.eclipse.jetty.plus.webapp.EnvConfiguration",
                "org.eclipse.jetty.plus.webapp.PlusConfiguration");
        classlist.addBefore(
                "org.eclipse.jetty.webapp.JettyWebXmlConfiguration",
                "org.eclipse.jetty.annotations.AnnotationConfiguration");

        // Create a WebApp
        WebAppContext webapp = new WebAppContext();
        webapp.setContextPath("/");
        File warFile = new File(
                "../../jetty-distribution/target/distribution/demo-base/webapps/test.war");
        webapp.setWar(warFile.getAbsolutePath());
        webapp.setAttribute(
                "org.eclipse.jetty.server.webapp.ContainerIncludeJarPattern",
                ".*/javax.servlet-[^/]*\\.jar$|.*/servlet-api-[^/]*\\.jar$");
        server.setHandler(webapp);

        // Register new transaction manager in JNDI
        // At runtime, the webapp accesses this as java:comp/UserTransaction
        new Transaction(new com.acme.MockUserTransaction());

        // Define an env entry with webapp scope.
        new EnvEntry(webapp, "maxAmount", new Double(100), true);

        // Register a mock DataSource scoped to the webapp
        new Resource(webapp, "jdbc/mydatasource", new com.acme.MockDataSource());

        // Configure a LoginService
        HashLoginService loginService = new HashLoginService();
        loginService.setName("Test Realm");
        loginService.setConfig("src/test/resources/realm.properties");
        server.addBean(loginService);

        server.start();
        server.join();
    }

}
```

<br>

<span id="132使用注解"></span>
##### 13.2、使用注解
- 13.2.1、[支持哪些注解](#1321支持哪些注解)
- 13.2.2、[检测注解和自检注解的对比](#1322检测注解和自检注解的对比)
- 13.2.3、[哪些jar包支持扫描检查注解的](#1323哪些jar包支持扫描检查注解的)
- 13.2.4、[多线程注解扫描](#1324多线程注解扫描)
- 13.2.5、[ServletContainerInitializers](#1325servletcontainerinitializers)

<span id="132使用注解"></span>
###### 13.2.1、支持哪些注解

Jetty支持解释和运用的注解有：

- @Resource
- @Resources
- @PostConstruct
- @PreDestroy
- @DeclaredRoles
- @RunAs
- @MultipartConfig
- @WebServlet
- @WebFilter
- @WebListener
- @WebInitParam
- @ServletSecurity, @HttpConstraint, @HttpMethodConstraint
- @HandlesTypes (on ServletContainerInitializers)

<br>

<span id="1322检测注解和自检注解的对比"></span>
###### 13.2.2、检测注解和自检注解的对比

一些类型的注解可以标注在任何类中，虽然这些注释并不一定能和容器产生直接交互作用。这些类型的注解我们称为“discovered（发现/检测）注释”，这代表容器需要主动的去检测这些注释。其它类型的注释我们称之为“introspected（自检）注释”，这意味着这种自检行为会发生在被容器的生命周期中直接作用的类上面（比如`javax.servlet.Servlet, javax.servlet.Filter`等等）因此可以通过对该类的简单的检测就可以找到。

一些简单的“discovered”注释比如：
- @WebServlet
- @WebFilter
- @WebListener

一些简单的“introspected”注释比如：
- @PostConstruct
- @PreDestroy
- @Resource

<br>

<span id="1323哪些jar包支持扫描检查注解的"></span>
###### 13.2.3、哪些jar包支持扫描检查注解的
<br>

web.xml文件可以包含`metadata-complete`属性。如果它的值为真，那么将不会扫描可检测的注释。然而，对类的扫描行为仍然会发生，因为有`javax.servlet.ServletContainerInitializer`。类一旦实现了这个接口，Jetty就会使用`javax.util.ServiceLoader`机制找到这个类，并且如果它里面有`@HandlesTypes`注释，那么jetty就必定会有层次地扫描这个类。如果你容器路径下或者`WEB-INF/lib`目录中包含了许多jar包的话，这样会非常消耗时间。

如果扫描一旦发生，要么是你没指定`metadata-complete`或者指定为false，要么是存在一个或者多个`javax.servlet.ServletContainerInitializer`和`@HandlesTypes`，这样Jetty就必须兼顾容器的路径内容以及web应用的类路径内容。

默认的情况下，Jetty不会扫描任何包含在容器类路径下的任何类。如果你需要扫描容器类路径下的类或者jar文件的话，你可以使用`org.eclipse.jetty.server.webapp.ContainerIncludeJarPattern`，这是一个`WebAppContext`属性，可以为你想要扫描容器类路径下的jar或者目录指定匹配模式，好进行扫描行为。

默认的情况下，Jetty会按照顺序扫描**所有**`WEB-INF/classes`下的类和**所有**`WEB-INF/lib`下的jar包，如果你有在web.xml下制定任何绝对的或者相对的条款顺序的话。如果你的web应用包含许多jar文件，你可以通过省略扫描这些jar包来显式的加速部署。为了做到这一点，你可以在`WebAppContext`中设置`org.eclipse.jetty.server.webapp.WebInfIncludeJarPattern`属性，通过模式来定义你需要扫描的jar包。

你需要注意到的是如果你有配置web应用的`extraClasspath`，那么它也会参与扫描过程。这里面的任何jar或者类都会像在`WEB-INF/classes`或者`WEB-INF/lib`中一样被扫描。

如果你需要控制它们被应用到的顺序，你可以在[这里](#1325servletcontainerinitializers)了解到更多。


<span id="1324多线程注解扫描"></span>
###### 13.2.4、多线程注解扫描

如果要执行注释扫描，默认的情况下，Jetty会使用多线程的方式去实现，企图以最短的时间完成。

如果处于某些原因你不想以多线程的方式扫描，你可以在Jetty中配置为单线程扫描。有以下几种方式：

- Set the context attribute org.eclipse.jetty.annotations.multiThreaded to false
- Set the Server attribute org.eclipse.jetty.annotations.multiThreaded to false
- Set the System property org.eclipse.jetty.annotations.multiThreaded to false

第一种只适用当前web应用，第二种适用所有部署在同一个server实例上的webapp，第三种适用所有在同一个JVM下部署的webapp。

默认的情况下，Jetty会最大等待60秒来完成所有线程的扫描。你可以通过以下的设置来配置你想要的秒数：

- Set the context attribute org.eclipse.jetty.annotations.maxWait
- Set the Server attribute org.eclipse.jetty.annotations.maxWait
- Set the System property org.eclipse.jetty.annotations.maxWait

第一种只适用当前web应用，第二种适用所有部署在同一个server实例上的webapp，第三种适用所有在同一个JVM下部署的webapp。

<br>


<span id="1325servletcontainerinitializers"></span>
###### 13.2.5、ServletContainerInitializers

<br>

[回到顶部](#top)
- - -
