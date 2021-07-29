---
title: 通过 PowerShell Azure 虚拟桌面（经典版）自定义 RDP 属性 - Azure
description: 如何通过 PowerShell cmdlet 为 Azure 虚拟桌面（经典版）自定义 RDP 属性。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9a9d5461be3a3e8954593d8230ea3d58f0c7ef6c
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111752074"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--azure-virtual-desktop-classic-host-pool"></a>为 Azure 虚拟桌面（经典版）主机池自定义远程桌面协议属性

>[!IMPORTANT]
>此内容适用于 Azure 虚拟桌面（经典），后者不支持 Azure 资源管理器 Azure 虚拟桌面对象。 若要尝试管理 Azure 资源管理器 Azure 虚拟桌面对象，请参阅[本文](../customize-rdp-properties.md)。

通过自定义主机池的远程桌面协议 (RDP) 属性（例如多监视器体验和音频重定向），可以根据用户的需要为用户提供最佳体验。 可以使用 Set-RdsHostPool cmdlet 中的 -CustomRdpProperty 参数自定义 Azure 虚拟桌面中的 RDP 属性。

有关支持的属性及其默认值的完整列表，请参阅[支持的 RDP 文件设置](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)。

首先[下载并导入 Azure 虚拟桌面 PowerShell 模块](/powershell/windows-virtual-desktop/overview/)（如果尚未这样做），方便在 PowerShell 会话中使用。 然后，运行以下 cmdlet 登录到你的帐户：

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>默认 RDP 属性

默认情况下，已发布的 RDP 文件包含以下属性：

|RDP 属性 | 台式机 | RemoteApps |
|---|---| --- |
| 多监视器模式 | 已启用 | 空值 |
| 驱动器重定向已启用 | 驱动器、剪贴板、打印机、COM 端口、USB 设备和智能卡| 驱动器、剪贴板和打印机 |
| 远程音频模式 | 本地播放 | 本地播放 |

为主机池定义的任何自定义属性都将覆盖这些默认值。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>添加或编辑单个自定义 RDP 属性

若要添加或编辑单个自定义 RDP 属性，请运行以下 PowerShell cmdlet：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![已突出显示 Name 和 FriendlyName 的 PowerShell cmdlet Get-RDSRemoteApp 编辑自定义 R D P 属性的屏幕截图。](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>添加或编辑多个自定义 RDP 属性

若要添加或编辑多个自定义 RDP 属性，请运行以下 PowerShell cmdlet，方法是以分号分隔的字符串提供自定义 RDP 属性：

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![已突出显示 Name 和 FriendlyName 的 PowerShell cmdlet Set-RDSRemoteApp 编辑自定义 R D P 属性的屏幕截图。](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>重置所有自定义 RDP 属性

可以按照[添加或编辑单个自定义 RDP 属性](#add-or-edit-a-single-custom-rdp-property)中的说明，将各个自定义 RDP 属性重置为其默认值，也可以通过运行以下 PowerShell cmdlet 来重置主机池的所有自定义 RDP 属性：

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![已突出显示 Name 和 FriendlyName 的 PowerShell cmdlet Get-RDSRemoteApp 的屏幕截图。](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>后续步骤

现在你已自定义给定主机池的 RDP 属性，因此可以登录到 Azure 虚拟桌面客户端，将其作为用户会话的一部分进行测试。 接下来的两篇操作指南将会介绍如何使用所选的客户端连接到会话：

- [使用 Windows 桌面客户端进行连接](connect-windows-7-10-2019.md)
- [使用 Web 客户端进行连接](connect-web-2019.md)