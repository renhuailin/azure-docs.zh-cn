---
title: 使用 Azure 门户更改 Azure 托管磁盘的性能
description: 了解如何使用 Azure 门户更改新的和现有托管磁盘的性能层。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: a868d5d236cf4c5a8d29f15490909c2f1a53546f
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "96016556"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>使用 Azure 门户更改性能层

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>入门

### <a name="new-disks"></a>新磁盘

以下步骤说明如何在首次创建磁盘时更改磁盘的性能层：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 导航到要为其创建新磁盘的虚拟机。
1. 选择新磁盘时，请首先选择所需的磁盘大小。
1. 选择大小后，选择其他性能层以更改其性能。
1. 选择 **"确定"** 以创建磁盘。

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="磁盘创建边栏选项卡的屏幕截图，将突出显示磁盘，并突出显示 &quot;性能层&quot; 下拉列表。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>现有磁盘

以下步骤概述了如何更改现有磁盘的性能层：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 导航到包含要更改的磁盘的 VM。
1. 解除分配 VM 或分离磁盘。
1. 选择磁盘
1. 选择 " **大小 + 性能**"。
1. 在 " **性能层** " 下拉列表中，选择与磁盘的当前基线不同的层。
1. 选择“调整大小”。

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="&quot;大小 + 性能&quot; 边栏选项卡的屏幕截图，其中突出显示了 &quot;性能层&quot;。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>后续步骤

如果需要调整磁盘大小以利用更高的性能层，请参阅以下文章：

- [使用 Azure CLI 扩展 Linux VM 上的虚拟硬盘](linux/expand-disks.md)
- [展开附加到 Windows 虚拟机的托管磁盘](windows/expand-os-disk.md)
