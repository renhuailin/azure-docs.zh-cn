---
title: 使用 Bicep 创建并管理 Azure Cosmos DB Cassandra API
description: 使用 Bicep 创建并配置 Azure Cosmos DB Cassandra API。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: 0433f6830100fc23420006ec2b980b7ccf7f9692
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594362"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-bicep"></a>使用 Bicep 管理 Azure Cosmos DB Cassandra API 资源

[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

本文介绍如何使用 Bicep 来部署和管理 Azure Cosmos DB Cassandra API 帐户、密钥空间与表。

本文介绍 Cassandra API 帐户的 Bicep 示例。 你还可以找到 [SQL](../sql/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md) 和[表](../table/manage-with-bicep.md) API 的 Bicep 示例。

> [!IMPORTANT]
>
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请用更新的 RU/s 重新部署模板。
> * 当你在 Azure Cosmos 帐户中添加或删除位置时，无法同时修改其他属性。 必须单独执行这些操作。

若要创建以下任何 Azure Cosmos DB 资源，请将以下示例复制到新的 Bicep 文件中。 可以选择创建一个要在使用不同名称和值部署同一资源的多个实例时使用的参数文件。 可以通过多种方式部署 Azure 资源管理器模板，这些方式包括：[Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) 和 [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md)。

<a id="create-autoscale"></a>

## <a name="cassandra-api-with-autoscale-provisioned-throughput"></a>具有自动缩放预配吞吐量的 Cassandra API

在两个区域创建一个 Azure Cosmos 帐户，其中包含用于一致性和故障转移的选项，以及为自动缩放吞吐量配置的密钥空间和表。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="cassandra-api-with-standard-provisioned-throughput"></a>具有标准预配吞吐量的 Cassandra API

在两个区域创建一个 Azure Cosmos 帐户，其中包含用于一致性和故障转移的选项，以及为标准吞吐量配置的密钥空间和表。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra/main.bicep":::

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Bicep 文档](../../azure-resource-manager/bicep/index.yml)
* [安装 Bicep 工具](../../azure-resource-manager/bicep/install.md)
