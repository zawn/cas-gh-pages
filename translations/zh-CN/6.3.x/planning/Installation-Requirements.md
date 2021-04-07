---
layout: default
title: CAS-安装要求
category: 安装
---

# 安装要求

根据配置组件的选择，可能需要额外的需求，例如LDAP目录，数据库和缓存基础设施。 在大多数情况下，对于具有明确的硬件和软件依赖性的组件部署者来说，要求应该是显而易见的。 在任何情况下，附加要求都为并不明显，有关组件配置的讨论应提及系统，软件，硬件和其他要求。

## Java

CAS的核心是基于Java的Web应用程序。 在部署之前，您需要先安装 [JDK](https://openjdk.java.net/projects/jdk/11/) `11`。

<div class="alert alert-danger"><strong>Oracle JDK许可证</strong><p>
Oracle更新了Oracle JDK的许可证条款。 新的Oracle Technology Network License Agreement for Oracle Java SE 与以前提供JDK 版本的许可证有很大不同。 <b>请在下载和使用此产品之前仔细审阅</b> 新条款。</p></div>

许可证的关键部分如下：

> 您不得：将本程序用于开发，测试，制作原型和演示应用程序之外的任何数据处理或任何商业，生产或内部业务目的。

除非您打算为此付费，否则请执行 **NOT** **改用OpenJDK构建。**

## Servlet容器

CAS没有官方指定的servlet容器，但 [Apache Tomcat](http://tomcat.apache.org/) 是最常用的。 对特定servlet容器的支持取决于社区成员的专业知识。

有关更多信息，请参见 [本指南](../installation/Configuring-Servlet-Container.html)

## 构建工具

WAR叠加为部署者 [提供](../installation/WAR-Overlay-Installation.html)了以简单，灵活的部署解决方案。 尽管它也需要很高的前期学习成本，但从长远来看，它可以带来许多好处。 

<div class="alert alert-info"><strong>提示</strong><p>
您 <b>不需要</b> 在事先Gradle。 它是自动提供给您的。
</p></div>

## Git（可选）

虽然严格来说不是一项要求，但它建议您安装 [Git](https://git-scm.com/downloads) 来进行您的 CAS 部署。并在源码仓库中管理所有 CAS 组件、配置、 构建脚本和设置。

## 操作系统

尽管基于Linux的安装通常比Windows更普遍，但对操作系统没有特别的偏好。

## 互联网连接

任何基于 Maven/Gradle 的项目的构建阶段，包括 推荐用于安装 CAS 的 WAR 叠加，一般都需要互联网连接。 构建过程通过搜索本地下载和安装的在线存储库 来解析依赖关系，这里面包含一些已下载并安装的组件(大多数情况下为jar 文件)。

## 硬件

社区上的传闻似乎表明，CAS部署平台应最低双核3.00Ghz和至少8GB的内存。 如果日志保存在服务器本身上，则还需要足够的磁盘空间(最好是SSD) 来容纳CAS生成的日志。

请记住，上述要求是 *建议*。 满足你要求的配置可能或多或少，具体还要取决于您的部署和请求数量。 从最低限度开始，并准备根据需要调整和增强容量。
