---
title: 排查 Azure Cosmos DB Cassandra API 中的常见错误
description: 此文档讨论了解决 Azure Cosmos DB 中遇到的常见问题的方法 Cassandra API
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: c969e4fac3ae30088cfe47a7b0edff22c578cb8b
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802348"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>排查 Azure Cosmos DB Cassandra API 中的常见问题
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 是一个兼容层，为流行的开源 Apache Cassandra 数据库提供 [线路协议支持](cassandra-support.md) ，并由 [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction)提供支持。 作为完全托管的云本机服务，Azure Cosmos DB 提供 Cassandra API 的 [可用性、吞吐量和一致性保证](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/) 。 Apache Cassandra 的传统实现不能保证这些保证。 Cassandra API 还有助于实现零维护平台操作和零停机修补。 因此，其中的一些后端操作不同于 Apache Cassandra，因此建议采用特定的设置和方法来避免常见错误。 

本文介绍使用 Azure Cosmos DB Cassandra API 的应用程序的常见错误和解决方案。

## <a name="common-errors-and-solutions"></a>常见错误和解决方法

| 错误               |  说明             | 解决方案  |
|---------------------|--------------------------|-----------|
| OverloadedException (Java)  | 使用的请求单位总数大于密钥空间或表上预配的请求单位数。 因此会限制请求。 | 请考虑从 Azure 门户缩放分配给密钥空间或表的吞吐量 (在 [此处](manage-scale-cassandra.md) 查看 Cassandra API) 中的缩放操作，或者可以实现重试策略。 对于 Java，请参阅针对 [v3. x 驱动程序](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) 和 [v4 驱动程序](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)的重试示例。 另请参阅 [适用于 Java 的 Azure Cosmos Cassandra 扩展](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| 即使有足够的吞吐量，OverloadedException (Java)  | 尽管为请求量和/或消耗的请求单位成本预配了足够的吞吐量，系统似乎会限制请求  | Cassandra API 实现了 (CREATE TABLE、ALTER TABLE、DROP TABLE) 的架构级别操作的系统吞吐量预算。 此预算应足以满足生产系统中的架构操作。 但是，如果您有大量的架构级操作，则可能会超出此限制。 由于此预算不是用户控制的，因此您需要考虑减少正在运行的架构操作的数量。 如果采取此操作并不能解决问题，或者工作负荷不可行，请 [创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。|
| ClosedConnectionException (Java)  | 成功连接后的一段空闲时间后，应用程序无法连接| 此错误可能是由于 Azure LoadBalancers 的空闲超时（这是4分钟）引起的。 在驱动程序中设置 "保持活动状态" 设置 (参阅下面的) 并增加操作系统中的 keep-alive 设置，或 [调整 Azure 负载均衡器中的空闲超时](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)。 |
| Java)  (的其他间歇性连接错误 | 连接意外中断或超时 | 适用于 Java 的 Apache Cassandra 驱动程序提供了两种本机重新连接策略： `ExponentialReconnectionPolicy` 和 `ConstantReconnectionPolicy` 。 默认值为 `ExponentialReconnectionPolicy`。 但是，对于 Azure Cosmos DB Cassandra API，建议 `ConstantReconnectionPolicy` 将延迟为2秒。 请参阅适用于 java 1.x 驱动程序的 [驱动程序文档](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/) [，并查看](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) 有关 java 1.x 指南的说明 (另请参阅下面) 的示例。|

如果上面未列出你的错误，并且在 Cassandra API 中执行 [支持的操作](cassandra-support.md)时遇到错误，在 *使用本机 Apache Cassandra 时不* 会出现错误，请 [创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="configuring-reconnectionpolicy-for-java-driver"></a>为 Java 驱动程序配置 ReconnectionPolicy

### <a name="version-3x"></a>3\.x 版

对于 Java 驱动程序的版本1.x，请在创建群集对象时配置重新连接策略：

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>版本 4.x

对于版本4.x 的 Java 驱动程序，请通过覆盖文件中的设置来配置重新连接策略 `reference.conf` ：

```xml
datastax-java-driver {
  advanced {
    reconnection-policy{
      # The driver provides two implementations out of the box: ExponentialReconnectionPolicy and
      # ConstantReconnectionPolicy. We recommend ConstantReconnectionPolicy for Cassandra API, with 
      # base-delay of 2 seconds.
      class = ConstantReconnectionPolicy
      base-delay = 2 second
    }
}
```

## <a name="enable-keep-alive-for-java-driver"></a>启用 Java 驱动程序的 keep-alive

### <a name="version-3x"></a>3\.x 版

对于 Java 驱动程序的版本1.x，请在创建群集对象时将其设置为 keep-alive，并确保 [在操作系统中启用](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)keep-alive：

```java
import java.net.SocketOptions;
    
SocketOptions options = new SocketOptions();
options.setKeepAlive(true);
cluster = Cluster.builder().addContactPoints(contactPoints).withPort(port)
  .withCredentials(cassandraUsername, cassandraPassword)
  .withSocketOptions(options)
  .build();
```

### <a name="version-4x"></a>版本 4.x

对于版本4.x 的 Java 驱动程序，请通过在中覆盖设置来设置 keep-alive， `reference.conf` 并确保 [在操作系统中启用](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)keep-alive：

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>后续步骤

- 了解 Azure Cosmos DB Cassandra API 中 [支持的功能](cassandra-support.md) 。
- 了解如何 [从本机 Apache Cassandra 迁移到 Azure Cosmos DB Cassandra API](cassandra-migrate-cosmos-db-databricks.md)

