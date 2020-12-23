---
title: Windows stop 错误-未处理0x0000007E 系统线程异常
description: 本文提供了解决以下问题的步骤：来宾 OS 遇到问题并希望重启 Azure VM。 消息将声明 "系统线程异常未处理"。
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
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681884"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows stop 错误-未处理0x0000007E 系统线程异常

本文提供了解决以下问题的步骤：来宾操作系统 (来宾操作系统) 遇到问题并尝试重新启动 Azure 虚拟机 (VM) 。 显示的错误消息显示 "系统线程异常未处理"。

## <a name="symptoms"></a>症状

当你使用 [启动诊断](./boot-diagnostics.md) 查看 VM 输出的屏幕截图时，你将看到 Windows 需要使用 "未处理系统线程异常" 停止代码或 "0x0000007e" 错误代码来重新启动。

![显示 Windows 在启动过程中停滞的屏幕截图，其中显示 "你的电脑遇到问题，需要重新启动。 我们将重新启动。 " 错误消息和 "未处理系统线程异常" 停止代码。](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>原因

在分析内存转储文件之前无法确定原因。 继续收集内存转储文件。

## <a name="solution"></a>解决方案

若要解决此问题，首先需要收集崩溃的内存转储文件，然后将该文件发送给 Microsoft 支持部门。 若要收集转储文件，请按照下面两个部分中的说明进行操作。

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>将 OS 磁盘附加到新的修复 VM

1. 若要准备修复 VM，请遵循 [VM 修复命令](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)的步骤1-3。
1. 使用远程桌面连接连接到修复 VM。

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>找到转储文件并提交支持票证

1. 在修复 VM 上，中转到附加 OS 磁盘上的 Windows 文件夹。 例如，如果分配给附加 OS 磁盘的驱动器号标记为 " *F*"，则请参阅 `F:\Windows` 。
1. 查找 *内存 dmp* 文件，然后提交包含附加的内存转储文件的 [支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。
1. 如果在查找 *内存 dmp* 文件时遇到问题，请按照指南 [使用不可屏蔽中断生成故障转储文件 (NMI) 调用](/windows/client-management/generate-kernel-or-complete-crash-dump)。

有关 NMI 调用的详细信息，请参阅 [Azure 串行控制台中的 NMI 调用](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) 用户指南。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [排查 Azure 虚拟机启动错误](./boot-error-troubleshoot.md)
