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

管理和维护一个单独的覆盖项目，并保持它们与 CAS版本同步的可能是一项昂贵的维护任务 CAS Initializr allows the project developers to automate the maintenance task, keep everything in the same repository for faster and more accurate upgrades.

<div class="alert alert-info"><strong>Note</strong>
<p>Remember that the CAS Initializr at this point in time is not able 
to produce an overlay project for the CAS Management web application. This 
functionality will be worked out in future releases.</p></div>

CAS Initializr is used internally by the CAS project itself in a very *Eat What Your Kill* type of way to dynamically generate overlay projects. These generated projects are used as CAS base Docker images published to Docker Hub, and as a baseline for browser/UI tests run by the CAS CI for each relevant feature. CAS Initializr uses itself to test itself!

## 项目生成

The [CAS Initializr][initializr] can be invoked using curl to generate a CAS overlay project. To access the CAS Initializr, the following strategies can be used.

### Heroku
The CAS projects provides a running an instance of the CAS Initializr on [Heroku][initializr]. To get started with this instance, a simple way might be to include the following function in your bash profile:

```bash
function getcas(){
    curl -k https://casinit.herokuapp.com/starter.tgz -d dependencies="$1" | tar -xzvf -
    ls
}
```

This allows you to generate a CAS overlay project using:

```bash
getcas duo,oidc
```

…which generates a CAS overlay project prepared with multifactor authentication by [Duo Security](../mfa/DuoSecurity-Authentication.html) and support for [OpenID Connect](OAuth-OpenId-Authentication.html).

<div class="alert alert-info"><strong>备注</strong>
<p>To help keep the deployment costs down, the Heroku instance has turned on support for 
rate-limiting requests. Be aware that frequent requests may be throttled for access.</p></div>

### Docker

In case the Initializr is not available on Heroku, you can also run your own Initializr instance via Docker:

```bash
docker run --rm -p 8080:8080 apereo/cas-initializr:${tag}
```

The CAS Initializr should become available at `http://localhost:8080` and will respond to API requests using curl. Published images and tags of the CAS Initializr [can be found here](https://hub.docker.com/r/apereo/cas-initializr/tags). Each tagged image corresponds to the CAS server version for which the image is able to produce overlay projects.

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
