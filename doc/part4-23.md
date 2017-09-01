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

Maven里的Jetty插件对于快速开发和快速测试来说是非常有用的。你可以把它添加到任意一个webapp项目中，只要这个项目的结构符合Maven标准。插件会定期的扫描你的项目的变化，并且自动的重新部署项目。这让生产周期大大缩减，因为你不用做部署和构建的步骤：你只需要在IDE里面做出修改，然后运行的web容器会自动地重新部署这些修改，所以在这样的情况下，你可以非常直接地做测试工作。

> **重要：**
>
> 你需要Maven3.3以上才能支持这个插件。

虽然Maven的Jetty插件可以非常有效地进行开发，但是我们并不推荐把它运用到生产环境。因为Maven插件的运作本身需要许多内置Maven API，并且Maven它本身并不是一个生产部署工具。我们还是推荐你使用传统的开发版部署方法或者使用嵌入式Jetty。

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
- - -

<span id="233jetty-maven插件的文档扫描"></span>
#### 23.3、Jetty Maven插件的文件扫描

[回到顶部](#top)
- - -

<span id="234jetty-jspc-maven插件"></span>
#### 23.4、Jetty Jspc Maven插件

[回到顶部](#top)
- - -
