---
layout: default
title: CAS - 架构
category: 概览
---

# 软件架构

![CAS软件架构图](../images/cas_architecture.png "CAS软件架构图")

## 系统组件

CAS服务端和客户端构成了CAS系统架构中的两个物理组件, 并通过各种协议进行通讯。

### CAS服务器

CAS服务器是基于Spring框架构建的Java Servlet，其主要职责是验证用户，并通过发放和验证令牌授予对启用CAS的服务（通常称为CAS客户端）的访问权限。 服务器会在用户成功登录后发放TGT凭据并创建SSO会话。 当用户请求服务时，服务将浏览器重定向到CAS服务器，CAS服务器验证TGT凭据后将向服务发放ST凭据。 随后服务通过后端通道与CAS服务器通讯并验证ST凭据的有效性。 这些交互在CAS协议文档中有详细描述。


### CAS 客户端

“CAS 客户端”一词在常用的场景中有两个不同的含义。 CAS客户端是任何有能力支持CAS协议并可以使用受支持的协议与CAS服务端进行通讯的应用程序。 CAS客户端也是软件包，各种软件平台和应用程序可以通过集成CAS客户端来提供身份验证协议(例如CAS，SAML，OAuth)并于CAS服务器进行通信。 CAS客户端已经在许多软件平台上提供了对应的产品。

平台：

* Apache httpd 服务器 ([mod_auth_cas 模块](https://github.com/Jasig/mod_auth_cas))
* Java ([Java CAS Client](https://github.com/apereo/java-cas-client))
* .NET ([.NET CAS Client](https://github.com/apereo/dotnet-cas-client))
* PHP ([phpCAS](https://github.com/Jasig/phpCAS))
* Perl (PerlCAS)
* Python (pycas)
* Ruby (rubycas-client)

应用程序：

* Canvas
* Atlassian Confluence
* Atlassian JIRA
* Drupal
* Liferay
* uPortal
* ...

当“ CAS客户端”一词在本手册中出现而没有进一步的限定时，它是指 组件，如Java CAS客户端而不是依赖于CAS服务器（作为其客户端）的应用程序。


## 受支持的协议

客户端通过多个支持的协议中的任何一个与服务器通信。  所有受支持的协议从理论上说是相似的，但是有些协议具有的功能和特性使他们更适合某些使用场景或者程序。 例如，CAS 协议支持委托（代理）身份验证，SAML 协议支持属性发布和单点登出。

协议支持

* [CAS (版本 1、2和3)](../protocol/CAS-Protocol.html)
* [SAML 1.1和2](../protocol/SAML-Protocol.html)
* [OpenID连接](../protocol/OIDC-Protocol.html)
* [OpenID](../protocol/OpenID-Protocol.html)
* [OAuth 2.0](../protocol/OAuth-Protocol.html)
* [WS联合协议](../protocol/WS-Federation-Protocol.html)


## 软件组件

可以用三层子系统来描述CAS服务器：

* Web (Pring MVC/Spring Webflow)
* [凭据发放](../ticketing/Configuring-Ticketing-Components.html)
* [身份验证](../installation/Configuring-Authentication-Components.html)

几乎部署的所有注意事项和组件配置都涉及这三个子系统。 Web层是与包括CAS客户端在内的所有外部系统进行通信的端点。 Web层委托到凭据发放子系统生成用于CAS客户端访问的凭据。 SSO会话从成功身份验证后颁发TGT凭据开始，因此凭据发放系统经常委派给身份验证子系统。

身份验证系统通常仅在SSO会话开始时处理请求，尽管在其他情况下也可以调用它（例如，强制身份验证）。

### Spring框架

CAS使用了Spring框架的多个组件。尤其是 [Spring MVC](http://docs.spring.io/spring/docs/current/spring-framework-reference/html/mvc.html) 和 [Spring Webflow](https://projects.spring.io/spring-webflow)。 Spring 为核心CAS代码库和部署人员提供了一个完整和可扩展的框架；可以简单的配置CAS和Spring API属性来定制或扩展CAS的行为。 Spring的基础知识有助于理解某些框架组件之间的相互作用，但这也不是必须的。

### Spring Boot

CAS还很大程度上基于 [Spring Boot](http://projects.spring.io/spring-boot/)，这使它可以基于Spring平台和第三方库，创建一个独立的Web应用程序，而不必担心XML配置的麻烦。 Spring Boot使CAS可以隐藏许多内部组件和配置的复杂性，提供自动配置模块  
并自动配置正在运行的应用程序上下文，而无需太多手动干预。 
