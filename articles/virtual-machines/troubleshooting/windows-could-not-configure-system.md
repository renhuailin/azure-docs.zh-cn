---
title: 排查 Windows 无法完成系统配置的问题
titlesuffix: Azure Virtual Machines
description: 本文介绍了如何解决 Sysprep 进程阻止 Azure VM 启动的问题。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 6cb3467fec99bd12810ed058a61de1be7b39cdd0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629583"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>排查 Windows 无法完成系统配置的问题

本文介绍了如何解决 Sysprep 进程阻止 Azure 虚拟机 (VM) 启动的问题。

## <a name="symptom"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的屏幕截图时，你会看到 Windows 安装程序启动服务时，该屏幕截图显示“安装 Windows”错误。 此错误将显示消息：

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![图1显示 "安装 Windows 错误" 错误消息： "Windows 无法完成系统的配置。 若要尝试恢复配置，请重启计算机。 安装程序正在启动服务 "](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>原因

当操作系统 (OS) 无法完成 [Sysprep 进程](/windows-hardware/manufacture/desktop/sysprep-process-overview)时，则会导致此错误。 尝试初次启动通用 VM 时，将发生此错误。 如果遇到此问题，请重新创建通用映像，因为该映像处于不可部署状态且无法恢复。

## <a name="solution"></a>解决方案

> [!TIP]
> 如果你有 VM 的最新备份，则可以尝试 [从备份还原 vm](../../backup/backup-azure-arm-restore-vms.md) ，以解决启动问题。

要修复此问题，请按照[有关准备/捕获映像的 Azure 指南](../windows/upload-generalized-managed.md)操作，并准备新的通用映像。