---
title: 排查 Azure Cosmos DB Cassandra API 中的常见错误
description: 本文档讨论了排查 Azure Cosmos DB Cassandra API 中遇到的常见问题的方法
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: 75ce307056d24f84e9f72f746eb77b09b89e21b2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927493"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>排查 Azure Cosmos DB Cassandra API 中的常见问题
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 是一个兼容层，它为常用的开源 Apache Cassandra 数据库提供[线路协议支持](cassandra-support.md)，由 [Azure Cosmos DB](./introduction.md) 提供支持。 作为一种完全托管的云原生服务，Azure Cosmos DB 为 Cassandra API 提供了[可用性、吞吐量和一致性保证](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)。 Apache Cassandra 的传统实现不能提供这些保证。 Cassandra API 还有助于实现零维护平台操作和零停机修补。 因此，它的许多后端操作不同于 Apache Cassandra，建议你采用特定的设置和方法来避免常见错误。 

本文介绍了使用 Azure Cosmos DB Cassandra API 的应用程序的常见错误和解决方案。

## <a name="common-errors-and-solutions"></a>常见错误和解决方法

| 错误               |  说明             | 解决方案  |
|---------------------|--------------------------|-----------|
| OverloadedException (Java) | 消耗的请求单位总数大于在密钥空间或表中预配的请求单位。 因此，请求受到限制。 | 请考虑从 Azure 门户中缩放分配给密钥空间或表的吞吐量（有关 Cassandra API 中的缩放操作，请参阅[此文](manage-scale-cassandra.md)），也可以实施一个重试策略。 对于 Java，请参阅适用于 [v3.x 驱动程序](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)和 [v4.x 驱动程序](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)的重试示例。 另请参阅[适用于 Java 的 Azure Cosmos Cassandra 扩展](https://github.com/Azure/azure-cosmos-cassandra-extensions) |
| 即使吞吐量足够，也发生 OverloadedException (Java) | 系统好像对请求进行了限制，尽管针对请求量和/或消耗的请求单位成本预配了足够的吞吐量。  | Cassandra API 针对架构级别的操作（CREATE TABLE、ALTER TABLE、DROP TABLE）实施系统吞吐量预算。 此预算应当足以满足生产系统中的架构操作的需求。 但是，如果你有大量架构级别的操作，则可能会超出此限制。 由于此预算不是用户控制的，因此你需要考虑减少正在运行的架构操作的数量。 如果采取此措施不能解决问题，或者它不适合你的工作负荷，请[创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。|
| ClosedConnectionException (Java) | 成功连接并空闲一段时间后，应用程序无法连接| 此错误可能是由 Azure LoadBalancers 的空闲超时（4 分钟）引起的。 在驱动程序中设置“保持连接”设置（如下所示），在操作系统中增大“保持连接”设置，或[在 Azure 负载均衡器中调整空闲超时](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)。 |
| 其他间歇性连接错误 (Java) | 连接意外中断或超时 | 适用于 Java 的 Apache Cassandra 驱动程序提供了两种原生的重新连接策略：`ExponentialReconnectionPolicy` 和 `ConstantReconnectionPolicy`。 默认为 `ExponentialReconnectionPolicy`。 但是，对于 Azure Cosmos DB Cassandra API，建议将 `ConstantReconnectionPolicy` 的延迟设置为 2 秒。 有关 Java v4.x 驱动程序，请参阅[驱动程序文档](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)；有关 Java 3.x 指南，请参阅[此文](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)（另请参阅以下示例）。|

如果上面未列出你的错误，并且在执行 [Cassandra API 中支持的操作](cassandra-support.md)时遇到错误，但使用原生 Apache Cassandra 时不存在该错误，请[创建一个 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="configuring-reconnectionpolicy-for-java-driver"></a>为 Java 驱动程序配置 ReconnectionPolicy

### <a name="version-3x"></a>3\.x 版

对于 Java 驱动程序 3.x 版，请在创建群集对象时配置重新连接策略：

```java
import com.datastax.driver.core.policies.ConstantReconnectionPolicy;

Cluster.builder()
  .withReconnectionPolicy(new ConstantReconnectionPolicy(2000))
  .build();
```

### <a name="version-4x"></a>版本 4.x

对于 Java 驱动程序 4.x 版，请通过在 `reference.conf` 文件中替代相关设置来配置重新连接策略：

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

## <a name="enable-keep-alive-for-java-driver"></a>为 Java 驱动程序启用“保持连接”

### <a name="version-3x"></a>3\.x 版

对于 Java 驱动程序的 3.x 版，请在创建群集对象时设置“保持连接”，并确保[在操作系统中启用](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)“保持连接”：

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

对于 Java 驱动程序的 4.x 版，请通过替代 `reference.conf` 中的设置来设置“保持连接”，并确保[在操作系统中启用](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)“保持连接”：

```xml
datastax-java-driver {
  advanced {
    socket{
      keep-alive = true
    }
}
```

## <a name="next-steps"></a>后续步骤

- 了解 Azure Cosmos DB Cassandra API 中[支持的功能](cassandra-support.md)。
- 了解如何[从原生 Apache Cassandra 迁移到 Azure Cosmos DB Cassandra API](cassandra-migrate-cosmos-db-databricks.md)