---
title: Azure 服务
description: 了解 Azure 中的区域类型和服务类别。
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: dc0adacbda491f7658056e7d313b9bdc923b43af
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620868"
---
# <a name="azure-services"></a>Azure 服务

各 Azure 区域的服务可用性取决于区域的类型。 Azure 中有两种类型的区域：建议和备用。

- **建议**：这些区域提供最广泛的服务功能，目前支持可用性区域。 在 Azure 门户中被指定为“建议”。
- **备用**：这些区域在数据驻留边界内（建议区域目前也位于其中）扩展 Azure 的占用空间。 备用区域有助于优化延迟，并提供辅助区域来应对灾难恢复需求，但不支持可用性区域。 Azure 定期评估备用区域，以确定它们是否应成为建议区域。 在 Azure 门户中被指定为“其他”。

## <a name="service-categories-across-region-types"></a>跨区域类型的服务类别

Azure 服务分为三种类别：基础、主流和战略  。 Azure 向任何指定区域部署服务的一般策略主要取决于区域类型、服务类别和客户需求.

- **基础**：在区域正式发布后或新基础服务正式发布后 90 天内，所有建议区域和备用区域均可用的服务。
- **主流**：在区域正式发布后 90 天内，所有建议区域均可用的服务。 在备用区域根据需求进行发布，许多已部署到备用区域的大型子集中。
- **战略**（以前称为专用）- 有针对性的服务产品，通常专注于特定行业或由自定义硬件提供支持。 在各区域根据需求进行发布，许多已部署到建议区域的大型子集中。

若要查看某个区域已部署哪些服务，以及区域中服务预览版或正式发布版的未来路线图，请参阅[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/)。

如果服务产品在某个区域不可用，请联系 Microsoft 销售代表了解详细信息并浏览各个选项。

| 区域类型 | 非区域 | 基础 | 主要支持 | 策略性 | 可用性区域 | 数据驻留 |
| --- | --- | --- | --- | --- | --- | --- |
| 建议 | **是** | **是** | **是** | 需求驱动 | **是** | **是** |
| 备用 | **是** | **是** | 需求驱动 | 需求驱动 | 空值 | **是** |

## <a name="available-services-by-category"></a>按类别提供的服务

Azure 在正式发布服务时，将服务类别指定为基础、主流和战略。 通常，服务一开始为战略服务，随着需求和使用的增长升级为主流和基础服务。

Azure 服务按类别显示在下表中。 请注意，有些服务是非区域服务。 这意味着它们不限区域，在全球范围内都可用。 有关非区域服务的信息和完整列表，请参阅[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/)。

> [!div class="mx-tableFixed"]
> | ![表示此服务是基础服务的图标。](media/icon-foundational.svg) 基础                           | ![表示此服务是主流服务的图标。](media/icon-mainstream.svg) 主要支持                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Azure 存储帐户                 | Azure API 管理                              | 
> | Azure 应用程序网关              | Azure 应用配置                           | 
> | Azure 备份                           | Azure 应用服务                                 | 
> | Azure Cosmos DB                        | Azure 自动化                                  | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory 域服务            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Azure 公共 IP                        | Azure Redis 缓存                             | 
> | Azure SQL Database                     | Azure 认知服务                          | 
> | Azure SQL 托管实例             | Azure 认知服务：计算机视觉         | 
> | 磁盘存储                           | Azure 认知服务：内容审查器       | 
> | Azure 事件中心                       | Azure 认知服务：人脸                    | 
> | Azure Key Vault                        | Azure 认知服务：文本分析          | 
> | Azure 负载均衡器                    | Azure 数据资源管理器                               | 
> | Azure 服务总线                      | Azure Database for MySQL                          | 
> | Azure Service Fabric                   | Azure Database for PostgreSQL                     | 
> | Azure 存储：热/冷 Blob 存储层   | Azure DDoS 防护                       | 
> | 存储：托管磁盘                 | Azure 防火墙                                    | 
> | Azure 虚拟机规模集       | Azure 防火墙管理器                            | 
> | Azure 虚拟机                 | Azure Functions                                   | 
> | 虚拟机：Azure 专用主机 | Azure IoT 中心                                     | 
> | 虚拟机：Av2 系列           | Azure Kubernetes 服务 (AKS)                    | 
> | 虚拟机：Bs 系列            | Azure Monitor：Application Insights               | 
> | 虚拟机：DSv2 系列          | Azure Monitor：Log Analytics                      | 
> | 虚拟机：DSv3 系列          | Azure 专用链接                                | 
> | 虚拟机：Dv2 系列           | Azure Site Recovery                               | 
> | 虚拟机：Dv3 系列           | Azure Synapse Analytics                           |     
> | 虚拟机：ESv3 系列          | Azure Batch                                       | 
> | 虚拟机：Ev3 系列           | Azure 云服务：M 系列                     | 
> | Azure 虚拟网络                  | Azure 容器实例                         | 
> | Azure VPN 网关                      | Azure 容器注册表                          | 
> |                                        | Azure 数据工厂                                | 
> |                                        | Azure 事件网格                                  | 
> |                                        | Azure HDInsight                                   |  
> |                                        | Azure 逻辑应用                                  | 
> |                                        | Azure 媒体服务                              | 
> |                                        | Azure 网络观察程序                             | 
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
> |                                        | Azure 虚拟 WAN                                 | 

### <a name="strategic-services"></a>战略服务
如前所述，Azure 将服务分为三种类别：基础、主要和战略。 服务类别在正式发布时指定。 通常，服务在生命周期之始为战略服务，随着需求和利用率增加，可能会提升为主流或基础服务。 下表列出了战略服务。 

> [!div class="mx-tableFixed"]
> | ![表示此服务是战略服务的图标。](media/icon-strategic.svg) 策略性                                          |
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
> | Azure 空间定位点                                |
> | 存储：存档存储                             |
> | Azure 超级磁盘存储                             |
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

\*支持可用性区域的虚拟机：AV2 系列、B 系列、DSv2 系列、DSv3 系列、Dv2 系列、Dv3 系列、ESv3 系列、Ev3 系列、F 系列、FS 系列、FSv2 系列和 M 系列。\*

未列出较早几代的服务或虚拟机。 有关详细信息，请参阅[前几代虚拟机大小](../virtual-machines/sizes-previous-gen.md)。

若要详细了解尚未正式发布的预览服务，并查看这些服务的列表，请参阅[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/)。 有关支持可用性区域的服务的完整列表，请参阅[支持可用性区域的 Azure 服务](az-region.md)。

## <a name="next-steps"></a>后续步骤

- [支持可用性区域的 Azure 服务](az-region.md)
- [Azure 中的区域和可用性区域](az-overview.md)
