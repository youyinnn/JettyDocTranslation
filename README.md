# Jetty : Servlet Engine and HTTP server
创立时间：2017年8月25日16:13:49

官方文档地址：http://www.eclipse.org/jetty/documentation/9.4.6.v20170531/
- - -
Maven依赖：
```
<properties>
    <jetty-version>9.4.6.v20170531</jetty-version>
</properties>

<dependencies>

    <!-- jetty-server部分 -->
    <dependency>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-server</artifactId>
        <version>${jetty-version}</version>
    </dependency>

    <!-- jetty-servlet部分 -->
    <dependency>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-servlet</artifactId>
        <version>${jetty-version}</version>
    </dependency>

    <!-- jetty-webapp部分 -->
    <dependency>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-webapp</artifactId>
        <version>${jetty-version}</version>
    </dependency>

    <!-- jetty-util部分 -->
    <dependency>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-util</artifactId>
        <version>${jetty-version}</version>
    </dependency>

    <!-- jetty-security部分 -->
    <dependency>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-security</artifactId>
        <version>${jetty-version}</version>
    </dependency>

</dependencies>
```
注意：以上依赖并不是全部需要，可以按需导入
- - -
#### 前言
> 这是一篇针对Jetty的官方英文文档翻译，对应版本号为：9.4.6.v20170531。
>
> 本着高尚的自学精神，再加上暑假有点无聊，我尝试着翻译Jetty的官方文档。
>
> 该翻译并不是来自于官方的，仅仅是自学用的翻译，如有错误，请再issue中指出。
>
> 文档并没有严格按照语法规范翻译，首先遵从本人自己的理解（如有理解错误也请在issue中指出），再尽量翻译成通俗的语句，要求的是能根据本文快速掌握Jetty。一切以实践为标准，我在学习的时候也会先实践，再结合实践翻译到文档中。
>
> 按照预期计划，我并不打算翻译全部的官方文档，仅仅满足我本身的开发需求足矣。一是本人知识水平有限，二是本身学习Jetty就是我当下的需求之一，我只是按需学习，再顺便做一些有趣的事情。
>
> 博客园平台也有一位前辈“已往之不谏”，他在16年的时候也在他的博客中发布了部分的文档翻译（当时版本：9.3.11.v20160721)，这里是[地址](http://www.cnblogs.com/yiwangzhibujian/p/5832294.html)，我在开启这个计划之前也和前辈联系过，他表示他翻译的章节已经满足他当时的开发需求，也满足了大部分Jetty开发者的需求，所以就没有继续翻译下去。他也不建议我开启翻译计划，本身翻译就是一件特别枯燥的事情，我也非常认同这一点。
>
> 在和前辈联系过之后，本来我已经取消了这个翻译计划了，但是在照着前辈的博客学习的时候，我发现博客里面有部分翻译有不足的地方，而且我也有一些更多的东西想表达出来，于是没有思考多久，我就重启了这个翻译计划。
>
> 浏览了以下百度的、谷歌的搜索前10的Jetty相关教程文章，有的要么太旧、已经不能运用到实际，有的要么东一块西一块，不明所以，甚至还有一篇《Jett6指南书》，可惜最后也搁浅了，国外也没有相关书籍，国内就更加没有了。这样看来Jetty的学习资料还是挺稀缺的。
>
> 大致看了下Jetty官方的目录，讲的太泛、太宽，入门章节就提到了它的许多feature，但是并没有好好介绍它的核心feature——“嵌入式开发”，这对新手来说太不友好了，但是回过头来想想，也许Jetty的定位就不是给新手使用的呢？
>
> 我之前也有动手翻译过一篇关于六年前的Java scheduler工具类Cron4j的官方文档，[传送门](https://github.com/youyinnn/Cron4jTranslation)。
- - -



翻译进度：

> 第一阶段
>
> 时间：
>
> 内容：

> 第二阶段
>
> 时间：
>
> 内容：

> 第三阶段
>
> 时间：
>
> 内容：
