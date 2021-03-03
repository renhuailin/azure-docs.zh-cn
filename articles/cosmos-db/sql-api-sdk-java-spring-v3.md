---
title: 用于 SQL API 的 Spring Data Azure Cosmos DB v3 的发行说明和资源
description: 了解用于 SQL API 的 Spring Data Azure Cosmos DB v3 的信息，包括发行日期、停用日期以及在 Azure Cosmos DB SQL Async Java SDK 各版本之间所做的更改。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/28/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 9c3209895902a11ad0b9f29ff28e9ac7f845b101
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692719"
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

## <a name="get-started-fast"></a>快速入门

  按照 [春季 Boot 入门指南](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)，按弹簧数据 Azure Cosmos DB 启动并运行。 建议使用春季 Boot 入门方法开始使用春季 Data Azure Cosmos DB 连接器。

  或者，您可以将弹簧数据 Azure Cosmos DB 依赖项添加到文件中，如下 `pom.xml` 所示：

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest-version</version>
  </dependency>
  ```

## <a name="helpful-content"></a>帮助性内容

| Content | 链接 |
|---|---|
|**SDK 下载**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-cosmos) |
|**API 文档** | [Java API 参考文档](/java/api/com.azure.spring.data.cosmos) |
|**参与 SDK** | [GitHub 上用于 Java 的 Azure SDK 中央存储库](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos) | 
|**入门** | [快速入门：生成用于管理 Azure Cosmos DB SQL API 数据的弹簧数据 Azure Cosmos DB 应用](./create-sql-api-spring-data.md) <br> [具有快速入门代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) | 
|**基本代码示例** | [Azure Cosmos DB： SQL API 的弹簧数据 Azure Cosmos DB 示例](sql-api-spring-data-sdk-samples.md) <br> [具有示例代码的 GitHub 存储库](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-samples)|
| **性能提示**| [适用于弹簧数据的 Java SDK v4 (性能提示) ](performance-tips-java-sdk-v4-sql.md)| 
| **疑难解答** | [排查适用于弹簧数据) 的 Java SDK v4 (](troubleshoot-java-sdk-v4-sql.md) | 
| **Azure Cosmos DB 研讨会和实验室** |[Cosmos DB 研讨会主页](https://aka.ms/cosmosworkshop)

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="additional-notes"></a>附加说明

* 春季 Data Azure Cosmos DB 支持 Java JDK 8 和 Java JDK 11。
* 当前支持春季 Data 2.3，当前不支持春季 Data 2.4。

## <a name="faq"></a>常见问题解答

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>后续步骤

了解有关 [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 的详细信息。

了解有关 [Spring Framework](https://spring.io/projects/spring-framework) 的详细信息。

详细了解有关 [Spring Boot](https://spring.io/projects/spring-boot) 的详细信息。

详细了解有关 [Spring Data](https://spring.io/projects/spring-data) 的详细信息。