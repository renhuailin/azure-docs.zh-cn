---
title: 使用 Azure 门户更改 Azure 托管磁盘的性能
description: 了解如何使用 Azure 门户更改新的和现有托管磁盘的性能层。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 37069bb17e0ce6a104ae3c1b79714da160737fe8
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356268"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>使用 Azure 门户更改性能层

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>入门

### <a name="new-disks"></a>新磁盘

以下步骤介绍了如何在首次创建磁盘时更改磁盘的性能层：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 导航到要为其创建新磁盘的 VM。
1. 选择新磁盘时，请首先选择所需的磁盘的大小。
1. 选择大小后，请选择其他性能层以更改其性能。
1. 选择“确定”以创建磁盘。

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="磁盘创建边栏选项卡的屏幕截图，其中突出显示了一个磁盘，并突出显示了性能层下拉列表。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>现有磁盘

以下步骤概述了如何更改现有磁盘的性能层：

1. 登录 [Azure 门户](https://portal.azure.com/)。
1. 导航到包含要更改的磁盘的 VM。
1. 解除分配 VM 或分离磁盘。
1. 选择磁盘
1. 选择“大小 + 性能”。
1. 在“性能层”下拉列表中，选择一个与磁盘的当前性能层不同的层。
1. 选择“调整大小”。

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="“大小 + 性能”边栏选项卡的屏幕截图，其中突出显示了性能层。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

### <a name="change-the-performance-tier-of-a-disk-without-downtime-preview"></a>在不停机的情况下更改磁盘的性能层（预览版）

你还可以在不停机的情况下更改性能层，因此无需释放 VM 或分离磁盘来更改层。

### <a name="prerequisites"></a>先决条件

磁盘必须满足[在不停机的情况下更改性能层（预览版）](#change-performance-tier-without-downtime-preview)部分中规定的要求，如果不满足，则更改性能层会造成停机。

必须先为订阅启用该功能，然后才能在不停机的情况下更改磁盘的性能层。 请按照以下步骤为订阅启用此功能：

1.  执行以下命令，为订阅注册此功能

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  在试用该功能之前，请使用以下命令确认注册状态是否为“已注册”（可能需要几分钟）。

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

### <a name="change-performance-tier"></a>更改性能层

现在，该功能已注册，可以在不停机的情况下更改适用磁盘的性能层。

1. 从以下链接登录到 Azure 门户：[https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview)。
1. 导航到包含要更改的磁盘的 VM。
1. 解除分配 VM 或分离磁盘。
1. 选择磁盘
1. 选择“大小 + 性能”。
1. 在“性能层”下拉列表中，选择一个与磁盘的当前性能层不同的层。
1. 选择“调整大小”。

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="“大小 + 性能”边栏选项卡的屏幕截图，其中突出显示了性能层。" lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>后续步骤

如果需要调整磁盘大小以利用更高的性能层，请参阅以下文章：

- [使用 Azure CLI 扩展 Linux VM 上的虚拟硬盘](linux/expand-disks.md)
- [展开附加到 Windows 虚拟机的托管磁盘](windows/expand-os-disk.md)
