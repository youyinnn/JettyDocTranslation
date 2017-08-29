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
##### 13.1.1、关于Jetty发行版

如果你是使用的Jetty发行版，那么注解是默认支持的。注解模块以及它所需要的过渡的依赖都是负责支持注解功能的。

你需要注意的是注解功能依赖JNDI，比如`@Resource`和`@Resources`都是通过JNDI模块来实现的，JNDI模块是注解模块的过渡依赖模块。

> *译者文外补充：Jetty发行版就是Jetty的客户端，与之相对的有Jetty嵌入式的方式。*

<br>

<span id="1312关于jetty-maven-plugin"></span>
##### 13.1.2、关于Jetty Maven Plugin

注解和JNDI在Jetty Maven Plugin中都是预置的。

<br>

<span id="1313关于嵌入式"></span>
##### 13.1.3、关于嵌入式
