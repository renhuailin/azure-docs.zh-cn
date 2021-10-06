---
title: 删除与 Azure Cache for Redis 配合使用的 TLS 1.0 和 1.1
description: 了解如何在与 Azure Cache for Redis 通信时从应用程序中删除 TLS 1.0 和 1.1
author: curib
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cauribeg
ms.openlocfilehash: 8af62a70d7c33a146c6f749be057f41a500ada58
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537617"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>删除与 Azure Cache for Redis 配合使用的 TLS 1.0 和 1.1

整个行业正趋向于使用传输层安全性 (TLS) 1.2 或更高版本。 TLS 版本 1.0 和 1.1 已知很容易遭到 BEAST 和 POODLE 之类的攻击，并且存在其他常见漏洞和披露 (CVE) 弱点。 此外，它们不支持支付卡行业 (PCI) 合规性标准推荐的新式加密法和加密套件。 此 [TLS 安全性博客](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)详细说明了其中一些漏洞。

作为此项工作的一部分，我们将对 Azure Cache for Redis 进行以下更改：

* **阶段 1：** 对于新创建的缓存实例，我们会将默认的最低 TLS 版本配置为 1.2（以前为 TLS 1.0）。 目前，不会更新现有的缓存实例。 仍可使用 Azure 门户或其他管理 API [将最低 TLS 版本更改](cache-configure.md#access-ports)为 1.0 或 1.1，以实现向后兼容性。
* **阶段 2：** 我们将停止支持 TLS 1.1 和 TLS 1.0。 在此更改之后，应用程序必须使用 TLS 1.2 或更高版本才能与缓存通信。 如果我们迁移 Azure Cache for Redis 服务以仅支持 TLS 1.2 或更高版本，则该服务应可用。

  > [!NOTE]
  > 阶段 2 因新冠疫情而推迟了。 强烈建议你立即开始规划此更改，并主动更新客户端以支持 TLS 1.2 或更高版本。 
  >

在此更改中，我们还将删除对不安全的较早加密套件的支持。 如果为缓存配置最低的版本 TLS 1.2，则受支持的加密套件只有以下套件：

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

本文提供了有关如何检测这些早期 TLS 版本的依赖项并将其从应用程序中删除的一般指导。

这些更改的生效日期为：

| 云                | 阶段 1 开始日期 | 阶段 2 开始日期         |
|----------------------|--------------------|----------------------------|
| Azure（公有云）       |  2020 年 1 月 13 日  | 由于 COVID-19 而推迟  |
| Azure Government     |  2020 年 3 月 13 日    | 由于 COVID-19 而推迟  |
| Azure 德国        |  2020 年 3 月 13 日    | 由于 COVID-19 而推迟  |
| Azure 中国世纪互联 |  2020 年 3 月 13 日    | 由于 COVID-19 而推迟  |

> [!NOTE]
> 阶段 2 因新冠疫情而推迟了。 确定具体日期后，本文将更新。
>

## <a name="check-whether-your-application-is-already-compliant"></a>检查应用程序是否已合规

可以通过将测试或暂存缓存上的“最低 TLS 版本”值设置为 TLS 1.2、然后运行测试，来了解应用程序是否适用于 TLS 1.2。 “最低 TLS 版本”设置位于Azure 门户的缓存实例的[高级设置](cache-configure.md#advanced-settings)中。  如果做出此项更改后，应用程序可继续按预期方式运行，则应用程序可能是合规的。 可能需要将应用程序使用的 Redis 客户端库配置为启用 TLS 1.2，以连接到 Azure Cache for Redis。

## <a name="configure-your-application-to-use-tls-12"></a>将应用程序配置为使用 TLS 1.2

大多数应用程序使用 Redis 客户端库来处理与缓存的通信。 这里说明了如何将以各种编程语言和框架编写的某些流行客户端库配置为使用 TLS 1.2。

### <a name="net-framework"></a>.NET Framework

在 .NET Framework 4.5.2 或更低版本上，Redis .NET 客户端默认使用最低的 TLS 版本；在 .NET Framework 4.6 或更高版本上，则使用最新的 TLS 版本。 如果使用的是较旧版本的 .NET Framework，请手动启用 TLS 1.2：

* **StackExchange.Redis：** 在连接字符串中设置 `ssl=true` 和 `sslprotocols=tls12`。
* **ServiceStack.Redis：** 请按照 [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) 说明操作，并至少需要 ServiceStack.Redis v5.6。

### <a name="net-core"></a>.NET Core

Redis .NET Core 客户端默认为 OS 默认 TLS 版本，此版本取决于 OS 本身。 

根据 OS 版本和已应用的任何修补程序，有效的默认 TLS 版本可能会有所不同。 有关详细信息，请参阅[此文](/dotnet/framework/network-programming/#support-for-tls-12)。

但是，如果使用的是旧版 OS，或者只是想要确定，我们建议通过客户端手动配置首选 TLS 版本。


### <a name="java"></a>Java

Redis Java 客户端基于 Java 版本 6 或更早版本使用 TLS 1.0。 如果在缓存中禁用了 TLS 1.0，则 Jedis、Lettuce 和 Redisson 无法连接到 Azure Cache for Redis。 升级 Java 框架以使用新的 TLS 版本。

对于 Java 7，Redis 客户端默认不使用 TLS 1.2，但可以配置为使用此版本。 Jedis 允许你使用以下代码片段指定基础 TLS 设置：

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Lettuce 和 Redisson 客户端尚不支持指定 TLS 版本。 如果缓存仅接受 TLS 1.2 连接，它们的工作会中断。 我们正在审查这些客户端的修补程序，因此请检查那些包是否有包含此支持的更新版本。

在 Java 8 中，默认情况下会使用 TLS 1.2，并且在大多数情况下都不需要更新客户端配置。 为了安全起见，请测试你的应用程序。

### <a name="nodejs"></a>Node.js

Node Redis 和 IORedis 默认使用 TLS 1.2。

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* 低于 PHP 7 的版本：Predis 仅支持 TLS 1.0。 这些版本不支持 TLS 1.2；必须升级才能使用 TLS 1.2。
 
* PHP 7.0 到 PHP 7.2.1：默认情况下，Predis 仅使用 TLS 1.0 或 TLS 1.1。 可以通过以下变通办法来使用 TLS 1.2。 在创建客户端实例时指定 TLS 1.2：

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 及更高版本：Predis 使用最新的 TLS 版本。

#### <a name="phpredis"></a>PhpRedis

PhpRedis 在任何 PHP 版本上均不支持 TLS。

### <a name="python"></a>Python

Redis-py 默认使用 TLS 1.2。

### <a name="go"></a>GO

Redigo 默认使用 TLS 1.2。

## <a name="additional-information"></a>其他信息

- [如何配置 Azure Cache for Redis](cache-configure.md)