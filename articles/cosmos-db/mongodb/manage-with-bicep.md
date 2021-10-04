---
title: 使用 Bicep 创建和管理 MongoDB API for Azure Cosmos DB
description: 使用 Bicep 创建和配置 MongoDB API for Azure Cosmos DB。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: e6ded17c15b9a126a996f4e6f368a5ab5c735ed9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699359"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-bicep"></a>使用 Bicep 管理 Azure Cosmos DB MongoDB API 资源

[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

本文介绍如何使用 Bicep 部署和管理 Azure Cosmos DB for MongoDB API 帐户、数据库和集合。

本文介绍 Gremlin API 帐户的 Bicep 示例。 你还可以查找 [SQL](../sql/manage-with-bicep.md)、[Cassandra](../cassandra/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md) 和[表](../table/manage-with-bicep.md) API 的 Bicep 示例。

> [!IMPORTANT]
>
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请用更新的 RU/s 重新部署模板。
> * 当你在 Azure Cosmos 帐户中添加或删除位置时，无法同时修改其他属性。 必须单独执行这些操作。

若要创建以下任何 Azure Cosmos DB 资源，请将以下示例复制到新的 bicep 文件中。 可以选择创建一个要在使用不同名称和值部署同一资源的多个实例时使用的参数文件。 可以通过多种方式部署 Azure 资源管理器模板，包括 [Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) 和 [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md)。

<a id="create-autoscale"></a>

## <a name="mongodb-api-with-autoscale-provisioned-throughput"></a>具有自动缩放预配吞吐量的 MongoDB API

此模板会创建一个 Azure Cosmos for MongoDB API（3.2、3.6 或 4.0）帐户，其中包含两个在数据库级别共享自动缩放吞吐量的集合。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="mongodb-api-with-standard-provisioned-throughput"></a>具有标准预配吞吐量的 MongoDB API

创建一个 Azure Cosmos for MongoDB API（3.2、3.6 或 4.0）帐户，其中包含两个在数据库级别共享 400 RU/秒标准（手动）吞吐量的集合。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb/main.bicep":::

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Bicep 文档](../../azure-resource-manager/bicep/index.yml)
* [安装 Bicep 工具](../../azure-resource-manager/bicep/install.md)
* 尝试为迁移到 Azure Cosmos DB 进行容量规划？ 可以使用有关现有数据库群集的信息进行容量规划。
  * 若只知道现有数据库群集中的 vcore 和服务器数量，请阅读[使用 vCore 或 vCPU 估算请求单位](../convert-vcore-to-request-unit.md)
  * 若知道当前数据库工作负载的典型请求速率，请阅读[使用 Azure Cosmos DB 容量计划工具估算请求单位](estimate-ru-capacity-planner.md)
