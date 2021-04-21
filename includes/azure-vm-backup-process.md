---
author: v-amallick
ms.service: Azure Backup
ms.topic: include
ms.date: 04/16/2021
ms.author: v-amallick
ms.openlocfilehash: e7af0819a94661a1008d96b7d0738c30a4a80c18
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/19/2021
ms.locfileid: "107716733"
---
1. 对于选择进行备份的 Azure VM，Azure 备份服务将根据指定的备份计划启动备份作业。
1. 首次备份期间，如果 VM 已运行，则会在 VM 上安装备份扩展。
    - 对于 Windows VM，将安装 [VMSnapshot 扩展](../articles/virtual-machines/extensions/vmsnapshot-windows.md)。
    - 对于 Linux VM，将安装 [VMSnapshotLinux 扩展](../articles/virtual-machines/extensions/vmsnapshot-linux.md)。
1. 对于正在运行的 Windows VM，备份服务将与卷影复制服务 (VSS) 互相配合，来创建 VM 的应用一致性快照。
    - 备份服务默认创建完整的 VSS 备份。
    - 如果备份服务无法创建应用一致性快照，则会创建基础存储的文件一致性快照（因为当 VM 停止时，不会发生应用程序写入）。
1. 对于 Linux VM，Azure 备份将创建文件一致性备份。 对于应用一致性快照，需要手动自定义前脚本/后脚本。
1. 备份服务创建快照后，会将数据传输到保管库。
    - 可以通过并行备份每个 VM 磁盘来优化备份。
    - 对于每个要备份的磁盘，Azure 备份将读取磁盘上的块，识别并只传输自上次备份以来已发生更改的数据块（增量传输）。
    - 快照数据可能不会立即复制到保管库。 在高峰期，可能需要好几个小时才能完成复制。 每日备份策略规定的 VM 备份总时间不会超过 24 小时。
1. 在 Windows VM 上启用 Azure 备份后，对 VM 所做的更改包括：
    - 在 VM 中安装 Microsoft Visual C++ 2013 Redistributable(x64) - 12.0.40660
    - 将卷影复制服务 (VSS) 的启动类型从手动更改为自动
    - 添加 IaaSVmProvider Windows 服务

1. 数据传输完成后，会删除快照并创建恢复点。

![Azure 虚拟机备份体系结构](../articles/backup/media/backup-azure-vms-introduction/vmbackup-architecture.png)
