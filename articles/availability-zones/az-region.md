---
title: 支持可用性区域的 Azure 服务
description: 若要在 Azure 中创建具有高可用性和弹性的应用程序，可用性区域提供了可用于运行资源的物理独立位置。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: e8e62d92afb70ec9d4df40f677de05583ef472b0
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955777"
---
# <a name="azure-services-that-support-availability-zones"></a>支持可用性区域的 Azure 服务

每一层都设计并构造了 Microsoft Azure 的全球基础结构，可为其客户提供最高级别的冗余和复原能力。 Azure 基础结构由地理位置、区域和可用性区域组成，它们限制了故障的冲击半径，因而限制了对客户应用程序和数据的潜在影响。 开发 Azure 可用性区域构造是为了提供软件和网络解决方案来防范数据中心故障，并为客户提供更高的高可用性 (HA) 。

可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，具有独立的电源、冷却和网络。 区域内可用性区域的物理分离限制了对应用程序和数据的影响（如大规模扩散、主要风暴和 superstorms），以及可能中断站点访问、安全的发布、扩展的实用程序运行时间和资源可用性的其他事件。 可用性区域及其关联的数据中心被设计为：如果一个区域被泄露，则该区域中的其他可用性区域支持服务、容量和可用性。

所有 Azure 管理服务都设计为可从区域级故障中复原。 在故障范围内，区域中的一个或多个可用性区域故障与整个区域发生故障相比具有更小的故障半径。 Azure 可以从区域内的管理服务的区域级故障中恢复。 Azure 会在区域中一次执行关键维护，以防止任何影响在区域内的可用性区域部署的客户资源的故障。


![具有3个区域的 Azure 区域的概念视图](./media/az-region/azure-region-availability-zones.png)


支持可用性区域的 Azure 服务分为三个类别 **：****区域、区域冗余** 和 **非区域性** 服务。 可以对客户工作负荷进行分类以利用任何这些体系结构方案来满足应用程序性能和持续性。

- **区域性服务** –可以将资源部署到特定的、自行选择的可用性区域，以实现更严格的延迟或性能要求。  复原可通过将应用程序和数据复制到区域内的一个或多个区域自行构建。  资源可以固定到特定区域。 例如，虚拟机、托管磁盘或标准 IP 地址可以固定到特定区域，这允许通过跨区域分布一个或多个资源实例来提高复原能力。

- **区域冗余服务** – Azure 平台跨区域复制资源和数据。  Microsoft 管理高可用性的交付，因为 Azure 会自动复制和分发该区域内的实例。  例如，ZRS 将数据复制到三个区域，以便区域故障不会影响数据的 HA。 

- **非区域服务** –始终可以从 Azure 地理位置获得服务，并且在区域范围中断和区域范围内中断时可以提供弹性。 


若要在 Azure 上实现全面的业务连续性，可结合使用可用性区域和 Azure 区域对来构建应用程序体系结构。 可以使用 Azure 区域中的可用性区域以同步方式复制应用程序和数据以实现高可用性，并以异步方式跨 Azure 区域复制以实现灾难恢复保护。 若要了解详细信息，请参阅 [使用可用性区域构建高可用性解决方案](/azure/architecture/high-availability/building-solutions-for-high-availability)。 

## <a name="azure-services-supporting-availability-zones"></a>支持可用性区域的 Azure 服务

 - 旧代虚拟机未列出。 有关详细信息，请参阅[前几代虚拟机大小](../virtual-machines/sizes-previous-gen.md)。
 - 如 Azure 中的 [区域和可用性区域](az-overview.md)中所述，某些服务是非区域。 这些服务不依赖于特定的 Azure 区域，因此可以灵活应对区域范围的服务中断和区域范围的服务中断。  可在 [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/)中找到非区域服务的列表。


## <a name="azure-regions-with-availability-zones"></a>具有可用性区域的 Azure 区域


| 美洲           | 欧洲         | 德国              | 非洲              | 亚太区   |
|--------------------|----------------|----------------------|---------------------|----------------|
|                    |                |                      |                     |                |
| 加拿大中部     | 法国中部 | 德国中西部 | 南非北部 * | Japan East     |
| 美国中部         | 北欧   |                      |                     | 东南亚 |
| 美国东部            | 英国南部       |                      |                     | 澳大利亚东部 |
| 美国东部 2          | 西欧    |                      |                     |                |
| 美国中南部 |                |                      |                     |                |
| US Gov 弗吉尼亚州 * |                |                      |                     |                |
| 美国西部2        |                |                      |                     |                |


\* 若要详细了解这些区域中的可用性区域和可用服务支持，请联系你的 Microsoft 销售或客户代表。 有关将支持可用性区域的即将推出的区域，请参阅 [Azure 地理](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)位置。


## <a name="azure-services-supporting-availability-zones"></a>支持可用性区域的 Azure 服务

- 下面未列出较旧的代虚拟机。 有关详细信息，请参阅 [以前代的虚拟机大小](../virtual-machines/sizes-previous-gen.md)。

- 某些服务不是区域，请参阅 [Azure 中的区域和可用性区域](az-overview.md) 获取详细信息。 这些服务不依赖于特定的 Azure 区域，使其能够在区域范围的服务中断和区域范围内中断时获得弹性。  可在 [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/)中找到非区域服务的列表。


### <a name="zone-resilient-services"></a>区域复原服务 

： globe_with_meridians：非区域服务-服务始终可从 Azure 地理位置获得，并且可在区域范围内中断和区域范围内中断时复原。

： large_blue_diamond：可在区域范围内中断时复原 

**基础服务**

|     产品                                                    | 复原             |
|-----------------------------------------------------------------|:----------------------------:|
|     存储帐户                                           | ： large_blue_diamond：  |
|     应用程序网关 (V2)                                   | ： large_blue_diamond：  |
|     Azure 备份                                                | ： large_blue_diamond：  |
|     Azure Cosmos DB                                           | ： large_blue_diamond：  |
|     Azure Data Lake Storage 第2代                             | ： large_blue_diamond：  |
|     Azure Express Route                                       | ： large_blue_diamond：  |
|     Azure 公共 IP                                           | ： large_blue_diamond：  |
|     常规用途层的 Azure SQL 数据库 ()                  | ： large_blue_diamond：  |
|     Azure SQL Database (高级 & 业务关键层)      | ： large_blue_diamond：  |
|     磁盘存储                                                | ： large_blue_diamond：  |
|     事件中心                                                  | ： large_blue_diamond：  |
|     Key Vault                                                   | ： large_blue_diamond：  |
|     负载均衡器                                               | ： large_blue_diamond：  |
|     服务总线                                                 | ： large_blue_diamond：  |
|     Service Fabric                                            | ： large_blue_diamond：  |
|     存储：热/冷 Blob 存储层                      | ： large_blue_diamond：  |
|     存储：托管磁盘                                    | ： large_blue_diamond：  |
|     虚拟机规模集                               | ： large_blue_diamond：  |
|     虚拟机                                          | ： large_blue_diamond：  |
|     虚拟机： Av2-Series                              | ： large_blue_diamond：  |
|     虚拟机： Bs-Series                               | ： large_blue_diamond：  |
|     虚拟机： DSv2-Series                             | ： large_blue_diamond：  |
|     虚拟机： DSv3-Series                             | ： large_blue_diamond：  |
|     虚拟机： Dv2-Series                              | ： large_blue_diamond：  |
|     虚拟机： Dv3-Series                              | ： large_blue_diamond：  |
|     虚拟机： ESv3-Series                             | ： large_blue_diamond：  |
|     虚拟机： Ev3-Series                              | ： large_blue_diamond：  |
|     虚拟网络                                           | ： large_blue_diamond：  |
|     VPN 网关                                                 | ： large_blue_diamond：  |


**主流服务**

| 产品                                        | 复原 |
|-------------------------------------------------|:------------:|
| 应用服务环境                        |      ： large_blue_diamond：  |
| Azure Active Directory 域服务          |      ： large_blue_diamond：  |
| Azure Bastion                                   |      ： large_blue_diamond：  |
| Azure Redis 缓存                           |      ： large_blue_diamond：  |
| Azure 认知服务：文本分析        |      ： large_blue_diamond：  |
| Azure 数据资源管理器                             |      ： large_blue_diamond：  |
| Azure Database for MySQL –灵活的服务器      |      ： large_blue_diamond：  |
| Azure Database for PostgreSQL –灵活的服务器 |      ： large_blue_diamond：  |
| Azure DDoS 防护                           |      ： large_blue_diamond：  |
| Azure 防火墙                                  |      ： large_blue_diamond：  |
| Azure 防火墙管理器                          |      ： large_blue_diamond：  |
| Azure Kubernetes 服务 (AKS)                  |      ： large_blue_diamond：  |
| Azure 专用链接                              |      ： large_blue_diamond：  |
| Azure Red Hat OpenShift                         |      ： large_blue_diamond：  |
| Azure Site Recovery                             |      ： large_blue_diamond：  |
| 容器注册表                              |      ： large_blue_diamond：  |
| 事件网格                                      |      ： large_blue_diamond：  |
| 网络观察程序                                 |      ： large_blue_diamond：  |
| Power BI Embedded                               |      ： large_blue_diamond：  |
| 高级 Blob 存储                            |      ： large_blue_diamond：  |
| 虚拟机： Ddsv4-Series                  |      ： large_blue_diamond：  |
| 虚拟机： Ddv4-Series                   |      ： large_blue_diamond：  |
| 虚拟机： Dsv4-Series                   |      ： large_blue_diamond：  |
| 虚拟机： Dv4-Series                    |      ： large_blue_diamond：  |
| 虚拟机： Edsv4-Series                  |      ： large_blue_diamond：  |
| 虚拟机： Edv4-Series                   |      ： large_blue_diamond：  |
| 虚拟机： Esv4-Series                   |      ： large_blue_diamond：  |
| 虚拟机： Ev4-Series                    |      ： large_blue_diamond：  |
| 虚拟机： Fsv2-Series                   |      ： large_blue_diamond：  |
| 虚拟机： M 系列                      |      ： large_blue_diamond：  |
| 虚拟 WAN                                     |      ： large_blue_diamond：  |


**非区域**

|     产品                                  |     复原    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     ： globe_with_meridians：             |
|     Azure Active Directory                  |     ： globe_with_meridians：             |
|     Azure 顾问                             |     ： globe_with_meridians：             |
|     Azure 机器人服务                        |     ： globe_with_meridians：             |
|     Azure Defender for IoT                  |     ： globe_with_meridians：             |
|     Azure 信息保护            |     ： globe_with_meridians：             |
|     Azure Lighthouse                        |     ： globe_with_meridians：             |
|     Azure 托管应用程序              |     ： globe_with_meridians：             |
|     Azure Maps                                |     ： globe_with_meridians：             |
|     Azure Policy                              |     ： globe_with_meridians：             |
|     Azure 资源关系图                    |     ： globe_with_meridians：             |
|     Azure Stack                               |     ： globe_with_meridians：             |
|     Azure Stack 边缘                        |     ： globe_with_meridians：             |
|     Cloud Shell                               |     ： globe_with_meridians：             |
|     Microsoft Azure 的客户密码箱    |     ： globe_with_meridians：             |
|     Microsoft Azure 对等互连服务         |     ： globe_with_meridians：             |
|     Microsoft Azure 门户                  |     ： globe_with_meridians：             |
|     安全中心                         |     ： globe_with_meridians：             |
|     流量管理器                         |     ： globe_with_meridians：             |


## <a name="pricing-for-vms-in-availability-zones"></a>可用性区域中 Vm 的定价

在可用性区域中部署虚拟机不会产生额外的费用。 有关详细信息，请参阅 [带宽定价页](https://azure.microsoft.com/pricing/details/bandwidth/)。


## <a name="get-started-with-availability-zones"></a>可用性区域入门

- [创建虚拟机](../virtual-machines/windows/create-portal-availability-zone.md)
- [使用 PowerShell 添加托管磁盘](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [创建区域冗余的虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [使用具有区域冗余前端的标准负载均衡器跨区域对 VM 进行负载均衡](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [使用具有区域性前端的标准负载均衡器在区域内对 VM 进行负载均衡](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [区域冗余存储](../storage/common/storage-redundancy.md)
- [SQL 数据库常规用途层](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [事件中心异地灾难恢复](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [服务总线异地灾难恢复](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [创建区域冗余的虚拟网关](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [为 Azure Cosmos DB 添加区域冗余区域](../cosmos-db/high-availability.md#availability-zone-support)
- [入门用于 Redis 的 Azure 缓存可用性区域](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [创建 Azure Active Directory 域服务实例](../active-directory-domain-services/tutorial-create-instance.md)
- [使用可用性区域 (AKS) 群集创建 Azure Kubernetes 服务](../aks/availability-zones.md)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure 中的区域和可用性区域](az-overview.md)
