---
title: 为 Windows 虚拟桌面（经典）用户自定义源 - Azure
description: 如何使用 PowerShell cmdlet 为 Windows 虚拟桌面（经典）用户自定义源。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd7496690ec88fbe4297386c32d1b8a2c3234577
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "91540755"
---
# <a name="customize-feed-for-windows-virtual-desktop-classic-users"></a>为 Windows 虚拟桌面（经典）用户自定义源

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../customize-feed-for-virtual-desktop-users.md)。

可以自定义源，以便 RemoteApp 和远程桌面资源以可识别的方式向用户显示。

首先[下载并导入 Windows 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），以便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>自定义 RemoteApp 的显示名称

可以通过设置易记名称来更改已发布的 RemoteApp 的显示名称。 在默认情况下，易记名称与 RemoteApp 程序的名称相同。

若要检索某个应用组的已发布 RemoteApps 的列表，请运行以下 PowerShell cmdlet：

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![PowerShell cmdlet Get-RDSRemoteApp 的屏幕截图，其中突出显示了“Name”和“FriendlyName”（用于自定义显示名称）。](../media/get-rdsremoteapp.png)

若要为 RemoteApp 分配易记名称，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![PowerShell cmdlet Set-RDSRemoteApp 的屏幕截图，其中突出显示了“Name”和“New FriendlyName”（用于自定义显示名称）。](../media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自定义远程桌面的显示名称

可以通过设置易记名称来更改已发布的远程桌面的显示名称。 如果通过 PowerShell 手动创建了主机池和桌面应用组，则默认易记名称为“会话桌面”。 如果通过 GitHub Azure 资源管理器模板或 Azure 市场产品/服务创建了主机池和桌面应用组，则默认易记名称与主机池名称相同。

若要检索远程桌面资源，请运行以下 PowerShell cmdlet：

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```

> [!div class="mx-imgBorder"]
> ![PowerShell cmdlet Get-RDSRemoteApp 的屏幕截图，其中突出显示了“Name”和“FriendlyName”。](../media/get-rdsremotedesktop.png)

若要为远程桌面资源分配易记名称，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```

> [!div class="mx-imgBorder"]
> ![PowerShell cmdlet Set-RDSRemoteApp 的屏幕截图，其中突出显示了“Name”和“New FriendlyName”。](../media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>后续步骤

你现在已经为用户自定义了源，可以登录到 Windows 虚拟桌面客户端对该源进行测试了。为此，请继续了解连接到 Windows 虚拟桌面的操作方法：

 * [从 Windows 10 或 Windows 7 进行连接](connect-windows-7-10-2019.md)
 * [从 Web 浏览器进行连接](connect-web-2019.md)
