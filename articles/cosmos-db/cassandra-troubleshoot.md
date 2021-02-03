---
title: 排查 Azure Cosmos DB Cassandra API 中的常见错误
description: 本文档讨论了排查 Azure Cosmos DB Cassandra API 中遇到的常见问题的方法
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: thvankra
ms.openlocfilehash: 6d9a74729768a326379b5efddb864a4fee02fa59
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493204"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-db-cassandra-api"></a>排查 Azure Cosmos DB Cassandra API 中的常见问题
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Azure Cosmos DB 中的 Cassandra API 是一个兼容层，它为常用的开源 Apache Cassandra 数据库提供[线路协议支持](cassandra-support.md)，由 [Azure Cosmos DB](./introduction.md) 提供支持。 作为一种完全托管的云原生服务，Azure Cosmos DB 为 Cassandra API 提供了[可用性、吞吐量和一致性保证](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)。 Apache Cassandra 的传统实现不能提供这些保证。 Cassandra API 还有助于实现零维护平台操作和零停机修补。 因此，它的许多后端操作不同于 Apache Cassandra，建议你采用特定的设置和方法来避免常见错误。 

本文介绍了使用 Azure Cosmos DB Cassandra API 的应用程序的常见错误和解决方案。 如果错误未在下面列出，并且 [在 Cassandra API](cassandra-support.md)中执行支持的操作时遇到错误，在 *使用本机 Apache Cassandra 时不* 会出现错误，请 [创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="nonodeavailableexception"></a>NoNodeAvailableException
这是一个最高级别的包装器异常，其中包含大量可能的原因和内部异常，其中很多都可与客户端相关。 
### <a name="solution"></a>解决方案
下面是一些常见的原因和解决方案： 
- Azure LoadBalancers 的空闲超时：这也可能表现为 `ClosedConnectionException` 。 若要解决此问题，请在驱动程序中设置 "保持活动状态" 设置 ([如下](#enable-keep-alive-for-java-driver) 所示) 并增加操作系统中的 keep-alive 设置，或 [调整 Azure 负载均衡器中的空闲超时](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)。 
- **客户端应用程序资源耗尽：** 确保客户端计算机有足够的资源来完成请求。 

## <a name="cannot-connect-to-host"></a>无法连接到主机
你可能会看到此错误： `Cannot connect to any host, scheduling retry in 600000 milliseconds` 。 

### <a name="solution"></a>解决方案
这可能是客户端上的 SNAT 消耗。 若要解决此问题，请按照 SNAT 中的步骤 [进行出站连接](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) 。 这也可能是一个空闲超时问题，其中默认情况下，Azure 负载均衡器有4分钟的空闲超时。 请参阅 [负载均衡器空闲超时](../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)的文档。 启用 "tcp-从驱动程序设置保持活动状态" (参阅 [下面](#enable-keep-alive-for-java-driver) 的) 并将 `keepAlive` 操作系统时间间隔设置为小于4分钟。

 

## <a name="overloadedexception-java"></a>OverloadedException (Java)
消耗的请求单位总数大于在密钥空间或表中预配的请求单位。 因此，请求受到限制。
### <a name="solution"></a>解决方案
请考虑从 Azure 门户中缩放分配给密钥空间或表的吞吐量（有关 Cassandra API 中的缩放操作，请参阅[此文](manage-scale-cassandra.md)），也可以实施一个重试策略。 对于 Java，请参阅适用于 [v3.x 驱动程序](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)和 [v4.x 驱动程序](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)的重试示例。 另请参阅 [适用于 Java 的 Azure Cosmos Cassandra 扩展](https://github.com/Azure/azure-cosmos-cassandra-extensions)。

### <a name="overloadedexception-even-with-sufficient-throughput"></a>OverloadedException 甚至具有足够的吞吐量 
尽管为请求量和/或消耗的请求单位成本预配了足够的吞吐量，系统似乎会阻止请求。 有两个可能的原因会导致意外的速率限制：
- **架构级别操作：** Cassandra API 实现了 (CREATE TABLE、ALTER TABLE、DROP TABLE) 的架构级别操作的系统吞吐量预算。 此预算应当足以满足生产系统中的架构操作的需求。 但是，如果你有大量架构级别的操作，则可能会超出此限制。 由于此预算不是用户控制的，因此您需要考虑减少正在运行的架构操作的数量。 如果采取此措施不能解决问题，或者它不适合你的工作负荷，请[创建 Azure 支持请求](../azure-portal/supportability/how-to-create-azure-support-request.md)。
- **数据歪斜：** 在 Cassandra API 中设置吞吐量时，它在物理分区之间平均分配，每个物理分区都有上限。 如果要在一个特定的分区中插入或查询大量数据，则可以按速率进行限制，尽管需要为该表设置大量 (请求单位) 。 查看您的数据模型，确保没有可能导致热分区的过多偏差。 

## <a name="intermittent-connectivity-errors-java"></a>Java)  (间歇连接错误 
连接意外断开或超时。

### <a name="solution"></a>解决方案 
适用于 Java 的 Apache Cassandra 驱动程序提供了两种原生的重新连接策略：`ExponentialReconnectionPolicy` 和 `ConstantReconnectionPolicy`。 默认为 `ExponentialReconnectionPolicy`。 但是，对于 Azure Cosmos DB Cassandra API，建议将 `ConstantReconnectionPolicy` 的延迟设置为 2 秒。 请参阅 Java 1.x 驱动程序的 [驱动程序文档](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/) [。有关 java](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/) 1.x 的指南，请参阅下面的为 [java 驱动程序配置 ReconnectionPolicy 的](#configuring-reconnectionpolicy-for-java-driver) 示例。

## <a name="error-with-load-balancing-policy"></a>负载平衡策略出错

如果已在 Java Datastax 驱动程序的 v3. x 中实现了负载平衡策略，代码如下所示：

```java
cluster = Cluster.builder()
        .addContactPoint(cassandraHost)
        .withPort(cassandraPort)
        .withCredentials(cassandraUsername, cassandraPassword)
        .withPoolingOptions(new PoolingOptions() .setConnectionsPerHost(HostDistance.LOCAL, 1, 2)
                .setMaxRequestsPerConnection(HostDistance.LOCAL, 32000).setMaxQueueSize(Integer.MAX_VALUE))
        .withSSL(sslOptions)
        .withLoadBalancingPolicy(DCAwareRoundRobinPolicy.builder().withLocalDc("West US").build())
        .withQueryOptions(new QueryOptions().setConsistencyLevel(ConsistencyLevel.LOCAL_QUORUM))
        .withSocketOptions(getSocketOptions())
        .build();
```

如果的值与 `withLocalDc()` 联系点数据中心不匹配，则可能会遇到非常间歇的错误： `com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)` 。 

### <a name="solution"></a>解决方案 
实现 [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java) (可能需要升级 datastax 次版本，使其) 运行：

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="count-fails-on-large-table"></a>对大型表的计数失败
在运行 `select count(*) from table` 或类似于大量行时，服务器将超时。

### <a name="solution"></a>解决方案 
如果使用本地 CQLSH 客户端，则可以尝试更改 `--connect-timeout` 或 `--request-timeout` 设置 ([在此处](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)) 了解更多详细信息。 如果这还不够并且计数仍超时，可以通过转到 Azure 门户中的 "指标" 选项卡，选择度量值 `document count` ，然后为数据库或 (集合添加筛选器（在 Azure Cosmos DB) 中的表模拟）来获取 Azure Cosmos DB 的后端遥测的记录计数。 然后，您可以将鼠标悬停在生成的关系图上，以获取记录数的计数。

:::image type="content" source="./media/cassandra-troubleshoot/metrics.png" alt-text="指标视图":::


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