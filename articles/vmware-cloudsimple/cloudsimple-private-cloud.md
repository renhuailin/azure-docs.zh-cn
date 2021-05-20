---
title: Azure VMware Solution by CloudSimple - 私有云
description: 了解 CloudSimple 的概念和优点，其中包括完整的 VMware 操作连续性、与现有工具、技能和过程的兼容性。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e2096e8c0ebfb233c7449510bf0bc8e80b56231e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140710"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple 私有云概述

CloudSimple 会在几分钟内将 VMware 工作负载转换并扩展到公有云。 利用 CloudSimple 服务，可以在 Azure 裸机基础结构上部署本机 VMware。 部署位于 Azure 位置，并与 Azure 云的其余部分完全集成。

CloudSimple 解决方案提供完整的 VMware 操作连续性。 此解决方案提供了公有云的以下优势：

* 弹性
* 创新
* 效率

通过 CloudSimple，可从云使用模型受益，该模型会降低总拥有成本。 它还提供按需预配、即用即付和容量优化功能。

CloudSimple 与以下内容完全兼容：

* 现有工具
* 技能
* 进程

由于这种兼容性，团队能够在 Azure 云中管理工作负载，而不会中断以下类型的策略：

* 网络
* 安全性  
* 数据保护  
* 审核

CloudSimple 管理基础结构以及所有必需的网络和管理服务。 通过使用 CloudSimple 服务，团队能够专注于：

* 业务价值
* 应用程序预配
* 业务连续性
* 支持
* 策略强制执行

## <a name="private-cloud-environment-overview"></a>私有云环境概述

私有云是一种独立的 VMware 堆栈，它支持：

* ESXi 主机
* vCenter
* vSAN
* NSX

私有云通过 CloudSimple 门户进行管理。 它们在各自的管理域中具有各自的 vCenter 服务器。

此堆栈运行于：

* 专用节点
* 独立裸机硬件节点

用户通过本机 VMware 工具使用此堆栈，其中包括：

* vCenter
* NSX 管理器

可以在 Azure 位置中部署专用节点。 然后，可以通过 Azure 和 CloudSimple 管理它们。 私有云由一个或多个 vSphere 群集组成，每个群集包含 3 到 16 个节点。

你可以使用购买的即用即付节点或预留的专用节点来创建私有云。

可以使用以下连接将私有云连接到本地环境和 Azure 网络：

* 安全
* 专用 VPN
* Azure ExpressRoute

根据设计，私有云环境会消除单一故障点：

* ESXi 群集配置为具有 vSphere 高可用性，并且大小设置为至少有一个备用节点用于复原。
* vSAN 提供冗余的主存储。 vSan 需要至少三个节点以便防范单一故障。 可以配置 vSAN，以便为更大的群集提供更强的复原能力。
* 可以为 vCenter、PSC 和 NSX 管理器 VM 配置 RAID-10 存储策略，防范存储故障。 vSphere HA 可防范节点和网络故障。

## <a name="scenarios-for-deploying-a-private-cloud"></a>部署私有云的方案

下面是一些私有云部署用例示例。

### <a name="data-center-retirement-or-migration"></a>数据中心停用或迁移

* 达到现有数据中心的限制或刷新硬件时，可获得更多容量。
* 在云中添加所需的容量，消除管理硬件刷新带来的烦恼。
* 与执行耗时的转换或重新构建体系结构相比，降低了云迁移的风险和成本。
* 使用熟悉的 VMware 工具和技能来加速云迁移。 在云中，按照自己的节奏使用 Azure 服务实现应用程序的现代化。

### <a name="expand-on-demand"></a>按需扩展

* 扩展到云以满足意外的需求，如新开发环境或季节性容量突增。
* 按需创建新容量，并只在需要时保留该容量。
* 跨本地和云利用相同的体系结构和策略，减少前期投资，加快预配速度并降低复杂性。

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure 云中的灾难恢复和虚拟桌面

* 在 Azure 云中创建对数据、应用和桌面的远程访问。 通过高带宽连接，可以快速上传/下载数据，以从事件中恢复。 低延迟网络为你提供用户希望桌面应用具有的快速响应时间。

* 使用 CloudSimple 门户和熟悉的 VMware 工具将所有策略和网络复制到云中。 副本减少了创建和管理 DR 和 VDI 实现带来的工作量和风险。

### <a name="high-performance-applications-and-databases"></a>高性能应用程序和数据库

* 使用 CloudSimple 提供的超融合体系结构运行要求最严格的工作负载。
* 运行 Oracle、Microsoft SQL Server、中间件系统和高性能的非 SQL 数据库。
* 通过 25 Gbps 的高速网络连接将云作为自己的数据中心进行体验。 借助高速连接，可以运行跨本地、Azure 上的 VMware 和 Azure 专用工作负载的混合应用，而不会影响性能。

### <a name="true-hybrid"></a>真正混合

* 跨 VMware 和 Azure 服务统一 DevOps。
* 优化 Azure 服务和解决方案的 VMware 管理，该管理可跨所有工作负载应用。
* 访问公有云服务，而无需扩展数据中心或重新架构应用程序。
* 将用于 VMware 应用程序的标识、访问控制策略、日志记录和监视集中到 Azure 中。

## <a name="limits"></a>限制

下表列出了私有云资源的节点限制。

| 资源 | 限制 |
|----------|-------|
| 创建私有云所需的最小节点数 | 3 |
| 私有云群集中的最大节点数 | 16 |
| 私有云中的最大节点数 | 64 |
| 新群集上的最小节点数 | 3 |

## <a name="next-steps"></a>后续步骤

* 了解如何[创建私有云](create-private-cloud.md)
* 了解如何[配置私有云环境](quickstart-create-private-cloud.md)
