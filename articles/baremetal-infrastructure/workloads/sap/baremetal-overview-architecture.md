---
title: Azure 中的 BareMetal 基础结构预览版概述
description: Azure 中的 BareMetal 基础结构概述。
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: eb4dc129719dc410f7101598e3d72e68f17809c1
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97860979"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>什么是 Azure 上的 BareMetal 基础结构预览版？

Azure BareMetal 基础结构提供了一个用于迁移企业自定义工作负荷的安全解决方案。 BareMetal 实例是分配给你的非共享主机/服务器硬件。 它将本地解决方案与需要经过认证的硬件、许可和支持协议的专用工作负荷进行移植。 Azure 会处理基础结构监视和维护，而来宾操作系统 (OS) 监视和应用程序监视在所有权范围内。

BareMetal 基础结构提供了一个用于实现基础结构现代化的途径，同时保持现有的投资和体系结构。 借助 BareMetal 基础结构，你可以将专用工作负荷引入 Azure，使你能够访问 Azure 服务并将其与低延迟集成。

## <a name="sku-availability-in-azure-regions"></a>Azure 区域中的 SKU 可用性
适用于专用工作负荷的 BareMetal 基础结构可用，从基于修订4.2 的四个区域开始， (Rev 4.2) stamp：
- 西欧
- 北欧
- 美国东部 2
- 美国中南部

>[!NOTE]
>**修订版 4.2** 是使用现有 Rev 4 体系结构的最新更名 BareMetal 基础结构。  修订版4更接近于 Azure 虚拟机 (VM) 主机。 它显著改进了在修订版4戳记或 rows 中部署的 Azure Vm 和 BareMetal 实例单位之间的网络延迟。  可以通过 Azure 门户访问和管理 BareMetal 实例。 

## <a name="support"></a>支持
BareMetal 基础结构是 ISO 27001、ISO 27017、SOC 1 和 SOC 2 兼容。  它还使用自带许可证 (BYOL) 模型： OS、专用工作负荷和第三方应用程序。  

一旦你收到根目录访问和完全控制，你就会承担以下责任：
- 设计和实现备份和恢复解决方案、高可用性和灾难恢复
- 操作系统和第三方软件的许可、安全和支持

Microsoft 负责：
- 为专用工作负荷提供硬件 
- 预配 OS

:::image type="content" source="media/baremetal-support-model.png" alt-text="BareMetal 基础结构支持模型" border="false":::

## <a name="compute"></a>计算
BareMetal 基础结构为专用工作负荷提供多个 Sku。 可用的 Sku 范围从小型双插槽系统到24插槽系统。 使用特定于工作负荷的 Sku 来实现专用工作负荷。

BareMetal 实例标记本身将以下组件组合在一起：

- **计算：** 基于不同代的 Intel 强处理器的服务器，这些处理器提供必要的计算功能并针对专用工作负荷进行认证。

- **网络：** 统一的高速网络结构互连计算、存储和 LAN 组件。

- **存储：** 通过统一网络结构访问的基础结构。

在 BareMetal 戳记的多租户基础结构中，客户部署在隔离的租户中。 部署租户时，请在 Azure 注册中命名 Azure 订阅。 此 Azure 订阅是对 BareMetal 实例计费的订阅。

>[!NOTE]
>部署在 BareMetal 实例中的客户会隔离到租户中。 租户在网络、存储和计算层中相互隔离。 分配给不同租户的存储和计算单元在 BareMetal 实例上无法相互查看或通信。

## <a name="os"></a>操作系统
在 BareMetal 实例的预配过程中，可以选择要安装在计算机上的操作系统。 

>[!NOTE]
>请记住，BareMetal 基础结构是 BYOL 模型。

可用的 Linux OS 版本如下：
- Red Hat Enterprise Linux (RHEL) 7。6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>存储
基于特定 SKU 类型的 BareMetal 实例附带预定义的 NFS 存储，适用于特定工作负荷类型。 设置 BareMetal 时，你可以通过提交支持请求来根据你的预计增长情况预配更多存储。 所有存储都附带版本4.2 中的所有闪存磁盘，并支持 NFSv3 和 NFSv4。 较新版本 4.5 NVMe SSD 将可用。 有关存储大小的详细信息，请参阅 [BareMetal 工作负荷类型](../../../virtual-machines/workloads/sap/get-started.md) 部分。

>[!NOTE]
>默认情况下，用于 BareMetal 的存储满足 [美国联邦信息处理标准 (FIPS) 发布 140-2](/microsoft-365/compliance/offering-fips-140-2) 要求提供静态加密。 数据将安全地存储在磁盘上。

## <a name="networking"></a>网络
Azure 网络服务的体系结构是在 BareMetal 实例中成功部署专用工作负荷的关键组件。 可能并非所有 IT 系统都已位于 Azure 中。 Azure 为你提供网络技术，使 Azure 看起来像是一个虚拟数据中心，即本地软件部署。 BareMetal 实例所需的 Azure 网络功能如下：

- Azure 虚拟网络连接到连接到本地网络资产的 ExpressRoute 线路。
- 将本地连接到 Azure 的 ExpressRoute 线路的最小带宽应为 1 Gbps 或更高。
- Azure 中的扩展 Active directory 和 DNS，或在 Azure 中完全运行。

使用 ExpressRoute，你可以通过与连接提供商提供的帮助，通过专用连接将本地网络扩展到 Microsoft 云。 可以启用 **Expressroute 高级版** 来跨地缘政治边界扩展连接，或使用 **expressroute 本地** 在所需的 Azure 区域附近的位置之间进行经济高效的数据传输。

可在 Azure VNET 服务器 IP 地址范围内预配 BareMetal 实例。

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal 基础结构示意图" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

所示体系结构分为三个部分：
- **Left：** 显示客户的本地基础结构，该基础结构运行不同的应用程序，通过合作伙伴或本地边缘路由器（如 Equinix）进行连接。 有关详细信息，请参阅 [连接提供商和位置： Azure ExpressRoute](../../../expressroute/expressroute-locations.md)。
- **Center：** 显示使用 azure 订阅连接到 azure 边缘网络的 [ExpressRoute](../../../expressroute/expressroute-introduction.md) 。
- **Right：** 显示 azure IaaS，在这种情况下，使用 vm 来托管你的应用程序，这些应用程序是在 Azure 虚拟网络中预配的。
- **下：** 显示如何将 Expressroute 网关与 [Expressroute FastPath](../../../expressroute/about-fastpath.md) for BareMetal connectivity 一起使用，以实现低延迟。   
   >[!TIP]
   >为支持这种情况，ExpressRoute 网关应为 UltraPerformance。  有关详细信息，请参阅 [关于 ExpressRoute 虚拟网络网关](../../../expressroute/expressroute-about-virtual-network-gateways.md)。

## <a name="next-steps"></a>后续步骤

下一步是了解如何通过 Azure 门户确定 BareMetal 实例单位并与其交互。

> [!div class="nextstepaction"]
> [通过 Azure 门户管理 BareMetal 实例](baremetal-infrastructure-portal.md)