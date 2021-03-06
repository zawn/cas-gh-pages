---
layout: default
title: CAS - YubiKey Authentication
category: Multifactor Authentication
---

# YubiKey Authentication

Yubico is a cloud-based service that enables strong, easy-to-use and affordable two-factor authentication with one-time passwords through their flagship product, YubiKey. Once Yubico `clientId` and `secretKey` are obtained, then the configuration options available to use YubiKey devices as a primary authentication source that CAS server could use to authenticate users.

To configure YubiKey accounts and obtain API keys, [refer to the documentation](https://upgrade.yubico.com/getapikey/).

[YubiKey](https://www.yubico.com/products/yubikey-hardware) authentication components are enabled by including the following dependencies in the WAR overlay:

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-support-yubikey</artifactId>
     <version>${cas.version}</version>
</dependency>
```

## Administrative Endpoints

The following endpoints are provided by CAS:

| Endpoint                   | Description                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `yubikeyAccountRepository` | Manage and control [Google Authenticator account records](YubiKey-Authentication.html). A `GET` operation produces a list of all account records. A `DELETE` operation will delete all account records. A `GET` operation produces with a parameter selector of `/{username}` will list the record assigned to the user. A `DELETE` operation produces with a parameter selector of `/{username}` will remove the record assigned to the user. |

## Configuration

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#yubikey).

By default, all YubiKey accounts for users are allowed to authenticate. Devices that need to be authorized for authentication need to have followed an out-of-band registration process where the record for them is found in one of the following storage backends. Upon authentication, CAS will begin to search the configured registration database for matching record for the authenticated user and device in order to allow for a successful authentication event.

### JSON

Registration records may be tracked inside a JSON file, provided the file path is specified in CAS settings. See [review this guide](../configuration/Configuration-Properties.html#yubikey) for more info.

The JSON structure is a map of user id to yubikey public id representing any particular device:

```json
{
  "uid1": ["yubikeyPublicId1"],
  "uid2": ["yubikeyPublicId2"]
}
```

### REST

Registration records can be managed via an external REST API. See [review this guide](../configuration/Configuration-Properties.html#yubikey) for more info.

The following endpoints are expected to be available and implemented by the REST API:

| METHOD   | Endpoint       | Description                                                                           |
| -------- | -------------- | ------------------------------------------------------------------------------------- |
| `GET`    | `/`            | Get all registered records.                                                           |
| `GET`    | `/{user}`      | Get all registered records for the user.                                              |
| `DELETE` | `/`            | Delete all registered records.                                                        |
| `DELETE` | `/{user}`      | Delete all registered records for the user.                                           |
| `DELETE` | `/{user}/{id}` | Delete the registered device by its id from the the registration record for the user. |
| `POST`   | `/`            | Store registration records passed as the request body.                                |

### Permissive

Registration records may be specified statically via CAS settings in form of a map that links registered usernames with the public id of the YubiKey device. See [review this guide](../configuration/Configuration-Properties.html#yubikey) for more info.

### JPA

Support is enabled by including the following dependencies in the WAR overlay:

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-support-yubikey-jpa</artifactId>
     <version>${cas.version}</version>
</dependency>
```

The expected database schema that is automatically created and configured by CAS contains a single table as `YubiKeyAccount` with the following fields:

| Field      | Description                                                      |
| ---------- | ---------------------------------------------------------------- |
| `id`       | Unique record identifier, acting as the primary key.             |
| `publicId` | The public identifier/key of the device used for authentication. |
| `username` | The username whose device is registered.                         |

### CouchDb

Support is enabled by including the following dependencies in the WAR overlay:

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-support-yubikey-couchdb</artifactId>
     <version>${cas.version}</version>
</dependency>
```

The registration records are kept inside a single CouchDb database of your choosing that will be auto-created by CAS. The structure of this database's documents is as follows:

| Field      | Description                                                      |
| ---------- | ---------------------------------------------------------------- |
| `id`       | Unique record identifier, acting as the primary key.             |
| `publicId` | The public identifier/key of the device used for authentication. |
| `username` | The username whose device is registered.                         |

### Redis

Support is enabled by including the following dependencies in the WAR overlay:

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-support-yubikey-redis</artifactId>
     <version>${cas.version}</version>
</dependency>
```

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#yubikey).

### DynamoDb

Support is enabled by including the following dependencies in the WAR overlay:

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-support-yubikey-dynamodb</artifactId>
     <version>${cas.version}</version>
</dependency>
```

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#yubikey).

### MongoDb

Support is enabled by including the following dependencies in the WAR overlay:

```xml
<dependency>
     <groupId>org.apereo.cas</groupId>
     <artifactId>cas-server-support-yubikey-mongo</artifactId>
     <version>${cas.version}</version>
</dependency>
```

The registration records are kept inside a single MongoDb collection of your choosing that will be auto-created by CAS. The structure of this collection is as follows:

| Field      | Description                                                      |
| ---------- | ---------------------------------------------------------------- |
| `id`       | Unique record identifier, acting as the primary key.             |
| `publicId` | The public identifier/key of the device used for authentication. |
| `username` | The username whose device is registered.                         |

To see the relevant list of CAS properties, please [review this guide](../configuration/Configuration-Properties.html#yubikey).

### Custom

If you wish to plug in a custom registry implementation that would determine which users are allowed to use their YubiKey accounts for authentication, you may plug in a custom implementation of the `YubiKeyAccountRegistry` that allows you to provide a mapping between usernames and YubiKey public keys.


```java
package org.apereo.cas.support.yubikey;

@Configuration("myYubiKeyConfiguration")
@EnableConfigurationProperties(CasConfigurationProperties.class)
public class MyYubiKeyConfiguration {

  @Bean
  public YubiKeyAccountRegistry yubiKeyAccountRegistry() {
      ...
  }
}
```

## Device Registrations

In the event that a new YubiKey should be registered, it may be desirable to execute additional validation processes before the account is registered with the underlying store. By default, the device registration step only verifies the device token. If you wish to extend this behavior, you can design your own validator that cross-checks the account against alternative sources and databases for validity and authorization:

```java
package org.apereo.cas.support.yubikey;

@Configuration("myYubiKeyConfiguration")
@EnableConfigurationProperties(CasConfigurationProperties.class)
public class MyYubiKeyConfiguration {

  @Bean
  public YubiKeyAccountValidator yubiKeyAccountValidator() {
      ...
  }
}
```

[See this guide](../configuration/Configuration-Management-Extensions.html) to learn more about how to register configurations into the CAS runtime.

## REST Protocol Credential Extraction

In the event that the [CAS REST Protocol](../protocol/REST-Protocol.html) is turned on, a special credential extractor is injected into the REST authentication engine in order to recognize YubiKey credentials and authenticate them as part of the REST request. The expected parameter name in the request body is `yubikeyotp`.
