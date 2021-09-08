---
title: Azure Cosmos DB Core (SQL API) 的 Azure 资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: mjbrown
ms.openlocfilehash: 9ba89a26f65046792366a7d08ced69eb53787eb0
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037822"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>用于 Azure Cosmos DB 的 Azure 资源管理器模板
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

本文仅显示适用于 Core (SQL) API 帐户的 Azure 资源管理器模板示例。 你还可以查找 [Cassandra](cassandra/templates-samples.md)、[Gremlin](templates-samples-gremlin.md)、[MongoDB](mongodb/resource-manager-template-samples.md) 和[表](table/resource-manager-templates.md) API 的模板示例。

## <a name="core-sql-api"></a>Core (SQL) API

|**模板**|**说明**|
|---|---|
|[创建具有自动缩放吞吐量的 Azure Cosmos 帐户、数据库和容器](manage-with-templates.md#create-autoscale) | 此模板在两个区域（具有自动缩放吞吐量的数据库和容器）中创建一个 Core (SQL) API 帐户。 |
|[创建具有分析存储的 Azure Cosmos 帐户、数据库和容器](manage-with-templates.md#create-analytical-store) | 此模板在一个区域中创建一个 Core (SQL) API 帐户，其中包含配置了启用分析 TTL 的容器和手动或自动缩放吞吐量选项。 |
|[创建具有标准（手动）吞吐量的 Azure Cosmos 帐户、数据库和容器](manage-with-templates.md#create-manual) | 此模板在两个区域（具有标准吞吐量的数据库和容器）中创建一个 Core (SQL) API 帐户。 |
|[创建包含存储过程、触发器和 UDF 的 Azure Cosmos 帐户、数据库和容器](manage-with-templates.md#create-sproc) | 此模板在两个区域（具有存储过程、触发器和容器的 UDF）中创建了一个 Core (SQL) API 帐户。 |
|[创建具有 Azure AD 标识、角色定义和角色分配的 Azure Cosmos 帐户](manage-with-templates.md#create-rbac) | 此模板创建一个核心 (SQL) API 帐户，该帐户具有服务主体上的 Azure AD 标识、角色定义和角色分配。 |
|[为现有的 Azure Cosmos 帐户创建专用终结点](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  此模板为现有虚拟网络中的现有 Azure Cosmos Core (SQL) API 帐户创建专用终结点。 |
|[创建免费层 Azure Cosmos 帐户](manage-with-templates.md#free-tier) |  此模板在免费层上创建 Azure Cosmos DB Core (SQL) API 帐户。 |

有关参考文档，请参阅 [Azure Cosmos DB 的 Azure 资源管理器参考](/azure/templates/microsoft.documentdb/allversions)页。

## <a name="next-steps"></a>后续步骤

正在尝试为迁移到 Azure Cosmos DB 进行容量计划？ 可以使用有关现有数据库群集的信息进行容量计划。
* 若只知道现有数据库群集中的 vCore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](convert-vcore-to-request-unit.md) 
* 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-with-capacity-planner.md)