---
title: Azure Cosmos DB 中的 Azure 基于角色的访问控制
description: 了解 Azure Cosmos DB 如何使用 Active Directory 集成 (Azure RBAC) 来提供数据库保护。
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/17/2021
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 623d541b3b004bf7d85537674bf17aa89528e767
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284627"
---
# <a name="azure-role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB 中的 Azure 基于角色的访问控制
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> Azure Cosmos DB 中的 Azure RBAC 支持仅适用于管理平面操作。 本文介绍了有关 Azure Cosmos DB 中管理平面操作的基于角色的访问控制。 如果使用的是数据平面操作，则使用主键、资源令牌或 Azure Cosmos DB RBAC 来保护数据。 若要详细了解应用于数据平面操作的基于角色的访问控制，请参阅[安全地访问数据](secure-access-to-data.md)和 [Azure Cosmos DB RBAC](how-to-setup-rbac.md) 这两篇文章。

Azure Cosmos DB 为 Azure Cosmos DB 中的常见管理方案提供内置的 Azure 基于角色的访问控制 (Azure RBAC)。 在 Azure Active Directory 中创建了配置文件的个人可将这些 Azure 角色分配给用户、组、服务主体或托管标识，以授予或拒绝对 Azure Cosmos DB 中的资源和操作的访问权限。 角色分配范围仅限控制平面访问，包括对 Azure Cosmos 帐户、数据库、容器和套餐（吞吐量）的访问。

## <a name="built-in-roles"></a>内置角色

下面是 Azure Cosmos DB 支持的内置角色：

|**内置角色**  |**说明**  |
|---------|---------|
|[DocumentDB 帐户参与者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可管理 Azure Cosmos DB 帐户。|
|[Cosmos DB 帐户读取者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以读取 Azure Cosmos DB 帐户数据。|
|[Cosmos 备份操作员](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)| 可以为支持定期备份的数据库或容器向 Azure 门户提交还原请求。 可以修改 Azure 门户上的备份间隔和保留期。 无法访问任何数据或使用数据资源管理器。  |
| [CosmosRestoreOperator](../role-based-access-control/built-in-roles.md) | 可以对连续备份模式下的 Azure Cosmos DB 帐户执行还原操作。|
|[Cosmos DB 操作员](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|可预配 Azure Cosmos 帐户、数据库和容器。 无法访问任何数据或使用数据资源管理器。|

## <a name="identity-and-access-management-iam"></a>标识和访问管理 (IAM)

Azure 门户中的“访问控制(IAM)”窗格用于针对 Azure Cosmos 资源配置 Azure 基于角色的访问控制。 角色将应用到 Active Directory 中的用户、组、服务主体和托管标识。 对于个人和组，可使用内置角色或自定义角色。 以下屏幕截图显示在 Azure 门户中使用访问控制 (IAM) 的 Active Directory 集成 (Azure RBAC)：

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Azure 门户中的访问控制 (IAM) - 演示数据库安全性。":::

## <a name="custom-roles"></a>自定义角色

除内置角色以外，用户还可以在 Azure 中创建[自定义角色](../role-based-access-control/custom-roles.md)，并将这些角色应用到其 Active Directory 租户内的所有订阅中的服务主体。 自定义角色可让用户使用一组自定义的资源提供程序操作来创建 Azure 角色定义。 若要了解可以使用哪些操作来为 Azure Cosmos DB 生成自定义角色，请参阅 [Azure Cosmos DB 资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

> [!TIP]
> 如果自定义角色需要访问存储在 Cosmos DB 中的数据或要在 Azure 门户中使用数据资源管理器，则这些角色必须具有 `Microsoft.DocumentDB/databaseAccounts/listKeys/*` 操作。

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>阻止从 Azure Cosmos DB SDK 进行更改

可以锁定 Azure Cosmos DB 资源提供程序，以防止从使用帐户密钥连接的客户端（即通过 Azure Cosmos SDK 连接的应用程序）对资源做出任何更改。 对于需要更高程度的控制和生产环境管理的用户，此功能可能是理想的。 阻止从 SDK 进行更改还会启用资源锁和控制平面操作诊断日志等功能。 将阻止通过 Azure Cosmos DB SDK 连接的客户端更改 Azure Cosmos 帐户、数据库、容器和吞吐量的任何属性。 涉及对 Cosmos 容器本身进行数据读取和写入的操作不会受到影响。

此功能启用时，对任何资源的更改都只能由具有正确 Azure 角色和 Azure Active Directory 凭据（包括托管服务标识）的用户来进行。

> [!WARNING]
> 启用此功能可能对应用程序造成影响。 确保在启用前了解此影响。

### <a name="check-list-before-enabling"></a>启用前的核对清单

此设置将阻止从使用帐户密钥连接的任何客户端（包括任何 Cosmos DB SDK）、通过帐户密钥连接的任何工具或 Azure 门户对任何 Cosmos 资源做出任何更改。 若要防止在启用此功能后应用程序出现问题或错误，请在启用此功能前检查应用程序或 Azure 门户用户是否执行以下任何操作，包括：

- 更改 Cosmos 帐户，包括更改任何属性或者添加或删除区域。

- 创建、删除子资源（例如数据库和容器）。 其中包括用于其他 API 的资源，例如 Cassandra、MongoDB、Gremlin 和表资源。

- 更新数据库或容器级别资源的吞吐量。

- 修改容器属性，包括索引策略、TTL 和唯一键。

- 修改存储过程、触发器或用户定义的函数。

如果应用程序（或者用户通过 Azure 门户）执行这些操作中的任何一种，则需要将它们迁移，以通过 [ARM 模板](./manage-with-templates.md)、[PowerShell](manage-with-powershell.md)、[Azure CLI](manage-with-cli.md)、REST 或 [Azure 管理库](https://github.com/Azure-Samples/cosmos-management-net)来执行。 请注意，可使用[多种语言](/azure/index?product=featured#languages-and-tools)进行 Azure 管理。

### <a name="set-via-arm-template"></a>通过 ARM 模板进行设置

若要使用 ARM 模板设置此属性，请更新现有模板或为当前部署导出新模板，然后，将 `"disableKeyBasedMetadataWriteAccess": true` 包含到 `databaseAccounts` 资源的属性。 下面是具有此属性设置的 Azure 资源管理器模板的基本示例。

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> 在使用此属性进行部署时，请确保包含用于帐户和子资源的其他属性。 请勿按原样部署此模板，否则它将重置所有的帐户属性。

### <a name="set-via-azure-cli"></a>通过 Azure CLI 进行设置

若要启用 Azure CLI，请使用以下命令：

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>通过 PowerShell 进行设置

若要启用 Azure PowerShell，请使用以下命令：

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/overview.md)
- [Azure 自定义角色](../role-based-access-control/custom-roles.md)
- [Azure Cosmos DB 资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)