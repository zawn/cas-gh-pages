---
layout: default
title: CAS - Docker 安装
category: 安装
---

# Docker 安装

在每次发布CAS软件时，相关docker镜像都会在 [Docker Hub](https://hub.docker.com/r/apereo/cas/)上的Apereo CAS存储库中做标记。 可以通过以下命令拉取镜像：

```bash
docker pull apereo/cas:v[A.B.C]
```

...其中 `[A.B.C]` 代表映射到CAS服务器版本的镜像标签。

## 概述

Docker化的CAS部署是使用Docker包装已存在的 [CAS覆盖项目](WAR-Overlay-Installation.html) 覆盖项目已经包含一个嵌入式容器来处理CAS的部署。 覆盖项目还包括一个嵌入式构建工具，因此CAS构建和部署将不需要单独的步骤来下载和配置。

托管在Docker Hub上的Docker映像为 *多数为* 用作快速入门和演示。 您也可以将它们用作基础镜像，以将自定义内容添加到镜像中。 镜像是基于现有的 [CAS覆盖](WAR-Overlay-Installation.html)。
