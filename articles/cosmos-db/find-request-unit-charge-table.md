---
title: 查找请求单位 (RU) 对表 API 查询计费 Azure Cosmos DB
description: 了解如何查找针对 Azure Cosmos 容器执行的表 API 查询的请求单位 (RU) 。 可以使用 Azure 门户、.NET、Java、Python 和 Node.js 语言来查找 RU 费用。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 1d7a12e436fd3bc1700dc4a1d76dc2b80d861144
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078452"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>查找 Azure Cosmos DB 中执行的操作的请求单位费用表 API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB 支持多种 API，例如 SQL、MongoDB、Cassandra、Gremlin 和表。 每个 API 具有自身的数据库操作集。 这些操作包括简单的点读取和写入，以及复杂的查询等等。 每个数据库操作根据其复杂性消耗系统资源。

所有数据库操作的成本将由 Azure Cosmos DB 规范化，并以“请求单位”（简称 RU）表示。 你可以将 RU 视为性能货币，它抽象化了执行 Azure Cosmos DB 支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。 不管使用哪个 API 来与 Azure Cosmos 容器和数据库操作交互，都始终以 RU 来计量成本。 无论数据库操作是写入、点读取还是查询，都始终以 RU 来计量成本。 若要了解详细信息，请参阅 " [请求单位" 和 "注意事项](request-units.md) " 一文。

本文介绍了不同的方法，可用于查找对 Azure Cosmos DB 表 API 中的容器执行的任何操作的 [请求单位](request-units.md) (RU) 消耗。 如果你使用的是其他 API，请参阅 [适用于 MongoDB 的 api](find-request-unit-charge-mongodb.md)、 [CASSANDRA API](find-request-unit-charge-cassandra.md)、 [Gremlin API](find-request-unit-charge-gremlin.md)和 [SQL API](find-request-unit-charge.md) 文章来查找 RU/秒的费用。

## <a name="use-the-net-sdk"></a>使用 .NET SDK

目前，唯一能够返回表操作 RU 费用的 SDK 是 [.NET 标准 SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)。 `TableResult` 对象公开 `RequestCharge` 属性，针对 Azure Cosmos DB 表 API 使用该 SDK 时，该 SDK 会填充该属性：

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

有关详细信息，请参阅[快速入门：使用 .NET SDK 和 Azure Cosmos DB 生成表 API 应用](create-table-dotnet.md)。

## <a name="next-steps"></a>后续步骤

若要了解如何优化 RU 消耗量，请参阅以下文章：

* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)
* [在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)
* [优化 Azure Cosmos DB 中的查询成本](./optimize-cost-reads-writes.md)