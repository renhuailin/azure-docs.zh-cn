---
title: 托管磁盘突发
description: 了解 Azure 磁盘和 Azure 虚拟机的磁盘突发。
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4024d2b1357f3dda8216e9ebdd2055b28b064d33
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677485"
---
# <a name="managed-disk-bursting"></a>托管磁盘突发
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

Azure [Premium ssd](disks-types.md#premium-ssd) 提供了两种突发模式：

- 按需突发模型 (预览) ，在这种情况下，每当磁盘需要超过其当前容量时，磁盘就会出现突发情况。 当磁盘出现猝发时，此模型会产生额外的费用。 Noncredit 突发仅适用于大于 512 GiB 的磁盘。
- 基于信用额度的模型，如果磁盘在其信用桶中累积了猝发信用额度，则磁盘将突发。 当磁盘出现突发时，此模型不会产生额外的费用。 基于信用额度的突发仅适用于磁盘 512 GiB 和更小。

此外， [可以更改托管磁盘的性能层](disks-change-performance.md)，这可能是理想情况下，如果工作负荷将在突发中运行。

|  |基于信用的突发  |按需突发  |更改性能层  |
|---------|---------|---------|---------|
| 方案|适用于短期缩放 (30 分钟或更少) 。|适用于短期缩放 (不受时间限制的) 。|如果你的工作负荷将继续在突发中运行，则这是理想之选。|
|成本     |免费         |成本是可变的，有关详细信息，请参阅 [计费](#billing) 部分。        |每个性能层的成本是固定的，有关详细信息，请参阅 [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/) 。         |
|可用性     |仅适用于高级 Ssd 512 GiB 和更小。         |仅适用于大于 512 GiB 的高级 Ssd。         |适用于所有高级 SSD 大小。         |
|支持     |默认情况下，在合格磁盘上启用。         |必须由用户启用。         |用户必须手动更改其层级。         |

## <a name="common-scenarios"></a>常见场景
以下需求场景可显著受益于突发：
- **缩短启动时间**  –使用突发后，实例将以明显更快的速度启动。 例如，启用高级层的 VM 的默认 OS 磁盘是 P4 磁盘，其预配性能最高可达 120 IOPS 和 25 MB/s。 使用突发后，P4 最多可达 3500 IOPS 和 170 MB/秒，允许启动，最多可加速6倍。
- **处理批处理作业** –某些应用程序工作负载本质上是循环的。 它们在大多数时间都需要基线性能，在短时间内需要更高的性能。 这种情况的一个示例是一个会计程序，该程序处理需要少量磁盘流量的日常事务。 本月结束时，此计划将完成需要大量磁盘流量的协调报表。
- **流量高峰** – Web 服务器及其应用程序随时可以遇到流量电涌。 如果你的 web 服务器受使用突发的 Vm 或磁盘的支持，则服务器将更好地处理流量高峰。 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>后续步骤

若要启用按需突发，请参阅 [启用按需突发](disks-enable-bursting.md)。
若要了解如何深入了解突发资源，请参阅 [磁盘突发指标](disks-metrics.md)。
