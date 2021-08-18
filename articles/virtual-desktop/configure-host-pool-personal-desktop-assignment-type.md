---
title: Azure 虚拟桌面个人桌面分配类型 - Azure
description: 如何为 Azure 虚拟桌面个人桌面主机池配置自动或直接分配。
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: e53bcbca2e40ef9a9587e0b571f2798132c2a829
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033580"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>配置个人桌面主机池分配类型

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[此文](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md)。

你可以配置个人桌面主机池的分配类型，以调整 Azure 虚拟桌面环境来更好地满足你的需求。 在本主题中，我们将介绍如何为用户配置自动分配或直接分配。

>[!NOTE]
> 本文中的说明仅适用于个人桌面主机池而不适用于共用主机池，因为不会将共用主机池中的用户分配到特定的会话主机。

## <a name="prerequisites"></a>先决条件

本文假设你已下载并安装 Azure 虚拟桌面 PowerShell 模块。 如果尚未安装，请按照[设置 PowerShell 模块](powershell-module.md)中的说明进行操作。

## <a name="configure-automatic-assignment"></a>配置自动分配

自动分配是 Azure 虚拟桌面环境中新创建的个人桌面主机池的默认分配类型。 自动分配用户不需要特定的会话主机。

若要自动分配用户，请先将其分配到个人桌面主机池，以便他们能够在其源中看到桌面。 当分配的用户在其源中启动桌面时，如果他们尚未连接到主机池，则他们将认领一个可用的会话主机，分配过程就此完成。

若要将主机池配置为自动将用户分配到 VM，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

若要将用户分配到个人桌面主机池，请运行以下 PowerShell cmdlet：

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>配置直接分配

与自动分配不同，使用直接分配时，必须将用户分配到个人桌面主机池和特定的会话主机，然后他们才能连接到其个人桌面。 如果仅将用户分配到主机池而不分配会话主机，则他们将无法访问资源。

若要将主机池配置为要求直接将用户分配到会话主机，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

若要将用户分配到个人桌面主机池，请运行以下 PowerShell cmdlet：

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

若要将用户分配到特定的会话主机，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

要直接将用户分配到 Azure 门户中的会话主机，请执行以下操作：

1. 通过 <https://portal.azure.com> 登录到 Azure 门户。
2. 在搜索栏中输入“Azure 虚拟桌面”。
3. 在“服务”下，选择“Azure 虚拟桌面” 。
4. 在“Azure 虚拟桌面”页上，转到窗口左侧的菜单，选择“主机池”。
5. 选择要更新的主机池的名称。
6. 接下来，请跳到窗口左侧的菜单，并选择“应用程序组”。
7. 选择要编辑的桌面应用组的名称，然后在窗口左侧的菜单中选择“分配”。
8. 选择“+ 添加”，然后选择要将此桌面应用组发布到的用户或用户组。
9. 选择信息栏中的“分配 VM”，从而为用户分配会话主机。
10. 选择要分配给用户的会话主机，然后选择“分配”。
11. 从可用用户列表中选择要将会话主机分配给的用户。
12. 完成后，选择“选择”。

## <a name="next-steps"></a>后续步骤

配置个人桌面分配类型后，可以登录到 Azure 虚拟桌面客户端，将其作为用户会话的一部分进行测试。 接下来的两篇操作指南将会介绍如何使用所选的客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](./user-documentation/connect-windows-7-10.md)
- [使用 Web 客户端进行连接](./user-documentation/connect-web.md)
- [使用 Android 客户端进行连接](./user-documentation/connect-android.md)
- [使用 iOS 客户端进行连接](./user-documentation/connect-ios.md)
- [使用 macOS 客户端进行连接](./user-documentation/connect-macos.md)
