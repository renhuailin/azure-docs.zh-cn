---
title: Linux 虚拟机上的完整操作系统磁盘问题
description: 如何解决 Linux 虚拟机上的完整操作系统磁盘问题
author: v-miegge
ms.author: tibasham
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 11/20/2020
ms.openlocfilehash: 2e350fe297b2aaf89ac302baaefb29092cfe2532
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523411"
---
# <a name="issues-with-a-full-os-disk-on-a-linux-virtual-machine"></a>Linux 虚拟机上的完整操作系统磁盘问题

当 Linux 虚拟机的 OS 磁盘)  (VM 已满时，这可能会导致 VM 的正确操作出现问题。

## <a name="symptom"></a>症状

尝试创建新文件时，会收到以下消息：

```
username@AZUbuntu1404:~$ touch new 
touch: cannot touch “new”: No space left on device 
username@AZUbuntu1404:~$
```

然后，多个守护程序指示它们无法在启动会话过程中创建临时文件。

```
ERROR:IOError: [Errno 28] No space left on device: '/var/lib/waagent/events/1474306860983232.tmp' 
    
OSError: [Errno 28] No space left on device: '/var/lib/cloud/data/tmpDZCq0g'
```
    
## <a name="cause"></a>原因

出现此错误消息的原因有以下几个：

1. 磁盘可能已满。
1. 文件系统可能已耗尽 Inode。
1. 数据磁盘可能会装载到现有目录上，导致文件被隐藏。
1. 由进程打开然后删除的文件。

## <a name="solution"></a>解决方案

### <a name="process-overview"></a>过程概述

1. 创建和访问修复 VM。
1. 释放磁盘上的空间。
1. 重新生成 VM。

> [!NOTE]
> 遇到此错误时，来宾操作系统无法正常运行。 在脱机模式下进行故障排除来解决此问题。

### <a name="create-and-access-a-repair-vm"></a>创建和访问修复 VM

1. 使用 [VM 修复命令](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md) 的步骤 1-3 准备一个修复 VM。
1. 使用 SSH 连接到修复 VM。

### <a name="free-up-space-on-the-disk"></a>释放磁盘上的空间

若要解决此问题：

- 如果磁盘尚未达到最大大小 1 TB，请将其大小调整为 1 TB。
- 释放磁盘空间。

1. 检查磁盘是否已满。 如果磁盘大小低于 1 TB，则 [使用 Azure CLI](../linux/expand-disks.md)将其扩展到最大为 1 tb。
1. 如果磁盘已是 1 TB，则需释放磁盘空间。
1. 调整大小并完成清理后，将继续重新生成 VM。

### <a name="rebuild-the-vm"></a>重新生成 VM

使用 [VM 修复命令的步骤 5](./repair-linux-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) 重新生成 VM。
