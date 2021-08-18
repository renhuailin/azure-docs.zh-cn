---
title: 创建 Azure 视频分析器帐户
description: 本主题介绍如何为 Azure 视频分析器创建帐户。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 1cb2f317ca712f2ad8ca911ecff0ac5e62cac0f5
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114603998"
---
# <a name="create-a-video-analyzer-account"></a>创建视频分析器帐户

若要开始使用 Azure 视频分析器，需要创建视频分析器帐户。 此帐户需要与存储帐户和[用户分配的托管标识][docs-uami]相关联。 托管标识需要具有存储帐户的[存储 Blob 数据参与者][docs-storage-access]角色和[参与者][docs-role-reader]角色的权限。 本文介绍创建新视频分析器帐户的步骤。

 可以使用 Azure 门户或 [Azure 资源管理器模板][docs-arm-template]创建视频分析器帐户。 为要使用的方法选择选项卡。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>在 Azure 门户中创建视频分析器帐户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 在顶部的搜索栏中输入“视频分析器”。
1. 单击“服务”下的“视频分析器” 。
1. 单击“添加”。
1. 在“创建视频分析器帐户”部分中输入所需的值。

    | 名称 | 说明 |
    | ---|---|
    |**订阅**|如果有多个订阅，请从有权访问的 Azure 订阅的列表中选择一个订阅。|
    |**资源组**|选择现有资源或创建新的资源。 资源组是共享生命周期、权限和策略的资源的集合。 在[此处](../../azure-resource-manager/management/overview.md#resource-groups)了解更多信息。|
    |视频分析器帐户名称|输入新视频分析器帐户的名称。 视频分析器帐户名称由小写字母或数字构成（不含空格），长度为 3 到 24 个字符。|
    |**位置**|选择用于存储视频分析器帐户的视频和元数据记录的地理区域。 下拉列表中仅显示可用的视频分析器区域。 |
    |**存储帐户**|选择一个存储帐户，以便为视频分析器帐户的视频内容提供 Blob 存储。 可以选择位于视频分析器帐户所在的地理区域内的现有存储帐户，也可以创建一个新的存储帐户。 在同一区域内会创建一个新的存储帐户。 适用于存储帐户名的规则对视频分析器帐户同样适用。<br/>|
    |用户标识|选择新视频分析器帐户用于访问存储帐户的用户托管标识。 可以选择现有的用户分配的托管标识，也可以创建新的托管标识。 将为用户分配的托管标识分配存储帐户的[存储 Blob 数据参与者][docs-storage-access]和[读取者][docs-role-reader]角色。

1. 在窗体底部单击“查看 + 创建”。

## <a name="template"></a>[模板](#tab/template/)

[!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

### <a name="create-a-video-analyzer-account-using-a-template"></a>使用模板创建视频分析器帐户

该模板中定义了以下资源：

- [**Microsoft.Media/videoAnalyzers**](/azure/templates/Microsoft.Media/videoAnalyzers)：视频分析器的帐户资源。
- [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts)：视频分析器用于存储视频和元数据的存储帐户。
- [**Microsoft.ManagedIdentity/userAssignedIdentities**](/azure/templates/Microsoft.ManagedIdentity/userAssignedIdentities)：视频分析器用于访问存储的用户分配的托管标识。
- [**Microsoft.Storage/storageAccounts/providers/roleAssignments**](/azure/templates/microsoft.authorization/roleassignments)：使视频分析器能够访问存储帐户的角色分配。

<!-- TODO replace with a reference like this:
:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.compute/vm-simple-linux/azuredeploy.json":::
-->

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namePrefix": {
            "metadata": {
                "description": "Used to qualify the names of all of the resources created in this template."
            },
            "defaultValue": "avasample",
            "type": "string",
            "minLength": 3,
            "maxLength": 13
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
        "accountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
        "managedIdentityName": "[concat(parameters('namePrefix'),'-',resourceGroup().name,'-storage-access-identity')]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-10-01",
            "name": "deploy-storage-and-identity",
            "properties": {
                "mode": "Incremental",
                "expressionEvaluationOptions": {
                    "scope": "Inner"
                },
                "parameters": {
                    "namePrefix": {
                        "value": "[parameters('namePrefix')]"
                    },
                    "managedIdentityName": {
                        "value": "[variables('managedIdentityName')]"
                    }
                },
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "namePrefix": {
                            "type": "string"
                        },
                        "managedIdentityName": {
                            "type": "string"
                        }
                    },
                    "variables": {
                        "storageAccountName": "[concat(parameters('namePrefix'),uniqueString(resourceGroup().id))]",
                        "managedIdentityName": "[parameters('managedIdentityName')]",
                        "storageBlobDataContributorAssignment": "[guid('Storage Blob Data Contributor',variables('managedIdentityName'))]",
                        "storageBlobDataContributorDefinitionId": "[concat(resourceGroup().id, '/providers/Microsoft.Authorization/roleDefinitions/', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')]",
                        "readerAssignment": "[guid('Reader',variables('managedIdentityName'))]",
                        "readerDefinitionId": "[concat(resourceGroup().id, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
                    },
                    "resources": [
                        {
                            "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
                            "name": "[variables('managedIdentityName')]",
                            "apiVersion": "2015-08-31-preview",
                            "location": "[resourceGroup().location]"
                        },
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "apiVersion": "2019-04-01",
                            "name": "[variables('storageAccountName')]",
                            "location": "[resourceGroup().location]",
                            "sku": {
                                "name": "Standard_LRS"
                            },
                            "kind": "StorageV2",
                            "properties": {
                                "accessTier": "Hot"
                            }
                        },
                        {
                            "name": "[concat(variables('storageAccountName'), '/Microsoft.Authorization/', variables('storageBlobDataContributorAssignment'))]",
                            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
                            "apiVersion": "2021-04-01-preview",
                            "dependsOn": [
                                "[variables('managedIdentityName')]",
                                "[variables('storageAccountName')]"
                            ],
                            "properties": {
                                "roleDefinitionId": "[variables('storageBlobDataContributorDefinitionId')]",
                                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName')), '2018-11-30').principalId]",
                                "principalType": "ServicePrincipal"
                            }
                        },
                        {
                            "name": "[concat(variables('storageAccountName'), '/Microsoft.Authorization/', variables('readerAssignment'))]",
                            "type": "Microsoft.Storage/storageAccounts/providers/roleAssignments",
                            "apiVersion": "2021-04-01-preview",
                            "dependsOn": [
                                "[variables('managedIdentityName')]",
                                "[variables('storageAccountName')]"
                            ],
                            "properties": {
                                "roleDefinitionId": "[variables('readerDefinitionId')]",
                                "principalId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName')), '2018-11-30').principalId]",
                                "principalType": "ServicePrincipal"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        },
        {
            "type": "Microsoft.Media/videoAnalyzers",
            "comments": "The Azure Video Analyzer account",
            "apiVersion": "2021-05-01-preview",
            "name": "[variables('accountName')]",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "deploy-storage-and-identity"
            ],
            "properties": {
                "storageAccounts": [
                    {
                        "id": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
                        "identity": {
                            "userAssignedIdentity": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName'))]"
                        }
                    }
                ]
            },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities',variables('managedIdentityName'))]": {}
                }
            }
        }
    ],
    "outputs": { }
}
```

> [!NOTE]
> 模板将嵌套部署用于角色分配，确保在部署视频分析器帐户资源之前可使用此部署。

### <a name="deploy-the-template"></a>部署模板

1. 要登录到 Azure 并打开模板，请单击“部署到 Azure”按钮。

    [![部署到 Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/ava-click-to-deploy)

1. 选择或输入以下值。 使用可用的默认值。

    - 订阅：选择一个 Azure 订阅。
    - 资源组：从下拉列表中选择现有资源组，或选择“新建”并输入资源组的唯一名称，然后单击“确定” 。
    - 位置：选择一个位置。  例如 **美国西部 2**。
    - **名称前缀**：提供作为资源名称前缀的字符串（建议使用默认值）。

1. 选择“查看 + 创建”。 验证完成后，选择“创建”以创建和部署 VM。

使用 Azure 门户部署模板。 除了 Azure 门户，还可以使用 Azure CLI、Azure PowerShell 和 REST API。 若要了解其他部署方法，请参阅[部署模板](../../azure-resource-manager/templates/deploy-cli.md)。

### <a name="review-deployed-resources"></a>查看已部署的资源

你可以使用 Azure 门户查看创建的帐户和其他资源。 部署完成后，请选择“转到资源组”以查看帐户和其他资源。

### <a name="clean-up-resources"></a>清理资源

如果不再需要资源组，可以将其删除，这将删除帐户和资源组中的所有资源。

1. 选择“资源组”。
1. 在资源组页上，选择“删除”。
1. 出现提示时，键入资源组的名称，然后选择“删除”。

---

### <a name="next-steps"></a>后续步骤

了解如何[在 IoT Edge 设备上部署视频分析器][docs-deploy-on-edge]。

<!-- links -->
[docs-uami]: ../../active-directory/managed-identities-azure-resources/overview.md
[docs-storage-access]: ../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor
[docs-role-reader]: ../../role-based-access-control/built-in-roles.md#reader
[docs-arm-template]: ../../azure-resource-manager/templates/overview.md
[docs-deploy-on-edge]: deploy-iot-edge-device.md
[click-to-deploy]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fgist.githubusercontent.com%2Fbennage%2F58523b2e6a4d3bf213f16893d894dcaf%2Fraw%2Fazuredeploy.json
<!-- TODO update the link above! -->
