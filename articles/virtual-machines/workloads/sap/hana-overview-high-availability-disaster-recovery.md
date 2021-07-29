---
title: Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复 | Microsoft Docs
description: 了解如何为 Azure 上的 SAP HANA（大型实例）建立高可用性并规划灾难恢复。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/8/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: df7d33a08b9d3eb868cecdec7753744782590b51
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111809861"
---
# <a name="sap-hana-large-instances-high-availability-and-disaster-recovery-on-azure"></a>Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复 

>[!IMPORTANT]
>本文档不会取代 SAP HANA 管理文档或 SAP 说明。 我们假定你具备 SAP HANA 管理和操作方面的专业知识，尤其是涉及到备份、还原、高可用性和灾难恢复主题。

本文概述了 Azure 上的 SAP HANA 大型实例（也称为“BareMetal 基础结构”）的高可用性 (HA) 和灾难恢复 (DR)。 我们也会详细介绍与 HA 和 DR 相关的一些要求和注意事项。

本文档中所述的部分流程已简化。 它们并不是要包含在操作手册中的详细步骤。 如果要为配置创建操作手册，请使用你的特定 HANA 版本和发行版本运行并测试你的流程。 然后，可以记录配置的具体过程。

## <a name="ha-and-dr"></a>HA 和 DR

高可用性和灾难恢复是运行任务关键型 Azure 上的 SAP HANA（大型实例）服务器的重要方面。 请务必咨询 SAP、系统集成商和/或 Microsoft，以便正确构建和实施适当的高可用性和灾难恢复策略。 此外，应考虑到与环境相关的恢复点目标 (RPO) 和恢复时间目标 (RTO)。

Microsoft 通过 HANA 大型实例支持一些 SAP HANA 高可用性功能。 这些功能包括：

- **存储复制**：存储系统能够将所有数据复制到另一个 Azure 区域中的另一个 HANA 大型实例戳记。 SAP HANA 的工作独立于此方法。 此功能是为 HANA 大型实例提供的默认灾难恢复机制。
- **HANA 系统复制**：将 [SAP HANA 中的所有数据](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)复制到独立的 SAP HANA 系统。 通过定期复制数据最大限度地减小 RTO。 SAP HANA 支持异步模式、内存中同步模式和同步模式。 仅针对同一数据中心内或距离不超过 100 公里范围内的 SAP HANA 系统使用同步模式。 在 HANA 大型实例戳的当前设计中，HANA 系统复制仅可用于在一个区域内实现高可用性。 HANA 系统复制要求使用第三方反向代理或路由组件将灾难恢复配置传送到另一 Azure 区域。 
- **主机自动故障转移**：用于替代 HANA 系统复制的 SAP HANA 本地故障恢复解决方案。 如果主节点不可用，可在横向扩展模式下配置一个或多个备用 SAP HANA 节点，SAP HANA 会自动故障转移到备用节点。

Azure 上的 SAP HANA（大型实例）在四个地缘政治区域（美国、澳大利亚、欧洲和日本）的两个 Azure 区域中提供。 一个地缘政治区域内托管 HANA 大型实例 (HLI) 标记的两个区域与单独的专用网络线路连接。 这些 HLI 用于复制存储快照，以提供灾难恢复方法。 默认情况下不会设置复制，只针对订购灾难恢复功能的客户进行设置。 存储复制取决于 HANA 大型实例存储快照的使用情况。 无法选择位于另一个地缘政治区域的 Azure 区域作为 DR 区域。

## <a name="currently-supported-options"></a>当前支持的选项

下表显示了当前支持的高可用性和灾难恢复方法与组合：

| HANA 大型实例支持的方案 | 高可用性选项 | 灾难恢复选项 | 注释 |
| --- | --- | --- | --- |
| 单节点 | 不可用。 | 专用 DR 设置。<br /> 多用途 DR 设置。 | |
| 主机自动故障转移：横向扩展（使用或不使用备用）<br /> 包括 1+1 | 在备用节点充当活动角色的情况下可行。<br /> HANA 控制角色切换。 | 专用 DR 设置。<br /> 多用途 DR 设置。<br /> 使用存储复制实现 DR 同步。 | HANA 卷集将附加到所有节点。<br /> DR 站点必须拥有相同的节点数。 |
| HANA 系统复制。 | 在使用主要或辅助设置的情况下可行。<br /> 在故障转移案例中，辅助角色变为主要角色。<br /> HANA 系统复制和 OS 控制故障转移。 | 专用 DR 设置。<br /> 多用途 DR 设置。<br /> 使用存储复制实现 DR 同步。<br /> 在没有第三方组件的情况下，尚且无法使用 HANA 系统复制实现 DR。 | 单独一组磁盘卷附加到每个节点。<br /> 仅生产站点中的辅助副本磁盘卷被复制到 DR 位置。<br /> DR 站点中需要一组卷。 | 

专用 DR 设置是在不用于运行任何其他工作负荷或非生产系统的 DR 站点中部署 HANA 大型实例单元。 此单元是被动单元，仅当执行灾难故障转移时才部署。 对于大多数客户来说，这种设置不是首选选项。

要了解体系结构的存储布局和以太网详细信息，请参阅[HLI 支持的方案](hana-supported-scenario.md)。

> [!NOTE]
> [SAP HANA MCOD 部署](https://launchpad.support.sap.com/#/notes/1681092)（一个单元上多个 HANA 实例）作为覆盖方案，与表中列出的 HA 和 DR 方法配合使用。 例外情况是将 HANA 系统复制与基于 Pacemaker 的自动故障转移群集配合使用。 这种情况仅支持一个单元一个 HANA 实例。 而对于 [SAP HANA MDC](https://launchpad.support.sap.com/#/notes/2096000) 部署，如果部署了多个租户，仅非基于存储的 HA 和 DR 方法可用。 若仅部署了一个租户，则上面列出的所有方法均有效。  

多用途 DR 设置在运行非生产工作负荷的 DR 站点中部署 HANA 大型实例单元。 如果发生灾难，可关闭非生产系统，装载存储复制的（添加的）卷集，并启动生产 HANA 实例。 使用 HANA 大型实例灾难恢复功能的大多数客户都使用此配置。 

可在以下 SAP 文章中找到有关 SAP HANA 高可用性的详细信息： 

- [SAP HANA 高可用性白皮书](https://go.sap.com/documents/2016/05/f8e5eeba-737c-0010-82c7-eda71af511fa.html)
- [SAP HANA 管理指南](https://help.sap.com/hana/SAP_HANA_Administration_Guide_en.pdf)
- [有关 SAP HANA 系统复制的 SAP HANA 学院视频](https://scn.sap.com/community/hana-in-memory/blog/2015/05/19/sap-hana-system-replication)
- [SAP 支持说明 #1999880 - 有关 SAP HANA 系统复制的常见问题](https://apps.support.sap.com/sap/support/knowledge/preview/en/1999880)
- [SAP 支持说明 #2165547 - SAP HANA 系统复制环境中的 SAP HANA 备份和还原](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3231363535343726)
- [SAP 支持说明 #1984882 - 使用 SAP HANA 系统复制在最大限度地减少停机时间/不停机的情况下实现硬件更换](https://websmp230.sap-ag.de/sap(bD1lbiZjPTAwMQ==)/bc/bsp/sno/ui_entry/entry.htm?param=69765F6D6F64653D3030312669765F7361706E6F7465735F6E756D6265723D3139383438383226)

## <a name="network-considerations-for-disaster-recovery-with-hana-large-instances"></a>使用 HANA 大型实例实现灾难恢复的网络注意事项

若要利用 HANA 大型实例的灾难恢复功能，需要规划好与两个 Azure 区域之间的网络连接。 需使用一条 Azure ExpressRoute 线路从本地连接到主要 Azure 区域，使用另一个线路从本地连接到灾难恢复区域。 这种措施可以应对 Azure 区域（包括 Microsoft 企业边缘路由器 (MSEE) 位置）中出现的问题。

还可以将连接到某个区域中 Azure 上的 SAP HANA（大型实例）的所有 Azure 虚拟网络都连接到 ExpressRoute 线路，此线路将连接其他区域的 HANA 大型实例。 通过此“跨连接”，1 号区域的 Azure 虚拟网络中运行的服务可以连接到 2 号区域中的 HANA 大型实例单元，也可以从后者连接到前者。 这种措施可以避免当 Azure 离线时，只有一个 MSEE 位置连接到本地位置。

下图展示了灾难恢复方案的弹性配置：

![灾难恢复的最佳配置](./media/hana-overview-high-availability-disaster-recovery/image1-optimal-configuration.png)


## <a name="other-requirements-with-hana-large-instances-storage-replication-for-disaster-recovery"></a>使用 HANA 大型实例存储复制实现灾难恢复的其他要求

- 订购的 Azure 上的 SAP HANA（大型实例）的 SKU 大小必须与生产 SKU 大小相同，并且应部署在灾难恢复区域中。 在当前客户部署中，这些实例用于运行非生产 HANA 实例。 这些配置被称为“多用途 DR 设置”。   
- 在 DR 站点上为想要在灾难恢复站点中恢复的每个 Azure 上的 SAP HANA（大型实例）SKU 订购更多的存储。 购买更多的存储可以分配存储卷。 可以分配用作从生产 Azure 区域复制到灾难恢复 Azure 区域的存储复制目标的存储卷。
- 你可以在主站点设置 SAP HANA 系统复制，以及到 DR 站点的基于存储的复制。 然后，必须在 DR 站点购买更多的存储，以便将主节点和辅助节点的数据复制到 DR 站点。

## <a name="next-steps"></a>后续步骤

了解在 HANA 大型实例上备份和还原 SAP HANA。

> [!div class="nextstepaction"]
> [在 HANA 大型实例上备份和还原 SAP HANA](hana-backup-restore.md)
