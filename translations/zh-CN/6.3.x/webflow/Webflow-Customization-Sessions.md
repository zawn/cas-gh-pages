---
layout: default
title: CAS - Web Flow Customization
category: Webflow Management
---

# Webflow Session

CAS uses [Spring Webflow](https://github.com/spring-projects/spring-webflow) to manage the authentication sequence. Spring Webflow provides a pluggable architecture whereby various actions, decisions and operations throughout the primary authentication workflow can be easily controlled and navigated. In order for this navigation to work, some form of conversational session state must be maintained.

## Client-side Sessions

CAS provides a facility for storing flow execution state on the client in Spring Webflow. Flow state is stored as an encoded byte stream in the flow execution identifier provided to the client when rendering a view. By default, CAS automatically attempts to store and keep track of this state on the client in an encrypted form via encryption and signing keys to remove the need for session cleanup, termination and replication.

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#spring-webflow).

In the event that keys are not generated by the deployer, CAS will attempt to auto-generate keys and will output the result for each respected key. The deployer MUST attempt to copy the generated keys to their CAS properties file, specially when running a multi-node CAS deployment. Failure to do so will prevent CAS to appropriate decrypt and encrypt the webflow state and will prevent successful single sign-on.

<div class="alert alert-warning"><strong>Usage Warning!</strong><p>
While the above settings are all optional, it is recommended that you provide your own configuration and settings for encrypting and
transcoding of the web session state.</p></div>

## Server-side Sessions

In the event that you wish to use server-side session storage for managing the webflow session, you will need to enable this behavior via CAS properties. To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#spring-webflow).

Doing so will likely require you to also enable sticky sessions and/or session replication in a clustered deployment of CAS.

<div class="alert alert-warning"><strong>Usage Warning!</strong><p>
Generally speaking, you do not need to enable server-side sessions unless you have a rather specialized deployment or are in need of features that store bits and pieces of data into a sever-backed session object. It is recommended that you stick with the default client-side session storage and only switch if and when mandated by a specific CAS behavior.</p></div>

### Hazelcast Session Replication

If you don't wish to use the native container's strategy for session replication, you can use CAS's support for Hazelcast session replication.

This feature is enabled via the following module:

```xml
<dependency>
  <groupId>org.apereo.cas</groupId>
  <artifactId>cas-server-support-session-hazelcast</artifactId>
  <version>${cas.version}</version>
</dependency>
```

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#spring-webflow).

### Redis Session Replication

If you don't wish to use the native container's strategy for session replication, you can use CAS's support for Redis session replication.

This feature is enabled via the following module:

```xml
<dependency>
  <groupId>org.apereo.cas</groupId>
  <artifactId>cas-server-support-session-redis</artifactId>
  <version>${cas.version}</version>
</dependency>
```

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#spring-webflow).

### MongoDb Session Replication

If you don't wish to use the native container's strategy for session replication, you can use CAS's support for Mongo session replication.

This feature is enabled via the following module:

```xml
<dependency>
  <groupId>org.apereo.cas</groupId>
  <artifactId>cas-server-support-session-mongo</artifactId>
  <version>${cas.version}</version>
</dependency>
```

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#spring-webflow).

### JDBC Session Replication

If you don't wish to use the native container's strategy for session replication, you can use CAS's support for JDBC session replication.

This feature is enabled via the following module:

```xml
<dependency>
  <groupId>org.apereo.cas</groupId>
  <artifactId>cas-server-support-session-jdbc</artifactId>
  <version>${cas.version}</version>
</dependency>
```

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#spring-webflow).
