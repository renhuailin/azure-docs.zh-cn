---
title: 使用 Bicep 创建并管理 Azure Cosmos DB
description: 使用 Bicep 创建并配置 Azure Cosmos DB for Core (SQL) API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: b5a3fcdffe3c93a396bb607595b1ee02775a26f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698934"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-bicep"></a>使用 Bicep 管理 Azure Cosmos DB Core (SQL) API 资源

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

本文介绍如何使用 Bicep 部署和管理 Azure Cosmos DB 帐户、数据库和容器。

本文介绍 Core (SQL) API 帐户的 Bicep 示例。 你还可以找到 [Cassandra](../cassandra/manage-with-bicep.md)、[Gremlin](../graph/manage-with-bicep.md)、[MongoDB](../mongodb/manage-with-bicep.md) 和[表](../table/manage-with-bicep.md) API 的 Bicep 示例。

> [!IMPORTANT]
>
> * 帐户名称限制为 44 个字符，全部小写。
> * 若要更改吞吐量值，请使用更新的 RU/秒重新部署 Bicep 文件。
> * 当你在 Azure Cosmos 帐户中添加或删除位置时，无法同时修改其他属性。 必须单独执行这些操作。
> * 无法重命名 Azure Cosmos DB 资源，因为这违反了 Azure 资源管理器与资源 URI 的工作方式。
> * 若要在数据库级别预配吞吐量并跨所有容器进行共享，请将吞吐量值应用于数据库选项属性。

若要创建以下任何 Azure Cosmos DB 资源，请将以下示例复制到新的 Bicep 文件中。 可以选择创建一个要在使用不同名称和值部署同一资源的多个实例时使用的参数文件。 可以通过多种方式部署 Azure 资源管理器模板，这些方式包括：[Azure CLI](../../azure-resource-manager/bicep/deploy-cli.md)、[Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) 和 [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md)。

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>具有自动缩放吞吐量的 Azure Cosmos 帐户

在两个区域创建一个 Azure Cosmos 帐户，其中包含用于一致性和故障转移的选项，以及为启用了大多数策略选项的自动缩放吞吐量配置的数据库和容器。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-autoscale/main.bicep":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>具有分析存储的 Azure Cosmos 帐户

在一个区域中创建一个 Azure Cosmos 帐户，其中包含启用了分析 TTL 的容器和手动或自动缩放吞吐量的选项。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-analytical-store/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>具有标准预配吞吐量的 Azure Cosmos 帐户

在两个区域创建一个 Azure Cosmos 帐户，其中包含用于一致性和故障转移的选项，以及为启用了大多数策略选项的标准吞吐量配置的数据库和容器。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/main.bicep":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>具有服务器端功能的 Azure Cosmos DB 容器

创建包含存储过程、触发器和用户定义函数的 Azure Cosmos 帐户、数据库和容器。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-container-sprocs/main.bicep":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>使用 Azure AD 和 RBAC 的 Azure Cosmos DB 帐户

创建一个 Azure Cosmos 帐户、一个以原生方式维护的角色定义，以及一个为 AAD 标识提供的以原生方式维护的角色分配。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-rbac/main.bicep":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>免费层 Azure Cosmos DB 帐户

创建一个免费层 Azure Cosmos 帐户和一个具有共享吞吐量（最多可以与 25 个容器共享）的数据库。

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-free/main.bicep":::

## <a name="next-steps"></a>后续步骤

下面是一些其他资源：

* [Bicep 文档](../../azure-resource-manager/bicep/index.yml)
* [安装 Bicep 工具](../../azure-resource-manager/bicep/install.md)
