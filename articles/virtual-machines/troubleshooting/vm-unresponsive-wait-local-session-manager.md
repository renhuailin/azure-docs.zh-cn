---
title: VM 在等待本地会话管理器服务时无响应
description: 本文提供了一些步骤，用于解决来宾操作系统在启动 Azure VM 时等待本地会话管理器完成处理的问题。
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
ms.openlocfilehash: 8af8d7695c48c6ac682109bb38935e98921fa9e4
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681901"
---
# <a name="vm-is-unresponsive-while-waiting-for-the-local-session-manager-service"></a>VM 在等待本地会话管理器服务时无响应

本文提供了一些步骤，用于解决在启动 Azure 虚拟机 (VM) 时，来宾操作系统 (来宾操作系统) 停滞等待本地会话管理器完成处理的问题。

## <a name="symptoms"></a>症状

使用 " [启动诊断](./boot-diagnostics.md) " 查看 VM 输出的屏幕截图时，你会看到屏幕截图显示 "请等待本地会话管理器" 消息的提示。

![显示 Windows Server 2012 R2 中停滞来宾操作系统的屏幕截图，并显示 "请等待本地会话管理器" 消息。](media/vm-unresponsive-wait-local-session-manager/vm-unresponsive-wait-local-session-manager-1.png)

## <a name="cause"></a>原因

可能有多个原因导致 VM 停滞在等待本地会话管理器。 如果此问题仍然存在，则需要收集内存转储以进行分析。

## <a name="solution"></a>解决方案

在某些情况下，只需等待进程完成即可解决问题。 如果 VM 未响应并在一小时内保持在等待屏幕上，则应收集内存转储，然后联系 Microsoft 支持部门。

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
> [排查 Azure 虚拟机启动错误](boot-error-troubleshoot.md)