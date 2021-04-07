---
layout: default
title: CAS - 首页
---

# CAS 企业单点登录

欢迎来到Apereo中央认证服务项目的主页，该项目通常也称为CAS。 CAS项目是一个提供身份验证和授权的多语言单点登录整体解决方案。

CAS协议是一个开放且设计良好的身份验证协议。 当前项目是CAS协议的Java服务端首选实现，同时当前项目也支持大量其他身份验证协议和功能。

以下是CAS项目所包含的主要功能要点和技术摘要：

* [Spring Webflow](webflow/Webflow-Customization.html)/ Spring Boot [Java服务器组件](planning/Architecture.html)。
* [可接入的验证方式](installation/Configuring-Authentication-Components.html) ([LDAP](installation/LDAP-Authentication.html)， [数据库](installation/Database-Authentication.html)， [X.509](installation/X509-Authentication.html)， [SPNEGO](installation/SPNEGO-Authentication.html)， [JAAS](installation/JAAS-Authentication.html)， [JWT](installation/JWT-Authentication.html)， [RADIUS](mfa/RADIUS-Authentication.html)， [MongoDB](installation/MongoDb-Authentication.html)等)
* 支持多种协议 ([CAS](protocol/CAS-Protocol.html)， [SAML](protocol/SAML-Protocol.html)， [WS-Federation](protocol/WS-Federation-Protocol.html)， [OAuth2](protocol/OAuth-Protocol.html)， [OpenID](protocol/OpenID-Protocol.html)， [OpenID连接](protocol/OIDC-Protocol.html)， [REST](protocol/REST-Protocol.html))
* 支持 [多因素验证](mfa/Configuring-Multifactor-Authentication.html) ([Duo Security](mfa/DuoSecurity-Authentication.html), [FIDO U2F](mfa/FIDO-U2F-Authentication.html), [YubiKey](mfa/YubiKey-Authentication.html), [Google身份验证器](mfa/GoogleAuthenticator-Authentication.html), [Authy](mfa/AuthyAuthenticator-Authentication.html), [Acceptto](mfa/Acceptto-Authentication.html), etc.)
* 支持 [委托验证](integration/Delegate-Authentication.html)（例如 [ADFS](integration/ADFS-Integration.html)，Facebook，Twitter，SAML2 IdP等）
* 内建对 [密码管理](password_management/Password-Management.html)， [通知](webflow/Webflow-Customization-Interrupt.html)， [使用条款](webflow/Webflow-Customization-AUP.html) 和 [代位验证](installation/Surrogate-Authentication.html)等功能的支持。
* 在获取 [用户属性](integration/Attribute-Release.html)的时候可提供 [用户确认](integration/Attribute-Release-Consent.html)界面。
* [实时监视和跟踪](monitoring/Monitoring-Statistics.html) 应用程序的行为，统计信息和日志。
* 使用特定的身份验证策略管理和注册 [客户端应用程序和服务](services/Service-Management.html)
* [跨平台客户端支持](integration/CAS-Clients.html) (Java，.Net，PHP，Perl，Apache等)。
* 与 [InCommon，Box，Office365，ServiceNow，Salesforce，Workday，WebAdvisor](integration/Configuring-SAML-SP-Integrations.html)，Drupal，Blackboard，Moodle， [Google Apps](integration/Google-Apps-Integration.html)等集成。

## 贡献

要了解如何为该项目做出贡献， [请查看本指南](/cas/developer/Contributor-Guidelines.html)。

## 开始使用

我们建议阅读以下文档，以便规划和进行CAS部署。

* [软件架构](planning/Architecture.html)
* [入门指南](planning/Getting-Started.html)
* [安装要求](planning/Installation-Requirements.html)
* [安装](installation/WAR-Overlay-Installation.html)
* [博客](https://apereo.github.io)

## 支持

CAS的开发由以下工具，项目和服务支持。

{:.list-group}
* {:.list-group-item} <a href="https://www.jetbrains.com/idea/"><img src="https://user-images.githubusercontent.com/1205228/31548576-1ac3d688-b038-11e7-9565-ffd89501872e.png" width="150"></a>
* {:.list-group-item} <a href="https://www.eclipse.org"><img width="130" src="https://user-images.githubusercontent.com/1205228/32225495-ac7b1e94-be5a-11e7-8f83-5c7399398fb8.png"></a>
* {:.list-group-item} <a href="http://projects.spring.io/spring-boot/"><img width="130" src="https://user-images.githubusercontent.com/1205228/32322526-0b58ac44-bfda-11e7-822e-ad763eb80faf.png"></a>
* {:.list-group-item} <a href="https://www.yourkit.com"><img src="https://user-images.githubusercontent.com/1205228/38207124-f6c6db34-36c1-11e8-9bbf-8dee5bd199c4.png" width="130"></a><br/>YourKit提供了全功能的开源项目Java Profiler。 YourKit, LLC是 <a href="https://www.yourkit.com/java/profiler/">YourKit Java Profiler</a> 和 <a href="https://www.yourkit.com/.net/profiler/">YourKit .NET Profiler</a>的创建者, 他们是用于分析Java和.Net程序的创新和智能工具。
