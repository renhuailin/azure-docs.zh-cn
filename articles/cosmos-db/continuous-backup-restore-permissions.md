---
title: 配置还原 Azure Cosmos DB 帐户的权限。
description: 了解如何隔离和限制连续备份帐户对特定角色或主体的还原权限。 它演示了如何使用 Azure 门户、CLI 或定义自定义角色来分配内置角色。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 3614a85a6df2e793a73a2609d6f5762e4dc873fb
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527319"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>管理用于还原 Azure Cosmos DB 帐户的权限
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB 允许你隔离和限制对特定角色或主体的连续备份帐户的还原权限。 帐户所有者可以触发还原，并将角色分配给其他主体，以执行还原操作。 可以在订阅范围应用这些权限，或者在源帐户范围内更精确地应用这些权限，如下图所示：

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="执行还原操作所需的角色的列表。" lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

作用域是一组有权访问的资源，若要了解有关范围的详细信息，请参阅 [AZURE RBAC](../role-based-access-control/scope-overview.md) 文档。 在 Azure Cosmos DB 中，适用的作用域是大部分用例的源订阅和数据库帐户。 执行还原操作的主体应对目标资源组具有写入权限。

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>使用 Azure 门户分配用于还原的角色

若要执行还原操作，用户或主体需要具有还原 "还原/操作" 权限)  (，以及预配 (为 "写入" 权限) 的新帐户的权限。  若要授予这些权限，所有者可以将 "CosmosRestoreOperator" 和 "Cosmos DB 运算符" 内置角色分配给主体。

1. 登录到 [Azure 门户](https://portal.azure.com/)

1. 导航到你的订阅，转到 "**访问控制 (IAM)** " 选项卡，然后选择 "**添加**" "添加  >  **角色分配**"

1. 在 " **添加角色分配** " 窗格中，为 " **角色** " 字段选择 **CosmosRestoreOperator** 角色。 选择 " **用户"、"组" 或** " **分配访问权限** " 字段的服务主体，搜索用户名或电子邮件 ID，如下图所示：

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="分配 CosmosRestoreOperator 和 Cosmos DB 运算符角色。" border="true":::

1. 选择 " **保存** " 以授予 "还原/操作" 权限。

1. 重复步骤3，将 **Cosmos DB 操作员** 角色授予写入权限。 从 Azure 门户分配此角色时，它会向整个订阅授予还原权限。

## <a name="permission-scopes"></a>权限范围

|范围  |示例  |
|---------|---------|
|订阅 | /subscriptions/00000000-0000-0000-0000-000000000000 |
|资源组 | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|CosmosDB 可还原帐户资源 | /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

可还原的帐户资源可从 PowerShell 中命令的输出 `az cosmosdb restorable-database-account list --name <accountname>` 或 `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` PowerShell 中的 cmdlet 提取。 输出中的 name 属性表示可恢复帐户的 "instanceID"。 若要了解详细信息，请参阅 [PowerShell](continuous-backup-restore-powershell.md) 或 [CLI](continuous-backup-restore-command-line.md) 一文。

## <a name="permissions"></a>权限

若要执行与还原连续备份模式帐户相关的不同活动，需要以下权限：

|权限  |影响  |最小范围  |最大范围  |
|---------|---------|---------|---------|
|Microsoft. 资源/部署/验证/操作，Microsoft. 资源/部署/写入 | ARM 模板部署需要这些权限来创建还原的帐户。 请参阅下面的示例权限 [RestorableAction]() ，了解如何设置此角色。 | 不适用 | 不适用  |
|Microsoft.DocumentDB/databaseAccounts/write | 将帐户还原到资源组需要此权限 | 用于创建已还原帐户的资源组。 | 用于创建已还原帐户的订阅 |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action |在源可还原数据库帐户范围内需要此权限，以允许对其执行还原操作。  | 属于正在还原的源帐户的 "RestorableDatabaseAccount" 资源。 此值还由可还原数据库帐户资源的 "ID" 属性提供。 可还原帐户的示例为 `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>` | 包含可还原数据库帐户的订阅。 无法将资源组选作作用域。  |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read |在源可还原数据库帐户范围内需要此权限，以列出可还原的数据库帐户。  | 属于正在还原的源帐户的 "RestorableDatabaseAccount" 资源。 此值还由可还原数据库帐户资源的 "ID" 属性提供。 可还原帐户的示例为 `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| 包含可还原数据库帐户的订阅。 无法将资源组选作作用域。  |
|Microsoft.DocumentDB/位置/restorableDatabaseAccounts/*/read | 在源可恢复帐户范围内需要此权限，以允许读取可恢复的资源，例如可还原帐户的数据库和容器的列表。  | 属于正在还原的源帐户的 "RestorableDatabaseAccount" 资源。 此值还由可还原数据库帐户资源的 "ID" 属性提供。 可还原帐户的示例为 `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`| 包含可还原数据库帐户的订阅。 无法将资源组选作作用域。 |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Azure CLI 要在不同范围中还原的角色分配方案

可以将具有权限的角色分配给不同的作用域，以精确地控制哪些用户可以在订阅或给定帐户中执行还原操作。

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>分配功能以从订阅中的任何可恢复帐户还原

`CosmosRestoreOperator`在订阅级别分配内置角色

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>分配从特定帐户还原的功能

* 对特定资源组分配用户写入操作。 若要在资源组中创建新帐户，必须执行此操作。

* 向需要还原的特定可恢复数据库帐户分配 "CosmosRestoreOperator" 内置角色。 在下面的命令中，如果使用 CLI) ，则从 (的输出中的 "ID" 属性检索 "RestorableDatabaseAccount" 的作用域 `az cosmosdb restorable-database-account` ;  `Get-AzCosmosDBRestorableDatabaseAccount` 如果使用 PowerShell) ，则从 (中检索。

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>分配功能以便从资源组中的任何源帐户还原。
当前不支持此操作。

## <a name="custom-role-creation-for-restore-action-with-cli"></a>用于通过 CLI 执行还原操作的自定义角色创建

订阅所有者可以提供还原到任何其他 Azure AD 标识的权限。 还原权限基于操作： "Microsoft.DocumentDB/位置/restorableDatabaseAccounts/restore/action"，它应包括在其还原权限中。 有一个名为 "CosmosRestoreOperator" 的内置角色，其中包含此角色。 您可以使用此内置角色或创建自定义角色来分配权限。

下面的 RestorableAction 表示自定义角色。 必须显式创建此角色。 以下 JSON 模板创建具有 restore 权限的自定义角色 "RestorableAction"：

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

接下来，使用以下模板部署命令，通过 ARM 模板创建具有 restore 权限的角色：

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>后续步骤

* 使用 [Azure 门户](continuous-backup-restore-portal.md)、 [PowerShell](continuous-backup-restore-powershell.md)、 [CLI](continuous-backup-restore-command-line.md)或 [Azure 资源管理器](continuous-backup-restore-template.md)配置和管理连续备份。
* [连续备份模式的资源模型](continuous-backup-restore-resource-model.md)
