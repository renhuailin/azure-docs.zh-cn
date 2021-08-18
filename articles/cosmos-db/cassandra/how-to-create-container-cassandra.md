---
title: 在 Azure Cosmos DB Cassandra API 中创建容器
description: 了解如何使用 Azure 门户、.NET、Java、Python、Node.js 和其他 SDK 在 Azure Cosmos DB Cassandra API 中创建容器。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/16/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f3aef2457228acfefc364eb55a428abb101f9f54
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778778"
---
# <a name="create-a-container-in-azure-cosmos-db-cassandra-api"></a>在 Azure Cosmos DB Cassandra API 中创建容器
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

本文介绍在 Azure Cosmos DB Cassandra API 中创建容器的不同方式。 它演示了如何使用 Azure 门户、Azure CLI、PowerShell 或受支持的 SDK 来创建容器。 本文演示如何创建容器、指定分区键和预配吞吐量。

本文介绍在 Azure Cosmos DB Cassandra API 中创建容器的不同方式。 如果你使用其他 API，请参阅[适用于 MongoDB 的 API](../mongodb/how-to-create-container-mongodb.md)、[Gremlin API](../how-to-create-container-gremlin.md)、[表 API](../table/how-to-create-container.md) 和 [SQL API](../how-to-create-container.md) 文章来创建容器。

> [!NOTE]
> 创建容器时，请确保不创建名称相同但大小写不同的两个容器。 这是因为 Azure 平台的某些部分不区分大小写，这可能会对具有此类名称的容器导致遥测和操作混乱/冲突。

## <a name="create-using-azure-portal"></a><a id="portal-cassandra"></a>使用 Azure 门户进行创建

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](manage-data-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建表” 。 接下来，请提供以下详细信息：

   * 表明要创建新密钥空间还是使用现有密钥空间。
   * 输入表名称。
   * 输入属性并指定一个主键。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="../media/how-to-create-container/partitioned-collection-create-cassandra.png" alt-text="Cassandra API 的屏幕截图，突出显示“添加表”对话框":::

> [!NOTE]
> Cassandra API 的主键用作分区键。

## <a name="create-using-net-sdk"></a><a id="dotnet-cassandra"></a>使用 .NET SDK 创建

```csharp
// Create a Cassandra table with a partition/primary key and provision 1000 RU/s throughput.
session.Execute(CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=1000);
```

如果创建集合时遇到超时异常，请执行读取操作来验证是否已成功创建集合。 成功完成集合创建操作之前，读取操作将引发异常。 有关创建操作所支持的状态代码列表，请参阅 [Azure Cosmos DB 的 HTTP 状态代码](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)一文。

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>使用 Azure CLI 创建

[使用 Azure CLI 创建 Cassandra 表](../scripts/cli/cassandra/create.md)。 有关所有 Azure Cosmos DB API 的所有 Azure CLI 示例的列表，请参阅 [Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)。

## <a name="create-using-powershell"></a>使用 PowerShell 创建

[使用 PowerShell 创建 Cassandra 表](../scripts/powershell/cassandra/create.md)。 有关所有 Azure Cosmos DB API 的所有 PowerShell 示例的列表，请参阅 [PowerShell 示例](powershell-samples.md)

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](../partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](../request-units.md)
* [在容器和数据库上预配吞吐量](../set-throughput.md)
* [使用 Azure Cosmos 帐户](../account-databases-containers-items.md)