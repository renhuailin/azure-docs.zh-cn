---
title: include 文件
description: include 文件
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: a2f6a3fd8d36bb54773db21e3d36dab0060bae57
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178307"
---
## <a name="disk-level-bursting"></a>磁盘级突发

### <a name="on-demand-bursting-preview"></a>按需突发 (预览) 

使用磁盘突发的按需突发模式的磁盘可能会超出原始预配目标，频率取决于其工作负荷的需要，最大可达最大突发目标。 例如，在 TiB P30 磁盘上，预配的 IOPS 为 5000 IOPS。 在此磁盘上启用磁盘突发功能时，工作负荷可以向此磁盘发出 Io，最大可达 30000 IOPS 的最大突发性能和 1000 MBps。

如果希望工作负荷在预配的性能目标之外频繁运行，则磁盘突发不会经济高效。 在这种情况下，我们建议你改为将磁盘的性能层改为 [较高的层级](../articles/virtual-machines/disks-performance-tiers.md) ，以获得更好的基准性能。 查看计费详细信息，并根据工作负荷的流量模式对其进行评估。

启用按需突发操作之前，请先了解以下内容：

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>区域可用性

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>计费

使用按需突发模型的磁盘将按小时计费，启用固定费用，并将事务成本应用到超出预配目标的任何突发事务。 使用即用即付模型根据非缓存磁盘 Io （包括超过预配目标的读取和写入）对事务成本收费。 下面是一个按计费时间排列的磁盘流量模式示例：

磁盘配置：高级 SSD-1 TiB (P30) ，已启用磁盘突发。

- 00:00:00 00:10:00-低于预配目标的 5000 IOPS 
- 00:10:01 –00:10:10 应用程序发出了一个批处理作业，导致磁盘 IOPS 以 6000 IOPS 的速率突然增长10秒 
- 00:10:11 00:59:00-低于预配目标的 5000 IOPS 
- 00:59:01 –01:00:00 应用程序发出了另一个批处理作业，导致在60秒内以 7000 IOPS 突发磁盘 IOPS 

在此计费小时内，突发成本包括两个费用：

第一次收费是) 确定的 $X (的突发性支持。 在禁用附加状态之前，这一固定费用始终会在磁盘上进行收费。 

其次是突发事务成本。 磁盘突发发生在两个时间段。 从00:10:01 –00:10:10，累积的突发事务 (6000 – 5000) X 10 = 10000。 从00:59:01 –01:00:00，累积的突发事务 (7000 – 5000) X 60 = 120000。 猝发事务总数为 10000 + 120000 = 130000。 根据区域定价) ，将根据 10000 (事务的13个单位，按 $Y 向突发事务成本收费。

这样，此计费小时的磁盘突发总成本就等于 $X + $Y。 此计算将适用于预配目标为 Mbits/s 的突发。 我们将 MB 的超额转换为 IO 大小为256KB 的事务。 如果磁盘流量超过了预配的 IOPS 和 MBps 目标，则可以参考以下示例来计算突发事务。 

磁盘配置：高级 SSD-1 TB (P30) ，已启用磁盘突发。

- 00:00:01 –00:00:05 应用程序发出了一个批处理作业，导致磁盘 IOPS 以 10000 IOPS 和 300 MBps 的速度突然增长5秒。
- 00:00:06 –00:00:10 应用程序发出了一个恢复作业，导致磁盘 IOPS 以 6000 IOPS 和 600 MBps 的速度突然增长5秒。

突发事务的处理方式是 IOPS 或 MBps 突发中的最大事务数。 从00:00:01 –00:00:05，累积的突发事务为 max ( (10000 – 5000) ， (300-200) * 1024/256) ) * 5 = 25000 事务。 从00:00:06 –00:00:10，累积的突发事务为 max ( (6000 – 5000) ， (600-200) * 1024/256) ) * 5 = 8000 事务。 在这种情况下，你可以包括突发启用固定费用，以获取启用按需磁盘突发的总成本。 

有关定价的详细信息，请参阅 [托管磁盘定价页](https://azure.microsoft.com/pricing/details/managed-disks/) ，并使用 [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=storage) 对工作负荷进行评估。 

### <a name="credit-based-bursting"></a>基于信用的突发

基于信用的突发适用于 Azure 公共、政府和中国云的所有区域中 P20 和更小的磁盘大小。 默认情况下，在支持的磁盘大小的所有新的和现有部署上启用磁盘突发。 VM 级突发只使用基于信用额度的突发。

## <a name="virtual-machine-level-bursting"></a>虚拟机级突发

VM 级别突发只使用基于信用额度的突发模型进行突发，默认情况下，它为支持它的所有 Vm 启用。

在 Azure 公有云中的所有区域中，根据这些支持的大小启用了 VM 级突发功能： 
- [Lsv2 系列](../articles/virtual-machines/lsv2-series.md)
- [Dv3 和 Dsv3 系列](../articles/virtual-machines/dv3-dsv3-series.md)
- [Ev3 和 Esv3 系列](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>突发流

突发信用系统以相同的方式应用于 VM 级别和磁盘级别。 资源（VM 或磁盘）将在其自己的突发桶中以完全贮存的信用开始。 通过这些信用额度，你可以以最大猝发速率传输最多30分钟。 每当资源的 IOPS 或 MB/秒在资源的性能目标下使用时，就会积累信用额度。 如果你的资源有累积的突发信用额度，而你的工作负荷需要额外的性能，则你的资源可以使用这些信用额度超出其性能限制，并提高其性能以满足工作负荷需求。

![突发桶关系图。](media/managed-disks-bursting/bucket-diagram.jpg)

你的可用信用额度取决于你。 你可以连续使用30分钟的突发信用额度，也可以在一天偶尔使用。 部署资源时，它们会提供全部信用额度。 当这些消耗时，restock 不到一天。 信用额度可供你决定，无需对资源进行完整的突发，就能突发资源。 突发累积根据每个资源的不同而异，因为它基于未使用的 IOPS，并且低于其性能目标。 更高的基线性能资源可以更快地降低其突发信用额度，而不是降低基线性能。 例如，P1 disk 置于空闲状态将每秒累积 120 IOPS，而置于空闲状态 P20 磁盘会每秒累积 2300 IOPS。

## <a name="bursting-states"></a>突发状态
启用了突发功能时，资源可能处于以下三种状态之一：
- **累积** –资源的 IO 流量小于性能目标。 为 IOPS 和 MB/s 累积额度是彼此分开执行的。 你的资源可能会积累 IOPS 额度并支出 MB/s 额度，也可能会支出 IOPS 额度并积累 MB/s 额度。
- **突发** –资源的流量超过性能目标。 突发流量将独立消耗 IOPS 或带宽的额度。
- **常量** –资源的流量与性能目标完全相同。

## <a name="bursting-examples"></a>突发示例

以下示例显示了突发如何与各种 VM 和磁盘组合一起工作。 为了使示例易于理解，我们将重点放在 MB/s 上，但相同的逻辑也独立适用于 IOPS。

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>具有可突增磁盘的非可突增虚拟机
**VM 和磁盘组合：** 
- Standard_D8as_v4 
    - 未缓存的 MB/s：192
- P4 OS 磁盘
    - 预配的 MB/s：25
    - 最大突发 MB/s：170 
- 2 个 P10 数据磁盘 
    - 预配的 MB/秒：100
    - 最大突发 MB/s：170

 VM 启动时，将从 OS 磁盘检索数据。 由于 OS 磁盘是正在启动的 VM 的一部分，因此 OS 磁盘将满突发信用额度。 在这些信用额度内，操作系统磁盘将以 170 MB/秒的速度突然增长。

![VM 将针对 192 MB/秒的吞吐量发送到 OS 磁盘，操作系统磁盘响应的数据为 170 MB/s。](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

启动完成后，应用程序将在 VM 上运行，并且具有非关键工作负荷。 此工作负荷需要 15 MB/S，这会在所有磁盘上均匀分配。

![应用程序向 VM 发送 15 MB/秒的吞吐量请求，VM 将请求并发送其中每个磁盘 a 请求 5 MB/秒，每个磁盘将返回 5 MB/秒，而 VM 则会将 15 MB/秒返回到应用程序。](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

然后，应用程序需要处理一个批处理作业，该作业需要 192 MB/s。 OS 磁盘使用 2 MB/秒，其余部分在数据磁盘之间平均剥离。

![应用程序将针对 192 MB/秒的吞吐量的请求发送到 VM，VM 会执行请求，并将其请求发送到数据磁盘， (每) 95 MB/秒到 OS 磁盘、数据磁盘脉冲以满足需求，并且所有磁盘都将请求的吞吐量返回给 VM，并将其返回给应用程序。](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>具有非可突发磁盘的可突发虚拟机
**VM 和磁盘组合：** 
- Standard_L8s_v2 
    - 未缓存的 MB/s：160
    - 最大突发 MB/s：1280
- P50 OS 磁盘
    - 预配的 MB/s：250 
- 2 个 P10 数据磁盘 
    - 预配的 MB/s：250

 初始启动后，应用程序将在 VM 上运行，并且具有非关键工作负荷。 此工作负荷需要 30 MB/s，可在所有磁盘上均匀分配。
![应用程序将请求的吞吐量从 30 MB/秒发送到 VM，VM 将请求并发送其每个磁盘 a 请求 10 MB/秒，每个磁盘将返回 10 mb/s，而 VM 则会将 30 MB/秒返回到应用程序。](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

然后，应用程序需要处理一个批处理作业，该作业需要 600 MB/s。 Standard_L8s_v2 突发以满足这一需求，然后对磁盘的请求会均匀地分散到 P50 磁盘。

![应用程序将针对 600 MB/秒的吞吐量发送到 VM 的请求，VM 会将请求发送到请求，并向其每个磁盘发送 200 MB/秒的请求，每个磁盘返回 200 MB/秒，VM 猝发返回 600 MB/秒到应用程序。](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>可突增包含可突增磁盘的虚拟机
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

VM 启动时，会将其从 OS 磁盘请求突发为 1280 MB/秒的突发限制，操作系统磁盘将响应其突发性能 170 MB/秒。

![在启动时，VM 会向后发送 1280 MB/秒的请求到 OS 磁盘，操作系统磁盘会出现猝发，返回 1280 MB/秒。](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

启动后，启动一个具有非关键工作负荷的应用程序。 此应用程序需要 15 MB/s，这会在所有磁盘上均匀分配。

![应用程序向 VM 发送 15 MB/秒的吞吐量请求，VM 将请求并发送其中每个磁盘 a 请求 5 MB/秒，每个磁盘将返回 5 MB/秒，而 VM 则会将 15 MB/秒返回到应用程序。](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

然后，应用程序需要处理一个批处理作业，该作业需要 360 MB/s。 Standard_L8s_v2 发生突发以满足此要求，然后进行请求。 OS 磁盘只需要 20 MB/s。 剩余 340 MB/秒由突发 P4 数据磁盘处理。

![应用程序将针对 360 MB/秒的吞吐量发送到 VM 的请求，VM 会将请求发送到请求，并将其每个数据磁盘的请求从 OS 磁盘发送到 170 MB/秒和 20 MB/秒，每个磁盘将返回所请求的 MB/秒，将 VM 猝发360到应用程序。](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
