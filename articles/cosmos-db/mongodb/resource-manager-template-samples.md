---
title: 用于 Azure Cosmos DB API for MongoDB 的资源管理器模板
description: 使用 Azure 资源管理器模板创建和配置 Azure Cosmos DB API for MongoDB。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 10/14/2020
ms.author: mjbrown
ms.openlocfilehash: 654b9694661ac99603ccaea960f65388cf642d46
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778265"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>使用 Azure 资源管理器模板管理 Azure Cosmos DB MongoDB API 资源
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

本文介绍如何使用 Azure 资源管理器模板来帮助部署和管理 Azure Cosmos DB for MongoDB API 帐户、数据库和集合。

本文仅提供 Azure Cosmos DB API for MongoDB 的示例，若要查找其他 API 类型帐户的示例，请参阅以下文章：将 Azure 资源管理器模板与适用于 [Cassandra](../cassandra/templates-samples.md)、[Gremlin](../templates-samples-gremlin.md)、[SQL](../templates-samples-sql.md)、[表](../table/resource-manager-templates.md)的 Azure Cosmos DB API 配合使用。

> [!IMPORTANT]
>
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请用更新的 RU/s 重新部署模板。
> * 当你在 Azure Cosmos 帐户中添加或删除位置时，无法同时修改其他属性。 必须单独执行这些操作。

若要创建以下任何 Azure Cosmos DB 资源，请将下列示例模板复制到新的 json 文件中。 在部署具有不同名称和值的同一资源的多个实例时，可以选择创建要使用的参数 json 文件。 可以通过多种方式部署 Azure 资源管理器模板，包括 [Azure 门户](../../azure-resource-manager/templates/deploy-portal.md)、[Azure CLI](../../azure-resource-manager/templates/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) 和 [GitHub](../../azure-resource-manager/templates/deploy-to-azure-button.md)。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>预配了自动缩放吞吐量的 Azure Cosmos for MongoDB 帐户

此模板将创建一个 Azure Cosmos for MongoDB API 帐户（3.2 或 3.6），其中包含两个在数据库级别共享自动缩放吞吐量的集合。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>预配了标准吞吐量的 Azure Cosmos for MongoDB 帐户

此模板将创建一个 Azure Cosmos for MongoDB API 帐户（3.2 或 3.6），其中包含两个在数据库级别共享 400 RU/s 标准（手动）吞吐量的集合。 此模板还支持从 Azure 快速入门模板库进行一键部署。

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="部署到 Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Azure 资源管理器文档](../../azure-resource-manager/index.yml)
* [Azure Cosmos DB 资源提供程序架构](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 快速入门模板](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [排查常见 Azure 资源管理器部署错误](../../azure-resource-manager/templates/common-deployment-errors.md)