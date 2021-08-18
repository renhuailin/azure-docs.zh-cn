---
title: 排查 Azure Cosmos DB Cassandra API 中的常见错误
description: 本文介绍了 Azure Cosmos DB Cassandra API 中的常见问题，以及如何解决这些问题。
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: 55434b56d249a7fb314098e4a53d3c3782bcfe95
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778052"
---
# <a name="troubleshoot-common-issues-in-the-azure-cosmos-db-cassandra-api"></a>排查 Azure Cosmos DB Cassandra API 中的常见问题

[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

[Azure Cosmos DB](../introduction.md) 中的 Cassandra API 是一个兼容层，它为开源 Apache Cassandra 数据库提供[线路协议支持](cassandra-support.md)。

本文介绍了使用 Azure Cosmos DB Cassandra API 的应用程序的常见错误和解决方案。 如果你遇到的错误没有列出，并且你在执行 [Cassandra 中支持的操作](cassandra-support.md)时遇到错误，但使用原生 Apache Cassandra 时不存在该错误，请[创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

>[!NOTE]
>作为一种完全托管的云原生服务，Azure Cosmos DB 为 Cassandra API 提供了[可用性、吞吐量和一致性保证](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)。 Cassandra API 还有助于实现零维护平台操作和零停机修补。
>
>这些保证在以前的 Apache Cassandra 实现中是不可能的，因此许多 Cassandra API 后端操作与 Apache Cassandra 不同。 建议采用特定的设置和方法，以帮助避免常见错误。

## <a name="nonodeavailableexception"></a>NoNodeAvailableException

此错误是最高级别的包装器异常，它附带了大量可能原因和内部异常，而其中很多异常可能与客户端相关。

常见原因和解决方案：

- Azure 负载均衡器空闲超时：此问题也可能表现为 `ClosedConnectionException`。 若要解决此问题，请在驱动程序中设置“保持连接”设置（参阅[为 Java 驱动程序启用保持连接](#enable-keep-alive-for-the-java-driver)），并增加操作系统中的“保持连接”设置，或[调整 Azure 负载均衡器中的空闲超时](../../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)。

- 客户端应用程序资源耗尽：确保客户端计算机具有足够的资源来完成请求。

## <a name="cant-connect-to-a-host"></a>无法连接到主机

你可能会看到此错误：“无法连接到任何主机，计划在 600000 毫秒内重试”。

此错误可能是由客户端源网络地址转换 (SNAT) 耗尽导致的。 请按照[用于出站连接的 SNAT](../../load-balancer/load-balancer-outbound-connections.md) 中的步骤排查此问题。

此错误也可能是空闲超时问题，Azure 负载均衡器的空闲超时时间默认为 4 分钟。 请参阅[负载均衡器空闲超时](../../load-balancer/load-balancer-tcp-idle-timeout.md?tabs=tcp-reset-idle-portal)。 [为 Java 驱动程序启用“保持连接”](#enable-keep-alive-for-the-java-driver)并将操作系统上的 `keepAlive` 间隔时间设置为小于 4 分钟。

## <a name="overloadedexception-java"></a>OverloadedException (Java)

请求被限制是因为消耗的请求单元总数大于你在键空间或表上预配的请求单元数。

请考虑从 Azure 门户缩放分配给键空间或表的吞吐量（请参阅[弹性缩放 Azure Cosmos DB Cassandra API 帐户](scale-account-throughput.md)）或实施重试策略。

对于 Java，请参阅适用于 [v3.x 驱动程序](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample)和 [v4.x 驱动程序](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4)的重试示例。 另请参阅[适用于 Java 的 Azure Cosmos Cassandra 扩展](https://github.com/Azure/azure-cosmos-cassandra-extensions)。

### <a name="overloadedexception-despite-sufficient-throughput"></a>即使吞吐量充足，也发生了 OverloadedException

即使为请求卷或消耗的请求单位成本预配了足够的吞吐量，系统似乎也会限制请求。 有两个可能的原因：

- 架构级操作：Cassandra API 针对架构级别的操作（CREATE TABLE、ALTER TABLE、DROP TABLE）实施系统吞吐量预算。 此预算应当足以满足生产系统中的架构操作的需求。 但是，如果你有大量架构级别的操作，可能会超出此限制。

  由于预算不是由用户控制的，因此请考虑减少运行的架构操作数量。 如果此操作不能解决问题，或者它不适合你的工作负载，请[创建 Azure 支持请求](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

- 数据倾斜：在 Cassandra API 中预配吞吐量时，会在物理分区中均匀划分，并且每个物理分区都有上限。 如果在一个特定分区中插入或查询大量数据，则即使为该表预配较大的总体吞吐量（请求单位），也可能发生速率限制。

  查看数据模型，确保没有可能导致热分区的过多倾斜。

## <a name="intermittent-connectivity-errors-java"></a>间歇性连接错误 (Java)

连接意外中断或超时。

适用于 Java 的 Apache Cassandra 驱动程序提供了两种原生的重新连接策略：`ExponentialReconnectionPolicy` 和 `ConstantReconnectionPolicy`。 默认为 `ExponentialReconnectionPolicy`。 但是，对于 Azure Cosmos DB Cassandra API，建议将 `ConstantReconnectionPolicy` 的延迟时间设置为 2 秒。

请参阅 [Java 4.x 驱动程序文档](https://docs.datastax.com/en/developer/java-driver/4.9/manual/core/reconnection/)、[Java 3.x 驱动程序文档](https://docs.datastax.com/en/developer/java-driver/3.7/manual/reconnection/)或[为 Java 驱动程序配置 ReconnectionPolicy](#configure-reconnectionpolicy-for-the-java-driver) 示例。

## <a name="error-with-load-balancing-policy"></a>负载均衡策略出错

你可能使用如下所示的代码在 Java Datastax 驱动程序 v3.x 中实现了负载均衡策略：

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

如果 `withLocalDc()` 的值与联系点数据中心不一致，则可能会遇到间歇性错误：`com.datastax.driver.core.exceptions.NoHostAvailableException: All host(s) tried for query failed (no host was tried)`。

实现 [CosmosLoadBalancingPolicy](https://github.com/Azure/azure-cosmos-cassandra-extensions/blob/master/package/src/main/java/com/microsoft/azure/cosmos/cassandra/CosmosLoadBalancingPolicy.java)。 若要使其正常工作，可能需要使用以下代码升级 DataStax：

```java
LoadBalancingPolicy loadBalancingPolicy = new CosmosLoadBalancingPolicy.Builder().withWriteDC("West US").withReadDC("West US").build();
```

## <a name="the-count-fails-on-a-large-table"></a>对大型表的计数失败

针对大量的行运行 `select count(*) from table` 或类似语句时，服务器超时。

如果你使用的是本地 CQLSH 客户端，请更改 `--connect-timeout` 或 `--request-timeout` 设置。 请参阅 [cqlsh：CQL shell](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html)。

如果计数仍然超时，你可以转到 Azure 门户中的“指标”选项卡，选择指标 `document count`，然后为数据库或集合添加一个筛选器（Azure Cosmos DB 中的表模拟），以从 Azure Cosmos DB 后端遥测获取记录计数。 然后，可以将鼠标悬停在生成图上以查看获取记录计数的时间点。

:::image type="content" source="./media/troubleshoot-common-issues/metrics.png" alt-text="指标视图":::

## <a name="configure-reconnectionpolicy-for-the-java-driver"></a>为 Java 驱动程序配置 ReconnectionPolicy

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

## <a name="enable-keep-alive-for-the-java-driver"></a>为 Java 驱动程序启用“保持连接”

### <a name="version-3x"></a>3\.x 版

对于 Java 驱动程序 3.x 版，请在创建群集对象时设置“保持连接”，并确保[在操作系统中启用](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)“保持连接”：

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

对于 Java 驱动程序 4.x 版，请通过替代 `reference.conf` 中的设置来设置“保持连接”，并确保[在操作系统中启用](https://knowledgebase.progress.com/articles/Article/configure-OS-TCP-KEEPALIVE-000080089)“保持连接”：

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
- 了解如何[从原生 Apache Cassandra 迁移到 Azure Cosmos DB Cassandra API](migrate-data-databricks.md)。
