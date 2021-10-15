---
title: 如何使用排出模式隔离会话主机 - Azure 虚拟桌面
description: 如何使用排出模式隔离会话主机以便在 Azure 虚拟桌面中执行维护。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: 4bacff4ecef6762a4e08760c463531392a71245f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352013"
---
# <a name="use-drain-mode-to-isolate-session-hosts-and-apply-patches"></a>使用排出模式隔离会话主机并应用修补程序

如果要应用补丁并进行维护而不中断用户会话，排出模式可隔离会话主机。 隔离时，会话主机不会接受新的用户会话。 任何新连接都会重定向到下一个可用的会话主机。 在用户注销或管理员结束会话之前，会话主机中的现有连接会保持工作。 会话主机处于排出模式时，管理员还可以远程连接到服务器，而无需通过 Azure 虚拟桌面服务。 可以将此设置应用于共用和个人桌面。

## <a name="set-drain-mode-using-the-azure-portal"></a>使用 Azure 门户设置排出模式

若要在 Azure 门户中启用排出模式，请执行以下操作：

1. 打开 Azure 门户，转到要隔离的主机池。

2. 在导航菜单中，选择“会话主机”。

3. 接下来，选择要为其启用排出模式的主机，然后选择“启用排出模式”。

4. 若要禁用排出模式，请选择已启用排出模式的主机池，然后选择“禁用排出模式”。

## <a name="set-drain-mode-using-powershell"></a>使用 PowerShell 设置排出模式

可以在 PowerShell 中使用 AllowNewSessions 参数设置排出模式，该参数是 [Update-AzWvdSessionhost](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true) 命令的一部分。

运行此 cmdlet 以启用排出模式：

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

运行此 cmdlet 以禁用排出模式：

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>需要为要应用设置的每个会话主机都运行此命令。

## <a name="next-steps"></a>后续步骤

如果要了解有关适用于 Azure 虚拟桌面的 Azure 门户的详细信息，请查看[我们的教程](create-host-pools-azure-marketplace.md)。 如果已熟悉基本知识，请查看可通过 Azure 门户使用的其他一些功能，例如 [MSIX 应用附加](app-attach-azure-portal.md)和 [Azure 顾问](azure-advisor.md)。

如果使用 PowerShell 方法，并且要了解模块可以执行的其他操作，请查看[设置适用于 Azure 虚拟桌面的 PowerShell 模块](powershell-module.md)和 [PowerShell 参考](/powershell/module/az.desktopvirtualization/)。
