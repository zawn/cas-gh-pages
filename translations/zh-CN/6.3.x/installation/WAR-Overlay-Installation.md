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

The `cas/build/cas-resources` files are unzipped from `cas.war!WEB-INF\lib\cas-server-webapp-resources-<version>.jar` via `gradle explodeWar` in the overlay.

To construct the overlay project, you need to copy directories and files *that you need to customize* in the build directory over to the source directory.

The WAR overlay also provides additional tasks to explode the binary artifact first before re-assembling it again. You may need to do that step manually yourself to learn what files/directories need to be copied over to the source directory.

Note: Do **NOT** ever make changes in the above-noted build directory. The changeset will be cleaned out and set back to defaults every time you do a build. Put overlaid components inside the source directory and/or other instructed locations to avoid surprises.

## CAS Configuration Server Overlay

See this [Maven WAR overlay](https://github.com/apereo/cas-configserver-overlay) for more details.

To learn more about the configuration server, please [review this guide](../configuration/Configuration-Server-Management.html).

## Dockerized Deployment

有关更多信息，请参见 [本指南](Docker-Installation.html)

## Servlet Container

CAS can be deployed to a number of servlet containers. 有关更多信息，请参见 [本指南](Configuring-Servlet-Container.html)

## Custom and Third-Party Source

It is common to customize or extend the functionality of CAS by developing Java components that implement CAS APIs or to include third-party source by dependency references. Including third-party source is trivial; simply include the relevant dependency in the overlay `build.gradle` file. 

<div class="alert alert-warning"><strong>Stop Coding</strong><p>
Overlaying or modifying CAS internal components and classes, <i>unless ABSOLUTELY required</i>, should be a last resort and is generally 
considered a misguided malpractice. Where possible, avoid making custom changes to carry the maintenance burden solely on your own. 
Avoid carrying . You will risk the stability and security of your deployment. If the enhancement 
case is attractive or modest, contribute back to the project. Stop writing code, or rite it where it belongs.
</p></div>

In order to include custom Java source, it should be included under a `src/main/java` directory in the overlay project source tree.

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

## Dependency Management

Each release of CAS provides a curated list of dependencies it supports. In practice, you do not need to provide a version for any of these dependencies in your build configuration as the CAS distribution is managing that for you. When you upgrade CAS itself, these dependencies will be upgraded as well in a consistent way.

The curated list of dependencies contains a refined list of third party libraries. The list is available as a standard Bills of Materials (BOM). Not everyone likes inheriting from the BOM. You may have your own corporate standard parent that you need to use, or you may just prefer to explicitly declare all your configuration.

To take advantage of the CAS BOM at `org.apereo.cas:cas-server-support-bom`, via Gradle, please [use this guide](https://plugins.gradle.org/plugin/io.spring.dependency-management) and configure the Gradle build accordingly.

<sub>(1) [WAR Overlays](http://maven.apache.org/plugins/maven-war-plugin/overlays.html)</sub>
