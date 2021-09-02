---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 06/02/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 0ae0fa18c2d3d223435656d871bd0d5be00627e4
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2021
ms.locfileid: "113356935"
---
### <a name="on-demand-bursting-preview"></a>按需突发（预览版）

使用按需磁盘突发模式的高级 SSD 的突发需求可能会超出初始预配目标，这种情况的频率取决于其工作负荷的需要，并可能达到其最大突发目标。 例如，在 1-TiB P30 磁盘上，预配的 IOPS 为 5000 IOPS。 在此磁盘上启用磁盘突发后，工作负荷向此磁盘发出 IO 时，突发性能最高可达 30000 IOPS 和 1000 MBps。

如果预期工作负荷运行时所需的性能会频繁超出预配的性能目标，磁盘突发的成本效益会比较低。 在这种情况下，建议将磁盘的性能层改为[较高层](../articles/virtual-machines/disks-performance-tiers.md)以获得更好的基线性能。 查看计费详细信息，并根据工作负荷的流量模式进行评估。

启用按需突发之前，请先了解以下内容：

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>区域可用性

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>计费

使用按需突发模型的高级 SSD 是按固定的每小时突发启用费用来计费的，并对超出预配目标的任何突发事务收取事务费。 将使用即用即付模型根据非缓存磁盘 IO（包括超过预配目标的读取和写入）收取事务费。 下面是一个计费小时内的磁盘流量模式示例：

磁盘配置：高级 SSD - 1 TiB (P30)，已启用磁盘突发。

- 00:00:00 - 00:10:00 磁盘 IOPS 低于 5,000 IOPS 的预配目标 
- 00:10:01 - 00:10:10 应用程序发出了一个批处理作业，导致磁盘 IOPS 以 6,000 IOPS 突发了 10 秒 
- 00:10:11 – 00:59:00 磁盘 IOPS 低于 5,000 IOPS 的预配目标 
- 00:59:01 – 01:00:00 应用程序发出了另一个批处理作业，导致磁盘 IOPS 以 7,000 IOPS 突发了 60 秒 

在此计费小时内，突发成本包括两项费用：

第一项费用是 $X 的固定突发启用费用（取决于所在区域）。 将始终对磁盘收取此固定费用，无论其附加状态如何，直到磁盘被禁用为止。 

第二项费用是突发事务成本。 磁盘突发发生在两个时间段。 00:10:01 - 00:10:10，累积突发事务数为 (6,000 - 5,000) X 10 = 10,000。 00:59:01 - 01:00:00，累积突发事务数为 (7,000 - 5,000) X 60 = 120,000。 突发事务总数为 10,000 + 120,000 = 130,000。 突发事务的费用为 $Y，该费用根据 10,000 个事务费用（基于区域定价）的 13 倍计算得出。

这样，此计费小时的磁盘突发总成本就等于 $X + $Y。 采用 MBps 级预配目标的突发也适用相同的计算。 我们将超额的 MB 部分转换为 IO 大小为 256KB 的事务。 如果磁盘流量同时超过了预配的 IOPS 和 MBps 目标，可以参考以下示例来计算突发事务。 

磁盘配置：高级 SSD - 1 TB (P30)，已启用磁盘突发。

- 00:00:01 - 00:00:05 应用程序发出了一个批处理作业，导致磁盘 IOPS 达到 10,000 IOPS 和 300 MBps，并突发了五秒。
- 00:00:06 - 00:00:10 应用程序发出了一个恢复作业，导致磁盘 IOPS 达到 6,000 IOPS 和 600 MBps，并突发了五秒。

突发事务按 IOPS 和 MBps 突发中的最大事务数来计算。 从 00:00:01 到 00:00:05，累积突发事务数最大为 ((10,000 - 5,000), (300 - 200) * 1024 / 256)) * 5 = 25,000 个事务。 从 00:00:06 到 00:00:10，累积突发事务数最大为 ((6,000 - 5,000), (600 - 200) * 1024 / 256)) * 5 = 8,000 个事务。 再加上固定的突发启用费用，便得到启用按需磁盘突发的总成本。 

有关定价的详细信息，请参阅[托管磁盘定价页](https://azure.microsoft.com/pricing/details/managed-disks/)，并使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=storage)对工作负荷进行评估。 


若要启用按需突发，请参阅[启用按需突发](../articles/virtual-machines/disks-enable-bursting.md)。

### <a name="credit-based-bursting"></a>基于额度的突发

对于高级 SSD，基于额度的突发适用于大小为 P20 和更小的磁盘。 对于标准 SSD，基于额度的突发适用于大小为 E30 和更小的磁盘。 对于标准和高级 SSD，基于额度的突发在 Azure 公有云、政府云和中国云的所有区域都可用。 默认情况下，在大小受支持的磁盘的所有新的和现有部署中均启用磁盘突发。 VM 级突发只使用基于额度的突发。

## <a name="virtual-machine-level-bursting"></a>虚拟机级突发

VM 级突发只使用基于额度的突发模型，默认情况下，将为所有支持该模型的 VM 启用该模型。

在 Azure 公有云的所有区域中，已为以下大小的 VM 启用 VM 级突发： 
- [Dsv4 系列](../articles/virtual-machines/dv4-dsv4-series.md)
- [Dasv4 系列](../articles/virtual-machines/dav4-dasv4-series.md)
- [Ddsv4 系列](../articles/virtual-machines/ddv4-ddsv4-series.md)
- [Esv4 系列](../articles/virtual-machines/ev4-esv4-series.md)
- [Easv4 系列](../articles/virtual-machines/eav4-easv4-series.md)
- [Edsv4 系列](../articles/virtual-machines/edv4-edsv4-series.md)
- [B 系列](../articles/virtual-machines/sizes-b-series-burstable.md)
- [Fsv2 系列](../articles/virtual-machines/fsv2-series.md)
- [Dsv3 系列](../articles/virtual-machines/dv3-dsv3-series.md)
- [Esv3 系列](../articles/virtual-machines/ev3-esv3-series.md)
- [Lsv2 系列](../articles/virtual-machines/lsv2-series.md)

## <a name="bursting-flow"></a>突发流

将同时在 VM 级别和磁盘级别以相同方式应用突发额度系统。 你的资源（VM 或磁盘）最初在自己的突发桶中将具有满额的额度。 这些额度可支持在最大突发速率下维持最多 30 分钟的突发。 每当资源的 IOPS 或 MB/s 的利用率低于资源的性能目标时，就会积累额度。 如果资源积累了突发额度，当工作负荷需要额外的性能时，资源可以使用这些额度提高性能并超出性能限制，以满足工作负荷需求。

![突发桶关系图。](media/managed-disks-bursting/bucket-diagram.jpg)

如何支出可用额度由你自己决定。 可以连续使用突发额度 30 分钟，也可以在一天内分散地使用。 部署资源时，它们会得到充分的额度分配。 当额度耗尽时，只需不到一天的时间即可补足。 可由你来决定支出的额度，突发桶无需满额也可为资源支持突发。 突发的累积因资源而异，因为累积基于未达到性能目标时未使用的 IOPS 和 MB/s。 与基线性能较低的资源相比，基线性能更高的资源可以更快地积累其突发额度。 例如，处于空闲状态的 P1 磁盘每秒可积累 120 IOPS，而处于空闲状态的 P20 磁盘每秒可积累 2,300 IOPS。

## <a name="bursting-states"></a>突发状态
启用了突发功能时，资源可能处于以下三种状态之一：
- **正在累积** - 资源的 IO 流量的性能耗用低于性能目标。 为 IOPS 和 MB/s 累积额度是彼此分开执行的。 你的资源可能会积累 IOPS 额度并支出 MB/s 额度，也可能会支出 IOPS 额度并积累 MB/s 额度。
- **正在突发** - 资源流量的性能耗用高于性能目标。 突发流量将独立消耗 IOPS 或带宽的额度。
- **恒定** - 资源流量的性能耗用与性能目标完全相同。

## <a name="bursting-examples"></a>突发示例

以下示例显示了在使用各种 VM 和磁盘组合时突发的工作情况。 为了使示例易于理解，我们将重点放在 MB/s 上，但相同的逻辑也独立适用于 IOPS。

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>具有可突发磁盘的非可突发虚拟机
**VM 和磁盘组合：** 
- Standard_D8as_v4 
    - 未缓存的 MB/s：192
- P4 OS 磁盘
    - 预配的 MB/s：25
    - 最大突发 MB/s：170 
- 2 个 P10 数据磁盘 
    - 预配的 MB/s：100
    - 最大突发 MB/s：170

 当 VM 启动时，它从 OS 磁盘中检索数据。 由于 OS 磁盘是正在启动的 VM 的一部分，因此 OS 磁盘的突发额度将是充分的。 这些额度使 OS 磁盘以 170 MB/s 的速度突发启动。

![VM 将 192 MB/s 的吞吐量请求发送到 OS 磁盘，OS 磁盘以 170 MB/s 数据进行响应。](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

启动完成后，应用程序将在 VM 上运行，并且具有非关键工作负荷。 此工作负荷需要 15 MB/S（在所有磁盘上均匀分布）。

![应用程序向 VM 发送 15 MB/s 的吞吐量请求，VM 接收请求并向其每个磁盘发送 5 MB/s 的请求，每个磁盘返回 5 MB/s，VM 将 15 MB/s 返回到应用程序。](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

然后，应用程序需要处理一个批处理作业，该作业需要 192 MB/s。 2 MB/s 由 OS 磁盘使用，其余部分在数据磁盘之间平均划分。

![应用程序将 192 MB/s 的吞吐量请求发送到 VM，VM 接收请求，并将其大部分请求发送到数据磁盘（每个 95 MB/s）并将 2 MB/s 发送到 OS 磁盘，数据磁盘突发以满足需求，并且所有磁盘都将请求的吞吐量返回给 VM，后者将其返回给应用程序。](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>具有非可突发磁盘的可突发虚拟机
**VM 和磁盘组合：** 
- Standard_L8s_v2 
    - 未缓存的 MB/s：160
    - 最大突发 MB/s：1280
- P50 OS 磁盘
    - 预配的 MB/s：250 
- 2 个 P50 数据磁盘 
    - 预配的 MB/s：250

 初始启动后，应用程序将在 VM 上运行，并且具有非关键工作负荷。 此工作负荷需要 30 MB/s（在所有磁盘上均匀分布）。
![应用程序向 VM 发送 30 MB/s 的吞吐量请求，VM 接收请求并向其每个磁盘发送 10 MB/s 的请求，每个磁盘返回 10 MB/s，VM 将 30 MB/s 返回到应用程序。](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

然后，应用程序需要处理一个批处理作业，该作业需要 600 MB/s。 Standard_L8s_v2 将发生突发以满足这一需求，然后，对磁盘的请求会均匀分布到 P50 磁盘。

![应用程序向 VM 发送 600 MB/s 的吞吐量请求，VM 接收突发以接收请求并向其每个磁盘发送 200 MB/s 的请求，每个磁盘返回 200 MB/s，VM 发生突发以将 600 MB/s 返回到应用程序。](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>具有可突发磁盘的可突发虚拟机
**VM 和磁盘组合：** 
- Standard_L8s_v2 
    - 未缓存的 MB/s：160
    - 最大突发 MB/s：1280
- P4 OS 磁盘
    - 预配的 MB/s：25
    - 最大突发 MB/s：170 
- 2 个 P4 数据磁盘 
    - 预配的 MB/s：25
    - 最大突发 MB/s：170 

VM 启动时，它将发生突发，从 OS 磁盘请求 1,280 MB/s（其突发上限），OS 磁盘将以其 170 MB/s 的突发性能作为响应。

![在启动时，VM 发生突发以向 OS 磁盘发送 1,280 MB/s 的请求，OS 磁盘发生突发以返回 1,280 MB/s。](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

启动后，你启动了一个具有非关键工作负荷的应用程序。 此应用程序需要 15 MB/s（在所有磁盘上均匀分布）。

![应用程序向 VM 发送 15 MB/s 的吞吐量请求，VM 接收请求并向其每个磁盘发送 5 MB/s 的请求，每个磁盘返回 5 MB/s 的响应，VM 将 15 MB/s 返回到应用程序。](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

然后，应用程序需要处理一个批处理作业，该作业需要 360 MB/s。 Standard_L8s_v2 发生突发以满足此要求，然后进行请求。 OS 磁盘只需要 20 MB/s。 剩余的 340 MB/s 由突发 P4 数据磁盘处理。

![应用程序向 VM 发送 360 MB/s 的吞吐量请求，VM 接收突发以接收请求并从 OS 磁盘向其每个磁盘发送 170 MB/s 和 20 MB/s 的请求，每个磁盘返回请求的 MB/s，VM 发生突发以将 360 MB/s 返回到应用程序。](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
