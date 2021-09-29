---
title: Azure 虚拟桌面准备 MSIX 应用附加映像 - Azure
description: 如何为 Azure 虚拟桌面主机池创建 MSIX 应用附加映像。
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: e804168d56092b6e5451aa26c7a2106b96672865
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587340"
---
# <a name="prepare-an-msix-image-for-azure-virtual-desktop"></a>为 Azure 虚拟桌面准备 MSIX 映像

MSIX 应用附加是一个应用程序分层解决方案，可用于将应用从 MSIX 包动态附加到用户会话。 MSIX 包系统将应用与操作系统分开，这样更易于为虚拟机构建映像。 借助 MSIX 包，还可以更好地控制用户在其虚拟机中可以访问哪些应用。 甚至可以将应用与主映像分开，稍后再将这些应用提供给用户。

有关如何将桌面安装程序（例如 MSI、EXE、ClickOnce、App-V 或脚本）转换为 MSIX 的说明，请参阅[从任何桌面安装程序（MSI、EXE、ClickOnce 或 App-V）创建 MSIX 包](/windows/msix/packaging-tool/create-app-package)。

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>为 MSIX 创建 VHD 或 VHDX 包

MSIX 包需要采用 VHD 或 VHDX 格式才能正常工作。 这意味着，需要创建 VHD 或 VHDX 包才能开始使用。

>[!NOTE]
>确保按照[在 Windows 10 上安装 Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) 中的说明来启用 Hyper-V（如果你尚未这样做）。

若要为 MSIX 创建 VHD 或 VHDX 包，请执行以下操作：

1. 首先，打开 PowerShell。
2. 然后，运行以下 cmdlet 来创建 VHD：

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > 确保 VHD 能够容纳展开后的 MSIX 包。

3. 运行以下 cmdlet 来装载刚创建的 VHD：

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. 接下来，运行此 cmdlet 来初始化装载的 VHD：

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. 然后运行此 cmdlet，为初始化的 VHD 创建新分区：

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. 运行此 cmdlet 可设置分区的格式：

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. 最后，在装载的 VHD 上创建父文件夹。 此步骤是必需的，因为 MSIX 包必须有父文件夹才能正常工作。 父文件夹如何命名无关紧要，只要父文件夹存在即可。

## <a name="expand-msix"></a>展开 MSIX

在此之后，需要通过将 MSIX 映像的文件“解压缩”到 VHD 中来展开该映像。

若要展开 MSIX 映像，请执行以下操作：

1. [下载 msixmgr 工具](https://aka.ms/msixmgr)，并将 .zip 文件夹保存到会话主机 VM 中的文件夹中。

2. 解压缩 msixmgr 工具 .zip 文件夹。

3. 将源 MSIX 包放入解压缩的 msixmgr 工具的同一文件夹中。

4. 以管理员身份打开命令提示符并导航到下载并解压缩 msixmgr 工具的文件夹。

5. 运行以下 cmdlet，以将 MSIX 解压缩到在上一部分创建的 VHD 中。

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    在解压缩完成后，应显示以下消息：

    > 已成功为包 (\<package name\>.msix) 解压缩并应用 ACL

    >[!NOTE]
    > 如果你在自己的网络上或未连接到 Internet 的设备上使用企业/教育 Microsoft Store 中的包，则需要从 Microsoft Store 下载并安装包许可证才能运行应用。 若要获取许可证，请参阅[脱机使用包](app-attach.md#use-packages-offline)。

6. 找到装载的 VHD 并打开应用文件夹，以确保包内容存在。

7. 卸载 VHD。

## <a name="upload-msix-image-to-share"></a>将 MSIX 映像上传到共享位置

在创建了 MSIX 包后，需要将生成的 VHD、VHDX 或 CIM 文件上传到用户的虚拟机可以访问的某个共享位置。

## <a name="next-steps"></a>后续步骤

请在 [Azure 虚拟桌面技术社区](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)询问有关此功能的问题。

还可以在 [Azure 虚拟桌面反馈中心](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app)提供关于 Azure 虚拟桌面的反馈。

下面是一些可能有用的其他文章：

- [MSIX 应用附加术语表](app-attach-glossary.md)
- [MSIX 应用附加常见问题解答](app-attach-faq.yml)
