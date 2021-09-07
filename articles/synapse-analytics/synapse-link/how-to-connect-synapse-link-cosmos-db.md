---
title: 连接到 Azure Synapse Link for Azure Cosmos DB
description: 了解如何通过 Azure Synapse Link 将 Azure Cosmos DB 数据库连接到 Azure Synapse 工作区。
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: f2c687d3124e19bd91321391396499675052662b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123113032"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>连接到 Azure Synapse Link for Azure Cosmos DB

本文介绍如何使用 Azure Synapse Link 从 Azure Synapse Analytics Studio 访问 Azure Cosmos DB 数据库。

## <a name="prerequisites"></a>先决条件

将 Azure Cosmos DB 数据库连接到工作区之前，需要以下各项：

* 现有的 Azure Cosmos DB 数据库或按照[快速入门：管理 Azure Cosmos DB 帐户](../../cosmos-db/how-to-manage-database-account.md)中的步骤创建一个新帐户。
* 现有的 Azure Synapse 工作区，或按照[快速入门：创建 Synapse 工作区](../quickstart-create-workspace.md)中的步骤创建一个新工作区。

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>在 Azure Cosmos DB 数据库帐户上启用 Synapse Link

若要在不影响操作性能的情况下对 Azure Cosmos DB 运行大规模分析，建议启用 Synapse Link for Azure Cosmos DB。 Synapse Link 将 HTAP 功能引入容器中，并在 Azure Synapse 中提供内置支持。

## <a name="go-to-synapse-studio"></a>转到 Synapse Studio

在 Azure Synapse 工作区中，选择“启动 Synapse Studio”。 在 Synapse Studio 主页上，选择“数据”，转到“数据对象资源管理器”。

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>将 Azure Cosmos DB 数据库连接到 Azure Synapse 工作区

连接 Azure Cosmos DB 数据库作为链接服务完成。 通过 Azure Cosmos DB 链接服务，可浏览和探索数据，并从 Apache Spark for Azure Synapse Analytics 或 SQL 读取数据或将数据写入 Azure Cosmos DB。

可以在数据对象资源管理器中通过执行以下步骤直接连接到 Azure Cosmos DB 数据库：

1. 选择“数据”旁边的“+”图标 。
1. 选择“连接到外部数据”。
1. 选择要连接到的 API，例如“SQL API”或“MongoDB API” 。
1. 选择“继续”。
1. 使用易记名称命名链接服务。 该名称将显示在数据对象资源管理器中，并由 Azure Synapse 运行时用于连接到数据库和容器。
1. 选择“Azure Cosmos DB 帐户名称”和“数据库名称” 。
1. （可选）如果没有指定区域，Azure Synapse 运行时操作将被路由到启用分析存储的最近的区域。 还可以手动设置希望用户用于访问 Azure Cosmos DB 分析存储的区域。 选择“其他连接属性”，然后选择“新建” 。 在“属性名称”下，输入“PreferredRegions” 。 将“值”设置为所需的区域，如 WestUS2 。 （单词和数字之间没有空格。）
1. 选择“创建”。

Azure Cosmos DB 数据库会显示在 Azure Cosmos DB 部分下的“链接”选项卡中 。 借助 Azure Cosmos DB，可以通过以下图标区分启用 HTAP 的容器和仅 OLTP 容器：

仅 OLTP 容器：

![显示 OLTP 容器图标的可视化效果。](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

启用 HTAP 的容器：

![显示 HTAP 容器图标的可视化效果。](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>与代码生成的操作快速交互

右键单击容器时，会获得一个用于触发 Spark 或 SQL 运行时的手势列表。 写入容器将通过 Azure Cosmos DB 的事务存储进行，并将消耗请求单位。  

## <a name="next-steps"></a>后续步骤

* [了解 Azure Synapse 和 Azure Cosmos DB 均支持的功能](./concept-synapse-link-cosmos-db-support.md)
* [为 Azure Cosmos DB 分析存储配置专用终结点](../../cosmos-db/analytical-store-private-endpoints.md)
* [了解如何使用 Spark 3 查询分析存储](./how-to-query-analytical-store-spark-3.md)
* [了解如何使用 Spark 2 查询分析存储](./how-to-query-analytical-store-spark.md)
