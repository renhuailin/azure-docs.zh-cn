---
title: 适用于 SQL API 的 Azure Cosmos DB Apache Spark 3 OLTP 连接器（预览版）发行说明和资源
description: 了解有关适用于 SQL API 的 Azure Cosmos DB Apache Spark 3 OLTP 连接器的信息，包括发行日期、停用日期和 Azure Cosmos DB SQL Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 06/21/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 2107f607d7af3dbcf1a0bed91be2a1e9b9b7edcc
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112466719"
---
# <a name="azure-cosmos-db-apache-spark-3-oltp-connector-for-core-sql-api-release-notes-and-resources"></a>适用于 Core (SQL) API 的 Azure Cosmos DB Apache Spark 3 OLTP 连接器：发行说明和资源
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 更改源 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 3 OLTP 连接器](sql-api-sdk-java-spark-v3.md)
> * [Spark 2 OLTP 连接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 资源提供程序](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

Azure Cosmos DB Spark 3 OLTP 连接器为使用 SQL API 的 Azure Cosmos DB 提供 Apache Spark v3 支持。
[Azure Cosmos DB](introduction.md) 是一种全球分布式数据库服务，它允许开发人员使用各种标准 API（如 SQL、MongoDB、Cassandra、Graph 和表）处理数据。

## <a name="documentation"></a>文档

- [入门](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/quick-start.md)
- [目录 API](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/catalog-api.md)
- [配置参数引用](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/cosmos/azure-cosmos-spark_3-1_2-12/docs/configuration-reference.md)


## <a name="version-compatibility"></a>版本兼容性

| 连接器     | Spark         | 最低 Java 版本 | 支持的 Scala 版本 |
| ------------- | ------------- | -------------------- | -----------------------  |
| 4.0.0         | 3.1.1         |        8             | 2.12                     |

## <a name="download"></a>下载 

可以使用 jar 的 maven 坐标自动从 Maven 将 Spark 连接器安装到 Databricks Runtime 8：`com.azure.cosmos.spark:azure-cosmos-spark_3-1_2-12:4.1.0`

还可以在 SBT 项目中与 Cosmos DB Spark 连接器集成：
```scala
libraryDependencies += "com.azure.cosmos.spark" % "azure-cosmos-spark_3-1_2-12" % "4.1.0"
```

Cosmos DB Spark 连接器适用于 [Maven 中央存储库](https://search.maven.org/artifact/com.azure.cosmos.spark/azure-cosmos-spark_3-1_2-12/)。

### <a name="general"></a>常规

如果遇到任何 bug，请在[此处](https://github.com/Azure/azure-sdk-for-java/issues/new)提交问题。

若要建议新功能或可以进行的更改，请以与提交 bug 相同的方式提交问题。

[!INCLUDE[Changelog](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-cosmos-spark_3-1_2-12/CHANGELOG.md)]

## <a name="next-steps"></a>后续步骤

请参阅[使用 Azure Cosmos DB Spark 3 OLTP 连接器的快速入门指南](create-sql-api-spark.md)。
