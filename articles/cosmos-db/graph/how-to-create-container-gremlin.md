---
title: 在 Azure Cosmos DB Gremlin API 中创建容器
description: 了解如何使用 Azure 门户、.NET 和其他 SDK 在 Azure Cosmos DB Gremlin API 中创建容器。
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/16/2020
author: manishmsfte
ms.author: mansha
ms.custom: devx-track-csharp
ms.openlocfilehash: 24b863608376b9a948d29f7642c0d57bee676f88
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778275"
---
# <a name="create-a-container-in-azure-cosmos-db-gremlin-api"></a>在 Azure Cosmos DB Gremlin API 中创建容器
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

本文介绍如何通过不同方式在 Azure Cosmos DB Gremlin API 中创建容器。 它演示了如何使用 Azure 门户、Azure CLI、PowerShell 或受支持的 SDK 来创建容器。 本文演示如何创建容器、指定分区键和预配吞吐量。

本文介绍如何通过不同方式在 Azure Cosmos DB Gremlin API 中创建容器。 如果你使用不同的 API，请参阅[适用于 MongoDB 的 API](../mongodb/how-to-create-container-mongodb.md)、[Cassandra API](../cassandra/how-to-create-container-cassandra.md)、[表 API](../table/how-to-create-container.md) 和 [SQL API](../how-to-create-container.md) 文章来创建容器。

> [!NOTE]
> 创建容器时，请确保不创建名称相同但大小写不同的两个容器。 这是因为 Azure 平台的某些部分不区分大小写，这可能会对具有此类名称的容器导致遥测和操作混乱/冲突。

## <a name="create-using-azure-portal"></a><a id="portal-gremlin"></a>使用 Azure 门户进行创建

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-graph-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建图形” 。 接下来，请提供以下详细信息：

   * 表明要创建新数据库还是使用现有数据库。
   * 输入图形 ID。
   * 对于“无限制”存储容量。
   * 输入顶点的分区键。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="../media/how-to-create-container/partitioned-collection-create-gremlin.png" alt-text="Gremlin API 的屏幕截图，突出显示“添加图形”对话框":::

## <a name="create-using-net-sdk"></a><a id="dotnet-sql-graph"></a>使用 .NET SDK 创建

如果创建集合时遇到超时异常，请执行读取操作来验证是否已成功创建集合。 成功完成集合创建操作之前，读取操作将引发异常。 有关创建操作所支持的状态代码列表，请参阅 [Azure Cosmos DB 的 HTTP 状态代码](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)一文。

```csharp
// Create a container with a partition key and provision 1000 RU/s throughput.
DocumentCollection myCollection = new DocumentCollection();
myCollection.Id = "myContainerName";
myCollection.PartitionKey.Paths.Add("/myPartitionKey");

await client.CreateDocumentCollectionAsync(
    UriFactory.CreateDatabaseUri("myDatabaseName"),
    myCollection,
    new RequestOptions { OfferThroughput = 1000 });
```

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>使用 Azure CLI 创建

[使用 Azure CLI 创建 Gremlin 图](../scripts/cli/gremlin/create.md)。 有关所有 Azure Cosmos DB API 的所有 Azure CLI 示例的列表，请参阅 [Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)。

## <a name="create-using-powershell"></a>使用 PowerShell 创建

[使用 PowerShell 创建 Gremlin 图](../scripts/powershell/gremlin/create.md)。 有关所有 Azure Cosmos DB API 的所有 PowerShell 示例的列表，请参阅 [PowerShell 示例](powershell-samples.md)

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](../partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](../request-units.md)
* [在容器和数据库上预配吞吐量](../set-throughput.md)
* [使用 Azure Cosmos 帐户](../account-databases-containers-items.md)