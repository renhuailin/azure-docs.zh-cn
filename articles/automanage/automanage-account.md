---
title: Azure Automanage 帐户
description: 了解 Automanage 帐户的工作原理以及创建方法。
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alsin
ms.openlocfilehash: d3a88c81f60eaf08f64326f2c53f4d5dfa886fa1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746750"
---
# <a name="automanage-accounts"></a>Automanage 帐户

Automanage 帐户是 Automanage 服务用来执行其自动化操作的标识。

在 Azure 门户体验中，在 VM 上启用 Automanage 时，“启用 Azure VM 最佳做法”边栏选项卡上有一个高级下拉列表，可用于分配或手动创建 Automanage 帐户。

Automanage 帐户将被授予订阅（包含加入 Automanage 的计算机）的“参与者”和“资源策略参与者”角色 。 你可以在跨多个订阅的计算机上使用同一 Automanage 帐户，这会向该 Automanage 帐户授予所有订阅的“参与者”和“资源策略参与者”权限 。

如果 VM 连接到其他订阅中的 Log Analytics 工作区，则还会向 Automanage 帐户授予该订阅中的“参与者”和“资源策略参与者”权限 。

如果要使用新的 Automanage 帐户启用 Automanage，则需要对订阅具有以下权限：“所有者”角色或“参与者”以及“用户访问管理员”角色  。

如果要使用现有的 Automanage 帐户启用 Automanage，必须在包含 VM 的资源组上具有“参与者”角色。

> [!NOTE]
> 禁用 Automanage 最佳做法时，会保留 Automanage 帐户对任何关联订阅的权限。 转到订阅的“标识和访问管理”页手动删除权限或删除 Automanage 帐户。 如果 Automanage 帐户仍在管理任何计算机，则无法将其删除。

## <a name="create-an-automanage-account"></a>创建 Automanage 帐户
可以使用门户或 ARM 模板创建 Automanage 帐户。

### <a name="portal"></a>门户
1. 导航到门户中的“Automanage”边栏选项卡
1. 单击“在现有计算机上启用”
1. 在“高级”下，单击“新建帐户”
1. 填写必填字段，然后单击“创建”

### <a name="arm-template"></a>ARM 模板
使用 ARM 模板创建 Automanage 帐户需要 2 个步骤：
1. 创建 Automanage 帐户
1. 向帐户授予足够的权限，使其能够为你执行操作
    1. 你将需要为此步骤创建的帐户的对象 ID。
        1. [此处](../active-directory/managed-identities-azure-resources/how-to-view-managed-identity-service-principal-portal.md#view-the-service-principal)提供了查找帐户服务主体（包括对象 ID）详细信息的步骤。
    1. 找到服务主体后，复制对象 ID。 保存此 ID，供下面委托权限时使用。

#### <a name="1-create-automanage-account-does-not-grant-permissions-to-it"></a>1. 创建 Automanage 帐户（不向其授予权限）
若要创建 Automanage 帐户，请将以下 ARM 模板保存为 `azuredeploy.json` 并运行以下 Azure CLI 命令。 完成后，请转到下面的第二个模板，向该帐户委托足够的权限。

```azurecli-interactive
az deployment group create --resource-group <resource group name> --template-file azuredeploy.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automanageAccountName": {
            "type": "String"
        },
        "location": {
            "type": "String"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "type": "Microsoft.Automanage/accounts",
            "name": "[parameters('automanageAccountName')]",
            "location": "[parameters('location')]",
            "identity": {
                "type": "SystemAssigned"
            }
        }
    ]
}
```
#### <a name="2-grant-permissions-to-the-automanage-account"></a>2. 向 Automanage 帐户授予权限
若要向 Automanage 帐户授予足够的权限，需要执行以下操作：
1. 将以下 ARM 模板保存为 `azuredeploy2.json`，并运行以下 Azure CLI 命令。
1. 出现提示时，请输入所创建并保存的 Automanage 帐户的对象 ID。

```azurecli-interactive
az deployment sub create --location <location> --template-file azuredeploy2.json
```

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "dateTime": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        }
    },
    "variables": {
        "contributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
        "resourcePolicyContributorRoleDefinitionID": "/providers/Microsoft.Authorization/roledefinitions/36243c78-bf99-498c-9df9-86d9f8d28608"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(concat(parameters('dateTime'), variables('contributorRoleDefinitionID')))]",
            "properties": {
                "roleDefinitionId": "[variables('contributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview",
            "name": "[guid(concat(parameters('dateTime'), variables('resourcePolicyContributorRoleDefinitionID')))]",
            "properties": {
                "roleDefinitionId": "[variables('resourcePolicyContributorRoleDefinitionID')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>后续步骤
* 了解用于 [Linux](./automanage-linux.md) 和 [Windows](./automanage-windows-server.md) 的 Automanage 服务