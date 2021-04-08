---
layout: default
title: CAS-安全指南
category: 概览
---

# 安全指南

CAS是安全软件，可为基于Web的应用程序提供安全的基于Web的单点登录。 单点登录在安全性和便利性方面双赢：仅CAS服务器会接触到密码，减少了密码使用，同时通过信任凭据透明地提供了对多种服务的访问，而无需重复登录。 CAS的使用会提高系统的安全性，当然在CAS的配置，策略和部署的时候也考虑适当的安全性。

<div class="alert alert-info"><strong>报告问题</strong><p>安全团队要求您 <strong>不要</strong> 创建公开可见的问题或帖子来讨论您可能认为的安全漏洞。 要正确报告问题并了解如何生成响应，请 <a href="/cas/developer/Sec-Vuln-Response.html">参见本指南</a>。</p></div>

## 公告

- [2020年10月14日漏洞披露](https://apereo.github.io/2020/10/14/gauthvuln/)
- [2020年7月24日漏洞披露](https://apereo.github.io/2020/07/24/credvuln/)
- [2020年2月8日漏洞披露](https://apereo.github.io/2020/02/08/webflowcrypto/)
- [2019年12月20日漏洞披露](https://apereo.github.io/2019/12/20/surrogatevuln/)
- [2019年11月21日漏洞披露](https://apereo.github.io/2019/11/24/smfavuln/)
- [2019年9月27日漏洞披露](https://apereo.github.io/2019/09/27/numvulndisc/)
- [2018年9月26日漏洞披露](https://apereo.github.io/2018/09/26/mfavulndisc/)
- [2017年3月6日漏洞披露](https://apereo.github.io/2017/03/06/moncfgsecvulndisc/)
- [2016年10月24日漏洞披露](https://apereo.github.io/2016/10/24/servlvulndisc/)
- [2016年4月8日漏洞披露](https://apereo.github.io/2016/04/08/commonsvulndisc/)

## 系统安全注意事项

要考虑的基础设施安全事项可能包括以下内容。

### 传输安全（https）

与CAS服务器的所有通信都必须通过安全通道（即TLSv1）进行。 此要求基于以下两个原因：

1. 身份验证过程需要传输安全凭证。
2. CAS TGT票据是一个不记名令牌。

由于任何一种数据的泄露都可能造成模拟攻击，因此保护CAS客户端和CAS服务器之间交互通道的安全性是至关重要的。

实际上，这意味着所有CAS向外提供的所有URL必须使用HTTPS， **也** 意味着必须使用HTTPS完成从CAS服务器到应用程序的所有连接

- 比如CAS服务器通过服务的回调URL向应用程序传递生成的服务票据（ST）
- 比如调用代理回调URL

要查看或者调整CAS与之相关属性设置，请 [查阅本指南](../configuration/Configuration-Properties.html#http-client)。


### 与相关系统的连接

CAS通常需要连接到其他系统，例如LDAP目录，数据库和缓存服务。 我们通常建议在可能的情况下使用到这些系统的安全传输（SSL / TLS，IPSec），也可能通过一些其他设置来满足安全传输的要求。 常见的例外情况是具有严格的准入控制的专用网络和公司网络，但仍然建议使用安全运输。 客户端证书验证可能是给LDAP提供足够安全性的另一个很好的解决方案。

如前所述，与其他系统的连接必须要是安全的。 但是，如果将CAS服务器部署在多个节点上，则表示CAS服务器本身。 例如基于缓存的票据注册在使用单个CAS服务器时候没有任何安全问题，但在网络不受保护的情况下使用多个节点时，同步可能会带来安全问题。

如果没有适当地保护，任何磁盘存储也很容易受到攻击。 可以关闭EhCache到磁盘缓存来增加保护，而数据库磁盘存储应使用高级数据加密机制。

## 增加安全特性

CAS支持许多可用于实施各种安全策略的功能。 对CAS服务端和CAS客户端的进行相关配置，CAS提供以下功能。 请注意，许多功能都是开箱即用的，而其他功能则需要显式设置

### 强制验证

CAS客户端和许多受支持的协议都支持强制身份验证的概念，即用户必须重新进行身份验证才能访问特定服务。 CAS 协议支持通过 _renew_ 参数支持强制验证。 强制身份验证为SSO会话的主体提供了额外的安全保障，因为用户必须在访问之前验证他或她的凭证。 强制身份验证适用于需要或强制要求更高安全性的服务。 通常强制身份验证是每个服务安需要配置的。 但 [服务管理](#service-management) 集中管理安全策略的基础设施也为强制验证提供一些具体的支持。 强制身份验证可以与[多因素身份验证](../configuration/Configuration-Properties.html#multifactor-authentication) 功能相结合从而为特定服务的实施任意的访问控制策略。


### 被动验证

在匿名访问服务过程中，当访问被CAS保护的服务的时候，某些CAS协议支持被动身份验证。 CASv2和CASv3协议通过 _gateway_ 功能支持此功能。 被动身份验证是对强制身份验证的补充；在强制身份验证需要身份验证才能访问服务的情况下，被动 身份验证允许服务访问（尽管是匿名的），而无需身份验证。


### 代理验证

代理身份验证或委托身份验证提供了强大，重要且可能会提高CAS安全性的功能。 代理身份验证受CASv2和CASv3协议支持，并由服务代表用户请求Proxy票据，因此，该服务代理了用户的身份验证。 代理身份验证通常用于服务无法直接与用户交互的情况，并且是将最终用户凭据传播到服务。

但是，代理票据存在风险，因为接受票据的服务负责验证代理链（服务列表，通过该服务列表，最终用户的身份验证被委派到了票据验证服务）。 Services can opt out of accepting proxy tickets entirely (and avoid responsibility for validating proxy chains) by validating tickets against the /serviceValidate validation endpoint, but experience has shown it's easy to be confused about this and configure to unintentionally use the /proxyValidate endpoint yet not scrutinize any proxy chains that appear in the ticket validation response. Thus proxy authentication requires careful configuration for proper security controls; it is recommended to disable proxy authentication components at the CAS server if proxy authentication is not needed.

Historically any service could obtain a proxy-granting ticket and from it a proxy ticket to access any other service. In other words, the security model is decentralized rather than centralized. The service management facility affords some centralized control of proxy authentication by exposing a proxy authentication flag that can enabled or disabled on a per-service basis. By default registered services are not granted proxy authentication capability.

### 凭据缓存和传播

The _ClearPass_ extension provides a mechanism to capture primary authentication credentials, cache them (encrypted), and replay on demand as needed to access legacy services. While [proxy authentication](#proxy-authentication) is recommended in lieu of password replay, it may be required to integrate legacy services with CAS. See the [ClearPass](../integration/ClearPass.html) documentation for detailed information.


### 服务管理

The service management facility provides a number of service-specific configuration controls that affect security policy and provide some support for centralized security policy. (Note that CAS has historically supported the decentralized security policy model.) Some highlights of service management controls:

* Authorized services
* Forced authentication
* Attribute release
* Proxy authentication control
* Theme control
* Service authorization control
* Multi-factor service access policy

The service management facility is comprised of a service registry containing one or more registered services, each of which specifies the management controls above. The service registry can be controlled via static configuration files, a Web user interface, or both. See the [Service Management](../services/Service-Management.html) section for more information.

<div class="alert alert-warning"><strong>服务授权</strong><p>
As a security best practice, it is <strong>strongly</strong> recommended to limit the service management facility
to only include the list of known applications that are authorized to use CAS. Leaving the management interface
open for all applications may create an opportunity for security attacks.
</p></div>

### SSO Cookie加密

A ticket-granting cookie is an HTTP cookie set by CAS upon the establishment of a single sign-on session. The cookie value is by default encrypted and signed via settings defined in CAS properties. While sample data is provided for initial deployments, these keys **MUST** be regenerated per your specific environment. Please [see this guide](../installation/Configuring-SSO.html) for more info.

### 密码管理中的安全链接

Account password reset requests are handled via a secured link that is sent to the registered email address of the user. The link is available only within a defined time window and the request is properly signed and encrypted by CAS. While sample data is provided for initial deployments, these keys **MUST** be regenerated per your specific environment.

Please [see this guide](../installation/Password-Policy-Enforcement.html) for more info.

### 协议票据加密

Protocol tickets that are issued by CAS and shared with other applications such as service tickets may optionally go through a signing/encryption process. Even though the CAS server will always cross check ticket validity and expiration policy, this may be forced as an extra check to ensure tickets in transit to other applications are not tampered with and remain to be authentic. While sample data is provided for initial deployments, these keys **MUST** be regenerated per your specific environment.

<div class="alert alert-warning"><strong>请注意</strong><p>Encrypting and signing a generated ticket will, depending on the encryption method and algorithm used, increase the generated ticket length. Not all CAS clients are equipped to handle lengthy ticket strings and may get upset with you. Evaluate existing integrations before turning this on and consider whether this feature is truly needed for your deployment.</p></div>

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#protocol-ticket-security).


### Ticket Registry Encryption

Secure ticket replication as it regards clustered CAS deployments may be required to ensure generated tickets by CAS are not tampered with in transit. CAS covers this issue by allowing tickets to be natively encrypted and signed. While sample data is provided for initial deployments, these keys **MUST** be regenerated per your specific environment. Please [see this guide](../installation/Ticket-Registry-Replication-Encryption.html) for more info.

### 管理页面的安全

CAS provides a large variety of web interfaces that are aimed at system administrators and deployers. These screens along with a number of REST endpoints allow a CAS deployer to manage and reconfigure CAS behavior without resorting to native command-line interfaces. Needless to say, these endpoints and screens must be secured and allowed proper access only to authorized parties. Please [see this guide](../monitoring/Monitoring-Statistics.html) for more info.

### 票据过期政策

Ticket expiration policies are a primary mechanism for implementing security policy. Ticket expiration policy allows control of some important aspects of CAS SSO session behavior:

* SSO session duration (sliding expiration, absolute)
* Ticket reuse

See the [Configuring Ticketing Components](../ticketing/Configuring-Ticketing-Components.html) section for a detailed discussion of the various expiration policies and configuration instructions.

### 单点登出

Single sign-out, or single log-out (SLO), is a feature by which CAS services are notified of the termination of a CAS SSO session with the expectation that services terminate access for the SSO session owner. While single sign-out can improve security, it is fundamentally a best-effort facility and may not actually terminate access to all services consumed during an SSO session. The following compensating controls may be used to improve risks associated with single sign-out shortcomings:

* Require forced authentication for sensitive services
* Reduce application session timeouts
* Reduce SSO session duration

SLO can happen in two ways: from the CAS server (back-channel logout) and/or from the browser (front-channel logout). For back-channel logout, the SLO process relies on the `SimpleHttpClient` class which has a threads pool: its size must be defined to properly treat all the logout requests. Additional not-already-processed logout requests are temporarily stored in a queue before being sent: its size is defined to 20% of the global capacity of the threads pool and can be adjusted. Both sizes are critical settings of the CAS system and their values should never exceed the real capacity of the CAS server.


### 登录限制

CAS supports a policy-driven feature to limit successive failed authentication attempts to help prevent brute force and denial of service attacks. The feature is beneficial in environments where back-end authentication stores lack equivalent features. In cases where this support is available in underlying systems, we encourage using it instead of CAS features; the justification is that enabling support in underlying systems provides the feature in all dependent systems including CAS. See the [login throttling configuration](../installation/Configuring-Authentication-Components.html#login-throttling) section for further information.

### 凭证加密

To learn how sensitive CAS settings can be secured via encryption, [please review this guide](../configuration/Configuration-Properties-Security.html).

### CAS安全过滤

The CAS project provides a number of a blunt [generic security filters][cas-sec-filter] suitable for patching-in-place Java CAS server and Java CAS client deployments vulnerable to certain request parameter based bad-CAS-protocol-input attacks. The filters are configured to sanitize authentication request parameters and reject the request if it is not compliant with the CAS protocol in the event that for instance, a parameter is repeated multiple times, includes multiple values, contains unacceptable values, etc.

It is **STRONGLY** recommended that all CAS deployments be evaluated and include this configuration if necessary to prevent protocol attacks in situations where the CAS container and environment are unable to block malicious and badly-configured requests.

#### CORS

CAS provides first-class support for enabling HTTP access control (CORS). One application of CORS is when a resource makes a cross-origin HTTP request when it requests a resource from a different domain than the one which the first resource itself serves. This should help more with CAS-enabled applications are accessed via XHR/Ajax requests.

To see the relevant list of CAS properties and tune this behavior, please [review this guide](../configuration/Configuration-Properties.html#http-web-requests).

#### 安全响应头部

As part of the CAS Security Filter, the CAS project automatically provides the necessary configuration to insert HTTP Security headers into the web response to prevent against HSTS, XSS, X-FRAME and other attacks. These settings are presently on by default. To see the relevant list of CAS properties and tune this behavior, please [review this guide](../configuration/Configuration-Properties.html#http-web-requests).

To review and learn more about these options, please visit [this guide][cas-sec-filter].

### Spring Webflow会话

The CAS project uses Spring Webflow to manage and orchestrate the authentication process. The conversational state of the webflow used by CAS is managed by the client which is then passed and tracked throughout various states of the authentication process. This state must be secured and encrypted to prevent session hijacking. While CAS provides default encryption settings out of the box, it is **STRONGLY** recommended that [all CAS deployments](../webflow/Webflow-Customization.html) be evaluated prior to production deployments and regenerate this configuration to prevent attacks.

### 长期认证

The long term authentication feature, commonly referred to as "Remember Me", is selected (usually via checkbox) on the CAS login form to avoid re-authentication for an extended period of time. Long term authentication allows users to elect additional convenience at the expense of reduced security. The extent of reduced security is a function of the characteristics of the device used to establish a CAS SSO session. A long-term SSO session established from a device owned or operated by a single user is marginally less secure than a standard CAS SSO session. The only real concern would be the increased lifetime and resulting increased exposure of the CAS ticket-granting ticket. Establishing a long-term CAS SSO session from a shared device, on the other hand, may dramatically reduce security. The likelihood of artifacts from previous SSO sessions affecting subsequent SSO sessions established by other users, even in the face of single sign-out, may increase the likelihood of impersonation. While there is no feasible mitigation for improving security of long-term SSO sessions on a shared device, educating users on the inherent risks may improve overall security.

It is important to note that forced authentication supersedes long term authentication, thus if a service were configured for forced authentication, authentication would be required for service access even in the context of a long-term session.

Long term authentication support must be explicitly enabled through [configuration and UI customization](../installation/Configuring-Authentication-Components.html#long-term-authentication) during the installation process. Thus deployers choose to offer long-term authentication support, and when available users may elect to use it via selection on the CAS login form.


### 警告

CAS supports optional notification of service access during an established SSO session. By default CAS transparently requests tickets needed for service access and presents them to the target service for validation, whereby upon successful validation access to the service is permitted. In most cases this happens nearly instantly and the user is not aware of the CAS authentication process required to access CAS-enabled services. There may be some security benefit to awareness of this process, and CAS supports a _warn_ flag that may be selected by the user on the CAS login screen to provide an interstitial notification page that is displayed prior to accessing a service. By default the notification page offers the user an option to proceed with CAS authentication or abort by navigating away from the target service.

[cas-sec-filter]: https://github.com/apereo/cas-server-security-filter

[cas-sec-filter]: https://github.com/apereo/cas-server-security-filter
