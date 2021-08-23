---
title: 支持可用性区域的 Azure 服务
description: 若要在 Azure 中创建具有高可用性和弹性的应用程序，可用性区域提供了可用于运行资源的物理独立位置。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 07f0e87b77d359d9bc1009ccc7e5176795e12cd5
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2021
ms.locfileid: "112029002"
---
# <a name="azure-services-that-support-availability-zones"></a>支持可用性区域的 Azure 服务

Microsoft Azure 全球基础结构的每一层都设计并构造为向客户提供最高级别的冗余和复原能力。 Azure 基础结构由地理位置、区域和可用性区域组成，可以遏制故障的冲击半径，从而限制对客户应用程序和数据造成的潜在影响。 开发 Azure 可用性区域构造的目的是提供软件和网络解决方案来防范数据中心故障，并进一步为客户提供高可用性 (HA)。

可用性区域是 Azure 区域中独特的物理位置。 每个局部区域由一个或多个数据中心组成，这些数据中心配置了独立的电源、散热系统和网络。 地理区域中可用性区域的物理隔离限制了局部区域故障对应用程序和数据的影响，这些故障包括大规模洪灾、大风暴和超级风暴，以及可能导致现场进出、安全通道、公用设施长时间运转和资源可用性受到干扰的其他事件。 可用性区域及其关联数据中心的设计方式可确保当某个局部区域受到损坏时，地理区域中的其他可用性区域能够为服务、容量和可用性提供支持。

所有 Azure 管理服务的架构方式使其能够在发生地理区域级别的故障后复原。 在故障波及的范围方面，地理区域内一个或多个可用性区域的故障影响半径比整个地理区域的故障范围影响更小。 当地理区域内部的管理服务发生局部区域级别的故障时，Azure 可以恢复。 Azure 每次对地理区域内的一个局部区域执行关键维护，以防止任何故障影响到地理区域内各个可用性区域中部署的客户资源。


![包含 3 个局部区域的 Azure 区域概念视图](./media/az-region/azure-region-availability-zones.png)


支持可用性区域的 Azure 服务划分为三个类别：**局部区域**、**局部区域冗余** 和 **非地理区域** 服务。 可将客户工作负载分类，以利用其中的任一体系结构方案来满足应用程序性能和持久性需求。

- **局部区域服务** – 可将资源部署到自选的特定可用性区域，以满足更严格的延迟或性能要求。  复原能力是通过将应用程序和数据复制到地理区域中的一个或多个局部区域来自我形成的。  资源可固定到特定的局部区域。 例如，可将虚拟机、托管磁盘或标准 IP 地址固定到特定的区域，这样就可以凭借分散在多个局部区域的一个或多个资源实例来提高复原能力。

- **区域冗余服务** - 资源自动在各个区域间复制或分布。 例如，ZRS 将跨三个区域复制数据，这样可避免区域故障影响数据的高可用性。  

- **非地理区域服务** – 服务始终从 Azure 地理位置提供，可以灵活应对局部区域和地理区域范围的服务中断。 


若要在 Azure 上实现全面的业务连续性，可结合使用可用性区域和 Azure 区域对来构建应用程序体系结构。 可以使用 Azure 区域中的可用性区域以同步方式复制应用程序和数据以实现高可用性，并以异步方式跨 Azure 区域复制以实现灾难恢复保护。 有关详细信息，请参阅[使用可用性区域构建高可用性解决方案](/azure/architecture/high-availability/building-solutions-for-high-availability)。 

## <a name="azure-services-supporting-availability-zones"></a>支持可用性区域的 Azure 服务

 - 较旧一代的虚拟机未列出。 有关详细信息，请参阅[前几代虚拟机大小](../virtual-machines/sizes-previous-gen.md)。
 - 如 [Azure 中的地理区域和可用性区域](az-overview.md)中所述，某些服务不是地理区域性的。 这些服务不依赖于特定的 Azure 区域，因此可以灵活应对局部区域和地理区域范围的服务中断。  可在[各区域可用的产品](https://azure.microsoft.com/global-infrastructure/services/)中找到非地理区域服务的列表。


## <a name="azure-regions-with-availability-zones"></a>包含可用性区域的 Azure 区域
 

| 美洲           | 欧洲               | 非洲              | 亚太区   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Brazil South       | 法国中部       | 南非北部* | 澳大利亚东部 |
| 加拿大中部     | 德国中西部 |                     | 印度中部* |
| 美国中部         | 北欧         |                     | 日本东部     |
| 美国东部            | 英国南部             |                     | 韩国中部* |
| 美国东部 2          | 西欧          |                     | 东南亚 |
| 美国中南部 |                      |                     |                |
| US Gov 弗吉尼亚州    |                      |                     |                |
| 美国西部 2        |                      |                     |                |
| 美国西部 3*       |                      |                     |                |

\* 若要详细了解这些地理区域中的可用性区域和可用服务支持，请与 Microsoft 销售或客户代表联系。 有关即将支持可用性区域的地理区域，请参阅 [Azure 地理位置](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)。


## <a name="azure-services-supporting-availability-zones"></a>支持可用性区域的 Azure 服务

- 较旧一代的虚拟机未在下面列出。 有关详细信息，请参阅[前几代虚拟机大小](../virtual-machines/sizes-previous-gen.md)。

- 某些服务不是地理区域性的，有关详细信息，请参阅 [Azure 中的地理区域和可用性区域](az-overview.md)。 这些服务不依赖于特定的 Azure 区域，因此可以灵活应对局部区域和地理区域范围的服务中断。  可在[各区域可用的产品](https://azure.microsoft.com/global-infrastructure/services/)中找到非地理区域服务的列表。


### <a name="zone-resilient-services"></a>局部区域复原服务 

:globe_with_meridians: 非地理区域服务 – 服务始终从 Azure 地理位置提供，可以灵活应对局部区域和地理区域范围的服务中断。

:large_blue_diamond: 灵活应对局部区域范围的服务中断 

**基础服务**

|     产品                                                    | 复原             |
|-----------------------------------------------------------------|:----------------------------:|
|     [应用程序网关 (V2)](../application-gateway/application-gateway-autoscaling-zone-redundant.md)                                  | :large_blue_diamond:  |
|     [Azure 备份](../backup/backup-create-rs-vault.md#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md)                             | :large_blue_diamond:  |
|     [Azure DNS：Azure DNS 专用区域](../dns/private-dns-getstarted-portal.md)                   | :large_blue_diamond:  |
|     [Azure Express Route](../expressroute/designing-for-high-availability-with-expressroute.md)                                       | :large_blue_diamond:  |
|     [Azure 公共 IP](../virtual-network/public-ip-addresses.md)                                           | :large_blue_diamond:  |
|     Azure SQL 数据库（[常规用途层](../azure-sql/database/high-availability-sla.md)）                 | :large_blue_diamond:  |
|     Azure SQL 数据库（[高级和业务关键层](../azure-sql/database/high-availability-sla.md)）     | :large_blue_diamond:  |
|     [磁盘存储](../storage/common/storage-redundancy.md)                                                | :large_blue_diamond:  |
|     [事件中心](../event-hubs/event-hubs-geo-dr.md#availability-zones)                                                  | :large_blue_diamond:  |
|     [密钥保管库](../key-vault/general/disaster-recovery-guidance.md)                                                   | :large_blue_diamond:  |
|     [负载均衡器](../load-balancer/load-balancer-standard-availability-zones.md)                                               | :large_blue_diamond:  |
|     [服务总线](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)                                                 | :large_blue_diamond:  |
|     [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md)                                            | :large_blue_diamond:  |
|     [存储帐户](../storage/common/storage-redundancy.md)                                           | :large_blue_diamond:  |
|     存储：[热/冷 Blob 存储层](../storage/common/storage-redundancy.md)                      | :large_blue_diamond:  |
|     存储：[托管磁盘](../virtual-machines/managed-disks-overview.md)                                    | :large_blue_diamond:  |
|     [虚拟机规模集](../virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set.md)                               | :large_blue_diamond:  |
|     [虚拟机](../virtual-machines/windows/create-powershell-availability-zone.md)                                          | :large_blue_diamond:  |
|     虚拟机：[Av2 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     虚拟机：[Bs 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     虚拟机：[DSv2 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                             | :large_blue_diamond:  |
|     虚拟机：[DSv3 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                            | :large_blue_diamond:  |
|     虚拟机：[Dv2 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                             | :large_blue_diamond:  |
|     虚拟机：[Dv3 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     虚拟机：[ESv3 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                             | :large_blue_diamond:  |
|     虚拟机：[Ev3 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     虚拟机：[F 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                                | :large_blue_diamond:  |
|     虚拟机：[FS 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     虚拟机：[共享映像库](../virtual-machines/shared-image-galleries.md#make-your-images-highly-available) | :large_blue_diamond:  |
|     [虚拟网络](../vpn-gateway/create-zone-redundant-vnet-gateway.md)                                         | :large_blue_diamond:  |
|     [VPN 网关](../vpn-gateway/about-zone-redundant-vnet-gateways.md)                                             | :large_blue_diamond:  |


**主流服务**


|     产品                                                    | 复原             |
|-----------------------------------------------------------------|:----------------------------:|
|     [应用服务环境](../app-service/environment/zone-redundancy.md)                                    | :large_blue_diamond:  |
|     [Azure Active Directory 域服务](../active-directory-domain-services/overview.md)                      | :large_blue_diamond:  |
|     [Azure API 管理](../api-management/zone-redundancy.md)                      | :large_blue_diamond:  |
|     [Azure Bastion](../bastion/bastion-overview.md)                                               | :large_blue_diamond:  |
|     [用于 Redis 的 Azure 缓存](../azure-cache-for-redis/cache-high-availability.md)                              | :large_blue_diamond:  |
|     [Azure 认知搜索](../search/search-performance-optimization.md#availability-zones)               | :large_blue_diamond:  |
|     Azure 认知服务：[文本分析](../cognitive-services/text-analytics/index.yml)                    | :large_blue_diamond:  |
|     [Azure 数据资源管理器](/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     [Azure 数据工厂](../data-factory/index.yml)                               | :large_blue_diamond:  |
|     Azure Database for MySQL - [灵活服务器](../mysql/flexible-server/concepts-high-availability.md)                  | :large_blue_diamond:  |
|     Azure Database for PostgreSQL - [灵活服务器](../postgresql/flexible-server/overview.md)             | :large_blue_diamond:  |
|     [Azure DDoS 防护](../ddos-protection/ddos-faq.md)                                       | :large_blue_diamond:  |
|     [Azure 磁盘加密](../virtual-machines/disks-redundancy.md)                                       | :large_blue_diamond:  |
|     [Azure 防火墙](../firewall/deploy-availability-zone-powershell.md)                                              | :large_blue_diamond:  |
|     [Azure 防火墙管理器](../firewall-manager/quick-firewall-policy.md)                                      | :large_blue_diamond:  |
|     [Azure Kubernetes 服务 (AKS)](../aks/availability-zones.md)                              | :large_blue_diamond:  |
|     [Azure 专用链接](../private-link/private-link-overview.md)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)                                         | :large_blue_diamond:  |
|     Azure SQL：[虚拟机](../azure-sql/database/high-availability-sla.md)                                  | :large_blue_diamond:  |
|     [Azure Web 应用程序防火墙](../firewall/deploy-availability-zone-powershell.md)                              | :large_blue_diamond:  |
|     [容器注册表](../container-registry/zone-redundancy.md)                                          | :large_blue_diamond:  |
|     [事件网格](../event-grid/overview.md)                                                  | :large_blue_diamond:  |
|     [网络观察程序](/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | :large_blue_diamond:  |
|     网络观察程序：[流量分析](/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | :large_blue_diamond:  |
|     [Power BI Embedded](/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | :large_blue_diamond:  |
|     [高级 Blob 存储](../storage/blobs/storage-blob-performance-tiers.md)                                        | :large_blue_diamond:  |
|     存储：[Azure 高级文件](../storage/files/storage-files-planning.md)                                | :large_blue_diamond:  |
|     虚拟机：[Azure 专用主机](../virtual-machines/windows/create-powershell-availability-zone.md)                     | :large_blue_diamond:  |
|     虚拟机：[Ddsv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     虚拟机：[Ddv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     虚拟机：[Dsv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     虚拟机：[Dv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                                | :large_blue_diamond:  |
|     虚拟机：[Edsv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                              | :large_blue_diamond:  |
|     虚拟机：[Edv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     虚拟机：[Esv4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     虚拟机：[Ev4 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                                | :large_blue_diamond:  |
|     虚拟机：[Fsv2 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                               | :large_blue_diamond:  |
|     虚拟机：[M 系列](../virtual-machines/windows/create-powershell-availability-zone.md)                                  | :large_blue_diamond:  |
|     [虚拟 WAN](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     虚拟 WAN：[ExpressRoute](../virtual-wan/virtual-wan-faq.md#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     虚拟 WAN：[点到站点 VPN 网关](../vpn-gateway/about-zone-redundant-vnet-gateways.md)                      | :large_blue_diamond:  |
|     虚拟 WAN：[站点到站点 VPN 网关](../vpn-gateway/about-zone-redundant-vnet-gateways.md)                       | :large_blue_diamond:  |


**专用服务**

|     产品                                                    | 复原             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     认知服务：异常检测器                        | :large_blue_diamond:  |
|     认知服务：表单识别器                         | :large_blue_diamond:  |
|     存储：超级磁盘                                         | :large_blue_diamond:  |


**非地理区域**

|     产品                                                    | 复原             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | :globe_with_meridians: |
|     Azure Active Directory                                    | :globe_with_meridians: |
|     Azure 高级威胁防护                            | :globe_with_meridians: |
|     Azure 顾问                                               | :globe_with_meridians: |
|     Azure 蓝图                                            | :globe_with_meridians: |
|     Azure 机器人服务                                          | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Defender for IoT                                    | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure 信息保护                              | :globe_with_meridians: |
|     Azure Lighthouse                                          | :globe_with_meridians: |
|     Azure 托管应用程序                                | :globe_with_meridians: |
|     Azure Maps                                                  | :globe_with_meridians: |
|     Azure Performance Diagnostics                               | :globe_with_meridians: |
|     Azure Policy                                                | :globe_with_meridians: |
|     Azure Resource Graph                                      | :globe_with_meridians: |
|     Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Azure Stack Edge                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     内容分发网络                                    | :globe_with_meridians: |
|     成本管理                                             | :globe_with_meridians: |
|     Microsoft Azure 客户密码箱                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Microsoft Azure 对等互连服务                           | :globe_with_meridians: |
|     Microsoft Azure 门户                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     安全中心                                           | :globe_with_meridians: |
|     流量管理器                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>可用性区域中 VM 的定价

Azure 可用性区域随 Azure 订阅提供。 在此处了解详情 - [带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)。


## <a name="get-started-with-availability-zones"></a>可用性区域入门

- [创建虚拟机](../virtual-machines/windows/create-portal-availability-zone.md)
- [使用 PowerShell 添加托管磁盘](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [创建区域冗余的虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [使用具有区域冗余前端的标准负载均衡器跨区域对 VM 进行负载均衡](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [使用具有区域性前端的标准负载均衡器在区域内对 VM 进行负载均衡](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [区域冗余存储](../storage/common/storage-redundancy.md)
- [Azure SQL 数据库常规用途层](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [事件中心异地灾难恢复](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [服务总线异地灾难恢复](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [创建区域冗余的虚拟网关](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [为 Azure Cosmos DB 添加局部区域冗余的地理区域](../cosmos-db/high-availability.md#availability-zone-support)
- [Azure Cache for Redis 可用性区域入门](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [创建 Azure Active Directory 域服务实例](../active-directory-domain-services/tutorial-create-instance.md)
- [创建使用可用性区域的 Azure Kubernetes 服务 (AKS) 群集](../aks/availability-zones.md)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure 中的区域和可用性区域](az-overview.md)