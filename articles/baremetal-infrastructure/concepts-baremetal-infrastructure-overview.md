---
title: 什么是 Azure 中的 BareMetal 基础结构？
description: 概要介绍 Azure 中的 BareMetal 基础结构。
ms.custom: references_regions
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 744378fc00bc443fdae3babefdf626eacbf486e7
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154862"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>什么是 Azure 中的 BareMetal 基础结构？

Microsoft Azure 提供了一种具有广泛的集成云服务的云基础结构，可满足你的业务需求。 但在某些情况下，你可能需要在没有虚拟化层的裸机服务器上运行服务。 你可能需要 root 访问权限，以及对操作系统 (OS) 的控制权限。 为了满足此需求，Azure 为一些高价值的任务关键型应用程序提供了 BareMetal 基础结构。

BareMetal 基础结构由专用 BareMetal 实例（计算示例）组成。 主要功能包括：
- 高性能存储适用于应用程序（NFS、ISCSI 和光纤通道）。 你还可以在各 BareMetal 实例间共享存储，以实现横向扩展群集或 STONITH 高可用性对等功能。 
- 隔离环境中的一组特定于函数的虚拟 LAN (VLAN)。 
 
如果你在 Azure 订阅中的一个或多个 Azure 虚拟网络 (VNet) 上运行虚拟机 (VM)，则此环境还具有可访问的特殊 VLAN。 整个环境在 Azure 订阅中被表示为一个资源组。

BareMetal 基础结构提供 30 多个 SKU，其服务器从双插槽到 24 个插槽，内存大小范围从 1.5 TB 到 24 TB。 此外，还提供具有 Octane 内存的一组大型 SKU。 Azure 在超大规模云中提供了最大范围的裸机实例。

## <a name="why-baremetal-infrastructure"></a>为什么要使用 BareMetal 基础结构？  

企业中的一些工作负载是由一些技术组成的，这些技术在设计上并不适合在典型的虚拟化云设置中运行。 它们需要特殊的架构、经过认证的硬件或格外大的尺寸。 尽管这些技术具有最成熟的数据保护和业务连续性功能，但这些功能并不是为虚拟化云而构建的。 它们对延迟和干扰性邻居更敏感，需要对变更管理和维护活动进行更多的控制。

BareMetal 基础结构是针对一组选定的此类应用程序进行构建、认证和测试的。 Azure 率先提供此类解决方案，此后一直以最大的项目组合和最成熟的系统而领先。

### <a name="baremetal-benefits"></a>BareMetal 优势  

BareMetal 基础结构适用于需要证书才能运行企业应用程序的关键工作负载。 BareMetal 实例专为你准备，你将拥有对操作系统 (OS) 的完全访问权限（root 访问权限）。 你可以根据需要管理操作系统和应用程序安装。 为安全起见，可在 Azure 虚拟网络 (VNet) 中预配实例，而无需 Internet 连接。 只有在虚拟机 (VM) 上运行的服务，以及在同一第 2 层网络中的其他 Azure 服务才能与 BareMetal 实例通信。  

BareMetal 基础结构具有以下优势：

- 适用于专用工作负载的认证硬件
    - SAP（请参阅 [SAP 说明 #1928533](https://launchpad.support.sap.com/#/notes/1928533)。 你需要一个 SAP 帐户才能访问。）
    - Oracle（请参阅 [Oracle 文档 ID #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52)。 你需要一个 Oracle 帐户才能访问。）
- 非虚拟化监控 BareMetal 实例，单租户所有权
- Azure 托管应用程序 VM 与 BareMetal 实例之间的延迟非常低（0.35 毫秒）
- 所有闪存 SSD 和 NVMe
    - 最高 1 PB/租户 
    - IOPS 高达 120 万/租户 
    - 40/100-GB 的网络带宽
    - 可通过 NFS、ISCSI 和 FC 进行访问
- 冗余电源、电源、NIC、TOR、端口、WAN、存储和管理
- 用于在发生故障时更换的热备用（无需重新配置）
- 客户协调的维护时段
- 应用程序感知的快照、存档、镜像和克隆

## <a name="sku-availability-in-azure-regions"></a>Azure 区域中的 SKU 可用性

BareMetal 基础结构提供已针对专用工作负载认证的多个 SKU。 使用特定于工作负载的 SKU 满足你的需求。

- 大型实例 - 从双插槽到四插槽的系统。  
- 特大型实例 - 从四插槽到 20 插槽的系统。 

适用于专用工作负载的 BareMetal 基础结构可在以下 Azure 区域提供：
- 西欧
- 北欧
- 德国中西部 *区域支持
- 美国东部 2 *区域支持
- 美国东部 *区域支持
- 美国西部 *区域支持
- 美国西部 2 *区域支持
- 美国中南部

>[!NOTE]
>区域支持指的是区域中的可用性区域，可在其中的所有区域中部署 BareMetal 实例，以实现高复原能力和可用性。 此功能支持多站点主动-主动缩放。

## <a name="managing-baremetal-instances-in-azure"></a>在 Azure 中管理 BareMetal 实例 

根据不同的需求，BareMetal 基础结构的应用程序拓扑可能会很复杂。 你可以在一个或多个位置部署多个实例。 实例可以具有共享或专用存储，以及专用的 LAN 和 WAN 连接。 因此，对于 BareMetal 基础结构，Azure 提供咨询服务，由 CSA/GBB 在现场与你合作。 

当你预配完 BareMetal 基础结构后，操作系统、网络、存储卷、地区和区域中的位置以及位置之间的 WAN 连接均已预先配置完成。 你已经准备好注册你的操作系统许可证 (BYOL)、配置操作系统并安装应用程序层。

你将在 Azure 门户中看到所有 BareMetal 资源及其状态和属性。 你还可以运行实例，并从那里提交服务请求和支持票证。 

## <a name="operational-model"></a>操作模式

BareMetal 基础结构符合 ISO 27001、ISO 27017、SOC 1 和 SOC 2 标准。 它还使用自带许可 (BYOL) 模型：操作系统、专用工作负载和第三方应用程序。  

一旦收到根目录访问和完全控制，就要承担以下责任：
- 设计和实现备份与恢复解决方案、高可用性和灾难恢复。
- 操作系统和第三方软件的许可、安全性和支持。

Microsoft 负责：
- 为专用工作负载提供硬件。 
- 预配操作系统。

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="BareMetal 基础结构支持模型图。" border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal 实例标记

BareMetal 实例标记本身合并以下组件：

- 计算：基于 Intel Xeon 处理器代次的服务器，提供必要的计算功能，并针对专用工作负载进行了认证。

- **网络：** 统一的高速网络结构，与计算、存储和 LAN 组件互连。

- **存储：** 通过统一网络结构访问的基础结构。

在 BareMetal 标记的多租户基础结构中，客户部署在隔离的租户中。 在部署租户时，需要在 Azure 注册中命名一个 Azure 订阅。 此 Azure 订阅是对 BareMetal 实例计费的订阅。

>[!NOTE]
>部署 BareMetal 实例的客户会被隔离到租户中。 租户在网络、存储和计算层中相互隔离。 分配给不同租户的存储和计算单位在 BareMetal 实例上无法相互看到或进行通信。

## <a name="operating-system"></a>操作系统

在 BareMetal 实例的预配过程中，可以选择要安装在计算机上的操作系统。 

>[!NOTE]
>请记住，BareMetal 基础结构是 BYOL 模型。

可用的 Linux 操作系统版本包括：
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>存储

BareMetal 基础结构提供了高度冗余的 NFS 存储和光纤通道存储。 基础结构为 SAP、SQL 等企业工作负载提供深度整合。 它还提供了与应用程序一致的数据保护和数据管理功能。 自助服务管理工具提供节省空间的快照、克隆和精细复制功能，同时提供集中式监视。 基础结构可实现零 RPO 和 RTO 功能，以满足数据可用性和业务连续性需求。 

存储基础结构提供以下优势：
- 多达 4 x 100-GB 上行链路。
- 多达 32 GB 光纤通道上行链路。
- 所有闪存 SSD 和 NVMe 驱动器。
- 超低延迟和高吞吐量。
- 可扩展到多达 4 PB 的原始存储空间。 
- 多达 1100 万 IOPS。

支持以下数据访问协议： 
- iSCSI 
- NFS（v3 或 v4） 
- 光纤通道 
- NVMe over FC  

## <a name="networking"></a>网络

Azure 网络服务的体系结构是在 BareMetal 实例中成功部署专用工作负载的关键组件。 可能并非所有 IT 系统都已在 Azure 中。 Azure 提供网络技术，可让 Azure 充当本地软件部署的虚拟数据中心。 BareMetal 实例所需的 Azure 网络功能如下：

- Azure 虚拟网络连接到与本地网络资产连接的 Azure ExpressRoute 线路。
- 将本地环境连接到 Azure 的 ExpressRoute 线路的最小带宽应为 1 Gbps 或更高。
- Azure 中或完全在 Azure 中运行的扩展 Active directory 和 DNS。

使用 ExpressRoute，可在连接服务提供商的帮助下通过专用连接，将本地网络扩展到 Microsoft 云。 可以使用ExpressRoute Local实现本地位置和所需的 Azure 区域之间经济高效的数据传输。 你可以启用 ExpressRoute 高级版，将连接扩展为跨越地缘政治边界。 

BareMetal 实例是在 Azure VNet 服务器 IP 地址范围内预配的。

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal 基础结构图的架构示意图。" lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

所示体系结构分为三个部分：
- 左侧：显示运行不同应用程序的客户本地基础结构，通过合作伙伴或本地边缘路由器（如 Equinix）进行连接。 有关详细信息，请参阅[连接服务提供商和位置：Azure ExpressRoute](../expressroute/expressroute-locations.md)。
- 中间：显示使用 Azure 订阅预配的 [ExpressRoute](../expressroute/expressroute-introduction.md)，提供与 Azure 边缘网络的连接。
- 右侧：显示 Azure IaaS，在这种情况下，使用 VM 来托管应用程序，这些应用程序是在 Azure 虚拟网络中预配的。
- 底部：显示如何使用通过 [Expressroute FastPath](../expressroute/about-fastpath.md) 启用的 ExpressRoute 网关实现 BareMetal 连接，并提供低延迟。   
   >[!TIP]
   >为了支持这种情况，ExpressRoute 网关应该为 UltraPerformance。 有关更多信息，请参阅[关于 ExpressRoute 虚拟网关](../expressroute/expressroute-about-virtual-network-gateways.md)。

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 门户确定 BareMetal 实例并与之交互。

> [!div class="nextstepaction"]
> [通过 Azure 门户管理 BareMetal 实例](connect-baremetal-infrastructure.md)
