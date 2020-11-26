---
title: 修复损坏的 Azure Automanage 帐户
description: 如果最近将包含 Automanage 帐户的订阅移动到新租户，则需要对其进行重新配置。 本文介绍了如何操作。
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 4694fa679c7bbff309a0452219ff39bacf2488c4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183696"
---
# <a name="repair-an-automanage-account"></a>修复 Automanage 帐户
你的 [Azure Automanage 帐户](./automanage-virtual-machines.md#automanage-account) 是在其下执行自动操作的安全上下文或标识。 如果最近将包含 Automanage 帐户的订阅移动到新租户，则需要重新配置该帐户。 若要对其进行重新配置，需要重置标识类型并为该帐户分配适当的角色。

## <a name="step-1-reset-the-automanage-account-identity-type"></a>步骤1：重置 Automanage 帐户标识类型
使用以下 Azure 资源管理器 (ARM) 模板重置 Automanage 帐户标识类型。 将该文件在本地保存为 armdeploy.js或类似名称。 记下你的 Automanage 帐户名称和位置，因为它们是 ARM 模板中的必需参数。

1. 使用以下模板创建资源管理器部署。 请使用 `identityType = None`。
    * 您可以使用在 Azure CLI 中创建部署 `az deployment sub create` 。 有关详细信息，请参阅 [az deployment sub](/cli/azure/deployment/sub)。
    * 你可以使用模块在 PowerShell 中创建部署 `New-AzDeployment` 。 有关详细信息，请参阅 [AzDeployment](/powershell/module/az.resources/new-azdeployment)。

1. 再次运行相同的 ARM 模板 `identityType = SystemAssigned` 。

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

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>步骤2：为 Automanage 帐户分配适当的角色
Automanage 帐户需要订阅上包含 Automanage 所管理的 Vm 的参与者和资源策略参与者角色。 可以使用 Azure 门户、ARM 模板或 Azure CLI 来分配这些角色。

如果使用的是 ARM 模板或 Azure CLI，则需要主体 ID (也称为 Automanage 帐户的对象 ID) 。  (如果使用 Azure 门户，则不需要 ID。 ) 可以使用以下方法找到此 ID：

- [Azure CLI](/cli/azure/ad/sp)：使用命令 `az ad sp list --display-name <name of your Automanage Account>` 。

- Azure 门户：中转到 **Azure Active Directory** 并按名称搜索你的 Automanage 帐户。 在 " **企业应用程序**" 下，选择显示的 Automanage 帐户名称。

### <a name="azure-portal"></a>Azure 门户
1. 在 " **订阅**" 下，前往包含 automanaged vm 的订阅。
1. 请 **访问 (IAM) 的 "访问控制**"。
1. 选择 " **添加角色分配**"。
1. 选择 " **参与者** " 角色，并输入 Automanage 帐户的名称。
1. 选择“保存”。
1. 重复步骤3到5，这一次使用 **资源策略参与者** 角色。

### <a name="arm-template"></a>ARM 模板
运行以下 ARM 模板。 需要 Automanage 帐户的主体 ID。 本部分的开头介绍了获取此方法的步骤。 在出现提示时输入 ID。

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
[了解有关 Azure Automanage 的详细信息](./automanage-virtual-machines.md)