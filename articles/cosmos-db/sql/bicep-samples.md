---
title: Azure Cosmos DB Core (SQL API) 的 Bicep 示例
description: 使用 Bicep 创建并配置 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: 8cab03d2b3cd3c6d7b6a2f9bd15c686bea529de9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699333"
---
# <a name="bicep-for-azure-cosmos-db"></a>适用于 Azure Cosmos DB 的 Bicep

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文介绍 Core (SQL) API 帐户的 Bicep 示例。 你还可以找到 [Cassandra](../cassandra/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md) 和[表](../table/manage-with-bicep.md) API 的 Bicep 示例。

## <a name="core-sql-api"></a>Core (SQL) API

|**示例**|**说明**|
|---|---|
|[创建具有自动缩放吞吐量的 Azure Cosmos 帐户、数据库和容器](manage-with-bicep.md#create-autoscale) | 在两个区域中中创建 Core (SQL) API 帐户，其中包含具有自动缩放吞吐量的数据库和容器。 |
|[创建具有分析存储的 Azure Cosmos 帐户、数据库和容器](manage-with-bicep.md#create-analytical-store) | 在一个区域中创建 Core (SQL) API 帐户，其中包含配置为启用分析 TTL 的容器，以及选择使用手动还是自动缩放吞吐量的选项。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、数据库和容器](manage-with-bicep.md#create-manual) | 在两个区域中创建 Core (SQL) API 帐户，其中包含具有标准吞吐量的数据库和容器。 |
|[创建包含存储过程、触发器和 UDF 的 Azure Cosmos 帐户、数据库和容器](manage-with-bicep.md#create-sproc) | 在两个区域中创建 Core (SQL) API 帐户，其中包含存储过程、触发器和容器的 UDF。 |
|[创建具有 Azure AD 标识、角色定义和角色分配的 Azure Cosmos 帐户](manage-with-bicep.md#create-rbac) | 创建 Core (SQL) API 帐户，其中包含服务主体上的 Azure AD 标识、角色定义和角色分配。 |
|[创建免费层 Azure Cosmos 帐户](manage-with-bicep.md#free-tier) |  在免费层创建 Azure Cosmos DB Core (SQL) API 帐户。 |

## <a name="next-steps"></a>后续步骤

尝试为迁移到 Azure Cosmos DB 进行容量规划？ 可以使用有关现有数据库群集的信息进行容量规划。

* 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md)
* 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)
