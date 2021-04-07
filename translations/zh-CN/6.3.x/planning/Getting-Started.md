---
layout: default
title: CAS - 开始使用
category: 概览
---

# 开始使用

本文档提供了有关如何开始进行CAS服务器部署的高级指南。 本手册描述CAS部署人员必须遵循并采用一些方法，使大家可以成功的部署并方便后期升级。

## 收集用例

在部署之前，记录，分类和分析所需的用例和需求非常重要。 当你有些特别的想法的时候，请与 [CAS社区](/cas/Support.html)讨论并分享这些想法，你将会得到解决这些问题的思路，方式或者方法，也许你会发现你面临的问题已经得到了解决。 

<div class="alert alert-warning"><strong>小贴士</strong><p>一般而言，避免设计和/或采用严重改变CAS 内部组件的案例和工作流，避免重复造轮子，以及修改支持协议，这些都将会给后期的管理和维护带来沉重的负担。 这些都将会给后期的管理和维护带来沉重的负担。</p></div>

## 学习软件架构

了解CAS是什么并且可以做什么。 这将有助于你建立一个基础的认知，以了解你的使用场景和需求哪些可能已经在CAS中存在了。 查看 [CAS 软件架构](Architecture.html)了解一下哪些功能和选项可以用于部署和应用程序集成。

CAS [支持的协议和规范](../protocol/Protocol-Overview.html)的列表也同样重要。

## 查看博客

有时， 出现在 [Apeo 博客](https://apereo.github.io/)上博客文章，可能会对您想到需求和功能有帮助。 通常建议您关注该博客，并尽可能多地关注项目新闻和公告，并且积极的撰写和分享你在CAS部署和升级中的经验。

## 环境准备

参考 [安装要求](Installation-Requirements.html)来进行环境部署

## 部署CAS

建议使用 [WAR 叠加](../installation/WAR-Overlay-Installation.html) 在本地构建和部署CAS 这种方法不需要采用者 *明确* 下载CAS的任何版本，同时利用叠加机制合并CAS原有组件内容与本地定制资源，以方便未来的进一步升级和维护。

**注意**: 不要克隆或直接下载 CAS 代码。 仅当您希望为项目的发展做出贡献时，才需要这样做。

在执行其他任何操作之前，尝试使基线功能正常运行是 **非常重要** 避免立即进行临时更改以自定义部署。 坚持使用CAS提供的默认值和设置，并且更改后 **及时验证**。 通过源代码版本控制系统记录和跟踪过你应用的更改，并在成功时打上标记

## 自定义

This is where use cases get mapped to CAS features. Browse the documentation to find the closest match and apply. Again, it is important that you stick with the CAS baseline as much as possible:

- Avoid making ad-hoc changes to the software internals.
- Avoid making manual changes to core configuration components such as Spring and Spring Webflow.
- Avoid making one-off bug fixes to the deployment, should you encounter an issue.

As noted previously, all such strategies lead to headache and cost.

Instead, try to warm up to the following suggestions:

- Bug fixes and small improvements belong to the core CAS software. Not your deployment. Make every attempt to report issues, contribute fixes and patches and work with the CAS community to solve issues once and for all.
- Certain number of internal CAS components are made difficult to augment and modify. In most cases, this approach is done on purpose to steer you away from dangerous and needlessly complicated changes. If you come across a need and have a feature or use case in mind whose configuration and implementation requires modifications to the core internals of the software, discuss that with the CAS community and attempt to build the enhancement directly into the CAS software, rather than treating it as a snowflake.

To summarize, only make changes to the deployment configuration if they are truly and completely specific to your needs. Otherwise, try to generalize and contribute back to keep maintenance costs down. Repeatedly, failure to comply with this strategy will likely lead to disastrous results in the long run.

## 故障排查

[故障排除指南](../installation/Troubleshooting-Guide.html) 可能会为您遇到的问题提供一些答案，并且通常会尝试描述对故障排除和诊断有用的策略。 您也可以从 [CAS社区](/cas/Mailing-Lists.html)寻求帮助。
