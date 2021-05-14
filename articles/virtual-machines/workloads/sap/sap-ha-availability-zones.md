---
title: 使用 Azure 可用性区域的 SAP 工作负荷配置 | Microsoft Docs
description: 使用 Azure 可用性区域的 SAP NetWeaver 的高可用性体系结构和方案
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 842c56ef1fb6f68c3d8b82e2633d9a604db9fde2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671635"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>使用 Azure 可用性区域的 SAP 工作负荷配置
除了可以在 Azure 可用性集中部署不同的 SAP 体系结构层以外，还可以使用最近引入的 [Azure 可用性区域](../../../availability-zones/az-overview.md)部署 SAP 工作负载。 Azure 可用性区域 (zone) 定义为：“某个地理区域 (region) 中的独特物理位置。 每个局部区域 (zone) 由一个或多个数据中心组成，这些数据中心配置了独立电源、散热设备和网络”。 并非所有地理区域中都有 Azure 可用性区域。 有关提供可用性区域的 Azure 区域，请查看 [Azure 区域地图](https://azure.microsoft.com/global-infrastructure/geographies/)。 此地图显示了哪些区域提供或已宣布提供可用性区域。 

需要保护三个不同的典型 SAP NetWeaver 或 S/4HANA 体系结构层：

- SAP 应用程序层，可以是一到几十个 VM。 需要尽可能地避免在同一台主机服务器上部署多个 VM。 此外，这些 VM 与 DBMS 层之间应保持可接受的邻近距离，使网络延迟保持在可接受的范围内
- SAP ASCS/SCS 层，代表 SAP NetWeaver 和 S/4HANA 体系结构中的单一故障点。 你通常会看到要在故障转移框架中涵盖的两个 VM。 因此，应将这些 VM 分配到不同的基础结构容错域和更新域中
- SAP DBMS 层，也代表单一故障点。 在一般情况下，它由故障转移框架涵盖的两个 VM 组成。 因此，应将这些 VM 分配到不同的基础结构容错域和更新域中。 例外的情况是可以使用两个以上 VM 的 SAP HANA 横向扩展部署

通过可用性集或通过可用性区域部署关键 VM 的主要差别在于：

- 使用可用性集进行部署时，将在单个局部区域或数据中心（以适用于特定地理区域的情况为准）的集内排布 VM。 因此，如果通过可用性集进行部署，那么在发生影响整个局部区域中数据中心的电源、散热或网络问题时，这种部署无法受到保护。 但从另一方面看，VM 与该局部区域或数据中心内的更新域和容错域相对应。 具体对于可为每个可用性集内的两个 VM 提供保护的 SAP ASCS 或 DBMS 层而言，与容错域和更新域相对应可以防止这两个 VM 最终部署在相同的主机硬件上 
- 通过 Azure 可用性区域部署 VM 并选择不同的局部区域（目前最多可选择三个）会将 VM 部署到不同的物理位置，因此，在发生影响整个局部区域中数据中心的电源、散热或网络问题时，可以提供更多的保护。 但是，随着你将同一 VM 系列的多个 VM 部署到同一个可用性区域，最终部署在同一台主机上的这些 VM 就不会受到保护。 因此，通过可用性区域进行部署非常适合 SAP ASCS 和 DBMS 层，在其中的每个层我们通常会看到两个 VM。 对于可能远远超过两个 VM 的 SAP 应用程序层，可能需要回退到不同的部署模型（参阅后文）

跨 Azure 可用性区域进行部署的动机应该是，在能够应对单个关键 VM 发生故障，或者减少在关键 VM 中修补软件所造成的停机时间的基础上，在发生可能影响一个或多个 Azure 数据中心可用性的重大基础结构问题时提供保护。 

## <a name="considerations-for-deploying-across-availability-zones"></a>跨可用性区域部署的注意事项


使用可用性区域时需要考虑以下事项：

- 不能保证一个 Azure 区域中的不同可用性区域之间保持特定的距离。
- 可用性区域不是理想的灾难恢复 (DR) 解决方案。 在全球某些区域，自然灾害可能造成广泛破坏，其中包括对电力基础设施的严重破坏。 不同区域之间的距离可能不足以用作确定合适 DR 解决方案的标准。
- 可用性区域之间的网络延迟因 Azure 区域的不同而有所差异。 有时，可以跨不同区域部署并运行 SAP 应用层，因为从一个区域到活动 DBMS VM 的网络延迟是可接受的。 但在某些 Azure 区域中，部署在不同区域中的活动 DBMS VM 与 SAP 应用程序实例之间的延迟对于 SAP 业务流程而言不可接受。 在这种情况下，部署体系结构需要有所不同，要么对应用程序采用主动/主动体系结构，要么采用主动/被动体系结构（如果跨局部区域网络的延迟过高）。
- 根据在不同区域之间测得的网络延迟，确定在何处使用可用性区域。 网络延迟在两个方面发挥重要作用：
    - 需要进行同步复制的两个 DBMS 实例之间的延迟。 网络延迟越高，工作负荷可伸缩性受影响的可能性越大。
    - 在活动 DBMS 实例所在区域中运行 SAP 对话实例的 VM 与另一区域中的类似 VM 之间的网络延迟差。 此差越大，业务进程和批处理作业运行时受到的影响就越大，具体取决于它们是在 DBMS 所在区域中运行，还是在其他区域中运行。

跨可用性区域部署 Azure VM 并在同一 Azure 区域内建立故障转移解决方案存在一些限制：

- 部署到 Azure 可用性区域时必须使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 
- 区域枚举到物理区域的映射限定为 Azure 订阅。 如果使用不同的订阅部署 SAP 系统，则需要为每个订阅定义理想的区域。
- 除非使用 [Azure 邻近放置组](../../co-location.md)，否则无法在 Azure 可用性区域中部署 Azure 可用性集。 [用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)一文中介绍了如何跨局部区域部署 SAP DBMS 层和中心服务，以及如何同时使用可用性集部署 SAP 应用程序层并仍旧实现 VM 的邻近性。 如果未在使用 Azure 邻近放置组，则需要选择一个作为虚拟机的部署框架。
- 不能使用 [Azure 基本负载均衡器](../../../load-balancer/load-balancer-overview.md)基于 Windows Server 故障转移群集或 Linux Pacemaker 创建故障转移群集解决方案。 需要使用 [Azure 标准负载均衡器 SKU](../../../load-balancer/load-balancer-standard-availability-zones.md)。



## <a name="the-ideal-availability-zones-combination"></a>理想的可用性区域组合
如果你要跨局部区域部署 SAP NetWeaver 或 S/4HANA 系统，那么有以下两种主要体系结构可以部署：

- 主动/主动：运行 ASCS/SCS 的 VM 对以及运行 DBMS 层的 VM 对分布在两个局部区域中。 运行 SAP 应用程序层的 VM 会平均部署到相同的两个局部区域中。 如果 DBMS 或 ASCS/SCS VM 正在故障转移，某些未完成的和处于活动状态的事务可能会回滚。 但用户仍保持登录状态。 主动 DBMS VM 和应用程序实例在哪个局部区域中运行实际上并不重要。 此体系结构是用于跨局部区域部署的首选体系结构。
- 主动/被动：运行 ASCS/SCS 的 VM 对以及运行 DBMS 层的 VM 对分布在两个局部区域中。 运行 SAP 应用程序层的 VM 会部署到一个可用性区域中。 在主动 ASCS/SCS 和 DBMS 实例所在的同一局部区域中运行应用程序层。 如果跨不同局部区域的网络延迟太高，以致无法运行跨局部区域分布的应用程序层，请使用此部署体系结构。 SAP 应用程序层需要在主动 ASCS/SCS 和/或 DBMS 实例所在的同一局部区域中运行。 如果某个 ASCS/SCS 或 DBMS VM 故障转移到次要区域，则你可能会遇到更高的网络延迟，吞吐量也随之下降。 另外，需要尽快故障回复先前已故障转移的 VM，才能恢复到先前的吞吐量级别。 如果发生局部区域性服务中断，需要将应用程序层故障转移到次要区域。 用户遇到的一种事件就是系统完全关闭。 在某些 Azure 区域中，当你想要使用可用性区域时，此体系结构是唯一可行的体系结构。 如果你无法接受 ASCS/SCS 或 DBMS VM 故障转移到次要区域所造成的潜在影响，坚持使用可用性集部署可能更好


因此，在决定如何使用可用性区域之前，需要确定：

- Azure 区域的三个局部区域之间的网络延迟。 了解一个地理区域中各局部区域之间的网络延迟，这样，便可以选择在跨局部区域网络流量中网络延迟最低的局部区域。
- 所选区域之一中的 VM 到 VM 延迟，与所选两个区域之间的网络延迟的差。
- 确定需要部署的 VM 类型是否在所选的两个区域中可用的声明。 对于某些 VM（尤其是 M 系列 VM），可能会遇到这种情况：某些 SKU 只在两个（共三个）区域中可用。

## <a name="network-latency-between-and-within-zones"></a>区域之间和区域内部的网络延迟
若要确定不同区域之间的延迟，需要：

- 部署用于所有三个区域中的 DBMS 实例的 VM SKU。 执行此测量时，请务必启用 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。
- 找到网络延迟最低的两个区域后，部署该 VM SKU 的另外三个 VM，用作跨三个可用性区域的应用层 VM。 针对所选的两个 DBMS 区域中的两个 DBMS VM 测量网络延迟。 
- 使用 `niping` 作为测量工具。 SAP 支持说明 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中介绍了 SAP 提供的此工具。 请重点关注所述的延迟测量命令。 由于 **ping** 无法穿透 Azure 加速网络代码路径，因此我们不建议使用它。

无需手动执行这些测试。 可以找到一个 PowerShell 过程：[可用性区域延迟测试](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)，它能自动完成所述的延迟测试。 

根据测量结果以及可用性区域中 VM SKU 的可用性，需要做出一些决策：

- 定义 DBMS 层的理想区域。
- 根据区域内部或区域之间的网络延迟差，确定是否要跨一个、两个或全部三个区域分布主动 SAP 应用层。
- 从应用程序的角度确定是要部署主动/被动还是主动/主动配置。 （本文稍后会解释这些配置。）

在做出这些决策的同时，请考虑 SAP 在 SAP 说明 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中所述的网络延迟建议。

> [!IMPORTANT]
> 所做的测量和决策对于测量时所用的 Azure 订阅有效。 如果使用其他 Azure 订阅，则需要重复测量。 枚举区域的映射可能因另一个 Azure 订阅而异。


> [!IMPORTANT]
> 按如上述执行的测量预期会在支持[可用性区域](https://azure.microsoft.com/global-infrastructure/geographies/)的每个 Azure 区域中显示不同的结果。 即使网络延迟要求不变，也仍可能需要在不同 Azure 区域中采用不同的部署策略，因为区域之间的网络延迟可能不同。 在某些 Azure 区域中，三个不同区域之间的网络延迟可能会存在很大的差异。 在其他区域中，三个不同区域之间的网络延迟可能较为一致。 指出区域之间始终存在 1 毫秒到 2 毫秒网络延迟的声明是错误的。 Azure 区域中可用性区域之间的网络延迟不能一般化。

## <a name="activeactive-deployment"></a>主动/主动部署
此部署体系结构之所以称为主动/主动部署，是因为要跨两个或三个局部区域部署主动的 SAP 应用程序服务器。 使用排队复制的 SAP Central Services 实例将部署在两个区域之间。 这同样适用于 DBMS 层，它将部署在 SAP Central Service 所在的相同区域中。 考虑此配置时，需要在区域中找到两个适当的两个可用性区域，它们的跨区域网络延迟可让工作负荷接受并满足同步 DBMS 复制的需求。 此外，请确保所选区域中的网络延迟与跨区域网络延迟的差不会过大。 

SAP 体系结构的性质是，除非以不同的方式对它进行配置，否则用户作业和批处理作业可以在不同的应用程序实例中执行。 主动/主动部署的这一事实的副作用是，批处理作业可能会由任何 SAP 应用程序实例执行，与它们是否在包含主动 DBMS 的同一局部区域中运行无关。 如果不同局部区域之间的网络延迟相比某一个局部区域内部的网络延迟差异较小，则批处理作业运行时间的差异可能不明显。 但是，当某一个局部区域内部的网络延迟相比跨局部区域网络流量的网络延迟差异越大时，如果批处理作业在一个局部区域中执行且该局部区域中的 DBMS 实例并非主动实例，则批处理作业运行时间受到的影响可能就越大。 运行时间的可接受差异由客户确定。 跨局部区域流量的可容忍网络延迟也由客户确定。

下面列出了当跨不同可用性区域部署的应用程序层中运行时间和吞吐量差异不大时，支持此类主动/主动部署的 Azure 区域：

- 美国西部 2（所有三个局部区域）
- 美国东部 2（所有三个局部区域）
- 美国中部（所有三个局部区域）
- 北欧（所有三个局部区域）
- 西欧（三个局部区域中的两个）
- 美国东部（三个局部区域中的两个）
- 美国中南部（三个局部区域中的两个）
- 英国南部（三个局部区域中的两个）

不建议使用这种跨局部区域的 SAP 部署体系结构的 Azure 区域为：

- 法国中部 
- 南非北部
- 加拿大中部
- Japan East

其他未列出的地理区域可能也符合条件，具体取决于你对运行时间差异的容忍程度。


跨两个区域的主动/主动部署的简化架构如下所示：

![主动/主动区域部署](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

以下注意事项适用于此配置：

- 不使用 [Azure 邻近放置组](../../co-location.md)时，需要将 Azure 可用性区域视为所有 VM 的容错域和更新域，因为不能在 Azure 可用性区域中部署可用性集。
- 如果你要合并 DBMS 层和中心服务的局部区域部署，但要为应用程序层使用 Azure 可用性集，则需要根据[用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)一文中所述使用 Azure 邻近放置组。
- 对于 SAP Central Services 故障转移群集以及 DBMS 层的负载均衡器，需要使用[标准 SKU Azure 负载均衡器](../../../load-balancer/load-balancer-standard-availability-zones.md)。 基本负载均衡器不能跨区域工作。
- 所部署的用于托管 SAP 系统的 Azure 虚拟网络及其子网将跨区域延伸。 不需要隔离每个区域的虚拟网络。
- 对于部署的所有虚拟机，需要使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 区域部署不支持非托管磁盘。
- Azure 高级存储、[超级 SSD 存储](../../disks-types.md#ultra-disk)或 ANF 不支持任何类型的跨局部区域复制存储。 应用程序（DBMS 或 SAP Central Services）必须复制重要数据。
- 这一点同样适用于共享的 sapmnt 目录，包括共享磁盘 (Windows)、CIFS 共享 (Windows) 或 NFS 共享 (Linux)。 需要采用某种技术来复制此类共享磁盘或者在区域之间共享。 支持以下技术：
  - 对于 Windows，支持使用 SIOS DataKeeper 的群集解决方案，具体请参阅[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](./sap-high-availability-guide-wsfc-shared-disk.md)。
  - 对于 SUSE Linux，支持根据 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](./high-availability-guide-suse-nfs.md)中所述构建的 NFS 共享。
    
    目前，不支持使用[针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-file-share.md)中所述的 Microsoft 横向扩展文件服务的跨区域解决方案。
- 如果你要构建 [SUSE Linux Pacemaker 群集](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)并使用 SBD 设备而不是 Azure 隔离代理，则第三个局部区域用于托管 SBD 设备。 或者，此局部区域用于托管其他应用程序实例。
- 若要实现关键业务进程的运行时一致性，可以尝试使用 SAP 批处理服务器组、SAP 登录组或 RFC 组，将特定的批处理作业和用户定向到主动 DBMS 实例所在局部区域中的应用程序实例。 但是，发生区域性故障转移时，需要手动将这些组移动到在活动 DB VM 所在区域内的 VM 上运行的实例。  
- 你可能想要在每个区域中部署一些休眠对话实例。 

> [!IMPORTANT]
> 对于此主动/主动方案，Microsoft 已宣布从 2020 年 4 月 1 日起收取额外的带宽费。 请查看文档[带宽定价详细信息](https://azure.microsoft.com/pricing/details/bandwidth/)。 SAP 应用程序层与 SAP DBMS 层之间的数据传输活动非常密集。 因此，主动/主动方案的费用可能很高。 请不时查看本文以了解确切的费用 


## <a name="activepassive-deployment"></a>主动/被动部署
如果发现一个区域中的网络延迟与跨区域网络流量的网络延迟差不可接受，可部署的体系结构从 SAP 应用层的角度来看应该具有主动/被动特征。 定义一个主动区域，在其中部署完整的应用层，并尝试运行主动 DBMS 实例和主动 SAP Central Services 实例。 使用此类配置时，需要根据作业是否在主动 DBMS 实例所在区域中运行，确保业务事务与批处理作业的运行时差异不会过大。

可能更适合使用这种跨不同局部区域的部署体系结构的 Azure 区域为：

- Southeast Asia
- 澳大利亚东部
- 巴西南部
- 德国中西部
- 南非北部
- 法国中部 
- 加拿大中部
- Japan East


该体系结构的基本布局如下所示：

![主动/被动区域部署](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

以下注意事项适用于此配置：

- 不能在 Azure 可用性区域中部署可用性集。 若要弥补这一点，可以根据[用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)一文中所述使用 Azure 邻近放置组。
- 使用此体系结构时，需要进行密切监视状态，并尝试使主动 DBMS 和 SAP Central Services 实例与所部署的应用层位于同一区域。 故障转移 SAP Central Services 或 DBMS 实例时，请确保能够尽快手动故障回复到包含所部署的 SAP 应用层的区域。
- 对于 SAP Central Services 故障转移群集以及 DBMS 层的负载均衡器，需要使用[标准 SKU Azure 负载均衡器](../../../load-balancer/load-balancer-standard-availability-zones.md)。 基本负载均衡器不能跨区域工作。
- 所部署的用于托管 SAP 系统的 Azure 虚拟网络及其子网将跨区域延伸。 不需要隔离每个区域的虚拟网络。
- 对于部署的所有虚拟机，需要使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 区域部署不支持非托管磁盘。
- Azure 高级存储、[超级 SSD 存储](../../disks-types.md#ultra-disk)或 ANF 不支持任何类型的跨局部区域复制存储。 应用程序（DBMS 或 SAP Central Services）必须复制重要数据。
- 这一点同样适用于共享的 sapmnt 目录，包括共享磁盘 (Windows)、CIFS 共享 (Windows) 或 NFS 共享 (Linux)。 需要采用某种技术来复制此类共享磁盘或者在区域之间共享。 支持以下技术：
    - 对于 Windows，支持使用 SIOS DataKeeper 的群集解决方案，具体请参阅[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](./sap-high-availability-guide-wsfc-shared-disk.md)。
    - 对于 SUSE Linux，支持根据 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](./high-availability-guide-suse-nfs.md)中所述构建的 NFS 共享。
    
  目前，不支持使用[针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-file-share.md)中所述的 Microsoft 横向扩展文件服务的跨区域解决方案。
- 如果你要构建 [SUSE Linux Pacemaker 群集](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)并使用 SBD 设备而不是 Azure 隔离代理，则第三个局部区域用于托管 SBD 设备。 或者，此局部区域用于托管其他应用程序实例。
- 应在被动局部区域中部署休眠 VM（从 DBMS 角度看），以便在发生局部区域故障时能够启动应用程序资源。
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 目前无法在区域之间将主动 VM 复制到休眠 VM。 
- 应该投资于自动化功能，以便在发生局部区域性的服务中断时，自动在另一局部区域中启动 SAP 应用程序层。

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>高可用性和灾难恢复组合配置
Microsoft 不会共享有关托管 Azure 区域中不同 Azure 可用性区域的设施之间的地理距离的任何信息。 尽管如此，一些客户正在使用相关区域进行 HA 和 DR 组合配置，此配置承诺恢复点目标 (RPO) 为零。 PRO 为零意味着，即使是对于灾难恢复，也不应丢失任何提交的数据库事务。 

> [!NOTE]
> 建议仅在特定的场合采用此类配置。 例如，出于安全与合规原因，数据不能离开 Azure 区域，在这种情况下可以使用此配置。 

下面是此类配置的示例：

![在区域中结合使用高可用性和灾难恢复](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

以下注意事项适用于此配置：

- 假设托管可用性区域的设施之间的距离很大，或者你不能离开特定的 Azure 区域。 不能在 Azure 可用性区域中部署可用性集。 若要弥补这一点，可以根据[用于最大程度地降低 SAP 应用程序网络延迟的 Azure 邻近放置组](sap-proximity-placement-scenarios.md)一文中所述使用 Azure 邻近放置组。
- 使用此体系结构时，需要进行密切监视状态，并尝试使主动 DBMS 和 SAP Central Services 实例与所部署的应用层位于同一区域。 故障转移 SAP Central Services 或 DBMS 实例时，请确保能够尽快手动故障回复到包含所部署的 SAP 应用层的区域。
- VM 中应该预装了运行主动 QA 应用程序实例的生产应用程序实例。
- 发生局部区域性故障时，需要关闭 QA 应用程序实例并启动生产实例。 需要使用应用程序实例的虚拟名称才能做到这一点。
- 对于 SAP Central Services 故障转移群集以及 DBMS 层的负载均衡器，需要使用[标准 SKU Azure 负载均衡器](../../../load-balancer/load-balancer-standard-availability-zones.md)。 基本负载均衡器不能跨区域工作。
- 所部署的用于托管 SAP 系统的 Azure 虚拟网络及其子网将跨区域延伸。 不需要隔离每个区域的虚拟网络。
- 对于部署的所有虚拟机，需要使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 区域部署不支持非托管磁盘。
- Azure 高级存储和[超级 SSD 存储](../../disks-types.md#ultra-disk)不支持跨区域的任何存储复制类型。 应用程序（DBMS 或 SAP Central Services）必须复制重要数据。
- 这一点同样适用于共享的 sapmnt 目录，包括共享磁盘 (Windows)、CIFS 共享 (Windows) 或 NFS 共享 (Linux)。 需要采用某种技术来复制此类共享磁盘或者在区域之间共享。 支持以下技术：
    - 对于 Windows，支持使用 SIOS DataKeeper 的群集解决方案，具体请参阅[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](./sap-high-availability-guide-wsfc-shared-disk.md)。
    - 对于 SUSE Linux，支持根据 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](./high-availability-guide-suse-nfs.md)中所述构建的 NFS 共享。

  目前，不支持使用[针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-file-share.md)中所述的 Microsoft 横向扩展文件服务的跨区域解决方案。
- 如果你要构建 [SUSE Linux Pacemaker 群集](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)并使用 SBD 设备而不是 Azure 隔离代理，则第三个局部区域用于托管 SBD 设备。 





## <a name="next-steps"></a>后续步骤
下面是跨 Azure 可用性区域进行部署的后续步骤：

- [使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](./sap-high-availability-guide-wsfc-shared-disk.md)
- [针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](./sap-high-availability-infrastructure-wsfc-file-share.md)