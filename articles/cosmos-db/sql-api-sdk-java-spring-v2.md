---
title: 用于 SQL API 的Spring Data Azure Cosmos DB v2 的发行说明和资源
description: 了解有关用于 SQL API 的 Spring Data Azure Cosmos DB v2 的信息，包括发行日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 04/06/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6bc5566bdc56fa95eeaa466f1afb437e4d12e80c
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2021
ms.locfileid: "112232293"
---
# <a name="spring-data-azure-cosmos-db-v2-for-core-sql-api-release-notes-and-resources"></a>适用于 Core (SQL) API 的 Spring Data Azure Cosmos DB v2：发行说明和资源
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

 通过适用于 Core (SQL) 的 Spring Data Azure Cosmos DB 版本 2，开发人员可以在 Spring 应用程序中使用 Azure Cosmos DB。 Spring Data Azure Cosmos DB 公开 Spring Data 接口，以便操作数据库和集合、使用文档和发出查询。 同一 Maven 项目中同时支持 Sync 和 Async (Reactive) API。 

[Spring Framework](https://spring.io/projects/spring-framework) 是一种简化 Java 应用程序开发的编程和配置模型。 Spring 使用依赖项注入来简化应用程序的“管道”。 由于 Spring 使得生成和测试应用程序变得更加简单，因此许多开发人员都喜欢 Spring。 [Spring Boot](https://spring.io/projects/spring-boot) 重视 Web 应用程序和微服务的开发，扩展了对管道的处理。 [Spring Data](https://spring.io/projects/spring-data) 是一种编程模型，用于从 Spring 或 Spring Boot 应用程序的上下文中访问数据存储（如 Azure Cosmos DB）。 

可在 [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) 应用程序中使用 Spring Data Azure Cosmos DB。

> [!IMPORTANT]  
> 这些发行说明适用于 Spring Data Azure Cosmos DB 的版本 2。 可以[在此处找到版本 3 的发行说明](sql-api-sdk-java-spring-v3.md)。 
>
> Spring Data Azure Cosmos DB 仅支持 SQL API。
>
> 请参阅以下文章，了解其他 Azure Cosmos DB API 上的 Spring Data：
> * [将适用于 Apache Cassandra 的 Spring Data 用于 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [将 Spring Data MongoDB 用于 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
>
> 希望快速开始？
> 1. 安装[最低支持的 Java 运行时版本，JDK 8](/java/azure/jdk/)，以便可以使用 SDK。
> 2. 使用 [Starter](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) 创建 Spring Data Azure Cosmos DB 应用。 操作起来非常简单！
> 3. 通过 [Spring Data Azure Cosmos DB 开发人员指南](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb)了解基本的 Azure Cosmos DB 请求。
>
> 可使用 [Spring Initializr](https://start.spring.io/) 快速启动 Spring Boot Starter 应用！
>

## <a name="resources"></a>资源

| 资源 | 链接 |
|---|---|
| **SDK 下载** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API 文档** | [Spring Data Azure Cosmos DB 参考文档]() |
|**参与编写 SDK** | [GitHub 上的 Spring Data Azure Cosmos DB 存储库](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring Boot Starter**| [适用于 Java 的 Azure Cosmos DB Spring Boot Starter 客户端库](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**用于 Azure Cosmos DB 的 Spring TODO 应用示例**| [应用服务 Linux 中的端到端 Java 体验（第 2 部分）](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**开发人员指南** | [Spring Data Azure Cosmos DB 开发人员指南](/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**使用 Starter** | [如何将 Spring Boot Starter 与 Azure Cosmos DB SQL API 配合使用](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [适用于 Azure Cosmos DB Spring Boot Starter 的 GitHub 存储库](https://github.com/MicrosoftDocs/azure-dev-docs/blob/master/articles/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db.md) |
|**Azure 应用服务示例** | [如何将 Spring 和 Cosmos DB 与 Linux 上的应用服务配合使用](/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [TODO 应用示例](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>版本历史记录

### <a name="230-may-21-2020"></a>2.3.0（2020 年 5 月 21 日）
#### <a name="new-features"></a>新增功能
* 将 Spring Boot 版本更新为 2.3.0。


### <a name="225-may-19-2020"></a>2.2.5（2020 年 5 月 19 日）
#### <a name="new-features"></a>新增功能
* 将 Azure Cosmos DB 版本更新为 3.7.3。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
* 包含来自 Azure Cosmos DB SDK 3.7.3 的内存泄漏修复和 Netty 版本升级。

### <a name="224-april-6-2020"></a>2.2.4（2020 年 4 月 6 日）
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了 `allowTelemetry` 标志，以考虑到 `CosmosDbConfig` 中的问题。
* 修复了容器上的 `TTL` 属性。

### <a name="223-february-25-2020"></a>2.2.3（2020 年 2 月 25 日）
#### <a name="new-features"></a>新增功能
* 通过分区键 API 添加了新的 `findAll`。
* 将 Azure Cosmos DB 版本更新为 3.7.0。
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了 `collectionName` -> `containerName`。
* 修复了 `entityClass` 和 `domainClass` -> `domainType`。
* 修复了以下问题：返回存储库保存的实体集合而不是输入实体。

### <a name="2110-february-25-2020"></a>2.1.10（2020 年 2 月 25 日）
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 向后移植修复了以下问题：返回存储库保存的实体集合而不是输入实体

### <a name="222-january-15-2020"></a>2.2.2（2020 年 1 月 15 日）
#### <a name="new-features"></a>新增功能
* 将 Azure Cosmos DB 版本更新为 3.6.0。
#### <a name="key-bug-fixes"></a>关键 bug 修复

### <a name="221-december-31-2019"></a>2.2.1（2019 年 12 月 31 日）
#### <a name="new-features"></a>新增功能
* 将 Azure Cosmos DB SDK 版本更新为 3.5.0。
* 添加了注释字段以启用或禁用自动集合创建。
* 改进了异常处理。 通过 `CosmosDBAccessException` 公开 `CosmosClientException`。
* 通过 `ResponseDiagnostics` 公开 `requestCharge` 和 `activityId`。
#### <a name="key-bug-fixes"></a>关键 bug 修复
* SDK 3.5.0 更新修复了以下问题：Cosmos DB HTTP 响应标头大于 8192 字节时出现异常；ConsistencyPolicy.defaultConsistencyLevel() 在有限过期和前缀一致性上失败。
* 修复了 `findById` 方法的行为。 以前，如果找不到实体，则此方法返回空值，而不是引发异常。
* 修复了使用 `CosmosPageRequest` 时未在下一页应用排序的 bug。

### <a name="219-december-26-2019"></a>2.1.9（2019 年 12 月 26 日）
#### <a name="new-features"></a>新增功能
* 添加了注释字段以启用或禁用自动集合创建。
#### <a name="key-bug-fixes"></a>关键 Bug 修复
*  修复了 `findById` 方法的行为。 以前，如果找不到实体，则此方法返回空值，而不是引发异常。

### <a name="220-october-21-2019"></a>2.2.0（2019 年 10 月 21 日）
#### <a name="new-features"></a>新增功能
* 完整的 Reactive Cosmos Repository 支持。
* Azure Cosmos DB 请求诊断字符串和查询指标支持。
* Azure Cosmos DB SDK 版本更新为 3.3.1。
* Spring Framework 版本升级为 5.2.0.RELEASE。
* Spring Data Commons 版本升级为 2.2.0.RELEASE。
* 添加了 `findByIdAndPartitionKey` 和 `deleteByIdAndPartitionKey` API。
* 从 azure-documentdb 中删除了依赖项。
* 将 DocumentDB 重命名为了 Azure Cosmos DB。
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了以下问题：pageSize 小于存储库中的总项时排序引发异常。

### <a name="218-october-18-2019"></a>2.1.8（2019 年 10 月 18 日）
#### <a name="new-features"></a>新增功能
* 弃用了 DocumentDB API。
* 添加了 `findByIdAndPartitionKey` 和 `deleteByIdAndPartitionKey` API。
* 添加了基于 `_etag` 的乐观锁定。
* 对文档集合名称启用了 SpEL 表达式。
* 添加 `ObjectMapper` 改进。

### <a name="217-october-18-2019"></a>2.1.7（2019 年 10 月 18 日）
#### <a name="new-features"></a>新增功能
* 添加 Azure Cosmos DB SDK 版本 3 依赖项。
* 添加 Reactive Cosmos Repository。
* 更新 `DocumentDbTemplate` 的实现以使用 Azure Cosmos DB SDK 版本 3。
* 为 Reactive Cosmos Repository 支持添加其他配置更改。

### <a name="212-march-19-2019"></a>2.1.2（2019 年 3 月 19 日）
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 因以下原因删除了 `applicationInsights` 依赖性：
    * 依赖项污染的潜在风险。
    * Java 11 不兼容。
    * 避免对 CPU 和/或内存产生潜在的性能影响。

### <a name="207-march-20-2019"></a>2.0.7（2019 年 3 月 20 日）
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 因以下原因删除了 `applicationInsights` 依赖性：
    * 依赖项污染的潜在风险。
    * Java 11 不兼容。
    * 避免对 CPU 和/或内存产生潜在的性能影响。

### <a name="211-march-7-2019"></a>2.1.1（2019 年 3 月 7 日）
#### <a name="new-features"></a>新增功能
* 将主版本更新为 2.1.1。

### <a name="206-march-7-2019"></a>2.0.6（2019 年 3 月 7 日）
#### <a name="new-features"></a>新增功能
* 忽略遥测中的所有异常。

### <a name="210-december-17-2018"></a>2.1.0（2018 年 12 月 17 日）
#### <a name="new-features"></a>新增功能
* 将版本更新为 2.1.0 以解决问题。

### <a name="205-september-13-2018"></a>2.0.5（2018 年 9 月 13 日）
#### <a name="new-features"></a>新增功能
* 添加关键字 `exists` 和 `startsWith`。
* 更新自述文件。
#### <a name="key-bug-fixes"></a>关键 bug 修复
* 修复了以下问题：无法为 Entity 直接调用 self href。
* 修复了以下问题：如果未创建集合，findAll 将失败。

### <a name="204-prerelease-august-23-2018"></a>2.0.4（预发行版）（2018 年 8 月 23 日）
#### <a name="new-features"></a>新增功能
* 将包从 documentdb 重命名为 cosmosdb。
* 新增了查询方法关键字的功能。 现在支持来自 SQL API 的 16 个关键字。
* 新增了具有分页和排序的查询功能。
* 简化了 spring-data-cosmosdb 的配置。
* 添加了 `deleteCollection` 和 `deleteAll` API。

#### <a name="key-bug-fixes"></a>关键 bug 修复
* Bug 修复和缺陷缓解。

## <a name="faq"></a>常见问题解答
[!INCLUDE [cosmos-db-sdk-faq](includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤
了解有关 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 的详细信息。

了解有关 [Spring Framework](https://spring.io/projects/spring-framework) 的详细信息。

详细了解有关 [Spring Boot](https://spring.io/projects/spring-boot) 的详细信息。

详细了解有关 [Spring Data](https://spring.io/projects/spring-data) 的详细信息。