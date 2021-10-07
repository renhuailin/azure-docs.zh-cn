---
title: 修复损坏的 Azure Automanage 帐户
description: 如果最近将包含 Automanage 帐户的订阅移动到了新租户，则需要重新配置它。 在本文中，你将了解如何
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2ce8bc5c26eb99ed19578a036a4569829bc44879
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458295"
---
# <a name="repair-an-automanage-account"></a>修复 Automanage 帐户
[Azure Automanage 帐户](./automanage-virtual-machines.md#automanage-account)是执行自动化操作所基于的安全性上下文或标识。 如果最近将包含 Automanage 帐户的订阅移动到了新租户，则需要重新配置该帐户。 若要重新配置该帐户，则需要重置标识类型，并为该帐户分配适当的角色。

## <a name="step-1-reset-the-automanage-account-identity-type"></a>步骤 1：重置 Automanage 帐户标识类型
使用以下 Azure 资源管理器 (ARM) 模板重置 Automanage 帐户标识类型。 使用 armdeploy.json 或类似的名称在本地保存文件。 记下 Automanage 帐户名和位置，因为它们是 ARM 模板必需的参数。

1. 使用下面的模板创建资源管理器部署。 使用 `identityType = None`。
    * 可使用 `az deployment sub create` 通过 Azure CLI 创建部署。 有关详细信息，请参阅 [az deployment sub](/cli/azure/deployment/sub)。
    * 可使用 `New-AzDeployment` 模块通过 PowerShell 创建部署。 有关详细信息，请参阅 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment)。

1. 使用 `identityType = SystemAssigned` 再次运行相同的 ARM 模板。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>步骤 2：为 Automanage 帐户分配适当的角色
Automanage 帐户需要订阅（包含 Automanage 所管理的 VM）上的参与者角色和资源策略参与者角色。 可使用 Azure 门户、ARM 模板或 Azure CLI 来分配这些角色。

如果使用的是 ARM 模板或 Azure CLI，则需要 Automanage 帐户的主体 ID（也称为对象 ID）。 （如果使用的是 Azure 门户，则无需此 ID。）可以使用以下方法查找此 ID：

- [Azure CLI](/cli/azure/ad/sp)：使用命令 `az ad sp list --display-name <name of your Automanage Account>`。

- Azure 门户：转到 Azure Active Directory，然后按名称搜索 Automanage 帐户。 在“企业应用程序”下，在显示 Automanage 帐户名时选择它。

### <a name="azure-portal"></a>Azure 门户
1. 在“订阅”下，转到包含自动托管的 VM 的订阅。
1. 请转到“访问控制(IAM)”。
1. 选择“添加角色分配”。
1. 选择“参与者”角色并输入 Automanage 帐户的名称。
1. 选择“保存”。
1. 重复步骤 3 到 5，这次使用资源策略参与者角色。

### <a name="arm-template"></a>ARM 模板
运行以下 ARM 模板。 需要 Automanage 帐户的主体 ID。 本部分的开头介绍了获取此 ID 的步骤。 在出现提示时输入此 ID。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
运行以下命令：

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>后续步骤
[详细了解 Azure Automanage](./automanage-virtual-machines.md)
