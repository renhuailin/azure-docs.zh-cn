---
title: 为 Azure 上的 SAP HANA（大型实例）类型 II SKU 执行操作系统备份和还原 | Microsoft Docs
description: 为 Azure 上的 SAP HANA（大型实例）类型 II SKU 执行操作系统备份和还原
services: virtual-machines-linux
documentationcenter: ''
author: jaawasth
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: jaawasth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 245c371a002ebfdad4dd0765ba3d283c81c86f03
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110631551"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>修订版 3 标记的类型 II SKU 的操作系统备份和还原

本文档介绍如何为修订版 3 的 HANA 大型实例的“类型 II SKU”执行操作系统文件级备份和还原。 

>[!Important]
> 本文不适用于修订版 4 HANA 大型实例标记中的类型 II SKU 部署。 在修订版 4 HANA 大型实例标记中部署的类型 II HANA 大型实例单元的引导 LUN 可以使用存储快照进行备份，因为修订版 3 标记中的类型 I SKU 也是这样


>[!NOTE]
> * OS 备份脚本使用 xfsdump 实用程序。  
> * 本文档支持完整的根文件系统备份，不支持增量备份。
> * 在创建备份时，请确保没有文件写入同一系统。  否则，在备份过程中写入的文件可能不会包含在备份中。
> * 为修订版 3 的 HANA 大型实例的类型 II SKU 弃用 ReaR 备份。
> * 我们针对多种 OS 损坏情况在内部对此过程进行了测试。 但是，由于作为客户的你只负责 OS，所以建议你在为你的情况依赖于此文档之前进行全面测试。
> * 我们已在 SLES OS 上测试了此过程。
> * 不支持主版本升级（例如 SLES 12.x 升级到 SLES 15x）。
> * 若要使用此过程完成 OS 还原，你将需要 Microsoft 协助，因为恢复需要控制台访问权限。 请向 Microsoft 创建支持票证来获得恢复协助。


## <a name="how-to-take-a-manual-backup"></a>如何执行手动备份？

执行手动备份：

1. 安装备份工具。
   ```
   zypper in xfsdump
   ```

2. 创建完整备份。 
   ```
   xfsdump -l 0 -f /data1/xfs_dump /
   ```

   以下屏幕演示了示例手动备份：
   
    [![转储捕获](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png)](media/HowToHLI/OSBackupTypeIISKUs/dump-capture.png#lightbox)


3. 重要说明：在 data1 分区也损坏的情况下，还应在 NFS 卷中保存备份的副本。
   ```
   cp /data1/xfs_dump /osbackup/
   ```

4. 若要从转储中排除常规目录和文件，请用 chattr 标记文件。
   * chattr -R +d 目录
   * chattr +d 文件
   * 用“-e”选项运行 xfsdump
   * 请注意，不能排除 nfs 文件系统 [ntfs]




## <a name="how-to-restore-a-backup"></a>如何还原备份？

>[!NOTE]
> * 此步骤需要 Microsoft 操作团队的参与。
> * 若要使用此过程完成 OS 还原，需要 Microsoft 协助，因为恢复需要控制台访问权限。 请向 Microsoft 创建支持票证来获得恢复协助。
> * 我们将还原完整的 filesystem：

1. 在系统上装载 OS iso。

2. 进入修复模式。

3. 在读/写模式下装载 data1（或 nfs 卷，无论转储存储在哪个位置）分区。
   ```
   mount -o rw /dev/md126p4 /mnt1
   ```
4. 在读/写模式下装载根。
   ```
   mount -o rw /dev/md126p2 /mnt2
   ```
5. 还原 Filesystem。
   ```
   xfsrestore -f /mnt1/xfs_dump /mnt2
   ```
   [![还原屏幕快照](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png)](media/HowToHLI/OSBackupTypeIISKUs/restore-screenshot.png#lightbox)
6. 重新启动系统。
   ```
   reboot
   ```

如果任何发布检查失败，请与 OS 供应商和 Microsoft 联系以获得控制台访问权限。

## <a name="post-restore-check"></a>还原后的检查

1. 确保系统还原了完整的属性。
   * 网络正在运行。
   * NFS 卷已装入。
2. 确保已配置 RAID；请替换为你的 RAID 设备。
   ```
   mdadm -D /dev/md126
   ```
   [![raid 状态](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png)](media/HowToHLI/OSBackupTypeIISKUs/raid-status.png#lightbox)

3. 确保 RAID 磁盘已同步并且配置处于干净状态。
   * 在同步期间，RAID 磁盘需要一些时间；同步可能会持续数分钟才会 100% 同步。

4. 启动 HANA DB 并验证 HANA 是否按预期运行。

5. 确保 HANA 出现并且没有错误。
   ```
   hdbinfo
   ```
   [![hana 状态](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png)](media/HowToHLI/OSBackupTypeIISKUs/hana-status.png#lightbox)

6. 如果任何发布检查失败，请与 OS 供应商和 Microsoft 联系以获得控制台访问权限。