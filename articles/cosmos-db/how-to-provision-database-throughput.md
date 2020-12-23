---
title: 在 SQL API 中预配数据库吞吐量 Azure Cosmos DB
description: 了解如何使用 Azure 门户、CLI、PowerShell 和其他各种 Sdk Azure Cosmos DB SQL API 在数据库级别预配吞吐量。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 4ecbee2260da735cd6ba74d3b9ffb55b4a652e9e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93341988"
---
# <a name="provision-standard-manual-throughput-on-a-database-in-azure-cosmos-db---sql-api"></a>预配标准 (在 Azure Cosmos DB-SQL API 中的数据库上手动) 吞吐量
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文介绍如何在 Azure Cosmos DB SQL API 中的数据库上预配标准 (手动) 吞吐量。 可以为单个[容器](how-to-provision-container-throughput.md)预配吞吐量，也可以为数据库预配吞吐量，并在数据库中的容器之间共享吞吐量。 若要了解何时使用容器级别和数据库级别吞吐量，请参阅[容器和数据库预配吞吐量的用例](set-throughput.md)一文。 可以使用 Azure 门户或 Azure Cosmos DB SDK 来预配数据库级别吞吐量。

如果你使用的是其他 API，请参阅 [api For MongoDB](how-to-provision-throughput-mongodb.md)， [CASSANDRA API](how-to-provision-throughput-cassandra.md)， [Gremlin api](how-to-provision-throughput-gremlin.md) 文章来预配吞吐量。

## <a name="provision-throughput-using-azure-portal"></a>使用 Azure 门户预配吞吐量

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-sql-api-dotnet.md#create-account)，或选择现有的 Azure Cosmos 帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建数据库” 。 提供以下详细信息：

   * 输入数据库 ID。
   * 选择 " **设置数据库吞吐量** " 选项。
   * 输入吞吐量（例如 1000 RU）。
   * 选择“确定”。

    :::image type="content" source="./media/how-to-provision-database-throughput/provision-database-throughput-portal-sql-api.png" alt-text="“新建数据库”对话框屏幕截图":::

## <a name="provision-throughput-using-azure-cli-or-powershell"></a>使用 Azure CLI 或 PowerShell 预配吞吐量

若要创建具有共享吞吐量的数据库，请参阅

* [使用 Azure CLI 创建数据库](manage-with-cli.md#create-a-database-with-shared-throughput)
* [使用 PowerShell 创建数据库](manage-with-powershell.md#create-db-ru)

## <a name="provision-throughput-using-net-sdk"></a>使用 .NET SDK 预配吞吐量

> [!Note]
> 可以使用适用于 SQL API 的 Azure Cosmos Sdk 为所有 Api 预配吞吐量。 也可以选择将以下示例用于 Cassandra API。

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

```csharp
//set the throughput for the database
RequestOptions options = new RequestOptions
{
    OfferThroughput = 500
};

//create the database
await client.CreateDatabaseIfNotExistsAsync(
    new Database {Id = databaseName},  
    options);
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/DatabaseDocsSampleCode.cs?name=DatabaseCreateWithThroughput)]

---

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解在 Azure Cosmos DB 中预配的吞吐量：

* [全局缩放预配的吞吐量](./request-units.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [如何在容器上预配标准（手动）吞吐量](how-to-provision-container-throughput.md)
* [如何为容器预配自动缩放吞吐量](how-to-provision-autoscale-throughput.md)
* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)