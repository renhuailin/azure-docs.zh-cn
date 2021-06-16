---
title: Azure 虚拟机网络吞吐量 | Microsoft Docs
description: 了解 Azure 虚拟机网络吞吐量，包括如何向虚拟机分配带宽。
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: a93db3a2ee147267432d84ea7a7c87ee77eb50e4
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110786266"
---
# <a name="virtual-machine-network-bandwidth"></a>虚拟机网络带宽

Azure 提供各种 VM 大小和类型，每一种包含的性能各不相同。 其中一种是网络吞吐量（也称带宽），以兆位/秒 (Mbps) 表示。 由于虚拟机托管在共享硬件上，因此网络容量必须在共享同一硬件的虚拟机中公平地共享。 在分配时，较大的虚拟机相对于较小的虚拟机会获得相对较多的带宽。
 
分配给每个虚拟机的网络带宽按虚拟机的传出（出站）流量计算。 从虚拟机流出的所有网络流量均计入分配限制，不管流向哪个目标。 例如，如果虚拟机的限制为 1,000 Mbps，则不管出站流量的目标是同一虚拟网络中的另一虚拟机，还是 Azure 外部，均适用该限制。
 
传入流量不直接计算，或者说不直接受到限制。 但是，其他因素（例如 CPU 和存储限制）可能会影响虚拟机处理传入数据的能力。

加速网络是一项旨在改进网络性能（包括延迟、吞吐量和 CPU 使用率）的功能。 虽然加速网络可以改进虚拟机的吞吐量，但仍受分配给该虚拟机的带宽的限制。 若要详细了解如何使用加速网络，请查看适用于 [Windows](create-vm-accelerated-networking-powershell.md) 或 [Linux](create-vm-accelerated-networking-cli.md) 虚拟机的加速网络。
 
Azure 虚拟机必须有一个（但也可能有多个）连接的网络接口。 分配给某个虚拟机的带宽是流经所有网络接口（已连接到该虚拟机）的所有出站流量的总和。 换言之，分配的带宽是针对每个虚拟机的，不管为该虚拟机连接了多少网络接口。 若要了解不同的 Azure VM 大小支持的网络接口数，请查看 Azure [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小。 

## <a name="expected-network-throughput"></a>预期的网络吞吐量

若要详细了解每种 VM 大小支持的预期出站吞吐量和网络接口数，请查看 Azure [Windows](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 和 [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM 大小。 选择一个类型（例如“通用”），然后在生成的页面上选择一个大小系列（例如“Dv2 系列”）。 每个系统都有一个表，在最后一列（名为“最大 NIC 数/预期网络性能(Mbps)”）中包含网络规格。  

吞吐量限制适用于虚拟机。 吞吐量不受以下因素影响：
- **网络接口数**：带宽限制是源自虚拟机的所有出站流量的累积。
- **加速网络**：尽管此功能有助于流量达到已发布的限制，但不会更改限制。
- **流量目标**：所有目标都计入出站限制。
- **协议**：基于所有协议的所有出站流量都计入限制。

## <a name="network-flow-limits"></a>网络流限制

除了带宽，VM 上任意给定时间存在的网络连接数目也可能影响其网络性能。 Azure 网络堆栈保留名为“流”的数据结构中 TCP/UDP 连接的每个方向的状态。 典型的 TCP/UDP 连接将创建 2 个流，一个对应于入站方向，另一个对应于出站方向。 

在终结点之间进行数据传输时，除了那些执行数据传输的流，还必须创建多个流。 例如，为 DNS 解析创建的流，以及为负载均衡器运行状况探测创建的流。 另请注意，网关、代理、防火墙之类的网络虚拟设备 (NVA) 会看到为在设备上终止的连接创建的流，以及为设备所发起的连接创建的流。 

![通过转发设备进行的 TCP 对话的流计数](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-active-connections-recommendations"></a>流限制和活动连接数建议

针对一个 VM，Azure 网络堆栈现支持的流共计 100 万个（50 万个入站、50 万个出站）。 VM 可在不同场景中处理的活动连接总数如下所示。
- 属于 VNET 的所有大小的虚拟机都可以处理 50 万个活动连接，在每个方向都支持 50 万个活动流。  
- 具有网关、代理、防火墙等网络虚拟设备 (NVA) 的虚拟机可以处理 25 万个活动连接，在每个方向都支持 50 万个活动流，这是由于在下一个跃点建立新连接时，将进行转发并创建额外的新流，如上图所示。 

达到此限制后，系统就会删除其他连接。 连接建立速度和终止速度也可能影响网络性能，因为连接的建立和终止与包处理例程共享 CPU。 建议针对预期的流量模式对工作负荷进行基准测试，并根据性能需要对工作负荷进行相应的横向扩展。

[Azure Monitor](../azure-monitor/essentials/metrics-supported.md#microsoftcomputevirtualmachines) 中提供的指标用于跟踪 VM 或 VMSS 实例上的网络流数和流创建速率。

![此屏幕截图显示了 Azure Monitor 的“指标”页，其中包含入站流和出站流的折线图和总计。](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

## <a name="next-steps"></a>后续步骤

- [优化虚拟机操作系统的网络吞吐量](virtual-network-optimize-network-bandwidth.md)
- 针对虚拟机[测试网络吞吐量](virtual-network-bandwidth-testing.md)。
