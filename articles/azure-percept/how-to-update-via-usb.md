---
title: 通过 USB 连接更新 Azure Percept 深色
description: 了解如何通过 USB 连接更新 Azure Percept 深色
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661875"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>如何通过 USB 连接更新 Azure Percept 深色

遵循本指南了解如何为 Azure Percept 深色的运营商板执行 USB 更新。

## <a name="prerequisites"></a>先决条件
- 具有可用 USB-C 或 USB 端口的主机。
- Azure Percept 深色 (开发工具包) 运营商板和提供的 USB 到 USB 电缆。 如果您的主计算机有 USB-A 端口，但不是 USB 端口，则可以使用 USB-C 到 USB-A 电缆 () 单独出售。
- 安装 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) (需要管理员访问权限) 。
- 安装 NXP UUU 工具。 在 "资产" 选项卡下，下载适用于 Windows) 的[最新版本](https://github.com/NXPmicro/mfgtools/releases)uuu.exe 文件 (或适用于) Linux 的 uuu 文件 (。
- [安装 7-Zip](https://www.7-zip.org/)。 此软件将用于从其 XZ 压缩的文件中提取原始映像文件。 下载并安装相应的 .exe 文件。

## <a name="steps"></a>步骤
1.  下载以下 [三个 USB 更新文件](https://go.microsoft.com/fwlink/?linkid=2155734)：
    - pe101-版本的 ***&lt; 值 &gt;***。
    - emmc_full.txt
    - fast-hab-fw
 
1. _从压缩的 pe101_**&lt; &gt;** * _&lt; -uefi 版本号 * * 文件提取到 pe101--uefi 版本。 &gt;_ 不确定如何提取？ 下载并安装 7-Zip，并右键单击 **xz** 图像文件，并在此处选择 7-Zip &gt; 提取。

1. 将以下三个文件复制到包含 UUU 工具的文件夹中：
    - 从步骤 2) 中提取了 pe101 ***&lt; &gt;*** (的原始文件。
    - 步骤 1) emmc_full.txt (。
    - 步骤 1) 中的 fast-hab-fw (。
 
1. 开启开发工具包。
1. [通过 SSH 连接到开发工具包](./how-to-ssh-into-percept-dk.md)
1. 打开 Windows 命令提示符 (启动 &gt; cmd) 或 Linux 终端，然后导航到存储更新文件的文件夹。 运行以下命令以启动更新：
    - Windows： ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux：```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
运行这些命令后，你可能会看到一条消息，指出 "正在等待设备 ..."在命令提示符下。 这是预期的，你应该继续执行下一步。
    
1. 通过 USB 电缆将开发套件电信公司连接到主机计算机。 始终从运营商板 USB-C 端口连接到主机计算机的 USB-C 或 USB-A 端口 (USB-C 连接到 USB-电缆) 单独销售，具体取决于可用的端口。 
 
1. 在 SSH/PuTTY 终端中输入以下命令，将开发工具包设置为 USB 模式，然后重新启动开发工具包。
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. 你可能会发现主机计算机识别设备，并且更新过程将自动启动。 导航回命令提示符以查看状态。 此过程最多需要10分钟，更新成功后，将显示一条消息，指出 "成功1故障 0"
 
1. 更新完成后，请关闭承运人面板。 从电脑拔下 USB 电缆。  使用 USB 电缆将 Azure Percept 远景模块重新插入载波板。

1. 重新打开电信公司。

## <a name="next-steps"></a>后续步骤

开发工具包现在已成功更新。 你可以继续通过 devkit 进行开发和操作。