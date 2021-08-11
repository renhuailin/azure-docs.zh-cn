---
title: 使用模板在 Azure VM 上配置托管标识 - Azure AD
description: 分步说明如何使用 Azure 资源管理器模板在 Azure VM 上配置 Azure 资源的托管标识。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1412b0ff7703d5bcc9950c80d5ab59b4c33cc7ae
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113732217"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>使用模板在 Azure VM 上配置 Azure 资源的托管标识

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。

本文将介绍如何在 Azure VM 上使用 Azure 资源管理器部署模板来执行以下 Azure 资源的托管标识操作：

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 管理器部署模板，请查看[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager 模板

与 Azure 门户和脚本一样，[Azure 资源管理器](../../azure-resource-manager/management/overview.md)模板支持部署由 Azure 资源组定义的新资源或修改后的资源。 有多种可用于执行模板编辑和部署的方法（包括本地方法和基于门户的方法），包括：

   - 使用 [Azure 市场中的自定义模板](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)，这样可以从头开始创建模板，也可以在现有常见模板或[快速启动模板](https://azure.microsoft.com/resources/templates/)的基础之上操作。
   - 派生自现有资源组，具体方法是从[原始部署](../../azure-resource-manager/templates/export-template-portal.md)或[当前部署](../../azure-resource-manager/templates/export-template-portal.md)导出模板。
   - 使用本地 [JSON 编辑器（例如 VS Code）](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)，然后使用 PowerShell 或 CLI 进行上传和部署。
   - 使用 Visual Studio [Azure 资源组项目](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)同时创建和部署模板。

无论选择哪种方法，在初始部署和重新部署期间，模板语法都是相同的。 在新的或现有 VM 上启用系统或用户分配的托管标识所采用的方式是相同的。 此外，默认情况下，Azure 资源管理器还会对部署执行[增量更新](../../azure-resource-manager/templates/deployment-modes.md)。

## <a name="system-assigned-managed-identity"></a>系统分配的托管标识

在此部分中，将使用 Azure 资源管理器模板启用和禁用系统分配的托管标识。

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>在创建 Azure VM 的过程中或在现有 VM 上启用系统分配的托管标识

若要在 VM 上启用系统分配的托管标识，你的帐户需要[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。

2. 若要启用系统分配的托管标识，请将模板加载到编辑器中，在 `resources` 节中找到所关注的 `Microsoft.Compute/virtualMachines` 资源，并在与 `"type": "Microsoft.Compute/virtualMachines"` 属性相同的级别添加 `"identity"` 属性。 使用以下语法：

   ```json
   "identity": {
       "type": "SystemAssigned"
   },
   ```

3. 完成后，以下各节应当会添加到模板的 `resource` 节，该节应当呈现如下：

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>向 VM 的系统分配的托管标识分配一个角色

在 VM 上启用系统分配的托管标识后，建议向其授予一个角色，例如对创建它的资源组的“读取者”访问权限。 可以在[使用 Azure 资源管理器模板分配 Azure 角色](../../role-based-access-control/role-assignments-template.md)一文中找到有助于完成此步骤的详细信息。


### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中禁用系统分配的托管标识

若要从 VM 中删除系统分配的托管标识，你的帐户需要[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色分配。  无需其他 Azure AD 目录角色分配。

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。

2. 将模板加载到[编辑器](#azure-resource-manager-templates)，并在 `resources` 部分找到相关的 `Microsoft.Compute/virtualMachines` 资源。 如果 VM 只有系统分配的托管标识，则可以将标识类型更改为 `None` 来禁用它。

   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**

   如果 VM 同时具有系统和用户分配的托管标识，请从标识类型中删除 `SystemAssigned` 并保留 `UserAssigned` 以及 `userAssignedIdentities` 字典值。

   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**

   如果 `apiVersion` 为 `2017-12-01` 并且 VM 同时具有系统和用户分配的托管标识，请从标识类型中删除 `SystemAssigned` 并保留 `UserAssigned` 以及用户分配的托管标识的 `identityIds` 数组。

以下示例演示如何从没有用户分配的托管标识的 VM 删除系统分配的托管标识：

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('vmName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "None"
    }
}
```

## <a name="user-assigned-managed-identity"></a>用户分配的托管标识

在此部分中，将使用 Azure 资源管理器模板向 Azure VM 分配用户分配的托管标识。

> [!NOTE]
> 要使用 Azure 资源管理器模板创建用户分配托管标识，请参阅[创建用户分配托管标识](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)。

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>向 Azure VM 分配用户分配的托管标识

若要将用户分配的标识分配给 VM，你的帐户需要[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)和[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)角色分配。 无需其他 Azure AD 目录角色分配。

1. 在 `resources` 元素下添加以下条目，以向 VM 分配用户分配的托管标识。  请务必将 `<USERASSIGNEDIDENTITY>` 替换为你创建的用户分配的托管标识的名称。

   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**

   如果 `apiVersion` 为 `2018-06-01`，则用户分配的托管标识以 `userAssignedIdentities` 字典格式存储，并且 `<USERASSIGNEDIDENTITYNAME>` 值必须存储在模板的 `variables` 节中定义的某个变量中。

   ```json
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft.Compute/virtualMachines API 版本 2017-12-01**

   如果 `apiVersion` 为 `2017-12-01`，则用户分配的托管标识存储在 `identityIds` 数组中，并且 `<USERASSIGNEDIDENTITYNAME>` 值必须存储在模板的 `variables` 节中定义的某个变量中。

   ```json
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. 完成后，以下各节应当会添加到模板的 `resource` 节，该节应当呈现如下：

   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**

   ```json
     "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        }
    ] 
   ```

   **Microsoft.Compute/virtualMachines API 版本 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        }
   ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>从 Azure VM 中删除用户分配的托管标识

若要从 VM 中删除用户分配的标识，你的帐户需要[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)角色分配。 无需其他 Azure AD 目录角色分配。

1. 无论是在本地登录到 Azure 还是通过 Azure 门户登录，请使用与包含 VM 的 Azure 订阅关联的帐户。

2. 将模板加载到[编辑器](#azure-resource-manager-templates)，并在 `resources` 部分找到相关的 `Microsoft.Compute/virtualMachines` 资源。 如果 VM 只有用户分配的托管标识，则可以通过将标识类型更改为 `None` 来禁用它。

   以下示例演示如何从没有系统分配的托管标识的 VM 中删除所有用户分配的托管标识：

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft.Compute/virtualMachines API 版本 2018-06-01**

   若要从 VM 中删除单个用户分配的托管标识，请将其从 `useraAssignedIdentities` 字典中删除。

   如果具有系统分配的托管标识，请将其保持在 `identity` 值下的 `type` 值中。

   **Microsoft.Compute/virtualMachines API 版本 2017-12-01**

   若要从 VM 中删除单个用户分配的托管标识，请将其从 `identityIds` 数组中删除。

   如果具有系统分配的托管标识，请将其保持在 `identity` 值下的 `type` 值中。

## <a name="next-steps"></a>后续步骤

- [Azure 资源概述的托管标识](overview.md)。
