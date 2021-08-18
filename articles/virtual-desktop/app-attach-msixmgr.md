---
title: 使用 MSIXMGR 工具 - Azure
description: 如何使用适用于 Azure 虚拟桌面的 MSIXMGR 工具。
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f7c0af3ef2992fdd2c337ff6d1172f6da6db5f56
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114708776"
---
# <a name="using-the-msixmgr-tool"></a>使用 MSIXMGR 工具

MSIXMGR 工具用于将 MSIX 打包的应用程序扩展到 MSIX 映像中。 该工具使用 MSIX 打包的应用程序 (.MSIX) 并将其扩展到 VHD、VHDx 或 CIM 文件中。 生成的 MSIX 映像存储在 Azure 虚拟桌面部署使用的 Azure 存储帐户中。本文将演示如何使用 MSIXMGR 工具。

>[!NOTE]
>为了保证兼容性，请确保在 Azure 虚拟桌面主机池中运行的 OS 版本上生成存储 MSIX 映像的 CIM。 MSIXMGR 可以创建 CIM 文件，但只能将这些文件与运行 Windows 10 20H2 的主机池一起使用。

## <a name="requirements"></a>要求

在可以按照本文中的说明操作之前，需要执行以下操作：

- [下载 MSIXMGR 工具](https://aka.ms/msixmgr)
- 获取 MSIX 打包的应用程序（.MSIX 文件）
- 获取要在其中创建 MSIX 映像的计算机上的管理权限

## <a name="create-an-msix-image"></a>创建 MSIX 映像

扩展是将 MSIX 打包的应用程序 (.MSIX) 解压缩到 MSIX 映像（.VHD(x) 或 .CIM 文件）的过程。

展开 MSIX 文件：

1. [下载 MSIXMGR 工具](https://aka.ms/msixmgr)（如果尚未这样做）。

2. 将 MSIXMGR.zip 解压缩到本地文件夹。

3. 在提升的模式中打开命令提示符。

4. 在步骤 2 中查找本地文件夹。

5. 在命令提示符下运行以下命令以创建 MSIX 映像。

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    请务必将占位符值替换为相关值。 例如：

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. 现在，你已创建了映像，请前往目标文件夹，确保已成功创建 MSIX 映像 (.VHDX)。

## <a name="create-an-msix-image-in-a-cim-file"></a>在 CIM 文件中创建 MSIX 映像

还可以使用[步骤 5](#create-an-msix-image) 中的命令通过替换文件类型和目标路径来创建 CIM 和 VHDX 文件。

例如，下面介绍了如何使用该命令生成 CIM 文件：

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

下面介绍了如何使用该命令来创建 VHDX：

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>后续步骤

要详细了解 MSIX 应用附加，请参阅[什么是 MSIX 应用附加？](what-is-app-attach.md)

要了解如何设置应用附加，请查看以下文章：

- [通过 Azure 门户设置 MSIX 应用附加](app-attach-azure-portal.md)
- [通过 PowerShell 设置 MSIX 应用附加](app-attach-powershell.md)
- [为 MSIX 应用附加创建 PowerShell 脚本](app-attach.md)
- [为 Azure 虚拟桌面准备 MSIX 映像](app-attach-image-prep.md)
- [设置用于 MSIX 应用附加的文件共享](app-attach-file-share.md)

如果你对 MSIX 应用附加有疑问，请参阅我们的[应用附加常见问题解答](app-attach-faq.yml)和[应用附加术语表](app-attach-glossary.md)。
