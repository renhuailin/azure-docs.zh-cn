---
title: Windows 虚拟桌面准备 .MSIX 应用附加图像预览-Azure
description: 如何为 Windows 虚拟桌面主机池创建 .MSIX 应用附加映像。
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97425744"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>为 Windows 虚拟桌面准备 .MSIX 映像

> [!IMPORTANT]
> .MSIX 应用附加当前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

.MSIX 应用附加 (预览) 是应用程序分层解决方案，可用于将应用从 .MSIX 包动态连接到用户会话。 .MSIX 包系统将应用与操作系统分离，使为虚拟机构建映像变得更加容易。 借助 .MSIX 包，你还可以更好地控制用户在其虚拟机中可以访问的应用。 你甚至可以将应用程序与主映像分离，稍后再将其分配给用户。

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>为 .MSIX 创建 VHD 或 VHDX 包

.MSIX 包需要使用 VHD 或 VHDX 格式才能正常工作。 这意味着，若要开始，需要创建 VHD 或 VHDX 包。

>[!NOTE]
>如果尚未这样做，请确保按照在 [Windows 10 上安装 hyper-v](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v)中的说明启用 hyper-v。

若要为 .MSIX 创建 VHD 或 VHDX 包：

1. 首先，打开 PowerShell。
2. 接下来，运行以下 cmdlet 以创建 VHD：

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > 请确保 VHD 足够大，可容纳扩展的 .MSIX 包。

3. 运行以下 cmdlet 以装载刚创建的 VHD：

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. 接下来，运行此 cmdlet 以初始化装入的 VHD：

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. 之后，运行此 cmdlet 可为初始化的 VHD 创建新的分区：

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. 运行此 cmdlet 可设置分区的格式：

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. 最后，在装入的 VHD 上创建父文件夹。 此步骤是必需的，因为 .MSIX 包必须具有父文件夹才能正常工作。 无论父文件夹的命名方式如何，只要存在父文件夹。

## <a name="expand-msix"></a>展开 MSIX

之后，需要通过将其文件 "解包" 到 VHD 来展开 .MSIX 映像。

展开 .MSIX 映像：

1. [下载 msixmgr 工具](https://aka.ms/msixmgr)，并将 .zip 文件夹保存到会话主机 VM 中的文件夹中。

2. 解压缩 msixmgr 工具 .zip 文件夹。

3. 将源 MSIX 包放入解压缩的 msixmgr 工具的同一文件夹中。

4. 以管理员身份打开命令提示符并导航到下载并解压缩 msixmgr 工具的文件夹。

5. 运行以下 cmdlet，将 .MSIX 解压缩到在上一部分中创建的 VHD。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    解压缩完成后，应显示以下消息：

    > 已成功解包和应用包的 Acl： <package name> . .msix

    >[!NOTE]
    > 如果要在网络上或在未连接到 internet 的设备上使用来自 Microsoft Store for Business 或教育版的包，则需要从 Microsoft Store 下载并安装包许可证，才能运行这些应用。 若要获取许可证，请参阅 [脱机使用包](app-attach.md#use-packages-offline)。

6. 请继续执行装载的 VHD 并打开应用文件夹，以确保包的内容存在。

7. 卸载 VHD。

## <a name="upload-msix-image-to-share"></a>将 .MSIX 映像上传到共享

创建 .MSIX 包后，需要将生成的 VHD、VHDX 或 CIM 文件上传到用户的虚拟机可以访问的共享位置。

## <a name="next-steps"></a>后续步骤

请在 [Windows 虚拟桌面 TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)上咨询有关此功能的社区问题。

还可以在 [Windows 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Windows 虚拟桌面的反馈。

下面是一些可能有用的其他文章：

- [.MSIX 应用附加术语表](app-attach-glossary.md)
- [.MSIX 应用附加常见问题解答](app-attach-faq.md)