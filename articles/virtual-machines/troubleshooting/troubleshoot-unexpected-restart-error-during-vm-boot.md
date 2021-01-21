---
title: 操作系统启动-计算机意外重新启动或遇到意外错误
description: 本文提供的步骤可用于解决 VM 在安装 Windows 时遇到意外重启或错误的问题。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: d8d2ab2bb3f24e1faa4791ebdc1ce3852f6a790e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632684"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>操作系统启动-计算机意外重新启动或遇到意外错误

本文提供的步骤可用于解决虚拟机 (VM) 在安装 Windows 时遇到意外重启或错误的问题。

## <a name="symptom"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，可看到屏幕截图显示 Windows 安装失败，出现以下错误：

**计算机意外重启或遇到意外错误。Windows 安装无法继续。要安装 Windows，请单击“确定”重启计算机，然后重新开始安装。**

![进行 Windows 安装时出错：计算机意外重启或遇到意外错误。 Windows 安装无法继续。 要安装 Windows，请单击“确定”重启计算机，然后重新开始安装。](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Windows 安装程序启动服务时出错：计算机意外重启或遇到意外错误。 Windows 安装无法继续。 要安装 Windows，请单击“确定”重启计算机，然后重新开始安装。](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>原因

虚拟机在尝试初次启动[通用映像](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)，但因正在处理的自定义应答文件 (Unattend.xml) 而遇到了问题。 Azure 中不支持自定义应答文件。 

应答文件是一种特殊的 XML 文件，其中包含要在安装 Windows Server 操作系统的过程中自动完成的配置设置的设置定义和值。 配置选项包括有关以下内容的说明：如何对磁盘进行分区、在何处查找要安装的 Windows 映像、要应用的产品密钥，以及要运行的其他命令。

同样，Azure 中不支持自定义应答文件。 因此，发生这种情况的场景是，已经准备了要在 Azure 中使用的映像，但你通过将 SYSPREP 与一个标志配合使用（类似于以下命令）指定了自定义 Unattend.xml 文件：

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

在 Azure 中，请使用系统准备工具 GUI 中的“进入系统全新安装体验(OOBE)”选项，或者使用 `sysprep /oobe`，而不要使用 Unattend.xml 文件 。

此问题通常在你将 sysprep 与本地 VM 一起使用以将通用 VM 上传到 Azure 时发生。 在这种情况下，你可能还对如何正确上传通用 VM 感兴趣。

## <a name="solution"></a>解决方案

### <a name="do-not-use-unattendxml"></a>请勿使用 Unattend.xml

> [!TIP]
> 如果你有 VM 的最新备份，则可以尝试 [从备份还原 vm](../../backup/backup-azure-arm-restore-vms.md) ，以解决启动问题。

要修复此问题，请按照[有关准备/捕获映像的 Azure 指南](../windows/upload-generalized-managed.md)操作，并准备新的通用映像。 在执行 sysprep 的过程中，请勿使用 `/unattend:<your file’s name>` 标志。 请改为仅使用下面的标志：

`sysprep /oobe /generalize /shutdown`

- 全新安装体验 (OOBE) 是 Azure VM 支持的设置。

你还可以通过选择下面显示的选项，使用“系统准备工具 GUI”来完成通过上述命令完成的相同任务：

- 进入全新安装体验
- 通用化
- Shutdown
 
![“系统准备工具”窗口，其中选中了“OOBE”、“通用化”和“关闭”选项。](./media/unexpected-restart-error-during-vm-boot/3.png)
