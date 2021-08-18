---
title: 在 Azure Cosmos DB Cassandra API 资源上预配吞吐量
description: 了解如何在 Azure Cosmos DB Cassandra API 资源中预配容器、数据库和自动缩放吞吐量。 你将使用 Azure 门户、CLI、PowerShell 和其他各种 SDK。
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/15/2020
ms.custom: devx-track-js, devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 8d6f1565e2cc4c7acb49649fb79d8c80bc2a4d5c
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113360624"
---
# <a name="provision-database-container-or-autoscale-throughput-on-azure-cosmos-db-cassandra-api-resources"></a>在 Azure Cosmos DB Cassandra API 资源上预配数据库、容器或自动缩放吞吐量
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

本文介绍如何在 Azure Cosmos DB Cassandra API 中预配吞吐量。 你可在一个容器或数据库上预配标准（手动）或自动缩放吞吐量，并在数据库中的容器之间共享。 你可使用 Azure 门户、Azure CLI 或 Azure Cosmos DB SDK 来预配吞吐量。

如果你使用的是其他 API，请参阅 [SQL API](how-to-provision-container-throughput.md)、[适用于 MongoDB 的 API](how-to-provision-throughput-mongodb.md)、[Gremlin API](how-to-provision-throughput-gremlin.md) 文章来预配置吞吐量。

## <a name="azure-portal"></a><a id="portal-cassandra"></a> Azure 门户

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. [创建新的 Azure Cosmos 帐户](create-mongodb-dotnet.md#create-a-database-account)，或选择现有的 Azure Cosmos 帐户。

1. 打开“数据资源管理器”窗格，然后选择“新建表” 。 接下来，请提供以下详细信息：

   * 指出要创建新密钥空间还是使用现有密钥空间。 如果要在密钥空间级别预配吞吐量，请选择“预配数据库吞吐量”选项。
   * 在 CQL 命令中输入表 ID。
   * 输入主键值（例如 `/userrID`）。
   * 输入要预配的吞吐量（例如，1000 RU）
   * 选择“确定”。

    :::image type="content" source="./media/how-to-provision-throughput-cassandra/provision-table-throughput-portal-cassandra-api.png" alt-text="创建具有数据库级吞吐量的新集合时的数据资源管理器的屏幕截图":::

> [!Note]
> 若要使用 Cassandra API 在 Azure Cosmos DB 帐户中为容器预配吞吐量，请使用 `/myPrimaryKey` 作为分区键路径。

## <a name="net-sdk"></a><a id="dotnet-cassandra"></a> .NET SDK

### <a name="provision-throughput-for-a-cassandra-table"></a>为 Cassandra 表预配吞吐量

```csharp
// Create a Cassandra table with a partition (primary) key and provision throughput of 400 RU/s
session.Execute("CREATE TABLE myKeySpace.myTable(
    user_id int PRIMARY KEY,
    firstName text,
    lastName text) WITH cosmosdb_provisioned_throughput=400");

```
类似的命令可以通过任何 CQL 兼容的驱动程序发出。

### <a name="alter-or-change-throughput-for-a-cassandra-table"></a>修改或更改 Cassandra 表的吞吐量

```csharp
// Altering the throughput too can be done through code by issuing following command
session.Execute("ALTER TABLE myKeySpace.myTable WITH cosmosdb_provisioned_throughput=5000");
```

类似命令可以通过任何符合 CQL 标准的驱动程序执行。

```csharp
// Create a Cassandra keyspace and provision throughput of 400 RU/s
session.Execute("CREATE KEYSPACE IF NOT EXISTS myKeySpace WITH cosmosdb_provisioned_throughput=400");
```

## <a name="azure-resource-manager"></a>Azure 资源管理器

Azure 资源管理器模板可用于在数据库或容器级资源上为所有 Azure Cosmos DB API 预配自动缩放吞吐量。 有关示例，请参阅 [Azure Cosmos DB 的 Azure 资源管理器模板](templates-samples-cassandra.md)。

## <a name="azure-cli"></a>Azure CLI

Azure CLI 可用于在数据库或容器级资源上为所有 Azure Cosmos DB API 预配自动缩放吞吐量。 有关示例，请参阅[用于 Azure Cosmos DB 的 Azure CLI 示例](cli-samples-cassandra.md)。

## <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell 可用于在数据库或容器级资源上为所有 Azure Cosmos DB API 预配自动缩放吞吐量。 有关示例，请参阅[适用于 Azure Cosmos DB 的 Azure PowerShell 示例](powershell-samples-cassandra.md)。

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何在 Azure Cosmos DB 中预配吞吐量：

* [Azure Cosmos DB 中的请求单位和吞吐量](request-units.md)