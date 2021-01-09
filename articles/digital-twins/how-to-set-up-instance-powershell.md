---
title: " (PowerShell 中设置实例和身份验证) "
titleSuffix: Azure Digital Twins
description: 请参阅如何使用 Azure PowerShell 设置 Azure 数字孪生服务的实例
author: baanders
ms.author: baanders
ms.date: 12/16/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 024b238ef9a6330831ae6cf4dcd6bb72d72dcc74
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044265"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-powershell"></a>设置 Azure 数字孪生实例，并 (PowerShell 进行身份验证) 

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

本文介绍 **设置新的 Azure 数字孪生实例** 的步骤，包括创建实例和设置身份验证。 完成本文后，你将拥有一个可开始对进行编程的 Azure 数字孪生实例。

本文的此版本通过使用 [Azure PowerShell](/powershell/azure/new-azureps-module-az)逐个手动完成这些步骤。

* 若要使用 Azure 门户手动完成这些步骤，请参阅本文的门户版本： [*如何：设置实例和身份验证 (门户)*](how-to-set-up-instance-portal.md)。
* 若要通过使用部署脚本示例的自动安装运行，请参阅本文的脚本编写版本： how [*to： Set a instance and authentication (script)*](how-to-set-up-instance-scripted.md)。

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prepare-your-environment"></a>准备环境

1. 首先，选择要在其中运行此项目中命令的位置。 你可以选择使用 Azure PowerShell 的本地安装或使用 [Azure Cloud Shell](https://shell.azure.com)在浏览器窗口中运行 Azure PowerShell 命令。
    * 如果选择在本地使用 Azure PowerShell：
       1. [安装 Az PowerShell 模块](/powershell/azure/install-az-ps)。
       1. 在计算机上打开 PowerShell 窗口。
       1. 使用 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 连接到 Azure 帐户。
    * 如果选择使用 Azure Cloud Shell：
        1. 有关 Cloud Shell 的详细信息，请参阅 [Azure Cloud Shell 的概述](../cloud-shell/overview.md) 。
        1. 通过浏览器中的以下 [链接](https://shell.azure.com) 打开 Cloud Shell 窗口。
        1. 在 Cloud Shell 图标栏中，确保 Cloud Shell 设置为运行 PowerShell 版本。
    
            :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="显示 PowerShell 版本选择的 Cloud Shell 窗口":::
    
1. 如果有多个 Azure 订阅，请选择应当计费的资源所在的相应订阅。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet 选择特定订阅。

   ```azurepowershell-interactive
   Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
   ```

1. 如果这是你第一次将 Azure 数字孪生与此订阅一起使用，则必须注册 **DigitalTwins** 资源提供程序。 （如果不确定，可以再次运行该命令，即使过去某个时候操作过也是如此。）

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.DigitalTwins
   ```

1. 使用以下命令安装 **DigitalTwins** PowerShell 模块。
    ```azurepowershell-interactive
    Install-Module -Name Az.DigitalTwins
    ```

> [!IMPORTANT]
> 如果 **DigitalTwins** PowerShell 模块为预览版，则必须按如上所述使用 cmdlet 单独安装它 `Install-Module` 。 此 PowerShell 模块正式发布后，它会包含在将来的 Az PowerShell 模块发行版中，并在 Azure Cloud Shell 中默认提供。

## <a name="create-the-azure-digital-twins-instance"></a>创建 Azure 数字孪生实例

在本部分中，将使用 Azure PowerShell **创建 Azure 数字孪生的新实例** 。
需要提供：

* 将在其中部署实例的 [Azure 资源组](../azure-resource-manager/management/overview.md) 。 如果还没有现有资源组，可以使用 [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet 创建一个资源组：

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <name-for-your-resource-group> -Location <region>
  ```

* 部署的区域。 若要查看哪些区域支持 Azure 数字孪生，请访问 [*按区域提供的 azure 产品*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)。
* 实例的名称。 新实例的名称在订阅的区域内必须是唯一的。 如果你的订阅在已使用指定名称的区域中有另一个 Azure 数字孪生实例，则系统将要求你选择其他名称。

在以下命令中使用值创建实例：

```azurepowershell-interactive
New-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> -Location <region>
```

### <a name="verify-success-and-collect-important-values"></a>验证成功并收集重要值

如果成功创建了实例，结果将类似于以下输出，其中包含有关所创建资源的信息：

```Output
Location   Name                                         Type
--------   ----                                         ----
<region>   <name-for-your-Azure-Digital-Twins-instance> Microsoft.DigitalTwins/digitalTwinsInstances
```

接下来，通过运行并通过管道传递到来显示新实例的属性 `Get-AzDigitalTwinsInstance` `Select-Object -Property *` ，如下所示：

```azurepowershell-interactive
Get-AzDigitalTwinsInstance -ResourceGroupName <your-resource-group> -ResourceName <name-for-your-Azure-Digital-Twins-instance> |
  Select-Object -Property *
```

> [!TIP]
> 你随时都可以使用此命令查看实例的所有属性。

请注意 Azure 数字孪生实例的 **主机名**、 **名称** 和 **资源** 组。 当你继续使用 Azure 数字孪生实例、设置身份验证和相关的 Azure 资源时，可能需要这些值。 如果其他用户将对该实例进行编程，则应该与它们共享这些值。

现已准备好使用 Azure 数字孪生实例。 接下来，你将为适当的 Azure 用户授予管理权限。

## <a name="set-up-user-access-permissions"></a>设置用户访问权限

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

首先，确定应该为其分配角色的用户的 Azure AD 帐户的 **ObjectId** 。 你可以使用 [AzAdUser](/powershell/module/az.resources/get-azaduser) cmdlet 找到此值，方法是传入 Azure AD 帐户上的用户主体名称，以检索其 ObjectId (以及) 的其他用户信息。 在大多数情况下，用户主体名称将与 Azure AD 帐户上的用户电子邮件匹配。

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName <Azure-AD-user-principal-name-of-user-to-assign>
```

接下来，使用以下命令中的 **ObjectId** 分配角色。 命令还要求你输入在创建实例时之前选择的相同订阅 ID、资源组名称和 Azure 数字孪生实例名称。 此命令必须由在 Azure 订阅中具有 [足够权限](#prerequisites-permission-requirements) 的用户运行。

```azurepowershell-interactive
$Params = @{
  ObjectId = '<Azure-AD-user-object-id-of-user-to-assign>'
  RoleDefinitionName = 'Azure Digital Twins Data Owner'
  Scope = '/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DigitalTwins/digitalTwinsInstances/<name-for-your-Azure-Digital-Twins-instance>'
}
New-AzRoleAssignment @Params
```

此命令的结果将输出有关已创建的角色分配的信息。

### <a name="verify-success"></a>验证是否成功

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

现在，你已准备好使用 Azure 数字孪生实例，并已分配了管理它的权限。

## <a name="next-steps"></a>后续步骤

请参阅如何使用身份验证代码将客户端应用程序连接到实例：
* [*操作说明：编写应用身份验证代码*](how-to-authenticate-client.md)
