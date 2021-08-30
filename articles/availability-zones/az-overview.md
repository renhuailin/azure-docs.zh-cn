---
title: Azure 中的区域和可用性区域
description: 了解 Azure 中的区域和可用性区域，以满足技术和法规要求。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/09/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 8d2198c4c2c2dd100d7bbf88eebc4e521d6ace04
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747778"
---
# <a name="regions-and-availability-zones-in-azure"></a>Azure 中的区域和可用性区域

Microsoft Azure 服务遍布全球各地，可推动云操作实现最佳水平。 你可以根据以下技术和法规事项选择最符合需求的区域：服务功能、数据驻留、合规性要求和延迟。

## <a name="terminology"></a>术语

为更好地了解 Azure 中的区域和可用性区域，了解关键术语或概念很有帮助。

| 术语或概念 | 说明 |
| --- | --- |
| region | 在定义了延迟的外围中部署的一系列数据中心，通过专用的区域性低延迟网络互相连接。 |
| geography | 世界上至少包含一个 Azure 区域的地区。 地域定义一个独立的市场，具有数据驻留和符合性边界。 地域允许具有特定数据驻留和符合性要求的客户保持他们的数据和应用程序相邻近。 地域具有容错能力，通过与专用的高容量网络基础设施相连，可承受整个区域的故障。 |
| 可用性区域 | 某个区域内的唯一物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 |
| 建议的区域 | 提供最广泛服务功能的区域，旨在为可用性区域现在或将来使用提供支持。 这些项目在 Azure 门户中被指定为“建议”。 |
| 备用（其他）区域 | 在数据驻留边界内扩展 Azure 占用情况的区域，建议的区域也位于其中。 备用区域有助于优化延迟，并提供辅助区域来应对灾难恢复需求。 它们并非用来支持可用性区域（但 Azure 会定期评估这些区域，以确定它们是否应该成为建议的区域）。 这些项目在 Azure 门户中被指定为“其他”。 |
| 基础服务 | 在该区域正式发布后，所有区域均可用的核心 Azure 服务。 |
| 主要服务 | 在区域正式发布后 90 天内或在备用区域受需求驱动而发布后，所有建议区域均可用的 Azure 服务。 |
| 专用服务 | 在各区域受需求驱动而发布的 Azure 服务，由自定义/专用硬件提供支持。 |
| 区域服务 | 在区域内部署的 Azure 服务，允许客户指定要将该服务部署到的区域。 如需完整列表，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=all)。 |
| 非区域服务 | 不依赖于特定 Azure 区域的 Azure 服务。 非区域服务将部署到两个或多个区域，如果出现区域性故障，另一个区域中的服务实例将继续为客户提供服务。 如需完整列表，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=all)。 |

## <a name="regions"></a>区域

区域是指在定义了延迟的外围中部署的一系列数据中心，它们通过专用的区域性低延迟网络互相连接。 借助 Azure，可灵活地将应用程序部署到所需的位置，包括跨多个区域来提供跨区域复原能力。 有关详细信息，请参阅[复原能力支柱概述](/azure/architecture/framework/resiliency/principles)。

## <a name="availability-zones"></a>可用性区域

可用性区域是一种高可用性产品/服务，在数据中心发生故障时可以保护应用程序和数据。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的地区中都必须至少有三个单独的区域。 区域中可用性区域的物理隔离可以在发生数据中心故障的情况下保护应用程序和数据。 区域冗余服务可跨可用性区域复制应用程序和数据，以防范单点故障。 Azure 凭借可用性区域提供一流的 99.99% VM 运行时间 SLA。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

Azure 区域中的可用性区域是容错域和更新域的组合。 例如，如果在 Azure 区域的三个区域中创建三个或更多 VM，则 VM 将有效分布在三个容错域和三个更新域中。 Azure 平台可跨更新域识别分布情况，以确保不同区域中的 VM 不会同时更新。

通过将计算、存储、网络和数据资源共置在一个区域并将其复制到其他区域，在应用程序体系结构中内置高可用性。 支持可用性区域的 Azure 服务划分为两类：

- 区域服务 – 将资源固定到某个特定区域（例如，虚拟机、托管磁盘、标准 IP 地址）；或
- 区域冗余服务 – Azure 平台自动跨区域复制（例如，区域冗余存储、SQL 数据库）。

> [!NOTE]
> “标准 SKU 公共 IP 地址”和“公共 IP 地址前缀”资源类型也具有“无区域”选项。  这样，客户可使用标准 SKU 公共 IP（并将其关联到仅允许标准 SKU 的资源），但它不保证实现冗余。  （从基本 SKU [升级](https://docs.microsoft.com/azure/virtual-network/public-ip-upgrade-portal)到标准 SKU 的所有公共 IP 地址都属于“无区域”类型。）

若要在 Azure 上实现全面的业务连续性，可结合使用可用性区域和 Azure 区域对来构建应用程序体系结构。 可以使用 Azure 区域中的可用性区域以同步方式复制应用程序和数据以实现高可用性，并以异步方式跨 Azure 区域复制以实现灾难恢复保护。
 
![地区中发生故障的一个区域的概念视图](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> 可用性区域标识符（上图中的编号 1、2 和 3）会按逻辑分别映射到每个订阅的实际物理区域。 这意味着，指定订阅中的可用性区域 1 在其他订阅中可能指示可用性区域 1 之外的其他物理区域。 因此，建议在不同订阅中不要依赖于可用性区域 ID 来放置虚拟机。

## <a name="region-and-service-categories"></a>区域和服务类别

有关 Azure 在不同区域发布 Azure 服务的方法，最恰当的描述方式是说明在建议区域和备用区域发布的服务。

- 建议区域 - 提供最广泛服务功能的区域，旨在为可用性区域现在或将来使用提供支持。 这些项目在 Azure 门户中被指定为“建议”。
- 备用（其他）区域 - 在数据驻留边界内扩展 Azure 占用情况的区域，建议的区域也位于其中。 备用区域有助于优化延迟，并提供辅助区域来应对灾难恢复需求。 它们并非用来支持可用性区域（但 Azure 会定期评估这些区域，以确定它们是否应该成为建议的区域）。 这些项目在 Azure 门户中被指定为“其他”。

### <a name="comparing-region-types"></a>比较区域类型

Azure 服务分为三种类别：基础服务、主要服务和专用服务。 Azure 向任何指定区域部署服务的一般策略主要取决于区域类型、服务类别和客户需求：

- 基础 – 在区域正式发布后或新基础服务正式发布后 90 天内，所有建议区域和备用区域均可用的服务。
- 主要 – 在区域正式发布后 90 天内有建议区域均可用的服务；在备用区域由需求驱动而发布的服务（许多已部署到备用区域的大型子集中）。
- 专用 - 有针对性的服务产品，通常专注于特定行业或由自定义/专用硬件提供支持。 在不同区域根据需求进行发布（许多已部署到建议区域的大型子集中）。

要查看指定区域已部署哪些服务，以及区域中服务预览版或正式发布版的未来路线图，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=all)。

如果某个服务产品在特定区域不可用，可以通过联系 Microsoft 销售代表来分享你的兴趣。

| 区域类型 | 非区域 | 基础 | 主要支持 | 专用 | 可用性区域 | 数据驻留 |
| --- | --- | --- | --- | --- | --- | --- |
| 建议 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 需求驱动 | :heavy_check_mark: | :heavy_check_mark: |
| 备用 | :heavy_check_mark: | :heavy_check_mark: | 需求驱动 | 需求驱动 | 空值 | :heavy_check_mark: |

### <a name="services-by-category"></a>服务类别

如前所述，Azure 将服务分为三种类别：基础、主要和专用。 服务类别在正式发布时指定。 通常，服务在生命周期之始为专用服务，随着需求和利用率增加，可能会提升为主流或基础服务。 下表列出了基础、主要类别的服务。 关于该表，应注意以下事项：

- 有些服务是非区域服务。 有关非区域服务的信息和列表，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)。
- 未列出较早几代的服务或虚拟机。 有关详细信息，请参阅[先前几代虚拟机大小](../virtual-machines/sizes-previous-gen.md)文档。
- 在正式发布 (GA) 之前，不会为服务指定类别。 有关预览版服务的信息和列表，请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)。 

> [!div class="mx-tableFixed"]
> | 基础                           | 主要支持                                        | 
> |----------------------------------------|---------------------------------------------------|
> | 存储帐户                       | API 管理                                    | 
> | 应用程序网关                    | 应用配置                                 | 
> | Azure 备份                           | 应用服务                                       | 
> | Azure Cosmos DB                        | 自动化                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory 域服务            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure 公共 IP                        | Azure Redis 缓存                             | 
> | Azure SQL Database                     | Azure 认知服务                          | 
> | Azure SQL 托管实例             | Azure 认知服务：计算机视觉         | 
> | 磁盘存储                           | Azure 认知服务：内容审查器       | 
> | 事件中心                             | Azure 认知服务：人脸                    | 
> | Key Vault                              | Azure 认知服务：文本分析          | 
> | 负载均衡器                          | Azure 数据资源管理器                               | 
> | 服务总线                            | Azure Database for MySQL                          | 
> | Service Fabric                         | Azure Database for PostgreSQL                     | 
> | 存储：热/冷 Blob 存储层   | Azure DDoS 防护                             | 
> | 存储：托管磁盘                 | Azure 防火墙                                    | 
> | 虚拟机规模集             | Azure 防火墙管理器                            | 
> | 虚拟机                       | Azure Functions                                   | 
> | 虚拟机：Azure 专用主机 | Azure IoT 中心                                     | 
> | 虚拟机：Av2 系列           | Azure Kubernetes 服务 (AKS)                    | 
> | 虚拟机：Bs 系列            | Azure Monitor：Application Insights               | 
> | 虚拟机：DSv2 系列          | Azure Monitor：Log Analytics                      | 
> | 虚拟机：DSv3 系列          | Azure 专用链接                                | 
> | 虚拟机：Dv2 系列           | Azure Site Recovery                               | 
> | 虚拟机：Dv3 系列           | Azure Synapse Analytics                           |     
> | 虚拟机：ESv3 系列          | Batch                                             | 
> | 虚拟机：Ev3 系列           | 云服务：M 系列                          | 
> | 虚拟网络                        | 容器实例                               | 
> | VPN 网关                            | 容器注册表                                | 
> |                                        | 数据工厂                                      | 
> |                                        | 事件网格                                        | 
> |                                        | HDInsight                                         |  
> |                                        | 逻辑应用                                        | 
> |                                        | 媒体服务                                    | 
> |                                        | 网络观察程序                                   | 
> |                                        | 高级 Blob 存储                              | 
> |                                        | 高级文件存储                             | 
> |                                        | 虚拟机：Ddsv4 系列                    | 
> |                                        | 虚拟机：Ddv4 系列                     | 
> |                                        | 虚拟机：Dsv4 系列                     | 
> |                                        | 虚拟机：Dv4 系列                      | 
> |                                        | 虚拟机：Edsv4 系列                    | 
> |                                        | 虚拟机：Edv4 系列                     | 
> |                                        | 虚拟机：Esv4 系列                     | 
> |                                        | 虚拟机：Ev4 系列                      | 
> |                                        | 虚拟机：Fsv2 系列                     | 
> |                                        | 虚拟机：M 系列                        | 
> |                                        | 虚拟 WAN                                       | 



### <a name="specialized-services"></a>专用服务
如前所述，Azure 将服务分为三种类别：基础、主要和专用。 服务类别在正式发布时指定。 通常，服务在生命周期之始为专用服务，随着需求和利用率增加，可能会提升为主要或基础服务。 下表列出了专用服务。 

> [!div class="mx-tableFixed"]
> | 专用                                          |
> |------------------------------------------------------|
> | 适用于 FHIR 的 Azure API                                   |
> | Azure Analysis Services                              |
> | Azure 区块链服务                             |
> | Azure 认知服务：异常检测器           |
> | Azure 认知服务：自定义视觉              |
> | Azure 认知服务：表单识别器            |
> | Azure 认知服务：沉浸式阅读器           |
> | Azure 认知服务：语言理解     |
> | Azure 认知服务：个性化体验创建服务               |
> | Azure 认知服务：QnA Maker                  |
> | Azure 认知服务：语音服务            |
> | Azure Data Share                                     |
> | Azure Databricks                                     |
> | Azure Database for MariaDB                           |
> | Azure 数据库迁移服务                     |
> | Azure 专用 HSM                                  |
> | Azure 数字孪生                                  |
> | Azure Health Bot                                     |
> | Azure HPC 缓存                                      |
> | Azure 实验室服务                                   |
> | Azure NetApp 文件                                   |
> | Azure Red Hat OpenShift                              |
> | Azure SignalR 服务                                |
> | Azure Spring Cloud                                   |
> | Azure 流分析                               |
> | Azure 时序见解                           |
> | Azure VMware 解决方案                                |
> | Azure VMware Solution by CloudSimple                 |
> | 空间定位点                                      |
> | 存储：存档存储                             |
> | 超级磁盘存储                                   |
> | 视频索引器                                        |
> | 虚拟机：DASv4 系列                       |
> | 虚拟机：DAv4 系列                        |
> | 虚拟机：DCsv2 系列                       |
> | 虚拟机：EASv4 系列                       |
> | 虚拟机：EAv4 系列                        |
> | 虚拟机：HBv1 系列                        |
> | 虚拟机：HBv2 系列                        |
> | 虚拟机：HCv1 系列                        |
> | 虚拟机：H 系列                           |
> | 虚拟机：LSv2 系列                        |
> | 虚拟机：Mv2 系列                         |
> | 虚拟机：NCv3 系列                        |
> | 虚拟机：NDv2 系列                        |
> | 虚拟机：NVv3 系列                        |
> | 虚拟机：NVv4 系列                        | 
> | 虚拟机：Azure SAP HANA 大型实例  |




## <a name="next-steps"></a>后续步骤

- [在 Azure 中支持可用性区域的区域](az-region.md)
- [快速入门模板](https://aka.ms/azqs)
