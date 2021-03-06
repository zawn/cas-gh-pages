---
layout: default
title: CAS - MongoDb Ticket Registry
category: Ticketing
---

# MongoDb Ticket Registry

MongoDb ticket registry integration is enabled by including the following dependency in the WAR overlay:

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-support-mongo-ticket-registry</artifactId>
     <version>${cas.version}</version>
</dependency>
```

This registry stores tickets in one or more [MongoDb](https://www.mongodb.com/) instances. Tickets are auto-converted and wrapped into document objects as JSON. Special indices are created to let MongoDb handle the expiration of each document and cleanup tasks. Note that CAS generally tries to  create the relevant collections automatically to manage different ticket types.

## Configuration

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#mongodb-ticket-registry).

## Troubleshooting

To enable additional logging, configure the log4j configuration file to add the following levels:

```xml
...
<Logger name="com.mongo" level="debug" additivity="false">
    <AppenderRef ref="console"/>
    <AppenderRef ref="file"/>
</Logger>
...
```
