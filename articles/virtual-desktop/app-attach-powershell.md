---
title: Azure 虚拟桌面 MSIX 应用附加 PowerShell - Azure
description: 如何使用 PowerShell 设置 Azure 虚拟桌面的 MSIX 应用附加。
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
ms.custom: devx-track-azurepowershell
manager: femila
ms.openlocfilehash: d77dfb8883aac4e960274ce86de243f25df2f196
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114707041"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>通过 PowerShell 设置 MSIX 应用附加

除 Azure 门户之外，还可以通过 PowerShell 手动设置 MSIX 应用附加。 本文介绍如何使用 PowerShell 设置 MSIX 应用附加。

## <a name="requirements"></a>要求

配置 MSIX 应用附加需要以下各项：

- 正常运行的 Azure 虚拟桌面部署。 要了解如何部署 Azure 虚拟桌面（经典），请参阅[在 Azure 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)。 要了解如何使用 Azure 资源管理器集成部署 Azure 虚拟桌面，请参阅[使用 Azure 门户创建主机池](./create-host-pools-azure-marketplace.md)。
- 至少具有一个活动会话主机的 Azure 虚拟桌面主机池。
- 桌面远程应用组。
- MSIX 打包工具。
- 展开到已上传到文件共享中的 MSIX 映像的 MSIX 打包应用程序。
- Azure 虚拟桌面部署中将存储 MSIX 包的文件共享。
- 上传了 MSIX 映像的文件共享还必须可由主机池中的所有虚拟机 (VM) 访问。 用户需要只读权限才能访问该映像。
- 下载并安装 PowerShell Core。
- 下载公共预览版 Azure PowerShell 模块，并将其展开到本地文件夹。
- 通过运行以下 cmdlet 安装 Azure 模块：

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>登录到 Azure 并导入模块

满足所有需求后，在提升的命令提示符中打开 PowerShell core，并运行以下 cmdlet：

```powershell
Connect-AzAccount
```

运行后，使用凭据对帐户进行身份验证。 在这种情况下，系统可能会要求你提供设备 URL 或令牌。

## <a name="import-the-azwindowsvirtualdesktop-module"></a>导入 Az.WindowsVirtualDesktop 模块

需要 Az.WindowsVirtualDesktop 模块，才能按照本文中的说明进行操作。

>[!NOTE]
>对于公共预览版，我们将提供此模块作为必须手动导入的单独的 ZIP 文件。

在开始之前，可以运行以下 cmdlet 来查看你的会话或 VM 上是否已安装 Az.DesktopVirtualization 模块：

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

如果要卸载模块的现有副本并重新开始，请运行以下 cmdlet：

```powershell
Uninstall-Module Az.DesktopVirtualization
```

如果 VM 上阻止了该模块，请运行此 cmdlet 对其取消阻止：

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

取消阻止后，可以导入模块。

1. 运行以下 cmdlet，并根据提示按 R 键以同意运行自定义代码。

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. 运行导入 cmdlet 后，请运行以下 cmdlet，查看它是否有用于 MSIX 的 cmdlet：

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   如果有 cmdlet，输出应如下所示：

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   如果没有此输出，请关闭所有 PowerShell 和 PowerShell Core 会话，然后重试。

## <a name="set-up-helper-variables"></a>设置帮助程序变量

导入模块后，需要设置帮助程序变量。 下面的示例将演示如何执行每个操作。

获取订阅 ID：

```powershell
Get-AzContext -ListAvailable | fl
```

选择带名称的 Azure 租户和订阅的上下文：

```powershell
$obj = Select-AzContext -Name "<Name>"
```

设置订阅变量：

```powershell
$subId = $obj.Subscription.Id
```

设置工作区名称：

```powershell
$ws = "<WorksSpaceName>"
```

设置主机池名称：

```powershell
$hp = "<HostPoolName>"
```

设置用于配置会话主机 VM 的资源组：

```powershell
$rg = "<ResourceGroupName>"
```

最后，确认已正确设置所有变量：

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>将 MSIX 包添加到主机池

完成所有设置后，就可以将 MSIX 包添加到主机池了。 为此，首先需要获取 MSIX 映像的 UNC 路径。

使用 UNC 路径，运行此 cmdlet 以展开 MSIX 映像：

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

运行此 cmdlet 可将 MSIX 包添加到所需的主机池：

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

完成后，确认已通过此 cmdlet 创建包：

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>从主机池中删除 MSIX 包

从主机池中删除 MSIX 包：

使用此 cmdlet 获取与主机池关联的所有包的列表，然后在输出中查找要删除的包的名称：

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

此外，还可以使用此 cmdlet 根据其显示名称获取特定包：

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

要删除此包，请运行以下 cmdlet：

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>将 MSIX 应用发布到应用组

如果已按照前面几节中的说明进行了操作，则只能按照本部分中的说明进行操作。 如果你有一个具有活动会话主机的主机池（至少一个桌面应用组），并已将 MSIX 包添加到了主机池，则可以开始操作。

要将应用从 MSIX 包发布到应用组，需要找到其名称，然后在发布 cmdlet 中使用该名称。

要发布应用，请执行以下操作：

运行此 cmdlet 以列出所有可用的应用组：

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

找到要将应用发布到的应用组的名称后，在此 cmdlet 中使用其名称：

```powershell
$grName = "<AppGroupName>"
```

最后，需要发布该应用。

- 要将 MSIX 应用程序发布到桌面应用组，请运行以下 cmdlet：

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- 要将应用发布到远程应用组，请改为运行以下 cmdlet：

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>如果将某个用户同时分配到同一主机池中的远程应用组和桌面应用组，则当该用户连接到其远程桌面时，他们将看到两个组的 MSIX 应用。

## <a name="next-steps"></a>后续步骤

请在 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)询问有关此功能的问题。

还可以在 [Azure 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Azure 虚拟桌面的反馈。

下面是一些可能有用的其他文章：

- [MSIX 应用附加术语表](app-attach-glossary.md)
- [MSIX 应用附加常见问题解答](app-attach-faq.yml)
