---
title: 体系结构：迁移到 Azure 虚拟 WAN
description: 了解如何从现有的客户管理型中心辐射拓扑迁移到利用 Microsoft 管理型虚拟 WAN 中心的设计。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: 3fa07445b667d59fba81040c70a111c4a0f94670
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2021
ms.locfileid: "110580037"
---
# <a name="migrate-to-azure-virtual-wan"></a>迁移到 Azure 虚拟 WAN

有了 Azure 虚拟 WAN，公司可以简化其全球连接，以便受益于规模广泛的 Microsoft 全球网络。 本文主要面向那些希望从现有的客户托管型中心辐射拓扑迁移到利用 Microsoft 托管型虚拟 WAN 中心的设计的公司，为其提供了技术详细信息。

若要了解 Azure 虚拟 WAN 为那些采用以云为中心的新式企业级全球网络的企业带来的好处，请参阅[全球传输网络体系结构和虚拟 WAN](virtual-wan-global-transit-network-architecture.md)。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/hub-spoke.png" alt-text="中心和辐射":::
**图：Azure 虚拟 WAN**

数千客户已采用 Azure 中心辐射连接模型，以利用 Azure 网络的默认传递路由行为来构建简单且可缩放的云网络。 Azure 虚拟 WAN 在这些概念的基础之上又引入了新的功能，不仅允许本地位置和 Azure 之间的全球连接拓扑，还允许客户利用大规模的 Microsoft 网络来扩展其现有的全球网络。

本文介绍如何将客户管理的现有中心辐射型环境迁移到基于 Azure 虚拟 WAN 的拓扑。

## <a name="scenario"></a>方案

Contoso 是一家全球金融组织，在欧洲和亚洲设有办事处。 他们计划将其现有的应用程序从本地数据中心迁移到 Azure，并基于客户管理的中心辐射体系结构建立了基础设计，其中包括用于混合连接的区域中心虚拟网络。 在迁移到基于云的技术的过程中，网络团队的任务是确保针对不断发展的业务优化其连接。

下图显示了现有全球网络的概览性视图，其中包括与多个 Azure 区域的连接。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png" alt-text="Contoso 现有网络拓扑":::
**图：Contoso 现有网络拓扑**

从现有网络拓扑中可了解以下几点：

* 已在多个区域使用了中心辐射型拓扑，其中包括用于连回到公共专用广域网 (WAN) 的 ExpressRoute 线路。

* 其中一些站点还将 VPN 隧道与 Azure 直接连接，以访问云中托管的应用程序。

## <a name="requirements"></a>要求

网络团队的任务是提供一个全球网络模型，该模型可以支持 Contoso 向云的迁移，且必须在成本、规模和性能方面进行优化。 总而言之，需要满足以下要求：

* 为总部 (HQ) 和分支机构提供云托管应用程序的优化路径。
* 消除 VPN 终端对现有本地数据中心 (DC) 的依赖，同时保留以下连接路径：
  * **分支到 VNet**：连接 VPN 的办事处必须能够访问本地 Azure 区域中迁移到云的应用程序。
  * **分支 -> 中心 -> 中心 -> VNet**：连接 VPN 的办事处必须能够访问远程 Azure 区域中迁移到云的应用程序。
  * **分支到分支**：连接区域 VPN 的办事处必须能够相互通信，且能够与连接 ExpressRoute 的 HQ/DC 站点通信。
  * **分支 -> 中心 -> 中心 -> 分支**：连接全球分布 VPN 的办事处必须能够相互通信，且能够与任何连接 ExpressRoute 的 HQ/DC 站点通信。
  * **分支到 Internet**：连接的站点必须能够与 Internet 通信。 必须筛选并记录此流量。
  * **VNet 到 VNet**：同一区域中的辐射虚拟网络必须能够相互通信。
  * **VNet -> 中心 -> 中心 -> VNet**：不同区域中的辐射虚拟网络必须能够相互通信。
* 使 Contoso 漫游用户（笔记本电脑和手机）无需连接公司网络即可访问公司资源。

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Azure 虚拟 WAN 体系结构

下图显示了更新后的目标拓扑的概览性视图，该拓扑使用 Azure 虚拟 WAN 来满足上一部分详述的要求。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/vwan-architecture.png" alt-text="Contoso 虚拟 WAN 体系结构":::
**图：Azure 虚拟 WAN 体系结构**

摘要：

* 欧洲 HQ 仍连接 ExpressRoute，而欧洲本地 DC 已完全迁移到 Azure，现已停用。
* 亚洲 DC 和 HQ 仍连接专用 WAN。 Azure 虚拟 WAN 现用于扩展本地运营商网络并提供全球连接。
* 西欧和东南亚 Azure 区域均部署了 Azure 虚拟 WAN 中心，以便为连接 ExpressRoute 和 VPN 的设备提供连接中心。
* 中心还通过全球网格网络的 OpenVPN 连接，为使用多种客户端类型的漫游用户提供 VPN 终端，这样，用户不仅可以访问已迁移到 Azure 的应用程序，而且还能访问保留在本地的任何资源。
* Azure 虚拟 WAN 提供的虚拟网络中的资源的 Internet 连接。

同样由 Azure 虚拟 WAN 提供的远程站点 Internet 连接。 通过合作伙伴集成支持的本地 Internet 中断，用于优化对 Microsoft 365 等 SaaS 服务的访问。

## <a name="migrate-to-virtual-wan"></a>迁移到虚拟 WAN

本部分介绍迁移到 Azure 虚拟 WAN 的各个步骤。

### <a name="step-1-single-region-customer-managed-hub-and-spoke"></a>步骤 1：单区域客户托管中心辐射

下图显示 Azure 虚拟 WAN 推出之前 Contoso 的单区域拓扑：

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure1.png" alt-text="单区域拓扑":::
图 1：**单区域手动中心辐射**

与中心辐射方法一致，客户托管的中心虚拟网络包含几个功能块：

* 共享服务（多个辐射网络所需的任何常用功能）。 示例：Contoso 在基础结构即服务 (IaaS) 虚拟机上使用 Windows Server 域控制器。
* IP/路由防火墙服务由第三方网络虚拟设备提供，可实现辐射网络到辐射网络的第 3 层 IP 路由。
* Internet 入口/出口服务，其中包括用于入站 HTTPS 请求的 Azure 应用程序网关，以及在虚拟机上运行且用于已筛选的 Internet 资源出站访问的第三方代理服务。
* ExpressRoute 和 VPN 虚拟网关，用于连接到本地网络。

### <a name="step-2-deploy-virtual-wan-hubs"></a>步骤 2：部署虚拟 WAN 中心

在每个区域中部署虚拟 WAN 中心。 按以下文章所述，使用 VPN 和 ExpressRoute 功能设置虚拟 WAN 中心：

* [教程：使用 Azure 虚拟 WAN 创建站点到站点连接](virtual-wan-site-to-site-portal.md)
* [教程：使用 Azure 虚拟 WAN 创建 ExpressRoute 关联](virtual-wan-expressroute-portal.md)

> [!NOTE]
> 若要启用本文所述的某些流量路径，Azure 虚拟 WAN 必须使用标准 SKU。
>

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure2.png" alt-text="部署虚拟 WAN 中心":::
图 2：**从客户托管的中心辐射型拓扑迁移到虚拟 WAN**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>步骤 3：将远程站点（ExpressRoute 和 VPN）连接到虚拟 WAN

将虚拟 WAN 中心连接到现有 ExpressRoute 线路，并通过 Internet 在任何远程分支上设置站点到站点 VPN。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure3.png" alt-text="将远程站点连接到虚拟 WAN":::
图 3：**从客户托管的中心辐射型拓扑迁移到虚拟 WAN**

此时，本地网络设备将开始接收路由，这些路由表明分配给虚拟 WAN 托管中心 VNet 的 IP 地址空间。 在此阶段，连接 VPN 的远程分支将在辐射虚拟网络中显示两条指向任何现有应用程序的路径。 这些设备应配置为继续使用指向客户托管中心的隧道，以确保转换阶段的对称路由。

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>步骤 4：通过虚拟 WAN 测试混合连接

使用托管的虚拟 WAN 中心进行生产连接之前，建议你设置测试性辐射虚拟网络和虚拟 WAN VNet 连接。 继续执行后续步骤之前，通过 ExpressRoute 和站点到站点 VPN 验证此测试环境的连接是否正常工作。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure4.png" alt-text="通过虚拟 WAN 测试混合连接":::
图 4：**从客户托管的中心辐射型拓扑迁移到虚拟 WAN**

在此阶段，必须认识到，原来的客户管理的中心虚拟网络和新的虚拟 WAN 中心都连接到同一条 ExpressRoute 线路。 因此，我们有一条可用于使两种环境中的分支进行通信的通信路径。 例如，来自连接到客户管理中心虚拟网络的分支的流量会遍历用于 ExpressRoute 线路的 MSEE 设备，以访问通过 VNet 连接连接到新虚拟 WAN 中心的任何分支。 这样就可以在步骤 5 中分阶段迁移分支。

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>步骤 5：将连接转换到虚拟 WAN 中心

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure5.png" alt-text="将连接转换到虚拟 WAN 中心":::
图 5：**从客户托管的中心辐射型拓扑迁移到虚拟 WAN**

a。 删除辐射虚拟网络到旧的客户托管中心的现有对等连接。 步骤 a-c 完成之前，无法访问辐射虚拟网络中的应用程序。

**b**. 通过 VNet 连接将辐射虚拟网络连接到虚拟 WAN 中心。

**c**. 删除之前在辐射虚拟网络中使用的用于辐射网络到辐射网络通信的任何用户定义路由 (UDR)。 虚拟 WAN 中心内提供的动态路由现已启用此路径。

**d**. 客户托管中心内的现有 ExpressRoute 和 VPN 网关现已停用，以便执行下一个步骤 (e)。

**e**. 通过新的 VNet 连接将旧的客户托管中心（中心虚拟网络）连接到虚拟 WAN 中心。

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>步骤 6：旧中心成为共享服务辐射网络

现已重新设计了 Azure 网络，使虚拟 WAN 中心成为了新拓扑的中心点。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure6.png" alt-text="旧中心成为共享服务辐射网络":::
**图 6：从客户托管的中心辐射型拓扑迁移到虚拟 WAN**

由于虚拟 WAN 中心是托管实体，且不允许部署虚拟机之类的自定义资源，因此共享服务功能块现以辐射虚拟网络形式存在，该网络通过 Azure 应用程序网关或网络虚拟设备托管 Internet 入口等功能。 现在，共享服务环境与后端虚拟机之间的流量在虚拟 WAN 托管的中心内传输。

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>步骤 7：优化本地连接以充分利用虚拟 WAN

在此阶段，Contoso 基本已将业务应用程序迁移到 Microsoft 云，仅少量旧版应用程序保留在本地 DC。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure7.png" alt-text="优化本地连接以充分利用虚拟 WAN":::
图 7：**从客户托管的中心辐射型拓扑迁移到虚拟 WAN**

为利用 Azure 虚拟 WAN 的全部功能，Contoso 决定停用其旧的本地 VPN 连接。 继续访问 HQ 或 DC 网络的任何分支都能够使用 Azure 虚拟 WAN 的内置传输路由在 Microsoft 全球网络中传输。

> [!NOTE]
> 对于希望利用 Microsoft 主干网提供 ExpressRoute 到 ExpressRoute 间传输（图 7 中未显示）的客户，ExpressRoute Global Reach 必不可少。
>

## <a name="end-state-architecture-and-traffic-paths"></a>最终状态体系结构和流量路径

:::image type="content" source="./media/migrate-from-hub-spoke-topology/figure8.png" alt-text="最终状态体系结构和流量路径":::
**图：双区域虚拟 WAN**

本节通过介绍一些示例流量来概述此拓扑如何满足初始要求。

### <a name="path-1"></a>路径 1

路径 1 显示了从亚洲连接 S2S VPN 的分支到东南亚区域 Azure VNet 的通信流。

流量按如下方式路由：

* 亚洲分支通过启用了可复原 S2S BGP 的隧道连接到东南亚虚拟 WAN 中心。

* 亚洲虚拟 WAN 中心将流量本地路由到连接的 VNet。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow1.png" alt-text="流量流 1":::

### <a name="path-2"></a>路径 2

路径 2 显示了从连接 ExpressRoute 的欧洲总部到东南亚区域 Azure VNet 的通信流。

流量按如下方式路由：

* 欧洲总部通过 ExpressRoute 线路连接到欧洲西部虚拟 WAN 中心。

* 虚拟 WAN 中心到中心全球连接确保流量能够传输到远程区域中连接的 VNet。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow2.png" alt-text="流量流 2":::

### <a name="path-3"></a>路径 3

路径 3 显示了从连接到专用 WAN 的亚洲本地 DC 到连接欧洲 S2S 的分支的通信流。

流量按如下方式路由：

* 亚洲 DC 连接到本地专用 WAN 运营商。

* 专用 WAN 中的 ExpressRoute 线路本地终端连接到东南亚虚拟 WAN 中心。

* 虚拟 WAN 中心之间的全球连接可实现流量传输。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow3.png" alt-text="流量流 3":::

### <a name="path-4"></a>路径 4

路径 4 显示了从东南亚区域 Azure VNet 到西欧区域 Azure VNet 的通信流。

流量按如下方式路由：

* 虚拟 WAN 中心之间的全球连接确保无需进一步的用户配置即可本地传输所有连接的 Azure VNet。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow4.png" alt-text="流量流 4":::

### <a name="path-5"></a>路径 5

路径 5 显示了从漫游 VPN (P2S) 用户到西欧区域 Azure VNet 的通信流。

流量按如下方式路由：

* 笔记本电脑和移动设备用户使用 OpenVPN 客户端实现与西欧 P2S VPN 网关的透明连接。

* 西欧虚拟 WAN 中心将流量本地路由到连接的 VNet。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow5.png" alt-text="流量流 5":::

## <a name="security-and-policy-control-via-azure-firewall"></a>通过 Azure 防火墙的安全和策略控制

Contoso 现已按照本文前面部分讨论的要求验证了所有分支与 VNet 之间的连接。 为满足对安全控制和网络隔离的要求，他们需要继续通过中心网络来分离和记录流量。 以前，此功能是由网络虚拟设备 (NVA) 执行的。 Contoso 还希望停用其现有代理服务，并利用本机 Azure 服务进行出站 Internet 筛选。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/security-policy.png" alt-text="通过 Azure 防火墙的安全和策略控制":::
**图：虚拟 WAN（安全虚拟中心）中的 Azure 防火墙**

若要将 Azure 防火墙引入虚拟 WAN 中心以启用统一的策略控制点，需要执行以下高级步骤。 有关此过程的详细信息以及安全虚拟中心的概念，请参阅 [Azure 防火墙管理器](../firewall-manager/index.yml)。

1. 创建 Azure 防火墙策略。
2. 将防火墙策略链接到 Azure 虚拟 WAN 中心。 此步骤允许现有的虚拟 WAN 中心充当安全虚拟中心，并部署所需的 Azure 防火墙资源。

> [!NOTE]
> 存在与使用安全虚拟中心（包括区域间流量）相关的约束。 有关详细信息，请参阅[防火墙管理器 - 已知问题](../firewall-manager/overview.md#known-issues)。
>

以下路径显示了通过使用 Azure 安全虚拟中心启用的连接路径：

### <a name="path-6"></a>路径 6

路径 6 显示了同一区域中 VNet 之间的安全通信流。

流量按如下方式路由：

* 连接到同一安全虚拟中心的虚拟网络现通过 Azure 防火墙路由流量。

* Azure 防火墙可将策略应用这些流量流。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow6.png" alt-text="流量流 6":::

### <a name="path-7"></a>路径 7

路径 7 显示了 Azure VNet 到 Internet 或第三方安全服务的通信流。

流量按如下方式路由：

* 连接到安全虚拟中心的虚拟网络使用安全中心作为 Internet 访问的中心点，可以将流量发送到 Internet 上的公共目标位置。

* 可使用 Azure 防火墙 FQDN 规则以本地方式筛选此流量，也可将其发送到第三方安全服务进行检查。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow7.png" alt-text="流量流 7":::

### <a name="path-8"></a>路径 8

路径 8 显示了分支到 Internet 或第三方安全服务的通信流。

流量按如下方式路由：

* 连接到安全虚拟中心的分支使用安全中心作为 Internet 访问的中心点，可以将流量发送到 Internet 上的公共目标位置。

* 可使用 Azure 防火墙 FQDN 规则以本地方式筛选此流量，也可将其发送到第三方安全服务进行检查。

:::image type="content" source="./media/migrate-from-hub-spoke-topology/flow8.png" alt-text="流量流 8":::

## <a name="next-steps"></a>后续步骤

详细了解 [Azure 虚拟 WAN](virtual-wan-about.md)。
