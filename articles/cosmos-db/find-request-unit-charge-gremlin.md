---
title: 在 Azure Cosmos DB 中查找 Gremlin API 查询的请求单位 (RU) 费用
description: 了解如何 () RU 查找针对 Azure Cosmos 容器执行的 Gremlin 查询的请求单位。 您可以使用 Azure 门户，.NET，Java 驱动程序来查找 RU 费用。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 8393fb431c5148d3f4885135c90fe4d0b8970d52
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082005"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>查找 Azure Cosmos DB Gremlin API 中执行的操作的请求单位费用
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB 支持多种 API，例如 SQL、MongoDB、Cassandra、Gremlin 和表。 每个 API 具有自身的数据库操作集。 这些操作包括简单的点读取和写入，以及复杂的查询等等。 每个数据库操作根据其复杂性消耗系统资源。

所有数据库操作的成本将由 Azure Cosmos DB 规范化，并以“请求单位”（简称 RU）表示。 你可以将 RU 视为性能货币，它抽象化了执行 Azure Cosmos DB 支持的数据库操作所需的系统资源，例如 CPU、IOPS 和内存。 不管使用哪个 API 来与 Azure Cosmos 容器和数据库操作交互，都始终以 RU 来计量成本。 无论数据库操作是写入、点读取还是查询，都始终以 RU 来计量成本。 若要了解详细信息，请参阅 " [请求单位" 和 "注意事项](request-units.md) " 一文。

本文介绍了不同的方法，可用于查找对 Azure Cosmos DB Gremlin API 中的容器执行的任何操作的 [请求单位](request-units.md) (RU) 消耗。 如果你使用的是其他 API，请 [参阅适用于 MongoDB 的 api](find-request-unit-charge-mongodb.md)、 [CASSANDRA API](find-request-unit-charge-cassandra.md)、 [SQL API](find-request-unit-charge.md)和 [表 API](find-request-unit-charge-table.md) 文章来查找 RU/秒的费用。

Gremlin API 返回的标头将映射到目前由 Gremlin .NET 和 Java SDK 公开的自定义状态特性。 请求费用显示在 `x-ms-request-charge` 键下。 使用 Gremlin API 时，可以通过多个选项来查找针对 Azure Cosmos 容器执行的操作所消耗的 RU。

## <a name="use-the-azure-portal"></a>使用 Azure 门户

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-graph-gremlin-console.md#create-a-database-account)并在其中植入数据，或选择一个已包含数据的现有帐户。

1. 转到“数据资源管理器”窗格，然后选择要处理的容器。

1. 输入有效的查询，然后选择 " **执行 Gremlin 查询** "。

1. 选择“查询统计信息”，以显示执行的请求的实际请求费用。

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="在 Azure 门户中获取 Gremlin 查询请求费用的屏幕截图":::

## <a name="use-the-net-sdk-driver"></a>使用 .NET SDK 驱动程序

使用 [Gremlin .NET SDK](https://www.nuget.org/packages/Gremlin.Net/) 时，状态特性将显示在 `ResultSet<>` 对象的 `StatusAttributes` 属性下：

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

有关详细信息，请参阅[快速入门：使用 Azure Cosmos DB Gremlin API 帐户生成 .NET Framework 或 Core 应用程序](create-graph-dotnet.md)。

## <a name="use-the-java-sdk-driver"></a>使用 Java SDK 驱动程序

使用 [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) 时，可以通过对 `ResultSet` 对象调用 `statusAttributes()` 方法来检索状态特性：

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

有关详细信息，请参阅[快速入门：使用 Java SDK 在 Azure Cosmos DB 中创建图形数据库](create-graph-java.md)。

## <a name="next-steps"></a>后续步骤

若要了解如何优化 RU 消耗量，请参阅以下文章：

* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)
* [在 Azure Cosmos DB 中优化预配的吞吐量成本](optimize-cost-throughput.md)
* [优化 Azure Cosmos DB 中的查询成本](./optimize-cost-reads-writes.md)