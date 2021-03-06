---
layout: default
title: CAS - 故障排除指南
category: 安装
---

# 疑难解答

这里收集了一些常见的问题和答案。 请注意更新，因为随着时间/发展的发展，更新可能会增加。

## 查看日志

如果已配置适当的日志级别，则CAS服务器日志是确定问题根本原因的最佳资源。 具体来说，您要确保在日志配置中为 `org.apereo`包打开 `DEBUG`级别的日志。

```xml
<Logger name="org.apereo" level="trace" additivity="false" includeLocation="true">
    <AppenderRef ref="console"/>
    <AppenderRef ref="file"/>
</Logger>
```

应用更改后，请重新启动服务器环境并观察日志文件，以更好地了解CAS行为 。 有关更多信息，请 [本指南](../logging/Logging.html) ，了解如何使用CAS配置日志。

请注意，以上配置代码仅开启了CAS组件的日志记录行为记录，不是CAS所依赖的第三方组件。 请参考log4j 配置，并为每个相关组件适当的打开 `DEBUG` 日志。 这些通常是您进行诊断和故障排除的最佳数据源。

如果你选择的容器是 [Apache Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/logging.html)，你可能也想看看你的 `catalina.out`和 `localhost-X-Y-Z.log`日志文件，以了解更多的问题根源。

## 部署问题； 某某配置问题。 你能帮我吗？

[请继续研究这个](#review-logs)。

## 如何调整/扩展MongoDb，MySQL，Spring Webflow等？

如果您对CAS使用的外部组件的调优和配置有疑问，并且需要实现CAS默认设置以外的其他更高级的用例，那么最好由负责该组件开发的社区来解决和支持。 一般来说， 你应该总是选择你最熟悉的技术，否则，请向Spring Webflow、MongoDb、Hazelcast等论坛中的专家寻求建议和评审。

以下类型的典型问题在其他地方可以得到最好的回答：

- 如何为Apache Tomcat，Jetty等配置SSL？
- 如何在Spring Webflow中将变量从一个流传递到下一个流？
- 我如何调优hazelcast群集？
- 为提高MongoDb的可用性，有什么建议的策略？

## 使用 `SNAPSHOT` 版本

在某些情况下，您可能会了解到可以修复与CAS部署相关的缺陷或行为，并且建议您升级到当前可用的 `SNAPSHOT` 版本。 根据您的选择的[安装方式](WAR-Overlay-Installation.html)，您需要找到你当前部署配置中的设置，并将描述您 *当前* CAS 版本的构建脚本升级到下一个 `SNAPSHOT`版本。 构建脚本还应该有README 文件中添加关于如何获取并在生成 `SNAPSHOT` 版本的额外说明。

要找到适合于你当前部署的 `SNAPSHOT` 版本， 您可以查看发布计划或CAS codebase的适当分支。 例如，如果您已部署CAS `2.0.4` ，并且发布计划显示下一个发行版针对 `2.0.5`，那么可用的 `SNAPSHOT` 发行版将是 `2.0.5-SNAPSHOT`。 您还可以查看分配给issue/pull请求的里程碑设置，已确定` SNAPSHOT`发布。 `SNAPSHOT` 发行版始终使用 `-SNAPSHOT`后缀。 如果为issue分配的里程碑为 `1.2.5-RC1`，那么 `SNAPSHOT` 发行版将是 `1.2.5-RC1-SNAPSHOT`。

## 在负载均衡器/代理后面配置SSL

You might be running CAS inside a [servlet container](Configuring-Servlet-Container.html) such as Apache Tomcat behind some sort of proxy such as haproxy, Apache httpd, etc where the proxy is handling the SSL termination. The connections to the user are secured via `https`, yet those between the proxy and CAS service are just `http`.

With this setup, the CAS login screen may still warn you about a non-secure connection. There is no setting in CAS that would allow you to control/adjust this, as this is entirely controlled by the container itself. All CAS cares about is whether the incoming connection request identifies itself as a secure connection. So to remove the warning, you will need to look into your container's configuration and docs to see how the connection may be secured between the proxy and CAS.

For [Apache Tomcat](https://tomcat.apache.org/tomcat-9.0-doc/config/http.html), you may be able to adjust the connector that talks to the proxy with a `secure=true` attribute.

## Application X "redirected you too many times"

"Too many redirect" errors are usually cause by service ticket validation failure events, generally caused by application misconfiguration. Ticket validation failure may be caused by expired or unrecognized tickets, SSL-related issues and such. Examine your CAS logs and you will find the cause.

## Not Receiving Attributes

If your client application is not receiving attributes, you will need to make sure:

1. The client is using a version of [CAS protocol](../protocol/CAS-Protocol.html) that is able to release attributes.
2. The client, predicated on #1, is hitting the appropriate endpoint for service ticket validation (i.e. `/p3/serviceValidate`).
3. The CAS server itself is [resolving and retrieving attributes](../integration/Attribute-Resolution.html) correctly.
4. The CAS server is authorized to [release attributes](../integration/Attribute-Release.html) to that particular client application inside its service registry.

Please [review this guide](../services/Service-Management.html) to better understand the CAS service registry.

## Application Not Authorized

You may encounter this error, when the requesting application/service url cannot be found in your CAS service registry. When an authentication request is submitted to the CAS `login` endpoint, the destination application is indicated as a url parameter which will be checked against the CAS service registry to determine if the application is allowed to use CAS. If the url is not found, this message will be displayed back. Since service definitions in the registry have the ability to be defined by a url pattern, it is entirely possible that the pattern in the registry for the service definition is misconfigured and does not produce a successful match for the requested application url.

Please [review this guide](../services/Service-Management.html) to better understand the CAS service registry.

## Invalid/Expired CAS Tickets

You may experience `INVAILD_TICKET` related errors when attempting to use a CAS ticket whose expiration policy dictates that the ticket has expired. The CAS log should further explain in more detail if the ticket is considered expired, but for diagnostic purposes, you may want to adjust the [ticket expiration policy configuration](../ticketing/Configuring-Ticket-Expiration-Policy.html) to remove and troubleshoot this error.

Furthermore, if the ticket itself cannot be located in the CAS ticket registry the ticket is also considered invalid. You will need to observe the ticket used and compare it with the value that exists in the ticket registry to ensure that the ticket id provided is valid.

## Out of Heap Memory Error

```bash
java.lang.OutOfMemoryError: GC overhead limit exceeded
        at java.util.Arrays.copyOfRange(Arrays.java:3658)
        at java.lang.StringBuffer.toString(StringBuffer.java:671)
        at 
```

You may encounter this error, when in all likelihood, a cache-based ticket registry such as EhCache is used whose eviction policy is not correctly configured. Objects and tickets are cached inside the registry storage back-end tend to linger around longer than they should or the eviction policy is not doing a good enough job to clean unused tickets that may be marked as expired by CAS.

To troubleshoot, you can configure the JVM to perform a heap dump prior to exiting, which you should set up immediately so you have some additional information if/when it happens next time. The follow system properties should do the trick:

```bash
-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath="/path/to/jvm-dump.hprof" 
```

Also ensure that your container is configured to have enough memory available. For Apache Tomcat, the following setting as an environment variable may be configured:

```bash
CATALINA_OPTS=-Xms1000m -Xmx2000m
```

You will want to profile your server with something like [JVisualVM](https://visualvm.github.io/). This will help you see what is actually going on with your memory.

You might also consider taking periodic heap dumps using the JMap tool or [YourKit Java profiler](http://www.yourkit.com/java/profiler/) and analyzing offline using some analysis tool.

Finally, review the eviction policy of your ticket registry and ensure the values that determine object lifetime are appropriate for your environment.

## SSL & Certificates

### PKIX Path Building Failed

```bash
Sep 28, 2009 4:13:26 PM org.apereo.cas.client.validation.AbstractCasProtocolUrlBasedTicketValidator retrieveResponseFromServer
SEVERE: javax.net.ssl.SSLHandshakeException:
sun.security.validator.ValidatorException: PKIX path building failed:
sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
javax.net.ssl.SSLHandshakeException:
sun.security.validator.ValidatorException: PKIX path building failed:
sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
      at com.sun.net.ssl.internal.ssl.Alerts.getSSLException(Unknown Source)
      at com.sun.net.ssl.internal.ssl.SSLSocketImpl.fatal(Unknown Source)
      at com.sun.net.ssl.internal.ssl.Handshaker.fatalSE(Unknown Source)
      at com.sun.net.ssl.internal.ssl.Handshaker.fatalSE(Unknown Source)
      at com.sun.net.ssl.internal.ssl.ClientHandshaker.serverCertificate(Unknown Source)
```

PKIX path building errors are the most common SSL errors. The problem here is that the CAS client does not trust the certificate presented by the CAS server; most often this occurs because of using a *self-signed certificate* on the CAS server. To resolve this error, import the CAS server certificate into the system truststore of the CAS client. If the certificate is issued by your own PKI, it is better to import the root certificate of your PKI into the CAS client truststore.

By default the Java system truststore is at `$JAVA_HOME/jre/lib/security/cacerts`. The certificate to be imported **MUST** be a DER-encoded file. If the contents of the certificate file are binary, it's likely DER-encoded; if the file begins with the text `---BEGIN CERTIFICATE---`, it is PEM-encoded and needs to be converted to DER encoding.

```bash
keytool -import -keystore $JAVA_HOME/jre/lib/security/cacerts -file tmp/cert.der -alias certName
```

If you have multiple java editions installed on your machine, make sure that the app / web server is pointing to the correct JDK/JRE version (The one to which the certificate has been exported correctly) One common mistake that occurs while generating self-validated certificates is that the `JAVA_HOME` might be different than that used by the server.


### No subject alternative names

```bash
javax.net.ssl.SSLHandshakeException: java.security.cert.CertificateException: No subject alternative names present
```

This is a hostname/SSL certificate CN mismatch. This commonly happens when a self-signed certificate issued to localhost is placed on a machine that is accessed by IP address. It should be noted that generating a certificate with an IP address for a common name, e.g. `CN=192.168.1.1,OU=Middleware,dc=vt,dc=edu`, will not work in most cases where the client making the connection is Java.

### HTTPS hostname wrong

```bash
java.lang.RuntimeException: java.io.IOException: HTTPS hostname wrong:  should be <eiger.iad.vt.edu>
    org.apereo.cas.client.validation.Saml11TicketValidator.retrieveResponseFromServer(Saml11TicketValidator.java:203)
    org.apereo.cas.client.validation.AbstractUrlBasedTicketValidator.validate(AbstractUrlBasedTicketValidator.java:185)
    org.apereo.cas.client.validation.AbstractTicketValidationFilter.doFilter
```

The above error occurs most commonly when the CAS client ticket validator attempts to contact the CAS server and is presented a certificate whose CN does not match the fully-qualified host name of the CAS server. There are a few common root causes of this mismatch:

- CAS client misconfiguration
- Complex multi-tier server environment (e.g. clustered CAS server)
- Host name too broad for scope of wildcard certificate

It is also worth checking that the certificate your CAS server is using for SSL encryption matches the one the client is checking against.

### No name matching X found

```bash
Caused by: java.security.cert.CertificateException: No name matching cas.server found
    at sun.security.util.HostnameChecker.matchDNS(Unknown Source) ~[?:1.8.0_77]
    at sun.security.util.HostnameChecker
```

Same as above.

### Wildcard Certificates

Java support for wildcard certificates is limited to hosts strictly in the same domain as the wildcard. For example, a certificate with `CN=.vt.edu` matches hosts **`a.vt.edu`** and **`b.vt.edu`**, but *not* **`a.b.vt.edu`**.

### Unrecognized Name Error

```bash
javax.net.ssl.SSLProtocolException: handshake alert: unrecognized_name
```

The above error occurs mainly in Oracle JDK CAS Server installations. In JDK, SNI (Server Name Indication) is enabled by default. When the HTTPD Server does not send the correct Server Name back, the JDK HTTP Connection refuses to connect and the exception stated above is thrown.

You must ensure your HTTPD Server is sending back the correct hostname. E.g. in Apache HTTPD, you must set the ServerAlias in the SSL vhost:

```bash
ServerName your.ssl-server.name
ServerAlias your.ssl-server.name
```

Alternatively, you can disable the SNI detection in JDK, by adding this flag to the Java options of your CAS Servers' application server configuration:
```bash
-Djsse.enableSNIExtension=false
```

### When All Else Fails

If you have read, understood, and tried all the troubleshooting tips on this page and continue to have problems, please perform an SSL trace and attach it to a posting to the CAS mailing lists. An SSL trace is written to STDOUT when the following system property is set, `javax.net.debug=ssl`. An example follows of how to do this in the Tomcat servlet container.

Sample `setenv.sh` Tomcat Script follows:

```bash
# Uncomment the next 4 lines for custom SSL keystore
# used by all deployed applications
# KEYSTORE="$HOME/path/to/custom.keystore"
# CATALINA_OPTS=$CATALINA_OPTS" -Djavax.net.ssl.keyStore=$KEYSTORE"
# CATALINA_OPTS=$CATALINA_OPTS" -Djavax.net.ssl.keyStoreType=BKS"
# CATALINA_OPTS=$CATALINA_OPTS" -Djavax.net.ssl.keyStorePassword=changeit"

# Uncomment the next 4 lines to allow custom SSL trust store
# used by all deployed applications
# TRUSTSTORE="$HOME/path/to/custom.truststore"
# CATALINA_OPTS=$CATALINA_OPTS" -Djavax.net.ssl.trustStore=$TRUSTSTORE"
# CATALINA_OPTS=$CATALINA_OPTS" -Djavax.net.ssl.trustStoreType=BKS"
# CATALINA_OPTS=$CATALINA_OPTS" -Djavax.net.ssl.trustStorePassword=changeit"

# Uncomment the next line to print SSL debug trace in catalina.out
# CATALINA_OPTS=$CATALINA_OPTS" -Djavax.net.debug=ssl"

export CATALINA_OPTS
```
