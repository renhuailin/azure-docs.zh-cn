---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4a61399bbb75639fdf4f03da7b77f22a2136adbe
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628894"
---
仅可对部分磁盘类型启用共享磁盘。 目前只有超磁盘和高级 Ssd 可以启用共享磁盘。 已启用共享磁盘的每个托管磁盘受到下列限制，按磁盘类型进行组织：

### <a name="ultra-disks"></a>超级磁盘

超磁盘有各自独立的限制列表，与共享磁盘无关。 有关 ultra 磁盘限制，请参阅 [使用 Azure 超磁盘](../articles/virtual-machines/disks-enable-ultra-ssd.md)。

共享超磁盘时，它们具有以下附加限制：

- 当前仅限于 Azure 资源管理器或 SDK 支持。 
- 只可对 Windows Server 故障转移群集的某些版本使用基本磁盘；有关详细信息，请参阅[故障转移群集硬盘要求和存储选项](/windows-server/failover-clustering/clustering-requirements)。

共享的 ultra 磁盘在默认情况下支持的所有区域均提供，无需注册访问权限即可使用这些磁盘。

### <a name="premium-ssds"></a>高级 SSD

- 当前仅限于 Azure 资源管理器或 SDK 支持。 
- 仅可对数据磁盘启用，不可对 OS 磁盘启用。
- ReadOnly 主机缓存不适用于采用 `maxShares>1` 的高级 SSD。
- 磁盘突发不适用于采用 `maxShares>1` 的高级 SSD。
- 通过 Azure 共享磁盘将可用性集与虚拟机规模集一起使用时，不会对共享数据磁盘强制实施与虚拟机容错域的[存储容错域对齐](../articles/virtual-machines/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)。
- 使用 [邻近放置组 (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md) 时，共享一个磁盘的所有虚拟机都必须属于同一个 PPG。
- 只可对 Windows Server 故障转移群集的某些版本使用基本磁盘；有关详细信息，请参阅[故障转移群集硬盘要求和存储选项](/windows-server/failover-clustering/clustering-requirements)。
- 尚无 Azure Site Recovery 支持。
- Azure 备份通过 [Azure 磁盘备份 (预览) ](../articles/backup/disk-backup-overview.md)提供。

#### <a name="regional-availability"></a>区域可用性

可在提供托管磁盘的所有区域中使用共享高级 SSD。