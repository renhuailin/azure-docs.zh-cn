---
title: 为 Azure 虚拟桌面用户自定义源 - Azure
description: 如何使用 PowerShell cmdlet 为 Azure 虚拟桌面用户自定义源。
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: d2a1e8df23bca2e57f2d26bd83fc06ad93847903
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113032932"
---
# <a name="customize-the-feed-for-azure-virtual-desktop-users"></a>为 Azure 虚拟桌面用户自定义源

>[!IMPORTANT]
>本教程的内容适用于包含 Azure 资源管理器 Azure 虚拟桌面对象的 Azure 虚拟桌面。 如果你使用的是不包含 Azure 资源管理器对象的 Azure 虚拟桌面（经典），请参阅[本文](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md)。

可以自定义源，以便 RemoteApp 和远程桌面资源以可识别的方式向用户显示。

## <a name="prerequisites"></a>先决条件

本文假设你已下载并安装 Azure 虚拟桌面 PowerShell 模块。 如果尚未安装，请按照[安装 PowerShell 模块](powershell-module.md)中的说明进行操作。

## <a name="customize-the-display-name-for-a-remoteapp"></a>自定义 RemoteApp 的显示名称

可以通过设置易记名称来更改已发布的 RemoteApp 的显示名称。 在默认情况下，易记名称与 RemoteApp 程序的名称相同。

若要检索某个应用组的已发布 RemoteApps 的列表，请运行以下 PowerShell cmdlet：

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

若要为 RemoteApp 分配易记名称，请使用所需参数运行以下 cmdlet：

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

例如，假定已使用以下示例 cmdlet 检索了当前应用程序：

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

输出会如下所示：

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
若要更新易记名称，请运行此 cmdlet：

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

若要确认你已成功更新易记名称，请运行此 cmdlet：

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

该 cmdlet 应该会生成以下输出：

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>自定义远程桌面的显示名称

可以通过设置易记名称来更改已发布的远程桌面的显示名称。 如果通过 PowerShell 手动创建了主机池和桌面应用组，则默认易记名称为“会话桌面”。 如果通过 GitHub Azure 资源管理器模板或 Azure 市场产品/服务创建了主机池和桌面应用组，则默认易记名称与主机池名称相同。

若要检索远程桌面资源，请运行以下 PowerShell cmdlet：

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

若要为远程桌面资源分配易记名称，请运行以下 PowerShell cmdlet：

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>在 Azure 门户中自定义显示名称

可以通过使用 Azure 门户设置易记名称来更改已发布的远程桌面的显示名称。

1. 通过 <https://portal.azure.com> 登录到 Azure 门户。

2. 搜索“Azure 虚拟桌面”。

3. 在“服务”下，选择“Azure 虚拟桌面”。

4. 在“Azure 虚拟桌面”页上，选择屏幕左侧的“应用程序组”，然后选择要编辑的应用组的名称。 （例如，如果要编辑桌面应用组的显示名称，请选择名为“桌面”的应用组。）

5. 在屏幕左侧的菜单中选择“应用程序”。

6. 选择要更新的应用程序，然后输入新的“显示名称”。

7. 选择“保存”。 你编辑的应用程序现在应该会显示更新后的名称。

## <a name="next-steps"></a>后续步骤

你已经为用户自定义源，现在可以登录到 Azure 虚拟桌面客户端进行测试。为此，请继续阅读“连接到 Azure 虚拟桌面操作指南”：

 * [使用 Windows 10 或 Windows 7 进行连接](./user-documentation/connect-windows-7-10.md)
 * [使用 Web 客户端进行连接](./user-documentation/connect-web.md)
 * [使用 Android 客户端进行连接](./user-documentation/connect-android.md)
 * [使用 iOS 客户端进行连接](./user-documentation/connect-ios.md)
 * [使用 macOS 客户端进行连接](./user-documentation/connect-macos.md)
