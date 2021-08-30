---
title: 优化 Azure 磁盘池（预览）性能
description: 了解如何充分利用 Azure 磁盘池。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e3ffadcdc77d09d04518cdf577208250c2906407
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114469880"
---
# <a name="azure-disk-pools-preview-planning-guide"></a>Azure 磁盘池（预览）规划指南

请务必在部署 Azure 磁盘池（预览）之前了解工作负载的性能要求。 预先确定你的要求可让你充分利用磁盘池。 磁盘池的性能由三个主要因素确定，调节其中任何一个因素都会调整磁盘池的性能：

- 磁盘池的可伸缩性目标
- 包含在磁盘池中的各个磁盘的可伸缩性目标
- 客户端计算机与磁盘池之间的网络连接。

## <a name="optimize-for-low-latency"></a>针对低延迟进行优化

如果你优先考虑低延迟，请将超级磁盘添加到磁盘池中。 超级磁盘提供了亚毫秒级磁盘延迟。 若要尽可能降低延迟，还必须评估网络配置，并确保其使用最佳路径。 考虑使用 [ExpressRoute FastPath](../expressroute/about-fastpath.md) 来最小化网络延迟。

## <a name="optimize-for-high-throughput"></a>针对高吞吐量进行优化

如果你优先考虑吞吐量，请先评估交付吞吐量目标所需的磁盘池数。 获得必要的目标后，可以在每个单独的磁盘及其类型之间进行拆分。 目前，可在磁盘池、高级 SSD 和超级磁盘中使用两种磁盘类型。 高级 SSD 可以提供高 IOPS 和 MBps（可随存储容量缩放），而超级磁盘可独立于其存储容量缩放其性能。 选择最适合实现成本和性能均衡的类型。 同时，确认从客户端到磁盘池的网络连接不是瓶颈，特别是吞吐量。


## <a name="use-cases"></a>用例

下表列出了使用 Azure VMware 解决方案的磁盘池的一些典型用例以及建议的配置。


|Azure VMware 解决方案用例  |建议的磁盘类型  |建议的网络配置  |
|---------|---------|---------|
|阻止活动工作集的存储，如 Azure VMware 解决方案 vSAN 的扩展。     |超级磁盘         |使用 ExpressRoute 虚拟网络网关 - 超高性能和 ErGw3AZ (10 Gbps) - 将磁盘池虚拟网络连接到 Azure VMware 解决方案云，并启用 FastPath 以最大限度地减少网络延迟。         |
|分层 - 将不常访问的数据从 Azure VMware 解决方案 vSAN 分层到磁盘池。     |高级·SSD         |使用 ExpressRoute 虚拟网络网关 - 标准 (1 Gbps) 或高性能 (2 Gbps) - 将磁盘池虚拟网络连接到 Azure VMware 解决方案云。         |
|Azure VMware 解决方案上的灾难恢复站点的数据存储：将数据从本地或主要 VMware 环境复制到作为辅助站点的磁盘池。     |高级·SSD         |使用 ExpressRoute 虚拟网络网关 - 标准 (1 Gbps) 或高性能 (2 Gbps) - 将磁盘池虚拟网络连接到 Azure VMware 解决方案云。         |

请参阅 [Azure VMware 解决方案的网络规划清单](../azure-vmware/tutorial-network-checklist.md)以规划网络设置，以及其他 Azure VMware 解决方案注意事项。

## <a name="disk-pool-scalability-and-performance-targets"></a>磁盘池可伸缩性和性能目标

|资源  |限制  |
|---------|---------|
|每个磁盘池的最大磁盘数|8|
|每个磁盘池的最大 IOPS|25,600|
|每个磁盘池的最大 MBps|384|

下面的示例将让你了解不同性能因素是如何协同工作的：

例如，如果将两个 1-TiB 高级 SSD（P30，预配目标为 5000 IOPS 和 200 Mbps）添加到磁盘池，则可实现 2 x 5000 = 10,000 IOPS。 但是，吞吐量会由磁盘池限制为 384 MBps。 若要超过此 384-MBps 限制，可以部署更多的磁盘池以进行横向扩展来获得额外的吞吐量。 网络吞吐量会限制横向扩展的有效性。

## <a name="availability"></a>可用性

磁盘池目前处于预览状态，不应该用于生产工作负载。

如果 Azure VMware 解决方案云出于任何原因而无法访问磁盘池，则你会遇到以下情况：

- 将无法再访问与磁盘池关联的所有数据存储。
- 此 Azure VMware 解决方案云中托管的使用受影响数据存储的所有 VMware VM 将处于不正常状态。
- 此 Azure VMware 解决方案云中群集的运行状况不受影响，但一个操作除外：你将无法将主机置于维护模式。 Azure VMware 解决方案将处理此故障，并通过断开受影响的数据存储来尝试恢复。

如果进入到此状态，请按照以下[步骤](disks-pools-troubleshoot.md#recover-a-disk-pool-or-an-iscsi-target)恢复磁盘池。

## <a name="next-steps"></a>后续步骤

[部署磁盘池](disks-pools-deploy.md)。
