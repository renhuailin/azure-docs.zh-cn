---
title: 设置实例和身份验证 (PowerShell)
titleSuffix: Azure Digital Twins
description: 了解如何使用 Azure PowerShell 设置 Azure 数字孪生服务实例
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1e2751c7df1cd3b43ce6c700bc51b51faf95963f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468684"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>设置 Azure 数字孪生实例和身份验证 (PowerShell)。

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文将介绍新 Azure 数字孪生实例的设置步骤，包括创建实例和设置身份验证。 完成本文操作后，可准备对 Azure 数字孪生实例开始编程。

本文此版本借助 [Azure PowerShell](/powershell/azure/new-azureps-module-az) 逐个手动完成这些步骤。

* 若要使用 Azure 门户手动完成这些步骤，请参阅本文的门户版本：[设置实例和身份验证（门户）](how-to-set-up-instance-portal.md)。
* 若要使用部署脚本示例完成自动设置，请参阅本文的脚本编写版本：[设置实例和身份验证（已编写脚本）](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="prepare-your-environment"></a>准备环境

1. 首先，选择运行本文命令的位置。 可以选择使用 Azure PowerShell 的本地安装或在浏览器窗口中使用 [Azure Cloud Shell](https://shell.azure.com) 运行 Azure PowerShell 命令。
    * 如果选择在本地使用 Azure PowerShell：
       1. [安装 Az PowerShell 模块](/powershell/azure/install-az-ps)。
       1. 在计算机上打开 PowerShell 窗口。
       1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。
    * 如果选择使用 Azure Cloud Shell：
        1. 请参阅 [Azure Cloud Shell 概述](../cloud-shell/overview.md)了解有关 Cloud Shell 的详细信息。
        1. [在浏览器中打开一个 Cloud Shell 窗口](https://shell.azure.com)。
        1. 在 Cloud Shell 图标栏中，确保已将 Cloud Shell 设置为运行 PowerShell 版本。
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Azure 门户中 Cloud Shell 窗口的屏幕截图，其中显示选择 PowerShell 版本。":::
    
1. 如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. 如果首次将 Azure 数字孪生与此订阅一起使用，则必须注册 Microsoft.DigitalTwins 资源提供程序。 （如果不确定，可以再次运行该命令，即使过去某个时候操作过也是如此。）

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. 使用以下命令安装 Az.DigitalTwins PowerShell 模块。
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> 尽管 Az.DigitalTwins PowerShell 模块为预览版，但必须使用上述 `Install-Module` cmdlet 单独安装它。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中默认提供。

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

在本部分中，将使用 Azure PowerShell 创建新的 Azure 数字孪生实例。
你需要提供：

* 将在其中部署实例的 [Azure 资源组](../azure-resource-manager/management/overview.md)。 如果没有现有的资源组，可以使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建一个。

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* 部署区域。 若要查看哪些区域支持 Azure 数字孪生，请访问各区域的 Azure 产品可用性。
* 实例名。 新实例的名称在订阅所在区域内必须是唯一的。 如果订阅在区域中已存在使用指定名称的其他 Azure 数字孪生实例，则系统将要求选择其他名称。

在以下命令中使用值创建实例：

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>验证是否成功并收集重要值

如果成功创建实例，结果将类似于以下输出，其中包含有关所创建资源的信息：

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

接下来，运行 `Get-AzDigitalTwinsInstance` 并通过管道传递到 `Select-Object -Property *` 以显示新实例的属性，如下所示：

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> 可以使用此命令随时查看实例的所有属性。

记下 Azure 数字孪生实例的主机名、名称和资源组  。 在继续使用 Azure 数字孪生实例、设置身份验证和使用相关 Azure 资源时，可能需要使用这些重要的值。 如果其他用户将对该实例进行编程，则应与他们共享这些值。

现已准备好 Azure 数字孪生实例。 接下来，你将向适当的 Azure 用户授予权限来管理该实例。

## <a name="set-up-user-access-permissions"></a>设置用户访问权限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

### <a name="prerequisites-permission-requirements"></a>先决条件：权限要求
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role"></a>分配角色

若要授予用户管理 Azure 数字孪生实例的权限，必须在实例中为他们分配“Azure 数字孪生数据所有者”角色。

首先，确定应为其分配角色的用户的 Azure AD 帐户 ObjectId。 可以使用 [Get-AzAdUser](/powershell/module/az.resources/get-azaduser) cmdlet 查找此值，方法是传入 Azure AD 帐户中的用户主体名，以检索其 ObjectId（以及用户其他信息）。 在大多数情况下，用户主体名与 Azure AD 帐户中的用户电子邮件匹配。

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

接下来，在以下命令中使用 ObjectId 来分配角色。 此外，此命令还要求输入在之前创建实例时所选的相同订阅 ID、资源组名和 Azure 数字孪生实例名。 此命令必须由在 Azure 订阅中具有[足够权限](#prerequisites-permission-requirements)的用户运行。

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-ID-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

此命令的输出结果是有关已创建的角色分配的信息。

### <a name="verify-success"></a>验证是否成功

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

现在，你已准备好 Azure 数字孪生实例，并分配了管理权限。

## <a name="next-steps"></a>后续步骤

请参阅如何使用验证码将客户端应用程序连接到实例：
* [编写应用身份验证码](how-to-authenticate-client.md)
