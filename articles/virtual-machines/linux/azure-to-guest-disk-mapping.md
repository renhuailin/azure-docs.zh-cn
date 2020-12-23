---
title: 如何将 Azure 磁盘映射到 Linux VM 来宾磁盘
description: 如何确定是 Linux VM 的来宾磁盘的 Azure 磁盘。
author: timbasham
ms.service: virtual-machines-linux
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2020
ms.author: tibasham
ms.openlocfilehash: 4f0e48bf1c14728c54d4e89f30700017b0420d7d
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523431"
---
# <a name="how-to-map-azure-disks-to-linux-vm-guest-disks"></a>如何将 Azure 磁盘映射到 Linux VM 来宾磁盘

可能需要确定备份 VM 的来宾磁盘的 Azure 磁盘。 在某些情况下，可以将磁盘或卷大小与附加的 Azure 磁盘的大小进行比较。 如果有多个大小相同的 Azure 磁盘附加到 VM，则需要使用数据磁盘的逻辑单元号 (LUN) 。 

## <a name="what-is-a-lun"></a>什么是 LUN？

LUN)  (逻辑单元号是用于标识特定存储设备的数字。 为每个存储设备分配一个唯一的数字标识符（从零开始）。 设备的完整路径由总线编号、目标 ID 号和逻辑单元号表示， (LUN) 。 

例如： ***总线号0，目标 ID 0，LUN 3** _

对于我们的练习，只需使用 LUN。

## <a name="finding-the-lun"></a>查找 LUN

下面列出了两种方法，用于在 Linux 中查找磁盘的 LUN。

### <a name="lsscsi"></a>lsscsi

1. 连接到 VM
1. `sudo lsscsi`

列出的第一列将包含 LUN，格式为 [主机：通道：目标： _ * LUN * *]。

### <a name="listing-block-devices"></a>列出块设备

1. 连接到 VM
1. `sudo ls -l /sys/block/*/device`

列出的最后一列将包含 LUN，格式为 [主机：通道：目标：**LUN**]

## <a name="finding-the-lun-for-the-azure-disks"></a>查找 Azure 磁盘的 LUN

你可以使用 Azure 门户，Azure CLI 找到 Azure 磁盘的 LUN。

### <a name="finding-an-azure-disks-lun-in-the-azure-portal"></a>在 Azure 门户中查找 Azure 磁盘的 LUN

1. 在 Azure 门户中，选择 "虚拟机" 以显示虚拟机的列表
1. 选择虚拟机
1. 选择 "磁盘"
1. 从附加的磁盘列表中选择一个数据磁盘。
1. 磁盘的 LUN 将显示在 "磁盘详细信息" 窗格中。 此处显示的 LUN 与使用 lsscsi 在来宾中查找的 Lun 相关联，或列出了块设备。

### <a name="finding-an-azure-disks-lun-using-azure-cli"></a>使用 Azure CLI 查找 Azure 磁盘的 LUN

```azurecli-interactive
az vm show -g myResourceGroup -n myVM --query "storageProfile.dataDisks"
```
