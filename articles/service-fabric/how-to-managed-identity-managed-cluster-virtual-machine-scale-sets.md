---
title: 将托管标识添加到 Service Fabric 托管群集节点类型
description: 本文介绍如何将托管标识添加到 Service Fabric 托管群集节点类型
ms.topic: how-to
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6cf2d65fe90656fe3025e438a57ea60fe17abd0d
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112453082"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type"></a>将托管标识添加到 Service Fabric 托管群集节点类型

Service Fabric 托管群集中的每个节点类型都受虚拟机规模集的支持。 为了配合使用托管标识和托管群集节点类型，已将属性 `vmManagedIdentity` 添加到包含可以使用的标识列表 `userAssignedIdentities` 的节点类型定义中。 功能反映了如何在非托管群集中使用托管标识，例如配合使用托管标识与 [Azure Key Vault 虚拟机规模集扩展](../virtual-machines/extensions/key-vault-windows.md)。

有关对节点类型使用托管标识的 Service Fabric 托管群集部署的示例，请参阅[此模板](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI)。 该示例涉及到两个模板：

1. 托管标识和角色分配：用于创建托管标识和角色分配的模板，Service Fabric RP 将标识分配给托管群集的虚拟机规模集。 在节点类型资源上使用托管标识之前，应仅部署一次。

2. 托管群集和节点类型：Service Fabric 托管群集和节点类型资源的模板，使用之前创建的托管标识。

> [!NOTE]
> 此功能目前仅支持用户分配的标识。

## <a name="prerequisites"></a>先决条件

开始之前：

* 如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。
* 如果计划使用 PowerShell，请[安装](/cli/azure/install-azure-cli) Azure CLI 来运行 CLI 参考命令。

## <a name="1-create-identity-and-role-assignment"></a>1. 创建标识和角色分配

### <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

可以在 Azure 资源管理器 (ARM) 模板的“资源”部分中定义用户分配的托管标识，以便在部署时创建：

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
 New-AzResourceGroup -Name <managedIdentityRGName> -Location <location>
New-AzUserAssignedIdentity -ResourceGroupName <managedIdentityRGName> -Name <userAssignedIdentityName>
```

### <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>使用 Service Fabric 资源提供程序添加角色分配

使用 Service Fabric 资源提供程序应用程序将角色分配添加到托管标识。 通过这种分配，Service Fabric 资源提供程序可以将上一步中创建的标识分配给托管群集的虚拟机规模集。 此为一次性步骤

获取 Service Fabric 资源提供程序应用程序的服务主体：

```powershell
Login-AzAccount
Select-AzSubscription -SubscriptionId <SubId>
Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
```

> [!NOTE]
> 确保你位于正确的订阅中，如果订阅位于其他租户中，则主体 ID 将更改。

```powershell
ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
ObjectType            : ServicePrincipal
DisplayName           : Azure Service Fabric Resource Provider
Id                    : 00000000-0000-0000-0000-000000000000
```

当在模板或 PowerShell 命令上适用时，将上一个输出的 ID 用作 principalId，将下面的角色定义 ID 用作 roleDefinitionId  ：

|角色定义名称|角色定义 ID|
|----|-------------------------------------|
|托管的标识操作员|f1a07417-d97a-45cb-824c-7a7467783830|


可以使用主体 ID 和角色定义 ID 在“资源”部分模板中定义此角色分配：

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
        "principalId": "00000000-0000-0000-0000-000000000000" 
    } 
}, 
```
> [!NOTE]
> vmIdentityRoleNameGuid 应为有效的 GUID。 如果再次部署同一模板（包括此角色分配），请确保 GUID 与最初使用的相同，如果不同则删除此资源，因为它只需创建一次。

或者使用主体 ID 和角色定义名称通过 PowerShell 创建：

```powershell
New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

### <a name="deploy-managed-identity-and-role-assignment"></a>部署托管标识和角色分配。
运行 New-AzResourceGroupDeployment cmdlet 以创建托管标识并添加角色分配：

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <managedIdentityRGName> -TemplateFile ".\MangedIdentityAndSfrpRoleAssignment.json" -TemplateParameterFile ".\MangedIdentityAndSfrpRoleAssignment.Parameters.json" -Verbose
```

## <a name="2-assign-identity-to-the-node-type-resource"></a>2. 将标识分配给节点类型资源

### <a name="add-managed-identity-properties-to-node-type-definition"></a>将托管标识属性添加到节点类型定义

最后，使用第一步创建的标识的完整资源 ID 将 `vmManagedIdentity` 和 `userAssignedIdentities` 属性添加到托管群集的节点类型定义。 请务必使用 2021-05-01 或更高版本的 `apiVersion`。

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-05-01",
    ...
    "properties": {
        "isPrimary" : true,
        "vmInstanceCount": 5,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2_v2",
        "vmImagePublisher" : "MicrosoftWindowsServer",
        "vmImageOffer" : "WindowsServer",
        "vmImageSku" : "2019-Datacenter",
        "vmImageVersion" : "latest",
        "vmManagedIdentity": {
            "userAssignedIdentities": [
                "[parameters('userAssignedIdentityResourceId')]"
            ]
        }
    }
}
```

### <a name="deploy-the-node-type-resource-assigning-the-identity"></a>部署分配标识的节点类型资源

运行 New-AzResourceGroupDeployment cmdlet 部署 Service Fabric 托管群集模板，该模板将托管标识分配给节点类型资源。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <sfmcRGName> -TemplateFile ".\SfmcVmMangedIdentity.json" -TemplateParameterFile ".\SfmcVmMangedIdentity.Parameters.json" -Verbose
```

部署后，创建的托管标识已添加到指定节点类型的虚拟机规模集，并且可以按预期使用，就像在任何非托管群集中一样。

## <a name="troubleshooting"></a>故障排除

部署时，如果无法正确添加角色分配，将遇到以下错误：

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Azure 门户部署错误，显示具有 SFRP 的对象/应用程序 ID 的客户端无权执行标识管理活动":::

在这种情况下，请确保使用“托管标识操作员”角色成功创建角色分配。 可以在受托管标识资源的 Azure 门户的托管标识资源上找到角色分配，如下所示。

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-portal.png" alt-text="Azure 门户中显示的用户分配的托管标识上的 Service Fabric 资源提供程序的角色分配属性":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [将应用部署到 Service Fabric 托管群集](./tutorial-managed-cluster-deploy-app.md)
