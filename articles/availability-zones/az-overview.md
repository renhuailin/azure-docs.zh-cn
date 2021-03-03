---
title: Azure 中的区域和可用性区域
description: 了解 Azure 中的区域和可用性区域，以满足你的技术和法规要求。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 4adfb63ecab72eb42e188af472bb5387a0276a79
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723769"
---
# <a name="regions-and-availability-zones-in-azure"></a>Azure 中的区域和可用性区域

Microsoft Azure 服务可在全球范围内实现云操作的最佳水平。 你可以根据技术和监管注意事项选择最适合你的需求区域：服务功能、数据常驻、合规性要求和延迟。

## <a name="terminology"></a>术语

为了更好地了解 Azure 中的区域和可用性区域，它有助于了解关键术语或概念。

| 术语或概念 | 说明 |
| --- | --- |
| region | 在延迟定义的外围中部署的一组数据中心，并通过专用的区域低延迟网络进行连接。 |
| geography | 世界上至少包含一个 Azure 区域的区域。 地理位置定义了一个保持数据驻留和符合性界限的离散市场。 地域允许具有特定数据驻留和符合性要求的客户保持他们的数据和应用程序相邻近。 地域具有容错能力，可通过其与专用的高容量网络基础结构的连接来经受完成区域故障。 |
| 可用性区域 | 区域中的唯一物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 |
| 推荐的区域 | 提供最广泛的服务功能的区域，旨在支持可用性区域现在或将来使用。 根据 **建议** 在 Azure 门户中指定这些项。 |
| 备用 (其他) 区域 | 一种区域，它在 "数据驻留" 边界内扩展 Azure 占用量，其中还存在建议的区域。 备用区域有助于优化延迟，并为灾难恢复需要提供另一个区域。 它们不是为了支持可用性区域 (，不过 Azure 会定期评估这些区域，以确定它们是否应该成为推荐的区域) 。 它们在 Azure 门户中指定为 " **其他**"。 |
| 基础服务 | 一种核心 Azure 服务，该服务在区域公开上市时在所有区域提供。 |
| 主流服务 | 一项 Azure 服务，该服务在区域的90天内的所有推荐区域中提供，在备用区域中公开上市或按需驱动的可用性。 |
| 专用服务 | 一项 Azure 服务，在自定义/专用硬件支持的各区域之间实现了要求驱动的可用性。 |
| 区域服务 | 突破部署的 Azure 服务，可让客户指定要将服务部署到的区域。 有关完整列表，请参阅 [可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=all)）。 |
| 非区域服务 | 一个 Azure 服务，它与特定的 Azure 区域没有任何依赖关系。 非区域服务部署到两个或多个区域，如果出现区域性故障，另一个区域中的服务实例将继续为客户提供服务。 有关完整列表，请参阅 [可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/?products=all)）。 |

## <a name="regions"></a>区域

区域是在延迟定义的外围中部署的一组数据中心，通过专用的区域低延迟网络进行连接。 Azure 可让你灵活地部署需要的应用程序，包括跨多个区域，以提供跨区域复原能力。 有关详细信息，请参阅 [复原支柱概述](/azure/architecture/framework/resiliency/overview)。

## <a name="availability-zones"></a>可用性区域

可用性区域是一种高可用性产品，可保护应用程序和数据免受数据中心故障的影响。 可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源、冷却和网络。 为确保能够进行复原，所有已启用的地区中都必须至少有三个单独的区域。 区域中可用性区域的物理隔离可以在发生数据中心故障的情况下保护应用程序和数据。 区域冗余服务可跨可用性区域复制应用程序和数据，以防范单点故障。 Azure 凭借可用性区域提供一流的 99.99% VM 运行时间 SLA。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) 说明了 Azure 作为整体的保证可用性。

Azure 区域中的可用性区域是容错域和更新域的组合。 例如，如果在 Azure 区域的三个区域中创建三个或更多 VM，则 VM 将有效分布在三个容错域和三个更新域中。 Azure 平台可识别更新域中的此分发，以确保不会计划同时更新不同区域中的 Vm。

通过将计算、存储、网络和数据资源共置在一个区域并将其复制到其他区域，在应用程序体系结构中内置高可用性。 支持可用性区域的 Azure 服务划分为两类：

- **区域性服务** –将资源固定到特定区域的位置 (例如，虚拟机、托管磁盘、标准 IP 地址) 或
- **区域冗余服务** –当 Azure 平台跨区域自动复制时 (例如，区域冗余存储、SQL Database) 。

若要在 Azure 上实现全面的业务连续性，可结合使用可用性区域和 Azure 区域对来构建应用程序体系结构。 可以使用 Azure 区域中的可用性区域以同步方式复制应用程序和数据以实现高可用性，并以异步方式跨 Azure 区域复制以实现灾难恢复保护。
 
![地区中发生故障的一个区域的概念视图](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
>  () 上图中的数字1、2和3的可用性区域标识符在逻辑上分别映射到每个订阅的实际物理区域。 这意味着，给定订阅中的可用性区域1可能引用不同订阅中不同于可用性区域1的物理区域。 因此，建议不要在虚拟机放置的不同订阅之间依赖于可用性区域 Id。

## <a name="region-and-service-categories"></a>区域和服务类别

Azure 上跨区域提供 Azure 服务可用性的最佳方法是通过表达在建议区域和备用区域中提供的服务来对其进行说明。

- **推荐的区域** -一种提供范围最广的服务功能并设计为支持可用性区域现在或将来的服务的区域。 根据 **建议** 在 Azure 门户中指定这些项。
- **备用 (其他) 区域** -一个区域，该区域在一个推荐的区域也存在的数据派驻边界内扩展 Azure 占用空间。 备用区域有助于优化延迟，并为灾难恢复需要提供另一个区域。 它们不是为了支持可用性区域 (，不过 Azure 会定期评估这些区域，以确定它们是否应该成为推荐的区域) 。 它们在 Azure 门户中指定为 " **其他**"。

### <a name="comparing-region-types"></a>比较区域类型

Azure 服务分为三个类别：基本、主流和专用服务。 将服务部署到任何给定区域的 Azure 一般策略主要由区域类型、服务类别和客户需求驱动：

- **基础** –当区域公开上市时，或在新的基础服务公开上市后的90天内，所有推荐和备用区域均提供此功能。
- **主流** –在该区域的90天内的所有推荐地区提供此功能;备用区域中的按需驱动 (许多已部署到) 的一大部分备用区域中。
- **专门** 面向行业的服务产品，通常由自定义/专用硬件集中或支持。 跨区域的需求驱动可用性 (许多都已部署到) 的建议区域的一小部分。

若要查看给定区域中部署了哪些服务，以及区域中服务的预览或公开服务的未来发展路线图，请参阅 [按区域提供的产品](https://azure.microsoft.com/global-infrastructure/services/?products=all)。

如果某个服务产品在特定区域中不可用，你可以通过联系 Microsoft 销售代表来分享你的兴趣。

| 区域类型 | 非区域 | 基础 | 主要支持 | 专用 | 可用性区域 | 数据驻留 |
| --- | --- | --- | --- | --- | --- | --- |
| 建议 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 需求驱动 | :heavy_check_mark: | :heavy_check_mark: |
| 备用 | :heavy_check_mark: | :heavy_check_mark: | 需求驱动 | 需求驱动 | 不适用 | :heavy_check_mark: |

### <a name="services-by-category-with-availability-zones"></a>按类别分类的服务可用性区域

如前所述，Azure 将服务分为三个类别：基础、主流和专用化。 服务类别在正式发布时分配。 通常，服务会将其生命周期作为专用服务启动，并按需求和利用率增加，从而提升为主流或基础。 下表列出了 "基本"、"主流" 服务的类别。 应注意以下有关表的信息：

- 某些服务不是区域。 有关非区域性服务的信息和列表，请参阅 [可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/)）。
- 不会列出较早的服务或虚拟机的生成。 有关详细信息，请参阅[之前代虚拟机大小的](../virtual-machines/sizes-previous-gen.md)文档
- .在公开上市 (GA) 之前，不会为服务分配类别。 有关信息以及预览服务的列表，请参阅 [可用产品（按区域](https://azure.microsoft.com/global-infrastructure/services/)）。 

> [!div class="mx-tableFixed"]
> | 基础                           | 主要支持                                        | 
> |----------------------------------------|---------------------------------------------------|
> | 存储帐户                       | API 管理                                    | 
> | 应用程序网关                    | 应用程序配置                                 | 
> | Azure 备份                           | 应用服务                                       | 
> | Azure Cosmos DB                        | 自动化                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory 域服务            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure 公共 IP                        | 用于 Redis 的 Azure 缓存                             | 
> | Azure SQL 数据库                     | Azure 认知搜索                            | 
> | Azure SQL：托管实例           | Azure 认知服务                          | 
> | 磁盘存储                           | Azure 认知服务：计算机视觉         | 
> | 事件中心                             | Azure 认知服务：内容审查器       | 
> | Key Vault                              | Azure 认知服务：人脸                    | 
> | 负载均衡器                          | Azure 认知服务：沉浸式读者        | 
> | 服务总线                            | Azure 认知服务：语言理解  | 
> | Service Fabric                         | Azure 认知服务：语音服务         | 
> | 存储：热/冷 Blob 存储层   | Azure 认知服务：文本分析          | 
> | 存储：托管磁盘                 | Azure 认知服务：转换器              | 
> | 虚拟机规模集             | Azure 数据资源管理器                               | 
> | 虚拟机                       | Azure Data Share                                  | 
> | 虚拟机： Azure 专用主机 | Azure Database for MySQL                          | 
> | 虚拟机： Av2-Series           | Azure Database for PostgreSQL                     | 
> | 虚拟机： Bs-Series            | Azure DDoS 防护                             | 
> | 虚拟机： DSv2-Series          | Azure 防火墙                                    | 
> | 虚拟机： DSv3-Series          | Azure 防火墙管理器                            | 
> | 虚拟机： Dv2-Series           | Azure Functions                                   | 
> | 虚拟机： Dv3-Series           | Azure IoT 中心                                     |     
> | 虚拟机： ESv3-Series          | Azure Kubernetes 服务 (AKS)                    | 
> | 虚拟机： Ev3-Series           | Azure 机器学习                            | 
> | 虚拟网络                        | Azure Monitor： Application Insights               | 
> | VPN 网关                            | Azure Monitor： Log Analytics                      | 
> |                                        | Azure 专用链接                                | 
> |                                        | Azure Red Hat OpenShift                           | 
> |                                        | Azure Site Recovery                               | 
> |                                        | Azure 流分析                            | 
> |                                        | Azure Synapse Analytics                           | 
> |                                        | Batch                                             | 
> |                                        | 云服务： M 系列                          | 
> |                                        | 容器实例                               | 
> |                                        | 容器注册表                                | 
> |                                        | 数据工厂                                      | 
> |                                        | 事件网格                                        | 
> |                                        | HDInsight                                         |  
> |                                        | 逻辑应用                                        | 
> |                                        | 媒体服务                                    | 
> |                                        | 网络观察程序                                   | 
> |                                        | 通知中心                                 | 
> |                                        | 高级 Blob 存储                              | 
> |                                        | 高级文件存储                             | 
> |                                        | 虚拟机： Ddsv4-Series                    | 
> |                                        | 虚拟机： Ddv4-Series                     | 
> |                                        | 虚拟机： Dsv4-Series                     | 
> |                                        | 虚拟机： Dv4-Series                      | 
> |                                        | 虚拟机： Edsv4-Series                    | 
> |                                        | 虚拟机： Edv4-Series                     | 
> |                                        | 虚拟机： Esv4-Series                     | 
> |                                        | 虚拟机： Ev4-Series                      | 
> |                                        | 虚拟机： Fsv2-Series                     | 
> |                                        | 虚拟机： M 系列                        | 
> |                                        | 虚拟 WAN                                       | 



### <a name="specialized-services"></a>专用服务
如前所述，Azure 将服务分为三个类别：基础、主流和专用化。 服务类别在正式发布时分配。 通常，服务会将其生命周期作为专用服务启动，并按需求和利用率增加，从而提升为主流或基础。 下表列出了专用服务。 

> [!div class="mx-tableFixed"]
> | 专用                                          |
> |------------------------------------------------------|
> | 适用于 FHIR 的 Azure API                                   |
> | Azure Analysis Services                              |
> | Azure 认知服务：异常探测器           |
> | Azure 认知服务：自定义视觉              |
> | Azure 认知服务：窗体识别器            |
> | Azure 认知服务： Personalizer               |
> | Azure 认知服务： QnA Maker                  |
> | Azure Database for MariaDB                           |
> | Azure 数据库迁移服务                     |
> | Azure 专用 HSM                                  |
> | Azure 数字孪生                                  |
> | Azure 运行状况机器人                                     |
> | Azure HPC 缓存                                      |
> | Azure 实验室服务                                   |
> | Azure NetApp 文件                                   |
> | Azure SignalR 服务                                |
> | Azure 春季云服务                           |
> | Azure 时序见解                           |
> | Azure VMware 解决方案                                |
> | Azure VMware Solution by CloudSimple                 |
> | Data Lake Analytics                                  |
> | Azure 机器学习工作室（经典）              |
> | 空间定位点                                      |
> | 存储：存档存储                             |
> | 超级磁盘存储                                   |
> | 视频索引器                                        |
> | 虚拟机： DASv4-Series                       |
> | 虚拟机： DAv4-Series                        |
> | 虚拟机： DCsv2 系列                       |
> | 虚拟机： EASv4-Series                       |
> | 虚拟机： EAv4-Series                        |
> | 虚拟机： HBv1-Series                        |
> | 虚拟机： HBv2-Series                        |
> | 虚拟机： HCv1-Series                        |
> | 虚拟机： H 系列                           |
> | 虚拟机： LSv2-Series                        |
> | 虚拟机： Mv2-Series                         |
> | 虚拟机： NCv3-Series                        |
> | 虚拟机： NDv2-Series                        |
> | 虚拟机： NVv3-Series                        |
> | 虚拟机： NVv4-Series                        | 
> | 虚拟机： Azure SAP HANA 大型实例  |




## <a name="next-steps"></a>后续步骤

- [支持在 Azure 中可用性区域的区域](az-region.md)
- [快速入门模板](https://aka.ms/azqs)
