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

Note that CAS itself ships with a number of embedded containers that allow the platform to be self-contained as much as possible. These embedded containers are an integral part of the CAS software, are maintained and updated usually for every release and surely are meant to and can be used in production deployments. You **DO NOT** need to, but can if you want to, configure and deploy to an externally configured container. 

<div class="alert alert-info"><strong>提示</strong><p>Remember that most if not all aspects of the embedded 
container can be controlled via the CAS properties. 有关更多信息，请参见 <a href="../configuration/Configuration-Properties.html#embedded-apache-tomcat-container">本指南</a></p></div>

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#embedded-container).

### Execution

The CAS web application, once built, may be deployed in place with the embedded container via the following command:

```bash
java -jar /path/to/cas.war
```

Additionally, it is also possible to run CAS as a fully executable web application:

```bash
# chmod +x /path/to/cas.war
/path/to/cas.war
```

This is achieved via the build process of the deployment overlay where a launch script is *inserted* at the beginning of the web application artifact. If you wish to see and examine the script, simply run the following commands:

 ```bash
 # X is the number of lines from the beginning of the file
 head -n X /path/to.cas.war
 ```

Note that running CAS as a standalone and fully executable web application is supported on most Linux and OS X distributions. Other platforms such as Windows may require custom configuration.

### Apache Tomcat

Note that by default, the embedded container attempts to enable the HTTP2 protocol.

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-webapp-tomcat</artifactId>
     <version>${cas.version}</version>
</dependency>
```

#### IPv4 Configuration

In order to force Apache Tomcat to use IPv4, configure the following as a system property for your *run* command:

```bash
-Djava.net.preferIPv4Stack=true 
```

The same sort of configuration needs to be applied to your `$CATALINA_OPTS` environment variable in case of an external container.

#### Faster Startup

[This guide](https://cwiki.apache.org/confluence/display/TOMCAT/HowTo+FasterStartUp) provides several recommendations on how to make web applications and Apache Tomcat as a whole to start up faster.

#### 日志

The embedded Apache Tomcat container is presently unable to display any log messages below `INFO` even if your CAS log configuration explicitly asks for `DEBUG` or `TRACE` level data. See [this bug report](https://github.com/spring-projects/spring-boot/issues/2923) to learn more.

While workarounds and fixes may become available in the future, for the time being, you may execute the following changes to get `DEBUG` level log data from the embedded Apache Tomcat. This is specially useful if you are troubleshooting the behavior of Tomcat's internal components such as valves, etc.

- Design a `logging.properties` file as such:

```properties
handlers = java.util.logging.ConsoleHandler
.level = ALL
java.util.logging.ConsoleHandler.level = FINER
java.util.logging.ConsoleHandler.formatter = java.util.logging.SimpleFormatter
```

- Design a`java.util.logging.config.file` setting as a system/environment variable or command-line argument whose value is set to the `logging.properties` path. Use the setting when you launch and deploy CAS.

For instance:

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

## External

A CAS deployment may be deployed to any number of external servlet containers. The container **MUST** support the servlet specification `v4.0.0` at a minimum. In these scenarios, the following vanilla CAS web application may be used, in the [WAR Overlay](WAR-Overlay-Installation.html) :

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-webapp</artifactId>
     <version>${cas.version}</version>
</dependency>
```

While there is no official project support, the following containers should be compatible with a CAS deployment:

* [Apache Tomcat](http://tomcat.apache.org/) (At a minimum, Apache Tomcat 9 is required)
* [JBoss](http://www.jboss.org/)
* [Wildfly](http://wildfly.org/)
* [Undertow](http://undertow.io/)
* [Jetty](http://www.eclipse.org/jetty/) (At a minimum, Jetty 9.4 is required)
* [GlassFish](http://glassfish.java.net/)
* [WebSphere](http://www.ibm.com/software/websphere/)

Remember that an external container's configuration is **NEVER** automated by CAS in any way which means you are responsible for upgrades, maintenance and all other manners of configuration such as logging, SSL, etc. CAS does not provide official support and troubleshooting guidelines, etc for an external container's configuration or issues. Refer to the servlet container's own documentation for more info.

Note for JBoss, Wildfly and EAP, you may need to add a `jboss-deloyment-structure.xml` file to `src/main/webapp/WEB-INF` in your overlay in order for CAS to start properly.

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

Support for external containers is enabled by including the following module in the overlay:

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-webapp</artifactId>
     <version>${cas.version}</version>
</dependency>
```

### Async Support

In the event that an external servlet container is used, you MAY need to make sure it's configured correctly to support asynchronous requests in the event you get related errors and your container requires this. This is typically handled by setting `<async-supported>true</async-supported>` inside the container's main `web.xml`  
file (i.e. For Apache Tomcat, that would be `$CATALINA_HOME/conf/web.xml`).

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

### Async Logging

CAS logging automatically inserts itself into the runtime application context and will clean up the logging context once the container is instructed to shut down. However, Apache Tomcat in particular seems to by default ignore all JAR files named `log4j*.jar`, which prevents this feature from working. You may need to change the `catalina.properties` and remove `log4j*.jar` from the `jarsToSkip` property. Failure to do so will prevent the container to gracefully shut down and causes logger context threads to hang.

You may need to do something similar on other containers if they skip scanning Log4j JAR files.

## Docker

You may also be interested to deploy CAS via [Docker](https://www.docker.com/). 有关更多信息，请参见 [本指南](Docker-Installation.html)

## System Service

CAS can be easily started as Unix/Linux services using either `init.d` or `systemd`. To learn more, please [visit this guide](Configuring-Deployment-System-Service.html).
