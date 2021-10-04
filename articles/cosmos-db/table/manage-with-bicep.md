---
title: 使用 Bicep 创建并管理 Azure Cosmos DB 表 API
description: 使用 Bicep 创建并配置 Azure Cosmos DB 表 API。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: fe911a945de6b7b15c49641b7aff3850c3759da2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594325"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-bicep"></a>使用 Bicep 管理 Azure Cosmos DB 表 API 资源

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

在本文中，你将了解如何使用 Bicep 来帮助部署和管理 Azure Cosmos DB 表 API 帐户与表。

本文仅提供表 API 帐户的示例。 你还可以查找 [Cassandra](../cassandra/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md) 和 [SQL](../sql/manage-with-bicep.md) 文章的 Bicep 示例。

> [!IMPORTANT]
>
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请用更新的 RU/s 重新部署模板。
> * 当你在 Azure Cosmos 帐户中添加或删除位置时，无法同时修改其他属性。 必须单独执行这些操作。

若要创建以下任何 Azure Cosmos DB 资源，请将以下示例复制到新的 bicep 文件中。 可以选择创建一个要在使用不同名称和值部署同一资源的多个实例时使用的参数文件。 可以通过多种方式部署 Azure 资源管理器模板，包括 [Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) 和 [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md)。

> [!TIP]
> 若要在使用表 API 时启用共享吞吐量，请在 Azure 门户中启用帐户级吞吐量。 不能使用 Bicep 设置帐户级共享吞吐量。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>具有自动缩放吞吐量的表的 Azure Cosmos 帐户

为包含一个可自动缩放吞吐量的表的表 API 创建 Azure Cosmos 帐户。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>具有标准预配吞吐量的表的 Azure Cosmos 帐户

为包含一个具有标准吞吐量的表的表 API 创建 Azure Cosmos 帐户。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table/main.bicep":::

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Bicep 文档](../../azure-resource-manager/bicep/index.yml)
* [安装 Bicep 工具](../../azure-resource-manager/bicep/install.md)
