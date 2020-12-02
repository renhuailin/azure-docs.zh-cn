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
ms.openlocfilehash: cfeb040893ae2be5842959ed8458bd713bebe6ee
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96512131"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>操作系统启动-计算机意外重新启动或遇到意外错误

本文提供的步骤可用于解决虚拟机 (VM) 在安装 Windows 时遇到意外重启或错误的问题。

## <a name="symptom"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，可看到屏幕截图显示 Windows 安装失败，出现以下错误：

**计算机意外重启或遇到意外错误。Windows 安装无法继续。要安装 Windows，请单击“确定”重启计算机，然后重新开始安装。**

![进行 Windows 安装时出错：计算机意外重启或遇到意外错误。 Windows 安装无法继续。 要安装 Windows，请单击“确定”重启计算机，然后重新开始安装。](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Windows 安装程序启动服务时出错：计算机意外重启或遇到意外错误。 Windows 安装无法继续。 要安装 Windows，请单击“确定”重启计算机，然后重新开始安装。](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>原因

计算机正在尝试初始启动 [通用化映像](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)，但由于自定义应答文件 ( # A0) 正在处理而遇到问题。 **Azure 中不支持自定义应答文件**。 

应答文件是一种特殊的 XML 文件，其中包含要在安装 Windows Server 操作系统的过程中自动完成的配置设置的设置定义和值。 配置选项包括有关以下内容的说明：如何对磁盘进行分区、在何处查找要安装的 Windows 映像、要应用的产品密钥，以及要运行的其他命令。

同样，Azure 中不支持自定义应答文件。 因此，当映像已准备好在 Azure 中使用时，会发生这种情况，但你通过使用具有类似于以下命令的标志的 **SYSPREP** 指定了自定义 Unattend.xml 文件：

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

在 Azure 中，使用 " **输入系统全新体验" (** " **系统准备工具" GUI** 中的 "OOBE) " 选项，或使用 `sysprep /oobe` 而非 Unattend.xml 文件。

此问题通常是在将 sysprep 与本地 VM 一起使用以将通用 VM 上传到 Azure 时创建的。 在这种情况下，你可能还对如何正确上传通用 VM 感兴趣。

## <a name="solution"></a>解决方案

### <a name="do-not-use-unattendxml"></a>不要使用 Unattend.xml

要修复此问题，请按照[有关准备/捕获映像的 Azure 指南](../windows/upload-generalized-managed.md)操作，并准备新的通用映像。 在 sysprep 期间，请不要 **使用 " `/unattend:<your file’s name>` 标志**"。 请改为仅使用下面的标志：

`sysprep /oobe /generalize /shutdown`

- 全新安装体验 (OOBE) 是 Azure VM 支持的设置。

你还可以通过选择下面显示的选项，使用“系统准备工具 GUI”来完成通过上述命令完成的相同任务：

- 进入全新安装体验
- 通用化
- Shutdown
 
![“系统准备工具”窗口，其中选中了“OOBE”、“通用化”和“关闭”选项。](./media/unexpected-restart-error-during-vm-boot/3.png)
