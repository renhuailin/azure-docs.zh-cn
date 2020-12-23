---
title: 在 Azure Cosmos DB 中创建容器表 API
description: 了解如何使用 Azure 门户、.NET、Java、Python、Node.js 和其他 Sdk 在 Azure Cosmos DB 表 API 中创建容器。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: fde3145e7bd7f4e53ae7a0c44f02e066c28ec785
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101572"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>在 Azure Cosmos DB 中创建容器表 API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

本文介绍了在 Azure Cosmos DB 表 API 中创建容器的不同方法。 它演示了如何使用 Azure 门户、Azure CLI、PowerShell 或受支持的 Sdk 创建容器。 本文演示如何创建容器、指定分区键和预配吞吐量。

本文介绍了在 Azure Cosmos DB 表 API 中创建容器的不同方法。 如果你使用的是其他 API，请参阅用于 [MongoDB 的 api](how-to-create-container-mongodb.md)、 [CASSANDRA API](how-to-create-container-cassandra.md)、 [Gremlin API](how-to-create-container-gremlin.md)和 [SQL API](how-to-create-container.md) 文章，以创建容器。

> [!NOTE]
> 创建容器时，请确保不创建名称相同但大小写不同的两个容器。 这是因为 Azure 平台的某些部分不区分大小写，这可能会对具有此类名称的容器导致遥测和操作混乱/冲突。

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>使用 Azure 门户进行创建

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-table-dotnet.md#create-a-database-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建表” 。 接下来，请提供以下详细信息：

   * 输入表 ID。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="./media/how-to-create-container/partitioned-collection-create-table.png" alt-text="表 API 的屏幕截图，突出显示“添加表”对话框":::

> [!Note]
> 就表 API 来说，每次添加新行时，都会指定分区键。

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>使用 Azure CLI 创建

[使用 Azure CLI 创建表 API 表](./scripts/cli/table/create.md)。 有关所有 Azure Cosmos DB API 的所有 Azure CLI 示例的列表，请参阅 [Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)。

## <a name="create-using-powershell"></a>使用 PowerShell 创建

[使用 PowerShell 创建表 API 表](./scripts/powershell/table/create.md)。 有关所有 Azure Cosmos DB API 的所有 PowerShell 示例的列表，请参阅 [PowerShell 示例](powershell-samples.md)

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](request-units.md)
* [在容器和数据库上预配吞吐量](set-throughput.md)
* [使用 Azure Cosmos 帐户](./account-databases-containers-items.md)