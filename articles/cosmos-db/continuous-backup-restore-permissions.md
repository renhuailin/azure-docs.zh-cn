---
title: 配置 Azure Cosmos DB 帐户的还原权限。
description: 了解如何隔离和限制将连续备份帐户还原为特定角色或主体的权限。 演示如何使用 Azure 门户、CLI 分配内置角色或定义自定义角色。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: d566a2ee66df4adb810cb5908da3c47657fab418
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733185"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>管理 Azure Cosmos DB 帐户的还原权限
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB 允许隔离和限制将连续备份帐户还原为特定角色或主体的权限。 帐户所有者可以触发还原，并将角色分配给其他主体以执行还原操作。 可以在订阅范围或源帐户范围（更精细的层面）内应用这些权限，如下图所示：

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="执行还原操作所需的角色的列表。" lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

范围是一组有权访问的资源，若要了解有关范围的详细信息，请参阅 [Azure RBAC](../role-based-access-control/scope-overview.md) 文档。 在 Azure Cosmos DB 中，适用范围是大部分用例的源订阅和数据库帐户。 执行还原操作的主体应对目标资源组拥有写入权限。

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>使用 Azure 门户分配还原角色

若要执行还原，用户或主体需要拥有还原权限（即还原/操作权限），以及预配新帐户的权限（即写入权限）。  若要授予这些权限，所有者可以向主体分配 `CosmosRestoreOperator` 和 `Cosmos DB Operator` 内置角色。

1. 登录到 [Azure 门户](https://portal.azure.com/)

1. 导航到你的订阅，转到“访问控制(IAM)”选项卡，然后选择“添加” > “添加角色分配”

1. 在“添加角色分配”窗格的“角色”字段中，选择“CosmosRestoreOperator”角色。 在“将访问权限分配到”字段中选择“用户、组或服务主体”，然后搜索用户名或电子邮件 ID，如下图所示：

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="分配 CosmosRestoreOperator 和 Cosmos DB 操作员角色。" border="true":::

1. 选择“保存”以授予“还原/操作”权限。

1. 对“Cosmos DB 操作员”角色重复步骤 3 以授予写入权限。 从 Azure 门户分配此角色时，会授予整个订阅的还原权限。

## <a name="permission-scopes"></a>权限范围

|范围  |示例  |
|---------|---------|
|订阅 | /subscriptions/00000000-0000-0000-0000-000000000000 |
|资源组 | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|CosmosDB 可还原帐户资源 | /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

可从 CLI 中 `az cosmosdb restorable-database-account list --name <accountname>` 命令的输出或 PowerShell 中的 `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` cmdlet 提取可还原帐户资源。 输出中的名称属性表示可还原帐户的 `instanceID`。 

## <a name="permissions"></a>权限

需要以下权限才能执行与还原连续备份模式帐户相关的不同活动：

> [!NOTE]
> 权限可以分配给帐户范围或订阅范围的可还原数据库帐户。 不支持在资源组范围内分配权限。

|权限  |影响  |最小范围  |最大范围  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | ARM 模板部署需要这些权限才能创建已还原帐户。 有关如何设置此角色的信息，请参阅下面的示例权限 [RestorableAction](#custom-restorable-action)。 | 不适用 | 不适用  |
|`Microsoft.DocumentDB/databaseAccounts/write` | 需要此权限才能将帐户还原到资源组 | 在其下创建已还原帐户的资源组。 | 在其下创建已还原帐户的订阅 |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` </br> 不能选择资源组作为权限范围。 |在源可还原数据库帐户范围内需要此权限，以允许对其执行还原操作。  | 属于正在还原的源帐户的 RestorableDatabaseAccount 资源。 此值还由可还原数据库帐户资源的 `ID` 属性指定。 可还原帐户的示例是 /subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/ *\<guid-instanceid\>* | 包含可还原数据库帐户的订阅。  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` </br> 不能选择资源组作为权限范围。 |在源可还原数据库帐户范围内需要此权限，以列出可还原的数据库帐户。  | 属于正在还原的源帐户的 RestorableDatabaseAccount 资源。 此值还由可还原数据库帐户资源的 `ID` 属性指定。 可还原帐户的示例是 /subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/ *\<guid-instanceid\>*| 包含可还原数据库帐户的订阅。 |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` </br> 不能选择资源组作为权限范围。 | 在源可还原帐户范围内需要此权限，以允许读取可还原资源，例如可还原帐户的数据库和容器的列表。  | 属于正在还原的源帐户的 RestorableDatabaseAccount 资源。 此值还由可还原数据库帐户资源的 `ID` 属性指定。 可还原帐户的示例是 /subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/ *\<guid-instanceid\>*| 包含可还原数据库帐户的订阅。 |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>要在不同范围中还原的 Azure CLI 角色分配方案

可以将具有权限的角色分配给不同的范围，以精确地控制哪些用户可以在订阅或给定帐户中执行还原操作。

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>分配从订阅中的任何可还原帐户还原的功能

在订阅级别分配 `CosmosRestoreOperator` 内置角色

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>分配从特定帐户还原的功能

* 对特定资源组分配用户写入操作。 在资源组中创建新帐户时需要执行此操作。

* 将 CosmosRestoreOperator 内置角色分配给需要还原的特定可还原数据库帐户。 在下面的命令中，将从 `az cosmosdb restorable-database-account`（如果使用 CLI）或 `Get-AzCosmosDBRestorableDatabaseAccount`（如果使用 PowerShell）的输出中的 `ID` 属性检索 RestorableDatabaseAccount 的范围。

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>分配从资源组中的任何源帐户还原的功能。
目前不支持此操作。

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>用于通过 CLI 执行还原操作的自定义角色创建

订阅所有者可以提供还原到任何其他 Azure AD 标识的权限。 还原权限基于操作：`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action`，并且应包括在其还原权限中。 有一个名为 CosmosRestoreOperator 的内置角色，其中包含此角色。 可以使用此内置角色分配权限，也可以创建自定义角色。

下面的 RestorableAction 表示自定义角色。 必须显式创建此角色。 以下 JSON 模板创建具有还原权限的自定义角色 RestorableAction：

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

接下来，使用以下模板部署命令，通过 ARM 模板创建具有还原权限的角色：

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](provision-account-continuous-backup.md#provision-portal)、[PowerShell](provision-account-continuous-backup.md#provision-powershell)、[CLI](provision-account-continuous-backup.md#provision-cli) 或 [Azure 资源管理器](provision-account-continuous-backup.md#provision-arm-template)预配连续备份。
* 使用 [Azure 门户](restore-account-continuous-backup.md#restore-account-portal)、[PowerShell](restore-account-continuous-backup.md#restore-account-powershell)、[CLI](restore-account-continuous-backup.md#restore-account-cli) 或 [Azure 资源管理器](restore-account-continuous-backup.md#restore-arm-template)还原帐户。
* [将帐户从定期备份迁移到连续备份](migrate-continuous-backup.md)。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
