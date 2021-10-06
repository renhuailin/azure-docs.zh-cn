---
title: 用于 SQL 托管实例迁移的网络拓扑
titleSuffix: Azure Database Migration Service
description: 了解使用 Azure 数据库迁移服务迁移 Azure SQL 托管实例的源和目标配置。
author: pochiraju
ms.author: rajpo
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 4a4cdce5ae20147bbf25a82401c9993a620320e5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363032"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>使用 Azure 数据库迁移服务迁移 Azure SQL 托管实例的网络拓扑

本文介绍 Azure 数据库迁移服务可使用的各种网络拓扑，以提供从 SQL Server 到 Azure SQL 托管实例的全面迁移体验。

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>为混合工作负载配置的 Azure SQL 托管实例 

如果 Azure SQL 托管实例与本地网络连接，请使用此拓扑。 此方法提供最简化的网络路由，并在迁移过程中提供最大数据吞吐量。

![混合工作负载的网络拓扑](media/resource-network-topologies/hybrid-workloads.png)

**要求**

- 在此方案中，SQL 托管实例和 Azure 数据库迁移服务实例位于同一 Microsoft Azure 虚拟网络中，但它们使用不同的子网。  
- 本方案中使用的虚拟网络还使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 连接到本地网络。

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>SQL 托管实例与本地网络隔离

如果环境要求以下的一种或多种方案，则使用此网络拓扑：

- SQL 托管实例与本地连接隔离，但 Azure 数据库迁移服务实例已连接到本地网络。
- 如果 Azure 基于角色的访问控制 (Azure RBAC) 策略已经到位，并且你需要限制用户访问托管 SQL 托管实例的同一订阅。
- 用于 SQL 托管实例和用于 Azure 数据库迁移服务的虚拟网络位于不同的订阅中。

![托管实例的网络拓扑与本地网络分离](media/resource-network-topologies/mi-isolated-workload.png)

**要求**

- Azure 数据库迁移服务针对本方案使用的虚拟网络还需通过使用 [ExpressRoute](../expressroute/expressroute-introduction.md) 或 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) 连接到本地网络。
- 在用于 SQL 托管实例的虚拟网络和 Azure 数据库迁移服务之间设置 [VNet 网络对等互连](../virtual-network/virtual-network-peering-overview.md)。

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>云到云的迁移：共享的虚拟网络

如果源 SQL Server 托管在 Azure VM 中，且与 SQL 托管实例以及 Azure 数据库迁移服务共享同一个虚拟网络，请使用此拓扑。

![共享 VNet 的云到云迁移的网络拓扑](media/resource-network-topologies/cloud-to-cloud.png)

**要求**

- 没有其他要求。

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>云到云的迁移：隔离的虚拟网络

如果环境要求以下的一种或多种方案，则使用此网络拓扑：

- SQL 托管实例在隔离虚拟网络中进行预配。
- 如果 Azure 基于角色的访问控制 (Azure RBAC) 策略已经到位，并且你需要限制用户访问托管 SQL 托管实例的同一订阅。
- 用于 SQL 托管实例和用于 Azure 数据库迁移服务的虚拟网络位于不同的订阅中。

![独立 VNet 的云到云迁移的网络拓扑](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**要求**

- 在用于 SQL 托管实例的虚拟网络和 Azure 数据库迁移服务之间设置 [VNet 网络对等互连](../virtual-network/virtual-network-peering-overview.md)。

## <a name="inbound-security-rules"></a>入站安全规则

| **NAME**   | **PORT** | **PROTOCOL** | **源** | **DESTINATION** | **ACTION** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | 任意      | 任意          | DMS SUBNET | 任意             | 允许      |

## <a name="outbound-security-rules"></a>入站安全规则

| **NAME**                  | **PORT**                                              | **PROTOCOL** | **源** | **DESTINATION**           | **ACTION** | **规则的原因**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443，ServiceTag：ServiceBus                           | TCP          | 任意        | 任意                       | Allow      | 通过服务总线进行管理平面通信。 <br/>（如果启用了 Microsoft 对等互连，可能不需要此规则。）                                                             |
| 存储                   | 443，ServiceTag：Storage                              | TCP          | 任意        | 任意                       | Allow      | 使用 Azure Blob 存储的管理平面。 <br/>（如果启用了 Microsoft 对等互连，可能不需要此规则。）                                                             |
| 诊断               | 443，ServiceTag：AzureMonitor                         | TCP          | 任意        | 任意                       | 允许      | DMS 使用此规则收集诊断信息以进行故障排除。 <br/>（如果启用了 Microsoft 对等互连，可能不需要此规则。）                                                  |
| SQL 源服务器         | 1433（或 SQL Server 正在侦听的 TCP IP 端口） | TCP          | 任意        | 本地地址空间 | 允许      | 来自 DMS 的 SQL Server 源连接 <br/>（如果使用站点到站点连接，则可能不需要此规则。）                                                                                       |
| SQL Server 命名实例 | 1434                                                  | UDP          | 任意        | 本地地址空间 | 允许      | 来自 DMS 的 SQL Server 命名实例源连接 <br/>（如果使用站点到站点连接，则可能不需要此规则。）                                                                        |
| SMB 共享                 | 445（如果方案需要）                             | TCP          | 任意        | 本地地址空间 | 允许      | DMS 的 SMB 网络共享用于存储数据库备份文件，以便迁移到 Azure VM 上的 Azure SQL 数据库 MI 和 SQL Server <br/>（如果使用站点到站点连接，则可能不需要此规则）。 |
| DMS_subnet                | 任意                                                   | 任意          | 任意        | DMS_Subnet                | 允许      |                                                                                                                                                                                                  |

## <a name="see-also"></a>另请参阅

- [将 SQL Server 迁移到 SQL 托管实例](./tutorial-sql-server-to-managed-instance.md)
- [使用 Azure 数据库迁移服务的先决条件概述](./pre-reqs.md)
- [使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>后续步骤

- 有关 Azure 数据库迁移服务的概述，请参阅[什么是 Azure 数据库迁移服务？](dms-overview.md)一文。
- 有关 Azure 数据库迁移服务区域可用性的最新信息，请参阅[可用产品（按区域）](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)页。