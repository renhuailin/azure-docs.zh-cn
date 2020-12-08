---
title: '将托管标识添加到 Service Fabric 托管群集节点类型 (预览版) '
description: 本文介绍如何将托管标识添加到 Service Fabric 托管群集节点类型
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 00e679b07a44b799b6ac6677201bb59eeddcd6cf
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841416"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>将托管标识添加到 Service Fabric 托管群集节点类型 (预览版) 

Service Fabric 托管群集中的每个节点类型都受虚拟机规模集的支持。 若要允许托管标识与托管群集节点类型一起使用，则已向 `vmManagedIdentity` 节点类型定义中添加了一个属性，其中包含可使用的标识列表 `userAssignedIdentities` 。 功能反映了如何在非托管群集中使用托管标识，如使用具有 [Azure Key Vault 虚拟机规模集扩展](https://docs.microsoft.com/azure/virtual-machines/extensions/key-vault-windows)的托管标识。


有关对节点类型使用托管标识的 Service Fabric 托管群集部署的示例，请参阅 [此模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI)。 有关支持的区域的列表，请参阅 [托管群集常见问题解答](https://docs.microsoft.com/azure/service-fabric/faq-managed-cluster#what-regions-are-supported-in-the-preview)。

> [!NOTE]
> 此功能目前仅支持用户分配的标识。

## <a name="prerequisites"></a>先决条件

开始之前：

* 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
* 如果计划使用 PowerShell，则 [安装](https://docs.microsoft.com/cli/azure/install-azure-cli) Azure CLI 以运行 CLI 参考命令。

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识 

可以在 Azure 资源管理器 (ARM) 模板的 "资源" 部分中定义用户分配的托管标识，以便在部署时创建：

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

或通过 PowerShell 创建：

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>使用 Service Fabric 资源提供程序添加角色分配

使用 Service Fabric 资源提供程序应用程序将角色分配添加到托管标识。 此分配允许 Service Fabric 资源提供程序将标识分配给托管群集的虚拟机规模集。 

在适用的情况下，必须使用以下值：

|“属性”|对应 Service Fabric 资源提供程序值|
|----|-------------------------------------|
|应用程序 ID|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|对象 ID|fbc587f2-66f5-4459-a027-bcd908b9d278|


|角色定义名称|角色定义 ID|
|----|-------------------------------------|
|托管的标识操作员|f1a07417-d97a-45cb-824c-7a7467783830
|



可以使用对象 ID 和角色定义 ID 在 resources 节中定义此角色分配：

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

或使用应用程序 ID 和角色定义 ID 通过 PowerShell 创建：

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

或对象 ID 和角色定义 ID：

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>向节点类型定义添加托管标识属性

最后，将 `vmManagedIdentity` 和 `userAssignedIdentities` 属性添加到托管群集的节点类型定义：

```json

"properties": {
    "isPrimary" : true,
    "vmInstanceCount": 5,
    "dataDiskSizeGB": 100,
    "vmSize": "Standard_D2",
    "vmImagePublisher" : "MicrosoftWindowsServer",
    "vmImageOffer" : "WindowsServer",
    "vmImageSku" : "2019-Datacenter",
    "vmImageVersion" : "latest",
    "vmManagedIdentity": {
        "userAssignedIdentities": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
        ]
    }
}
```

部署后，创建的托管标识已添加到指定节点类型的虚拟机规模集，可以按预期方式使用，就像在任何非托管群集中一样。

## <a name="troubleshooting"></a>故障排除

如果无法正确添加角色分配，则在部署时出现以下错误：

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Azure 门户部署错误显示客户端具有 SFRP 的对象/应用程序 ID 无权执行标识管理活动":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将应用部署到 Service Fabric 托管群集](https://docs.microsoft.com/azure/service-fabric/tutorial-managed-cluster-deploy-app) 
