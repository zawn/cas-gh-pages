---
layout: default
title: CAS - 升级指南
category: 概览
---

# 升级指南

一般来说，建议使用者尽量保持其 CAS 部署与最新的 CAS 版本保持一致。 尤其是，应尽快升级 `PATCH` 或 `SECURITY` 性质的发行版，他们是你当前版本的最佳替代。 有关更多信息，请参见CAS [发布策略](../../developer/Release-Policy.html)

CAS升级的总体目标可能是：

1.
2. 升级是否提供了对本地用例有用的功能的实现？
3. 升级是否提供了在我的本地叠加层中实现的功能，例如，通过消除这些本地更改，我可以直接从CAS中受益，并最终得到一个较小的，更易于维护的叠加层？

本文档试图从一个很高的层次描述升级给定的[CAS部署](../installation/WAR-Overlay-Installation.html)所需的范围和工作。 而不是描述为了进行审查和调整所需的具体步骤/更改（这是不可能的），我们描述的是可以执行升级的策略。

## 更改日志

尝试升级之前，请查看 [CAS更改日志](https://github.com/apereo/cas/releases) 以确定您打算升级到的版本中包含哪些更改/修复，以及这些更改/修复是否适用于您的环境和您的CAS部署。 如果您使用的是较旧的CAS版本，并且似乎遇到的是BUG，你有可能通过查看更改日志，找到一个直接替代的叠加层，从而可以解决问题。

## 讨论问题

查看了更改日志后，如果没有发现你想象中对功能改进的修复/调整，请在适当的CAS邮件列表上讨论该问题。 讨论的结果将是进行范围/工作评估，以确定解决方案的可行性和完成解决方案的目标版本。

## 范围审查

一旦你决定了你升级的理想CAS版本，在尝试升级之前请查看CAS [发行策略](../../developer/Release-Policy.html)。 这将使您了解您可能期望从新版本中得到哪些更改，以及升级可能需要付出哪些努力。

## 评估本地叠加

作为最佳实践，建议您通过 [覆盖方法](../installation/WAR-Overlay-Installation.html)部署CAS。 如果你是这样做的话，这里的任务将是确定叠加层中你接触和修改的文件数。 记录你应用这些修改的原因和修改了那些内容，并使用CAS更改日志差分对比这些修改。 升级的结果很有可能是，在你覆盖层中出现的许多本地更改都是通过CAS默认提供的，这将使您在摆脱许多本地的这些改进。

您所做的更改通常是：

*
* 在CAS属性文件中设置并控制CAS的行为
* 用户界面更改可能包括CSS和JavaScript
* 用户属性解析和发布策略
* CAS中服务的注册和授权

## 准备开发环境

<img src="http://i.imgur.com/jcdDHWb.jpg" width="160px" height="200px" />

Um, No.

请确保您有一个单独的开发环境可以进行配置和测试。 不管升级有多小，您都应确保在生产部署之前在您的环境中对它进行了良好的测试。 Evaluate the software dependencies and platform requirements of the new upgrade (i.e.

## 清理配置

We recommend that you first start out with a separate clean [CAS overlay](../installation/WAR-Overlay-Installation.html) targeted at the version to which you want to upgrade. This has the advantage of guaranteeing that your new CAS deployment will be functional without any local changes. Build and deploy the clean CAS overlay once to make sure your build/deployment process is functional.

## 应用更改

Go through your catalog of changes found in your local overlay. Compare and diff those files with their original version. You can find out the delta between two versions via the following ways:

1. If you have built the clean CAS overlay once, you will automatically get the original version typically in the `target` or `build/libs` directory of CAS overlay. Find the correct file at the correct path and compare.

2. Go directly to the [project source repository](https://github.com/apereo/cas), find the appropriate branch and compare files.

Needless to say, you are going to need:

1. A decent diff tool, such as [KDiff3](http://kdiff3.sourceforge.net/), [WinDiff](http://winmerge.org), [Beyond Compare](http://www.scootersoftware.com/), etc.
2. A decent intelligent text editor, such as [Sublime](http://www.sublimetext.com), [Atom](https://atom.io/) or a full blown IDE such as [IntelliJ IDEA](https://www.jetbrains.com/idea/).

## 文件变更

Remember to document the remaining changes that exist within your local overlay, so that the next time you do the same process, you have a clue as for why the overlay looks the way it does. 
