---
title: Windows 虚拟桌面 .MSIX 应用附加预览 PowerShell-Azure
description: 如何使用 PowerShell 为 Windows 虚拟桌面设置 .MSIX app attach。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8aa6a2168bff6e90d636770804900fa93f081ced
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425742"
---
# <a name="set-up-msix-app-attach-preview-using-powershell"></a>使用 PowerShell 设置 .MSIX 应用附加 (预览) 

> [!IMPORTANT]
> .MSIX 应用附加当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

除了 Azure 门户之外，还可以通过 PowerShell 手动设置 .MSIX 应用附加 (preview) 。 本文介绍如何使用 PowerShell 设置 .MSIX app attach。

## <a name="requirements"></a>要求

>[!IMPORTANT]
>在开始之前，请确保填写并提交 [此表单](https://aka.ms/enablemsixappattach) ，以便在你的订阅中启用 .msix 应用附件。 如果没有已批准的请求，.MSIX 应用附加将不起作用。 在工作日内批准请求可能需要长达24小时。 请求被接受并完成后，会收到一封电子邮件。

下面是配置 .MSIX app attach 所需的内容：

- 正常运行的 Windows 虚拟桌面部署。 若要了解如何部署 Windows 虚拟桌面 (经典) ，请参阅 [在 Windows 虚拟桌面中创建租户](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)。 若要了解如何使用 Azure 资源管理器集成部署 Windows 虚拟桌面，请参阅 [使用 Azure 门户创建主机池](./create-host-pools-azure-marketplace.md)。
- 至少具有一个活动会话主机的 Windows 虚拟桌面主机池。
- 桌面远程应用组。
- .MSIX 打包工具。
- 扩展到已上载到文件共享中的 .MSIX 映像的 .MSIX 封装应用程序。
- Windows 虚拟桌面部署中将存储 .MSIX 包的文件共享。
- 你上载 .MSIX 映像的文件共享还必须可供主机池中 (Vm) 的所有虚拟机访问。 用户将需要只读权限才能访问该映像。
- 下载并安装 PowerShell Core。
- 下载公共预览版 Azure PowerShell 模块，并将其扩展到本地文件夹。
- 通过运行以下 cmdlet 安装 Azure 模块：

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>登录到 Azure 并导入模块

准备好所有需求后，请在提升的命令提示符中打开 PowerShell core，并运行以下 cmdlet：

```powershell
Connect-AzAccount
```

运行后，使用你的凭据对你的帐户进行身份验证。 在这种情况下，系统可能会要求提供设备 URL 或令牌。

## <a name="import-the-azwindowsvirtualdesktop-module"></a>导入 WindowsVirtualDesktop 模块

需要 DesktopVirtualization 模块，才能按照本文中的说明进行操作。

>[!NOTE]
>对于公共预览版，我们将提供模块作为你必须手动导入的单独的 ZIP 文件。

在开始之前，你可以运行以下 cmdlet 来查看你的会话或 VM 上是否已安装了 DesktopVirtualization 模块：

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

如果要卸载模块的现有副本并重新开始，请运行以下 cmdlet：

```powershell
Uninstall-Module Az.DesktopVirtualization
```

如果在 VM 上阻止该模块，请运行此 cmdlet 对其取消阻止：

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

通过这种方式，可以导入模块。

1. 运行以下 cmdlet，并在出现提示时按 **R** 键以同意运行自定义代码。

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. 运行 import cmdlet 后，请运行以下 cmdlet，查看它是否具有用于 .MSIX 的 cmdlet：

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   如果其中有 cmdlet，输出应如下所示：

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   如果看不到此输出，请关闭所有 PowerShell 和 PowerShell Core 会话，然后重试。

## <a name="set-up-helper-variables"></a>设置帮助器变量

导入模块后，需要设置帮助器变量。 下面的示例将演示如何执行每个操作。

获取订阅 ID：

```powershell
Get-AzContext -ListAvailable | fl
```

选择名为的 Azure 租户和订阅的上下文：

```powershell
$obj = Select-AzContext -Name "<Name>"
```

若要设置订阅变量：

```powershell
$subId = $obj.Subscription.Id
```

设置工作区名称：

```powershell
$ws = "<WorksSpaceName>"
```

若要设置主机池名称，请执行以下操作：

```powershell
$hp = "<HostPoolName>"
```

设置用于配置会话主机 Vm 的资源组：

```powershell
$rg = "<ResourceGroupName>"
```

最后，确认正确设置了所有变量：

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>将 .MSIX 包添加到主机池

完成所有设置后，就可以将 .MSIX 包添加到主机池了。 为此，首先需要获取 .MSIX 映像的 UNC 路径。

使用 UNC 路径，运行此 cmdlet 以展开 .MSIX 映像：

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

运行此 cmdlet 可将 .MSIX 包添加到所需的主机池：

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

完成后，确认已通过此 cmdlet 创建包：

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>从主机池中删除 .MSIX 包

从主机池删除包：

使用此 cmdlet 获取与主机池关联的所有包的列表，然后在输出中查找要删除的包的名称：

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

此外，还可以使用此 cmdlet 根据其显示名称获取特定包：

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

若要删除该包，请运行以下 cmdlet：

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>将 .MSIX 应用发布到应用组

如果已按照前面几节中的说明进行操作，则只能按照此部分中的说明进行操作。 如果有一个具有活动会话主机的主机池，至少有一个桌面应用组，并已将 .MSIX 包添加到了主机池，则可以开始使用。

若要将应用从 .MSIX 包发布到应用组，你需要找到其名称，然后在发布 cmdlet 中使用该名称。

若要发布应用：

运行此 cmdlet 可列出所有可用的应用组：

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

找到要将应用发布到的应用组的名称后，在此 cmdlet 中使用其名称：

```powershell
$grName = "<AppGroupName>"
```

最后，需要发布该应用。

- 若要将 .MSIX 应用程序发布到桌面应用组，请运行以下 cmdlet：

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- 若要将应用发布到远程应用组，请改为运行此 cmdlet：

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>如果将用户分配到同一主机池中的远程应用组和桌面应用组，则当用户连接到其远程桌面时，他们将看到两个组中的 .MSIX 应用。

## <a name="next-steps"></a>后续步骤

请在 [Windows 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)上咨询有关此功能的社区问题。

还可以在 [Windows 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Windows 虚拟桌面的反馈。

下面是一些可能有用的其他文章：

- [.MSIX 应用附加术语表](app-attach-glossary.md)
- [.MSIX 应用附加常见问题解答](app-attach-faq.md)