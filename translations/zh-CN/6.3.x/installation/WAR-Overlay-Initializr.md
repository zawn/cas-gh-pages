---
layout: default
title: CAS - WAR 覆盖初始化
category: 安装
---

# WAR 覆盖初始化

[Apereo CAS Initializr][initializr]是Apereo CAS生态系统中的一个相对较新的功能，允许作为部署者，在你需要快速启动项目的情况下，迅速生成CAS WAR覆盖项目

为了进行CAS部署，部署者之前通常从一个[基于Gradle的覆盖项目](WAR-Overlay-Installation.html)开始，并将其做为基线版本进行功能定制。 尽管多年来一直是传统的推荐使用WAR覆盖，但对于刚接触CAS的新手而言，下载、修改、准备覆盖项目以及加入所有需要的自定义项也是一个挑战。 考虑到覆盖项目的静态性质，对于项目的所有者和维护者来说，如何在快速的更新项目或提供额外附加功能的时候不影响基线版本模板也可能是一个挑战。

为了解决这种情况， [CAS WAR Overlay Initializr][initializr] 提供了一种快速的方法来拉取CAS部署所需的所有依赖项和模块，同时提供了友好且可编程的curl-friendly API来生成覆盖结构和所需的构建文件。

处理项目生成任务的基础框架基于 [Spring Initializr](https://github.com/spring-io/initializr)

## 概述

CAS 初始化器可以依据实际部署情况请求所需的模块和依赖，动态地生成一个初始项目。 该功能可以由用户按照自己的需求进行定制，通过用户输入的条件来生成初始模板、附加引用和文件、以及其他，这使得部署过程更加人性化。

当前CAS Initializr主要是一个后端服务和一些API。 然而，可以想象可以在可用的API之上构建现代话的图形界面，来帮助完成项目生成任务，特别是对于项目新手这很有用。

管理和维护一个单独的覆盖项目，并保持它们与 CAS版本同步的可能是一项昂贵的维护任务 CAS 初始化器允许项目开发者实现维护任务自动化，将所有内容都保持在同一个存储库以便更快、更准确地升级。

<div class="alert alert-info"><strong>备注</strong>
<p>请记住，CAS 初始化器在这个当前无法生成<code>CAS管理Web应用程序</code>的覆盖项目。 这个功能将在未来版本中完成。</p></div>

在CAS 项目内部，我们使用CAS 初始化器器，*自己自足*的动态生成覆盖项目。 这些生成的项目被用作发布到Docker Hub的Docker映像，并用作CAS CI针对浏览器/用户界面进行测试的基线版本。 CAS 初始化器使用自己测试自己！

## 项目生成

可用使用`CURL`调用[CAS 初始化器][initializr] 生成一个 CAS 覆盖项目。 要访问CAS Initializr，可以使用以下方法。

### Heroku
CAS 项目在[Heroku][initializr] 上提供了一个 CAS 初始化器的运行实例。 要使用这个实例创建初始项目，一个简单的方法可能是在你的Bash配置文件中包含以下函数:

```bash
function getcas(){
    curl -k https://casinit.herokuapp.com/starter.tgz -d dependencies="$1" | tar -xzvf -
    ls
}
```

这使您可以使用以下方法生成CAS覆盖项目：

```bash
getcas duo,oidc
```

…这里生成一个具有 [Duo Security](../mfa/DuoSecurity-Authentication.html)多因素认证和[OpenID Connect](OAuth-OpenId-Authentication.html)支持的CAS覆盖项目。

<div class="alert alert-info"><strong>备注</strong>
<p>为了降低部署成本，Heroku实例启用了对请求限速。 请注意，访问时可能会限制频繁请求。</p></div>

### Docker

如果在 Heroku 上的初始化器不可用，您也可以通过 Docker 运行您自己的初始实例：

```bash
docker run --rm -p 8080:8080 apereo/cas-initializr:${tag}
```

CAS 初始化器应可在 `http://localhost:8080` 上访问，并可以使用 curl 访问API获取响应。 已发布的CAS 初始化器的镜像和标签 [可以在这里找到](https://hub.docker.com/r/apereo/cas-initializr/tags)。 每个镜像标签都于相应的CAS服务器版本对应，该镜像能够生成对应版本的CAS覆盖项目。

## CAS模块

CAS project modules and dependencies that can be requested must be specified by their identifier. To see a full list of all dependencies supported and available by this service, you can invoke the following command:

```bash
curl https://casinit.herokuapp.com/dependencies
```

Typically, dependency identifiers match CAS server dependency/module artifact names without the `cas-server-` prefix. Furthermore, certain dependencies can are assigned aliases as shortcuts to simplify requests. To see the full list of dependencies and their aliases, you may use:

```bash
curl https://casinit.herokuapp.com/actuator/info
```

Furthermore, CAS Initializr publishes metadata about its capabilities, that is the available options for all request parameters (dependencies, type, etc.) A client to the service uses that information to initialize the select options and the tree of available dependencies.

You can grab the metadata on the root endpoint with the appropriate `Accept` header:

```bash
curl -H 'Accept: application/json' https://casinit.herokuapp.com
```

Or using HTTPie:

```bash
http https://casinit.herokuapp.com Accept:application/json
```

[initializr]: https://casinit.herokuapp.com/

[initializr]: https://casinit.herokuapp.com/

[initializr]: https://casinit.herokuapp.com/

[initializr]: https://casinit.herokuapp.com/
