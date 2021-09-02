---
title: 通过 USB-C 连接更新 Azure Percept DK
description: 了解如何通过 USB-C 数据线连接更新 Azure Percept DK
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: eef23597e6cb8f215fd7ce2374d66ff104cc00bc
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226018"
---
# <a name="update-azure-percept-dk-over-a-usb-c-connection"></a>通过 USB-C 连接更新 Azure Percept DK

本指南将介绍如何通过 USB 连接成功更新开发工具包的操作系统和固件。 下面概述了此过程期间要执行的操作。

1. 将更新包下载到主机
1. 运行将更新包转移到开发工具包的命令
1. 使用 SSH 或 DIP 开关将开发工具包设置为 USB 模式
1. 通过 USB-C 数据线将开发工具包连接到主计算机
1. 等待更新完成

> [!WARNING]
> 通过 USB 更新开发工具包会删除设备上的所有现有数据（包括 AI 模型和容器）。
>
> 请按顺序遵循所有说明操作。 跳过某些步骤可能导致开发工具包不可用。

## <a name="prerequisites"></a>必备条件

- 一个 Azure Percept DK
- 一台基于 Windows 或 Linux 的主计算机，它具备 Wi-Fi 功能和可用 USB-C 或 USB-A 端口
- 一根 USB-C 转 USB-A 数据线（可选，单独销售）
- 一个在 [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)期间创建的 SSH 登录帐户
- 开发工具包附带的一个六角扳手，用于取下开发工具包背面的螺钉（如果使用 DIP 开关方法）

> [!NOTE]
> Mac 用户 - 使用 Mac 作为主计算机时，将无法通过 USB 连接更新 Azure Percept DK。 

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

## <a name="run-the-command-that-transfers-the-update-package-to-the-dev-kit"></a>运行将更新包转移到开发工具包的命令

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

1. 命令提示符窗口将显示一条消息，指出“正在等待显示已知 USB 设备…”。UUU 工具现在正在等待主计算机检测到开发工具包。 继续执行后续步骤，并将开发工具包置于 USB 模式下。

## <a name="set-the-dev-kit-into-usb-mode"></a>将开发工具包设置为 USB 模式
有两种方法来将开发工具包设置为“USB 模式”：通过 SSH，或者通过更改开发工具包上的 DIP 开关。 请选择最适合自身情况的方法。

### <a name="using-ssh"></a>使用 SSH
要将开发工具包设置为 USB 模式，最安全的首选方法是使用 SSH。 但是，它确实要求你连接到开发工具包的 Wi-Fi 接入点。 如果无法连接到开发工具包的 Wi-Fi 接入点，则需要使用 DIP 开关方法。

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

### <a name="using-the-dip-switch-method"></a>使用 DIP 开关方法
当无法通过 SSH 连接到设备时，请使用 DIP 开关方法。

1. 如果开发板已连接电源线，请拔下它。
1. 使用开发工具包附带的六角扳手取下开发板背面的 4 颗螺钉。

    :::image type="content" source="media/how-to-usb-update/dip-switch-01.jpg" alt-text="取下开发板背面的 4 颗螺钉":::

1. 在 LED 方向轻轻滑动开发板。 散热器将继续连接到开发板的顶部。 仅将开发板滑动 2-3 厘米，避免断开任何数据线连接。

    :::image type="content" source="media/how-to-usb-update/dip-switch-02.jpg" alt-text="将开发板滑动几厘米":::

1. 可在开发板的一角找到 DIP 开关。 有 4 个开关，每个开关都有两个位置 - 向上 (1) 或向下 (0)。 这些开关的默认位置是向上-向下-向下-向上 (1001)。 使用回形针或其他尖形器具将开关的位置更改为向下-向上-向下-向上 (0101)。

    :::image type="content" source="media/how-to-usb-update/dip-switch-03.jpg" alt-text="找到开发板下方一角的开关":::

1. 开发工具包现在处于 USB 模式，你可继续执行后续步骤。 更新完成后，将 DIP 开关更改回默认位置，即向上-向下-向下-向上 (1001)。 然后，将开发板滑回原位，并重新安装背面的 4 颗螺钉。

## <a name="connect-the-dev-kit-to-the-host-computer-via-a-usb-c-cable"></a>通过 USB-C 数据线将开发工具包连接到主计算机
此过程使用开发工具包的单个 USB-C 端口进行更新。  如果你的计算机具有 USB-C 端口，你可使用开发工具包附带的 USB-C 转 USB-C 数据线。  如果你的计算机只有 USB-A 端口，则需要使用 USB-C 转 USB-A 数据线（单独销售）。

1. 使用合适的 USB-C 数据线将开发工具包连接到主计算机。
1. 主计算机现在应将开发工具包检测为 USB 设备。 如果成功地运行了将更新包传输到开发工具包的命令，并且命令提示符指示“等待显示已知 USB 设备…”，**则更新应在大约 10 秒后自动启动。

## <a name="wait-for-the-update-to-complete"></a>等待更新完成

1. 导航回到另一命令提示符或终端。 更新完成后，会显示包含 ```Success 1    Failure 0``` 的消息：

    > [!NOTE]
    > 更新后，设备将重置为出厂设置，你将丢失 Wi-Fi 连接和 SSH 登录名。

1. 更新完成后，关闭开发工具包电源。 从电脑上拔下 USB 数据线。
1. 如果使用 DIP 开关方法将开发工具包置于 USB 模式下，则请确保将 DIP 开关更改回默认位置。 然后，将开发板滑回原位，并重新安装背面的 4 颗螺钉。   

## <a name="next-steps"></a>后续步骤

完成 [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)以重新配置设备。
