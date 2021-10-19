---
title: 支持可用性区域的 Azure 服务
description: 了解可用性区域支持哪些服务并了解所有 Azure 服务的复原能力。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: 452948e3fffa9d6bdd5bc8b9523c659a06247a27
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668022"
---
# <a name="azure-services-that-support-availability-zones"></a>支持可用性区域的 Azure 服务

Azure [区域和可用性区域](az-overview.md)是每个 Azure 区域中物理隔离的位置，可承受数据中心故障，这是因为有冗余的基础结构以及 Azure 服务的逻辑隔离。 

可用性区域与网络冗余密不可分。 支持可用性区域的 Azure 服务旨在提供适当级别的复原能力和灵活性，同时提供超低的延迟。 使用支持可用性区域的 Azure 服务，无论是自行构建复原能力还是选择自动复制和分发，都能获得相同的优势。 无论服务类型如何，你都可以跨高度可用的服务获得卓越的复原能力。 

Azure 致力于在每项服务和产品中实现高度的复原能力。 运行支持可用性区域的 Azure 服务可针对几乎所有场景提供完全透明且一致的复原能力，而不会中断。

## <a name="azure-regions-with-availability-zones"></a>包含可用性区域的 Azure 区域

Azure 为所有云提供商提供最广泛的全球覆盖，并正在迅速开放新的区域和可用性区域。 以下区域目前支持可用性区域。

| 美洲 | 欧洲 | 非洲 | 亚太区 |
|--------------------|----------------------|---------------------|----------------|
| 巴西南部 | 法国中部 | 南非北部 | 澳大利亚东部 |
| 加拿大中部 | 德国中西部 | | 印度中部\* |
| 美国中部 | 北欧 | | 日本东部 |
| 美国东部 | 挪威东部 | | 韩国中部 |
| 美国东部 2 | 英国南部 | | 东南亚 |
| 美国中南部 | 西欧 | | |
| US Gov 弗吉尼亚州 | 瑞典* | | |
| 美国西部 2 | | | |
| 美国西部 3 | | | |

\* 若要详细了解该区域中的可用性区域和可用服务支持，请与 Microsoft 销售或客户代表联系。 有关即将支持可用性区域的地理区域的信息，请参阅 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。

请参阅[可用性区域文档](az-overview.md)，其中按 Azure 区域列出了支持可用性区域的 Azure 服务。

## <a name="highly-available-services"></a>高度可用的服务

三种类型的 Azure 服务支持可用性区域：“区域”、“区域冗余”和“始终可用”服务。   在设计复原能力策略时，可以组合使用这三种体系结构方法。

- 局部区域服务：可将资源部署到自选的特定可用性区域，以满足更严格的延迟或性能要求。 复原能力是通过将应用程序和数据复制到地理区域中的一个或多个局部区域来自我形成的。 资源可固定到特定的局部区域。 例如，可将虚拟机、托管磁盘或标准 IP 地址固定到特定的区域，这样就可以凭借分散在多个局部区域的一个或多个资源实例来提高复原能力。
- 区域冗余服务：资源自动在各个区域间复制或分布。 例如，区域冗余服务可以跨三个区域复制数据，因此一个区域中发生故障不会影响数据的高可用性。 
- 始终可用的服务：在所有 Azure 地区始终可用，并且对区域范围的中断和区域范围的中断具有复原能力。 有关 Azure 中始终可用服务（也称为非区域服务）的完整列表，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)。

正如上文所述，并非所有 Azure 服务都在所有区域普遍可用。 产品/服务分为三类，以反映其区域可用性：基础服务、主流服务和战略服务。   Azure 向任何指定区域部署服务的一般策略主要取决于区域类型、服务类别和客户需求。 有关详细信息，请参阅 [Azure 服务](region-types-service-categories-azure.md)。

- 基础服务：在区域正式发布后或新基础服务正式发布后 90 天内，所有建议区域和备用区域均可用的服务。
- 主流服务：在区域正式发布后 90 天内，所有建议区域均可用的服务。 主流服务在备用区域根据需求进行发布，许多已部署到备用区域的大型子集中。
- 战略服务：有针对性的服务产品，通常专注于特定行业或由定制硬件提供支持。 战略服务在不同区域根据需求进行发布，许多已部署到建议区域的大型子集中。

支持可用性区域的 Azure 服务（包括区域和区域冗余产品/服务）正在不断扩展。

有关上一代虚拟机的详细信息，请参阅[前几代虚拟机大小](/azure/virtual-machines/sizes-previous-gen)。

以下几张表汇总了当前提供的区域、区域冗余和始终可用的 Azure 服务。 这几张表根据每个 Azure 产品/服务的区域可用性列出这些 Azure 产品/服务。

##### <a name="legend"></a>图例
![图例包含与表中每个服务的服务类别和区域可用性相关的图标和含义。](media/legend.png) 

在产品目录中，始终可用的服务被列为“非区域”服务。

### <a name="an-icon-that-signifies-this-service-is-foundational-foundational-services"></a>![表示此服务是基础服务的图标。](media/icon-foundational.svg) 基本服务 

| **产品**   | **复原能力**   |
| --- | --- |
| [Azure 应用程序网关 (V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| [Azure 备份](/architecture/backup/backup-create-rs-vault#set-storage-redundancy.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)   |
| [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure DNS：Azure DNS 专用区域](../dns/private-dns-getstarted-portal.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 公共 IP](../virtual-network/public-ip-addresses.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| Azure SQL 数据库（[常规用途层](../azure-sql/database/high-availability-sla.md)）  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| Azure SQL 数据库（[高级和业务关键层](../azure-sql/database/high-availability-sla.md)）  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 磁盘存储](../storage/common/storage-redundancy.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 事件中心](../event-hubs/event-hubs-geo-dr.md#availability-zones)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure Key Vault](../key-vault/general/disaster-recovery-guidance.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 负载均衡器](../load-balancer/load-balancer-standard-availability-zones.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| [Azure 服务总线](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| [Azure 存储帐户](../storage/common/storage-redundancy.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| 存储： [热/冷 Azure Blob 存储层](../storage/common/storage-redundancy.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| 存储： [托管磁盘](../virtual-machines/managed-disks-overview.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| [Azure 虚拟机规模集](../virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| [Azure 虚拟机](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Av2 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Bs 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [DSv2 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [DSv3 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Dv2 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Dv3 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [ESv3 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Ev3 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [F 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [FS 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [共享映像库](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 虚拟网络](../vpn-gateway/create-zone-redundant-vnet-gateway.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure VPN 网关](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
### <a name="an-icon-that-signifies-this-service-is-mainstream-mainstream-services"></a>![表示此服务是主流服务的图标。](media/icon-mainstream.svg) 主流服务

| **产品**   | **复原能力**   |
| --- | --- |
| [应用服务](../app-service/how-to-zone-redundancy.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| [Azure 应用服务环境](../app-service/environment/zone-redundancy.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| [Azure Active Directory 域服务](../active-directory-domain-services/overview.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure API 管理](../api-management/zone-redundancy.md)  | ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| [Azure 应用配置](../azure-app-configuration/faq.yml#how-does-app-configuration-ensure-high-data-availability)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure Bastion](../bastion/bastion-overview.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure Batch](../batch/create-pool-availability-zones.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [用于 Redis 的 Azure 缓存](../azure-cache-for-redis/cache-high-availability.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| [Azure 认知搜索](../search/search-performance-optimization.md#availability-zones)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| Azure 认知服务： [文本分析](../cognitive-services/text-analytics/index.yml)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 数据资源管理器](/azure/data-explorer/create-cluster-database-portal)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 数据工厂](../data-factory/index.yml)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| Azure Database for MySQL -  [灵活服务器](../mysql/flexible-server/concepts-high-availability.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| Azure Database for PostgreSQL -  [灵活服务器](../postgresql/flexible-server/overview.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure DDoS 防护](../ddos-protection/ddos-faq.yml)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) |
| [Azure 磁盘加密](../virtual-machines/disks-redundancy.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) |
| [Azure 防火墙](../firewall/deploy-availability-zone-powershell.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 防火墙管理器](../firewall-manager/quick-firewall-policy.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure Functions](https://azure.github.io/AppService/2021/08/25/App-service-support-for-availability-zones.html)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure Kubernetes 服务 (AKS)](../aks/availability-zones.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| [Azure Monitor](../azure-monitor/logs/availability-zones.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure Monitor：Application Insights](../azure-monitor/logs/availability-zones.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure Monitor：Log Analytics](../azure-monitor/logs/availability-zones.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 专用链接](../private-link/private-link-overview.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)  | ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| Azure SQL： [虚拟机](../azure-sql/database/high-availability-sla.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) |
| [Azure Web 应用程序防火墙](../firewall/deploy-availability-zone-powershell.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 容器注册表](../container-registry/zone-redundancy.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Azure 事件网格](../event-grid/overview.md)  | ![表示此服务是区域冗余服务的图标](media/icon-zone-redundant.svg)  |
| [Azure HDInsight](../hdinsight/hdinsight-use-availability-zones.md)  | ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| [Azure 网络观察程序](../network-watcher/frequently-asked-questions.yml)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| 网络观察程序： [流量分析](../network-watcher/frequently-asked-questions.yml)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [Power BI Embedded](/power-bi/admin/service-admin-failover#what-does-high-availability)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| [高级 Blob 存储](../storage/blobs/storage-blob-performance-tiers.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| 存储： [Azure 高级文件](../storage/files/storage-files-planning.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| 虚拟机： [Azure 专用主机](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Ddsv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Ddv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Dsv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Dv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Edsv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Edv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Esv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Ev4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [Fsv2 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| 虚拟机： [M 系列](../virtual-machines/windows/create-powershell-availability-zone.md)  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |
| [Azure 虚拟 WAN](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| 虚拟 WAN： [Azure ExpressRoute](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| 虚拟 WAN： [点到站点 VPN 网关](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| 虚拟 WAN： [站点到站点 VPN 网关](../vpn-gateway/about-zone-redundant-vnet-gateways.md)  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |

### <a name="an-icon-that-signifies-this-service-is-strategic-strategic-services"></a>![表示此服务是战略服务的图标。](media/icon-strategic.svg) 战略服务

| **产品**   | **复原能力**   |
| --- | --- |
| Azure Red Hat OpenShift  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg) ![表示此服务是区域性服务的图标](media/icon-zonal.svg)  |
| Azure 认知服务：异常检测器  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| Azure 认知服务：表单识别器  | ![表示此服务是区域冗余服务的图标。](media/icon-zone-redundant.svg)  |
| Azure 存储：超级磁盘  | ![表示此服务是区域性服务的图标。](media/icon-zonal.svg)  |

### <a name="an-icon-that-signifies-this-service-is-non-regional-non-regional-services-always-available-services"></a>![表示此服务是非区域服务的图标。](media/icon-always-available.svg) 非区域服务（始终可用的服务）

| **产品**   | **复原能力**   |
| --- | --- |
| Azure DNS  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Active Directory  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 高级威胁防护  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 顾问  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 蓝图  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 机器人服务  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Front Door  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| 适用于 IoT 的 Azure Defender  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Front Door  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 信息保护  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Lighthouse  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 托管应用程序  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Maps  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Performance Diagnostics  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Policy  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Resource Graph  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Sentinel  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Stack  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Stack Edge  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure Cloud Shell  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 内容分发网络  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 成本管理和计费 | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Microsoft Azure 客户密码箱  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Microsoft Intune  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 对等互连服务  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 门户  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Microsoft Cloud App Security  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Microsoft Graph  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 安全中心  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |
| Azure 流量管理器  | ![表示此服务始终可用服务的图标。](media/icon-always-available.svg) |

请参阅[可用性区域文档](az-overview.md)，其中按 Azure 区域列出了支持可用性区域的 Azure 服务。

## <a name="pricing-for-virtual-machines-in-availability-zones"></a>可用性区域中虚拟机的定价

可以使用 Azure 订阅访问 Azure 可用性区域。 要了解更多信息，请参阅[带宽定价](https://azure.microsoft.com/pricing/details/bandwidth/)。

## <a name="next-steps"></a>后续步骤

- [使用可用性区域构建高可用性解决方案](/architecture/high-availability/building-solutions-for-high-availability)
- [使用 Azure 服务实现高可用性](/architecture/framework/resiliency/overview)
- [高可用性的设计模式](/architecture/framework/resiliency/app-design)
