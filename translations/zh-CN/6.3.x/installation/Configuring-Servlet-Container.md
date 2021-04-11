---
layout: default
title: CAS - 覆盖安装
category: 安装
---

# Servlet 容器配置

有许多可选的容器可用于部署CAS。 [WAR 覆盖](WAR-Overlay-Installation.html) 指南描述了如何构建和部署CAS。

## 我该如何选择？

我可以选择的Servlet容器和服务器范围比较广。 选择标准概述如下：

- 选择您最熟悉的技术，并具有排除故障、调整和扩展以赢得胜利的技能和耐心。
- 选择一种不会强制将CAS配置绑定到群集中任何单个服务器/节点的技术，因为这会带来自动扩展问题和手动工作。
- 选择一种适合您的网络和防火墙配置，并且根据您的网络拓扑具有足够性能和可靠性的技术。
- 选择一种技术，该技术在 *预期负载*下有比较好的结果，并已进行了 [性能和压力测试](../high_availability/High-Availability-Performance-Testing.html)。
- 选择一种尽可能不依赖外部流程，系统和人工工作，自力更生且自成体系的技术。

## 生产质量

此处介绍的所有servlet容器（嵌入式或其他方式）都是生产就绪的。 这意味着CAS开箱即用的有用的默认设置，虽软部分值可能会被覆盖，默认情况下如果有必要，CAS现在将会为您配置从开发到生产的所有东西。 就生产质量而言，使用嵌入式容器与使用外部容器几乎没有区别。

除非有特定的，技术上的和合理的反对意见，否则选择嵌入式servlet容器几乎总是更好的选择。

## 嵌入式

请注意，CAS本身附带了许多嵌入式容器，并且尽可能的使这些容器可以独立使用。 这些嵌入式容器是CAS软件不可或缺的一部分，通常每个版本都会对这部分组件进行维护和升级，并旨在保证嵌入式容器可以在生产环境使用。 一般情况下你 **不需要**配置和部署到外部容器，当然果你想要部署到外部容器，这些事可以的。 

<div class="alert alert-info"><strong>提示</strong><p>请记住，嵌入的容器的大部分甚至全部配置都可以通过 CAS 属性来控制。 有关更多信息，请参见 <a href="../configuration/Configuration-Properties.html#embedded-apache-tomcat-container">本指南</a></p></div>

要查看CAS与之相关属性设置，请 [查阅本指南](../configuration/Configuration-Properties.html#embedded-container)。

### 执行

一旦构建了CAS Web应用程序，就可以通过以下命令将其与嵌入式容器一起部署：

```bash
java -jar /path/to/cas.war
```

此外，还可以将CAS作为完全可执行的Web应用程序运行：

```bash
# chmod +x /path/to/cas.war
/path/to/cas.war
```

这是`Cas覆盖`在构建过程中通过向Web应用程序包中*插入*启动脚本实现的。 如果您想查看和检查这个脚本，只需运行以下命令：

 ```bash
 # X is the number of lines from the beginning of the file
 head -n X /path/to.cas.war
 ```

请注意，大多数Linux和OS X发行版都支持将CAS作为独立且完全可执行的Web应用程序运行。 其他平台，例如Windows可能需要额外的配置。

### Apache Tomcat

请注意，默认情况下，嵌入式容器会尝试启用HTTP2协议。

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-webapp-tomcat</artifactId>
     <version>${cas.version}</version>
</dependency>
```

#### IPv4配置

为了强制Apache Tomcat使用IPv4，请将以下内容配置为 *运行* 命令的系统属性：

```bash
-Djava.net.preferIPv4Stack=true 
```

如果是外部容器，则需要使用类似的配置应用于 `$CATALINA_OPTS`指定的配置文件

#### 更快的启动

[本指南](https://cwiki.apache.org/confluence/display/TOMCAT/HowTo+FasterStartUp)提供了一些关于如何更快地启动Web应用程序和整个Apache Tomcat的建议。

#### 日志

嵌入的 Apache Tomcat 容器目前无法显示 `INFO`或者更低优先级的日志 ，即使您的 CAS 日志配置明确要求显示 `DEBUG` 或 `TRACE` 级别数据。 请参阅 [此错误报告](https://github.com/spring-projects/spring-boot/issues/2923) 以了解更多信息。

尽管将来可能会出现变通或者修复的方法，但在这之前，你可用通过已下修改来获取嵌入式Tomcat的 `DEBUG` 级别的日志。 如果要对Tomcat的内部组件（例如valves等）进行故障诊断，这特别有用。

- 参照如下代码配置一个`logging.properties` 文件：

```properties
handlers = java.util.logging.ConsoleHandler
.level = ALL
java.util.logging.ConsoleHandler.level = FINER
java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter
```

- 添加一个`java.util.logging.config.file`设置作为你的系统变量或者命令行参数，并将其值设置为你的`logging.properties` 路径 并在启动和部署CAS时使用该设置。

就像这样：

```bash
java -jar /path/to/cas.war -Djava.util.logging.config.file=/path/to/logging.properties
```

### Jetty

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-webapp-jetty</artifactId>
     <version>${cas.version}</version>
</dependency>
```

### Undertow

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-webapp-undertow</artifactId>
     <version>${cas.version}</version>
</dependency>
```

## 外部容器

CAS部署可以部署到多种外部servlet容器。 容器 **必须** 至少支持servlet规范 `v4.0.0`。 在这些情况下，可以在 [WAR叠加](WAR-Overlay-Installation.html) 中添加以下普通CAS Web应用程序依赖：

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-webapp</artifactId>
     <version>${cas.version}</version>
</dependency>
```

虽然没有官方项目支持，但以下容器应与CAS部署兼容：

* [Apache Tomcat](http://tomcat.apache.org/) （至少需要Apache Tomcat 9）
* [JBoss](http://www.jboss.org/)
* [Wildfly](http://wildfly.org/)
* [Undertow](http://undertow.io/)
* [Jetty](http://www.eclipse.org/jetty/) (至少需要 Jetty 9.4)
* [GlassFish](http://glassfish.java.net/)
* [WebSphere](http://www.ibm.com/software/websphere/)

请记住，CAS**绝不**会自动对外部容器的配置进行配置，这意味着需要您负责升级，维护容器以及所有其他配置，例如日志记录，SSL等。 CAS不为外部容器提技术支持或故障排除方案，例如：外部容器的配置或者问题等。 有关这方面的信息，请参阅servlet容器自己的文档。

对于JBoss，Wildfly和EAP，请注意，您需要将 `jboss-deloyment-structure.xml` 文件添加到 `src/main/webapp/WEB-INF` 中，以使CAS正常启动。

```xml
<jboss-deployment-structure>
    <deployment>
        <dependencies>
            <module name="jdk.unsupported" />
        </dependencies>
    </deployment>
</jboss-deployment-structure>
```

### 配置

通过在叠加层中包含以下模块来启用对外部容器的支持：

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-webapp</artifactId>
     <version>${cas.version}</version>
</dependency>
```

### 异步支持

如果使用了外部servlet容器，如果你遇到相关错误，那么你需要正确的将容器配置为支持异步请求。 通常通过在容器配置文件 `web.xml`  
中的 `<async-supported>true</async-supported>`进行配置 （对于Apache Tomcat，需要配置 `$CATALINA_HOME/conf/web.xml`）。

### 日志

When using an external container, you may need to ensure that logging configuration file that ships with CAS by default is disabled and turned into a no-op **specially** if the log configuration and location is to be controlled via CAS settings. This is required because initialization of the CAS web applications context inside an external servlet container tends to prematurely initialize the log configuration from classpath before CAS itself has had a chance to control logging via settings.

To disable CAS' own logging, define a `log4j2.xml` under `src/main/resources` and put the following content in it:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Configuration>
    <Appenders>
        <Console name="console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d %p [%c] - &lt;%m&gt;%n" />
        </Console>
    </Appenders>
    <Loggers>
        <AsyncRoot level="off">
            <AppenderRef ref="console"/>
        </AsyncRoot>
    </Loggers>
</Configuration>
```

The above configuration will turn the logging initialization moot, allowing the location and configuration of logs to be defined via CAS settings.

### 异步日志记录

CAS logging automatically inserts itself into the runtime application context and will clean up the logging context once the container is instructed to shut down. However, Apache Tomcat in particular seems to by default ignore all JAR files named `log4j*.jar`, which prevents this feature from working. You may need to change the `catalina.properties` and remove `log4j*.jar` from the `jarsToSkip` property. Failure to do so will prevent the container to gracefully shut down and causes logger context threads to hang.

You may need to do something similar on other containers if they skip scanning Log4j JAR files.

## Docker

You may also be interested to deploy CAS via [Docker](https://www.docker.com/). 有关更多信息，请参见 [本指南](Docker-Installation.html)

## 系统服务

CAS can be easily started as Unix/Linux services using either `init.d` or `systemd`. To learn more, please [visit this guide](Configuring-Deployment-System-Service.html).
