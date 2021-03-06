---
layout: default
title: CAS - Couchbase Service Registry
category: Services
---

# Couchbase Service Registry
Couchbase integration is enabled by including the following dependency in the WAR overlay:

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-support-couchbase-service-registry</artifactId>
     <version>${cas.version}</version>
</dependency>
```

[Couchbase](http://www.couchbase.com) is a highly available, open source NoSQL database server based on [Erlang/OTP](http://www.erlang.org) and its mnesia database. The intention of this registry is to leverage the capability of Couchbase server to provide high availability to CAS.

<div class="alert alert-info"><strong>Compatibility</strong><p>Couchbase support in CAS at the moment is limited to Couchbase v4.</p></div>

## Configuration

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#couchbase-service-registry).

The Couchbase integration currently assumes that the service registries are stored in their own buckets. Optionally set passwords for the buckets, optionally setup redundancy and replication as per normal Couchbase configuration.

The only truly mandatory setting is the list of nodes. The other settings are optional, but this is designed to store data in buckets so in reality the bucket property must also be set.

## Auto Initialization

Upon startup and configuration permitting, the registry is able to auto initialize itself from default JSON service definitions available to CAS. See [this guide](AutoInitialization-Service-Management.html) for more info.


## Troubleshooting

To enable additional logging, configure the log4j configuration file to add the following levels:

```xml
...
<Logger name="com.couchbase" level="debug" additivity="false">
    <AppenderRef ref="console"/>
    <AppenderRef ref="file"/>
</Logger>
...
```
