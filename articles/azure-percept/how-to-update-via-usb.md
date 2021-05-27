---
title: 通过 USB-C 数据线连接更新 Azure Percept DK
description: 了解如何通过 USB-C 数据线连接更新 Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39889455cd41883f5782f9fb140b400d6ed09ed7
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109786728"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-c-cable-connection"></a>如何通过 USB-C 数据线连接更新 Azure Percept DK

本指南将介绍如何通过 USB 连接成功更新开发工具包的操作系统和固件。 下面概述了此过程期间要执行的操作。
1. 将更新包下载到主机
1. 运行将更新包转移到开发工具包的命令
1. （使用 SSH）将开发工具包设置为“USB 模式”，以便主机能够检测到它并接收更新包
1. 通过 USB-C 数据线将开发工具包连接到主机
1. 等待更新完成

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

1. [NXP UUU 工具](https://github.com/NXPmicro/mfgtools/releases)。 在“资产”选项卡下下载最新版本的 uuu.exe 文件（适用于 Windows）或 uuu 文件（适用于 Linux）。UUU 是 NXP 创建的工具，用于更新 NXP 开发板。 

1. [下载更新文件](https://go.microsoft.com/fwlink/?linkid=2155734)。 它们都包含在 zip 文件中，你将在下一部分中提取该文件。

1. 确保存在以下所有三个生成工件：
    - Azure-Percept-DK-&lt;版本号&gt;.raw
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>设置你的环境

1. 在主计算机上可通过命令行轻松访问的某个位置创建一个文件夹/目录。

1. 将 UUU 工具（uuu.exe 或 uuu）复制到新文件夹 。

1. 将之前下载的更新文件提取到包含 UUU 工具的新文件夹。

## <a name="update-your-device"></a>更新设备

此过程使用开发工具包的单个 USB-C 端口进行更新。  如果计算机具有 USB-C 端口，可以断开 Azure Percept Vision 设备的连接并使用该数据线。  如果计算机只有 USB-A 端口，请将 Azure Percept Vision 设备与开发工具包的 USB-C 端口断开连接，将 USB-C 转 USB-A 数据线（单独销售）连接到开发工具包和主机。

1. 打开 Windows 命令提示符（“开始”>“cmd”）或 Linux 终端，然后导航到存储更新文件和 UUU 工具的文件夹。 

1. 在命令提示符或终端中输入以下命令。

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux：

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. 命令提示符窗口将显示一条消息，指出“正在等待已知 USB 设备显示…”。UUU 工具现在正在等待主机检测到开发工具包。 现在可以继续执行后续步骤。

1. 将附送的 USB-C 数据线连接到开发工具包的 USB-C 端口以及主计算机的 USB-C 端口。 如果你的计算机只有 USB-A 端口，请将 USB-C 转 USB-A 数据线（单独销售）连接到开发工具包和主计算机。

1. 通过 SSH 连接到开发工具包。 如果需要 SSH 帮助，[请按照这些说明操作](./how-to-ssh-into-percept-dk.md)。

1. 在 SSH 终端中输入以下命令：

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

1. 更新完成后，关闭开发工具包电源。 从电脑上拔下 USB 数据线。  

## <a name="next-steps"></a>后续步骤

完成 [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)以重新配置设备。
