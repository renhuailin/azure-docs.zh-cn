---
title: 在 Azure 环境下的 Oracle 灾难恢复方案概述 | Microsoft 文档
description: 在 Azure 环境下的 Oracle Database 12c 数据库灾难恢复方案
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: b90de3d7f80eb6dcb60d3e44b13aac83e1dec574
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688465"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>在 Azure 环境下的 Oracle Database 12c 数据库灾难恢复

适用于：:heavy_check_mark: Linux VM 

## <a name="assumptions"></a>假设

- 你了解 Oracle Data Guard 设计和 Azure 环境。


## <a name="goals"></a>目标
- 设计满足灾难恢复 (DR) 需求的拓扑和配置。

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>方案 1：Azure 上的主站点和 DR 站点

客户在主站点上安装了 Oracle 数据库。 DR 站点位于其他区域。 客户使用 Oracle Data Guard 在这两个站点间进行快速恢复。 主站点还拥有用于报告等其他用途的辅助数据库。 

### <a name="topology"></a>拓扑

以下是关于 Azure 安装的摘要：

- 两个站点（主站点和 DR 站点）
- 两个虚拟网络
- 两个具有 Data Guard 的 Oracle 数据库（主数据库和备用数据库）
- 两个具有 Golden Gate 或 Data Guard 的 Oracle 数据库（仅限主站点）
- 两个应用程序服务，一个位于主站点，一个位于 DR 站点
- 一个“可用性集”，用于主站点上的数据库和应用程序服务
- 每个站点各有一个 jumpbox，用于限制对专用网络的访问，且仅允许管理员登录
- 位于不同子网的 jumpbox、应用程序服务、数据库和 VPN 网关
- 对应用程序和数据库子网强制执行的 NSG

![图中显示了 Azure 上的主站点和 DR 站点。](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>方案 2：本地主站点和 Azure 上的 DR 站点

客户在本地安装了 Oracle 数据库（主站点）。 DR 站点位于 Azure 上。 使用 Oracle Data Guard 在这些站点间进行快速恢复。 主站点还拥有用于报告等其他用途的辅助数据库。 

安装方式有两种。

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>方式 1：在本地和 Azure 之间直接连接，要求在防火墙上开启 TCP 端口 

不建议进行直接连接，因为这会向外部公开 TCP 端口。

#### <a name="topology"></a>拓扑

以下是关于 Azure 安装的摘要：

- 一个 DR 站点 
- 一个虚拟网络
- 一个具有 Data Guard 的 Oracle 数据库（活动）
- 一个 DR 站点上的应用程序服务
- 一个 jumpbox，用于限制对专用网络的访问，且仅允许管理员登录
- 位于不同子网的 jumpbox、应用程序服务、数据库和 VPN 网关
- 对应用程序和数据库子网强制执行的 NSG
- NSG 策略/规则，以允许入站 TCP 端口 1521（或用户定义的端口）
- NSG 策略/规则，以仅限制一个或多个本地 IP 地址（DB 或应用程序）访问虚拟网络

![图中显示了在本地和 Azure 之间直接连接，要求在防火墙上开启 TCP 端口。](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>方式 2：站点到站点 VPN
使用站点到站点 VPN 是一个比较好的做法。 有关安装 VPN 的详细信息，请参阅[使用 CLI 创建具有站点到站点 VPN 连接的虚拟网络](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)。

#### <a name="topology"></a>拓扑

以下是关于 Azure 安装的摘要：

- 一个 DR 站点 
- 一个虚拟网络 
- 一个具有 Data Guard 的 Oracle 数据库（活动）
- 一个 DR 站点上的应用程序服务
- 一个 jumpbox，用于限制对专用网络的访问，且仅允许管理员登录
- 位于不同子网的 jumpbox、应用程序服务、数据库和 VPN 网关
- 对应用程序和数据库子网强制执行的 NSG
- 本地与 Azure 之间的站点到站点 VPN 连接

![“DR 拓扑”页屏幕截图](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>附加阅读材料

- [在 Azure 上设计和实现 Oracle 数据库](oracle-design.md)
- [配置 Oracle Data Guard](configure-oracle-dataguard.md)
- [配置 Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle 备份和恢复](./oracle-overview.md)


## <a name="next-steps"></a>后续步骤

- [教程：创建具有高可用性的 VM](../../linux/create-cli-complete.md)
- [浏览 VM 部署 Azure CLI 示例](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)