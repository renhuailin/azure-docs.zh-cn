---
title: 使用 Azure 资源管理器模板添加 Azure 角色分配条件（预览版）- Azure ABAC
description: 了解如何使用 Azure 资源管理器模板和 Azure 基于角色的访问控制 (Azure RBAC) 在 Azure 角色分配中添加基于属性的访问控制 (ABAC) 条件。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 06/29/2021
ms.author: rolyon
ms.openlocfilehash: 6e64afaab3b367ed807f77e5ebc0214904ed763f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113094757"
---
# <a name="add-azure-role-assignment-conditions-using-azure-resource-manager-templates-preview"></a>使用 Azure 资源管理器模板添加 Azure 角色分配条件（预览版）

> [!IMPORTANT]
> Azure ABAC 和 Azure 角色分配条件目前为预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

[Azure 角色分配条件](conditions-overview.md)是一项额外检查，你可选择性地将其添加到角色分配中，从而提供更精细的访问控制。 例如，为了读取对象，你可添加要求对象具有特定标记的条件。 本文介绍如何使用 Azure 资源管理器模板为角色分配添加条件。

## <a name="prerequisites"></a>先决条件

有关添加角色分配条件的先决条件的信息，请参阅[有关条件的先决条件](conditions-prerequisites.md)。

## <a name="add-a-condition"></a>添加条件

以下模板演示了如何为[存储 Blob 数据读取者](built-in-roles.md#storage-blob-data-reader)角色分配条件。 该条件检查容器名称是否等于“blobs-example-container”。

若要使用模板，必须指定以下输入：

- 要为其分配角色的用户、组、托管标识或应用程序的 ID。
- 主体的类型，例如 `User`、`Group` 或 `ServicePrincipal`。 有关详细信息，请参阅[新服务主体](role-assignments-template.md#new-service-principal)。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "Principal ID to assign the role to"
            }
        },
        "principalType": {
            "type": "string",
            "metadata": {
                "description": "Type of principal"
            }
        },
        "roleAssignmentGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "New GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "StorageBlobDataReader": "[concat(subscription().Id, '/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1')]" // ID for Storage Blob Data Reader role, but can be any valid role ID
    },
    "resources": [
        {
            "name": "[parameters('roleAssignmentGuid')]",
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview", // API version to call the role assignment PUT.
            "properties": {
                "roleDefinitionId": "[variables('StorageBlobDataReader')]",
                "principalId": "[parameters('principalId')]",
                "principalType": "[parameters('principalType')]",
                "description": "Role assignment condition created with an ARM template",
                "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))", // Role assignment condition
                "conditionVersion": "2.0"
            }
        }
    ]
}
```

角色分配的范围是根据部署级别确定的。 下面是 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 和 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) 命令示例，演示如何在资源组范围启动部署。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName example-group -TemplateFile rbac-test.json -principalId $principalId -principalType "User"
```

```azurecli
az deployment group create --resource-group example-group --template-file rbac-test.json --parameters principalId=$principalId principalType="User"
```

## <a name="next-steps"></a>后续步骤

- [示例 Azure 角色分配条件（预览版）](../storage/common/storage-auth-abac-examples.md)
- [Azure 角色分配条件问题排查（预览版）](conditions-troubleshoot.md)
- [使用 Azure 资源管理器模板分配 Azure 角色](role-assignments-template.md)
