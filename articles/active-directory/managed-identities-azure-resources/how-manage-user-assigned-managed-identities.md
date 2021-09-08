---
title: 管理用户分配的托管标识 - Azure AD
description: 创建用户分配的托管标识。
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: barclayn
zone_pivot_groups: identity-mi-methods
ms.openlocfilehash: 83f47dbbdd0968a845871b106be9142fd67f9967
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "122652427"
---
# <a name="manage-user-assigned-managed-identities"></a>管理用户分配的托管标识



Azure 资源的托管标识无需在代码中管理凭据。 当访问支持 Azure AD 身份验证的资源时，可以使用它们来获取你的应用程序可使用的 Azure Active Directory (Azure AD) 令牌。 Azure 管理标识，因此用户不必管理。

有两种类型的托管标识：系统分配和用户分配的托管标识。 它们之间的主要区别在于，系统分配的托管标识将生命周期链接到使用它们的资源。 而用户分配的托管标识可用于多个资源。 若要详细了解托管标识，请参阅[什么是 Azure 资源的托管标识？](overview.md)。

::: zone pivot="identity-mi-methods-azp"
本文介绍如何使用 Azure 门户创建、列出和删除用户分配的托管标识以及如何为其分配角色。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。


## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

1. 使用与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)，以创建用户分配的托管标识。
1. 在搜索框中输入“托管标识”。 在“服务”下选择“托管标识” 。
1. 选择“添加”，然后在“创建用户分配的托管标识”窗格中的以下框中输入值 ：
    - **订阅**：选择要在其下创建用户分配的托管标识的订阅。
    - **资源组**：选择要在其中创建用户分配的托管标识的资源组，或选择“新建”来创建新资源组。
    - 区域：选择用于部署用户分配的托管标识的区域，例如“美国西部”。
    - **名称**：这是用户分配的托管标识的名称，例如 UAI1。
  
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]
  
   ![显示“创建用户分配的托管标识”窗格的屏幕截图。](media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
1. 选择“查看 + 创建”以查看更改。
1. 选择“创建” 。

## <a name="list-user-assigned-managed-identities"></a>列出用户分配的托管标识

若要列出或读取用户分配的托管标识，你的帐户需要[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)或[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

1. 使用与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)，以列出用户分配的托管标识。
1. 在搜索框中输入“托管标识”。 在“服务”下选择“托管标识” 。
1. 这会返回一个列表，其中包含订阅中的用户分配的托管标识。 要查看用户分配的托管标识的详细信息，请选择其名称。

   ![显示用户分配的托管标识列表的屏幕截图。](media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>删除用户分配的托管标识

若要删除用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

删除用户分配的标识不会将其从 VM 或其他已分配给的资源中删除。 要从 VM 中删除用户分配的标识，请参阅[从 VM 中删除用户分配的托管标识](qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm)。

1. 使用与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)，以删除用户分配的托管标识。
1. 选择用户分配的托管标识，然后选择“删除”。
1. 在确认框下，选择“是”。

   ![显示“删除”用户分配的托管标识的屏幕截图。](media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>为用户分配的托管标识分配角色 

若要为用户分配的托管标识分配角色，你的帐户需要[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色分配。

1. 使用与 Azure 订阅关联的帐户登录 [Azure 门户](https://portal.azure.com)，以列出用户分配的托管标识。
1. 在搜索框中输入“托管标识”。 在“服务”下选择“托管标识” 。
1. 这会返回一个列表，其中包含订阅中的用户分配的托管标识。 选择要为其分配角色的用户分配托管标识。
1. 依次选择“访问控制(IAM)”、“添加角色分配” 

   ![显示用户分配的托管标识启动的屏幕截图。](media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

1. 在“添加角色分配”窗格中，配置以下值，然后选择“保存” ：
   - **角色**：要分配的角色。
   - **将访问权限分配到**：将用户分配的托管标识分配到的资源。
   - **选择**：要将访问权限分配到的成员。
   
   ![显示用户分配的托管标识 IAM 的屏幕截图。](media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)



::: zone-end



::: zone pivot="identity-mi-methods-azcli"

本文介绍如何使用 Azure CLI 创建、列出和删除用户分配的托管标识以及如何为其分配角色。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。


[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!IMPORTANT]  
> 若要在通过 CLI 使用应用服务主体时修改用户权限，必须在 Azure Active Directory Graph API 中为服务主体提供更多权限，因为 CLI 的某些部分针对图形 API 执行 GET 请求。 否则，你可能最终会收到“权限不足，无法完成操作”消息。 若要执行此步骤，请进入 Azure AD 中的“应用注册”，依次选择你的应用、“API 权限”，然后向下滚动并选择“Azure Active Directory Graph”  。 从那里选择“应用程序权限”，然后添加适当的权限。 

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识 

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

使用 [az identity create](/cli/azure/identity#az_identity_create) 命令创建用户分配的托管标识。 `-g` 参数指定创建了用户分配的托管标识的资源组。 `-n` 参数指定其名称。 将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>列出用户分配的托管标识

若要列出或读取用户分配的托管标识，你的帐户需要[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)或[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

若要列出用户分配的托管标识，请使用 [az identity list](/cli/azure/identity#az_identity_list) 命令。 将 `<RESOURCE GROUP>` 值替换为自己的值。

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

在 JSON 响应中，用户分配的托管标识为 `type` 键返回 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 值。

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>删除用户分配的托管标识

若要删除用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

若要删除用户分配的托管标识，请使用 [az identity delete](/cli/azure/identity#az_identity_delete) 命令。 -n 参数指定其名称。 -g 参数指定创建了用户分配托管标识的资源组。 将 `<USER ASSIGNED IDENTITY NAME>` 和 `<RESOURCE GROUP>` 参数值替换为自己的值。

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> 删除用户分配的托管标识不会从将其分配到的任何资源中删除引用。 使用 `az vm/vmss identity remove` 命令从 VM 或虚拟机规模集中删除这些标识。

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 标识命令的完整列表，请参阅 [az identity](/cli/azure/identity)。

有关如何向 Azure VM 分配用户分配的托管标识的信息，请参阅[使用 Azure CLI 在 Azure VM 上配置 Azure 资源的托管标识](qs-configure-cli-windows-vm.md#user-assigned-managed-identity)。


::: zone-end

::: zone pivot="identity-mi-methods-powershell"

本文介绍如何使用 PowerShell 创建、列出和删除用户分配的托管标识以及如何为其分配角色。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 若要运行示例脚本，有两个选项：
    - 使用 [Azure Cloud Shell](../../cloud-shell/overview.md)，你可使用代码块右上角的“试用”按钮打开它。
    - 如下一节所述，使用 Azure PowerShell 在本地运行脚本。

本文将介绍如何使用 PowerShell 创建、列出和删除用户分配的托管标识。

### <a name="configure-azure-powershell-locally"></a>在本地配置 Azure PowerShell

若要在本文中本地使用 Azure PowerShell 而不是使用 Cloud Shell，请执行以下操作：

1. 安装[最新版本的 Azure PowerShell](/powershell/azure/install-az-ps)（如果尚未安装）。

1. 登录 Azure。

    ```azurepowershell
    Connect-AzAccount
    ```

1. 安装[最新版本的 PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)。

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    在下一步运行此命令后，可能需要 `Exit` 退出当前 PowerShell 会话。

1. 安装 `Az.ManagedServiceIdentity` 模块的预发布版本，以执行本文中用户分配的托管标识操作。

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

若要创建用户分配的托管标识，请使用 `New-AzUserAssignedIdentity` 命令。 `ResourceGroupName` 参数指定创建了用户分配的托管标识的资源组。 `-Name` 参数指定其名称。 将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

## <a name="list-user-assigned-managed-identities"></a>列出用户分配的托管标识

若要列出或读取用户分配的托管标识，你的帐户需要[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)或[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

要列出用户分配的托管标识，请使用 [Get-AzUserAssigned] 命令。 `-ResourceGroupName` 参数指定创建了用户分配托管标识的资源组。 将 `<RESOURCE GROUP>` 值替换为自己的值。

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```

在响应中，用户分配的托管标识为 `Type` 键返回 `"Microsoft.ManagedIdentity/userAssignedIdentities"` 值。

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>删除用户分配的托管标识

若要删除用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

若要删除用户分配的托管标识，请使用 `Remove-AzUserAssignedIdentity` 命令。 `-ResourceGroupName` 参数指定从中已创建用户分配标识的资源组。 `-Name` 参数指定其名称。 将 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 参数值替换为自己的值。

```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

> [!NOTE]
> 删除用户分配的托管标识不会从将其分配到的任何资源中删除引用。 标识分配必须单独移除。

## <a name="next-steps"></a>后续步骤

有关 Azure 资源的 Azure PowerShell 托管标识命令的完整列表和详细信息，请参阅 [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)。


::: zone-end


::: zone pivot="identity-mi-methods-arm"

本文将使用 Azure 资源管理器创建用户分配的托管标识。

## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

不能使用资源管理器模板来列出和删除用户分配的托管标识。 请参阅以下文章来创建和列出用户分配的托管标识：

- [列出用户分配的托管标识](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [删除用户分配的托管标识](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)

## <a name="template-creation-and-editing"></a>模板创建和编辑

与 Azure 门户和脚本一样，资源管理器模板支持部署由 Azure 资源组定义的新资源或修改后的资源。 有多种可用于执行模板编辑和部署的方法（包括本地方法和基于门户的方法）。 方法：

- 使用 [Azure 市场中的自定义模板](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)从头开始创建模板，或在现有常见模板或[快速启动模板](https://azure.microsoft.com/resources/templates/)的基础之上操作。
- 派生自现有资源组，具体方法是从[原始部署](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)或[当前部署](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)导出模板。
- 使用本地 [JSON 编辑器（例如 VS Code）](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)，然后使用 PowerShell 或 Azure CLI 进行上传和部署。
- 使用 Visual Studio [Azure 资源组项目](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)创建和部署模板。 

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识 

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

若要创建用户分配的托管标识，请使用以下模板。 将 `<USER ASSIGNED IDENTITY NAME>` 值替换为自己的值。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>后续步骤

有关如何使用资源管理器模板向 Azure VM 分配用户分配的托管标识的信息，请参阅[使用模板在 Azure VM 上配置 Azure 资源的托管标识](qs-configure-template-windows-vm.md)。




::: zone-end


::: zone pivot="identity-mi-methods-rest"

本文将介绍如何使用 REST 创建、列出和删除用户分配的托管标识。


## <a name="prerequisites"></a>先决条件

- 如果不熟悉 Azure 资源的托管标识，请查阅[概述部分](overview.md)。 请务必了解[系统分配的托管标识与用户分配的托管标识之间的差异](overview.md#managed-identity-types)。
- 如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。
- 可在云中或在本地运行本文中的所有命令：
    - 若要在云中运行，请使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。
    - 若要在本地运行，请安装 [curl](https://curl.haxx.se/download.html) 和 [Azure CLI](/cli/azure/install-azure-cli)。


本文介绍如何使用 CURL 创建、列出和删除用户分配的托管标识以进行 REST API 调用。

## <a name="obtain-a-bearer-access-token"></a>获取持有者访问令牌

1. 如果在本地运行，请通过 Azure CLI 登录到 Azure。

    ```
    az login
    ```

1. 使用 [az account get-access-token](/cli/azure/account#az_account_get_access_token) 获取访问令牌。

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>创建用户分配的托管标识 

若要创建用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**请求标头**

|请求标头  |说明  |
|---------|---------|
|*Content-Type*     | 必需。 设置为 `application/json`。        |
|*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        |

**请求正文**

|名称  |说明  |
|---------|---------|
|位置     | 必需。 资源位置。        |

## <a name="list-user-assigned-managed-identities"></a>列出用户分配的托管标识

若要列出或读取用户分配的托管标识，你的帐户需要[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)或[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|请求标头  |说明  |
|---------|---------|
|*Content-Type*     | 必需。 设置为 `application/json`。        |
|*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        |

## <a name="delete-a-user-assigned-managed-identity"></a>删除用户分配的托管标识

若要删除用户分配的托管标识，你的帐户需要[托管标识参与者](../../role-based-access-control/built-in-roles.md#managed-identity-contributor)角色分配。

> [!NOTE]
> 删除用户分配的托管标识不会从将其分配到的任何资源中删除引用。 要使用 CURL 从 VM 中删除用户分配的托管标识，请参阅[从 Azure VM 中删除用户分配的标识](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm)。

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|请求标头  |说明  |
|---------|---------|
|*Content-Type*     | 必需。 设置为 `application/json`。        |
|*授权*     | 必需。 设置为有效的 `Bearer` 访问令牌。        |

## <a name="next-steps"></a>后续步骤

有关如何使用 CURL 向 Azure VM 或虚拟机规模集分配用户分配的托管标识的信息，请参阅：
- [使用 REST API 调用在 Azure VM 上配置 Azure 资源的托管标识](qs-configure-rest-vm.md#user-assigned-managed-identity) 
- [使用 REST API 调用在虚拟机规模集上配置 Azure 资源的托管标识](qs-configure-rest-vmss.md#user-assigned-managed-identity)

::: zone-end


