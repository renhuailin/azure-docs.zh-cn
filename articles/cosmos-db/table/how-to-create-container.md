---
title: 在 Azure Cosmos DB 表 API 中创建容器
description: 了解如何使用 Azure 门户、.NET、Java、Python、Node.js 和其他 SDK 在 Azure Cosmos DB 表 API 中创建容器。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 9320e34ccd4cdb217ac574eeef0d5c0b0f1eeb42
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812094"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>在 Azure Cosmos DB 表 API 中创建容器
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

本文介绍如何在 Azure Cosmos DB 表 API 中通过不同方式创建容器。 它演示了如何使用 Azure 门户、Azure CLI、PowerShell 或受支持的 SDK 来创建容器。 本文演示如何创建容器、指定分区键和预配吞吐量。

本文介绍如何在 Azure Cosmos DB 表 API 中通过不同方式创建容器。 如果使用的是不同的 API，请参阅[适用于 MongoDB 的 API](../mongodb/how-to-create-container-mongodb.md)、[Cassandra API](../cassandra/how-to-create-container-cassandra.md)、[Gremlin API](../how-to-create-container-gremlin.md) 和 [SQL API](../how-to-create-container.md) 文章来创建容器。

> [!NOTE]
> 创建容器时，请确保不创建名称相同但大小写不同的两个容器。 这是因为 Azure 平台的某些部分不区分大小写，这可能会对具有此类名称的容器导致遥测和操作混乱/冲突。

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>使用 Azure 门户进行创建

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-table-dotnet.md#1---create-an-azure-cosmos-db-account)或选择现有的帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建表” 。 接下来，请提供以下详细信息：

   * 输入表 ID。
   * 输入要进行预配的吞吐量（例如，1000 RU）。
   * 选择“确定” 。

    :::image type="content" source="../media/how-to-create-container/partitioned-collection-create-table.png" alt-text="表 API 的屏幕截图，突出显示“添加表”对话框":::

> [!Note]
> 就表 API 来说，每次添加新行时，都会指定分区键。

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>使用 Azure CLI 创建

[使用 Azure CLI 创建表 API 表](../scripts/cli/table/create.md)。 有关所有 Azure Cosmos DB API 的所有 Azure CLI 示例的列表，请参阅 [Azure Cosmos DB 的 Azure CLI 示例](cli-samples.md)。

## <a name="create-using-powershell"></a>使用 PowerShell 创建

[使用 PowerShell 创建表 API 表](../scripts/powershell/table/create.md)。 有关所有 Azure Cosmos DB API 的所有 PowerShell 示例的列表，请参阅 [PowerShell 示例](powershell-samples.md)

## <a name="next-steps"></a>后续步骤

* [Azure Cosmos DB 中的分区](../partitioning-overview.md)
* [Azure Cosmos DB 中的请求单位](../request-units.md)
* [在容器和数据库上预配吞吐量](../set-throughput.md)
* [使用 Azure Cosmos 帐户](../account-databases-containers-items.md)