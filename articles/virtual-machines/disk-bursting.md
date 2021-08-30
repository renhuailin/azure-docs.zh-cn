---
title: 托管磁盘突发
description: 了解 Azure 磁盘和 Azure 虚拟机的磁盘突发。
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6af3d66afe107bae470f4081407f2293fe04f0fb
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356936"
---
# <a name="managed-disk-bursting"></a>托管磁盘突发

Azure 提供了提升磁盘存储 IOPS 和 MB/s 性能的功能，这称为虚拟机 (VM) 和磁盘突发。 可以有效地使用 VM 和磁盘突发，以在 VM 和磁盘上实现更好的突发性能。

Azure Vm 和磁盘资源的突发不会相互依赖。 你不需要使用具有突发功能的 VM 来为与该 VM 相连接且同样具有突发功能的磁盘进行突发。 同样，不需要使用与具有突发功能的 VM 相连接且同样具有突发功能的磁盘为该 VM 进行突发。

## <a name="common-scenarios"></a>常见场景
以下需求场景可显著受益于突发：
- **缩短启动时间** – 使用突发后，实例将以更快的速度启动。 例如，启用高级层的 VM 的默认 OS 磁盘是 P4 磁盘，其预配性能最高可达 120 IOPS 和 25 MB/s。 在使用突发的情况下，P4 的性能最高可达 3500 IOPS 和 170 MB/s，可使启动最多加快 6 倍。
- **处理批处理作业** – 某些应用程序工作负载本质上是循环的。 它们在大多数时间都需要基线性能，在短时间内需要更高的性能。 这种情况的一个示例是一个会计程序，该程序处理需要少量磁盘流量的每日事务。 在月末，该程序将完成对帐报表，而这需要大量磁盘流量。
- **流量高峰** - Web 服务器及其应用程序随时都可能遇到流量激增。 如果 Web 服务器由使用突发的 VM 或磁盘提供支持，那么这些服务器便可以更好地处理流量高峰。 

## <a name="disk-level-bursting"></a>磁盘级突发

目前，有两种会突发的托管磁盘类型，即[高级 SSD](disks-types.md#premium-ssd) 和[标准 SSD](disks-types.md#standard-ssd)。 其他磁盘类型当前无法突发。 磁盘有两种突发模式：

- 按需突发模式（预览版），在该模式下，磁盘在其需求超出其当前容量时突发。 只要磁盘出现突发时，此模型就会产生额外的费用。 按需突发仅适用于大于 512 GiB 的高级 SSD。
- 基于额度的模型，在该模型下，仅当磁盘在其额度桶中累积了突发额度时，磁盘才会突发。 当磁盘出现突发时，此模型不会产生额外的费用。 基于额度的突发仅适用于 512 GiB 和更小的高级和标准 SSD。

Azure [高级 SSD](disks-types.md#premium-ssd) 可以使用任一突发模式，但[标准 SSD](disks-types.md#standard-ssd) 目前仅提供基于额度的突发。

此外，[可以更改托管磁盘的性能层](disks-change-performance.md)，如果工作负载将在突发模式运行，这可能是理想情况。

|  |基于额度的突发  |按需突发  |更改性能层级  |
|---------|---------|---------|---------|
| **方案**|适用于短期缩放（30 分钟或更少）。|适用于短期缩放（不受时间限制）。|如果你的工作负载将继续突发运行，则这是理想之选。|
|**成本**     |免费         |成本是可变的，有关详细信息，请参阅[计费](#billing)部分。        |每个性能层的成本是固定的，有关详细信息，请参阅[托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)。         |
|**可用性**     |仅适用于高级和标准 SSD 512 GiB 和更小的磁盘。         |仅适用于大于 512 GiB 的高级 SSD。         |适用于所有高级 SSD 大小。         |
|**启用**     |默认情况下，在合格磁盘上启用。         |必须由用户启用。         |用户必须手动更改其层级。         |

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>后续步骤

- 若要启用按需突发，请参阅[启用按需突发](disks-enable-bursting.md)。
- 如需了解如何深入了解突发资源，请参阅“[磁盘突发指标](disks-metrics.md)”。
- 若要准确了解每个适用磁盘大小可突发的数量，请参阅 [VM 磁盘的可伸缩性和性能目标](disks-scalability-targets.md)。