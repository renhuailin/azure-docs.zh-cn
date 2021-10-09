---
title: 使用 Bicep 创建并管理 Azure Cosmos DB Gremlin API
description: 使用 Bicep 创建并配置 Azure Cosmos DB Gremlin API。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: a5ac7ad31cb09ab57948f9a7b1eb86e07ec608a5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699016"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-bicep"></a>使用 Bicep 管理 Azure Cosmos DB Gremlin API 资源

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

本文介绍如何使用 Bicep 部署和管理 Azure Cosmos DB Gremlin API 帐户、数据库和图。

本文介绍 Gremlin API 帐户的 Bicep 示例。 你还可以查找 [SQL](../sql/manage-with-bicep.md)、[Cassandra](../cassandra/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md) 和[表](../table/manage-with-bicep.md) API 的 Bicep 示例。

> [!IMPORTANT]
>
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请用更新的 RU/s 重新部署模板。
> * 当你在 Azure Cosmos 帐户中添加或删除位置时，无法同时修改其他属性。 必须单独执行这些操作。

若要创建以下任何 Azure Cosmos DB 资源，请将以下示例复制到新的 Bicep 文件中。 可以选择创建一个要在使用不同名称和值部署同一资源的多个实例时使用的参数文件。 可以通过多种方式部署 Azure 资源管理器模板，这些方式包括：[Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) 和 [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md)。

<a id="create-autoscale"></a>

## <a name="gremlin-api-with-autoscale-provisioned-throughput"></a>具有自动缩放预配吞吐量的 Gremlin API

为 Gremlin API（使用具有自动缩放吞吐量的数据库和图）创建一个 Azure Cosmos 帐户。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="gremlin-api-with-standard-provisioned-throughput"></a>具有标准预配吞吐量的 Gremlin API

为 Gremlin API（使用具有标准预配吞吐量的数据库和图）创建一个 Azure Cosmos 帐户。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin/main.bicep":::

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Bicep 文档](../../azure-resource-manager/bicep/index.yml)
* [安装 Bicep 工具](../../azure-resource-manager/bicep/install.md)
