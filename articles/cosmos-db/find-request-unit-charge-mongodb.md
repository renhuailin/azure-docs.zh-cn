---
title: 查找 Azure Cosmos DB API for MongoDB 操作的请求单位费用
description: 了解如何查找针对 Azure Cosmos 容器执行的 MongoDB 查询所产生的请求单位 (RU) 费用。 可以使用 Azure 门户、MongoDB .NET、Java、Node.js 驱动程序。
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/19/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7a2ff0e2b2ebc373c0de1dab5003b3c06cc284be
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354684"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-api-for-mongodb"></a>查找 Azure Cosmos DB API for MongoDB 中执行的操作的请求单位费用
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB 支持多种 API，例如 SQL、MongoDB、Cassandra、Gremlin 和表。 每个 API 具有自身的数据库操作集。 这些操作包括简单的点读取和写入，以及复杂的查询等等。 每个数据库操作根据其复杂性消耗系统资源。

所有数据库操作的成本将由 Azure Cosmos DB 规范化，并以“请求单位”（简称 RU）表示。 请求费用是所有数据库操作使用的请求单位。 你可以将 RU 视为性能货币，它抽象化了执行 Azure Cosmos DB 支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。 不管使用哪个 API 来与 Azure Cosmos 容器和数据库操作交互，都始终以 RU 来计量成本。 无论数据库操作是写入、点读取还是查询，都始终以 RU 来计量成本。 若要了解详细信息，请参阅[请求单位和注意事项](request-units.md)一文。

本文介绍如何通过不同的方式，来查找针对 Azure Cosmos DB API for MongoDB 中的容器执行的任何操作所消耗的[请求单位](request-units.md) (RU)。 如果你使用的是其他 API，请参阅 [SQL API](find-request-unit-charge.md)、[Cassandra API](find-request-unit-charge-cassandra.md)、[Gremlin API](find-request-unit-charge-gremlin.md) 和[表 API](find-request-unit-charge-table.md) 文章，找到每秒的 RU 费用。

RU 费用由名为 `getLastRequestStatistics` 的自定义[数据库命令](https://docs.mongodb.com/manual/reference/command/)公开。 该命令返回一个文档，其中包含上次执行的操作的名称、其请求费用和持续时间。 如果使用 Azure Cosmos DB API for MongoDB，则可以通过多个选项来检索 RU 费用。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-mongodb-dotnet.md#create-a-database-account)并在其中植入数据，或选择一个已包含数据的现有帐户。

1. 转到“数据资源管理器”窗格，然后选择要处理的容器。

1. 选择容器名称旁边的“...”，然后选择“新建查询” 。

1. 输入有效的查询，然后选择“执行查询”。

1. 选择“查询统计信息”，以显示执行的请求的实际请求费用。 此查询编辑器允许你仅针对查询谓词运行和查看请求单位费用。 不能将此编辑器用于数据操作命令（例如 insert 语句）。

   :::image type="content" source="./media/find-request-unit-charge/portal-mongodb-query.png" alt-text="Azure 门户中的 MongoDB 查询请求费用屏幕截图":::

1. 若要获取数据操作命令的请求费用，请在基于 shell 的 UI（如 Mongo shell、[Robo 3T](mongodb-robomongo.md)、[MongoDB Compass](mongodb-compass.md) 或包含 shell 脚本的 VS Code 扩展）中运行 `getLastRequestStatistics` 命令。

   `db.runCommand({getLastRequestStatistics: 1})`

## <a name="use-the-mongodb-net-driver"></a>使用 MongoDB .NET 驱动程序

使用[官方 MongoDB .NET 驱动程序](https://docs.mongodb.com/ecosystem/drivers/csharp/)时，可以通过对 `IMongoDatabase` 对象调用 `RunCommand` 方法来执行命令。 此方法需要 `Command<>` 抽象类的实现：

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

有关详细信息，请参阅[快速入门：使用 Azure Cosmos DB API for MongoDB 生成 .NET Web 应用](create-mongodb-dotnet.md)。

## <a name="use-the-mongodb-java-driver"></a>使用 MongoDB Java 驱动程序


使用[官方 MongoDB Java 驱动程序](https://mongodb.github.io/mongo-java-driver/)时，可以通过对 `MongoDatabase` 对象调用 `runCommand` 方法来执行命令：

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

有关详细信息，请参阅[快速入门：使用 Azure Cosmos DB API for MongoDB 和 Java SDK 生成 Web 应用](create-mongodb-java.md)。

## <a name="use-the-mongodb-nodejs-driver"></a>使用 MongoDB Node.js 驱动程序

使用[官方 MongoDB Node.js 驱动程序](https://mongodb.github.io/node-mongodb-native/)时，可以通过对 `db` 对象调用 `command` 方法来执行命令：

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

有关详细信息，请参阅[快速入门：将现有的 MongoDB Node.js Web 应用迁移到 Azure Cosmos DB](create-mongodb-nodejs.md)。

## <a name="next-steps"></a>后续步骤

若要了解如何优化 RU 消耗量，请参阅以下文章：

* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)
* [在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)
* [优化 Azure Cosmos DB 中的查询成本](./optimize-cost-reads-writes.md)