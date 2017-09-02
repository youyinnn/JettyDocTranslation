<span id="top"></span>
### Ⅳ. Jetty开发指南-23.Jetty和Maven
- 23. [Jetty和Maven](#23jetty和maven)
  - [使用Maven](#231使用maven)
    - [使用Maven做嵌入式Jetty开发](#2311使用maven做嵌入式jetty开发)
    - [使用Maven开发标准的WebApp](#2321使用maven开发标准的wabapp)
  - [配置Jetty的Maven插件](#232配置jetty的maven插件)
    - [快速开始](#2321快速开始)
    - [支持目标](#2322支持目标)
    - [配置Jetty容器](#2323配置jetty容器)
    - [配置你的WebApp](#2324配置你的webapp)
    - [jetty:run](#2325jettyrun)
    - [jetty:run-war](#2326jettyrunwar)
    - [jetty:run-exploded](#2327jettyrunexploded)
    - [jetty:run-forked](#2328jettyrunforked)
    - [jetty:start](#2329jettystart)
    - [jetty:stop](#23210jettystop)
    - [jetty:effective-web-xml](#23211jettyeffectivewebxml)
    - [使用覆盖war](#23212使用覆盖war)
    - [配置Security](#23213配置security)
    - [使用多个WebappRoot目录](#23214使用多个webapproot目录)
    - [运行一个以上的Webapp](#23215运行一个以上的webapp)
    - [设置系统属性](#23216设置系统属性)
  - [Jetty Maven插件的文件扫描](#233jetty-maven插件的文件扫描)
  - [Jetty Jspc Maven插件](#234jetty-jspc-maven插件)

- - -

<span id="23jetty和maven"></span>
### 23、Jetty和Maven

- 23.1、[使用Maven](#231使用maven)
- 23.2、[配置Jetty的Maven插件](#232配置jetty的maven插件)
- 23.3、[Jetty Maven插件的文件扫描](#233jetty-maven插件的文档扫描)
- 23.4、[Jetty Jspc Maven插件](#234jetty-jspc-maven插件)

<span id="231使用maven"></span>
#### 23.1、使用Maven

- 23.1.1、[使用Maven做嵌入式Jetty开发](#2311使用maven做嵌入式jetty开发)
- 23.1.2、[使用Maven开发标准的WebApp](#2321使用maven开发标准的wabapp)

> *插播原文：Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project’s build, reporting and documentation from a central piece of information.*

所以这是一个理想的用来构建web应用的工具，一些插件比如`jetty-maven-plugin`可以很容易的就运行一个web应用，以节省了部署操作所花费的时间。你可以非常容易地使用Maven去构建、测试、运行一个嵌入了Jetty服务器的web项目。

> **NOTE**
>
> 使用`jetty-maven-plugin`并不是必须的。但是用Maven来实现Jetty是一种流行的做法，不过你还是可以根据你的业务需求来选择其它流行的工具，比如ant和gradle。

首先我们来看一个非常简单的java应用的HelloWorld，它嵌入了Jetty。然后看一个简单的web应用，它使用`jetty-maven-plugin`来加快了开发周期。

<span id="2311使用maven做嵌入式jetty开发"></span>
##### 23.1.1、使用Maven做嵌入式Jetty开发

为了理解对Jetty的构建和运行的基本操作，你首先得了解“Jetty的嵌入式开发”。

俗话说得好，“约定大于配置”，所以你最好使用Maven推荐的标准web应用目录结构。你可以使用`archetypes `去快速地设置好基本的Maven项目结构，但在本教程中我们将会手动地创建项目结构：

```
> mkdir JettyMavenHelloWorld
> cd JettyMavenHelloWorld
> mkdir -p src/main/java/org/example
```

<br>

###### 23.1.1.1、创建HelloWorld类

路径`src/main/java/org/example/HelloWorld.java`:

```
package org.example;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.ServletException;
import java.io.IOException;
import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.server.Request;
import org.eclipse.jetty.server.handler.AbstractHandler;

public class HelloWorld extends AbstractHandler
{
    public void handle(String target,
                       Request baseRequest,
                       HttpServletRequest request,
                       HttpServletResponse response)
        throws IOException, ServletException
    {
        response.setContentType("text/html;charset=utf-8");
        response.setStatus(HttpServletResponse.SC_OK);
        baseRequest.setHandled(true);
        response.getWriter().println("<h1>Hello World</h1>");
    }

    public static void main(String[] args) throws Exception
    {
        Server server = new Server(8080);
        server.setHandler(new HelloWorld());

        server.start();
        server.join();
    }
}
```

<br>

###### 23.1.1.2、创建POM文件

```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <modelVersion>4.0.0</modelVersion>
  <groupId>org.example</groupId>
  <artifactId>hello-world</artifactId>
  <version>0.1-SNAPSHOT</version>
  <packaging>jar</packaging>
  <name>Jetty HelloWorld</name>

  <properties>
      <!-- Adapt this to a version found on
           http://central.maven.org/maven2/org/eclipse/jetty/jetty-maven-plugin/
        -->
      <jettyVersion>9.3.9.v20160517</jettyVersion>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.eclipse.jetty</groupId>
      <artifactId>jetty-server</artifactId>
      <version>${jettyVersion}</version>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
        <version>1.1</version>
        <executions>
          <execution><goals><goal>java</goal></goals></execution>
        </executions>
        <configuration>
          <mainClass>org.example.HelloWorld</mainClass>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```
<br>

###### 23.1.1.3、构建和运行嵌入式HelloWorld

你现在可以使用以下命令编译和执行Hello World类：

```
> mvn clean compile exec:java
```

使用浏览器访问`http://localhost:8080`页面，你就可以看见HelloWorld页面了。你可以观察一下Maven到底干了什么，使用`mvn dependency:tree`命令，它会揭示传递依赖关系以及下载内容：

```
> mvn dependency:tree
[INFO] Scanning for projects...
...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building Jetty HelloWorld 0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO]
[INFO] --- maven-dependency-plugin:2.8:tree (default-cli) @ hello-world ---
...
[INFO] org.example:hello-world:jar:0.1-SNAPSHOT
[INFO] \- org.eclipse.jetty:jetty-server:jar:9.3.9.v20160517:compile
[INFO]    +- javax.servlet:javax.servlet-api:jar:3.1.0:compile
[INFO]    +- org.eclipse.jetty:jetty-http:jar:9.3.9.v20160517:compile
[INFO]    |  \- org.eclipse.jetty:jetty-util:jar:9.3.9.v20160517:compile
[INFO]    \- org.eclipse.jetty:jetty-io:jar:9.3.9.v20160517:compile
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 4.145 s
[INFO] Finished at: 2016-08-01T13:46:42-04:00
[INFO] Final Memory: 15M/209M
[INFO] ------------------------------------------------------------------------
```


[回到顶部](#top)
<br>

<span id="2321使用maven开发标准的wabapp"></span>
##### 23.1.2、使用Maven开发标准的WebApp

之前的部分演示了在应用中如何使用maven去做嵌入式Jetty。现在我们将研究如何使用Maven和Jetty开发一个标准的web应用，首先创建Maven标准web目录结构：
```
> mkdir JettyMavenHelloWarApp
> cd JettyMavenHelloWebApp
> mkdir -p src/main/java/org/example
> mkdir -p src/main/webapp/WEB-INF
```

<br>

###### 23.1.2.1、创建一个Servlet

路径`src/main/java/org/example/HelloServlet.java`：

```
package org.example;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class HelloServlet extends HttpServlet
{
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException
    {
        response.setContentType("text/html");
        response.setStatus(HttpServletResponse.SC_OK);
        response.getWriter().println("<h1>Hello Servlet</h1>");
        response.getWriter().println("session=" + request.getSession(true).getId());
    }
}
```

你需要在`web.xml`中声明这个servlet：

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app
   xmlns="http://xmlns.jcp.org/xml/ns/javaee"
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
   metadata-complete="false"
   version="3.1">

  <servlet>
    <servlet-name>Hello</servlet-name>
    <servlet-class>org.example.HelloServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>Hello</servlet-name>
    <url-pattern>/hello/*</url-pattern>
  </servlet-mapping>

</web-app>
```
<br>

###### 23.1.2.2、创建POM

特别注意里面有`jetty-maven-plugin`插件：
```
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

  <modelVersion>4.0.0</modelVersion>
  <groupId>org.example</groupId>
  <artifactId>hello-world</artifactId>
  <version>0.1-SNAPSHOT</version>
  <packaging>war</packaging>
  <name>Jetty HelloWorld WebApp</name>

  <properties>
      <jettyVersion>9.4.6.v20170531</jettyVersion>
  </properties>

  <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-maven-plugin</artifactId>
        <version>${jettyVersion}</version>
      </plugin>
    </plugins>
  </build>

</project>
```
<br>

###### 23.1.2.3、构建和运行Web项目

现在你可以同时构建和运行web应用，不需要把项目组装成一个war，你只需要使用`jetty-maven-plugin`提供的命令就可以了：
```
> mvn jetty:run
```
然后你可以在`http://localhost:8080/hello`地址中看到静态和动态的内容。

###### 23.1.2.4、创建一个war文件

你可以通过以下的命令，从项目中创建一个Web应用存档（Web Application Archive (WAR)）文件：
```
> mvn package
```
生产的war文件会在target目录，并且它可以部署在任何的标准servlet服务器，包括Jetty。

[回到顶部](#top)
- - -

<span id="232配置jetty的maven插件"></span>
#### 23.2、配置Jetty的Maven插件

- 23.2.1、[快速开始](#2321快速开始)
- 23.2.2、[支持目标](#2322支持目标)
- 23.2.3、[配置Jetty容器](#2323配置jetty容器)
- 23.2.4、[配置你的WebApp](#2324配置你的webapp)
- 23.2.5、[jetty:run](#2325jettyrun)
- 23.2.6、[jetty:run-war](#2326jettyrunwar)
- 23.2.7、[jetty:run-exploded](#2327jettyrunexploded)
- 23.2.8、[jetty:run-forked](#2328jettyrunforked)
- 23.2.9、[jetty:start](#2329jettystart)
- 23.2.10、[jetty:stop](#23210jettystop)
- 23.2.11、[jetty:effective-web-xml](#23211jettyeffectivewebxml)
- 23.2.12、[使用覆盖war](#23212使用覆盖war)
- 23.2.13、[配置Security](#23213配置security)
- 23.2.14、[使用多个WebappRoot目录](#23214使用多个webapproot目录)
- 23.2.15、[运行一个以上的Webapp](#23215运行一个以上的webapp)
- 23.2.16、[设置系统属性](#23216设置系统属性)

Jetty Maven插件插件对于快速开发和快速测试来说是非常有用的。你可以把它添加到任意一个webapp项目中，只要这个项目的结构符合Maven标准。插件会定期的扫描你的项目的变化，并且自动的重新部署项目。这让生产周期大大缩减，因为你不用做部署和构建的步骤：你只需要在IDE里面做出修改，然后运行的web容器会自动地重新部署这些修改，所以在这样的情况下，你可以非常直接地做测试工作。

> **重要：**
>
> 你需要Maven3.3以上才能支持这个插件。

虽然Jetty Maven插件可以非常有效地进行开发，但是我们并不推荐把它运用到生产环境。因为Maven插件的运作本身需要许多内置Maven API，并且Maven它本身并不是一个生产部署工具。我们还是推荐你使用传统的开发版部署方法或者使用嵌入式Jetty。

<br>

<span id="2321快速开始"></span>
##### 23.2.1、快速开始

首先，在pom中配置插件：
```
<plugin>
  <groupId>org.eclipse.jetty</groupId>
  <artifactId>jetty-maven-plugin</artifactId>
  <version>9.4.6.v20170531</version>
</plugin>
```
然后在和pom同级的目录下，执行以下命令：
```
mvn jetty:run
```
然后你可以在`http://localhost:8080/`访问你的项目。

Jetty会一直执行下去直到你停止它。当它在运行的时候，它会定期扫描你项目文件是否有变动，如果文件有变动并且重编译了成class文件的话，Jetty会重新部署你的项目，然后你可以直接测试你刚才的变更结果。

你可以在终端窗口使用`ctrl-c`热键来终止这个插件的运行。

> **注意：**
>
> 运行Jetty实例的类路径和它下面部署的web应用都是通过Maven来管理的，这可能和你期待的有点不同。比如说：一个web应用的依赖jar可能会引用本地maven仓库中的版本，而不是WEB-INF/lib目录下的jar包。

[回到顶部](#top)
<br>

<span id="2322支持目标"></span>
##### 23.2.2、支持目标

Jetty Maven插件有大量的Maven目标。这其中最有用的目标就是run目标，它可以运行一个未装配的webapp。还有其它的目标，可以帮助你完成不同的任务。比如说，你可能需要在另外一个Jetty的实例上运行你的webapp而不是就在当前的maven进程里面运行；或者你可能需要在你想部署的webapp上，对项目的生命周期的各个阶段进行更细致粒度的控制。

Maven中有很多目标都可以完成这些任务，你可以通过命令来查看Jetty Maven插件支持哪些目标：
```
mvn jetty:help
```

为了看到更多详细信息，你可以传入特定目标的名字：
```
mvn jetty:help -Ddetail=true -Dgoal= <goal name>
```
[回到顶部](#top)
<br>

<span id="2323配置jetty容器"></span>
##### 23.2.3、配置Jetty容器

- httpConnector
  可选项。如果没有指定，Jetty会创建一个`ServerConnector`实例来监听8080端口。你可以通过命令行在启动的时候更改这些默认的设置，比如说`mvn -Djetty.http.port=9999 jetty:run`。除此之外，你可以使用配置对象来设置`ServerConnector`信息。以下是可配置的子对象：
  - port：
    - 可修改的端口号，默认是8080。
  - host：
    - 特定的connector接口，默认是所有接口。
  - name：
    - connector的名字。
  - idleTimeout：
    - 最大等待连接时间。
  - soLinger：
    - socket的延迟时间。

示例：
```
<plugin>
    <groupId>org.eclipse.jetty</groupId>
    <artifactId>jetty-maven-plugin</artifactId>
    <version>${jetty-version}</version>
    <configuration>
        <httpConnector>
            <port>8888</port>
        </httpConnector>
    </configuration>
</plugin>
```

<br>

- jettyXml
  可选项。这里可以配置你需要加载的jetty的xml文件，需要用逗号分开。

示例：
```
<plugin>
  <groupId>org.eclipse.jetty</groupId>
  <artifactId>jetty-maven-plugin</artifactId>
  <version>9.4.6.v20170531</version>
  <configuration>
    <jettyXml>jetty.xml,jetty-ssl.xml,jetty-https.xml</jettyXml>
  </configuration>
</plugin>
```

<br>

- scanIntervalSeconds
  扫描和检查webapp变更的时间间隔，这可以用来做自动的热部署。默认情况下为0，意思是不开启热部署，取一个大于1 的值就开启了热部署。

<br>

- reload
  默认值是“automatic”，也就是自动的，只要你配置了一个非0的`scanIntervalSeconds`。你可以设置为“manual”，也就是手动的，这时你需要手动触发扫描，你得在插件运行的情况下在控制台键入一个换行键。这对于你在做一些重要的变更的时候比较有用。

<br>

- dumpOnStart
  可选项。默认是false。如果为true，Jetty会在启动的时候转储出服务器结构。

<br>

> *译者文外补充：后面的部分我感觉配置不上，也就不翻译了。*

- loginServices
  Optional. A list of org.eclipse.jetty.security.LoginService implementations. Note that there is no default realm. If you use a realm in your web.xml you can specify a corresponding realm here. You could instead configure the login services in a jetty xml file and add its location to the jettyXml parameter.

<br>

- requestLog
  Optional. An implementation of the org.eclipse.jetty.server.RequestLog request log interface. An implementation that respects the NCSA format is available as org.eclipse.jetty.server.NCSARequestLog. There are three other ways to configure the RequestLog:

  - In a jetty xml config file, as specified in the jettyXml parameter.
  - In a context xml config file, as specified in the contextXml parameter.
  - In the webApp element.
  See Configuring Request Logs for more information.

<br>

- server
  Optional as of Jetty 9.3.1. This would configure an instance of the org.eclipse.jetty.server.Server for the plugin to use, however it is usually NOT necessary to configure this, as the plugin will automatically configure one for you. In particular, if you use the jettyXml element, then you generally DON’T want to define this element, as you are probably using the jettyXml file to configure up a Server with a special constructor argument, such as a custom threadpool. If you define both a server element AND use a jetty xml element which points to a config file that has a line like <Configure id="Server" class="org.eclipse.jetty.server.Server"> then the the xml configuration will override what you configure for the server in the pom.xml.

<br>

- stopPort
  Optional. Port to listen on for stop commands. Useful to use in conjunction with the stop or run-forked goals.

<br>

- stopKey
  Optional. Used in conjunction with stopPort for stopping jetty. Useful when used in conjunction with the stop or run-forked goals.

<br>

- systemProperties
  Optional. Allows you to configure System properties for the execution of the plugin. For more information, see Setting System Properties.

<br>

- systemPropertiesFile
  Optional. A file containing System properties to set for the execution of the plugin. By default, settings that you make here do not override any system properties already set on the command line, by the JVM, or in the POM via systemProperties. Read Setting System Properties for how to force overrides.

<br>

- skip
  Default is false. If true, the execution of the plugin exits. Same as setting the SystemProperty -Djetty.skip on the command line. This is most useful when configuring Jetty for execution during integration testing and you want to skip the tests.

<br>

- useProvidedScope
  Default value is false. If true, the dependencies with <scope>provided</scope> are placed onto the container classpath. Be aware that this is NOT the webapp classpath, as "provided" indicates that these dependencies would normally be expected to be provided by the container. You should very rarely ever need to use this. Instead, you should copy the provided dependencies as explicit dependencies of the plugin instead.

<br>

- excludedGoals
  Optional. A list of Jetty plugin goal names that will cause the plugin to print an informative message and exit. Useful if you want to prevent users from executing goals that you know cannot work with your project.

<br>

- - -

<span id="233jetty-maven插件的文档扫描"></span>
#### 23.3、Jetty Maven插件的文件扫描

[回到顶部](#top)
- - -

<span id="234jetty-jspc-maven插件"></span>
#### 23.4、Jetty Jspc Maven插件

[回到顶部](#top)
- - -
