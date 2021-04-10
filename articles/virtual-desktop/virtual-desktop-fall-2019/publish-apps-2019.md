---
title: 在 Windows 虚拟桌面（经典）中发布内置应用 - Azure
description: 如何在 Windows 虚拟桌面（经典）中发布内置应用。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1b179ac555ea86aff381c1217e834b8d0aa85e8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561698"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop-classic"></a>在 Windows 虚拟桌面（经典）中发布内置应用

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。 要尝试管理 Azure 资源管理器 Windows 虚拟桌面对象，请参阅[本文](../publish-apps.md)。

本文将介绍如何在 Windows 虚拟桌面环境中发布应用。

## <a name="publish-built-in-apps"></a>发布内置应用

发布内置应用：

1. 连接到主机池中的某个虚拟机。
2. 按照[本文](/powershell/module/appx/get-appxpackage)中的说明获取要发布的应用的 PackageFamilyName。
3. 最后，运行以下 cmdlet，并将其 `<PackageFamilyName>` 替换为在上一步中找到的 PackageFamilyName：

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:appsFolder\<PackageFamilyName>!App"
   ```

>[!NOTE]
> Windows 虚拟桌面仅支持发布其安装位置以 `C:\Program Files\Windows Apps` 开头的应用。

## <a name="update-app-icons"></a>更新应用图标

发布应用后，它将具有默认的 Windows 应用图标，而不是其常规图标图片。 要将图标更改为应用的常规图标，请将所需图标的图像放在网络共享上。 支持的图像格式为 PNG、BMP、GIF、JPG、JPEG 和 ICO。

## <a name="publish-microsoft-edge"></a>发布 Microsoft Edge

用于发布 Microsoft Edge 的过程与其他应用的发布过程稍有不同。 若要发布带有默认主页的 Microsoft Edge，请运行以下 cmdlet：

```powershell
New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -FriendlyName <remoteappname> -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge"
```

## <a name="next-steps"></a>后续步骤

- 若要了解如何配置源以组织应用向用户显示的方式，请参阅[为 Windows 虚拟桌面用户自定义源](customize-feed-virtual-desktop-users-2019.md)。
- 若要了解有关 MSIX 应用附加功能，请参阅[设置 MSIX 应用附加](../app-attach.md)。

