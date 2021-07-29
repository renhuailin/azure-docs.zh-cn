---
title: 适用于 Azure 虚拟桌面的 PowerShell 模块 - Azure
description: 如何安装和设置适用于 Azure 虚拟桌面的 PowerShell 模块。
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 029034cd4c4e118cca532509208f08843ca8b7fd
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753118"
---
# <a name="set-up-the-powershell-module-for-azure-virtual-desktop"></a>设置适用于 Azure 虚拟桌面的 PowerShell 模块

>[!IMPORTANT]
>本内容适用于包含 Azure 资源管理器集成的 Azure 虚拟桌面。

Azure 虚拟桌面 PowerShell 模块集成到 Azure PowerShell 模块中。 本文介绍如何设置 PowerShell 模块，以便针对 Azure 虚拟桌面运行 cmdlet。

## <a name="set-up-your-powershell-environment"></a>设置 PowerShell 环境

若要开始使用该模块，请先安装[最新版本的 PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core)。 Azure 虚拟桌面 cmdlet 目前只能与 PowerShell Core 配合使用。

接下来，需要安装要在 PowerShell 会话中使用的 DesktopVirtualization 模块。

在提升模式下运行以下 PowerShell cmdlet 以安装该模块：

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> 如果此 cmdlet 无法正常工作，请尝试使用提升的权限重新运行它。

接下来，运行以下 cmdlet 以连接到 Azure：

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>如果正在连接到 US Gov 门户，请改为运行以下 cmdlet：
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

登录到 Azure 帐户的操作需要在运行 Connect cmdlet 时生成的代码。 若要登录，请访问 <https://microsoft.com/devicelogin>，输入代码，然后使用 Azure 管理员凭据登录。

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

这会直接将你登录到与管理员凭据对应的默认订阅。

## <a name="change-the-default-subscription"></a>更改默认订阅

若要在登录后更改默认订阅，请运行以下 cmdlet：

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

还可以使用 Out-GridView cmdlet 从列表中选择一个订阅：

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

选择要使用的新订阅时，无需在随后运行的 cmdlet 中指定该订阅的 ID。 例如，以下 cmdlet 检索特定的会话主机，无需订阅 ID：

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

还可以根据 cmdlet 更改订阅，只需以参数形式添加所需的订阅名称即可。 下一个 cmdlet 与上一个示例相同，只是添加了订阅 ID 作为参数来更改 cmdlet 使用的订阅。

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>获取位置

对于创建新对象的所有 New-AzWVD cmdlet，location 参数是必需的。

运行以下 cmdlet 以获取订阅支持的位置的列表：

```powershell
Get-AzLocation
```

Get-AzLocation 的输出将如下所示：

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

知道帐户的位置后，即可在 cmdlet 中使用它。 例如，下面是一个在“southeastasia”位置创建主机池的 cmdlet：

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>后续步骤

设置 PowerShell 模块后，可以通过运行 cmdlet 来执行 Azure 虚拟桌面中的各种操作。 下面是一些可以使用模块的位置：

- 浏览我们的 [Azure 虚拟桌面教程]()，以设置你自己的 Azure 虚拟桌面环境。
- [使用 PowerShell 创建主机池](create-host-pools-powershell.md)
- [配置 Azure 虚拟桌面负载均衡方法](configure-host-pool-load-balancing.md)
- [配置个人桌面主机池分配类型](configure-host-pool-personal-desktop-assignment-type.md)
- 还有更多其他功能！

如果遇到任何问题，请查阅我们的 [PowerShell 故障排除文章](troubleshoot-powershell.md)以获取帮助。

