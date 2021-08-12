---
title: Azure Cosmos DB：批量执行程序 Java API、SDK 和资源
description: 了解有关批量执行程序 Java API 和 SDK 的所有信息，包括发布日期、停用日期和 Azure Cosmos DB 批量执行程序 Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 09fa626e77e20feff55f7b17807754ac1d2b873f
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/07/2021
ms.locfileid: "111568839"
---
# <a name="java-bulk-executor-library-download-information"></a>Java 批量执行程序库：下载信息
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

| | 链接/注释 |
|---|---|
|**说明**|批量执行程序库允许客户端应用程序在 Azure Cosmos DB 帐户中执行批量操作。 批量执行程序库提供 BulkImport 和 BulkUpdate 命名空间。 BulkImport 模块可以批量以优化方式引入文档，以便最大程度地使用为集合配置的吞吐量。 BulkUpdate 模块可以作为修补程序批量更新 Azure Cosmos 容器中的现有数据。|
|**SDK 下载**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**GitHub 中的批量执行程序库**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API 文档**| [Java API 参考文档](/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**入门**|[批量执行程序库 Java SDK 入门](bulk-executor-java.md)|
|受支持的最小运行时|[Java 开发工具包 (JDK) 7+](/java/azure/jdk/)|

## <a name="release-notes"></a>发行说明
### <a name="2123"></a><a name="2.12.3"></a>2.12.3

* 修复了当 `GoneException` 包装在 `IllegalStateException` 中时的重试策略 - 此更改是确保在出现 410 时刷新网关缓存所必需的，这样 Spark 连接器（适用于 Spark 2.4）便可以使用自定义重试策略，使查询能够在分区拆分期间获得成功

### <a name="2122"></a><a name="2.12.2"></a>2.12.2

* 修复了导致文档在出现暂时性错误时不始终导入的问题。

### <a name="2121"></a><a name="2.12.1"></a>2.12.1

* 升级到使用最新的 Cosmos Core SDK 版本。

### <a name="2120"></a><a name="2.12.0"></a>2.12.0

* 改进了通过 Spark 连接器为批量操作提供的 RU 预算的处理。 最初的一次性批量导入是使用 baseBatchSize 从 spark 连接器执行的，并且会收集上述批量导入的 RU 消耗量。
  基于以上 RU 消耗计算 miniBatchSizeAdjustmentFactor，并据此调整小型批处理大小。 根据每个批量导入的运行时间和消耗的 RU，计算休眠持续时间，以限制每秒 RU 消耗量，并用于在下次批量导入之前暂停线程。

### <a name="2110"></a><a name="2.11.0"></a>2.11.0

* 修复了在使用嵌套分区键时阻止批量更新的 bug

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* 修复了 DocumentAnalyzer.java，以从 JSON 中正确提取嵌套的分区键值。

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* 在 BulkDelete 操作中添加了相应的功能，以便在发生特定的失败后重试，并向用户返回可重试的失败列表。

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Cosmos SDK 版本 2.4.7 的更新。

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* 修复了“mergeAll”，使之能够在遇到“id”和分区键值时继续运行，以便将“id”和分区键值后面的所有已修补文档属性添加到更新的项列表。

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* 已将起始并发度更新为 1，并增加了 minibatch 的调试日志。