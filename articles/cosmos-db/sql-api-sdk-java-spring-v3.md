---
title: 用于 SQL API 的 Spring Data Azure Cosmos DB v3 的发行说明和资源
description: 了解 SQL API Azure Cosmos DB v3 的弹簧数据，其中包括发布日期、停用日期和 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 64054a2bb5c1f7e17eef87c3babb28137b6c912a
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097118"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>适用于 Core (SQL) API 的 Spring Data Azure Cosmos DB v3：发行说明和资源
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
> * [Spark 连接器](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 资源提供程序](/rest/api/cosmos-db-resource-provider/)
> * [SQL](./sql-query-getting-started.md)
> * [批量执行工具 - .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [批量执行程序 - Java](sql-api-sdk-bulk-executor-java.md)

开发人员可以通过适用于 Core (SQL) 的 Spring Data Azure Cosmos DB 版本 3 在 Spring 应用程序中使用 Azure Cosmos DB。 Spring Data Azure Cosmos DB 公开 Spring Data 接口，以便操作数据库和集合、使用文档和发出查询。 同一 Maven 项目中同时支持 Sync 和 Async (Reactive) API。 

Spring Data Azure Cosmos DB 依赖于 Spring Data 框架。 Azure Cosmos DB SDK 团队发布了适用于 Spring Data 2.2 和 2.3 的 Maven 项目。

[Spring Framework](https://spring.io/projects/spring-framework) 是一种简化 Java 应用程序开发的编程和配置模型。 Spring 使用依赖项注入来简化应用程序的“管道”。 由于 Spring 使得生成和测试应用程序变得更加简单，因此许多开发人员都喜欢 Spring。 [Spring Boot](https://spring.io/projects/spring-boot) 重视 Web 应用程序和微服务的开发，扩展了这种管道处理。 [Spring Data](https://spring.io/projects/spring-data) 是一种编程模型和框架，用于从 Spring 或 Spring Boot 应用程序的上下文中访问数据存储（如 Azure Cosmos DB）。 

可在 [Azure Spring Cloud](https://azure.microsoft.com/services/spring-cloud/) 应用程序中使用 Spring Data Azure Cosmos DB。

> [!IMPORTANT]  
> 这些发行说明适用于 Spring Data Azure Cosmos DB 的版本 3。 可以[在此处找到版本 2 的发行说明](sql-api-sdk-java-spring-v2.md)。 
>
> Spring Data Azure Cosmos DB 仅支持 SQL API。
>
> 请参阅以下文章，了解其他 Azure Cosmos DB API 上的 Spring Data：
> * [将适用于 Apache Cassandra 的 Spring Data 用于 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [将 Spring Data MongoDB 用于 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [将 Spring Data Gremlin 用于 Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>从此处开始

# <a name="explore"></a>[探索](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>这些选项卡包含基本的 Spring Data Azure Cosmos DB 示例。

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>配置依赖项

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[“连接”](#tab/connect)

### <a name="connect"></a>连接

指定 Azure Cosmos DB 帐户和容器详细信息。 Spring Data Azure Cosmos DB 会自动创建客户端并连接到容器。

[application.properties](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties)：
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[文档操作](#tab/docs)

### <a name="document-operations"></a>文档操作

---

## <a name="resources"></a>资源

* **参与编写 SDK** ： [GitHub 上的 Spring Data Azure Cosmos DB 存储库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **教程** ： [GitHub 上的 Spring Data Azure Cosmos DB 教程](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="faq"></a>常见问题解答

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 的详细信息。

了解有关 [Spring Framework](https://spring.io/projects/spring-framework) 的详细信息。

详细了解有关 [Spring Boot](https://spring.io/projects/spring-boot) 的详细信息。

详细了解有关 [Spring Data](https://spring.io/projects/spring-data) 的详细信息。