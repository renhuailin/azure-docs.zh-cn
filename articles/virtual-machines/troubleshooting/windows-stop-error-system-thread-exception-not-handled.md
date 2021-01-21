---
title: Windows 停止错误 - 0x0000007E 系统线程异常未处理
description: 本文提供解决以下问题的步骤：来宾操作系统遇到问题并希望重启 Azure VM。 消息将声明 "系统线程异常未处理"。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: beb658474e49f9b47900d8481dab0424dae8c5be
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661385"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows 停止错误 - 0x0000007E 系统线程异常未处理

本文提供解决以下问题的步骤：来宾操作系统（来宾 OS）遇到问题并尝试重启 Azure 虚拟机 (VM)。 显示的错误消息显示 "系统线程异常未处理"。

## <a name="symptoms"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 输出的屏幕截图时，你将看到 Windows 需要重启，显示“系统线程异常未处理”停止代码或“0x0000007E”错误代码。

![显示 Windows 在启动过程中停滞的屏幕截图，其中显示 "你的电脑遇到问题，需要重新启动。 我们将重新启动。 " 错误消息和 "未处理系统线程异常" 停止代码。](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>原因

在分析内存转储文件之前，无法确定原因。 继续收集内存转储文件。

## <a name="solution"></a>解决方案

> [!TIP]
> 如果你有 VM 的最新备份，则可以尝试 [从备份还原 vm](../../backup/backup-azure-arm-restore-vms.md) ，以解决启动问题。


若要解决此问题，首先需要收集崩溃的内存转储文件，然后将该文件发送给 Microsoft 支持部门。 如需收集转储文件，请遵循后面两个部分中的说明。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将 OS 磁盘附加到新的修复 VM

1. 若要准备一个修复 VM，请按照 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步骤 1-3 进行操作。
1. 使用远程桌面连接来连接到修复 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

1. 在修复 VM 上，转到附加的 OS 磁盘中的 Windows 文件夹。 例如，如果分配给附加的 OS 磁盘的驱动器号为 F，请转到 `F:\Windows`。
1. 查找 memory.dmp 文件，然后[提交附加该内存转储文件的支持工单](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
1. 如果在查找 memory.dmp 文件时遇到问题，请按照指南[使用不可屏蔽的中断 (NMI) 调用来生成故障转储文件](/windows/client-management/generate-kernel-or-complete-crash-dump)。

有关 NMI 调用的详细信息，请参阅 [Azure 串行控制台中的 NMI 调用](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 用户指南。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [对 Azure 虚拟机启动错误进行故障排除](./boot-error-troubleshoot.md)
