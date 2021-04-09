---
layout: default
title: CAS - 配置命令行 Shell
category: 安装
---

# CAS 命令行 Shell

CAS 命令行工具能够查询CAS 服务器，以获得可用设置/模块的帮助，并提供了其他各种实用功能。

要调用和使用该实用程序，请执行：

```bash
java -jar /path/to/cas-server-support-shell-$casVersion.jar
```

... `$casVersion` 是已部署的CAS版本。

接下来显示的界面将指导您完成可用的参数和查询方法。 您将学习如何启动交互式shell并动态查询CAS引擎。

<div class="alert alert-info"><strong>JCE要求</strong><p>请确保您已经在您的Java环境中安装了正确的JCE捆绑包，特别是在需要使用特定的签名/加密算法和方法的情况下。 
请务必为您的Java版本选择正确的JCE版本。 Java 版本可以通过 <code>java -version</code> 命令检测到.</p></div>

请注意， [WAR覆盖](WAR-Overlay-Installation.html) 默认的部署策略应该已经集成了这个功能。 您不必做任何特别的事情即可与shell进行交互。 查看相关的叠加文档以了解如何调用和使用外壳的更多信息。
