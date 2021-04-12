---
layout: default
title: CAS - 覆盖安装
category: 安装
---

# WAR 覆盖安装

CAS安装是从根本上来说是面向源代码的过程，我们建议您使用WAR overlay（1）项目来组织自定义设置，例如组件配置和UI设计。 WAR覆盖最终会构建输出是一个 `cas.war` 文件，可以将其部署到servlet容器（如 [Apache Tomcat](Configuring-Servlet-Container.html)。

## 安装要求

[请参阅本指南](../planning/Installation-Requirements.html) 了解更多信息。

## 什么是WAR覆盖

覆盖是一种对抗重复代码和/或资源的策略。 与下载CAS代码库并从源代码进行构建不同，WAR叠加允许您下载项目本身提供的预构建的普通CAS web应用程序服务器，并覆盖/插入特定的行为。 在构建时，构建安装过程将首先尝试下载提供的二进制工件。 然后，该工具将在相同的项目目录中找到您的配置文件和可用的设置，并将它们合并到下载的工件中，以便生成一个完整的存档(即`cas.war`)。 覆盖的工件可能包括资源、java类、图像、CSS和javascript文件。 为了使合并进程成功执行，被覆盖的工件的位置和名称必须与该项目提供原始下载的存档位置和名称进行 **精确地** 的匹配，。 在覆盖层项目中的Java代码目录 `src/main/java` 目录和资源文件目录`src/main/resources` 最终将会出现在cas.war的`WEB-INF\classes` 目录中，它们将被类加载器加载，并替换jar文件中`WEB-INF\lib`下的同名资源。

毋庸置疑，尽管前期准备时间可能会有些复杂，但这种方法有很多优势：

1. 无需从源代码下载/构建。
2. 在大多数情况下，只需调整构建脚本以下载较新的CAS版本，升级就变得非常容易。
3. 不需要托管整个软件源代码，作为部署者，您 **仅** 将保留自己的本地自定义项，这使更改跟踪变得更加容易。
4. 同样，仅因为仅管理相关的更改（而不是整个软件），所以用于跟踪源代码并进行版本控制的代码仓库将非常轻巧。

### 管理覆盖

CAS的大部分（如果不是全部）方面都可以通过添加，删除或修改覆盖层中的文件来控制；通过添加第三方组件如实现CAS APIs的Java源文件或依赖项来改变 CAS的行为是可能的，而且的确是常见的。

覆盖的处理过程可以归纳为以下步骤：

- 从提供的基础原版组件开始构建/部署。
- 从构建生成的组件中识别需要更改的工件。 这些工件通常由 Gradel构建任务生成在`build` 目录中。 使用gradle `explodeWar` 任务。
- 将已标识的组件从上面标识的目录复制到 `src/main/resources` 目录。
1. 创建 `src/main/resources` 目录，如果它们不存在。
2. 已复制的路径和文件名 **必须是他们的构建对应方** ，否则更改不会生效。 请参阅下表的表格以了解如何将文件夹和文件从内部版本映射到 `src`。
- 更改后，请重新构建并重复该过程，以尽完成你需要的定制。
- 在构建的二进制工件中仔细检查您的更改，以确保覆盖过程正常完成。

<div class="alert alert-warning"><strong>准确无误</strong><p>不要复制构建产生的所有内容。 尽量减少更改和定制，并且抓住你的实际需求。 确保部署环境保持整洁精确，否则将带来可怕的升级问题和痛苦的麻烦。</p></div>

## CAS WAR 覆盖

下文介绍的CAS WAR覆盖项目供参考和研究。

### CAS覆盖初始化

Apereo CAS Initializr是Apereo CAS生态系统中的一个相对较新的功能，允许你作为部署者，在你需要快速启动的情况下，快速生成CAS WAR覆盖项目

要了解更多关于`initializr`的信息，请[查看本指南](WAR-Overlay-Initializr.html)。

### CAS覆盖模板

您可以下载或克隆以下存储库，以开始使用CAS覆盖模板。

<div class="alert alert-info"><strong>请注意你使用的分支！</strong><p>下面的代码仓库将您引向其 <code>master</code> 分支。
您应该始终确保您所在的分支与您要配置和部署的CAS版本匹配。 <code>master</code>分支通常与CAS服务器的最新稳定版本相匹配。 检查你的构建配置，如果发现不恰当的使用了错误的分支，请使用<code>git branch -a</code> 查看可用的分支，然后使用 <code>git checkout [branch-name]</code> 切换。</p></div>

| 项目                                                                | 构建目录                      | 源目录                  |
| ----------------------------------------------------------------- | ------------------------- | -------------------- |
| [CAS WAR Overlay](https://github.com/apereo/cas-overlay-template) | `cas/build/cas-resources` | `src/main/resources` |

其中在覆盖项目中的`cas/build/cas-resources`文件是由`gradle explodeWar`任务从`cas.war!WEB-INF\lib\cas-server-webapp-resources-<version>.jar`中解压而来。

为了组织你的覆盖项目，你需要从构建目录复制*你需要定制*的文件和目录到源代码目录。

WAR叠加层还提供了其他任务，以便在重新组装之前先展开二进制工件。 具体要将那些文件或者目录拷贝到你的源代码目录你可能需要自己手动操作。

注意：请**不要**在上面提到的构建目录中进行任何修改。 每次构建时，构建目录的变更将被清除，并将恢复为默认值。 将覆盖组件放在源目录和/或其他指定位置，以避免修改丢失。

## CAS配置服务器覆盖

有关更多详细信息， [Maven WAR覆盖](https://github.com/apereo/cas-configserver-overlay)

要了解有关配置服务器的更多信息，请 [本指南](../configuration/Configuration-Server-Management.html)。

## 在Docker中部署

有关更多信息，请参见 [本指南](Docker-Installation.html)

## Servlet容器

CAS可以部署到许多servlet容器中。 有关更多信息，请参见 [本指南](Configuring-Servlet-Container.html)

## 自定义和第三方源代码

通常，通过开发实现CAS API的Java组件来定制或扩展CAS的功能，或者通过依赖引入第三方源代码。 引入第三方源码通常是比较容易的；只需在`build.gradle` 文件中添加相关的依赖项即可。 

<div class="alert alert-warning"><strong>停止编码</strong><p>
覆盖或修改CAS 内部组件和类通常被认为是错误的做法， 除非<i>绝对</i>需要， 否则不要这样做。 尽可能避免进行自定义更改，这些修改需要你后期独自进行维护。 
缺少维护的修改， 将使您的部署存在稳定和安全性的风险。 如果你的修改是普适或者适当的，请把它回馈到本项目中来。 停止编写代码，或将其归类。
</p></div>

应该在覆盖项目的`src/main/java` 目录下存放自定义Java源代码。

    ├── src
    │   ├── main
    │   │   ├── java
    │   │   │   └── edu
    │   │   │       └── sso
    │   │   │           └── middleware
    │   │   │               └── cas
    │   │   │                   ├── audit
    │   │   │                   │   ├── CompactSlf4jAuditTrailManager.java
    │   │   │                   ├── authentication
    │   │   │                   │   └── principal
    │   │   │                   │       └── UsernamePasswordCredentialsToPrincipalResolver.java
    │   │   │                   ├── services
    │   │   │                   │   └── JsonServiceRegistryDao.java
    │   │   │                   ├── util
    │   │   │                   │   └── X509Helper.java
    │   │   │                   └── web
    │   │   │                       ├── HelpController.java
    │   │   │                       ├── flow
    │   │   │                       │   ├── AbstractForgottenCredentialAction.java
    │   │   │                       └── util
    │   │   │                           ├── ProtocolParameterAuthority.java

## 依赖管理

每个CAS版本都有自己的特定依赖列表。 实践中， 您不需要在构建配置中为这些依赖指定具体的版本，因为CAS 会自动管理这些依赖关系。 当您升级CAS时，这些依赖项也将以一致的方式进行升级。

这些依赖列表中包含选定的第三方库列表。 该列表作为标准物料清单（BOM）提供。 并非每个人都喜欢从BOM表继承。 您可能需要使用自己的公司标准父级，也可能只想明确声明所有配置。

在使用Gradle的情况，如果你需要了解CAS BOM （`org.apereo.cas:cas-server-support-bom`）的高级用法，请 [使用本指南](https://plugins.gradle.org/plugin/io.spring.dependency-management)，并对Gradle构建进行相应的配置。

<sub>（1）[WAR覆盖]（http://maven.apache.org/plugins/maven-war-plugin/overlays.html）</sub>
