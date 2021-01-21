---
title: VM 在等待本地会话管理器服务时无响应
description: 本文提供了一些步骤，用于解决来宾 OS 在启动 Azure VM 的过程中等待本地会话管理器完成处理时停滞的问题。
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/22/2020
ms.author: v-mibufo
ms.openlocfilehash: fc3bd5d2590e969db07e9dffa61b4902ea4604c3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632540"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>VM 在等待本地会话管理器服务时无响应

本文提供了一些步骤，用于解决来宾操作系统（来宾 OS）在启动 Azure 虚拟机 (VM) 的过程中等待本地会话管理器完成处理时停滞的问题。

## <a name="symptoms"></a>症状

使用[启动诊断](./boot-diagnostics.md)查看 VM 的输出的屏幕截图时，会看到屏幕截图显示一个提示，其消息为“请等待本地会话管理器”。

![屏幕截图显示了 Windows Server 2012 R2 中停滞的来宾 OS，其中显示了“请等待本地会话管理器”消息。](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>原因

有多个原因可能会导致 VM 在等待本地会话管理器时停滞。 如果此问题一直存在，你需要收集内存转储来进行分析。

## <a name="solution"></a>解决方案

> [!TIP]
> 如果你有 VM 的最新备份，则可以尝试 [从备份还原 vm](../../backup/backup-azure-arm-restore-vms.md) ，以解决启动问题。

在某些情况下，只需等待进程完成即可解决问题。 如果 VM 未响应并在一小时内保持在等待屏幕上，则应收集内存转储，然后联系 Microsoft 支持部门。

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
> [对 Azure 虚拟机启动错误进行故障排除](boot-error-troubleshoot.md)