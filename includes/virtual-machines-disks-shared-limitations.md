---
title: include 文件
description: include 文件
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/11/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 19b9cfc6ad6467b2779abb3561899fd3bd8d037e
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112042011"
---
仅可对部分磁盘类型启用共享磁盘。 目前只有超级磁盘和高级 SSD 可以启用共享磁盘。 已启用共享磁盘的每个托管磁盘都受到以下限制（按磁盘类型组织）：

### <a name="ultra-disks"></a>超级磁盘

超级磁盘有自己单独的与共享磁盘无关的限制列表。 有关超级磁盘限制，请参阅[使用 Azure 超级磁盘](../articles/virtual-machines/disks-enable-ultra-ssd.md)。

共享超级磁盘时，它们有以下附加限制：

- 当前仅限于 Azure 资源管理器或 SDK 支持。 
- 只可对 Windows Server 故障转移群集的某些版本使用基本磁盘；有关详细信息，请参阅[故障转移群集硬盘要求和存储选项](/windows-server/failover-clustering/clustering-requirements)。
- 仅支持[服务器端加密](../articles/virtual-machines/disk-encryption.md)，目前不支持 [Azure 磁盘加密](../articles/virtual-machines/windows/disk-encryption-overview.md)。

默认情况下，共享的超级磁盘在所有支持超级磁盘的区域中都可以使用，并且无需注册即可使用。

### <a name="premium-ssds"></a>高级 SSD

- 当前仅限于 Azure 资源管理器或 SDK 支持。 
- 仅可对数据磁盘启用，不可对 OS 磁盘启用。
- ReadOnly 主机缓存不适用于采用 `maxShares>1` 的高级 SSD。
- 磁盘突发不适用于采用 `maxShares>1` 的高级 SSD。
- 通过 Azure 共享磁盘将可用性集与虚拟机规模集一起使用时，不会对共享数据磁盘强制实施与虚拟机容错域的[存储容错域对齐](../articles/virtual-machines/availability.md)。
- 使用 [邻近放置组 (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md) 时，共享一个磁盘的所有虚拟机都必须属于同一个 PPG。
- 只可对 Windows Server 故障转移群集的某些版本使用基本磁盘；有关详细信息，请参阅[故障转移群集硬盘要求和存储选项](/windows-server/failover-clustering/clustering-requirements)。
- Azure Site Recovery 支持尚不可用。
- Azure 备份通过 [Azure 磁盘备份](../articles/backup/disk-backup-overview.md)提供。
- 仅支持[服务器端加密](../articles/virtual-machines/disk-encryption.md)，目前不支持 [Azure 磁盘加密](../articles/virtual-machines/windows/disk-encryption-overview.md)。

#### <a name="regional-availability"></a>区域可用性

可在提供托管磁盘的所有区域中使用共享高级 SSD。
