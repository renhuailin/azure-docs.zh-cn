---
title: 通过 USB 连接更新 Azure Percept DK
description: 了解如何通过 USB 连接更新 Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2021
ms.locfileid: "104887744"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>如何通过 USB 连接更新 Azure Percept DK

尽管使用无线 (OTA) 更新是使开发工具包的操作系统和固件保持最新状态的最佳方法，但某些情况下，需要通过 USB 连接来更新（或“刷写”）开发工具包：

- 由于连接或其他技术问题，无法进行 OTA 更新
- 需要将设备重置为出厂状态

本指南将介绍如何通过 USB 连接成功更新开发工具包的操作系统和固件。

> [!WARNING]
> 通过 USB 更新开发工具包会删除设备上的所有现有数据（包括 AI 模型和容器）。
>
> 请按顺序遵循所有说明操作。 跳过某些步骤可能导致开发工具包不可用。

## <a name="prerequisites"></a>必备条件

- 一个 Azure Percept DK
- 一台具备 Wi-Fi 功能和可用 USB-C 或 USB-A 端口的基于 Windows、Linux 或 OS X 的主计算机
- 一根 USB-C 转 USB-A 数据线（可选，单独销售）
- 在 [体验 Azure Percept DK 设置](./quickstart-percept-dk-set-up.md) 过程中创建的 SSH 登录名

## <a name="download-software-tools-and-update-files"></a>下载软件工具并更新文件

1. [NXP UUU 工具](https://github.com/NXPmicro/mfgtools/releases)。 在“资产”选项卡下，下载最新版本的 uuu.exe 文件（适用于 Windows）或 uuu 文件（适用于 Linux） 。

1. [7-Zip](https://www.7-zip.org/)。 此软件用于从 XZ 压缩文件中提取原始映像文件。 下载并安装适当的 .exe 文件。

1. [下载更新文件](https://go.microsoft.com/fwlink/?linkid=2155734)。

1. 确保存在以下所有三个生成工件：
    - Azure-Percept-DK-&lt;版本号&gt;.raw.xz
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>设置你的环境

1. 在主计算机上可通过命令行轻松访问的某个位置创建一个文件夹/目录。

1. 将 UUU 工具（uuu.exe 或 uuu）复制到新文件夹 。

1. 右键单击“Azure-Percept-DK-&lt;版本号&gt;.raw.xz”并选择“7-Zip”&gt;“提取到此处”，提取压缩文件中的 Azure-Percept-DK-&lt;版本号&gt;.raw.xz   。

1. 将提取的 Azure-Percept-DK-&lt;版本号&gt;.raw 文件、fast-hab-fw.raw 和 emmc_full.txt 移到包含 UUU 工具的文件夹中  。

## <a name="update-your-device"></a>更新设备

1. [通过 SSH 连接到开发工具包](./how-to-ssh-into-percept-dk.md)。

1. 接下来，打开 Windows 命令提示符（“开始” > “cmd”）或 Linux 终端，然后导航到存储更新文件和 UUU 工具的文件夹 。 在命令提示符或终端中输入以下命令，使计算机准备好接收可刷写的设备：

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux：

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. 从载板的 USB-C 端口断开连接 Azure Percept Vision 设备。

1. 将附送的 USB-C 数据线连接到载板的 USB-C 端口以及主计算机的 USB-C 端口。 如果你的计算机只有 USB-A 端口，请将 USB-C 转 USB-A 数据线（单独销售）连接到载板和主计算机。

1. 在 SSH 客户端提示符下输入以下命令：

    1. 将设备设置为 USB 更新模式：

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. 重新启动设备。 更新安装随即开始。

        ```bash
        sudo reboot -f
        ```

1. 导航回到另一命令提示符或终端。 更新完成后，将看到包含 ```Success 1    Failure 0``` 的消息：

    > [!NOTE]
    > 更新后，设备将重置为出厂设置，你将丢失 Wi-Fi 连接和 SSH 登录名。

1. 更新完成后，关闭载板电源。 从电脑上拔下 USB 数据线。  

## <a name="next-steps"></a>后续步骤

完成 [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)以重新配置设备。